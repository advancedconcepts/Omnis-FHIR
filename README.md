# Omnis FHIR
This is a collection of Open Source [Omnis][omnis-studio] Libraries to work with the [HL7 FHIR][fhir-spec] Standard.

## Requirements ##
Since oAuth2 is the standard for authentication on FHIR Servers. Omnis Studio 10.2 is the minimum requirement, this is 
the lowest version having the necessary functionality.

## Supported Specs ##

The following versions of the specifications are supported in respectively named libraries
|Spec version|Library file name|Library default name|Name for Context construction
|---|---|---|---|
|[STU3][stu3-spec]|`fhir_stu3.lbs`|`fhir_stu3`|`STU3`|
|[R4][r4-spec]|`fhir_r4.lbs`|`fhir_r4`|`R4`|
|[R5][r5-spec]|`fhir_r5.lbs`|`fhir_r5`|`R5`|

They all need the base library `fhir.lbs` to be present.

If you don't need a certain version, you can leave this out.

## Building Blocks

There are three different elements to connecting Omnis to a FHIR Resource

### Setting the proper FHIR Version

This is done using the **FHIRContext**, it makes sure you can connect against different versions 
from the same application

### Subclassing FHIRBrigde

This is needed for translating between your own data structures the FHIR Resource "twin"

[Bridge](bridge.md)

### Subclassing FHIRClientEndpoint

You subclass this in order to adapt to the pecularities of certain endpoints, the may need to fulfill different
requirements on the data transferrred.

[Client](client.md)

## Testing

In order to test your bindings, it is recommended to use a test server:

[Testing](testing.md)

## Regenerating

For the truly brave: you can also recreate the version specific libraries from the published data of a certain version
[Regenerating](regenerating.md)

## References

[fhir-spec]: https://hl7.org/fhir/
[dstu2-spec]: https://hl7.org/fhir/DSTU2/index.html
[stu3-spec]: https://hl7.org/fhir/STU3/index.html
[r4-spec]: https://hl7.org/fhir/R4/index.html
[r5-spec]: https://hl7.org/fhir/R5/index.html
[fhirpath-spec]: https://hl7.org/fhirpath/

[omnis-studio]: https://www.omnis.net
