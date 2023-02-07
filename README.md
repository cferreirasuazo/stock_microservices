
# Stock Microservices 

![Python](https://badgen.net/badge/Python/3.8/blue?)

![Django](https://img.shields.io/badge/django-%23092E20.svg?style=for-the-badge&logo=django&logoColor=white)

![DjangoREST](https://img.shields.io/badge/DJANGO-REST-ff1709?style=for-the-badge&logo=django&logoColor=white&color=ff1709&labelColor=gray)

![SQLite](https://img.shields.io/badge/sqlite-%2307405e.svg?style=for-the-badge&logo=sqlite&logoColor=white)


## Description

This projected is designed to showcase my knowledge of back-end web technologies, specifically in Python/Django, Rest APIs, and decoupled services (microservices).

The goal was create a simple API using Django and Django Rest Framework to allow users to query [stock quotes](https://www.investopedia.com/terms/s/stockquote.asp).

The project consists of two separate services:
* A user-facing API that will receive requests from registered users asking for quote information.
* An internal stock service that queries external APIs to retrieve the requested quote information.

Both services share the same dependencies (requirements.txt) and can be run from the same virtualenv.

### API service
* Basic user authentication and authorization (login, register)
* Endpoints require authentication (annonymous requests are not allowed)
* When a user makes a request to get a stock quote, if the stock is found, the stock is saved in the database and associated to the user making the request.

* API Response

  `GET /stock?q=aapl.us`
  ```
    {
    "name": "APPLE",
    "symbol": "AAPL.US",
    "open": 123.66,
    "high": 123.66,
    "low": 122.49,
    "close": 123
    }
  ```
* A user can get his history of queries made to the api service by hitting the history endpoint. The endpoint should return the list of entries saved in the database, showing the latest entries first:
  
  `GET /history`
  ```
  [
      {"date": "2021-04-01T19:20:30Z", "name": "APPLE", "symbol": "AAPL.US", "open": "123.66", "high": 123.66, "low": 122.49, "close": "123"},
      {"date": "2021-03-25T11:10:55Z", "name": "APPLE", "symbol": "AAPL.US", "open": "121.10", "high": 123.66, "low": 122, "close": "122"},
      ...
  ]
  ```
* A super user (and only super users) can hit the stats endpoint, which will return the top 5 most requested stocks:

  `GET /stats`
  ```
  [
      {"stock": "aapl.us", "times_requested": 5},
      {"stock": "msft.us", "times_requested": 2},
      ...
  ]
  ```
* All endpoint responses should be in JSON format.

### Stock service

* It's used as an internal service, It doesnt require authentication.
* It requests to stooq.com for get stocks information. The endpoint used is: 
`​https://stooq.com/q/l/?s={stock_code}&f=sd2t2ohlcvn&h&e=csv​`.
* Note that `{stock_code}` above is a parameter that should be replaced with the requested stock code.
* You can see a list of available stock codes here: https://stooq.com/t/?i=518

## User Cases
1. A user makes a request asking for Apple's current Stock quote: `GET /stock?q=aapl.us`
2. The API service calls the stock service to retrieve the requested stock information
3. The stock service delegates the call to the external API, parses the response, and returns the information back to the API service.
4. The API service saves the response from the stock service in the database.
5. The data is formatted and returned to the user.

## How to run the project
* Create a virtualenv: `python -m venv virtualenv` and activate it `. virtualenv/bin/activate`.
* Install dependencies: `pip install -r requirements.txt`
* Start the api service: `cd api_service ; ./manage.py runserver 8001`
* Start the stock service: `cd stock_service ; ./manage.py runserver 8000`
* Start the api service: `cd api_service ; ./manage.py migrate`
* Start the stock service: `cd stock_service ; ./manage.py migrate`


## Ednpoints

Get history of stocks

```
/history 
```

Saves a stock

```
/stock?q=
```

Gets stats of stocks

```
/stats
```

Registers user

```
/signin
```

In order to make requests to the endpoints, must provide username and password


## Deploy 

set Debug = False in both projects

## Run tests

In order to run tests in api, most have stock service running for get stocks

* Run tests in api service  `python manage.py test`
* Run tests in stock service  `python manage.py test`


## Improvements 
* Add unit tests for the bot and the main app.
* Connect the two services via RabbitMQ instead of doing http calls.
* Use JWT instead of basic authentication for endpoints.




