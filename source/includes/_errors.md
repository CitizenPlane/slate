# Errors

<aside class="notice">
CitizenPlane uses conventional HTTP response codes to indicate the success or failure of an API request. Codes in the 4xx range indicate an error that failed given the information provided (e.g., a required parameter was omitted, a request has expired, a charge failed, etc.). Codes in the 5xx range indicate an error with CitizenPlane's servers (these are rare).
</aside>

The CitizenPlane API uses the following error codes:


Code | Meaning
---- | -------
400 | Bad Request -- Your request is invalid (e.g missing payload keys or process a booking with a `passenger_count` that doesn't match the data stored during the request call).
401 | Unauthorized -- Your API key is wrong.
403 | Forbidden -- The requested action cannot be permitted (e.g process a booking on a `ABORTED` request)
404 | Not Found -- The specified data could not be found (e.g `flight_id` doesn't exist)
500 | Internal Server Error -- We had a problem with our server. Try again later and contact us if the issue remain.
