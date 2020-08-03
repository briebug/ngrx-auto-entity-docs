# Using Facade Selections

To use one of these properties in your component, simply inject your facade classes:

{% code title="customers.component.ts" %}
```typescript
export class CustomersComponent implements OnInit {
    customers$: Observable<Customer[]>;
    currentCustomer$: Observable<Customer>;
    isLoading$: Observable<boolean>;
    
    constructor(private customers: CustomerFacade) {
    }
    
    ngOnInit(): void {
        this.customers$ = this.customers.all$;
        this.customer$ = this.customers.current$;
        this.isLoading$ = this.customers.isLoading$;
        
        this.customers.loadAll();
    }
    
    select(customer: Customer): void {
        this.customers.select(customer);
    }
}
```
{% endcode %}

The classic use case is to expose observables on your component to represent the various streams you will use in your template. With NgRx Auto-Entity, this becomes a rather trivial exercise, as there is no real work that must be done in the component. \(Continue to the next section to learn how to improve upon the classic approach when using facades.\)

### Use the Async Pipe

We highly recommend following NgRx best practices here to avoid subscribing directly to any observable on an entity facade. Instead, use the `async` pipe from Angular in your templates, and follow container/presenter model for your component architecture:

{% code title="customers.component.html" %}
```markup
<div class="customers" *ngIf="isLoading$ | async; else #loading">
    <app-customer-list 
        [customers]="customers$ | async" 
        (selected)="select($event)">
    </app-customer-list>
    <app-customer-detail 
        *ngIf="(customer$ | async) as customer"
        [customer]="customer">
    </app-customer-detail>
</div>
<ng-template #loading>
Loading customers...
</ng-template>
```
{% endcode %}

