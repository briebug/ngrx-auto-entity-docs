---
description: Are you even a programmer anymore?
---

# So Little Code!

Here is the full excerpt of code that you had to write to implement our customers container component from the facade examples. We are assuming code module imports are handled by the IDE and are not something you would necessarily "write" yourself. Standard Angular decorator content excluded as it is usually generated for you. 

{% code title="customer.state.ts" %}
```typescript
export const { initialState, facade: CustomerFacadeBase } = buildState(Customer);
export function customerReducer(state = initialState): IEntityState<Customer> {
    return state;
}
```
{% endcode %}

{% code title="customer.facade.ts" %}
```typescript
@Injectable({providedIn: 'root'})
export class CustomerFacade extends CustomerFacadeBase {
    constructor(private store: Store<AppState>) {
        super(Customer, store);
    }
}
```
{% endcode %}

{% code title="customers.component.ts" %}
```typescript
@Component({...})
export class CustomersComponent {
    constructor(
        public customers: CustomerFacade, 
        public router: Router,
        public route: ActivatedRoute
    ) {
        customers.loadAll();
        
        this.route.paramMap.pipe(
            filter(params => params.has('id')),
            map(params => params.get('id'))
        ).subscribe(id => this.customers.selectByKey(id));
    }
    
    select(customer: Customer): void {
        this.router.navigate(['./', customer.id]);
    }
}
```
{% endcode %}

{% code title="customers.component.html" %}
```markup
<div class="customers" *ngIf="customers.isLoading$ | async; else #loading">
    <app-customer-list 
        [customers]="customers.all$ | async" 
        (selected)="select($event)">
    </app-customer-list>
    <app-customer-detail 
        *ngIf="(customers.current$ | async) as customer"
        [customer]="customer">
    </app-customer-detail>
</div>
<ng-template #loading>
    Loading customers...
</ng-template>
```
{% endcode %}

There are also a couple additional lines of code you may need to add to your `app.state.ts` file for the app state interface and app reducer map, as well as a provider mapping between the model and the entity service. See [quick start](../../getting-started/from-scratch/) for more info.

