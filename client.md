# Client Endpoints

Client endpoints are subclasses of `FHIRClientEndpoint`.

## Properties

Before being ready they need the following properties set

| Property  | Content  | Example  |
|-----------|--------|----------|
| `$bridge`   | instance of subclass of `FHIRBridge` | `Calculate $cinst.$bridge as $classes.oMCFHIRBridge.$newref()` |
| `$baseUrl`    |  the base URL of the REST endpoint | `Calculate $cinst.$baseURL as 'http://localhost:8080/hapi/fhir/' `|
| `$context` | the FHIR release this endpoint is supporting | `Calculate $cinst.$context as 'r4' `

## Security

There are several different methods that get used for authorization, right now we only support authorization methods that can implemented using HTTP headers.

### Bearer Token Authorization

The FHIRClientEndpoint has a `$addHeader` method which can be used to add bearer token, e.g.

`Do $cinst.$addHeader('Authorization',con('Bearer ','7bada06393f677d72fee0b93c07ee807')`

## Posting a resource

The `FHIRClientEndpoint` has a low-level `$POSTResource` method

| Parameter  | Value  | Example  |
|-----------|--------|----------|
| `pRelativeURL`   | part of the URL that gets added to the baseURL | `'Patient'` |
| `pResource`    | the FHIR Resource to post | |
| `pFormat` | the format | `'json'` (default)

For frequently used Resources there are convenience methods available eg `$POSTPatient` that only need `pResource` and (optionally) `pFormat`

There are methods using 'native' Resources, eg `$POSTNativePatient` taking a Patient row specific to the host application that then get translated to its FHIR counterbart using the `$bridge$`