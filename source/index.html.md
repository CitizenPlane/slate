---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
- shell: curl
- javascript--nodejs: Node

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

The default API endpoint is <a href="https://booking-api.citizenplane.com" target="_blank"> booking-api.citizenplane.com</a>. JSON is returned by all API responses, included errors.

To make the API as explorable as possible, accounts have test and live API tokens. API tokens can be obtained after contacting us at <a href="mailto:tech@citizenplane.com" target="_blank">tech@citizenplane.com</a>.

If you wish to test the different endpoints detailed below, we have a dedicated page allowing you to perform requests with your parameters. An API token will be required for each call. To test our endpoints, <a href="https://booking-api.citizenplane.com/documentation" target="_blank">click here</a>.

If you have any questions, feel free to send us a message at <a href="mailto:tech@citizenplane.com">tech@citizenplane.com</a>

# Authentication


```shell
# Example request

curl "https://booking-api.citizenplane.com/airports/origins" \
   -H "Authorization: Bearer {your_api_token}"

# Make sure to replace {your_api_token} with the token you were given.
```

Authentication to the API is performed via Bearer Tokens. Once you have registered your account with us, you will be given a test token to ensure a proper integration period. When you're ready to go live, we will assign you a live token. Make sure not to share your token in publicly accessible area such GitHub or client-side code.

Every request to CitizenPlane API must include a Bearer token in the following format: `Authorization: Bearer {your_api_token}`

# Endpoints

## Retrieve all departure airports

> Example request

```shell
curl "https://booking-api.citizenplane.com/airports/origins" \
   -H "Accept: application/json" \
   -H "Authorization: Bearer {your_api_token}"
```

```javascript--nodejs
const rp = require("request-promise")

const getAirports = async () => {
  try {
    const airports = await rp({
      method: "GET",
      uri: "https://booking-api.citizenplane.com/airports/origins",
      headers: {
	Authorization: "Bearer {your_api_token}"
      }
    })
    return airports
  } catch (err) {
    console.log(err)
 }
}
```

> Example response

```json
{
  "results": [
    {
      "iata_code": "ADB",
      "country_name": "Turkey",
      "city_name": "Izmir"
    },
    {
      "iata_code": "AYT",
      "country_name": "Turkey",
      "city_name": "Antalya"
    }
  ],
  "total": 2
}
```

This endpoint retrieves a list of all departure airports in CitizenPlane's stock of available routes.

### HTTP request

`GET https://booking-api.citizenplane.com/airports/origins`

<aside class="success">To test this endpoint, <a href="https://booking-api.citizenplane.com/documentation#!/airports/getAirportsOrigins" target="_blank">click here</a>.</aside>

## Retrieve a list of arrival airports

> Example request

```shell
curl "https://booking-api.citizenplane.com/airports/destinations?origin=CDG" \
   -H "Accept: application/json" \
   -H "Authorization: Bearer {your_api_token}"
```

```javascript--nodejs
const rp = require("request-promise")

const getAirports = async () => {
  try {
    const airports = await rp({
      method: "GET",
      uri: "https://booking-api.citizenplane.com/airports/destination?origin=CDG",
      headers: {
	Authorization: "Bearer {your_api_token}"
      }
    })
    return airports
  } catch (err) {
    console.log(err)
 }
}
```

> Example response

```json
{
  "CDG": [
    {
      "iata_code": "DJE",
      "country_name": "Tunisia",
      "city_name": "Djerba"
    },
    {
      "iata_code": "HER",
      "country_name": "Greece",
      "city_name": "Heraklion"
    }
  ]
}
```

This endpoint retrieves a list of all arrival airports associated to a specific departure airport. The response only includes arrival airports for CitizenPlane's open and available routes.


### HTTP request

`GET https://booking-api.citizenplane.com/airports/destinations`

<aside class="success">To test this endpoint, <a href="https://booking-api.citizenplane.com/documentation#!/airports/getAirportsDestinations" target="_blank">click here</a>.</aside>

### URL parameters

Parameter | Type | Status | Description
--------- | ---- | ------ | -----------
origin | *string* | **required** | An uppercase string of comma separated iata codes departure airports (can also be a single string).
nearby | *string* | *optional* | If nearby airports (up to a distance of 100km) should be taken into account, set the nearby parameter to `true`. Defaults to `false`.

## Retrieve a list of flights

> Example request

```shell
curl "https://booking-api.citizenplane.com/flights?origin=CDG&destination=DJE&start=2018-08-07&end=2018-08-30&seats=2"
  -H "Accept: application/json" \
  -H "Authorization: Bearer {your_api_token}"
```

```javascript--nodejs
const rp = require("request-promise")

const getFlights = async () => {
  try {
    const flights = await rp({
      method: "GET",
      uri: "https://booking-api.citizenplane.com/flights?origin=CDG&destination=DJE&start=2018-08-07&end=2018-08-30&seats=2",
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

> Example response

```json
{
  "CDG-DJE": [
    {
      "id": "Ar5WmxNM2k",
      "flight_number": "QS1234",
      "departure_date": "2018-08-29T15:00:00.000Z",
      "arrival_date": "2018-08-29T18:00:00.000Z",
      "origin": {
        "iata_code": "CDG",
        "city_name": "Paris"
      },
      "organization": {
        "name": "Demo"
      },
      "destination": {
        "iata_code": "DJE",
        "city_name": "Djerba"
      },
      "luggage": 15,
      "online_price": 215,
      "infant_price": 30
    }
  ]
}
```

This endpoint retrieves the details of all open flights in CitizenPlane's stock matching the query parameters. Flights returned by the API are identified by a unique ID that will be need for the ``/requests`` endpoint.

### HTTP request

`GET https://booking-api.citizenplane.com/flights`

<aside class="success">To test this endpoint, <a href="https://booking-api.citizenplane.com/documentation#/flights" target="_blank">click here</a>.</aside>

### URL parameters


Parameter | Type | Status | Description
--------- | ---- | ------ | -----------
origin | *string* | **required** | An uppercase string of comma-separated iata codes for the departure airports (can also be a single string).
nearbyO | *string* | *optional* | If set to `true`, nearby departure airports (up to 100km) will be considered in the flight search. Defaults to `false`.
destination | *string* | **required** | An uppercase string of comma-separated iata codes for the arrival airports (can also be a single string).
nearbyD | *string* | *optional* | If set `true`, nearby arrival airports (up to 100km) will be considered in the flight search. Defaults to `false`.
start | *date* | *optional* | Date from which the flight search will be operated. Date has to be formatted following this model: `YYYY-MM-DD`.
end | *date* | *optional* | Date until which the flight search will be operated. Date has to be formatted following this model: `YYYY-MM-DD`.
seats | *integer* | *optional* | Maximum number of seats to search for.

## Create a request

> Example request

```shell
curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' --header 'authorization: {your_api_token}' -d '{
  "flight_id": "Ar5WmxNM2k",
    "passengers": {
        "adults": 2,
        "children": 0,
        "infants": 0
    }
}' 'https://booking-api.citizenplane.com`/requests`'
```

```javascript--nodejs
const rp = require("request-promise")

const createRequest = async () => {
  try {
    const request = await rp({
      method: "POST",
      uri: `https://booking-api.citizenplane.com`/requests``,
      headers: {
	Authorization: "Bearer {your_api_token}"
      },
      json: true,
      body: {
	flight_id: "Ar5WmxNM2k",
	passengers: {
	  adults: 2,
	  children: 0,
	  infants: 0
	}
      }
    })
    return request
  } catch (err) {
    console.log(err)
  }
}
```

> Example response

```json
{
  "request": {
    "seats": 2,
    "effective_price": 215,
    "created_at": "2018-07-12T11:57:28.217Z",
    "id": "2AjMnZMDxz",
    "flight_id": "Ar5WmxNM2k"
  },
  "flight": {
      "formatted_date": {
	"departure": {
	  "date": "August 29th 2018",
	  "time": "17:00"
	},
	"arrival": {
	  "date": "August 29th 2018",
	  "time": "19:00"
	}
      },
      "online_price": 215,
      "luggage": 15,
      "origin": {
	"iata_code": "CDG"
      },
      "destination": {
	"iata_code": "DJE"
      }
  }
}
```

This endpoint creates a booking request on a specific flight identified by its id. A booking request holds a number of seats passed in the request payload for **fifteen minutes**. Seat price and infant price won't be subject to any changes during this time period and will match the information sent by the `/flights` request.

<aside class ="notice">Should the booking not be confirmed, the request would be canceled and the seats put back in sales.</aside>

### HTTP request

`POST https://booking-api.citizenplane.com`/requests`

<aside class="success">To test this endpoint, <a href="https://booking-api.citizenplane.com/documentation#/requests" target="_blank">click here</a>.</aside>

### URL parameters


Parameter | Type | Status | Description
--------- | ---- | ------ | -----------
flight_id | *string* | **required** | The flight id returned by the `/flights` GET request.
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

> Example request

```shell
curl POST --header 'Content-Type: application/json' --header 'Accept: application/json' --header 'authorization: {your_api_token}' -d '{
  "request_id": "2AjMnZMDxz",
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
      "is_infant": false
    },
    {
      "first_name": "Jane",
      "last_name": "Doe",
      "gender": "female",
      "is_infant": false
    }
  ],
  "card_data": {
    "number": "4242424242424242",
    "exp_year": 2019,
    "exp_month": 11,
    "cvc": "555",
    "name": "John Doe"
  }
}' 'https://booking-api.citizenplane.com/bookings'
```

```javascript--nodejs
const rp = require("request-promise")

const createBooking = async () => {
  try {
    const booking = await rp({
      method: "POST",
      uri: `https://booking-api.citizenplane.com/bookings`,
      headers: {
	Authorization: "Bearer {your_api_token}"
      },
      json: true,
      body: {
	"request_id": "2AjMnZMDxz",
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
	    "is_infant": false
	  },
	  {
	    "first_name": "Jane",
	    "last_name": "Doe",
	    "gender": "female",
	    "is_infant": false
	  }
	],
	"card_data": {
	  "number": "4242424242424242",
	  "exp_year": 2019,
	  "exp_month": 11,
	  "cvc": "555",
	  "name": "John Doe"
	}
      }
    })
    return booking
  } catch (err) {
    console.log(err)
  }
}
```

> Example response

```json
{
  "booking": {
    "request_id": "2AjMnZMDxz",
    "reseller_id": "eL67Pl7Wax",
    "flight_id": "Ar5WmxNM2k",
    "effective_price": 215,
    "reseller_booking_ref": "{your_orga_name}-2AjMnZMDxz",
    "first_name": "john",
    "last_name": "doe",
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
	"is_infant": false
      },
      {
	"first_name": "Jane",
	"last_name": "Doe",
	"gender": "female",
	"is_infant": false
      }
    ],
    "effective_infant_price": 30,
    "created_at": "2018-07-12T16:07:31.628Z",
    "id": "kY3vYGJ5zd"
  },
  "flight": {
    "origin": {
      "iata_code": "CDG",
      "country_name": "France",
      "city_name": "Paris",
      "timezone": "Europe/Paris",
    },
    "destination": {
      "iata_code": "DJE",
      "country_name": "Tunisia",
      "city_name": "Djerba",
      "timezone": "Africa/Tunis",
    },
    "departure_date": "2018-08-29T15:00:00.000Z",
    "arrival_date": "2018-08-29T18:00:00.000Z",
    "organization": {
      "name": "Demo"
    },
    "airline": {
      "id": "LAd5VA7mva",
      "name": "SmartWings",
      "iata_code": "QS",
      "created_at": "2018-03-06T15:57:28.034Z",
      "updated_at": "2018-07-06T06:15:38.742Z"
    }
  }
}
```

This endpoint confirms the previously created booking request and sends the booking information along with card information to CitizenPlane's API in order to process the booking and the associated payment. Once the booking is created, the customer will receive a confirmation by email along with the ticket(s). 

<aside class="notice">If the payment fails for any reason, the request associated to this booking will be considered as **ABORTED** and the process is to be started again.</aside>

### HTTP request

`POST https://booking-api.citizenplane.com/bookings`


<aside class="success">To test this endpoint, <a href="https://booking-api.citizenplane.com/documentation#/bookings" target="_blank">click here</a>.</aside>

### URL parameters


Parameter | Type | Status | Description
--------- | ---- | ------ | -----------
request_id | *string* | **required** | The request id returned by the `/requests` POST call.
distribution_channel | *string* | *optional* | Name of the ETA through which the reservation has been made.
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
passengers | *object* | **required** | An object containing personal information for each passenger on this booking. <a href="#booking-passengers-info">See child arguments</a>.
card_data | *object* | **required** | An object containing the customer's card information to process the booking's payment. <a href="#booking-card-info">See child arguments</a>.

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
      <td align="left">birth_country</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>*optional*</b></td>
      <td align="left">Passenger's birth country.</td>
    </tr>
    <tr>
      <td align="left">date_of_birth</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>*optional*</b></td>
      <td align="left">Passenger's date of birth.</td>
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

#### <table id="booking-card-info">
  <caption><b>card_data child arguments</b></caption>
  <tbody>
    <tr>
      <th align="left">Parameter</th>
      <th align="left">Type</th>
      <th align="left">Status</th>
      <th align="left">Description</th>
    </tr>
    <tr>
      <td align="left">number</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">The customer's card number.</td>
    </tr>
    <tr>
      <td align="left">exp_year</td>
      <td align="left"><i>integer</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">The customer's card expiration year (format `YYYY`).</td>
    </tr>
    <tr>
      <td align="left">exp_month</td>
      <td align="left"><i>integer</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">The customer's card expiration month (format `MM`).</td>
    </tr>
    <tr>
      <td align="left">cvc</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>required</b></td>
      <td align="left">Card security code. Only required for accounts based in European countries but highly recommended for other countries.</td>
    </tr>
    <tr>
      <td align="left">name</td>
      <td align="left"><i>string</i></td>
      <td align="left"><b>*optional*</b></td>
      <td align="left">The cardholder's full name.</td>
    </tr>
  </tbody>
</table>

# Testing

A test environment has been set up to ensure a proper integration. During this process, you will be given a **test token** allowing you to make calls in a controlled environment. Before going live, a **partner token** will be assigned to you. This token is **unique** and **non-expirable**.

## How testing works

If you'd like to test our endpoints, you can use tools like Postman or curl or you can use our additional API documentation which allows you to make calls <a href="https://booking-api.citizenplane.com/documentation" target="_blank">within this page</a>.

### Retrieve all departure airports

A simple **GET** request returning the list of all departure airports that are currently associated with open routes in CitizenPlane's stock. No specific instruction is needed for this request.

### Retrieve a list of arrival airports

A simple **GET** request returning the list of all our arrival airports (given an `origin` iata code) that are currently associated with open routes in CitizenPlane's stock. No specific instruction is needed for this request.

### Retrieve a list of flights

Given the limited available data to be requested, here are some guidelines to get proper responses according to the following query parameters: 

- `origin` & `destination`

Please make sure to requests only flights with the following origin & destination airports : 

airport name | iata code | country
------------ | --------- | -------
Brussels | BRU | Belgium
Roissy Charles-De-Gaulle | CDG | France
Djerba-Zarzis | DJE | Tunisia
Antalya | AYT | Turkey
Heraklion | HER | Greece
Adnan Menderes | ADB | Turkey
Orly | ORY | France

Any combination of these airports as `destination` or `origin` airports will return a result. 

- `nearbyO` & `nearbyD`

If set to true, the result will include any flight combination of nearby origin airports (if `nearbyO === 'true'`) and nearby destination airports (if `nearbyD === 'false'`) up to **100km**. 

To test this option, please use the two airports located in Paris: *CDG* & *ORY*. Other airports won't return any flights associated to nearby airports. 

- `seats`

Available flights hold between **5** and **70** seats. Thus, make sure your `seats` parameter is always below 70. Note that infants (< 2 yo) don't take a seat.

 - `start` & `end`

 Available flights range from **2018-07-10** to **2018-09-13**. Make sure your `start` and `end` parameters are between this date range.

### Create a request

 Creates a booking request that will hold the requested number of seats during a limited time period. If the request expires, the seats are no longer reserved and the process has to be started again. 

 Use any of the `flight_id` returned by the **/flights** call to create a request. To ensure that your request is accepted, note that: 

 - `effective_price` should equal `online_price` returned the the **/flights** call on the selected flight
 - `passengers` should not hold more than 70 adults and children (combined). Note that infants (< 2 yo) do not take a seat on the booking reservation

 ### Create a booking

 Confirms the booking requests and creates a booking on a specific flight. This call also processes the payment for the customer. 

 **Some instructions for the payload object:** 

 - `flight_id`: an id returned by the **/flights** call
 - `request_id`: an id returned by the **`/requests`** call
 - `effective_price` should equal `online_price`, returned by the **/flights** call on the selected flight
 - `passenger_count` and `infant_count` should match the `passengers` information
 - To test the payment processing method, use any of the testing cards provided by Stripe: <a href="https://stripe.com/docs/testing#cards" target="_blank">Stripe testing cards</a>.

 If you have any questions, feel free to reach us at <a href="mailto:tech@citizenplane.com">tech@citizenplane</a>
