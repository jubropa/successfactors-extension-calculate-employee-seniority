## Customize the app and services

In this mission, we enriched the original Calculate Employee Seniority use case with a remote service to SAP SuccessFactors. This allows us to retrieve potentially sensitive HR data, such as employee full names and pictures, on demand in SAP AppGyver. We blend data between SAP SuccessFactors and SAP BTP persistence into a view that is exposed over OData for app consumption. Below, you will find a summary of the new artifacts created for the mission. Some of these can be optimized, and we'll continue working to improve this technology pattern. We also welcome your pull requests and issues in the usual fashion.

1. 2 files have been added to srv/external
    - PhotoUser.edmx was created for ```cds import --as cds``` from the URL:

      https://apisalesdemo4.successfactors.com:443/odata/v2/Photo,User,EmpEmployment/$metadata

      Thanks to the [reference from SAP support](https://userapps.support.sap.com/sap/support/knowledge/en/2724321)

    - PhotoUser.csn was created using the above command and additional "on" condition:

      ```"on": [ { "ref": ["userNav", "userId"] }, "=", { "ref": ["$self", "userId"] } ]```

1. The new model, ```PhotoUser``` was added to [package.json](https://github.tools.sap/btp-use-case-factory/successfactors-appgyver-seniority-calc/blob/dev-termination-date/package.json) and set to the ```seniority-calc-sfsf-service``` destination

1. Redefined ```originalStartDate```, ```hireDate```, and ```terminationDate``` as **Timestamp** to accommodate SAP AppGyver OData requirements in [schema.cds](https://github.tools.sap/btp-use-case-factory/successfactors-appgyver-seniority-calc/blob/dev-termination-date/db/schema.cds), and annotated the GUID with 

1. Added a [srv/server.js](https://github.tools.sap/btp-use-case-factory/successfactors-appgyver-seniority-calc/blob/dev-termination-date/srv/server.js) to address CORS for SAP AppGyver with commented locked down version

1. Enhanced [request.http](https://github.tools.sap/btp-use-case-factory/successfactors-appgyver-seniority-calc/blob/dev-termination-date/srv/request.http) with sample requests

1. Added remote entities and mixin view for consumption by SAP AppGyver in [emp-service.cds](https://github.tools.sap/btp-use-case-factory/successfactors-appgyver-seniority-calc/blob/dev-termination-date/srv/emp-service.cds) and annotated ID with ```@odata.Type:'Edm.String'```

1. Significant additions to service implementation [emp-service.js](https://github.tools.sap/btp-use-case-factory/successfactors-appgyver-seniority-calc/blob/dev-termination-date/srv/emp-service.js) for the following:
    - Add exception criteria to calcSeniorityTotalDays for rehires where the previous termination event doesn't exist in SAP BTP persistence
    - Duplicate calcSeniorityTotalDays into calcSeniorityTotalDaysException for use in exception handling
    - Event handler for READ of ProfileMixin view
    - Event handler for UPDATE of Employee entity

You can customize the service implementation further to handle your own scenario. Thanks for trying out our work!