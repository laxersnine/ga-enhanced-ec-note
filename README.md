# Google Analytics Enhanced Ecommerce Note

- [Google Analytics Enhanced Ecommerce Note](#google-analytics-enhanced-ecommerce-note)
    - [Setup](0-setup)
        - [Load GA](#load-ga)
        - [On each page](#on-each-page)
        - [Object](#object)
            - [ImpressionFieldObject](#impressionfieldobject)
            - [ProductFieldObject](#productfieldobject)
            - [ActionFieldObject](#actionfieldobject)
    - [View product (on category page)](#1-view-product-on-the-category-page)
    - [Click product in the category page](#2-click-product-in-the-category-page)
    - [Add to cart (on the category page)](#3-add-to-cart-on-the-category-page)
    - [View product (on the product page)](#4-view-product-on-the-product-page)
    - [Add to cart (on the product page)](#5-add-to-cart-on-the-product-page)
    - [Remove from cart (at the cart)](#6-remove-from-cart-at-the-cart)
    - [Click Checkout](#7-click-checkout)
    - [Click next step button on each checkout step](#8-click-next-step-button-on-each-checkout-step)
    - [Purchase (complete order)](#9-purchase-complete-order)
    - [Refund](#10-refund)

## 0. Setup

### Load GA
``` html
<script>
/* GA code*/

// GA init
ga('create', 'UA-XXXXXXXX');

// GA plugins
ga('require', 'ec');

</script>
```

### On each page
``` html
<script type="text/javascript">
    ga('send','pageview');
</script>
```

### Object
``` Typescript
class ImpressionFieldObject {
    id: string;
    name: string;
    list: string;
    brand: string;
    category: string;
    variant: string;
    position: number;
    price: number;
}

class ProductFieldObject {
    id: string
    name: string
    brand: string
    category: string
    variant: string
    price: number;
    quantity: number;
    coupon: string;
    position: number
}

class ActionFieldObject {
    id: string;
    affiliation: string;
    revenue: number;
    tax: number;
    shipping: number;
    coupon: string
    list: string
    step: number;
    option: string; 
}

enum EcActionType {
    Click = 'click',
    Detail = 'detail',
    Add = 'add',
    Remove = 'remove',
    Checkout = 'checkout',
    CheckoutOption = 'checkout_option',
    Purchase = 'purchase',
    Refund = 'refund',
    PromoClick = 'promo_click'
}
```
- #### ImpressionFieldObject
  - id: The product id (e.g. B-612). **(This field or name is required)**
  - name: The product name (e.g. Besixdouze.) **(This field or id is required)**
  - list: The list this product belongs (e.g. Search Results).
  - brand: The brand of the product (e.g. Solar System).
  - category: The category of the product. Use / as a delimiter to specify up to 5-levels of hierarchy(e.g. Solar System/Asteroid).
  - variant: The variant of the product (e.g. Brown).
  - position: The product's position in a list or collection (e.g. 2).
  - price: The price of the product (e.g. 299.95).
- #### ProductFieldObject
  - id: The product id (e.g. B-612). **(This field or name is required)**
  - name: The product name (e.g. Besixdouze.) **(This field or id is required)**
  - brand: The brand of the product (e.g. Solar System).
  - category: The category of the product. Use / as a delimiter to specify up to 5-levels of hierarchy(e.g. Solar System/Asteroid).
  - variant: The variant of the product (e.g. Brown).
  - price: The price of the product (e.g. 299.95).
  - quantity: 
  - coupon: 
  - position: 
- #### ActionFieldObject
  - id: The transaction id **(required if the action type is purchase or refund)**.
  - affiliation: The store or affiliation where this transaction occured.
  - revenue: The total revenue including tax, shipping fee or other adjustments. **If this field is not set, its value will be automatically calculated using the product quantity and price fields of all products in the same hit.**
  - tax: The total tax.
  - shipping: The total shipping cost.
  - coupon: The coupon applied.
  - list: The list this product belongs.
  - step: The step in the checkout process (Optional on checkout actions).
  - option: Additional field for checkout and checkout_option actions that can describe option information on the checkout page, like selected payment method.


## 1. View product (on the category page)

``` TypeScript
let impressionFieldObject: ImpressionFieldObject = {};

ga('ec:addImpression', impressionFieldObject)
```

## 2. Click product in the category page

``` Typescript
let productFieldObject: ProductFieldObject = {};

let list = 'Search Results';

ga('ec:addProduct', productFieldObject);
ga('ec:setAction', EcActionType.Click, {list: list});
ga('send', 'event', list, EcActionType.Click, 'Results', {
    hitCallback: () => {/* Do something when request done (e.g. navigate to the product page). */}
});
```

## 3. Add to cart (on the category page)

``` Typescript
let productFieldObject: ProductFieldObject = {};

let list = 'Search Results';

ga('ec:addProduct', productFieldObject);
ga('ec:setAction', EcActionType.Add);
ga('send', 'event', list, EcActionType.Click, 'Add to Cart');
```

## 4. View product (on the product page)
``` Typescript
let productFieldObject: ProductFieldObject = {};

ga('ec:addProduct', productFieldObject);
ga('ec:setAction', EcActionType.Detail);
```

## 5. Add to cart (on the product page)
``` Typescript
let productFieldObject: ProductFieldObject = {};

const list = 'Product detail'

ga('ec:addProduct', productFieldObject);
ga('ec:setAction', EcActionType.Add);
ga('send', 'event', list, EcActionType.Click, 'Add to Cart');
```

## 6. Remove from cart (at the cart)
``` Typescript
let productsInTheCart: ProductFieldObject[] = [];

const onProductRemoveBtnClick = (product: ProductFieldObject) => {
    ga('ec:addProduct', product)
    ga('ec:setAction', EcActionType.Remove);
    ga('send', 'event', 'Cart', EcActionType.Click, 'Remove from Cart');
}
```

## 7. Click Checkout
``` Typescript
let productsInTheCart: ProductFieldObject[] = [];

for(let product of productsInTheCart) {
    ga('ec:addProduct', product);
}

ga('ec:setAction', EcActionType.Checkout, {step: 1});
ga('send', 'pageview');
```

## 8. Click next step button on each checkout step
``` Typescript
const nextStep = 2;

ga('ec:setAction', EcActionType.Checkout, {step: 2});
ga('send', 'pageview');
```

## 9. Purchase (complete order)
``` Typescript
let productsInTheCart: ProductFieldObject[] = [];

for(let product of productsInTheCart) {
    ga('ec:addProduct', product);
}

ga('ec:setAction', EcActionType.Purchase, {step: 1});
ga('send', 'pageview');
```

## 10. Refund 
``` Typescript
// No need if refund the entire order.
let productsToRefund: ProductFieldObject[] = [];
for(let product of productsInTheCart) {
    ga('ec:addProduct', product);
}

let transactionId: string = 'XXXXXXX';
let category = 'yyyyy'; // e.g. Ecommerce
let actionName = 'Refund';
ga('ec:setAction', EcActionType.Refund, {id: transactionId});
ga('send', 'events', category, actionName, {nonInteraction: 1});
```
**If you need to send refund data using an event and the event is not part of normally measured onsite behavior (i.e. not user initiated), then it’s recommended that you send a non-interaction event. This will prevent metrics such as bounce rate, time on site, etc. from being affected by the event.**
