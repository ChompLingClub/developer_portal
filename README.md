
# Using The Developer Portal for College Hackathons

## Table of contents:

 * Introduction to the Developer Portal
 * Amex Express Checkout
   * Displaying the button
   * Connecting a back-end service

## Introduction to the Developer Portal


## Amex Express Checkout

#### You will need...
* Basic knowledge of HTML / CSS / JS.
* An account with [Stripe](https://stripe.com/).
  * If you don't have one, make one [here](https://dashboard.stripe.com/register).
* A backend language of your choice. In this demo, I will be using Python and Flask (a simple web hosting framework).

** - Stripe supports the following languages/frameworks: Ruby, Python, Node, ASP .NET, Go, PHP. [Click here](https://stripe.com/docs/checkout/tutorial) for documentation on another language. Documentation for Java may be found on the [Amex Developer Portal website](https://developer.americanexpress.com/products/express-checkout/guide).

#### View the finished product here
Link to [my Github repo](https://github.com/dannysepler/developer_portal).


#### Getting started

American Express has partnered with the Stripe API to make the Express Checkout flow easy to implement. Much of the "Getting Started" section will resemble the Stripe documentation located [here](https://stripe.com/docs/amex-express-checkout).

1. First, load the American Express helper library onto your page.

```html
<script src="https://icm.aexp-static.com/Internet/IMDC/US_en/RegisteredCard/AmexExpressCheckout/js/AmexExpressCheckout.js"></script>
```

2. Before the Express Checkout button can be displayed on the screen, you must insert an `<amex:init>` element, which will dynamically populate our button.

```html
<amex:init client_id="CLIENT_ID" env="qa" callback="aexpCallback"/>
```

Your CLIENT_ID can be found on the Stripe Documentation [here](https://stripe.com/docs/amex-express-checkout#testing). (*Make sure you log in first!*)

Otherwise, you can use the following generic CLIENT_ID: `799a098b-b65c-47dc-abb3-d9832bfc7227`. Using a generic CLIENT_ID will prevent you from being able to track your requests made to Stripe, and will make debugging more difficult.


3. Add a form and the button tag to your page. The contents will automatically populate from the Amex API.

```html
<form id="payment-form">
	<div id="amex-express-checkout"></div>
</form>
```

4. Add jQuery to your page.

```html
<script
  src="https://code.jquery.com/jquery-3.2.1.js"
  integrity="sha256-DZAnKJ/6XZ9si04Hgrsxu/8s717jcIzLy3oi35EouyE="
  crossorigin="anonymous"></script>
```

5. Add callback logic. This will store your stripe token as a hidden field on the input. We'll come back to this part when we submit the form.

```javascript
function aexpCallback(response) {
  var token = response.token;
  if (token) {
  	console.log('Stripe token is: ' + token);
  
    var stripeToken = '<input type="hidden" name="stripeToken">';
    $('#payment-form').append($(stripeToken).val(token));
  }
};
```

6. Test it out! Load your page and try logging in with the following credentials.

* Username: *test_user*
* Password: *password*
* One-time access code: *123456*

For reference, your HTML file should look something like...

```html
<!doctype html>
<html>
    <body>
    	<amex:init client_id="CLIENT_ID" env="qa" callback="aexpCallback"/>

    
        <form id="payment-form">
          	<div id="amex-express-checkout"></div>
        </form>
      
        <script src="https://code.jquery.com/jquery-3.2.1.js"
      			integrity="sha256-DZAnKJ/6XZ9si04Hgrsxu/8s717jcIzLy3oi35EouyE="
      			crossorigin="anonymous"></script>
    	<script src="https://icm.aexp-static.com/Internet/IMDC/US_en/RegisteredCard/AmexExpressCheckout/js/AmexExpressCheckout.js"></script>
        <script>
          function aexpCallback(response) {
            var token = response.token;
            if (token) {
              console.log('Stripe token is: ' + token);

              var stripeToken = '<input type="hidden" name="stripeToken">';
              $('#payment-form').append($(stripeToken).val(token));
            }
          };
	</script>
    </body>
</html>
```

Check your logs after clicking on the Express Checkout button and logging in, and you should see a message like: `Stripe token is: <STRIPE_TOKEN>`.

If you got it, nice! :+1: Otherwise, check your spelling and return here once you receive that message.