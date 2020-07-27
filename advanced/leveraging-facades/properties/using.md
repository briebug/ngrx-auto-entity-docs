# Using Facade Properties

To use one of these properties in your component, simply inject your facade class and assign the property to a local observable:

{% code title="customers.component.ts" %}
```typescript
export class CustomersComponent implements OnInit {
    customers$: Observable<Customer[]>;
    currentCustomer$: Observable<Customer>;
    isLoading$: Observable<boolean>;
    
    constructor(private customerFacade: CustomerFacade) {
    }
    
    ngOnInit(): void {
        this.customers$ = this.customerFacade.all$;
        this.customer$ = this.customerFacade.current$;
        this.isLoading$ = this.customerFacade.isLoading$;
        
        this.customerFacade.loadAll();
    }
    
    select(customer: Customer): void {
        this.customerFacade.select(customer);
    }
}
```
{% endcode %}

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

