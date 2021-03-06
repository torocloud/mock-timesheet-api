# Mock Timesheet API
A simple timesheet api with CRUD operations

### Prerequisites

  - Apache Maven 3+
  - [Martini Desktop](https://www.torocloud.com/martini/download)

### Building the Martini Package

```
$ mvn clean package
```
This will create a ZIP file named `mock-timesheet-api-bin.zip` containing all the files (services, configurations, etc.) needed under the `target` folder. This ZIP file is what we call a [Martini Package](https://docs.torocloud.com/martini/latest/developing/package/) which then you can import in Martini Desktop to get started. You can learn more how to import a Martini Package by visiting our [documentation](https://docs.torocloud.com/martini/latest/developing/package/importing/)

### Getting the Martini Package from TORO Marketplace

You can also get this package via TORO Marketplace. See our documentation on [How to import a Martini Package from Marketplace](https://docs.torocloud.com/martini/latest/developing/package/importing/#from-the-marketplace).

### Usage
This package exposes operations for a simple timesheet REST API that allows you to do CRUD operations. You can find the [Gloop REST API](https://docs.torocloud.com/martini/latest/developing/gloop/api/rest/) file at `io/toro/mock/timesheet/api/Timesheet` under the `code` folder after importing the package to your Martini Desktop application.

### Setup
This Martini Package automatically sets up the required resources for you. During the package startup, Martini will execute the configured _Startup Service_ that initializes the database to be used for storing the record that will be creating for using this mock api.

This package uses HSQL as the default datastore but you can change this to MySQL by updating the [package properties](https://docs.torocloud.com/martini/latest/developing/package/properties/) located at [conf](https://docs.torocloud.com/martini/latest/developing/package/directory/) folder. Below is what the contents of the properties file look like

```
# Flag used for testing
debug.enabled=true

# The database type to be used
database.type=hsql

# HSQL database configuration to be used for testing environment
hsql.driver=org.hsqldb.jdbc.JDBCDriver
hsql.connection.url=jdbc:hsqldb:file:${toroesb.home}data/hsql/mock_timesheet_api.db;hsqldb.tx=MVCC;sql.syntax_mys=true
hsql.username=sa
hsql.password=

# MySQL database configuration to be used in production environment
mysql.driver=com.mysql.cj.jdbc.Driver
mysql.connection.url=jdbc:mysql://<host>/<database-to-use>?allowMultiQueries=true&sessionVariables=sql_mode='ANSI'
mysql.username=root
mysql.password=
```
* `debug.enabled` when set to `true` initializes the database with dummy data when the package starts. The initialized data are also removed when the package stops or is unloaded. Set the value of this property to `false` if you want to keep your data when doing a restart. You can also set this property to `true` when the package starts and then set to `false` afterwards so that you'll have the data initialized on startup, and keep the data when the package or the Martini instance do a restart
* `database.type` sets the database provider the Martini package will use. If you will use the default hsql config, you don't need to change anything in the hsql configuration. **Note**: If you will use a different hsql database, make sure that you add `sql.syntax_mys=true` in the connection properties. This ensures that the SQL query from the SQL Services in this package will be compatible with hsql.

### Sending Authenticated Requests

You can use your ECC account to send authenticated request to the API. Your ECC credentials must be sent in the `Authorization` header in the HTTP request

#### To authenticate a request with basic authentication

1. Combine your email and password with a colon (`:`). e.g. `jdoe@mailinator.com:pa$$w0rd`
2. Encode the resulting string in Base64
3. Include an Authorization header in the HTTP request containing the base64-encoded string. Example: ```
Authorization: Basic amRvZUBtYWlsaW5hdG9yLmNvbTpwYSQkdzByZA==```

### Operations

The base url is `<host>/api/mock-timesheet-api` where `host` is the location where the Martini instance is deployed. By default, it's `localhost:8080`.

#### Users

`GET /users`

Returns a list of available users

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-timesheet-api/users \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Users fecthed successfully.",
    "users": [
        {
            "name": "Romeo Wavish",
            "id": "6003165b-a3c5-426b-a659-ce1595bffec9"
        },
        {
            "name": "Slade Klampk",
            "id": "7d9fcc44-530d-4307-a6b2-e6202d582bdb"
        },
        {
            "name": "Celestyn Eadmead",
            "id": "8acd811b-9b1c-42fc-83cf-ab5a8265d38c"
        }
    ]
}
```

`POST /users`

Create a new user record

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-timesheet-api/users \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "John Doe"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `201` with the response payload similar below.
```
{
    "result": "SUCCESS",
    "message": "Successfully created user with id: 957af7e2-e4ae-417c-92ff-80f9fc100b3c",
    "user": {
        "name": "John Doe",
        "id": "957af7e2-e4ae-417c-92ff-80f9fc100b3c"
    }
}
```

`GET /users/<userId>`

Retrieves a single user record that matches the given `<userId>`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-timesheet-api/users \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload similar below
```
{
    "user": {
        "name": "Romeo Wavish",
        "id": "6003165b-a3c5-426b-a659-ce1595bffec9"
    }
}
```

`PUT /users/<userId>`

Updates a user name

**Sample Request with custom field**

**curl**
```
curl -X PUT \
  http://localhost:8080/api/mock-timesheet-api/users/957af7e2-e4ae-417c-92ff-80f9fc100b3c \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "John P. Doe"
}'
```

**Sample Response** 

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Successfully updated user with id: 6003165b-a3c5-426b-a659-ce1595bffec9",
    "user": {
        "name": "Romeo Wavish",
        "id": "6003165b-a3c5-426b-a659-ce1595bffec9"
    }
}
```

`DELETE /users/<userId>`

Deletes a user record that matches the given `userId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/mock-timesheet-api/users/957af7e2-e4ae-417c-92ff-80f9fc100b3c \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Successfully deleted user with id: 957af7e2-e4ae-417c-92ff-80f9fc100b3c"
}
```

`GET /users/<userId>/attendance`

Retrieves all the attendance record for the given `userId`. Filtering the attendance record is also supported. To filter attendance records, include query parameters `startDate` and `endDate`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-timesheet-api/users/6003165b-a3c5-426b-a659-ce1595bffec9/attendance?startDate=2019-10-01T00:00:00&endDate=2019-10-05T00:00:00
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Successfully fetched attendance for period Wed May 02 00:00:00 PST 2018 - Wed Mar 11 00:00:00 PST 2020",
    "attendance": [
        {
            "type": "TIME OUT",
            "timestamp": "2019-10-02T19:45:36+0800",
            "id": "2db87c3f-f512-45c5-919e-53212ff82549",
            "userId": "6003165b-a3c5-426b-a659-ce1595bffec9"
        },
        {
            "type": "TIME OUT",
            "timestamp": "2019-10-03T17:01:03+0800",
            "id": "62fc08db-695f-4534-979e-4dcd8dc8b4b7",
            "userId": "6003165b-a3c5-426b-a659-ce1595bffec9"
        },
        ...
        {
            "type": "TIME OUT",
            "timestamp": "2019-10-01T17:07:46+0800",
            "id": "c6f8174a-23f6-44ff-8480-976876986061",
            "userId": "6003165b-a3c5-426b-a659-ce1595bffec9"
        },
        {
            "type": "TIME IN",
            "timestamp": "2019-10-01T07:55:17+0800",
            "id": "e8c92818-a3a5-41ad-80bd-c350c1111966",
            "userId": "6003165b-a3c5-426b-a659-ce1595bffec9"
        }
    ]
}
```

`POST /users/<userId>/attendance`

Creates a new attendance record for the given `userId.

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-timesheet-api/users/6003165b-a3c5-426b-a659-ce1595bffec9/attendance \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Content-Type: application/json' \
  -d '{
    "type": "TIME IN",
    "timestamp": "2020-03-11T07:55:17"
}'
```

Available values for `type`: `TIME IN`, `TIME OUT`

**Sample Response**

If the request is successful, it will return an HTTP status code `201`, with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully recorded 'TIME OUT' attendance 'caedbe7e-d8a6-4105-9cf1-2af9a79b8bfc' of user '6003165b-a3c5-426b-a659-ce1595bffec9' on 'Wed Oct 02 07:45:17 PST 2019'",
    "attendance": {
        "type": "TIME OUT",
        "timestamp": "2019-10-02T19:45:36+0800",
        "id": "2db87c3f-f512-45c5-919e-53212ff82549",
        "userId": "6003165b-a3c5-426b-a659-ce1595bffec9"
    }
}
```

`GET /attendance`

Retrieves all attendance record in a given period. Attendance period can be filtered using `startDate` and `endDate` query parameters

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-timesheet-api/attendance?startDate=2019-10-01T00:00:00&endDate=2019-10-05T00:00:00 \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP response code `200`, with the response payload below:
```
{
    "response": {
        "result": "SUCCESS",
        "message": "Fetched all attendance for given period.",
        "attendance": [
            {
                "type": "TIME IN",
                "timestamp": "2019-10-03T01:11:25+0000",
                "id": "034dfe94-acdd-4ad9-9544-50451d5129d6",
                "userId": "8acd811b-9b1c-42fc-83cf-ab5a8265d38c"
            },
            {
                "type": "TIME IN",
                "timestamp": "2019-10-02T23:56:44+0000",
                "id": "1b4764ea-89b5-44d4-b73c-5bddd5217c23",
                "userId": "7d9fcc44-530d-4307-a6b2-e6202d582bdb"
            },
            {
                "type": "TIME IN",
                "timestamp": "2019-10-01T23:51:27+0000",
                "id": "d23d393e-ac4a-42a2-8e35-af9ff2a341e0",
                "userId": "8acd811b-9b1c-42fc-83cf-ab5a8265d38c"
            },
            {
                "type": "TIME IN",
                "timestamp": "2019-09-30T23:55:17+0000",
                "id": "e8c92818-a3a5-41ad-80bd-c350c1111966",
                "userId": "6003165b-a3c5-426b-a659-ce1595bffec9"
            }
        ]
    }
}
```

`GET /attendance/<attendanceId>`

Retrieves a single attendance record that matches the given `attendanceId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-timesheet-api/attendance/cf2eee33-aa20-4156-9705-d9bf8ac03cc9 \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `202` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Attendance fetched sucessfully.",
    "attendance": {
        "type": "TIME OUT",
        "timestamp": "2019-10-02T19:45:36+0800",
        "id": "2db87c3f-f512-45c5-919e-53212ff82549",
        "userId": "6003165b-a3c5-426b-a659-ce1595bffec9"
    }
}
```

`PUT /attendance/<attendanceId>`

Updates an attendance record. Mainly for updating the timestamp, or the attendance type.

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-timesheet-api/users/6003165b-a3c5-426b-a659-ce1595bffec9/attendance \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Content-Type: application/json' \
  -d '{
    "type": "TIME IN"
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Attendance successfully updated.",
    "attendance": {
        "type": "TIME IN",
        "timestamp": "2019-10-01T17:07:46+0800",
        "id": "c6f8174a-23f6-44ff-8480-976876986061",
        "userId": "6003165b-a3c5-426b-a659-ce1595bffec9"
    }
}
```

`DELETE /attendance/<attendanceId`

Deletes an attendance record that matches the given `attendanceId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/mock-timesheet-api/attendance/cf2eee33-aa20-4156-9705-d9bf8ac03cc9 \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP response `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Successfully deleted attendance with id: cf2eee33-aa20-4156-9705-d9bf8ac03cc9"
}
```