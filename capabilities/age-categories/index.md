---
title: Age Categories
---

# Age Categories

Age categories allow bookings to define different ticket types for a booking such as adult, child etc.

These age categories are static, and do not change.

- adult
- child
- youth
- infant
- senior
- student

Testing some JSON

```json
{
  "type": "BookingConfirmation",
  "capabilities": [ "contact_info", "age_categories", "reservation", "pickups" ],
  "data": {
    "booking": {
      "agent_reference": "TEST312312123",
      "product": "TOUR12352",
      "travel_date": "2018-10-07",
      "departure_time": "09:00",
      "pickup": {
        "lodging": "Hilton Garden Inn",
        "address": "401 S San Fernando Blvd, Burbank, CA 91502, USA"
      },
      "age_categories": {
        "adult": 2,
        "child": 1
      },
      "contact_info": {
        "customer": "Mr. John Smith",
        "email": "john.smith@example.com",
        "mobile": "+17345551212",
        "locale": "en-GB"
      }
    }
  }
}
```