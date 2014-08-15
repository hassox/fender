# Fender

Fender is a collection of protocol buffer extensions for use in constructing PROTO/JSON APIs over HTTP.

# Example

my/service.proto

    package my.service;

    import "fender/v1.proto";

    message Request {
      optional id = 1 [(fender.v1.fender_field).present = true];
    }

    message Response {
      optional id = 1;
      optional name = 2;
    }

    service TheService {
      option (fender.v1.fender_service) = {
        address: "example.com",
        request_format: JSON,
        transport: HTTPS
      };

      rpc GetMe (Request) returns (Response) {
        option (fender.v1.fender_method) = {
          http_method: GET,
          http_path: "/me/{+id}",
          timeout_millis: 3000
        }
      }
    }

This proto definition for TheService specifies that the method `GetMe` will make the following request

    theService.GetMe({id: 'ABCDE'})

    // Issues a GET request to 
    https://example.com/me/ABCDE

    // Sets the Accept header to application/json

    // Parses the response into a Response object.

Other options on method are:

* `request_body_field` - takes a field from the request object and makes that the body of the request
* `response_body_field` - Takes the response body, and sets a particular field rather than constructing the message from the raw response. For example, when an API returns an array.

