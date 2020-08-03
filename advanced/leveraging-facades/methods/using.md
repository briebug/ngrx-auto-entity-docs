# Using Facade Activities

To use one of these activities in your component, simply inject your facade class and call the method in the constructor, an `ngOnInit` or similar handler, or a custom handler:

{% code title="customers.component.ts" %}
```typescript
export class CustomersComponent implements OnInit {    
    constructor(
        public customers: CustomerFacade, 
        public router: Router,
        public route: ActivatedRoute
    ) {
        this.customers.loadAll();
    }
    
    ngOnInit(): void {
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

### Use the Async Pipe!

We highly recommend following NgRx best practices here to avoid subscribing directly to any observable on an entity facade. Instead, use the `async` pipe from Angular in your templates, and follow container/presenter model for your component architecture:

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

