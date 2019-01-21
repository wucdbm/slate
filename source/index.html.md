---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - php

includes:
  - errors

search: true
---

# Introduction

Welcome to the air viva APIv2 documentation.

We have example code in Shell (using cURL) and PHP. You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Authentication

> Authentication can be done with a header or with a GET parameter:

```php
<?php 
$client = new \GuzzleHttp\Client();

# With a query parameter
$response = $client->get('https://air-viva.com/api/v2/api-endpoint-here.json?accessToken=YourAccessToken');

# With a header
$response = $client->get('https://air-viva.com/api/v2/api-endpoint-here.json', [
    \GuzzleHttp\RequestOptions::HEADERS => [
        'Authorization' => 'YourAccessToken'
    ]
]);
```

```shell
# With shell, you can just pass the correct header with each request
curl -X GET "https://air-viva.com/api/v2/api-endpoint-here.json"
  -H "Authorization: YourAccessToken"
  
# Or you could pass your access token as a GET parameter
curl -X GET "https://air-viva.com/api/v2/api-endpoint-here.json?accessToken=YourAccessToken"
```

> Make sure to replace `YourAccessToken` with your token.

APIv2 uses tokens to allow access to the API. You will be provided with a token.

APIv2 expects your access token on each request, with the exception of <a href='#redirect'>Redirect</a>.

You can pass your access token either in an `Authorization: YourAccessToken` header or as a `accessToken=YourAccessToken` GET parameter.

<aside class="notice">
You must replace <code>YourAccessToken</code> with your personal token.
</aside>

# Airports

> Don't forget to <a href='#authentication'>authenticate</a> with your token.

```php
<?php 
$client = new \GuzzleHttp\Client();
$response = $client->get('https://air-viva.com/api/v2/data/airports.json');
```

```shell
curl -X GET "https://air-viva.com/api/v2/data/airports.json"
```

> The above URL returns JSON structured like this:

```json
[
  {
    "id": 1,
    "code": "DAR",
    "city": "Dar Es Salaam",
    "country": "Tanzania"
  },
  {
    "id": 2,
    "code": "ZNZ",
    "city": "Zanzibar",
    "country": "Tanzania"
  }
]
```

<aside class="notice">
If you do not need to support non-IATA airports, you can skip this step
</aside>

APIv2 exposes the full list of airports in air viva's database. 

This is done in order to make it possible to search smaller airports that do not have a IATA code.

The mapping between air viva's airports and the ones in your database is your responsibility.

<aside class="notice">
If you are going to support non-IATA airports, address them by ID as it will never change. 
Non-standard codes (containing numbers) may change.
</aside>

# Routes

## Routes

> Don't forget to <a href='#authentication'>authenticate</a> with your token.

```php
<?php 
$client = new \GuzzleHttp\Client();
$response = $client->get('https://air-viva.com/api/v2/data/routes.json');
```

```shell
curl -X GET "https://air-viva.com/api/v2/data/routes.json"
```

> The above URL returns JSON structured like this:

```json
[
  {
      "departure": {
          "id": 1,
          "code": "DAR",
          "city": "Dar Es Salaam",
          "country": "Tanzania"
      },
      "destination": {
          "id": 2,
          "code": "ZNZ",
          "city": "Zanzibar",
          "country": "Tanzania"
      }
  },
  {
      "departure": {
          "id": 2,
          "code": "ZNZ",
          "city": "Zanzibar",
          "country": "Tanzania"
      },
      "destination": {
          "id": 1,
          "code": "DAR",
          "city": "Dar Es Salaam",
          "country": "Tanzania"
      }
  }
]
```

We also provide a list of active routes. This list is updated every 15 minutes.

You can sync as often as you'd like, but a good idea is to do so hourly. 

This will reduce server load on both ends.

## Routes with Availability

> Don't forget to <a href='#authentication'>authenticate</a> with your token.

```php
<?php 
$client = new \GuzzleHttp\Client();
$response = $client->get('https://air-viva.com/api/v2/data/routes/with-availability.json');
```

```shell
curl -X GET "https://air-viva.com/api/v2/data/routes/with-availability.json"
```

> The above URL returns JSON structured like this:

```json
[
  {
      "dep": "DAR",
      "des": "ZNZ",
      "dow": "Mon,Tue,Wed,Thu,Fri,Sat,Sun",
      "from": "2018-01-01",
      "to": "2020-07-09"
  },
  {
      "dep": "ZNZ",
      "des": "DAR",
      "dow": "Mon,Wed,Thu,Fri,Sun",
      "from": "2018-02-01",
      "to": "2020-09-09"
  }
]
```

Routes with availability provides availability data for each route.

In addition to the route `dep` departure code `des` destination code, this will also provide the following data:

 - `from` - `to` flight date range
 - `dow` CSV days of the week

on which flights *should* (but not guaranteed) be available.

# Search

> Don't forget to <a href='#authentication'>authenticate</a> with your token.

```php
<?php 
$client = new \GuzzleHttp\Client();
$response = $client->get('https://air-viva.com/api/v2/flights.json?from=DAR&to=2&departureDate=2018-11-08');
```

```shell
curl -X GET "https://air-viva.com/api/v2/flights.json?from=DAR&to=2&departureDate=2018-11-08"
```

> The above URL returns JSON structured like this:

```json
{
    "departure": [
        {
            "from": {
                "id": 1,
                "code": "DAR",
                "city": "Dar Es Salaam",
                "country": "Tanzania"
            },
            "to": {
                "id": 2,
                "code": "ZNZ",
                "city": "Zanzibar",
                "country": "Tanzania"
            },
            "legs": [
                {
                    "from": {
                        "id": 1,
                        "code": "DAR",
                        "city": "Dar Es Salaam",
                        "country": "Tanzania"
                    },
                    "to": {
                        "id": 3,
                        "code": "ARK",
                        "city": "Arusha",
                        "country": "Tanzania"
                    },
                    "number": "5031",
                    "dates": {
                        "departure": "2019-01-28 12:00",
                        "arrival": "2019-01-28 12:45"
                    },
                    "duration": 2700,
                    "airline": {
                        "id": 1,
                        "name": "Test Airline",
                        "code": "TE"
                    }
                },
                {
                    "from": {
                        "id": 3,
                        "code": "ARK",
                        "city": "Arusha",
                        "country": "Tanzania"
                    },
                    "to": {
                        "id": 4,
                        "code": "JRO",
                        "city": "Kilimanjaro",
                        "country": "Tanzania"
                    },
                    "number": "5032",
                    "dates": {
                        "departure": "2019-01-28 13:00",
                        "arrival": "2019-01-28 13:45"
                    },
                    "duration": 2700,
                    "airline": {
                        "id": 1,
                        "name": "Test Airline",
                        "code": "TE"
                    }
                },
                {
                    "from": {
                        "id": 4,
                        "code": "JRO",
                        "city": "Kilimanjaro",
                        "country": "Tanzania"
                    },
                    "to": {
                        "id": 2,
                        "code": "ZNZ",
                        "city": "Zanzibar",
                        "country": "Tanzania"
                    },
                    "number": "5032",
                    "dates": {
                        "departure": "2019-01-28 14:00",
                        "arrival": "2019-01-28 14:45"
                    },
                    "duration": 2700,
                    "airline": {
                        "id": 1,
                        "name": "Test Airline",
                        "code": "TE"
                    }
                }
            ],
            "dates": {
                "departure": "2019-01-28 12:00",
                "arrival": "2019-01-28 14:45"
            },
            "duration": 2700,
            "airline": {
                "id": 1,
                "name": "Test Airline",
                "code": "TE"
            },
            "price": {
                "currency": "USD",
                "adult": "12.50",
                "child": "11.50",
                "infant": "1.25"
            },
            "priceUsd": {
                "adult": "12.50",
                "child": "11.50",
                "infant": "1.25"
            },
            "requiredFields": {
                "phone": false,
                "nationality": false,
                "documentType": false,
                "documentNumber": false,
                "documentExpiry": false,
                "documentIssuer": false,
                "documentIssueDate": false,
                "birthDate": false,
                "birthDateForChildren": false,
                "birthDateForInfants": false,
                "placeOfBirth": false
            },
            "id": 85
        }
    ],
    "return": [
      
    ]
}
```

Search parameters are passed as query parameters

### Query Parameters

Parameter | Type | Description
--------- | ---- | ----
from          | Required | Airport `id` or `code`
to            | Required | Airport `id` or `code`
departureDate | Required | Departure date in YYYY-MM-DD format with leading zeros
returnDate    | Optional | Return date in YYYY-MM-DD format with leading zeros. If provided, a return search will be performed, otherwise one-way. <br/> Please note that if you're performing a one-way search, the `return` key in the JSON response will be omitted!
adults        | Required | Adults traveling. Between 1 and 12. Default: 1
children      | Required | Children traveling. Between 0 and 12. Default: 0
infants       | Required | Infants traveling. Between 0 and 12. Default: 0

### Rules

Search parameters should adhere to the following rules:

* `from` airport is required.
* `to` airport is required. Should not match `from` airport.
* You'll get an error upon submission of a non-existent airport @ `from`, `to`. Feel free to pass any IATA code here if you don't want to sync airports and/or routes and go with IDs, but you should also handle the airport not found errors as something normal and assume no availability if air viva does not have these airports.
* Search will always execute regardless of <a href='#routes-with-availability'>Routes with Availability</a> - you'll simply get an empty result.
* `departureDate` is required and should be a valid date. Searches are not allowed for past dates.
* `returnDate` if doing a return search should be a valid date and should not be before `departureDate`. It can be equal to `departureDate` though, as same-day return is often a valid use-case.
* `adults` Should be numeric. There should be at least one adult traveling. Infants cannot be more than adults.
* `children` Should be numeric.
* `infants` Should be numeric.

<aside class="notice">
The adults, children and infants parameters will still work even if you provide malformed values such as random, non-numeric strings, but they'll have their default values.
This also applies to <a href='#redirect'>Redirect</a>
</aside>

<aside class="notice">
Departure / arrival dates are local.
</aside>

<aside class="notice">
Prices are floats, but are exposed as strings in the JSON to preserve precision to the second decimal.
</aside>

<aside class="notice">
Flight numbers under each leg are strings to prevent JSON serializers mangling flight numbers starting with a zero.
</aside>

<aside class="warning">
If <code>returnDate</code> is not provided, then a one-way search will be performed, the <code>return</code> key in the JSON response will be omitted.
</aside>

# Redirect

Redirection is public and does not require authentication.

To redirect a customer to air viva's booking page, you should use the following url: `https://air-viva.com/api/v2/redirect`

Parameter | Type | Description
--------- | ---- | ----
flightId       | Required | A Flight `id` from <a href='#search'>Search</a>
returnFlightId | Optional | A Flight `id` from <a href='#search'>Search</a>
affiliate      | Required | Your Affiliate ID. This will be provided together with your access token.
adults         | Required | Adults traveling. Between 1 and 12. Default: 1
children       | Required | Children traveling. Between 0 and 12. Default: 0
infants        | Required | Infants traveling. Between 0 and 12. Default: 0
locale         | Optional | Locale to redirect to. Will fallback to the default `en` one if unsupported locale is provided.
subAffiliate   | Optional | String, up to 255 characters. You save a reference of a partner of yours on each sale and can later be pulled via the API.
campaign       | Optional | String, up to 255 characters. You save your campaign/website (if you have multiple, for example a separate domain for each language) on each sale and can later be pulled via the API.
partnerClickId | Optional | String, up to 255 characters. You can save your redirection ID and map your data when pulling sales via the API.

<aside class="notice">
Please note that even if some parameters are required, redirection will still work even if you fail to provide a required parameter. Examples below.

</aside>

For example: 

* If you fail to provide `flightId` AND `returnFlightId`, the user will be taken to air viva's homepage.
* If you fail to provide the required `flightId`, but provide `returnFlightId`, the user will be taken to the search results page with the return flight pre-selected.
* If you fail to provide passenger counts, defaults will be assumed and the user will still be taken to the search results page.
