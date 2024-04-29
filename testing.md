# Testing Setup ##

To test client and server functionality the easiest thing is to setup a [HAPI server](https://github.com/hapifhir/hapi-fhir-jpaserver-starter) on your development machine.

I've changed in `src/main/resources/hapi.properties` one property `server_address=http://localhost:8080/hapi/fhir/` and I've added the Omnis Server for testing in `src/main/java/ca/uhn/fhir/jpa/starter/FhirTesterConfig.java` as follows

```
public TesterConfig testerConfig() {
   (...)
     retVal
       .addServer()
       .withId("oFHIR")
       .withFhirVersion(FhirVersionEnum.R4)
       .withBaseUrl("http://localhost:9090/api/mcfhirserver/fhir/base")
       .withName("Local Omnis Tester");
     retVal.setRefuseToFetchThirdPartyUrls(true);

```
