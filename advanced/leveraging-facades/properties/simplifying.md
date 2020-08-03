# Simplifying Further

In some cases, assigning facade properties to other properties on your component classes may itself be somewhat overly complex. There is not necessarily an explicit need to have observable properties on the component itself. 

### Light Weight Containers

For container components such as the above Customers component, there is nothing of particular note in the component itself that isn't simply replicating functionality we already have in the facade. We could simply expose the facade to the template by making it `public` in the constructor:

{% code title="customer.component.ts" %}
```typescript
export class CustomersComponent {    
    constructor(public customers: CustomerFacade) {
        customers.loadAll();
    }
}
```
{% endcode %}

### Facade in the Template

Then simply use the facade within the template, as it already provides all of the same exact functionality:

{% code title="customer.component.html" %}
```markup
<div class="customers" *ngIf="customers.isLoading$ | async; else #loading">
    <app-customer-list 
        [customers]="customers.all$ | async" 
        (selected)="customers.select($event)">
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

Note just how simple this container component is now that we have leveraged the full capabilities of a generated auto-entity facade! And note just how little code you really had to write.

### 



