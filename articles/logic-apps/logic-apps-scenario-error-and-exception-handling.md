---
title: Scenario voor het afhandelen van uitzonderingen & foutlogboekregistratie
description: Echte use case en scenario voor geavanceerde exception handling en foutlogging in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: 1bb6e28c9dcae01f3233178706d2a24156fa509a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76902697"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Scenario: Verwerking van uitzonderingen en foutlogboekregistratie voor logische apps

In dit scenario wordt beschreven hoe u een logische app uitbreiden om de afhandeling van uitzonderingen beter te ondersteunen. We hebben een echte use case gebruikt om de vraag te beantwoorden: "Ondersteunt Azure Logic Apps uitzondering en foutafhandeling?"

> [!NOTE]
> Het huidige Azure Logic Apps-schema biedt een standaardsjabloon voor actiereacties. Deze sjabloon bevat zowel interne validatie- als foutreacties die zijn geretourneerd vanuit een API-app.

## <a name="scenario-and-use-case-overview"></a>Overzicht van scenario' s en gebruikshoofdletters

Hier is het verhaal als de use case voor dit scenario: 

Een bekende zorgorganisatie heeft ons ingeschakeld om een Azure-oplossing te ontwikkelen die een patiëntenportal zou maken met behulp van Microsoft Dynamics CRM Online. Ze moesten afspraakgegevens versturen tussen de Dynamics CRM Online-patiëntenportal en Salesforce. We werden gevraagd om de [HL7 FHIR-standaard](https://www.hl7.org/implement/standards/fhir/) te gebruiken voor alle patiëntendossiers.

Het project had twee belangrijke eisen:  

* Een methode om records te registreren die zijn verzonden vanuit de Dynamics CRM Online-portal
* Een manier om eventuele fouten in de werkstroom weer te geven

> [!TIP]
> Zie [Integration User Group](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Gebruikersgroep Integratie")voor een video op hoog niveau over dit project.

## <a name="how-we-solved-the-problem"></a>Hoe we het probleem hebben opgelost

We kozen azure [cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") als opslagplaats voor de log- en foutrecords (Cosmos DB verwijst naar records als documenten). Omdat Azure Logic Apps een standaardsjabloon heeft voor alle reacties, hoeven we geen aangepast schema te maken. We kunnen een API-app maken voor **Invoegen** en **Query voor** zowel fout- als logboekrecords. We kunnen ook een schema voor elk in de API-app definiëren.  

Een andere vereiste was om records te zuiveren na een bepaalde datum. Cosmos DB heeft een eigenschap genaamd [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Tijd om te leven") (TTL), waardoor we een Time to **Live-waarde** konden instellen voor elke record of verzameling. Deze mogelijkheid elimineerde de noodzaak om records handmatig te verwijderen in Cosmos DB.

> [!IMPORTANT]
> Als u deze zelfstudie wilt voltooien, moet u een Cosmos DB-database en twee verzamelingen (Logboekregistratie en fouten) maken.

## <a name="create-the-logic-app"></a>De logische app maken

De eerste stap is het maken van de logische app en het openen van de app in Logic App Designer. In dit voorbeeld gebruiken we logica-apps voor ouder-kind. Laten we aannemen dat we de bovenliggende ouder al hebben gemaakt en één onderliggende logica-app gaan maken.

Omdat we gaan om de record coming out van Dynamics CRM Online te registreren, laten we beginnen aan de top. We moeten een **trigger voor aanvragen** gebruiken omdat de bovenliggende logica-app dit onderliggende bericht activeert.

### <a name="logic-app-trigger"></a>Logische app-trigger

We gebruiken een **trigger voor aanvragen** zoals weergegeven in het volgende voorbeeld:

``` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

```


## <a name="steps"></a>Stappen

We moeten de bron (aanvraag) van het patiëntendossier registreren via de Dynamics CRM Online-portal.

1. We moeten een nieuw afsprakenrecord krijgen van Dynamics CRM Online.

   De trigger van CRM biedt ons het **CRM PatentId,** **recordtype,** **Nieuwe of Bijgewerkte Record** (nieuwe of update Booleaanse waarde) en **SalesforceId**. De **SalesforceId** kan nietig zijn omdat deze alleen wordt gebruikt voor een update.
   We krijgen de CRM-record met behulp van de CRM **PatientID** en het **recordtype.**

2. Vervolgens moeten we onze Azure Cosmos DB SQL API-app **InsertLogEntry-bewerking** toevoegen, zoals hier in Logic App Designer wordt weergegeven.

   **Logboekinvoer invoegen**

   ![Logboekinvoer invoegen](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Foutinvoer invoegen**

   ![Logboekinvoer invoegen](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Controleren op recordfout maken**

   ![Voorwaarde](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Broncode van logische app

> [!NOTE]
> De volgende voorbeelden zijn alleen monsters. Omdat deze zelfstudie is gebaseerd op een implementatie die nu in productie is, wordt in de waarde van een **bronknooppunt** mogelijk geen eigenschappen weergegeven die verband houden met het plannen van een afspraak.> 

### <a name="logging"></a>Logboekregistratie

In het volgende voorbeeld van logische app-code ziet u hoe u logboekregistratie verwerken.

#### <a name="log-entry"></a>Logboekvermelding

Hier is de logische app broncode voor het invoegen van een log entry.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Logboekaanvraag

Hier is het log request bericht geplaatst op de API app.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Logreactie

Hier is het logresponsebericht van de API-app.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Laten we nu eens kijken naar de stappen voor foutafhandeling.

### <a name="error-handling"></a>Foutafhandeling

In het volgende voorbeeld van logische app-code ziet u hoe u foutafhandeling implementeren.

#### <a name="create-error-record"></a>Foutrecord maken

Hier is de logische app broncode voor het maken van een foutrecord.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Fout invoegen in Cosmos DB--request

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Fout invoegen in Cosmos DB--response

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Reactie van Salesforce-fouten

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Het antwoord terugsturen naar de bovenliggende logica-app

Nadat u het antwoord hebt ontvangen, u het antwoord terugsturen naar de bovenliggende logica-app.

#### <a name="return-success-response-to-parent-logic-app"></a>Succesrespons retourneren in de bovenliggende logica-app

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Foutreactie retourneren naar bovenliggende logica-app

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB repository en portal

Onze oplossing heeft mogelijkheden toegevoegd met [Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db)

### <a name="error-management-portal"></a>Foutbeheerportal

Als u de fouten wilt bekijken, u een MVC-web-app maken om de foutrecords van Cosmos DB weer te geven. De **bewerkingen Lijst,** **Details,** **Bewerken**en **Verwijderen** zijn opgenomen in de huidige versie.

> [!NOTE]
> Bewerking bewerken: Cosmos DB vervangt het hele document. De records in de **weergave Lijst** en **Detail** zijn alleen voorbeelden. Het zijn geen patiëntendossiers.

Hier vindt u voorbeelden van onze MVC-appdetails die zijn gemaakt met de eerder beschreven aanpak.

#### <a name="error-management-list"></a>Lijst met foutbeheer
![Foutlijst](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Detailweergave foutbeheer
![Foutdetails](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Portal voor logboekbeheer

Om de logboeken te bekijken, hebben we ook een MVC-webapp gemaakt. Hier vindt u voorbeelden van onze MVC-appdetails die zijn gemaakt met de eerder beschreven aanpak.

#### <a name="sample-log-detail-view"></a>Voorbeelddetailweergave
![Detailweergave logboeken](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>API-app-gegevens

#### <a name="logic-apps-exception-management-api"></a>API voor uitzonderingsbeheer van Logische Apps

Onze open-source Azure Logic Apps exception management API-app biedt functionaliteit zoals hier beschreven - er zijn twee controllers:

* **ErrorController** voegt een foutrecord (document) in een Azure Cosmos DB-verzameling.
* **LogController (LogController)** Hiermee voegt u een logboekrecord (document) in een Azure Cosmos DB-verzameling in.

> [!TIP]
> Beide controllers `async Task<dynamic>` maken gebruik van bewerkingen, waardoor bewerkingen kunnen worden opgelost tijdens uitvoering, zodat we het Azure Cosmos DB-schema in de hoofdtekst van de bewerking kunnen maken. 
> 

Elk document in Azure Cosmos DB moet een unieke id hebben. We gebruiken `PatientId` en voegen een tijdstempel toe die wordt geconverteerd naar een Unix-tijdstempelwaarde (dubbel). We tlopen de waarde af om de fractionele waarde te verwijderen.

U de broncode van onze API voor foutbesturing bekijken van [GitHub.](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs)

We roepen de API aan vanuit een logische app met behulp van de volgende syntaxis:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

De expressie in het vorige codevoorbeeld controleert op de *Create_NewPatientRecord* status van **Mislukt**.

## <a name="summary"></a>Samenvatting

* U logboekregistratie en foutafhandeling eenvoudig implementeren in een logische app.
* U Azure Cosmos DB gebruiken als opslagplaats voor logboek- en foutrecords (documenten).
* U MVC gebruiken om een portal te maken om logboek- en foutrecords weer te geven.

### <a name="source-code"></a>Broncode

De broncode voor de API-toepassing voor het uitzonderingsbeheer van Logic Apps is beschikbaar in deze [GitHub-repository.](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Api voor uitzonderingsbeheer van logische apps")

## <a name="next-steps"></a>Volgende stappen

* [Voorbeelden en scenario's van logische apps weergeven](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Logische apps bewaken](../logic-apps/monitor-logic-apps.md)
* [Implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
