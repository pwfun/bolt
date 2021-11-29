---
title: "Updating carts with Merchant API"
description: "My Bolt case study"
lead: "How the Merchant API simplifies the checkout process."
date: 2020-11-28T08:48:57+00:00
lastmod: 2020-11-28T08:48:57+00:00
draft: false
images: []
menu:
  docs:
    parent: "bolt case study"
weight: 100
toc: true
---

## Getting started

When a customer decides to purchase a product in your store, Bolt works with your server using two APIs that streamline on-page checkout.

Where the [Bolt API](https://help.bolt.com/api-bolt/) creates order tokens and manages transaction-related actions, the [Merchant API](https://help.bolt.com/developers/guides/checkout-guides/custom-cart-guides/how-to-integrate/merchant_api/) handles processes that occur during checkout, like discount codes, taxes, and shipping rates.

In this guide, you’ll learn how the Merchant API works. No prior development experience is necessary.

With a practical example that uses the Merchant API's `cart.update` event, you'll gain insight into the following concepts:

* **How the Merchant API interacts with your server**
* **How to interpret Merchant API requests and responses**
* **How to identify and use potential response errors**

First up: getting to know APIs.

{{< alert icon="👉" text="Estimated reading time: 10 minutes." />}}

## Understanding APIs

<!--Since the task mentioned that this is oriented towards a non-technical audience, I tried not to assume that the reader knows what an API is. To that end, I've included a section explaining APIs from a high level before diving into the Merchant API, in particular. -->

APIs, or **application programming interfaces**, are software programs that enable communication between computers and servers. One program can request information from and send information to another program in order to perform certain tasks.

Web-based APIs, like Bolt's, often use HTTP protocols to foster this communication. These protocols define standardized **methods** that computers can use to exchange information.

The following list includes some of the most frequently used HTTP methods:

* `GET`, used to retrieve and read information
* `POST`, used to create information
* `PUT`, used to update information

When an order is created in your store, for example, your server uses the `POST` method to send information about the order to a Bolt **endpoint**, a digital location on Bolt's server that manages data. Bolt's API receives the order information and stores it on its server.

### Bolt's Merchant API

<!--Answers: "who is sending the request?"" -->

During checkout, Bolt's Merchant API retrieves and sends information to your server to handle operations like tax calculation and cart updates.

#### How the API updates carts

<!--Answers: "What has the shopper changed about their cart?" -->

To illustrate the process, suppose a customer visits your online shop, Dogecoin Swag Store. After adding a pair of sunglasses to their cart, they change their mind and decide to purchase a t-shirt instead. It just so happens to be June 21st, the summer solstice, which reminds the customer of a discount code they received from you via email.

To make these changes, the Merchant API uses the `cart.update` event, which updates the customer's cart.  The `cart.update` event requires the following:

<!--Answers: "What is the endpoint?  what is the method?" -->

* An endpoint on your store's server, in this case `/cart_update`
* An HTTP method, in this case, `POST`, that your server understands
* A properly formatted **payload** that contains the new cart information

<!-- Note: I didn't include all request requirements (like authorization). Since this is a high-level explainer and not a reference guide, I focused just on the elements necessary for the reader to understand the basics. -->

Since you've already implemented Merchant API endpoints on your server, the Merchant API sends its `POST` request to the following resource:

```curl
https://dogecoinswagstore.com/api/cart_update
```

#### Understanding the JSON payload

APIs often exchange information using **JSON**, a format for sending and receiving data.

To update your customer's cart to reflect the t-shirt and discount code they've added, the Bolt Merchant API sends your server the following JSON payload:

```JSON
"event": "cart.update",
  "data": {
    "order_reference": "order-004",
    "add_items": [
      {
        "product_id": "t-shirt-floral-44",
        "price": 999,
        "currency": "USD",
        "quantity": 1
      }
    ],
    "remove_items": [
      {
        "product_id": "sunglasses-456",
        "quantity": 1
      }
    ],
    "discount_codes_to_add": [
      "SUMMERFUN15"
    ],
    "cart": {
      "order_reference": "order-12345",
      "display_id": "ID-12345",
      "billing_address": {
        "street_address1": "42 Wallaby Way",
        "street_address2": "42 Wallaby Way",
        "locality": "Kings County",
        "region": "New York",
        "postal_code": "11209",
        "country_code": "US",
        "country": "United States",
        "name": "Paul Sherman",
        "first_name": "Paul",
        "last_name": "Sherman",
        "phone": "867-5309",
        "email": "my.email@gmail.com"
      },
      "shipments": {
        "shipping_address_id": "address-1",
        "shipping_address": {
          "street_address1": "340 86th street",
          "street_address2": "APT 220",
          "locality": "Kings County",
          "region": "New York",
          "postal_code": "11209",
          "country_code": "US",
          "country": "United States",
          "name": "Charlotte Charles",
          "first_name": "Charlotte",
          "last_name": "Charles",
          "phone": "123-4567",
          "email": "my.email@gmail.com"
        },
        "total_weight": 55,
        "total_weight_unit": "kg",
        "shipping_method": "Shipping Method",
        "carrier": "FedEx",
        "reference": "1123",
        "signature": "a1B2s3dC4f5g5D6hj6E7k8F9l0",
        "service": "Option 1",
        "expedited": false,
        "cost": 1200,
        "tax_amount": 300,
        "package_type": "Standard",
        "package_width": 222,
        "package_height": 103,
        "package_depth": 90,
        "package_dimension_unit": "cm",
        "estimated_delivery_date": "08-30-2022",
      }
    }
  }
}
```

Breaking the payload down into parts shows the key information the Merchant API has sent. This `cart.update` request contains several objects that pass the data your server needs to update the customer's cart.  

First, the `add_items` array adds the `t-shirt-floral-44` product to the customer's cart, while `remove_items` deletes `sunglasses-456`, the sunglasses the customer no longer wants:

```JSON
"add_items": [
  {
    "product_id": "t-shirt-floral-44",
    "price": 999,
    "currency": "USD",
    "quantity": 1
  }
],
"remove_items": [
  {
    "product_id": "sunglasses-456",
    "quantity": 1
  }
]  
```

<!--Answers: "How do Bolt and the Merchant both know they're updating the correct order?"; "who is paying for the order?" -->

Next, the `cart` object confirms the order reference number so that your server associates the payload with the correct order. The `cart` object also contains the customer's billing information:

```JSON
"cart": {
     "order_reference": "order-12345",
     "display_id": "ID-12345",
     "billing_address": {
       "street_address1": "42 Wallaby Way",
       "street_address2": "42 Wallaby Way",
       "locality": "Kings County",
       "region": "New York",
       "postal_code": "11209",
       "country_code": "US",
       "country": "United States",
       "name": "Paul Sherman",
       "first_name": "Paul",
       "last_name": "Sherman",
       "phone": "867-5309",
       "email": "my.email@gmail.com"
     }
   }
```

Your store now knows that the customer, `Paul Sherman`, has made changes to order `12345.` Mr. Sherman plans to ship the order to a different address, though, shown in the `shipments` object:

```JSON
"shipments": {
        "shipping_address_id": "address-1",
        "shipping_address": {
          "street_address1": "340 86th street",
          "street_address2": "APT 220",
          "locality": "Kings County",
          "region": "New York",
          "postal_code": "11209",
          "country_code": "US",
          "country": "United States",
          "name": "Charlotte Charles",
          "first_name": "Charlotte",
          "last_name": "Charles",
          "phone": "123-4567",
          "email": "my.email@gmail.com"
        }
      }
```

In the next section, you'll learn how to interpret responses your server sends back to the Merchant API.

#### Reading API request responses

After your server receives a request from the Merchant API, it responds with an HTTP **status code** that tells Bolt if the original request succeeded or failed.

This **response** is typically formatted in JSON, like the request and payload in the `cart.update` request in the previous section.

<!--Answers: "What would a successfully added discount code look like in JSON?" -->

For example, suppose Mr. Sherman adds the `SUMMERFUN15` discount code to his cart. After Bolt verifies the code's validity, it uses the `POST` method to instruct your server to apply the discount:

```JSON
  "event": "discounts.code.apply",
  "data": {
    "discount_code": "SUMMERFUN15",
    "cart": {
      "order_reference": "order-12345",
      "display_id": "ID-12345",
      "currency": {
        "currency": "USD",
        "currency_symbol": "$"
        },
      },
    }
```

After your store server receives and successfully processes the request, it replies with an HTTP `200` status code, indicating the request's success:

```JSON
  "event": "discounts.code.apply",
  "status": "success",
  "data": {
    "discount_code": "SUMMERFUN15",
    "description": "Take 15% off of your order.",
    "discount_type": "percentage",
    "discount_amount": 754,
    "category": "hats"
    }
  }
```
<!--Answers: "What kinds of issues could cause this request to receive an error response?" -->

Sometimes, though, a Merchant API request fails. Your server's response includes an **error code** that tells Bolt what caused the failure and what action(s) your customer needs to take.  

Checkout issues that could produce error responses include the following:

* An item in a customer's cart is no longer available.
* A customer's session times out due to inactivity.
* A discount code can't be applied to certain items.

Suppose, for example, that the t-shirt Mr. Sherman added to his cart isn't eligible for a discount. After Bolt tries to apply the discount via the `cart.update` request, your server would respond with HTTP status code `422`, indicating that the request failed, and error code `2001006`, which specifies the failure reason:

```JSON
"event": "cart.update",
"status": "failure",
"errors": [
    {
      "code": 2001006,
      "message": "One or more items in the cart are not eligible for discount. Please refresh and try again."
    }
  ]
}
```

Detailed error code messages help you and your shoppers identify and resolve potential checkout issues. For a comprehensive list of recommended error codes, see Bolt's [error code developer reference](https://help.bolt.com/developers/references/error-codes/).

## Next steps

You now have a better understanding of APIs, generally, and Bolt's Merchant API, specifically. This knowledge can help you improve the way your server interacts with the Merchant API.

If you’re interested in discovering even more ways that Bolt can improve your store's efficiency, check out our [Merchant Tutorial guides](https://help.bolt.com/learn/merchant-tutorials/).  

## Need help?

Reach out to us with any questions you may have. [Help →](https://help.bolt.com/support)
