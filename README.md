# Omnis FHIR
This is a collection of Open Source [Omnis][omnis-studio] Libraries to work with the [HL7 FHIR][fhir-spec] Standard.

## Requirements ##
For the Client Libraries Omnis Studio 8.1.7 is required, for the Server Libraries Version Omnis Studio 10.1 is the lowest version having the necessary functionality.

## Supported Specs ##

The following versions of the specifications are supported in respectively named libraries
|Spec version|Library file name|Library default name|Name for Context construction
|---|---|---|
|[STU3][stu3-spec]|`fhir_stu3.lbs`|`fhir_stu3`|`STU3'|
|[R4][r4-spec]|`fhir_r4.lbs`|`fhir_r4`|`R4'|
|[R5 Draft][r5-draftspec]|`fhir_r5.lbs`|`fhir_r5`|`R5'|

They all need the base library `fhir.lbs` to be present.

## Regenerating Libraries from Specs

You must duplicate the `fhir_release_template.lbs` and give it the default name according to the table above. Load the `fhir_tools.lbs` and instantiate the `FHIRMenu`. Select the `FHIRGenerator`->`Process Definitions...` command. This needs the folder with the spec definitions `profiles-types.json` and will then generate all classes.

[fhir-spec]: http://www.hl7.org/fhir
[dstu2-spec]: http://hl7.org/fhir/DSTU2/index.html
[stu3-spec]: http://www.hl7.org/fhir
[r4-spec]: http://hl7.org/fhir/R4/index.html
[r5-draftspec]: http://hl7.org/fhir/2020Feb/index.html
[fhirpath-spec]: http://hl7.org/fhirpath/

[omnis-studio]: http://www.omnis.net
