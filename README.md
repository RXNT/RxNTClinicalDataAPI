<div id="top"></div>
## RxNT Clinical Data API - Version 1.0

This documentation provides instructions to access RxNT Clinical Data API (RxNT CDAPI). RxNT CDAPI provides access to patients data as part of certification criteria outlined by the Office of the National Coordinator for Health Information Technology (ONC).

This document should be used by third parties as a reference to access patient clinical data. This document serves to fulfill the following criterion outlined by ONC.

- <a href="#patSel">Application Access – Patient Selection - 45 CFR 170.315(g)(7)</a>
- <a href="#dataCat"> Application Access – Data Category Request - 45 CFR 170.315(g)(8) </a>
- <a href="#allData">Application Access – All Data Request - 45 CFR 170.315(g)(9) </a>

<div id="patSel"></div>
### Application Access – Patient Selection - 45 CFR 170.315(g)(7)

In compliance to 45 CFR 170.315(g)(7), RxNT CDAPI provides an API that receives a request from another software component/service with identifying information about a patient and returns a unique token specific to that patient.

In order to access patient information, any third party application/ patient representative should first be registered to RxNT. For registration, clients need to contact RxNT at **support@rxnt.com** with their information. Upon verification, RxNT will provide appropriate login information to the external clients and this API authenticates those third party clients.

RxNT performs checks to ensure that the third party has the correct login credentials and access to the patient information. If the third party is authorized to access patient data, RxNT authenticates the third party and returns a limited time token which should be used for subsequent API calls.

<div id="request"> </div>

The request body should contain the following login information provided to them by RxNT.

```
{
 “UserName” : “demouser”,
 “Password”: “demoPassword”
}
```

**Sample Request:**

Method: **POST**  
 URL:`https://app2.rxnt.com/MasterIndexExternalAPIServices/masterindexexternalapi/v1/authentication/AuthenticateUser`

```
Headers:
{
  “Content-Type”: “application/json”
}
```

```
Body:
{
  “Username” : “demouser”,
  “Password”: “demoPassword”
}
```

**Parameters:**

- UserName
- Password

Both the above parameters are required. They should be passed as JSON in the body of the request.

**Sample Response:**

```
{
  "AppLoginId": "loginId",
  "DoctorCompanyId": "DoctorCompanyId",
  "TokenExpiryDate": "TokenExpiryDate",
  "Token": "Token",
  "Signature": "Signature",
  "NoOfDaysToExpire": "NumberOfDaysToExpire",
  "ValidationMessages": null,
  "ValidationStatus": "Success",
  "Meta": null
}
```

The response is in JSON. It returns values for each of the keys in the sample response above.

In order to access RxNT CDAPI, developers/ third parties should have access to RxNT ExternalReferencePatientId, which is available to registered patients in their PHR. Third parties should call our API with the ExternalPatientId in order to access patient clinical information. RxNT uses patient external id as a primary key to return patient clinical data to registered third party clients.

We have created an API that checks to see if the ExternalPatientReferenceId exists, so that third parties can make sure they have the correct ExternalReferencePatientId exists before calling the API for data access.

**Sample Request**

Method: **POST**  
 URL:`https://app2.rxnt.com/MasterIndexExternalAPIServices/masterindexexternalapi/v1/patientdashboard/patientccd/GetV1PatientInfoByExternalPatientId`

```
Headers:
{
  “Content-Type”: “application/json”
}
```

```
Body:
{
  "DoctorCompanyId": “DoctorCompanyId”,
  "Signature": “Signature”,
  "Token": “Token”,
  "RequestCompanyId": “RequestCompanyId”,
  "ExternalReferencePatientId": "ExternalReferencePatientId"
}
```

**Parameters**

- DoctorCompanyId
- Signature
- Token
- RequestCompanyId
- ExternalReferencePatientId

All the above parameters are required. They should passed as JSON in the body of the request.

**Sample Response**

The response is in JSON an it returns values for ExternalReferencePatientId, ValidationMessages, ValidationStatus and Meta.

**On Success:**

```
{
  "ExternalReferencePatientId": "ExternalReferencePatientId",
  "ValidationMessages": null,
  "ValidationStatus": "Success",
  "Meta": null
}
```

**On Failure:**

```
{
  "ExternalReferencePatientId": null,
  "ValidationMessages": [
      "Patient doesn't exists with the External Reference Patient Id: xxxx"
  ],
  "ValidationStatus": "Failed",
  "Meta": null
}
```

<a href="#top">Click here to Navigate to Top</a>

<div id="dataCat"></div>  
### Application Access – Data Category Request - 45 CFR 170.315(g)(8)
  
  In compliance to 45 CFR 170.315(g)(8), RxNT CDAPI provides access to patient clinical data based on different CCDS data category .

This API responds to requests for patient data for each of the individual data categories specified in the Common Clinical Data Set and return the full set of data for that data category. If an already authenticated user, sends a post request to access a patient’s health information, the API authenticates the user, analyzes the request and returns appropriate response. The API also responds to requests for patient data associated with a specific date as well as requests for patient data within a specified date range.

The API returns patient data on these different categories:

- Patient Name
- Sex
- Date of Birth
- Race
- Ethnicity
- Preferred Language
- Smoking Status
- Problems
- Medications
- Medication Allergies
- Laboratory Test(s)
- Laboratory Value(s)/Result(s)
- Vital Signs
- Procedures
- Care Team Member(s)
- Immunizations
- Unique Device Identifier(s) for a Patient's Implantable Device(s)
- Assessment and Plan of Treatment
- Goals
- Health Concerns

In order to access patient data for specific category, categories should be passed as an array of string in the body of the request. The category string should follow the exact same formatting as in the list above. Detailed description of each of the fields in the list can be found at [2015 Edition Common Clinical Data Set - 45 CFR 170.102](https://www.healthit.gov/sites/default/files/2015Ed_CCG_CCDS.pdf).

The sample request is shown below:

Method: **POST**
URL:`https://app2.rxnt.com/MasterIndexExternalAPIServices/masterindexexternalapi/v1/patientdashboard/patientccd/GetV1PatientInfoByExternalPatientId`

**Sample Request**

```
Headers:
{
  “Content-Type”: “application/json”
}
```

```
Body:
{
  "DoctorCompanyId": “DoctorCompanyId”,
  "Signature": “Signature”,
  "Token": “Token”,
  "RequestCompanyId": “RequestCompanyId”,
  "ExternalReferencePatientId": "ExternalReferencePatientID",
  "Categories": ["Vital Signs", "Smoking Status"],
  "FromDate": "2017/01/01",
  "ToDate": "2017/12/31"
}
```

**Parameters:**

- DoctorCompanyId
- Signature
- Token
- RequestCompanyId
- ExternalReferencePatientId
- Categories
- FromDate
- ToDate

All the above parameters, apart from FromDate and ToDate are required. FromDate and ToDate are optional but you should either have both of them or none of them. The category array should not be empty; it should contain at least one of the categories.

**Sample Response:**

The response is in JSON and it returns values for PatientCCDSXml, ValidationMessages, ValidationStatus, Meta.

```
{
  "PatientCCDSXml": “Patient Data in XML”
  "ValidationMessages": null,
  "ValidationStatus": "Success",
  "Meta": null
}
```

In order to get patient data for a specific date, fields FromDate and ToDate should be same.

<a href="#top">Click here to Navigate to Top</a>

<div id="allData"> </div>  
### Application Access – All Data Request - 45 CFR 170.315(g)(9)
  
  RxNT CDAPI provides access to patient clinical data, in compliance to 45 CFR 170.315(g)(9). This API respond to requests for patient data for all of the data categories specified in the Common Clinical Data. The API also responds to  requests for patient data associated with a specific date as well as requests for patient data within a specified date range.

The sample request is shown below:

Method: POST
URL:`https://app2.rxnt.com/MasterIndexExternalAPIServices/masterindexexternalapi/v1/patientdashboard/patientccd/GetPatientCCDSData`

**Sample Request (All Data)**

Headers:

```
{
  “Content-Type”: “application/json”
}
```

```
Body:
{
  "DoctorCompanyId": “DoctorCompanyId”,
  "Signature": “Signature”,
  "Token": “Token”,
  "RequestCompanyId": “RequestCompanyId”,
  "ExternalReferencePatientId": "ExternalReferencePatientId",
  "FromDate": "2017/01/01",
  "ToDate": "2017/12/31"
}
```

**Parameters:**

- DoctorCompanyId
- Signature
- Token
- RequestCompanyId
- ExternalReferencePatientId
- FromDate
- ToDate

  All the above parameters, apart from FromDate and ToDate are required. FromDate and ToDate are optional but you should either have both of them or none of them.

**Sample Response**

The response is in JSON and it returns values for PatientCCDSXml, ValidationMessages, ValidationStatus, Meta.

```
{
  "PatientCCDSXml": “CCDS XML”,
  "ValidationMessages": null,
  "ValidationStatus": "Success",
  "Meta": null
}
```

The CCDS XML is returned as a value for the key PatientCCDSXml in the JSON response. It can be used by third party clients as per their needs.

In order to get patient data for a specific date, fields FromDate and ToDate should be same.

<a href="#top">Click here to Navigate to Top</a>

### Date Criteria filter for g(8), g(9) and VDT

Date is filtered based on the given FromDate and ToDate value and it must follow the following guidelines:

- “Start Date” should be less than or equal to “To Date”
- If “End Date” Exists, “From Date” should be less than or equal to “End Date”

The following section contains some scenarios that explain date filtering.

| Problem                               | Start Date | End Date |
| ------------------------------------- | ---------- | -------- |
| Essential hypertension                | 10/5/2011  |          |
| Severe hypothyroidism                 | 12/31/2006 |          |
| Chronic rejection of renal transplant | 12/31/2011 |          |
| Fever, unspecified fever cause        | 6/22/2015  |          |
| Overweight                            | 12/31/2006 | 6/1/2007 |

The above table is the master table used for the queries in the scenarios below:

**Scenario 1:**

- From Date = “01/01/2005”
- To Date = “12/31/2005”

No records will be listed
<br>
**Scenario 2:**

- From Date = “01/01/2005”
- To Date = “12/31/2006”

| Problem               | Start Date | End Date |
| --------------------- | ---------- | -------- |
| Severe hypothyroidism | 12/31/2006 |          |
| Overweight            | 12/31/2006 | 6/1/2007 |

<br>
**Scenario 3:**
  - From Date = “01/01/2007”
  - To Date = “12/31/2010”
  
   |Problem| Start Date|End Date|
  |  --- | --- | --- | 
  |Severe hypothyroidism| 12/31/2006 |  |
  |Overweight| 12/31/2006 | 6/1/2007 |

<br>
**Scenario 4:**
  - From Date = “01/01/2007”
  - To Date = “12/31/2011”
  
   
  |Problem| Start Date|End Date|
  |  --- | --- | --- | 
  |Essential hypertension| 10/5/2011 |  |
  |Severe hypothyroidism| 12/31/2006 |  |
  |Chronic rejection of renal transplant| 12/31/2011 |  |
  |Overweight| 12/31/2006 | 6/1/2007 |

<br>
**Scenario 5:**
  - From Date = “01/01/2007”
  - To Date = “12/31/2015”
  
  |Problem| Start Date|End Date|
  |  --- | --- | --- | 
  |Essential hypertension| 10/5/2011 |  |
  |Severe hypothyroidism| 12/31/2006 |  |
  |Chronic rejection of renal transplant| 12/31/2011 |  |
  |Fever, unspecified fever cause| 6/22/2015 | |
  |Overweight| 12/31/2006 | 6/1/2007 |

<br>
**Scenario 6:**
  - From Date = “01/01/2008”
  - To Date = “12/31/2011”
  
   |Problem| Start Date|End Date|
  |  --- | --- | --- | 
  |Essential hypertension| 10/5/2011 |  |
  |Severe hypothyroidism| 12/31/2006 |  |
  |Chronic rejection of renal transplant| 12/31/2011 |  |

<br>
**Scenario 7:**
  - From Date = “01/01/2015”
  - To Date = “12/31/2017”

| Problem                               | Start Date | End Date |
| ------------------------------------- | ---------- | -------- |
| Essential hypertension                | 10/5/2011  |          |
| Severe hypothyroidism                 | 12/31/2006 |          |
| Chronic rejection of renal transplant | 12/31/2011 |          |
| Fever, unspecified fever cause        | 6/22/2015  |          |

<br>
**Scenario 8:**
  - From Date = “12/31/2006”
  - To Date = “12/31/2006”
  
  |Problem| Start Date|End Date|
  |  --- | --- | --- | 
  |Severe hypothyroidism| 12/31/2006 |  |
  |Overweight| 12/31/2006 | 6/1/2007 |
  
<br>
**Scenario 9:**
  - From Date = “01/01/2015”
  - To Date = “01/01/2015”

| Problem                               | Start Date | End Date |
| ------------------------------------- | ---------- | -------- |
| Essential hypertension                | 10/5/2011  |          |
| Severe hypothyroidism                 | 12/31/2006 |          |
| Chronic rejection of renal transplant | 12/31/2011 |          |

<br>
**Scenario 10:**
  - From Date = “06/01/2007”
  - To Date = “06/01/2007”

| Problem               | Start Date | End Date |
| --------------------- | ---------- | -------- |
| Severe hypothyroidism | 12/31/2006 |          |
| Overweight            | 12/31/2006 | 6/1/2007 |

<br>
**Scenario 11:**
  - From Date = “06/02/2007”
  - To Date = “06/02/2007”
  
  |Problem| Start Date|End Date|
  |  --- | --- | --- | 
  |Severe hypothyroidism| 12/31/2006 |  |

<br>
<a href="#top">Click here to Navigate to Top</a>
### [Click Here to View RxNTs Terms and Conditions](https://rxnt.github.io/RxNTClinicalDataAPI/terms)
