# Using Custom Selectors

When it comes to using custom selectors, they are used the same as any prefabricated facade property or method. As a quick example, our prior two custom selections added to our `CustomerFacade` may be used as so:

{% code title="customer-by-name.component.ts" %}
```typescript
@Component({
  selector: 'app-customer-by-name',
  templateUrl: './customer-by-name.component.html',
  styleUrls: ['./customer-by-name.component.scss']
})
export class CustomerByNameComponent implements OnInit {
  customer$: Observable<Customer>;

  constructor(
    private route: ActivatedRoute, 
    private customers: CustomerFacade
  ) {}

  ngOnInit() {
    // This may be called elsewhere instead of here
    // i.e. dispatch a page loading or initializing action and 
    // load customers in an effect
    this.customers.loadAll(); 
  
    // This is component specific as it requires extracting information
    // from the currently activated route, which is context-specific
    this.customer$ = this.route.paramMap.pipe(
      filter(params => params.has('name')),
      map(params => params.get('name')),
      switchMap(name => this.customerFacade.customerByName$(name))
    );
  }
}
```
{% endcode %}

