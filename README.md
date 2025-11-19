# E-commerce Authentication & Order Management API

## Overview
A robust backend API for an e-commerce platform built with Node.js, Express, and Mongoose, featuring user authentication (JWT), product catalog management, brand administration, order processing, and real-time order status updates via Socket.io.

## Features
-   **Node.js**: Asynchronous event-driven JavaScript runtime for scalable network applications.
-   **Express.js**: Fast, unopinionated, minimalist web framework for Node.js.
-   **Mongoose**: MongoDB object modeling tool designed to work in an asynchronous environment.
-   **MongoDB**: NoSQL database for flexible and scalable data storage.
-   **JWT (JSON Web Tokens)**: Secure stateless authentication for API endpoints.
-   **Bcrypt**: Hashing library for secure password storage.
-   **Socket.io**: Enables real-time, bidirectional, event-based communication for order status notifications.
-   **Role-Based Authorization**: Differentiates access for 'admin' and 'customer' roles.
-   **Pagination**: Efficiently retrieves product listings by brand.

## Getting Started
### Installation
1.  Clone the repository:
    ```bash
    git clone git@github.com:njaumatilda/task_7_e-commerce_auth_app.git
    cd task_7_e-commerce_auth_app
    ```
2.  Install project dependencies:
    ```bash
    npm install
    ```
3.  Create a `.env` file in the root directory and configure environment variables.
4.  Start the development server:
    ```bash
    npm start
    ```

### Environment Variables
All required environment variables must be defined in a `.env` file in the project root.

-   `PORT`: The port number on which the Express server will run.
    -   Example: `PORT=3000`
-   `DB_URL`: The connection string for your MongoDB database.
    -   Example: `DB_URL=mongodb://localhost:27017/ecommerce_db`
-   `JWT_KEY`: A secret key used for signing and verifying JSON Web Tokens.
    -   Example: `JWT_KEY=your_jwt_secret_key_here`
-   `SALT`: The number of salt rounds to use when hashing passwords with bcrypt.
    -   Example: `SALT=10`

## API Documentation
### Base URL
`http://localhost:[PORT]` (Replace `[PORT]` with the value from your `.env` file)

All authenticated endpoints require a `Bearer` token in the `Authorization` header.
Example: `Authorization: Bearer <your_jwt_token>`

### Endpoints

#### POST /auth/register
Registers a new user with an email, password, full name, and role.
**Request**:
```json
{
  "fullName": "Jane Doe",
  "email": "jane.doe@example.com",
  "password": "SecurePassword123",
  "role": "customer"
}
```
**Response**:
```json
{
  "message": "Account created successfully",
  "newUser": {
    "fullName": "Jane Doe",
    "email": "jane.doe@example.com",
    "role": "customer",
    "_id": "65f8c8d8b2a3c4d5e6f7a8b9",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  }
}
```
**Errors**:
-   `409 Conflict`: Email is already in use.
-   `500 Internal Server Error`: Server-side error.

#### POST /auth/login
Authenticates a user and returns a JWT token.
**Request**:
```json
{
  "email": "jane.doe@example.com",
  "password": "SecurePassword123"
}
```
**Response**:
```json
{
  "message": "Login was successful",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "userId": "65f8c8d8b2a3c4d5e6f7a8b9",
    "fullName": "Jane Doe",
    "email": "jane.doe@example.com",
    "role": "customer"
  }
}
```
**Errors**:
-   `404 Not Found`: User does not exist.
-   `401 Unauthorized`: Incorrect password.
-   `500 Internal Server Error`: Server-side error.

#### GET /products
Retrieves a list of all products.
**Request**: None
**Response**:
```json
[
  {
    "_id": "65f8c8d8b2a3c4d5e6f7a8c0",
    "productName": "Wireless Earbuds",
    "brand": "65f8c8d8b2a3c4d5e6f7a8c1",
    "ownerId": "65f8c8d8b2a3c4d5e6f7a8b9",
    "category": "Electronics",
    "cost": 59.99,
    "productImages": ["url1.jpg", "url2.jpg"],
    "description": "High-quality wireless earbuds...",
    "stockStatus": "in-stock",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  }
]
```
**Errors**:
-   `500 Internal Server Error`: Server-side error.

#### GET /products/:brand/:page/:limit
Retrieves paginated products for a specific brand.
**Request**:
-   `brand` (Path Parameter): The ID of the brand.
-   `page` (Path Parameter): The page number for pagination.
-   `limit` (Path Parameter): The number of items per page.
**Response**:
```json
{
  "message": "Product(s) retrieved successfully",
  "brandProducts": {
    "docs": [
      {
        "_id": "65f8c8d8b2a3c4d5e6f7a8c0",
        "productName": "Wireless Earbuds",
        "brand": {
          "_id": "65f8c8d8b2a3c4d5e6f7a8c1",
          "brandName": "SoundWave",
          "aboutBrand": "Innovating audio solutions",
          "country": "USA",
          "socialLinks": [],
          "website": "http://soundwave.com"
        },
        "ownerId": "65f8c8d8b2a3c4d5e6f7a8b9",
        "category": "Electronics",
        "cost": 59.99,
        "productImages": ["url1.jpg", "url2.jpg"],
        "description": "High-quality wireless earbuds...",
        "stockStatus": "in-stock",
        "createdAt": "2023-01-01T12:00:00.000Z",
        "updatedAt": "2023-01-01T12:00:00.000Z",
        "__v": 0
      }
    ],
    "totalDocs": 1,
    "limit": 2,
    "page": 1,
    "totalPages": 1,
    "pagingCounter": 1,
    "hasPrevPage": false,
    "hasNextPage": false,
    "prevPage": null,
    "nextPage": null
  }
}
```
**Errors**:
-   `400 Bad Request`: Invalid ID format for brand.
-   `500 Internal Server Error`: Server-side error.

#### POST /products
Adds a new product. Requires `admin` role. If the brand does not exist, it will be created.
**Request**:
```json
{
  "productName": "Smart Watch Pro",
  "brandName": "TechGadgets",
  "aboutBrand": "Leading innovation in wearable tech.",
  "country": "Canada",
  "socialLinks": ["http://facebook.com/techgadgets"],
  "website": "http://techgadgets.com",
  "category": "Wearables",
  "cost": 199.99,
  "productImages": ["watch1.jpg", "watch2.jpg"],
  "description": "Advanced smartwatch with health tracking.",
  "stockStatus": "in-stock"
}
```
**Response**:
```json
{
  "message": "Product added successfully",
  "newProduct": {
    "productName": "Smart Watch Pro",
    "brand": "65f8c8d8b2a3c4d5e6f7a8c2",
    "ownerId": "65f8c8d8b2a3c4d5e6f7a8b9",
    "category": "Wearables",
    "cost": 199.99,
    "productImages": ["watch1.jpg", "watch2.jpg"],
    "description": "Advanced smartwatch with health tracking.",
    "stockStatus": "in-stock",
    "_id": "65f8c8d8b2a3c4d5e6f7a8c3",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  }
}
```
**Errors**:
-   `401 Unauthorized`: User does not have permissions to add products.
-   `500 Internal Server Error`: Server-side error.

#### DELETE /products/:id
Deletes a product by ID. Requires `admin` role.
**Request**:
-   `id` (Path Parameter): The ID of the product to delete.
**Response**:
```json
{
  "message": "Product deleted successfully",
  "deletedProduct": {
    "_id": "65f8c8d8b2a3c4d5e6f7a8c3",
    "productName": "Smart Watch Pro",
    "brand": "65f8c8d8b2a3c4d5e6f7a8c2",
    "ownerId": "65f8c8d8b2a3c4d5e6f7a8b9",
    "category": "Wearables",
    "cost": 199.99,
    "productImages": ["watch1.jpg", "watch2.jpg"],
    "description": "Advanced smartwatch with health tracking.",
    "stockStatus": "in-stock",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  }
}
```
**Errors**:
-   `400 Bad Request`: Invalid ID format.
-   `401 Unauthorized`: User does not have permissions to delete products.
-   `404 Not Found`: Product does not exist.
-   `500 Internal Server Error`: Server-side error.

#### GET /brands
Retrieves a list of all brands.
**Request**: None
**Response**:
```json
[
  {
    "_id": "65f8c8d8b2a3c4d5e6f7a8c1",
    "brandName": "SoundWave",
    "aboutBrand": "Innovating audio solutions.",
    "country": "USA",
    "socialLinks": [],
    "website": "http://soundwave.com",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  }
]
```
**Errors**:
-   `500 Internal Server Error`: Server-side error.

#### POST /brands
Adds a new brand. Requires `admin` role.
**Request**:
```json
{
  "brandName": "GlobalFootwear",
  "aboutBrand": "Specializing in athletic and casual shoes.",
  "country": "Germany",
  "socialLinks": ["http://instagram.com/globalfootwear"],
  "website": "http://globalfootwear.de"
}
```
**Response**:
```json
{
  "message": "Brand added successfully",
  "newBrand": {
    "brandName": "GlobalFootwear",
    "aboutBrand": "Specializing in athletic and casual shoes.",
    "country": "Germany",
    "socialLinks": ["http://instagram.com/globalfootwear"],
    "website": "http://globalfootwear.de",
    "_id": "65f8c8d8b2a3c4d5e6f7a8c4",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  }
}
```
**Errors**:
-   `401 Unauthorized`: User does not have permissions to add brands.
-   `409 Conflict`: Brand already exists.
-   `500 Internal Server Error`: Server-side error.

#### PUT /brands
Updates an existing brand's details. Requires `admin` role.
**Request**:
```json
{
  "brandName": "GlobalFootwear",
  "country": "USA",
  "website": "http://globalfootwear.com"
}
```
**Response**:
```json
{
  "message": "Brand updated successfully",
  "findBrandToUpdate": {
    "_id": "65f8c8d8b2a3c4d5e6f7a8c4",
    "brandName": "GlobalFootwear",
    "aboutBrand": "Specializing in athletic and casual shoes.",
    "country": "USA",
    "socialLinks": ["http://instagram.com/globalfootwear"],
    "website": "http://globalfootwear.com",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  }
}
```
**Errors**:
-   `401 Unauthorized`: User does not have permissions to update brands.
-   `404 Not Found`: Brand does not exist.
-   `500 Internal Server Error`: Server-side error.

#### DELETE /brands
Deletes a brand by name. Requires `admin` role.
**Request**:
-   `brandName` (Query Parameter): The name of the brand to delete.
**Response**:
```json
{
  "message": "Brand deleted successfully",
  "findBrandToDelete": {
    "_id": "65f8c8d8b2a3c4d5e6f7a8c4",
    "brandName": "GlobalFootwear",
    "aboutBrand": "Specializing in athletic and casual shoes.",
    "country": "USA",
    "socialLinks": ["http://instagram.com/globalfootwear"],
    "website": "http://globalfootwear.com",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  }
}
```
**Errors**:
-   `401 Unauthorized`: User does not have permissions to delete brands.
-   `404 Not Found`: Brand does not exist.
-   `500 Internal Server Error`: Server-side error.

#### GET /order
Retrieves all orders. Requires `admin` role.
**Request**: None
**Response**:
```json
[
  {
    "_id": "65f8c8d8b2a3c4d5e6f7a8c5",
    "productName": "Wireless Earbuds",
    "productId": "65f8c8d8b2a3c4d5e6f7a8c0",
    "ownerId": "65f8c8d8b2a3c4d5e6f7a8b9",
    "quantity": 1,
    "totalCost": 59.99,
    "shippingStatus": "pending",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  }
]
```
**Errors**:
-   `401 Unauthorized`: User does not have permissions to view orders.
-   `500 Internal Server Error`: Server-side error.

#### GET /order/:id
Retrieves a single order by ID. Requires `admin` role.
**Request**:
-   `id` (Path Parameter): The ID of the order.
**Response**:
```json
{
  "_id": "65f8c8d8b2a3c4d5e6f7a8c5",
  "productName": "Wireless Earbuds",
  "productId": "65f8c8d8b2a3c4d5e6f7a8c0",
  "ownerId": "65f8c8d8b2a3c4d5e6f7a8b9",
  "quantity": 1,
  "totalCost": 59.99,
  "shippingStatus": "pending",
  "createdAt": "2023-01-01T12:00:00.000Z",
  "updatedAt": "2023-01-01T12:00:00.000Z",
  "__v": 0
}
```
**Errors**:
-   `400 Bad Request`: Invalid ID format.
-   `401 Unauthorized`: User does not have permissions to view orders.
-   `404 Not Found`: Order does not exist.
-   `500 Internal Server Error`: Server-side error.

#### POST /order
Creates a new order. Requires `customer` role.
**Request**:
```json
{
  "productName": "Wireless Earbuds",
  "quantity": 2,
  "shippingStatus": "pending"
}
```
**Response**:
```json
{
  "message": "Order created successfully",
  "newOrder": [
    {
      "productName": "Wireless Earbuds",
      "productId": "65f8c8d8b2a3c4d5e6f7a8c0",
      "ownerId": "65f8c8d8b2a3c4d5e6f7a8b9",
      "quantity": 2,
      "totalCost": 119.98,
      "shippingStatus": "pending",
      "_id": "65f8c8d8b2a3c4d5e6f7a8c6",
      "createdAt": "2023-01-01T12:00:00.000Z",
      "updatedAt": "2023-01-01T12:00:00.000Z",
      "__v": 0
    }
  ]
}
```
**Errors**:
-   `401 Unauthorized`: User does not have permissions to create orders.
-   `404 Not Found`: Product does not exist.
-   `500 Internal Server Error`: Server-side error.

#### PATCH /order/:id
Updates the shipping status of an order. Requires `admin` role. Notifies the order owner via Socket.io.
**Request**:
-   `id` (Path Parameter): The ID of the order to update.
```json
{
  "shippingStatus": "shipped"
}
```
**Response**:
```json
{
  "message": "Order status updated successfully",
  "updatedOrder": {
    "_id": "65f8c8d8b2a3c4d5e6f7a8c6",
    "productName": "Wireless Earbuds",
    "productId": "65f8c8d8b2a3c4d5e6f7a8c0",
    "ownerId": "65f8c8d8b2a3c4d5e6f7a8b9",
    "quantity": 2,
    "totalCost": 119.98,
    "shippingStatus": "shipped",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  }
}
```
**Errors**:
-   `400 Bad Request`: Invalid ID format or Invalid order status value.
-   `401 Unauthorized`: User does not have permissions to change order status.
-   `404 Not Found`: Order does not exist.
-   `500 Internal Server Error`: Server-side error.

#### GET /profile
Retrieves the profile of the authenticated user.
**Request**: None
**Response**:
```json
{
  "_id": "65f8c8d8b2a3c4d5e6f7a8b9",
  "fullName": "Jane Doe",
  "email": "jane.doe@example.com",
  "role": "customer"
}
```
**Errors**:
-   `404 Not Found`: User not found.
-   `500 Internal Server Error`: Server-side error.

#### GET /order-history
Retrieves order history. If `admin`, retrieves all orders. If `customer`, retrieves personal orders.
**Request**: None
**Response** (Customer Role Example):
```json
[
  {
    "_id": "65f8c8d8b2a3c4d5e6f7a8c5",
    "productName": "Wireless Earbuds",
    "productId": "65f8c8d8b2a3c4d5e6f7a8c0",
    "ownerId": "65f8c8d8b2a3c4d5e6f7a8b9",
    "quantity": 1,
    "totalCost": 59.99,
    "shippingStatus": "pending",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  }
]
```
**Response** (Admin Role Example - similar to `GET /order`):
```json
[
  {
    "_id": "65f8c8d8b2a3c4d5e6f7a8c5",
    "productName": "Wireless Earbuds",
    "productId": "65f8c8d8b2a3c4d5e6f7a8c0",
    "ownerId": "65f8c8d8b2a3c4d5e6f7a8b9",
    "quantity": 1,
    "totalCost": 59.99,
    "shippingStatus": "pending",
    "createdAt": "2023-01-01T12:00:00.000Z",
    "updatedAt": "2023-01-01T12:00:00.000Z",
    "__v": 0
  },
  {
    "_id": "65f8c8d8b2a3c4d5e6f7a8c6",
    "productName": "Smart Watch Pro",
    "productId": "65f8c8d8b2a3c4d5e6f7a8c3",
    "ownerId": "65f8c8d8b2a3c4d5e6f7a8d0",
    "quantity": 1,
    "totalCost": 199.99,
    "shippingStatus": "delivered",
    "createdAt": "2023-01-02T12:00:00.000Z",
    "updatedAt": "2023-01-02T12:00:00.000Z",
    "__v": 0
  }
]
```
**Errors**:
-   `404 Not Found`: User not found.
-   `500 Internal Server Error`: Server-side error.

## Technologies Used

| Category       | Technology      | Version  | Purpose                                   |
| :------------- | :-------------- | :------- | :---------------------------------------- |
| **Backend**    | Node.js         | ^18.x.x  | JavaScript runtime environment            |
|                | Express         | ^5.1.0   | Web framework for API development         |
| **Database**   | MongoDB         | N/A      | NoSQL database                            |
|                | Mongoose        | ^8.16.4  | ODM for MongoDB                           |
|                | mongoose-paginate-v2 | ^1.9.1 | Pagination plugin for Mongoose            |
| **Authentication** | jsonwebtoken | ^9.0.2   | JWT implementation for API security       |
|                | bcrypt          | ^6.0.0   | Password hashing library                  |
| **Real-time**  | Socket.io       | ^4.8.1   | Real-time communication for notifications |
| **Utilities**  | dotenv          | ^17.2.0  | Loads environment variables from a .env file |

## Contributing
Contributions are welcome! If you have suggestions for improvements or new features, please follow these steps:

1.  Fork the repository.
2.  Create a new branch for your feature or bugfix: `git checkout -b feature/your-feature-name` or `bugfix/issue-description`.
3.  Make your changes and ensure tests (if applicable) pass.
4.  Commit your changes with a clear and descriptive message: `git commit -m "feat: Add new user registration endpoint"`.
5.  Push your branch to your forked repository: `git push origin feature/your-feature-name`.
6.  Open a Pull Request to the `main` branch of the original repository.

## Author Info
- **Matilda Njau**
  - GitHub: [https://github.com/njaumatilda](https://github.com/njaumatilda)

## Badges
[![Node.js](https://img.shields.io/badge/Node.js-339933?style=flat&logo=nodedotjs&logoColor=white)](https://nodejs.org/)
[![Express.js](https://img.shields.io/badge/Express.js-000000?style=flat&logo=express&logoColor=white)](https://expressjs.com/)
[![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=flat&logo=mongodb&logoColor=white)](https://www.mongodb.com/)
[![Mongoose](https://img.shields.io/badge/Mongoose-800000?style=flat&logo=mongoose&logoColor=white)](https://mongoosejs.com/)
[![JSON Web Tokens](https://img.shields.io/badge/JWT-000000?style=flat&logo=jsonwebtokens&logoColor=white)](https://jwt.io/)
[![Socket.io](https://img.shields.io/badge/Socket.io-010101?style=flat&logo=socket.io&logoColor=white)](https://socket.io/)

[![Readme was generated by Dokugen](https://img.shields.io/badge/Readme%20was%20generated%20by-Dokugen-brightgreen)](https://www.npmjs.com/package/dokugen)