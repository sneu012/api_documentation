## RxNT Clinical Data API 

This documentation provides instruction to access RxNT Clinical Data API (RxNT CDAPI). RxNT CDAPI provides access to patients data as part of certification criteria outlined by the Office of the National Coordinator for Health Information Technology (ONC).

  This document serves to fulfill the following criterion outlined by ONC.
- <a href="#patSel">Application Access – Patient Selection - 45 CFR 170.315(g)(7)</a>
- <a href="#dataCat"> Application Access – Data Category Request - 45 CFR 170.315(g)(8) </a>
- <a href="#allData">Application Access – All Data Request - 45 CFR 170.315(g)(9) </a>

<div id="patSel"></div>
### Application Access – Patient Selection - 45 CFR 170.315(g)(7)

  In order to access patient information, any third party application/ patient representative should first be registered to RxNT. For registration, clients need to contact RxNT at **support@rxnt.com** with their information. Upon verification, RxNT will provide appropriate login information to the external parties.

  In order to use RxNT CDAPI, developers/ third parties should have access to RxNT ExternalPatientID, which is available to patients in their PHR. Third parties should call the API with the ExternalPatientID in order to access patient clinical information. RxNT uses patient external id as a primary key to return patient clinical data to registered clients. 

  RxNT performs check to ensure that the third party has the correct login credentials and access to the patient information through external patient id. If the third party is authorized to access patient data, RxNT authenticates the third party and returns a limited time token which should be used for subsequent API calls. 
<div id="request"> </div>
The request body should contain the login information provided to the third party clients : 
  ```
  { 
   “UserName” : “demouser”,
   “Password”: “demoPassword”
  }
  ```
  
Sample Request:

  Method: **POST**              
  URL:`https://www.rxnt.com/MasterIndexExternalAPIServices/masterindexexternalapi/v1/authentication/AuthenticateUser`
  
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

Parameters:
- UserName
- Password

Sample Response:
  ```
  {
    "AppLoginId": "loginId",
    "DoctorCompanyId": "DoctorCompanyId",
    "TokenExpiryDate": "2017-12-01T14:40:03.847Z",
    "Token": "Token",
    "Signature": "xxxx+B1XiLQgBLU",
    "NoOfDaysToExpire": 300,
    "ValidationMessages": null,
    "ValidationStatus": "Success",
    "Meta": null
  }
  ```
  
  Third parties need to get External Reference Patient ID from patients in order to access their clinical data. We have created an API that checks to see if the External Patient Reference ID exists, so that third parties can make sure they have the correct External Patient Id exists before calling the API for data access.

Sample Request 

  Method: **POST**  
  URL:`https://devqa.rxnt.com/MasterIndexExternalAPIServices/masterindexexternalapi/v1/patientdashboard/patientccd/GetV1PatientInfoByExternalPatientId`
  ```
  Headers:
  {
    “Content-Type”: “application/json”
  }
  ```
  ```
  Body: 
  {
    "DoctorCompanyId": “xxxx”,
    "Signature": “xxxx”,
    "Token": “xxxx”},
    "RequestCompanyId": “xxxx”,
    "ExternalReferencePatientId": "External Reference Patient Id"
  }
  ```

Sample Response

  On Success:
  ```
  {
    "ExternalReferencePatientId": "External Reference Patient Id",
    "ValidationMessages": null,
    "ValidationStatus": "Success", 
    "Meta": null
  }
  ```

On Failure:
  
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
<div id="dataCat"></div>  
### Application Access – Data Category Request - 45 CFR 170.315(g)(8) 
  
  In compliance to 45 CFR 170.315(g)(8), RxNT CDAPI provides access to patient clinical data based on different CCDS data category .

This API responds to requests for patient data for each of the individual data categories specified in the Common Clinical Data Set and return the full set of data for that data category. If an already authenticated user, sends a post request to access a patient’s health information, the API authenticates the user, analyzes the request and returns appropriate response. The API also responds to requests for patient data associated with a specific date as well as requests for patient data within a specified date range

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
  - Laboratory Tests
  - Laboratory Values(s)/Result(s)
  - Vital Signs
  - Procedures
  - Care Team Member(s)
  - Immunizations
  - Unique Device Identifier(s) for a Patient’s Implantable Device(s)
  - Assessment and Plan of Treatment
  - Goals
  - Health Concerns
  
  In order to access patient data for specific category, the categories should be passed as an array of string in the body of the request. The sample request is shown below: 

Sample Request:

  Method: **POST** 
 URL:`https://www.rxnt.com/MasterIndexExternalAPIServices/masterindexexternalapi/v1/patientdashboard/patientccd/GetV1PatientInfoByExternalPatientId`

Sample Request 
  ```
  Headers:
  {
    “Content-Type”: “application/json”
  }
  ```
  
  ```
  Body:
  {
    "DoctorCompanyId": “xxxx”,
    "Signature": “xxxx”,
    "Token": “xxxx”,
    "RequestCompanyId": “xxxx”,
    "ExternalReferencePatientId": "External Reference Patient ID",
    "Categories": ["Vital Signs", "Smoking Status"],
    "FromDate": "2017/01/01",
    "ToDate": "2017/12/31"
  }
  ```

Parameters:
  - DoctorCompanyId
  - Signature
  - Token
  - RequestCompanyId
  - ExternalReferencePatientId
  - Categories
  - FromDate
  - ToDate

Sample Response: 
  ```
  {
    "PatientCCDSXml": “Patient Data in XML”
    "ValidationMessages": null,
    "ValidationStatus": "Success",
    "Meta": null	
  }
  ```
<div id="allData"> </div>  
### Application Access – All Data Request - 45 CFR 170.315(g)(9)
  
  RxNT CDAPI provides access to patient clinical data, in compliance to 45 CFR 170.315(g)(9). This API respond to requests for patient data for all of the data categories specified in the Common Clinical Data. The API also responds to  requests for patient data associated with a specific date as well as requests for patient data within a specified date range.


The sample request is shown below:

  Method: POST
 URL:`https://www.rxnt.com/MasterIndexExternalAPIServices/masterindexexternalapi/v1/patientdashboard/patientccd/GetPatientCCDSData`

Sample Response (All Data)

Headers:
  ```
  {
    “Content-Type”: “application/json”
  }
  ```
  ```
  Body:
  {
    "DoctorCompanyId": “xxxx”,
    "Signature": “xxxx”,
    "Token": “xxxx”,
    "RequestCompanyId": “xxxx”,
    "ExternalReferencePatientId": "External Reference Patient ID",
    "FromDate": "2017/01/01",
    "ToDate": "2017/12/31"
  }
  ```
  
Parameters:
  - DoctorCompanyId
  - Signature
  - Token
  - RequestCompanyId
  - ExternalReferencePatientId
  - FromDate
  - ToDate

Sample Response
  ```
  {
    "PatientCCDSXml": “CCDS XML”,
    "ValidationMessages": null,
    "ValidationStatus": "Success",
    "Meta": null
  }
  ```
  
  ###[CLick Here to View RxNTs Privacy Policy](https://www.rxnt.com/privacy-policy/)
