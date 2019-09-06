# Using Custom Selectors

When it comes to using custom selectors, they are used the same as any prefabricated facade property or method. As a quick example, our prior two custom selections added to our `CustomerFacade` may be used as so:

{% code-tabs %}
{% code-tabs-item title="customer-by-name.component.ts" %}
```typescript
@Component({
  selector: 'app-customer-by-name',
  templateUrl: './customer-by-name.component.html',
  styleUrls: ['./customer-by-name.component.scss']
})
export class CustomerByNameComponent implements OnInit {
  customer$: Observable<Customer>;

  constructor(
    private activatedRoute: ActivatedRoute, 
    private customerFacade: CustomerFacade
  ) {}

  ngOnInit() {
    this.customerFacade.loadAll(); // This may be called elsewhere instead of here
  
    this.customer$ = this.activatedRoute.paramMap.pipe(
      filter(params => params.has('name')),
      map(params => params.get('name')),
      switchMap(name => this.customerFacade.customerByName$(name))
    );
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

