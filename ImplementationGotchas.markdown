Eventbrite's API ahs been improved over the past couple
years, but still has some weirdness. 

I've catalogued a list of
issues you'll likely encounter if you want to make your own integration with
their API. Most of the code in this library exists simply to fix these
issues - otherwise you could probably just use HTTParty directly.


API Gotchas 
------------

A list of sticking points for anyone attempting their own integration with the Eventbrite API:


__/get => /update variable inconsistencies__

* event.timezone (Olson format, ex: "US/Central") => event.timezone (GMT offset hours, ex: "GMT-05")
* event.privacy (String representing privacy "Private"|"Public") => event.privacy (Boolean 0 = public)
* event.tickets.ticket.visible ("true" is visible) => ticket.hide (y is hidden, n is visible)

__Fields you can't edit__

* event.category
* event.tags
* event.logo
* ticket.hide (on /ticket_new. You must save the ticket then call /ticket_update to hide)

__Documentation errors__

* (fixed) /venue_new and /venue_update does not throw an error if "venue" is invalid/non-unique/empty.
* Dates are not technically ISO 8601 (ISO 8601 specifies a "T" - not a space - between date and time, so passing perfectly formatted ISO 8601 datetime strings such as those a standard library would provide will cause errors)
* Error description for event Privacy Error: <pre>"The privacy field must be equal to 0 (public) or 1 (private)"</pre> -> This is the opposite of the actual case. 0 is private and 1 is public, as described in other places within the API.
* /ticket_new and /ticket_update do not throw errors if quantity not set

__Other gotchas__

* Venue object included in event has extra "Lat-Long" attribute, along with "latitude" and "longitude". If you're turning a result into an object, this might cause an error if you don't expect it.
* Timezones are weird (nothing EventBrite can do about this one): GMT offset for timezones is always computed in standard time (don't adjust for Daylight Savings, unlike UTC offset). This weirdness means that once you save the timezone the Olson description might not match what you think it should (ex. US/Eastern becomes GMT-5 which then becomes America/Bogota)
