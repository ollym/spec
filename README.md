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

### -> Handshake
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

### <- Handshake
The receiver, having received the list of capabilities now matches them against their own implemented capabilities and returns the "negotiated" list of capabilities this connection will support per product. The key on the `products` dictionary indicated the ID used in future calls.

```json
{
  "type": "Handshake",
  "data": {
    "products": {
      "12352": {
        "code": "TOUR12352",
        "name": "Some tour name",
        "description": "Something that helps identify this product for mapping",
        "capabilities": [ "age-categories", "reservation", "pickups" ],
        "age-categories": [ "adult", "child" ]
      },
      "12353": {
        "code": "ATTRACTION12353",
        "name": "Some attraction name",
        "description": "Something that helps identify this product for mapping",
        "capabilities": [ "age-categories" ],
        "age-categories": [ "adult" ]
      }
    }
  }
}
```

Only products that match the required capabilities should be returned.

## Booking Confirmation

A booking confirmation is 

```json
{
  "type": "BookingConfirmation",
  "capabilities": [ "age-categories", "reservation", "pickups" ],
  "data": {
    "booking": {
      "reference": "TEST123",
      ...
    }
  }
}
```

## Capabilities

Capabilities underpin the OpenBooking API Protocol. They allow the specification to remain simple for those getting started, whilst enabling almost unlimited expansion.

### Getting Started

Anyone can create a capability. To do so, you simply fork the `openbooking/spec` Github repository and create a new folder under the `/capabilities` directory where you want to define your capability. You should name the folder the same as what you want to call your capability, for example `dynamic-pricing`. The actual capability name will be equal to the Github username you forked the repository to, followed by the name of your capability, e.g. `github-username/dyanmic-pricing`.

If the forked repository is public it will immediately become visible on the openbooking.org website in an incubating state. If you want to work on the capability in private, you should fork it into a private repository before making it public.

### Incubation

Anyone can come up with an idea for a capability, fork the spec, and start building it. Once done so, it will be added to the openbooking.org website in an "incubating" state.

During incubation, the capability spec can chance, especially during the early stages. Implementors are encouraged to get involved and collaborate with capability developers to help define the specification and include the features they believe are important for their clients.

After time the specification will stabilise as more platforms integrate the capability. When this happens the capability creator can create a pull request on their fork, doing so will automatically create a "request for graduation".

### Graduation

Once the owner of a capability believes their specification has stabilised, and has received enough traction, they can submit it for graduation by creating a pull request on their forked repository.

The OpenBooking Working Group will then discuss and vote for the capability to graduate into the core specification. This discussion will all happen in an open setting on the pull request itself, making the process transparent. Code reviews and changes can be made within that pull request using Github's code review tools.

Finally, once successful, the prefix of the capability is dropped (e.g. `my-company/dynamic-pricing` => `dynamic-pricing`), the specification is frozen, and the capability is promoted to a "recommended" state for platforms to implement.

The process of freezing a capability's specification simply means no breaking changes can ever be made to this capability in the future. We want implementors of core capabilities to feel assured that their implementation, once complete, will never need to be updated.

### Versioning

Not all functionality of a capability will surface during the incubation process, so for graduated capabilities that want to make breaking changes can do so by forking their original capability and going through the incubation process again, except with a new name. For example: `my-company/dynamic-pricing-2`