# Amadeus Java SDK

[![Build Status](https://travis-ci.org/amadeus4dev/amadeus-java.svg?branch=master)][travis]
[![Contact Support](https://github.com/amadeus4dev/amadeus-ruby/raw/master/.github/images/support.svg?sanitize=true)][support]

Amadeus provides a set of APIs for the travel industry. Flights, Hotels, Locations and more.

For more details see the [Java
documentation](https://amadeus4dev.github.io/amadeus-java/) on
[Amadeus.com](https://developers.amadeus.com).

## Installation

This library requires Java 1.7+ and the the [Gson library](https://github.com/google/gson). 

With __Maven__ you need to add to your pom.xml
```xml
<dependency>
  <groupId>com.google.code.gson</groupId>
  <artifactId>gson</artifactId>
  <version>2.8.5</version>
</dependency>
```

With __Gradle__
```js
compile 'com.google.code.gson:gson:2.8.5'
```

You can install the SDK via Maven or Gradle.

#### Maven
```xml
<dependency>
  <groupId>com.amadeus</groupId>
  <artifactId>amadeus-java</artifactId>
  <version>1.0.1</version>
</dependency>
```
#### Gradle
```js
compile "com.amadeus:amadeus-java:1.0.1"
```

## Getting Started

To send make your first API call you will need to [register for an Amadeus
Developer Account](https://developers.amadeus.com/create-account) and set up
your first application.

```java
import com.amadeus.Amadeus;
import com.amadeus.Params;

import com.amadeus.exceptions.ResponseException;
import com.amadeus.referenceData.Locations;
import com.amadeus.resources.Location;

public class AmadeusExample {
  public static void main(String[] args) throws ResponseException {
    Amadeus amadeus = Amadeus
            .builder("[client_id]", "[client_secret]")
            .build();

    Location[] locations = amadeus.referenceData.locations.get(Params
      .with("keyword", "LON")
      .and("subType", Locations.ANY));

    System.out.println(locations);
  }
}
```

## Initialization

The client can be initialized directly.

```java
//Initialize using parameters
Amadeus amadeus = Amadeus
        .builder("[client_id]", "[client_secret]")
        .build();
```

Alternatively it can be initialized without any parameters if the environment
variables `AMADEUS_CLIENT_ID` and `AMADEUS_CLIENT_SECRET` are present.

```java
Amadeus amadeus = Amadeus
        .builder(System.getenv())
        .build();
```

Your credentials can be found on the [Amadeus
dashboard](https://developers.amadeus.com/my-apps). [Sign
up](https://developers.amadeus.com/create-account) for an account today.

By default the environment for the SDK is the `test` environment. To switch to
a production (paid-for) environment please switch the hostname as follows:

```java
Amadeus amadeus = Amadeus
        .builder(System.getenv())
        .setHostname("production")
        .build();
```

## Documentation

Amadeus has a large set of APIs, and our documentation is here to get you
started today. Head over to our
[Reference](https://amadeus4dev.github.io/amadeus-java/) documentation for
in-depth information about every SDK method, its arguments and return types.


* [Get Started](https://amadeus4dev.github.io/amadeus-java/) documentation
  * [Initialize the SDK](https://amadeus4dev.github.io/amadeus-java/)
  * [Find an Airport](https://amadeus4dev.github.io/amadeus-java/)
  * [Find a Flight](https://amadeus4dev.github.io/amadeus-ruby/)
  * [Get Flight Inspiration](https://amadeus4dev.github.io/amadeus-ruby/)

## Making API calls

This library conveniently maps every API path to a similar path.

For example, `GET /v2/reference-data/urls/checkin-links?airline=1X` would be:

```java
amadeus.referenceData.urls.checkinLinks.get(Params.with("airline", "1X"));
```

Similarly, to select a resource by ID, you can pass in the ID to the **singular** path.

For example,  `GET /v1/shopping/hotel/ABC123/offers/DEF234` would be:

```java
amadeus.hotel("ABC123").offer("DEF234").get(...);
```

You can make any arbitrary API call as well directly with the `.get` method.
Keep in mind, this returns a raw `Resource`

```java
Resource resource = amadeus.get('/v2/reference-data/urls/checkin-links',
  Params.with("airline", "1X"));

resource.getResult();
```

## Response

Every successful API call returns a `Resource` object. The underlying
`Resource` with the raw available.

```java
Location[] locations = amadeus.referenceData.locations.get(Params
  .with("keyword", "LON")
  .and("subType", Locations.ANY));

 // The raw response, as a string
locations[0].getResponse().getBody();
```

## Pagination

If an API endpoint supports pagination, the other pages are available under the
`.next`, `.previous`, `.last` and `.first` methods.

```java
Location[] locations = amadeus.referenceData.locations.get(Params
  .with("keyword", "LON")
  .and("subType", Locations.ANY));

// Fetches the next page
Location[] locations = (Location[]) amadeus.next(locations[0]);
```

If a page is not available, the method will return `null`.

## Logging & Debugging

The SDK makes it easy to add your own logger.

```java
require 'logger'

amadeus = Amadeus::Client.new(
  client_id: '...',
  client_secret: '...',
  logger: Logger.new(STDOUT)
)
```

Additionally, to enable more verbose logging, you can set the appropriate level
on your own logger, though the easiest way would be to enable debugging via a
parameter on initialization, or using the `AMADEUS_LOG_LEVEL` environment
variable.

```java
Amadeus amadeus = Amadeus
        .builder("[client_id]", "[client_secret]")
        .setLogLevel("debug") // or warn
        .build();
```

## List of supported endpoints

```java
// Flight Cheapest Date Search
FlightDate[] flightDates = amadeus.shopping.flightDates.get(Params
  .with("origin", "NCE")
  .and("destination", "PAR")
  .and("duration", 1));

// Flight Inspiration Search
FlightDestination[] flightDestinations = amadeus.shopping.flightDestinations.get(Params
  .with("origin", "MAD"));

// Flight Low-fare Search
FlightOffer[] flightOffers = amadeus.shopping.flightOffers.get(Params
  .with("origin", "MAD")
  .and("destination", "OPO")
  .and("departureDate", "2018-07-08"));

// Flight Check-in Links
CheckinLink[] checkinLinks = amadeus.referenceData.urls.checkinLinks.get(Params
  .with("airline", "1X"));

// Airport & City Search (autocomplete)
// Find all the cities and airports starting by the keyword 'Lon'
Location[] locations = amadeus.referenceData.locations.get(Params
  .with("keyword", "lon")
  .and("subType", Locations.ANY));

// Get a specific city or airport based on its id
Location location = amadeus.referenceData
  .location("ALHR").get();

// Airport Nearest Relevant
Location[] locations = amadeus.referenceData.locations.airports.get(Params
  .with("latitude", 49.0000)
  .and("longitude", 2.55));

// Flight Most Searched Destinations
FareSearch[] fareSearches = amadeus.travel.analytics.fareSearches.get(Params
  .with("origin", "SFO")
  .and("sourceCountry", "US")
  .and("period", "2017-08"));

// Flight Most Traveled Destinations
AirTraffic[] airTraffics = amadeus.travel.analytics.airTraffic.traveled.get(Params
  .with("origin", "NCE")
  .and("period", "2017-08"));

// Hotel Search API
// Get list of hotels by cityCode
HotelOffer[] offers = amadeus.shopping.hotelOffers.get(Params
  .with("cityCode", "PAR"));
  
// Get list of offers for a specific hotel
HotelOffer offer = amadeus.shopping
  .hotel("SMPARCOL")
  .hotelOffers.get();
  
// Confirm the availability of a specific offer for a specific hotel
Offer offer = amadeus.shopping
  .hotel("SMPARCOL")
  .offer("4BA070CE929E135B3268A9F2D0C51E9D4A6CF318BA10485322FA2C7E78C7852E").get();
```

## Development & Contributing

Want to contribute? Read our [Contributors Guide](.github/CONTRIBUTING.md) for
guidance on installing and running this code in a development environment.


## License

This library is released under the [MIT License](LICENSE).

## Help

Our [developer support team](https://developers.amadeus.com/support) is here
to help you. You can find us on
[StackOverflow](https://stackoverflow.com/questions/tagged/amadeus) and
[email](mailto:developers@amadeus.com).

[travis]: http://travis-ci.org/amadeus4dev/amadeus-java
[support]: http://developers.amadeus.com/support
