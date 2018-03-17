# DRAFT OpenBooking API Protocol

Being a protocol there will always be a "caller" and "receiver". In the docs I use "->" to indicate a request from the caller to the receiver, and "<-" to indicate the response.


## Handshake Example

For anyone getting started with the protocol, it would go along the lines of:

1. Big OTA: Hey, we support the OpenBooking API, do you?
2. Supplier: Yes our system does, here is our endpoint: https://secret@api.bookingsystem.com/ob
3. Big OTA: Thanks, will plug this into the system and hand back to our team to do the mapping

Or, more interestingly:

1. Turnstile Manufacturer: Hey, we support the OpenBooking API, do you?
2. Supplier: Yes our system does, here is our endpoint: https://secret@api.bookingsystem.com/ob
3. Turnstile Manufacturer: Thanks, we'll configure this before making the shipment

The first request the "caller" (in this case the Big OTA) does is to perform a handshake. This tells the "receiver" (in this case the Supplier) what capabilities the caller supports, and allows the receiver to respond with their negotiated capabilities and product list so mapping can start.

-> Handshake
The handshake request, like all requests, is performed as a POST HTTP request using the HTTP Basic Authentication credentials provided in the endpoint URL. The capabilities it defines are given as "optional" and "required".

```json
{
  "type": "Handshake",
  "data": {
    "capabilities": {
      "optional": [ "pickups", "dynamic-pricing", "custom-fields", "travelconnect/content" ],
      "required": [ "age-categories", "availability", "batch-availability", "reservation" ]
    }
  }
}
```

<- Handshake
The receiver, having received the list of capabilities now matches them against their own implemented capabilities and returns the "negotiated" list of capabilities this connection will support, along with a list of products. The key on the `products` dictionary indicated the ID used in future calls. The product information returned at this stage is important only for 

```json
{
  "type": "Handshake",
  "data": {
    "capabilities": [ "age-categories", "reservation", "pickups" ],
    "products": {
      "12352": {
        "code": "TOUR12352",
        "name": "Some tour name",
        "description": "Something that helps identify this product for mapping",
        "age-categories": [ "adult", "child" ]
      },
      "12353": {
        "code": "TOUR12353",
        "name": "Some tour name",
        "description": "Something that helps identify this product for mapping",
        "age-categories": [ "adult" ]
      }
    }
  }
}
```

If the required capabilities were not met, the server should respond with an error response, for example:

```json

{
  "type": "Handshake",
  "error": "Required capability 'age-categories' was not met."
}
```

