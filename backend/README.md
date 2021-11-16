# Product Dashboard Data Flow

![Product Dashboard][dashboard-1]

Today you'll begin building a fullstack Products API using Express, Sequelize,
React and Redux! There will be two parts to this project:

1. Creating a backend API using Express and Sequelize.
2. Creating a separate frontend application using React and Redux.

When you have completed the first part of the project, your application should
have the following features:

1. A default "/" GET route.
2. A "/api/products" GET route to fetch an index of products.
3. A "/api/products" POST route to create products.
4. A "/api/products/:id" PUT route to update products.
5. A "/api/products/:id" DELETE route to delete products.

## Phase 0: Initialize project

`cd` into the `backend` directory and install your packages with `npm install`.

### Initializing Sequelize

The Sequelize CLI is already configured to know where your database
configuration is located and where to generate the `models`, `seeders`, and
`migrations` folders. The project skeleton has already taken care of
configuration by including complete `.sequelizerc`, `./config/index.js`, and
`./config/database.js` files.

### Configuring your environment variables

Before you set up your database, remember that the Sequelize CLI still
needs access to your database credentials. Create an `.env` file based off of
the `.env.example` file included in your project skeleton.

### Creating your database

Before initializing the `Product` model, you'll need to create a new database.

Take a moment to create a database user and database:

- The login user name that you should use is "product_app" (make sure to set a
  login password).
- Your user must be granted the CREATEDB privilege so that you can run `npx dotenv sequelize-cli db:create`.
- The database name that you should use is "product_app".

## Phase 1: Set up the Product model

It's time to generate and migrate your Product model with the following commands:

```sh
npx sequelize model:generate --name Product
--attributes "image:string,name:string,price:integer"
```

For this particular project, prices will only be represented as integers.

In the model and migration files, make sure to configure the product
`image`,`name` and `price` to not be nullable. Also in your migrations file add
to your createdAt and updatedAt values this key value pair

```js
defaultValue: Sequelize.fn('now')
```

in order to create a default value.
Then run your migrations:

```sh
npx dotenv sequelize db:migrate
```

After migrating, you should see the `Products` table in your Postbird client:

![postbird-migration][postbird-3]

Now generate a seed file by running the following command from the root of your
project:

```sh
npx sequelize seed:generate --name test-products
```

Go ahead and replace the contents of the
`./db/seeders/[timestamp]-test-products.js` with the following code:

```js
'use strict';

module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.bulkInsert(
      'Products',
      [
        {
          image:
            'https://d0bb7f9bf11b5ad1a6b2-6175f06f5e3f64e15abbf67415a276ec.ssl.cf1.rackcdn.com/product-images/designlab/6-panel-unconstructed-brushed-cotton-caps-cap02-black1582879924.jpg',
          name: 'Cotton Ballcap',
          price: 25,
        },
        {
          image:
            'https://media.everlane.com/image/upload/c_fill,dpr_1.0,f_auto,g_face:center,q_auto,w_auto:100:1200/v1/i/c5838652_bd67.jpg',
          name: 'White Boot',
          price: 115,
        },
        {
          image:
            'https://cdni.llbean.net/is/image/wim/233155_467_41?hei=764&wid=665&resMode=sharp2&defaultImage=llbstage/A0211793_2',
          name: 'LL Bean Raincoat',
          price: 75,
        },
      ],
      {}
    );
  },

  down: (queryInterface, Sequelize) => {
    return queryInterface.bulkDelete('Products', null, {});
  },
};
```

Now run your seed files with the command below:

```sh
npx dotenv sequelize db:seed:all
```

After seeding, you should see the products you have created in your Postbird
client:

![postbird-seed][postbird-3a]

## Phase 2: Set up test routes

Notice that your skeleton already has a basic Express application set up in
`app.js`. It's time for you to set up the routers for your project.

Create a `routes` module by creating a `routes` directory in the root of your
backend directory. Within your `routes` folder, create an `index.js` file and an
`api` directory, and in that directory, create an `index.js` file for your
default router and a `products.js` file for your products routers. In both
`index.js` files and the `api/products.js`, begin by requiring `express` and
creating a `router` with `express.Router()`. Lastly, make sure you export the
routers you just created.

A majority of RESTful APIs serve data in a JSON format. Let's do the same for
this project! To do this, update your `app.js` file to have your application
`use(express.json())`. The `express.json()` middleware is needed to parse
request body content formatted in JSON so that it is available via the
`req.body` property.

Lastly, in order to connect to the routes modules you have just created, import
your `./routes` directory as `routes` in the `app.js` file. Make sure your
application is using the `routes` router after your app has used the
`express.json()` middleware. In the `index.js` file in the `routes` directory,
import the router from the `api/index.js` file and use it at the `/api` route.
In the `api/index.js` file, import the router from the `api/products.js` file
and use it at the `/products` route. This will put the router in the
`api/products.js` file at the `/api/products` routes.

### Creating test routes

Let's get started in the `./routes/index.js` file. Move your GET route for `/`
from `app.js` into your `./routes/index.js` file. Update the route to use
`res.json()` in order to render a JSON response of `message: "test root index"`.

Now you'll add a test route in your `./routes/api/index.js` and
`./routes/api/products.js`. Create a route that handles a GET request to the
`/api` and a route that handles a GET request to the `/api/products`. path. Have
your result `send` a JSON response of `message: "test api index"` and `message:
"test products index"`, respectively.

### Testing requests on Postman

Now run `npm start` to start your server and open up Postman to test your GET
requests.

![using-postman][postman-1]

When sending a GET request to `localhost:8080/`, you should see JSON with your
"test index root" message in the body response. When sending a GET request to
`localhost:8080/api`, you should see JSON with your "test api index" message in
the body response. When sending a GET request to `localhost:8080/api/products`,
you should see JSON with your "test products index" message in the body
response.

Note that if you use your browser to navigate to `localhost:8080/`,
`localhost:8080/api` and `localhost:8080/api/products`, you'll see the same
response as in Postman.

## Phase 3: Set up product routes

Now you'll add your Product routes in your `./routes/api/products.js`. Begin by
requiring your `db` from your `../../db/models` directory:

```js
const db = require('../../db/models');
```

Now destructure your `Product` model from the `db` you have just imported:

```js
const { Product } = db;
```

In this phase, you'll interact with your `Product` model by using built-in
methods to set up the basic CRUD functionalities for products:

- `Product.findAll()` to fetch all of your database products. (Read)
- `Product.findByPk()` to fetch a database product based on the `id` from your
  request parameters. (Read)
- `Product.create()` to generate a product in your database. (Create)
- `Product.update()` to update a product in your database. (Update)
- `Product.destroy()` to delete a product from your database. (Delete)

### GET /api/products

It's time to set up a GET `/` route to fetch all of your seeded products when
sending a GET request to `localhost:8080/api/products`. Since you'll be awaiting a
database fetch, let's bring back your `asyncHandler` function.

Import the `asyncHandler` function from the `express-async-handler` package near
the top of your `routes/api/products.js` file.

```js
const asyncHandler = require('express-async-handler');
```

Let's begin by updating the `products` GET route to `/`. Wrap the route function
with your `asyncHandler` to be able to `await` the database fetch of all your
products. Look at the beginning of the Phase 3 instructions to determine which
Product model method to use.

Lastly, remember to render the products you have fetched from your database in
JSON by using `res.json({ products })`.

### GET /api/products/:id

Now you'll set up the GET `/:id(\\d+)` route to read a specific product when
sending a GET request to `localhost:8080/api/products/:id`. Parse the
`productId` from your `req.params` object and use your `productId` to fetch
a specific product from the database.

Note that the `app` module contains the following middleware function to catch
unhandled requests and pass a `404` error to the global error handler:

```js
app.use((req, res, next) => {
  const err = new Error("The requested resource couldn't be found.");
  err.status = 404;
  next(err);
});
```

This ensures that if a client makes a request to a route that doesn't exist
they'll receive an appropriate error message.

Any error that occurs in your routes will be handled by the below global error
handler so that error messages can be formatted and returned to the client in a
consistent way. The error's title, message, and stacktrace is rendered in JSON.
If your application is in production, the error will be rendered without the
error stacktrace.

```js
app.use((err, req, res, next) => {
  res.status(err.status || 500);
  const isProduction = environment === 'production';
  res.json({
    title: err.title || 'Server Error',
    message: err.message,
    stack: isProduction ? null : err.stack,
  });
});
```

Now let's go back to your GET route for a single product. Now that you have
written a database fetch for a product, you want to render errors for products
that were not found. Make sure your asynchronous route function is taking in
`next` as a parameter. If you have fetched a valid product, render the product
in JSON. If you have not fetched a valid product, you can use a function to
generate an error before invoking the `next` method.

Let's define your error generator function for products not found. In your
product route module, define a `productNotFoundError` function that takes in a
product ID. Generate a new `Error` object with a message stating that a product
of the given ID could not be found. Assign the error to have a `title` property
to be "Product not found." and a `status` of `404`. At the end of the function,
return the error.

Now that the `productNotFoundError` function is written to generate and return
an `Error` object, you can pass the return value of the `productNotFoundError`
function call into the `next` method to invoke the global error handler.

Take a moment to test your route and error handling in Postman.

### POST /api/products

Set up a POST `/` route to create a new product by sending a POST request to
`localhost:8080/api/products`. Now that you will take in JSON data to handle a
request, remember that your application is using the `express.json()` middleware
in `app.js` to parse the body content's JSON and access the `req.body`.

In the body of this route handler, `create` a new Product by passing it an
object with the data from the `req.body`. Go ahead and call `res.json` at the
end of this route to respond to the request with the next `product`.

```js
res.json({ product });
```

Now that you are taking in data, you'll need to validate that data. Import
`check` and `validationResult` from `express-validator`. Use the
`express-validator` library to check that the `image`, `name`, and `price`
values are present.

```js
const productValidation = [
  check('image')
    .exists({ checkFalsy: true })
    .withMessage('Image is required'),
  check('name')
    .exists({ checkFalsy: true})
    .withMessage('Name is required'),
  check('price')
    .exists({ checkFalsy: true })
    .withMessage('Price is required')
];
```

You'll also need to handle your validation errors. In previous projects, you've
been handling validations in each of the route handlers. Today, let's DRY up our
code and set up one middleware function that can check for errors in the `req`
object, and if there are errors, then we can generate an `Error` from that
middleware function and handle it there.

Define a `handleValidationErrors` function and have it take in `req`, `res`, and
`next` as parameters. Begin by generating a `validationErrors` object by
invoking the `validationResult` function with the request:

```js
const handleValidationErrors = (req, res, next) => {
  const validationErrors = validationResult(req);
  // TODO: Generate error object and invoke next middleware function
};
```

If you do you have validation errors, use `array()` to transform your
`validationErrors` object into a mappable array. Pluck out each error's `msg`
attribute to generate an `errors` array of error messages:

```js
const errors = validationErrors.array().map(error => error.msg);
```

After generating the `errors` array, you'll want to create a new `Error` object
with a 400 `status` and title of "Bad request":

```js
const err = new Error('Bad request.');
err.errors = errors;
err.status = 400;
err.title = 'Bad request.';
next(err);
```

You'll also want the `Error` object to set an `errors` array as a `errors`
property. Invoke the `next` middleware function with the `Error` object you have
created. If you do not have any validation errors, invoke the `next` middleware
function without an argument.

Your `handleValidationErrors` function should look something like this:

```js
const handleValidationErrors = (req, res, next) => {
  const validationErrors = validationResult(req);

  if (!validationErrors.isEmpty()) {
    const errors = validationErrors.array().map(error => error.msg);

    const err = Error('Bad request.');
    err.errors = errors;
    err.status = 400;
    err.title = 'Bad request.';
    return next(err);
  }
  next();
};
```

Now that you are adding the `.errors` property to this error you will
need to modify the generic error handler in `app.js` to add this `.errors`
array to the JSON you return, otherwise when you call our API and get validation
errors you won't be able to see them!

```js
app.use((err, req, res, next) => {
  res.status(err.status || 500);
  const isProduction = environment === 'production';
  res.json({
    title: err.title || 'Server Error',
    message: err.message,
    // Includes our array of validation errors in our JSON response
    errors: err.errors,
    stack: isProduction ? null : err.stack,
  });
});
```

Now make sure the POST route is using your product validations and
`handleValidationErrors` function as middleware. Test your new route in Postman.
Don't forget to set the "Content-Type" header to "application/json" and send
raw JSON data through the request body. Feel free to review how to send a POST
request through the image below.

![postman-post][postman-2a]

Take a moment to also test your error handling. You should see an error response
in JSON upon submitting bad data (i.e. an empty `message` field). Notice that
your error response has the following properties: a `status` of "400", a `title`
of "Bad request.", and an array of `errors`. Make sure that you see a `400 Bad
Request` error if there are failing data validations.

Don't forget to also test what happens when you submit valid data. The backend
route should respond with the newly created product item without any errors.

### PUT /api/products/:id

Set up a PUT `/:id(\\d+)` route to update a product. Begin by parsing the
product id within your `req.params` object and using the parsed `productId` to
fetch the product to update. If you have a valid product, await the update and
then render the updated product in JSON format.

If you have not fetched a valid product, use the same `404` error handling that
the `GET /api/products/:id` route used. Invoke your `productNotFoundError`
function to generate a `404` error. Then pass the return value of the
`productNotFoundError` function call into the `next` method.

Make sure to also use the same `400` error handling that the `POST
/api/products` route used. Validate your data and handle your validation errors,
like in the POST route, to generate an error response upon receiving bad form
data.

Test your PUT route:

1. Use Postman to send a GET request to `localhost:8080/api/products/1` to view the
   data of your first database product.
2. Configure your "Content-Type" header as "application/json".
3. Send a PUT request to `localhost:8080/api/products/1` with updated fields in the
   `raw` request body.
4. View your updated product in Postbird.
5. Send a PUT request to `localhost:8080/api/products/1` with invalid data to check
   the error handling.

### DELETE /products/:id

Set up the DELETE `/:id(\\d+)` route for deleting a product by sending a DELETE
request to `localhost:8080/api/products/:id`. Begin by parsing the product ID
and using the id to find your product to delete. If a valid product is found,
await to destroy the product and render a `204` response to confirm the deletion
by using `res.status(204).end()`.

If a valid product wasn't found, use the same `404` error handling that the GET
and PUT routes used. Pass the product ID into your `productNotFoundError`
function call to generate a `404` error. Then pass your newly generated `404`
error into the `next` method to invoke the global error handler.

Lastly, send a request with Postman to test your DELETE route and delete a
product. Verify that the product was properly deleted by viewing your seeded
objects with Postbird.

**Your backend is complete! Now move on to the [frontend][frontend]**
**instructions.**

[postbird-3]: https://jd-image-upload.s3.amazonaws.com/postbird3.png
[postbird-3a]: https://jd-image-upload.s3.amazonaws.com/postbird4.png
[postbird-4]: https://appacademy-open-assets.s3-us-west-1.amazonaws.com/Module-Express/projects/api-tweets-project-postbird-4.png
[postman-1]: https://jd-image-upload.s3.amazonaws.com/test-backend-initial.gif
[postman-2a]: https://pm-image-upload.s3.amazonaws.com/postman-post.gif
[proxy-1]: https://create-react-app.dev/docs/proxying-api-requests-in-development/
[dashboard-1]: https://jd-image-upload.s3.amazonaws.com/product-dashboard.gif
[frontend]: ../frontend/README.md
