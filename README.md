## RxNT Clinical Data API 

  <a name="request">This</a> documentation provides instruction to access RxNT Clinical Data API (RxNT CDAPI). RxNT CDAPI provides access to patients data as part of certification criteria outlined by the Office of the National Coordinator for Health Information Technology (ONC).

  This document serves to fulfill the following criterion outlined by ONC.
- <a name="patSel">Patient</a>
- <a name="dataCat"> Application Access – Data Category Request - 45 CFR 170.315(g)(8) </a>
- <a name="allData">Application Access – All Data Request - 45 CFR 170.315(g)(9) </a>

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

  Method: POST              
  URL:https://www.rxnt.com/MasterIndexExternalAPIServices/masterindexexternalapi/v1/authentication/AuthenticateUser 
  
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
    "DoctorCompanyId": DoctorCompanyId,
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

  Method: POST  
  URL:https://devqa.rxnt.com/MasterIndexExternalAPIServices/masterindexexternalapi/v1/patientdashboard/patientccd/GetV1PatientInfoByExternalPatientId
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
