# Omnis FHIR
This is a collection of Open Source [Omnis][omnis-studio] Libraries to work with the [HL7 FHIR][fhir-spec] Standard.

## Requirements ##
For the Client Libraries Omnis Studio 8.1.7 is required, for the Server Libraries Version Omnis Studio 10.1 is the lowest version having the necessary functionality.

## Supported Specs ##

The following versions of the specifications are supported in respectively named libraries
|Spec version|Library file name|Library default name|Name for Context construction
|---|---|---|---|
|[STU3][stu3-spec]|`fhir_stu3.lbs`|`fhir_stu3`|`STU3`|
|[R4][r4-spec]|`fhir_r4.lbs`|`fhir_r4`|`R4`|
|[R5 Draft][r5-draftspec]|`fhir_r5.lbs`|`fhir_r5`|`R5`|

They all need the base library `fhir.lbs` to be present.

## Regenerating Libraries from Specs

You must duplicate the `fhir_release_template.lbs` and give it the default name according to the table above. Load the `fhir_tools.lbs` and instantiate the `FHIRMenu`. Select the `FHIRGenerator`->`Process Definitions...` command. This needs the folder with the spec definitions `profiles-types.json` and will then generate all classes.

## Client Endpoints

Client endpoints are subclasses of `FHIRClientEndpoint`. 

### Properties

Before being ready they need the following properties set

| Property  | Content  | Example  |
|-----------|--------|----------|
| `$bridge`   | instance of subclass of `FHIRBridge` | `Calculate $cinst.$bridge as $classes.oMCFHIRBridge.$newref()` |
| `$baseUrl`    |  the base URL of the REST endpoint | `Calculate $cinst.$baseURL as 'http://localhost:8080/hapi/fhir/' `|
| `$context` | the FHIR release this endpoint is supporting | `Calculate $cinst.$context as 'r4' `

### Security

There are several different methods that get used for authorization, right now we only support authorization methods that can implemented using HTTP headers.

#### Bearer Token Authorization

The FHIRClientEndpoint has a `$addHeader` method which can be used to add bearer token, e.g.

`Do $cinst.$addHeader('Authorization',con('Bearer ','7bada06393f677d72fee0b93c07ee807')`

### Posting a resource

The `FHIRClientEndpoint` has a low-level `$POSTResource` method

| Parameter  | Value  | Example  |
|-----------|--------|----------|
| `pRelativeURL`   | part of the URL that gets added to the baseURL | `'Patient'` |
| `pResource`    | the FHIR Resource to post | |
| `pFormat` | the format | `'json'` (default)

For frequently used Resources there are convenience methods available eg `$POSTPatient` that only need `pResource` and (optionally) `pFormat` 

There are methods using 'native' Resources, eg `$POSTNativePatient` taking a Patient row specific to the host application that then get translated to its FHIR counterbart using the `$bridge$`

## FHIRBridge

The individual application needs to implement its own subclass to make things work. For translating the native patient row mentioned above the two methods `$patientToResource` and `$patientFromResource` are needed.

### $patientToResource

| Parameter  | Value |
|-----------|--------|
| `pContext`   | the FHIR Release Context in which this conversion takes place |
| `pNativePatient`    | item reference to the native row |
| `pFHIRPatient` | the empty patient resource object reference that has to be filled in | 

Sample Code

```
		Do code method fhir_r4.csGenderIdentity/$female Returns pFHIRPatient.$gender
	Else
		Do code method fhir_r4.csGenderIdentity/$unknown Returns pFHIRPatient.$gender
	End If
	;  adress
	Calculate address as pFHIRPatient.$addaddress()
	Calculate address.$use as 'home'
	If pNativePatient.PATIENT_CO<>''
		Do address.$addline(pNativePatient.PATIENT_CO)
	End If
	Do address.$addline(pNativePatient.PATIENT_STREET)
	Calculate address.$city as pNativePatient.PATIENT_CITY
	Calculate address.$postalCode as pNativePatient.PATIENT_ZIP
	Calculate address.$country as 'CH'
	;  telecom
	If pNativePatient.PATIENT_MOBILE<>''
		Calculate telecom as pFHIRPatient.$addtelecom()
		Calculate telecom.$use as 'mobile'
		Calculate telecom.$system as 'phone'
		Calculate telecom.$value as pNativePatient.PATIENT_MOBILE
	End If
	If pNativePatient.PATIENT_TEL<>''
		Calculate telecom as pFHIRPatient.$addtelecom()
		Calculate telecom.$use as 'home'
		Calculate telecom.$system as 'phone'
		Calculate telecom.$value as pNativePatient.PATIENT_PHONE
	End If
	If pNativePatient.PATIENT_TEL2<>''
		Calculate telecom as pFHIRPatient.$addtelecom()
		Calculate telecom.$use as 'home'
		Calculate telecom.$system as 'phone'
		Calculate telecom.$value as pNativePatient.PATIENT_PHONE2
	End If
	If pNativePatient.PATIENT_TELG<>''
		Calculate telecom as pFHIRPatient.$addtelecom()
		Calculate telecom.$use as 'work'
		Calculate telecom.$system as 'phone'
		Calculate telecom.$value as pNativePatient.PATIENT_WORK
	End If
	If pNativePatient.PATIENT_EMAIL<>''
		Calculate telecom as pFHIRPatient.$addtelecom()
		Calculate telecom.$use as 'mobile'
		Calculate telecom.$system as 'email'
		Calculate telecom.$value as pNativePatient.PATIENT_EMAIL
	End If
End If
```

### $patientFromResource

| Parameter  | Value |
|-----------|--------|
| `pContext`   | the FHIR Release Context in which this conversion takes place |
| `pFHIRPatient` | the patient resource object reference containing the data | 
| `pNativePatient`    | item reference to the native row that needs to be filled in |

Sample Code

```
Calculate pFHIRPatient.$identifier() as pNativePatient.PATIENT_SEQ
Calculate name as pFHIRPatient.$::name()
Calculate pNativePatient.PATIENT_NAME as con(name.$family,",")
Calculate given as name.$given()
For given.$line from 1 to given.$linecount step 1
	Calculate pNativePatient.PATIENT_NAME as con(pNativePatient.PATIENT_NAME," ",given.C1)
End For
Do code method fhir_r4.csGenderIdentity/$male Returns gender
If pFHIRPatient.$gender=gender
	Calculate pNativePatient.PATIENT_SEX as "M"
Else
	Do code method fhir_r4.csGenderIdentity/$female Returns gender
	If pFHIRPatient.$gender=gender
		Calculate pNativePatient.PATIENT_SEX as "F"
	Else
		Do code method fhir_r4.csGenderIdentity/$unknown Returns gender
		If pFHIRPatient.$gender=gender
			Calculate pNativePatient.PATIENT_SEX as ""
		End If
	End If
End If
```

[fhir-spec]: http://www.hl7.org/fhir
[dstu2-spec]: http://hl7.org/fhir/DSTU2/index.html
[stu3-spec]: http://www.hl7.org/fhir
[r4-spec]: http://hl7.org/fhir/R4/index.html
[r5-draftspec]: http://hl7.org/fhir/2020Feb/index.html
[fhirpath-spec]: http://hl7.org/fhirpath/

[omnis-studio]: http://www.omnis.net
