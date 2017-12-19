
# Using The Amex Developer Portal For College Hackathons

###### A brief introduction to The HitchHacker's Guide To Our Galaxy.

## Table of contents:

 * Introduction to the Developer Portal
 * Amex Express Checkout
   * Displaying the button
   * Connecting a back-end service

## Introduction to the Developer Portal

American Express powers billions of transactions worldwide. To make its software more accessible, Amex released the [Developer Portal](https://developer.americanexpress.com/home) in October 2016. Currently, 12 APIs are featured on the portal, in the following categories:

* Fraud Prevention
* Payment Services
* Personalization Services
* Utilities

The Developer Portal is publically available and free to access. Simply sign up, give *Business* as the name of your university, and *Business Email Address* as your school email address, and get started right away!

In this article, we have focused solely on Amex Express Checkout. You can find the official documentation on the [documentation tab](https://developer.americanexpress.com/documentation) as well as the [products tab](https://developer.americanexpress.com/products) of the Developer Portal.

In this article we will focus on implementing Express Checkout. Express Checkout offers the ability to securely and easily checkout on a merchant website, by using an American Express login to autofill Card details and Account information.

## Implementing Amex Express Checkout

#### What we are creating
View the code [here](https://github.com/dannysepler/developer_portal), or play with [a live demo](http://159.89.40.188/).

#### You will need...

* Basic knowledge of HTML / CSS / JS.
* An account with [Stripe](https://stripe.com/).
  * If you don't have one, make one [here](https://dashboard.stripe.com/register).
* A backend language of your choice. In this demo, I will be using Python and Flask.

** - Stripe supports the following languages/frameworks: Ruby, Python, Node, ASP .NET, Go, PHP. [Click here](https://stripe.com/docs/checkout/tutorial) for documentation on another language. Documentation for Java may be found on the [Amex Developer Portal website](https://developer.americanexpress.com/products/express-checkout/guide).


#### Creating The Button

American Express has partnered with the Stripe API to make the Express Checkout able to implement in just a few lines of code. Much of the "Getting Started" section on this page will resemble the Stripe documentation located [here](https://stripe.com/docs/amex-express-checkout).

1. First, create a file named `index.html`.
2. Load the American Express helper library onto your page.

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
	<div id="amex-express-checkout" />
</form>
```

4. Add jQuery to your page. For more information on jQuery, refer to their [official documentation](http://api.jquery.com/).

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

* Username: **test_user**
* Password: **password**
* One-time access code: **123456**

For reference, your file should look something like...

**index.html**

```html
<!doctype html>
<html>
   <body>
   
      <!-- DOM elements -->
      <amex:init client_id="CLIENT_ID" env="qa" callback="aexpCallback"/>

      <form id="payment-form">
         <div id="amex-express-checkout" />
      </form>


      <!-- Javascript files -->
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

Check your logs after clicking on the Express Checkout button and logging in, and you should see the following message: `Stripe token is: <STRIPE_TOKEN>`.

If you got it, nice! :+1: Otherwise, check your spelling and return here once you receive the message.

#### Linking With A Backend Service

Now we have properly fetched the user token. To properly integrate with Stripe and the American Express Checkout, two backend calls must be made:

* Using the token to autofill card details and account information
* Performing the transaction itself.

The following code will be performed in Flask, a minimal Python web-hosting framework. However, feel free to use another supported language for your uses!

#### Getting your Stripe API Keys

Once you've [made a Stripe account](https://dashboard.stripe.com/register), click the "API" tab on the left side, and you should be able to see your Publishable Key and reveal your Secret Key. Save this tab for later, we will return to it.

#### Download your dependencies

To get Flask up and running, you must have:

* Download [Python](https://www.python.org/downloads/) (I am using 2.x), however 3.x should also work with this sample code with only a few modifications.
* Download [pip](https://pip.pypa.io/en/stable/), a Python installer.
* Run `pip install flask`
* Run `pip install stripe`

#### Create a main.py file

Create a file named main.py, and paste the following code in it.

```python
import os
from flask import Flask, render_template, request
import stripe

stripe_keys = {
  'secret_key': os.environ['SECRET_KEY'],
  'publishable_key': os.environ['PUBLISHABLE_KEY']
}

stripe.api_key = stripe_keys['secret_key']

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html')
```

Next, create a folder named "templates" and place your index.html file in that folder.

Your directory structure should now look like...

```
templates
--> index.html

main.py
```

#### Run your main.py file

It is bad procedure to place your API keys in version control, so we will be passing these values in at runtime. Run the following command:

`PUBLISHABLE_KEY=pk-xxxx SECRET_KEY=sk-xxxx python main.py`

replacing pk-xxxx and sk-xxxx with your publishable and secret keys respectively.

You should see the following response message:

```
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 160-121-540
```

and be able to see your button at [http://localhost:5000](http://localhost:5000).
