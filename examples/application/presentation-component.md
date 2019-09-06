---
description: Bits of UI
---

# Presentation Components

### Customer List

{% code-tabs %}
{% code-tabs-item title="customer-list.component.ts" %}
```typescript
import { EventEmitter, Component, Input, Output } from '@angular/core';
import { Customer } from '../models';

@Component({
  selector: 'app-customers-list',
  templateUrl: './customers-list.component.html',
  styleUrls: ['./customers-list.component.scss']
})
export class CustomersListComponent {
  @Input() customers: Customer[];
  @Output() selected = new EventEmitter<Customer>();
  @Output() deleted = new EventEmitter<Customer>();
}
```
{% endcode-tabs-item %}

{% code-tabs-item title="customer-list.component.html" %}
```markup
<div class="customer-list">
  <table>  
    <thead>
      <tr>
        <th><i class="fa fa-key"></i></th>
        <th>Name</th>
        <th><i class="fa fa-wrench"></i></th>
      </tr>
    </thead>  
    <tbody>
      <tr *ngFor="let customer of customers">
        <td>{{customer.id}}</td>
        <td>{{customer.name}}</td>
        <td>
          <i class="fa fa-edit" (click)="selected.emit(customer)"></i>
          <i class="fa fa-trash" (click)="deleted.emit(customer)"></i>
        </td>
      </tr>
    </tbody>
    <tfoot>
      <tr>
        <td class="text-right" colspan="3">
          Total Customers: {{customers.length}}
        </td>
      </tr>
    </tfoot>
  </table>
</div>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Customer Detail

{% code-tabs %}
{% code-tabs-item title="customer-detail.component.ts" %}
```typescript
import { EventEmitter, Component, Input, Output } from '@angular/core';
import { Customer } from '../models';

@Component({
  selector: 'app-customer-detail',
  templateUrl: './customer-detail.component.html',
  styleUrls: ['./customer-detail.component.scss']
})
export class CustomersListComponent {
  @Input() customer: Customer;
  @Output() saved = new EventEmitter<Customer>();
  @Output() deleted = new EventEmitter<Customer>();
}
```
{% endcode-tabs-item %}

{% code-tabs-item title="customer-detail.component.html" %}
```markup
<div class="customer-detail">
    <h2>{{customer.name}}</h2>
    <h3>{{customer.title}}</h3>
    <a href="mailto:{{customer.email}}">
        {{customer.email | prettyEmail}}
    </a>
    
    <a *ngIf="customer.handles?.twitter"
       href="https://www.twitter.com/{{customer.handles?.twitter}}">
        {{customer.handles?.twitter}}
    </a>
    <a *ngIf="customer.handles?.facebook"
       href="https://www.facebook.com/{{customer.handles?.facebook}}">
        {{customer.handles?.facebook}}
    </a>
    
    <button (click)="edited.emit(customer)">Edit</button>    
    <button (click)="deleted.emit(customer)">Delete</button>
</div>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

