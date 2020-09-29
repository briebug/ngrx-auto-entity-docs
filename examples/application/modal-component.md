---
description: Rogue UI
---

# Modal Component

### Modal Container

{% tabs %}
{% tab title="components/customer-edit.component.ts" %}
```typescript
import { Component } from '@angular/core';
import { CustomerFacade } from '../facades';

@Component({
  selector: 'app-customer-edit',
  templateUrl: './customer-edit.component.html',
  styleUrls: ['./customer-edit.component.css']
})
export class CustomerEditComponent {
  @ViewChild('form') form: CustomerEditFormComponent;
   
  canSave = false;
  constructor(public customers: CustomerFacade, public modal: ModalInstance) {
  }
}
```
{% endtab %}

{% tab title="components/customer-edit.component.html" %}
```markup
<div class="customer-exit">
  <div>
    <h2>Edit Customer</h2>
    <app-customer-edit-form #form
      [customer]="customers.current$ | async"
      (submitted)="modal.dismiss($event)"
      (validated)="canSave = $event"
      >
    </app-customer-edit-form>
  </div>
  <div>
    <button [disabled]="canSave" click="form.submit()">Save</span>
    <button (click)="modal.dismiss()">Cancel</span>
  </div>
</div>
```
{% endtab %}
{% endtabs %}

### Modal Form

{% tabs %}
{% tab title="components/customer-edit-form.component.ts" %}
```typescript
import { Component, Input, Output } from '@angular/core';
import { Customer } from '../models';

@Component({
  selector: 'app-customers-edit-form',
  templateUrl: './customers-edit-form.component.html',
  styleUrls: ['./customers-edit-form.component.scss']
})
export class CustomersEditFormComponent implements OnChanges, OnInit {
  @Input() customer: Customer;
  @Output() submitted = new EventEmitter<Customer>();
  @Output() validated = new EventEmitter<boolean>();
  
  form: FormGroup;
  
  constructor(private builder: FormBuilder) {}
  
  ngOnInit(): void {
    this.form = this.buildForm(this.builder);
    this.form.statusChanges.pipe(
      map(() => this.form.valid)
    ).subscribe(this.validated.emit);
  }
  
  ngOnChanges(): void {
    if (this.customer) {
      form.patchValue(customer);
    }
  }
  
  buildForm(builder: FormBuilder): FormGroup {
    return builder.group({
      name: [null, [Validators.required, Validators.maxLength(30)]],
      title: [null, [Validators.required, Validators.maxLength(60)]],
      email: [null, [Validators.required, Validators.maxLength(35)]],
      handles: builder.group({
        twitter: [null, Validators.maxLength(50)],
        facebook: [null, Validators.maxLenth(50)]
      }),
      address: builder.group({
        city: [null, Validators.maxLength(50)],
        state: [null, Validators.maxLength(2)],
        zip: [null, [Validators.minLength(5), Validators.maxLength(10)]]
      })
    });
  }
  
  submit() {
    if (this.form.invalid) {
      return;
    }
    
    const updatedCustomer = {
      ...this.customer,
      ...this.form.value
    };
    
    this.submitted.emit(updatedCustomer);
  }
}
```
{% endtab %}

{% tab title="components/customer-edit-form.component.html" %}
```markup
<form [formGroup]="form">
    <div>
      <label>Customer Name</label>
      <input id="name" formControlName="name">
      <i class="text-red" 
         *ngIf="name.invalid && (name.dirty || name.touched)">
         Please fill out this field.
      </i>
    </div>
    <div>
      <label>title</label>
      <input id="title" formControlName="title">
      <i class="text-red" 
         *ngIf="title.invalid && (title.dirty || title.touched)">
         Please fill out this field.
      </i>    
    </div>
    <div>
      <label>Email Address</label>
      <input id="email" formControlName="email" type="email">
      <i class="text-red" 
         *ngIf="email.invalid && (email.dirty || email.touched)">
         Please fill out this field.
      </i>    
    </div>
  </div>
  <div>
    <div>
      <label>City</label>
      <input id="city" formControlName="city">
    </div>
    <div>
      <label>State</label>
      <select id="state" formControlName="state">
        <option value="AZ">Arizona</option>
        <option value="CO">Colorado</option>
        <option value="NM">New Mexico</option>
        <option value="UT">Utah</option>
      </select>
    </div>
    <div>
      <label>Zip</label>
      <input id="zip" formControlName="zip">
    </div>
  </div>
</form>
```
{% endtab %}
{% endtabs %}

