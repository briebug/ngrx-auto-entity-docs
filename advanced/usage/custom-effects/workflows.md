---
description: 'This leads to that, which leads to...'
---

# Workflows

An alternative use case to the bundling of actions or action composition, where related data must be handled with explicit order of execution, is workflows. A workflow is the use case where the result of one action may result in the dispatching of another, perhaps with derived data, perhaps with otherwise unrelated data. 

Let's face it. Sometimes we have to work with odd data structures in order to make things work. One of the key tenants of NgRx is denormalization of data, so we may have multiple collections that contain entities related to each other. 

### Use Case

A potential example use case here, however contrived it may be \(although not necessarily unheard of, especially for those who work with existing, well-established and particularly older backends\), might be tracking the things a user favorites with joiner records in a many-to-many table. Lets say we have a single-user app that needs to easily display this information. We may want to track an `isFavorite` flag on our _things_, and update said flag whenever our joiner entities are created or deleted.

### Chaining Actions

Unlike our full order creation example, where we needed to track a group of entities together and manage the order of execution for their creation, this use case requires that we chain one action off of the success of another. 

We do not need any new custom actions for this use case, however we do need some effects. Let's say we have `Post`s that a user may `Like`. The actual likes are tracked as a join between the `postId` and the `userId` and are created and deleted as independent entities in the database. For simplicity of processing this data in our UI and UI performance, we track an `wasLiked` flag on our `Post` entities in state. 

{% code-tabs %}
{% code-tabs-item title="like.effects.ts" %}
```typescript
export class LikeEffects {
    constructor(private actions$: Actions, private store: Store<AppState>) {}
    
    createSuccess$ = createEffect(() => 
        this.actions$.pipe(
            ofEntityType(Like, CreateSuccess),
            map(action => action.entity),
            withLatestFrom(this.store.pipe(select(allPosts))),
            map(([like, allPosts]) => 
                allPosts.find(post => post.id === like.postId)
            ),
            filter(post => !!post),
            map(post => new LoadSuccess(Post, {...post, wasLiked: true}))
        )
    );
    
    deleteSuccess$ = createEffect(() => 
        this.actions$.pipe(
            ofEntityType(Like, DeleteSuccess),
            map(action => action.entity),
            withLatestFrom(this.store.pipe(select(allPosts))),
            map(([like, allPosts]) => 
                allPosts.find(post => post.id === like.postId)
            ),
            filter(post => !!post),
            map(post => new LoadSuccess(Post, {...post, wasLiked: false}))
        )
    );
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="warning" %}
Don't forget register your new `LikeEffects` class with the NgRx `EffectsModule`!
{% endhint %}

### Understanding Workflows

These two effects are fairly strait forward. They look for the success of prior `Create` or `Delete` actions dispatched for `Like` entities. They then grab all the posts currently loaded into state, find the post related to the like, and dispatch a `LoadSuccess`. This may seem odd, however we do not actually wish to load a `Post` from the database, we only wish to update the related `Post` in state...the goal is efficiency and performance.

The Auto-Entity meta reducer handles `LoadSuccess` in a specific way...it merges the specified entity into state. If it already exists, it is updated, if not it is added. Since creating a Like marks a post as liked for the specified user, we know we can set `wasLiked` to true when a `Like` is successfully created. Similarly, we know we can set `wasLiked` to false when a `Like` is successfully deleted. 

{% hint style="info" %}
In the event that you may need to update many entities at once, rather than just a single entity, a similar result may be achieved by dispatching `LoadManySuccess` by including the array of updated entities. 

Do not dispatch `LoadAllSuccess`, as the semantics are different. The nature of `LoadMany` means that when its success result is dispatched, the entities will be MERGED into the existing state. In contrast, the nature of `LoadAll` means that when its success result is dispatched, the entities will REPLACE whatever is currently in state.
{% endhint %}

Further, if either the `Create` or `Delete` of a `Like` fails, due to network connectivity issues, a server outage, etc. then we do not update the `Post` in state, which might incorrectly reflect to the user their status for the given post. 

### Higher Order Facade

Ok, now that we have the necessary effects to handle these state updates, let's update our facades to support the new behavior here. In fact...let's create a new facade of a special type, what we like to call a `Manager`, to handle the composition of information from multiple entities into another.  

This is just a matter of code organization, and for simpler applications adding the necessary method to an existing facade, say the `PostFacade`. For larger applications, segregating  out behavior that relies on multiple entities can help keep facades small and manageable. 

```typescript
@Injectable({providedIn: 'root'})
export class PostManager  {
    constructor(
        private store: Store<AppState>, 
        private likeFacade: LikeFacade
    ) {}
    
    likePost(post: readonly Post, user: readonly User): void {
        this.likeFacade.create({ postId: post.id, userId: user.id});
    }
}
```

We could try to simplify things here. We could, say, bring in the `PostFacade` here as well, and right after creating our like we update the `Post` and directly set the `wasLiked` flag to true. We could... 

{% hint style="danger" %}
Remember that NgRx is **asynchronous**. Dispatching an _**initiating**_ action such as `Create` or `Delete` does _not_ imply success, or even completion. It simply means you have _requested_ a particular activity be performed. Said action's status is unknown until the _**result**_ action, such as `CreateSuccess` or `DeleteFailure`, has subsequently been dispatched. 
{% endhint %}

When we create something, such as our `Like` joiner entity here, that dispatches the request to create the entity. This request may be fulfilled at a later time, but it is not guaranteed to be fulfilled by the time we get to the next line of code. It may in fact never be fulfilled depending on network or server conditions. 

By creating a simple workflow with effects and subsequent action dispatches, we are able to enforce and maintain the integrity of our data in our application. We only update the `wasLiked` flag once the `CreateSuccess` or `DeleteSuccess` actions for the liked `Post` have been dispatched, something much harder to do in a facade or component.

