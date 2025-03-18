# Stock Portfolio Web Application (ASP.NET Core)

This is the backend of a web application that allows users to create and manage their stock portfolio. Built with **ASP.NET Core**, this project integrates with the **Financial Modeling Prep API** to seed and retrieve real-time stock data. Users can add stocks to their portfolio, view up-to-date stock information, and add comments to their stocks.

> **Note:** This project was developed by following along with a tutorial to help understand the concepts and implementation of the backend using ASP.NET Core and Financial Modeling Prep API.

## Table of Contents
- [Technologies Used](#technologies-used)
- [Features](#features)


## Technologies Used

- **C#** / **ASP.NET Core**: The main framework for building the backend API.
- **Financial Modeling Prep API**: For retrieving real-time stock data.
- **Entity Framework Core**: ORM for interacting with the database.
- **SQL Server**: Database for storing portfolio and stock data.
- **Swagger**: For API documentation.

## Features

- **Portfolio Management**: Users can create a stock portfolio and add/remove stocks.
- **Real-time Stock Data**: Fetch up-to-date information about any stock from the Financial Modeling Prep API.
- **Stock Comments**: Users can add, view, and delete comments on individual stocks in their portfolio.
- **Authentication & Authorization**: Users must authenticate to access and modify their portfolios.
- **RESTful API**: The backend exposes a RESTful API for interacting with the data (add stocks, view portfolio, add comments, etc.).
