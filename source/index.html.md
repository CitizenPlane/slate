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

> Example response

```json
{
  "results": [
    {
      "id": 20222,
      "organization_name": "Demo",
      "flight_number": "5O7892",
      "origin": "CDG",
      "destination": "PMI",
      "online_price": 273,
      "luggage": 20,
      "available_seats": 20,
      "airline": {
          "name": "CitizenPlane",
          "operated_by": "Transavia"
      },
      "cabin_class": "economy",
      "booking_class": "Y",
      "online_infant_price": 75,
      "included_airport_tax": 33,
      "departure_date": "2018-11-29 10:00",
      "arrival_date": "2018-11-29 16:00"
    },
    {...},
    {...}
  ],
  "nbResults": 255,
  "page": 0,
  "nbPages": 3
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
	flight_id: 20222,
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
    "id": 19,
    "seats": 2,
    "effective_price": 273,
    "created_at": "2018-10-16T15:02:28.797Z",
    "flight": {
        "id": 20222,
        "formatted_date": {
            "departure": {
                "date": "November 29th 2018",
                "time": "10:00"
            },
            "arrival": {
                "date": "November 29th 2018",
                "time": "16:00"
            }
        },
        "luggage": 20,
        "origin": {
            "iata_code": "CDG"
        },
        "destination": {
            "iata_code": "PMI"
        },
        "online_price": 273,
        "online_infant_price": 75,
        "airline": {
            "name": "CitizenPlane",
            "operated_by": "Transavia"
        }
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
  "request_id": 19,
  "booking_id": "12345",
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
        "booking_id": "12345",
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
    "flight_id": 20222,
    "effective_price": 250,
    "effective_infant_price": 75,
    "pnr_reference": "K9IR6XLGQ",
    "reseller_fields": {
      "id": "12345"
    },
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
	"is_infant": false
      },
      {
	"first_name": "Jane",
	"last_name": "Doe",
	"gender": "female",
	"is_infant": false
      }
    ],
    "created_at": "2018-10-16T15:02:42.441Z",
    "id": "8765"
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
     "departure_date": "2018-11-29T09:00:00.000Z",
     "arrival_date": "2018-11-29T15:00:00.000Z",
     "organization": {
	  "name": "Orga-1"
     },
     "cabin_class": "economy",
     "booking_class": "Y",
     "included_airport_tax": 30,
     "airline": {
	  "name": "CitizenPlane",
	  "operated_by": "ASL Airlines France"
     }
   }
  }
}
```

This endpoint confirms the previously created booking request and processes the booking as well as the associated payment. Once the booking is created, the customer will receive a confirmation by email along with the ticket(s).

<aside class="notice">If the payment fails for any reason, the request associated to this booking will be considered as aborted and the process is to be started again.</aside>

### HTTP request

`POST https://booking-api.citizenplane.com/v1/bookings`


<aside class="success">To test this endpoint, <a href="https://booking-api.citizenplane.com/documentation#/bookings" target="_blank">click here</a>.</aside>

### URL parameters


Parameter | Type | Status | Description
--------- | ---- | ------ | -----------
request_id | *integer* | **required** | The request id (see `/requests`).
booking_id | *string* | **required** | An id generated by the caller to identify the booking.
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
card_data | *object* | **required** | An object containing the customer's credit card information. <a href="#booking-card-info">See child arguments</a>.

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

CitizenPlane's API has a sandbox mode allowing you to safely test your integration with us. In order to do so, you will need a test token, which will give you access to our demo flights. You will be able to test our different endpoints without actually booking any seats or processing any payments.

<aside class ="notice">Please note that we only have a few demo flights for you to use. We advise you to first make a GET request to /flights without any query parameters except pagination to get the full list of demo flights. You'll then know wich query parameters to test.</aside>

To test the payment processing method, use any of the testing cards provided by Stripe: <a href="https://stripe.com/docs/testing#cards" target="_blank">Stripe testing cards</a>.

Once you're ready to go live, you'll receive a live token that will allow you to process bookings and payments through our API.

 If you have any questions, feel free to reach us at <a href="mailto:tech@citizenplane.com">tech@citizenplane</a>.
