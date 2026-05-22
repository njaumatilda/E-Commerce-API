# KODECAMP 5.0 NODE.JS BEGINNER TRACK

## Overview
A robust backend API for an e-commerce platform built with Node.js, Express, and Mongoose, featuring user authentication (JWT), product catalog management, brand administration, order processing, and real-time order status updates via Socket.io.


## Features

- User Registration and Sign-In
- Viewing, Adding & Deleting of Products
- Viewing Products by Brand
- Viewing, Adding, Updating & Deleting Brands
- Customer Order Requests
- Viewing, Adding & Updating of Orders
- Change of Order Status by Admins
- Real time updates on order shipping status for customers
- User Profile Viewing

| **Only admins are allowed to access the POST, PUT and DELETE brand endpoints** |


## Tech Stack
- Node.js
- Express.js 
- Mongoose
- MongoDB Atlas
- Socket.io


## Local setup instructions
1. Clone the repository

```bash
git clone git@github.com:njaumatilda/E-Commerce-API.git
```

2. Navigate to the project directory

```bash
cd E-Commerce-API
```

3. Install dependencies

```bash
npm install
```

4. Configure environment variables

To run this project, you will need to create a `.env` file in the project directory and make sure it is included in the `.gitignore` file. Configure the following environment variables:

```env
DB_URL = your-db-url
PORT = your-port
SALT = your-salt
JWT_KEY = your-secret-key
```

> Replace:
>
> `your-db-url` with your specified db url, 
>
> `your-port` with your specified port, 
>
>`your-salt` with your specified salt and 
>
>`your-secret-key` with your specified secret key

6. Start the server

```bash
npm start
```

## API Documentation
Here is the reference on the usage of the API: 
[API Documentation](https://documenter.getpostman.com/view/38132076/2sB3Hhs2Ue)

## Deployment
The API has been deployed to a publicly accessible endpoint on Render:
[Live URL](https://task-7-e-commerce-auth-app.onrender.com)

## Author
[Matilda Njau](https://github.com/njaumatilda) 