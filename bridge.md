# FHIRBridge

The individual application needs to implement its own subclass to make things work. For translating the native patient row mentioned above the two methods `$patientToResource` and `$patientFromResource` are needed.

## $patientToResource

| Parameter  | Value |
|-----------|--------|
| `pContext`   | the FHIR Release Context in which this conversion takes place |
| `pNativePatient`    | item reference to the native row |
| `pFHIRPatient` | the empty patient resource object reference that has to be filled in | 

Sample Code

```
Calculate identifier as pFHIRPatient.$addidentifier()
Calculate identifier.$use as 'usual'
Calculate identifier.$system as con("http://www.medicalconcept.com/",tApp.$getserialid())
Calculate identifier.$value as pNativePatient.PATIENT_SEQ
Calculate codeableConcept as identifier.$::type()
Calculate coding as codeableConcept.$addcoding()
Calculate coding.$system as "http://terminology.hl7.org/CodeSystem/v2-0203"
Calculate coding.$code as "MR"
;  name
Calculate name as pFHIRPatient.$addname()
Calculate name.$use as 'official'
Calculate name.$family as pNativePatient.$lastname()
Do name.$addGiven(pNativePatient.$firstname())
;  birth
Calculate pFHIRPatient.$birthDate as pNativePatient.PATIENT_GEBURT
;  gender
If pNativePatient.PATIENT_SEX="M"
	Do code method fhir_r4.csGenderIdentity/$male Returns pFHIRPatient.$gender
Else If pNativePatient.PATIENT_SEX="F"
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
If pNativePatient.PATIENT_PHONE<>''
	Calculate telecom as pFHIRPatient.$addtelecom()
	Calculate telecom.$use as 'home'
	Calculate telecom.$system as 'phone'
	Calculate telecom.$value as pNativePatient.PATIENT_PHONE
End If
If pNativePatient.PATIENT_PHONE2<>''
	Calculate telecom as pFHIRPatient.$addtelecom()
	Calculate telecom.$use as 'home'
	Calculate telecom.$system as 'phone'
	Calculate telecom.$value as pNativePatient.PATIENT_PHONE2
End If
If pNativePatient.PATIENT_WORK<>''
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
```

## $patientFromResource

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
