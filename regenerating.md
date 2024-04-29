# Regenerating Libraries from Specs

(Only for the brave, and do not forget to backup your previous FHIR VErsion Library)

## Download JSON specification files

Navigate to the [Implementation Support/Downloads page](https://www.hl7.org/fhir/downloads.html), switch in the heading
to the release you are interested in. Download the FHIR Definitions in JSON Format

## Prepare Omnis FHIR Version Library

Load the `FHIR.LBS` base library.

Duplicate the `FHIR_RELEASE_TEMPLATE.LBS` in the Finder/Explorer and give it the default name according to the 
table [Versions](README.md#supported-specs-). Load it into Omnis and change the _name_ and _defaultname_ in the property manager to the same
name (Lowercase) as well.

Load the `FHIR_TOOLS.LBS` and instantiate the `FHIRMenu` from within the Tools folder. Select the `FHIRGenerator`->`Process Definitions...` command. 
This needs the folder with the spec definitions `profiles-types.json` and will then generate all classes.

Select *Process Definitions...* from the _FHIRGenerator_ Menu and Select the folder with the files you downloaded above. 
