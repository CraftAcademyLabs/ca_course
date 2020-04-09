## The payment form 

Wrap the PaymentForm component with the Elements, and make sure that you import Elements from `"react-stripe-elements"`

```js
{
  this.state.showPaymentForm && (
    <div id="payment-form">
      <Elements>
        <PaymentForm orderDetails={this.state.orderDetails} />
      </Elements>
    </div>
  );
}
```

Move over to your PaymentForm and update it with the following code. 

```js
import React, { Component } from "react";
import { injectStripe, CardNumberElement } from "react-stripe-elements";

class PaymentForm extends Component {
  render() {
    return (
      <>
        <label>Card Number</label>
        <CardNumberElement />
      </>
    );
  }
}

export default injectStripe(PaymentForm);
```

So what are we doing here? We are adding an input field where the users can add their card number but to use that we need to use the injectStripe component to make use of their form elements.  

So we have added a rudimentary payment form and we have also added the package that we need to use stripe in our react app. But we need multiple input fields for the rest of our card information. So let's add that now. 

