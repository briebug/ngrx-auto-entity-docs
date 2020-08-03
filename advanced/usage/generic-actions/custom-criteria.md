# Custom Criteria

Special attention should be called to the `custom` parameter of all NgRx Auto-Entity generic actions. This parameter is optional and is always the last parameter of any action constructors. The availability of custom criteria for all actions is critical to supporting more advanced use of Auto-Entity, more complex API calls, support for hierarchical API structures, etc.

Sending custom criteria is entirely optional, but is an option for every action. This includes not only loads, but also CURD actions as well. If the set of entities to be retrieved, updated, replaced or deleted must be restricted by criteria beyond simply a uniquely identifying key, then custom criteria should be used. 

### Hierarchical Queries

Custom criteria should also be used to identify parent keys or relation keys in hierarchical API paths. Many REST APIs often relate entities hierarchically by nesting child entities under the paths of parent entities:

{% api-method method="get" host="" path="/api/v1/customers/:customerId/orders" %}
{% api-method-summary %}
Customer Orders
{% endapi-method-summary %}

{% api-method-description %}
Retrieves all the orders placed by the specified customer 
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customerId" type="number" required=true %}
The unique id of the Customer who's Orders are to be retrieved
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}
Will return the all orders for the specified customer 
{% endapi-method-response-example-description %}

```
[{
    orderId: number;
    customerId: number;
    datePlaced: Date;
    dateFulfilled?: Date;
    dateShipped?: Date;
}]
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

In the case of the above example REST API, the `customerId` is not a unique identifying key of the entity `Order`. As such it would be inappropriate to specify the customerId as the key in a `Load` action. Instead, the `customerId` should be passed as part of custom criteria:

```typescript
this.ordersFacade.loadAll({ customerId: 1 });
```

The custom criteria with `customerId` will be made available as the `criteria` parameter to the corresponding `loadAll` entity service method down the line, allowing you to make a proper call to the API:

```typescript
loadAll(entityInfo: IEntityInfo, criteria?: { customerId: number }): Observable<Order[]> {
    return this.http.get<Order[]>(
        `${environment.apiBaseUrl}/api/v1/customers/${criteria.customerId}/orders`
    );
}
```

### Partial Retrievals

Some times you may wish to retrieve restricted sets of entities based on criteria. Perhaps by date ranges. This is another use case for criteria, the fundamental case. If we expand our above API with additional custom query string parameters for the various order related dates:

{% api-method method="get" host="" path="/api/v1/customer/:customerId/orders?datePlaced&dateFulfilled&hasDateFullfilled" %}
{% api-method-summary %}
Customer Orders w/ Criteria  
{% endapi-method-summary %}

{% api-method-description %}
Retrieves all the orders placed by the specified customer, filtered by optional criteria
{% endapi-method-description %}

{% api-method-spec %}
{% api-method-request %}
{% api-method-path-parameters %}
{% api-method-parameter name="customerId" type="number" required=true %}
The unique id of the Customer who's Orders should be retrieved
{% endapi-method-parameter %}
{% endapi-method-path-parameters %}

{% api-method-query-parameters %}
{% api-method-parameter name="datePlaced" type="string" required=false %}
ISO start date of order placement
{% endapi-method-parameter %}

{% api-method-parameter name="dateFullfilled" type="string" required=false %}
ISO start date of order fulfillment
{% endapi-method-parameter %}

{% api-method-parameter name="wasFullfilled" type="boolean" required=false %}
Flag indicating whether orders should be fulfilled or not
{% endapi-method-parameter %}
{% endapi-method-query-parameters %}
{% endapi-method-request %}

{% api-method-response %}
{% api-method-response-example httpCode=200 %}
{% api-method-response-example-description %}

{% endapi-method-response-example-description %}

```
[{
    orderId: number;
    customerId: number;
    datePlaced: Date;
    dateFullfilled?: Date;
    dateShipped?: Date;
}]
```
{% endapi-method-response-example %}
{% endapi-method-response %}
{% endapi-method-spec %}
{% endapi-method %}

We might handle these optional criteria with a more advanced implementation of our Entity service for Orders. First, our initial dispatch:

```typescript
this.ordersFacade.loadMany({ 
    customerId: 1, 
    datePlaced: '2019-06-01', 
    wasFulfilled: true 
});
```

And our custom `loadMany` implementation in our entity service:

```typescript
interface LoadCriteria {
    customerId: number;
    datePlaced?: string;
    dateFulfilled?: string;
    wasFulfilled?: boolean;
}

loadMany(entityInfo: IEntityInfo, criteria?: LoadCriteria): Observable<Order[]> {
    let url = `${environment.apiBaseUrl}/api/v1/customers/${criteria.customerId}/orders`;
    return this.http.get<Order[]>(url, {
        params: {
            ...criteria, // Provide the rest of the criteria as query parameters
            customerId: undefined // Strip the id, as it is in the url
        }
    });
}
```

With **custom criteria**, we can handle any kind of backend API, even complex ones with custom and dynamic parameters. Note the use of `loadMany` here instead of `loadAll`...this is an important distinction with partial data loads vs. full data loads, which we will be going into more detail on in the next section.

