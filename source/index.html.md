---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
- shell: cURL
- javascript--nodejs: Node
- ruby: Ruby
- python: Python
- java: Java
- go: Go

toc_footers:
- <a href="mailto:tech@citizenplane.com" target="_blank">Contact CitizenPlane</a>
- CitizenPlane 2017-2018
- All rights reserved

includes:
- errors

search: true
---

# Introduction

<a href="https://citizenplane.com/" target="_blank">CitizenPlane</a> is a software company that connects travel agencies and metasearches to air content providers. Our REST API allows ETAs to search within our stock of open flights, create bookings and process payments.

The default API endpoint is <a href="https://booking-api.citizenplane.com" target="_blank"> booking-api.citizenplane.com</a>. JSON is returned by all API responses, including errors.

To make the API as explorable as possible, accounts have test and live API tokens. To get your token, contact us at <a href="mailto:tech@citizenplane.com" target="_blank">tech@citizenplane.com</a>.

If you wish to test the different endpoints detailed below, we have a dedicated page allowing you to perform requests with your parameters. An API token will be required for each call. To test our endpoints, <a href="https://booking-api.citizenplane.com/documentation" target="_blank">click here</a>.

<aside class ="notice">Current API version is 1.1. Therefore, every endpoint has /v1 appended to its url.</aside>

# Authentication


```shell
# Example request

curl "https://booking-api.citizenplane.com/v1/flights" \
   -H "Authorization: Bearer {your_api_token}"

# Make sure to replace {your_api_token} with the token you were given.
```

Authentication to the API is performed via **Bearer Tokens**. Tokens are unique and non-expirable. Make sure not to share your token in publicly accessible area such GitHub or client-side code.

Every request to CitizenPlane API must include a Bearer token in the following format: `Authorization: Bearer {your_api_token}`

# Endpoints

## Retrieve a list of flights

> Example request

```shell
curl "https://booking-api.citizenplane.com/v1/flights"
  -H "Accept: application/json" \
  -H "Authorization: Bearer {your_api_token}"
```

```javascript--nodejs
const rp = require("request-promise")

const getFlights = async () => {
  try {
    const flights = await rp({
      method: "GET",
      uri: "https://booking-api.citizenplane.com/v1/flights",
      headers: {
        Authorization: "Bearer {your_api_token}"
      }
    })
    return flights
  } catch (err) {
    console.log(err)
 }
}
```

```ruby
require 'rest-client'

response = RestClient.get 'https://booking-api.citizenplane.com/v1/flights', {:Authorization => 'Bearer {your_api_token}'}
```

```python
import requests

r = requests.get('https://booking-api.citizenplane.com/v1/flights', headers={ 'Authorization': 'Bearer {your_api_token}' })
```

```go
import (
        "fmt"
        "io/ioutil"
        "net/http"
)

client := &http.Client{}
        req, _ := http.NewRequest("GET", "https://booking-api.citizenplane.com/v1/flights", nil)
        req.Header.Add("Accept", "application/json")
        req.Header.Add("Authorization", "{your_api_token}")

        resp, _ := client.Do(req)

        defer resp.Body.Close()
        body, _ := ioutil.ReadAll(resp.Body)
        fmt.Println(string(body))
```

> Example response

```json
{
  "results": [
    {
      "id": 20222,
      "flight_number": "5O7892",
      "origin": "CDG",
      "destination": "PMI",
      "available_seats": 20,
      "organization_name": "Demo",
      "departure_date": "2018-11-29 10:00",
      "arrival_date": "2018-11-29 16:00",
      "airline": {
          "name": "CitizenPlane",
          "operated_by": {
            "name": "Transavia",
            "iata_code": "HV"
          }
      },
      "cabin_class": "economy",
      "booking_class": "Y",
      "price": 278.46,
      "infant_price": 76.5,
      "luggage_options": [
        {
          "weight": 20,
          "quantity": 1,
          "price": 25.5
        }
      ]
      "included_airport_tax": 33.66
      "cc_fee": 0.02
    },
    {...},
    {...}
  ],
  "total": 255,
  "page": 0,
  "total_pages": 3
}
```

This endpoint retrieves a list of flights in CitizenPlane's stock. Flights returned by the API are identified by a unique ID that will be needed for the `/requests` endpoint.

### HTTP request

`GET https://booking-api.citizenplane.com/v1/flights`

<aside class="success">To test this endpoint, <a href="https://booking-api.citizenplane.com/documentation#/flights" target="_blank">click here</a>.</aside>

### URL parameters


Parameter | Type | Status | Description
--------- | ---- | ------ | -----------
origin | *string* | *optional* | A string of uppercase, comma-separated iata codes containing a list of departure airports (up to a maximum of 10 airports).
nearbyO | *string* | *optional* | If set to `true`, nearby departure airports (up to 100km) will be considered in the flight search. Defaults to `false`.
destination | *string* | *optional* | A string of uppercase, comma-separated iata codes containing a list of arrival airports (up to a maximum of 10 airports).
nearbyD | *string* | *optional* | If set to `true`, nearby arrival airports (up to 100km) will be considered in the flight search. Defaults to `false`.
start | *date* | *optional* | Date from which the flight search will be operated. Date has to be formatted following this model: `YYYY-MM-DD`.
end | *date* | *optional* | Date until which the flight search will be operated. Date has to be formatted following this model: `YYYY-MM-DD`.
seats | *integer* | *optional* | Maximum number of seats to search for.
page | *integer* | *optional* | Specify the page to retrieve. Each page displays up to 100 results. Defaults to 0 (page numbers are zero-based).

## Create a request

> Example request

```shell
curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --header 'authorization: {your_api_token}' -d '{
  "flight_id": 20222,
  "customer_code": "opodo",
  "passengers": {
    "adults": 2,
    "children": 0,
    "infants": 0
  }
}' 'https://booking-api.citizenplane.com/v1/requests'
```

```javascript--nodejs
const rp = require("request-promise")

const createRequest = async () => {
  try {
    const request = await rp({
      method: "POST",
      uri: "https://booking-api.citizenplane.com/v1/requests",
      headers: {
      Authorization: "Bearer {your_api_token}"
      },
      json: true,
      body: {
        "flight_id": 20222,
        "customer_code": "opodo",
        "passengers": {
          "adults": 2,
          "children": 0,
          "infants": 0
        }
      }
    })
    return request
  } catch (err) {
    console.log(err)
  }
}
```

```ruby
require 'rest-client'
require 'json'

payload = {
  :flight_id => 20222,
  :customer_code => 'opodo',
  :passengers => {
    :adults => 2,
    :children => 0,
    :infants => 0
  }
}

response = RestClient.post 'https://booking-api.citizenplane.com/v1/requests', payload.to_json, {content_type: :json, :Authorization => 'Bearer {your_api_token}'}
```

```python
import requests

r = requests.post('https://booking-api.citizenplane.com/v1/requests', headers={
    'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjEiLCJzYW5kYm94IjoiZmFsc2UiLCJpYXQiOjE1Mzg3NTg3NzZ9.78oRovu8MJxzigSPtk8gLeJ2QhF07VHvD0Xo1t9YqKI'
    }, json={
        "flight_id": 20222,
        "customer_code": "opodo",
        "passengers": {
            "adults": 2,
            "children": 0,
            "infants": 0
        }
    }
)
```

```go
import (
        "bytes"
        "fmt"
        "io/ioutil"
        "net/http"
)

payload := []byte(`{ "flight_id": 20222, "customer_code": "opodo", "passengers": { "adults": 2, "children": 0, "infants": 0 } }`)
        req, _ = http.NewRequest(
                "POST",
                "https://booking-api.citizenplane.com/v1/requests",
                bytes.NewReader(payload),
        )
        req.Header.Add("Content-Type", "application/json")
        req.Header.Add("Accept", "application/json")
        req.Header.Add("Authorization", "{your_api_token}")

        resp, _ = client.Do(req)
        defer resp.Body.Close()
        body, _ = ioutil.ReadAll(resp.Body)
        fmt.Println(string(body))
```


> Example response

```json
{
    "id": 19,
    "passengers": {
        "adults": 2,
        "children": 0,
        "infants": 0
    },
    "price": 278.46,
    "infant_price": null,
    "cc_fee": 0.02,
    "customer_code": "opodo",
    "created_at": "2018-10-16 15:02",
    "flight": {
        "id": 20222,
        "flight_number": "5O7892"
        "origin": {
            "iata_code": "CDG",
            "city_name": "Paris",
            "country_name": "France",
            "timezone": "Europe/Paris"
        },
        "destination": {
            "iata_code": "PMI",
            "city_name": "Palma de Majorque",
            "country_name": "Espagne",
            "timezone": "Europe/Madrid"
        },
        "organization": {
          "name": "Demo"
        },
        "departure_date": "2018-10-29 10:00",
        "arrival_date": "2018-10-29 16:00",
        "airline": {
            "name": "CitizenPlane",
            "operated_by": {
              "name": "Transavia",
              "iata_code": "HV"
            }
        },
        "cabin_class": "economy",
        "booking_class": "Y",
        "luggage_options": [
          {
            "weight": 20,
            "quantity": 1,
            "price": 0
          }
        ],
        "included_airport_tax": 33.66
    },
    "customer": {
      "name": "Opodo",
      "email": "support@odopo.com",
      "is_company": true,
      "company_codes": [
        "opodo"
      ],
      "verified": true,
      "phonenumber": "+33170917575",
      "gender": null,
      "address": "Waterfront, Hammersmith Embankment, Chancellor's Road",
      "city_name": "London",
      "postal_code": "W69RU",
      "coutry_code": "UK",
      "country_name": "United Kingdom",
      "vat_number": null,
      "registration_number": null,
      "created_at": "2019-02-01T14:52:45.974Z"
      "updated_at": "2019-02-15T:52:45.974Z"
    }
}
```

This endpoint creates a booking request on a flight (identified by its id). A booking request holds the number of seats set in the request payload for **fifteen minutes**. Seat and infant prices won't be subject to any changes during this time period.

<aside class ="notice">Should the booking not be confirmed, the request would be canceled and the seats put back in sales.</aside>

### HTTP request

`POST https://booking-api.citizenplane.com/v1/requests`

<aside class="success">To test this endpoint, <a href="https://booking-api.citizenplane.com/documentation#/requests" target="_blank">click here</a>.</aside>

### URL parameters


Parameter | Type | Status | Description
--------- | ---- | ------ | -----------
flight_id | *integer* | **required** | The flight id (see `/flights`).
customer_code | *string* | *optional* | An string used to identify the customer making this reservation. Make sure your customer code is recognized in our system before making a request. If you're making a request with a customer that does not have an identification code, do not add this field.
passengers | *object* | **required** | An object containing the passenger count breakdown by age for this booking request. <a href="#request-passengers-info">See child arguments</a>.

<br/>
<br/>

#### <table id="request-passengers-info">
  <caption> Passengers child arguments</caption>
  <tbody>
    <tr>
      <th align="left">Parameter</th>
      <th align="left">Type</th>
      <th align="left">Status</th>
      <th align="left">Description</th>
    </tr>
    <tr>
      <td align="left">adults</td>
      <td align="left"><i>integer</i></td>
      <td align="left">*optional*</td>
      <td align="left">Number of adults to book on this flight.</td>
    </tr>
    <tr>
      <td align="left">children</td>
      <td align="left"><i>integer</i></td>
      <td align="left">*optional*</td>
      <td align="left">Number of children to book on this flight.</td>
    </tr>
    <tr>
      <td align="left">infants</td>
      <td align="left"><i>integer</i></td>
      <td align="left">*optional*</td>
      <td align="left">Number of infants (2 < yo) to book on this flight. Cannot be higher than the sum of adults and children passengers.</td>
    </tr>
  </tbody>
</table>

## Confirm a request and create a booking

> Card token generation

```shell
curl https://api.stripe.com/v1/tokens \
   -u rk_test_wZGrX11MrIkESU5ja9mpOoL9: \
   -d card[number]=4242424242424242 \
   -d card[exp_month]=12 \
   -d card[exp_year]=2019 \
   -d card[cvc]=123
```

```ruby
require "stripe"
Stripe.api_key = "rk_test_wZGrX11MrIkESU5ja9mpOoL9"

Stripe::Token.create(
  :card => {
    :number => "4242424242424242",
    :exp_month => 11,
    :exp_year => 2019,
    :cvc => "314"
  },
)
```

```python
require "stripe"
Stripe.api_key = "rk_test_wZGrX11MrIkESU5ja9mpOoL9"

Stripe::Token.create(
  :card => {
    :number => "4242424242424242",
    :exp_month => 11,
    :exp_year => 2019,
    :cvc => "314"
  },
)
```

```java
Stripe.apiKey = "rk_test_wZGrX11MrIkESU5ja9mpOoL9";

Map<String, Object> tokenParams = new HashMap<String, Object>();
Map<String, Object> cardParams = new HashMap<String, Object>();
cardParams.put("number", "4242424242424242");
cardParams.put("exp_month", 11);
cardParams.put("exp_year", 2019);
cardParams.put("cvc", "314");
tokenParams.put("card", cardParams);

Token.create(tokenParams);
```

```javascript--nodejs
const stripe = require("stripe")("rk_test_wZGrX11MrIkESU5ja9mpOoL9");

stripe.tokens.create({
  card: {
    "number": '4242424242424242',
    "exp_month": 12,
    "exp_year": 2019,
    "cvc": '123'
  }
}, function(err, token) {
  // asynchronously called
});
```

```go
stripe.Key = "rk_test_wZGrX11MrIkESU5ja9mpOoL9"

params := &stripe.TokenParams{
Card: &stripe.CardParams{
    Number: stripe.String("4242424242424242"),
        ExpMonth: stripe.String("12"),
            ExpYear: stripe.String("2019"),
                CVC: stripe.String("123"),
      },
}
t, err := token.New(params)
```

> Example Stripe response

```json
{
  "id": "tok_1DeKRxCLoBt04Rh8rYEA7bJz",
  "object": "token",
  "card":
   { "id": "card_1DeKRxCLoBt04Rh8ladNLIQn",
     "object": "card",
     "address_city": null,
     "address_country": null,
     "address_line1": null,
     "address_line1_check": null,
     "address_line2": null,
     "address_state": null,
     "address_zip": null,
     "address_zip_check": null,
     "brand": "Visa",
     "country": "US",
     "cvc_check": "unchecked",
     "dynamic_last4": null,
     "exp_month": 12,
     "exp_year": 2019,
     "fingerprint": "UQli8ZQEJ3So5j7d",
     "funding": "credit",
     "last4": "4242",
     "metadata": {},
     "name": "John Doe",
     "tokenization_method": null },
  "client_ip": "00.000.000.000",
  "created": 1544093481,
  "livemode": false,
  "type": "card",
  "used": false
}
```

> Booking request example

```shell
curl POST --header 'Content-Type: application/json' --header 'Accept: application/json' --header 'authorization: {your_api_token}' -d '{
  "request_id": 19,
  "customer_code": "opodo",
  "external_id": "ABC123",
  "first_name": "John",
  "last_name": "Doe",
  "gender": "male",
  "email": "john.doe@example.com",
  "phonenumber": "+33600000000",
  "passenger_count": 2,
  "infant_count": 0,
  "passengers": [
    {
      "first_name": "John",
      "last_name": "Doe",
      "gender": "male",
      "is_infant": false,
      "date_of_birth": "1987-06-21"
    },
    {
      "first_name": "Jane",
      "last_name": "Doe",
      "gender": "female",
      "is_infant": false,
      "date_of_birth": "1995-11-01"
    }
  ],
  "luggage": [
    {
      "weight": 20,
      "quantity": 1,
      "price": 0
    },
    {
      "weight": 20,
      "quantity": 1,
      "price": 0
    },
  ],
  "card_token": "tok_1CXrzBCLoBt04Rh8XWrZH34S"
}' 'https://booking-api.citizenplane.com/v1/bookings'
```

```javascript--nodejs
const rp = require("request-promise")

const createBooking = async () => {
  try {
    const booking = await rp({
      method: "POST",
      uri: `https://booking-api.citizenplane.com/v1/bookings`,
      headers: {
        Authorization: "Bearer {your_api_token}"
      },
      json: true,
      body: {
        "request_id": 19,
        "customer_code": "opodo",
        "external_id": "ABC123",
        "first_name": "John",
        "last_name": "Doe",
        "gender": "male",
        "email": "john.doe@example.com",
        "phonenumber": "+33600000000",
        "passenger_count": 2,
        "infant_count": 0,
        "passengers": [
          {
            "first_name": "John",
            "last_name": "Doe",
            "gender": "male",
            "is_infant": false,
            "date_of_birth": "1987-06-21"
          },
          {
            "first_name": "Jane",
            "last_name": "Doe",
            "gender": "female",
            "is_infant": false,
            "date_of_birth": "1995-11-01"
          }
        ],
        "luggage": [
          {
            "weight": 20,
            "quantity": 1,
            "price": 0
          },
          {
            "weight": 20,
            "quantity": 1,
            "price": 0
          },
        ],
        "card_token": "tok_1CXrzBCLoBt04Rh8XWrZH34S"
      }
    })
    return booking
  } catch (err) {
    console.log(err)
  }
}
```

```ruby
require 'rest-client'
require 'json'

payload = {
  :request_id => 19,
  :customer_code => "opodo",
  :external_id => "ABC123",
  :first_name => "John",
  :last_name => "Doe",
  :gender => "male",
  :email => "john.doe@example.com",
  :phonenumber => "+33600000000",
  :passenger_count => 2,
  :infant_count => 0,
  :passengers => [
    {
      :first_name => "John",
      :last_name => "Doe",
      :gender => "male",
      :is_infant => false,
      :date_of_birth => "1987-06-21"
    },
    {
      :first_name => "Jane",
      :last_name => "Doe",
      :gender => "female",
      :is_infant => false,
      :date_of_birth => "1995-11-01"
    }
  ],
  :luggage => [
    {
      :weight => 20,
      :quantity => 1,
      :price => 0
    },
    {
      :weight => 20,
      :quantity => 1,
      :price => 0
    }
  ],
  :card_token => "tok_1CXrzBCLoBt04Rh8XWrZH34S"
}

response = RestClient.post 'https://booking-api.citizenplane.com/v1/bookings', payload.to_json, {content_type: :json, :Authorization => 'Bearer {your_api_token}'}
```

```python
import requests

r = requests.post('https://booking-api.citizenplane.com/v1/requests', headers={
    'Authorization': 'Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjEiLCJzYW5kYm94IjoiZmFsc2UiLCJpYXQiOjE1Mzg3NTg3NzZ9.78oRovu8MJxzigSPtk8gLeJ2QhF07VHvD0Xo1t9YqKI'
    }, json={
      "request_id": 19,
      "customer_code": "opodo",
      "external_id": "ABC123",
      "first_name": "John",
      "last_name": "Doe",
      "gender": "male",
      "email": "john.doe@example.com",
      "phonenumber": "+33600000000",
      "passenger_count": 2,
      "infant_count": 0,
      "passengers": [
        {
          "first_name": "John",
          "last_name": "Doe",
          "gender": "male",
          "is_infant": False,
          "date_of_birth": "1987-06-21"
        },
        {
          "first_name": "Jane",
          "last_name": "Doe",
          "gender": "female",
          "is_infant": False,
          "date_of_birth": "1995-11-01"
        }
      ],
      "luggage": [
        {
          "weight": 20,
          "quantity": 1,
          "price": 0
        },
        {
          "weight": 20,
          "quantity": 1,
          "price": 0
        }
      ],
      "card_token": "tok_1CXrzBCLoBt04Rh8XWrZH34S"
    }
)
```

```go
import (
        "bytes"
        "fmt"
        "io/ioutil"
        "net/http"
)

payload = []byte(`{
            "request_id": 19,
            "customer_code": "opodo",
            "external_id": "123ABC",
            "first_name": "John",
            "last_name": "Doe",
            "gender": "male",
            "email": "john.doe@example.com",
            "phonenumber": "+33600000000",
            "passenger_count": 2,
            "infant_count": 0,
            "passengers": [
              {
                "first_name": "John",
                "last_name": "Doe",
                "gender": "male",
                "is_infant": false,
                "date_of_birth": "1987-06-21"
              },
              {
                "first_name": "Jane",
                "last_name": "Doe",
                "gender": "female",
                "is_infant": false,
                "date_of_birth": "1995-11-01"
              }
            ],
            "luggage": [
              {
                "weight": 20,
                "quantity": 1,
                "price": 0
              },
              {
                "weight": 20,
                "quantity": 1,
                "price": 0
              }
            ],
            "card_token": "tok_1CXrzBCLoBt04Rh8XWrZH34S"
          }`)

        req, _ = http.NewRequest(
                "POST",
                "https://booking-api.citizenplane.com/v1/bookings",
                bytes.NewReader(payload),
        )
        req.Header.Add("Content-Type", "application/json")
        req.Header.Add("Accept", "application/json")
        req.Header.Add("Authorization", "{your_api_token}")

        resp, _ = client.Do(req)
        defer resp.Body.Close()
        body, _ = ioutil.ReadAll(resp.Body)
        fmt.Println(string(body))
```


> Example response

```json
{
  "booking": {
    "id": "8765",
    "flight_id": 20222,
    "request_id": 19,
    "pnr_reference": "K9IR6XLGQ",
    "first_name": "John",
    "last_name": "Doe",
    "email": "john.doe@example.com",
    "phonenumber": "+33600000000",
    "gender": "male",
    "passenger_count": 2,
    "infant_count": 0,
    "passengers": [
      {
        "first_name": "John",
        "last_name": "Doe",
        "gender": "male",
        "is_infant": false,
        "date_of_birth": "1987-06-21"
      },
      {
        "first_name": "Jane",
        "last_name": "Doe",
        "gender": "female",
        "is_infant": false,
        "date_of_birth": "1995-11-01"
      }
    ],
    "luggage": [
      {
        "weight": 20,
        "quantity": 1,
        "price": 0
      },
      {
        "weight": 20,
        "quantity": 1,
        "price": 0
      },
    ],
    "price": 278.46,
    "infant_price": null,
    "cc_fee": 0.02,
    "luggage_fee": 0,
    "total_price": 556.92,
    "external_id": "ABC123",
    "created_at": "2018-10-16 15:02"
  },
  "flight": {
    "id": 20222,
    "origin": {
      "iata_code": "CDG",
      "city_name": "Paris",
      "country_name": "France",
      "timezone": "Europe/Paris"
    },
    "destination": {
      "iata_code": "PMI",
      "city_name": "Palma de Majorque",
      "country_name": "Espagne",
      "timezone": "Europe/Madrid"
    },
    "departure_date": "2018-11-29 09:00",
    "arrival_date": "2018-11-29 15:00",
    "airline": {
      "name": "CitizenPlane",
      "operated_by": {
        "name": "Transavia",
        "iata_code": "HV"
      }
    }
    "organization": {
      "name": "Demo"
    },
    "cabin_class": "economy",
    "booking_class": "Y",
    "luggage_options": [
      {
        "weight": 20,
        "quantity": 1,
        "price": 0
      }
    ],
    "included_airport_tax": 33.66,
  },
    "customer": {
      "name": "Opodo",
      "email": "support@odopo.com",
      "is_company": true,
      "company_codes": [
        "opodo"
      ],
      "verified": true,
      "phonenumber": "+33170917575",
      "gender": null,
      "address": "Waterfront, Hammersmith Embankment, Chancellor's Road",
      "city_name": "London",
      "postal_code": "W69RU",
      "coutry_code": "UK",
      "country_name": "United Kingdom",
      "vat_number": null,
      "registration_number": null,
      "created_at": "2019-02-01T14:52:45.974Z"
      "updated_at": "2019-02-15T:52:45.974Z"
    }
}
```

This endpoint confirms the previously created booking request and processes the booking as well as the associated payment. Once the booking is created, the customer will receive a confirmation by email along with the ticket(s).

<aside class="notice">If the payment fails for any reason, the request associated to this booking will be considered as aborted and the process is to be started again.</aside>

### Generating a card token

CitizenPlane's API uses (<a href="https://stripe.com">Stripe</a>) to process payments. In order to be PCI-compliant, CitizenPlane cannot directly process the customer's credit card data. Before sending your booking requests, you need to generate a credit card token by sending the card data to Stripe and then send the token alongside booking data to CitizenPlane's API.
To generate a token, you'll need to install Stripe's package and uses an api_key you will be given by CitizenPlane. Follow the examples on the right to generate a card token using Stripe's API.

### HTTP request

`POST https://booking-api.citizenplane.com/v1/bookings`

<aside class="success">To test this endpoint, <a href="https://booking-api.citizenplane.com/documentation#/bookings" target="_blank">click here</a>.</aside>

### URL parameters


Parameter | Type | Status | Description
--------- | ---- | ------ | -----------
request_id | *integer* | **required** | The request id (see `/requests`).
external_id | *string* | *optional* | The id used on your side to identify the booking. This field is optional, though highly recommended in case we need to retrieve a booking in your system.
customer_code | *string* | *optional* | A string used to identify the customer making this reservation. Make sure your customer code is recognized in our system before making a request. Information saved in our system will be used for invoicing. If you're making a request with a customer that does not have an identification code, do not add this field but refer to *customer_data*.
customer_data | *object* | *optional* | If the customer is unrecognized in our database, use this field to send information relative to the customer. This information will be used for invoicing. This field is required if *customer_code* is left empty.
first_name | *string* | **required** | The customer's first name.
last_name | *string* | **required** | The customer's last name.
gender | *string* | **required** | The customer's gender.
email | *string* | **required** | The customer's email.
phonenumber | *string* | **required** | The customer's phone number.
address | *string* | *optional* | The customer's address.
city_name | *string* | *optional* | The customer's city name.
country_code | *string* | *optional* | The customer's country code.
postal_code | *string* | *optional* | The customer's postal code.
passenger_count | *string* | **required** | Total passengers on this booking (infants excluded).
infant_count | *string* | **required** | Total infants on this booking (< 2yo).
passengers | *array* | **required** | An array containing personal information for each passenger on this booking. <a href="#booking-passengers-info">See child arguments</a>.
luggage | *array* | **required** | An array containing the luggage option chosen by each passenger. Each passenger is allowed to choose one option. Therefore, the array must not contain more options than passengers. If two passengers choose the same luggage weight option, this option must appear twice in the luggage array (though the luggage quantity may vary).<a href="#booking-luggage-info">See child arguments</a>.
card_token | *string* | **required** | A token obtained by sending credit card information to Stripe's <a href="https://stripe.com/docs/api/tokens/create_card?lang=curl">API</a>.

<br/>
<br/>

#### <table id="booking-passengers-info">
  <caption><b>passengers child arguments</b></caption>
  <tbody>
    <tr>
      <th align="left">Parameter</th>
      <th align="left">Type</th>
      <th align="left">Status</th>
      <th align="left">Description</th>
    </tr>
    <tr>
      <td align="left">first_name</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">Passenger's first name.</td>
    </tr>
    <tr>
      <td align="left">last_name</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">Passenger's last name.</td>
    </tr>
    <tr>
      <td align="left">gender</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">Passenger's gender.</td>
    </tr>
    <tr>
      <td align="left">is_infant</td>
      <td align="left"><i>boolean</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">If the passenger is an infant (< 2 yo), set is_infant to true.</td>
    </tr>
    <tr>
      <td align="left">date_of_birth</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">Passenger's date of birth.</td>
    </tr>
    <tr>
      <td align="left">birth_country</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>*optional*</b></td>
      <td align="left">Passenger's birth country.</td>
    </tr>
    <tr>
      <td align="left">passport_number</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>*optional*</b></td>
      <td align="left">Passenger's passport number.</td>
    </tr>
    <tr>
      <td align="left">passport_expiry_date</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>*optional*</b></td>
      <td align="left">Passenger's passport expiry date.</td>
    </tr>
    <tr>
      <td align="left">passport_country_of_issue</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>*optional*</b></td>
      <td align="left">Passenger's passport country of issue.</td>
    </tr>
    <tr>
      <td align="left">nationality</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>*optional*</b></td>
      <td align="left">Passenger's nationality.</td>
    </tr>
  </tbody>
</table>

<br/>
<br/>

#### <table id="booking-luggage-info">
  <caption><b>luggage child arguments</b></caption>
  <tbody>
    <tr>
      <th align="left">Parameter</th>
      <th align="left">Type</th>
      <th align="left">Status</th>
      <th align="left">Description</th>
    </tr>
    <tr>
      <td align="left">weight</td>
      <td align="left"><i>integer</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">Lugagge weight chosen by the passenger. Must correspond to the available option on the flight.</td>
    </tr>
    <tr>
      <td align="left">quantity</td>
      <td align="left"><i>integer</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">Number of luggage chosen by the passenger for this given weight option.</td>
    </tr>
    <tr>
      <td align="left">price</td>
      <td align="left"><i>integer</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">Price of the luggage option chosen by the passenger.</td>
    </tr>
  </tbody>
</table>


# Testing

CitizenPlane's API has a sandbox mode allowing you to safely test your integration with us. In order to do so, you will need a test token, which will give you access to our demo flights. You will be able to test our different endpoints without actually booking any seats or processing any payments.

<aside class ="notice">Please note that we only have a few demo flights for you to use. We advise you to first make a GET request to /flights without any query parameters except pagination to get the full list of demo flights. You'll then know wich query parameters to test.</aside>

To test the payment processing method, use any of the testing cards provided by Stripe: <a href="https://stripe.com/docs/testing#cards" target="_blank">Stripe testing cards</a>.

Once you're ready to go live, you'll receive a live token that will allow you to process bookings and payments through our API.

 If you have any questions, feel free to reach us at <a href="mailto:tech@citizenplane.com">tech@citizenplane</a>.
