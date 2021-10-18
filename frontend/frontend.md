# Product Dashboard Frontend

You will now create the Frontend for your Product Dashboard.

![Product Dashboard][dashboard-1]

## Phase 0: Getting Started

Inside your product directory, on the same level as your backend, clone the
frontend starter from [address]. Change the name of this directory to `frontend`

So you should now have a `frontend` and a `backend` directory.

Test your frontend by navigating into your directory and running `npm install`
and then `npm start`.

### Explore the frontend application

Your frontend currently contains:

- `index.js` at the root: You will notice that `BrowserRouter` has already been
  included.
- `App.js`: This component will handle your Navigation Component and your
  routing.
- `store` directory: Your store boilerplate in the `index.js` file has been
  supplied for you except for the reducer that you will add.
- `store -> product.js`: This is where you will add you `productReducer`, thunks
  and action creators.
- `Products`: This component will dispatch an action to get all of your products
  and will also list all of your products.
- `ProductDetail`: This component will describe the product as well as handle
  the delete code.
- `CreateProduct`: This component will contain a form to create a new product.
- `index.css`: Styles have been created to use in this application.

### Add a proxy

To tell your frontend server to
[proxy][proxy]
any unknown requests to your backend server in development, add a proxy field to
your `package.json`. Place the code beneath your devDependencies as shown in the
code below.

```js
  "devDependencies": {
    "redux-logger": "^3.0.6"
  },
  "proxy": "http://localhost:8080"
```

You will now be able to make fetch calls to your backend using a relative path.

### Routes

Now create routes and paths for the components that have already been included.
You want your Navigation to always render at the top of the screen. You want the
Product Component or the `CreateProduct` Component to render based on your link
choice in the Navbar.

Go to the Navigation Component. Create a `ul` with 2 `li`s. Each `li` should
contain a Navlink. The first `li` should have a `Navlink` for the url `'/'`
which leads to the **Products** component. The second `li` should have a
`NavLink` for the url `/create` which is for the **CreateProduct** component

Now go to your `App.js` and set up the routes for these Navlinks. Remove the
default code but leave the Fragments. Add your Navigation component to the jsx.
Beneath the Navigation component create the routes for the Products and
`CreateProduct` components. Make sure the route for the Products component is an
exact path.

Test your links in the browser. You should now be able to click on each option
in the Navbar and see the page render the corresponding component.

### Store

In the `products.js` file of your store directory, create a `productReducer`
with an empty object as the `initialState`.

Now go to your the `index.js` file in your store and add that `productReducer`
to your `rootReducer`. It should be named `product`.

Test that your store is connected by going to your browser and looking in your
Redux DevTools. Choose the **State** option in the DevTools and you should see a
product key with an empty object.

![initialState][devtools-1]

## Phase 1: Fetch all of your Products

**\*\*You might want to run your backend server in order to test routes during**
**this phase\*\***

### Store

In the `product.js` in your store directory, create a thunk `getAllProducts`
that fetches all products from your backend `/products` GET route.
`getAllProducts` should dispatch the products to an action creator called
`addProducts`. Create a `newState` variable inside your `productReducer`
function. It should be an empty object. Create a case in your `productReducer`
for `ADD_PRODUCTS`. Each product that has been retrieved should be stored in the
newState object using the product id as the key and the product object as the
value.

![getAllProducts][reducer-1]

### Products Component

In your Products component you now want to retrieve all of the products from the
database. Dispatch `getAllProducts` after first render to fetch all of the
products. This dispatch should occur only once.

Using `useSelector`, create a variable `products` that grabs the product slice
of state and convert it to an array so the you can list your products in your
jsx.

You can test to see if you get your array by logging your products variable
beneath the declaration.

![products-1][products-1]

### ProductDetail Component

You are going to want to map out all of the products using the `ProductDetail`
Component as a child inside the `Products` component. The `ProductDetail`
component should take the id, image, name, and price as props and render
everything inside a wrapper div like below:

![product-detail][product-detail-1]

In order for CSS stylings to be applied correctly, classNames must be identical.

Now in your `Products` Component, map through your products using the
`ProductDetail` Component like below.

![products-2][products-2]

Remember to account for the fact that there will be no values on the first
render. You can use the [optional
chaining][op-chain]
operator to handle this behavior. You should now be able to render all of your
products in the route '/' with the Products Component.

## Phase 2: Create A Product

Now it is time for you to create a product.

### Store

In `product.js` of your store directory, create a thunk, `addProduct`, that adds
a product to your database using the **POST** method on your `/products` backend
route. `addProduct` should take a product as an argument and send the product to
the backend using the POST method and the `/product` backend route. When the
product returns you should dispatch the product to an action creator called
`addOneProduct` Add that product to the payload in the returned object of
`addOneProduct`. Use the `ADD_ONE_PRODUCT` case in your reducer to update the
state by adding a product to your already flattened state object.

![add-product][add-product-1]

The case in your reducer should look similar to the example below:

![add-product-reducer][add-product-2]

### CreateProduct Component

You now want to create a form that will accept all values for your new product.
For this project your image input will accept a string which can be grabbed from
an image address on the internet.

Create a form with 3 inputs, one for image, name and price as well as a submit
button. Store the values of each input in it's own slice of component state.
Remember to create controlled inputs. Your form element should take an event
listener that runs a helper function called `handleSubmit` Create your
`handleSubmit` function. In your `handleSubmit` function create a `payload`
object based on the component state which contains the image, name and price. It
should dispatch the `addProduct` thunk from your `product.js` file in your store
with values from the payload. Finally, it should navigate to the route '/' which
renders all of the products.

![create-product][add-product-3]

**Congratulations!** You have created a full data flow from React Component, to
Redux, to Express, to Postgres and back!

## Bonus One

Delete a product using the button in the `ProductDetail` component, Redux, and
the delete route on your backend. You should use the product id for this one.

## Bonus Two

Create an `UpdateComponent` component and code the complete data flow to update
the title and/or price of a product using it's id.

[devtools-1]: https://jd-image-upload.s3.amazonaws.com/devtools-initialstate.png
[reducer-1]: https://jd-image-upload.s3.amazonaws.com/get-all-products-norm.png
[products-1]: https://jd-image-upload.s3.amazonaws.com/products-1.png
[products-2]: https://jd-image-upload.s3.amazonaws.com/products-2.png
[product-detail-1]: https://jd-image-upload.s3.amazonaws.com/product-detail-1.png
[add-product-1]: https://jd-image-upload.s3.amazonaws.com/add-product.png
[add-product-2]: https://jd-image-upload.s3.amazonaws.com/add-product-reducer.png
[add-product-3]: https://jd-image-upload.s3.amazonaws.com/create-product.png
[dashboard-1]: https://jd-image-upload.s3.amazonaws.com/product-dashboard.gif
[proxy]: https://create-react-app.dev/docs/proxying-api-requests-in-development/
[op-chain]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Optional_chaining
