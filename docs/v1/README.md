# Customer API - Version 1 - 2021-11-08

This is the first public release of the FoxInsights Customer API

## Endpoints

### Authorization

To use this API, an authorization token has to be acquired first. This is done by providing your credentials to
the `login` endpoint. The response contains an access token that is valid for 15 minutes and a refresh token
which can be used to get a new access and a new refresh token.

#### Login

Request:

* URL
    * POST https://api.oilfox.io/customer-api/v1/login
* Header
    * Content-Type: application/json
* Body  
  All fields are required
  ```
  {
      "password":"mySafePassword",
      "email":"myUser@someMailProvider.com"
  }
  ```

Response:

```
{
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV[...]wXdfjS9g7P7XAt6GnOQ",
    "refresh_token": "eyJ0eXAiOiJKV[...]mFpd7W2RmOx0XGvEAIg"
}
```

##### Example

```
curl https://api.oilfox.io/customer-api/v1/login -H 'Content-Type: application/json' --data '{"password":"mySafePassword","email":"myUser@someMailProvider.com"}'
```

```
{
  "token_type": "Bearer",
  "access_token": "eyJ0eXAiOiJKV[...]wXdfjS9g7P7XAt6GnOQ",
  "refresh_token": "eyJ0eXAiOiJKV[...]mFpd7W2RmOx0XGvEAIg"
}
```

#### Refresh Access Token

If your authorization token is no longer valid (after 15 minutes), you can request a new one using the refresh token
from the login response. The refresh token is invalidated with the request and a new one is returned in addition to the
authorization token.

Request:

* URL
    * POST https://api.oilfox.io/customer-api/v1/token?refresh_token={refresh_token}
* Header
    * Content-Type: application/x-www-form-urlencoded

Response:

```
{
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV[...]wXdfjS9g7P7XAt6GnOQ",
    "refresh_token": "eyJ0eXAiOiJKV[...]mFpd7W2RmOx0XGvEAIg"
}
```

##### Example

```
curl https://api.oilfox.io/customer-api/v1/token --data-urlencode "refresh_token=eyJ0eXAiOiJKV[...]mFpd7W2RmOx0XGvEAIg"
```

```
{
  "token_type": "Bearer",
  "access_token": "eyJ0eXAiOiJKV[...]09xNJHsc2wtfnxSEEdqQ",
  "refresh_token": "eyJ0eXAiOiJKV[...]gOkbAD0PcqfrESkyenMw"
}
```

### Fetching Device Data

There are two endpoints to provide you the data of your devices: one endpoint which returns a single device and one
which returns all your devices at once. A single device response contains the following fields:

| name              | type             | description                                           |
|-------------------|------------------|-------------------------------------------------------|
| hwid              | string           | Hardware ID of the device                             |
| currentMeteringAt | string           | RFC3339 timestamp                                     |
| nextMeteringAt    | string           | RFC3339 timestamp                                     |
| daysReach         | int, optional    | estimated days until the storage runs empty           |
| validationError   | string, optional | enum with errors regarding the measurement, see below |
| batteryLevel      | string, optional | enum of the battery level, see below                  |
| fillLevelPercent  | int, optional    | fill level in %, 0-100                                |
| fillLevelQuantity | int, optional    | fill level in `kg` or `L`                             | 
| quantityUnit      | string           | unit of the fill level: `kg` or `L`                   |

**Enum batteryLevel**

| name     | description            |
|----------|------------------------|
| FULL     | Full battery level     |
| GOOD     | Good battery level     |
| MEDIUM   | Medium battery level   |
| WARNING  | Low battery level      |
| CRITICAL | Critical battery level |

**Enum validationError**

| name                   | description                          |
|------------------------|--------------------------------------|
| NO_METERING            | No measurement yet                   |
| EMPTY_METERING         | Incorrect Measurement                |
| NO_EXTRACTED_VALUE     | No fill level detected               |
| SENSOR_CONFIG          | Faulty measurement                   |
| MISSING_STORAGE_CONFIG | Storage configuration missing        |
| INVALID_STORAGE_CONFIG | Incorrect storage configuration      |
| DISTANCE_TOO_SHORT     | Measured distance too small          |
| ABOVE_STORAGE_MAX      | Storage full                         |
| BELOW_STORAGE_MIN      | Calculated filling level implausible |

#### Get Single Device

* URL
    * GET https://api.oilfox.io/customer-api/v1/device/{hwid}
* Header
    * Authorization: Bearer eyJ0eXAiOiJKV1[...]wXdfjS9g7P7XAt6GnOQ

##### Example

```
curl https://api.oilfox.io/customer-api/v1/device/ON0123456789 -H 'Authorization: Bearer eyJ0eXAiOiJKV1[...]wXdfjS9g7P7XAt6GnOQ'
```

```
{
  "hwid": "ON0123456789",
  "currentMeteringAt": "2021-11-04T11:46:48.348650Z",
  "nextMeteringAt": "2021-11-05T11:46:48.348650Z",
  "validationError": "MISSING_STORAGE_CONFIG",
  "batteryLevel": "FULL",
  "quantityUnit": "L"
}
```

#### Get All Devices

* URL
    * GET https://api.oilfox.io/customer-api/v1/device
* Header
    * Authorization: Bearer eyJ0eXAiOiJKV1[...]wXdfjS9g7P7XAt6GnOQ

##### Example

```
curl https://api.oilfox.io/customer-api/v1/device -H 'Authorization: Bearer eyJ0eXAiOiJKV1[...]wXdfjS9g7P7XAt6GnOQ'
```

```
{
  "items": [
    {
      "hwid": "ON0123456789",
      "currentMeteringAt": "2021-11-04T11:46:48.348650Z",
      "nextMeteringAt": "2021-11-05T11:46:48.348650Z",
      "validationError": "MISSING_STORAGE_CONFIG",
      "quantityUnit": "L"
    },
    {
      "hwid": "ON0987654321",
      "currentMeteringAt": "2021-11-04T12:35:50.202821Z",
      "nextMeteringAt": "2021-11-05T12:35:50.202821Z",
      "daysReach": 93,
      "batteryLevel": "FULL",
      "fillLevelPercent": 43,
      "fillLevelQuantity": 1643,
      "quantityUnit": "L"
    }
}
```
