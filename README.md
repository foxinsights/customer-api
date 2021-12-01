# FoxInsights Customer API

The FoxInsights GmbH is providing an open API for users of the FoxMobile App for requesting the current state of their Fox devices for the purpose of ingesting that data into their home automation systems.
The usage of this API is subject to a Fair Use Policy and all code provided in this repository is licensed under the [SSPL](LICENSE).

## Fair Use Policy

The Fair Use Policy might be updated at will by FoxInsights GmbH. Only the most current version available in this repository is valid at any given time.

Every API call monitored and counts against a time based quota. If said quota is reached, you'll start getting the HTTP status code 429 - Too Many Requests - as response.
Getting the status of all of your device every hour is considered to be of fair use and no rate limiting is applied.

It is only acceptable to monitor you own devices, e.g. building a service that monitors and aggregates the data of devices of multiple users is not allowed.

## License
You can find a copy of the SSPL license in the LICENCE file in this repository. To quote [Wikipedia](https://en.wikipedia.org/wiki/Server_Side_Public_License) on the SSPL, all projects that use code available in this repository and make their  
> software available to third-parties (modified or not) as part of a "service" must release the source code for the entirety of the service, including without limitation all "management software, user interfaces, application program interfaces, automation software, monitoring software, backup software, storage software and hosting software, all such that a user could run an instance of the service using the Service Source Code you make available"

In short, that means that using the code provided here for private projects that you host yourself is allowed. In contrast to that, hosting your solution so others can use it without having an own installation, requires that you must release all the source code you use for the service as specified in section 13 of the SSPL.


## Contributions

If you find problems with the content of this repository you may open issue, we try to react on those in a timely manner.

Due to licensing issues we can't accept pull requests and will close them without comment.
At some point we might add a CLA to enable pull requests, if the interest is high enough.

## Documentation

Please refer to the [docs](docs/v1) folder for the API documentation

## Versioning

When a new version of the API is released, we will deprecate its predecessor.
From that moment on you will see an `x-api-warn` header in all responses, in addition to the `customer-api-version` that always contains the current version.
After three months, the old API version will be disabled, so keep watch on the response headers to be aware of version changes.
