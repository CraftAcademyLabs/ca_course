## Setting the stage

Start by adding a new state, where we are adding the payment form. Why are we setting it to false? 

```js
state = {
  productData: [],
  message: {},
  orderDetails: {},
  showOrder: false,
  orderTotal: "",
  showPaymentForm: false
};
```

After that, we need to refactor the button to show the form when the user clicks on it. 


```js
<button onClick={() => this.setState({ showPaymentForm: true })}>
  Confirm!
</button>;
{
  this.state.showPaymentForm && (
    <div id="payment-form">
      <PaymentForm />
    </div>
  );
}
```
Remember to also import the <PaymentForm/> on the top of the file.
But we don't have a PaymentForm component you are thinking. Well, that's right. We don't so...
Let's create one. 

```bash
$ touch src/components/PaymentForm.jsx
```

```js
import React, { Component } from "react";

class PaymentForm extends Component {
  render() {
    return (
      <div>
        <h1>Here we will show a payment form</h1>
      </div>
    );
  }
}

export default PaymentForm;
```
Run your test, commit and all that fun stuff. 
Just to check, you did remember to create a new branch on git before we started right? Of course you did. silly me....


