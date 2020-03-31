---
title: Uitgaande verificatie
description: Informatie over het instellen of verwijderen van uitgaande verificatie voor Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: bcd14e618323aec1c7ce47fcebb25099fa96be81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898512"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Uitgaande verificatie voor Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) vervangt Azure Scheduler, dat [wordt uitgeschakeld.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Als u wilt blijven werken met de taken die u in Scheduler hebt ingesteld, migreert u zo snel mogelijk [naar Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Scheduler is niet langer beschikbaar in de Azure-portal, maar de [REST API-](/rest/api/scheduler) en [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taakverzamelingen beheren.

Azure Scheduler-taken moeten mogelijk services aanroepen waarvoor verificatie vereist is, zoals andere Azure-services, Salesforce.com, Facebook en beveiligde aangepaste websites. De aangeroepen service kan bepalen of de taak Scheduler toegang heeft tot de gevraagde resources. 

Scheduler ondersteunt deze verificatiemodellen: 

* *Clientcertificaatverificatie* bij het gebruik van SSL/TLS-clientcertificaten
* *Basisverificatie*
* *Active Directory OAuth-verificatie*

## <a name="add-or-remove-authentication"></a>Verificatie toevoegen of verwijderen

* Als u verificatie wilt toevoegen aan een scheduler-taak, `authentication` voegt u bij het maken of `request` bijwerken van de taak het onderliggende element (JavaScript Object Notation) toe aan het element. 

  Antwoorden geven nooit geheimen terug die worden doorgegeven aan de Scheduler-service via een PUT-, PATCH- of POST-verzoek in het `authentication` object. 
  In antwoorden worden geheime informatie ingesteld op null of kan een openbaar token worden gebruikt dat de geverifieerde entiteit vertegenwoordigt. 

* Als u verificatie wilt verwijderen uit een Scheduler-taak, voert u `authentication` expliciet een PUT- of PATCH-aanvraag uit op de taak en stelt u het object in op null. Het antwoord bevat geen verificatie-eigenschappen.

## <a name="client-certificate"></a>Clientcertificaat

### <a name="request-body---client-certificate"></a>Aanvraagbody - Clientcertificaat

Wanneer u verificatie `ClientCertificate` toevoegt met het model, geeft u deze extra elementen op in de aanvraaginstantie.  

| Element | Vereist | Beschrijving |
|---------|----------|-------------|
| **verificatie** (bovenliggend element) | Het verificatieobject voor het gebruik van een SSL-clientcertificaat |
| **Type** | Ja | Het verificatietype. Voor SSL-clientcertificaten is `ClientCertificate`de waarde . |
| **pfx** | Ja | De basis64-gecodeerde inhoud van het PFX-bestand |
| **wachtwoord** | Ja | Het wachtwoord voor toegang tot het PFX-bestand |
||| 

### <a name="response-body---client-certificate"></a>Antwoordbody - Clientcertificaat 

Wanneer een aanvraag wordt verzonden met verificatiegegevens, bevat het antwoord deze verificatie-elementen.

| Element | Beschrijving | 
|---------|-------------| 
| **verificatie** (bovenliggend element) | Het verificatieobject voor het gebruik van een SSL-clientcertificaat |
| **Type** | Het verificatietype. Voor SSL-clientcertificaten is `ClientCertificate`de waarde . |
| **certificateThumbprint** |De duimafdruk van het certificaat |
| **certificateSubjectName** |De naam van het certificaatonderwerp |
| **certificaatVervaldatum** | De vervaldatum van het certificaat |
||| 

### <a name="sample-rest-request---client-certificate"></a>Voorbeeld AANVRAAG REST - Clientcertificaat

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>Voorbeeld VAN REST-antwoord - Clientcertificaat

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Basic

### <a name="request-body---basic"></a>Aanvraag lichaam - Basic

Wanneer u verificatie `Basic` toevoegt met het model, geeft u deze extra elementen op in de aanvraaginstantie.

| Element | Vereist | Beschrijving |
|---------|----------|-------------|
| **verificatie** (bovenliggend element) | Het verificatieobject voor het gebruik van basisverificatie | 
| **Type** | Ja | Het verificatietype. Voor Basisverificatie is `Basic`de waarde . | 
| **Gebruikersnaam** | Ja | De gebruikersnaam om te verifiëren | 
| **wachtwoord** | Ja | Het wachtwoord dat u moet verifiëren |
|||| 

### <a name="response-body---basic"></a>Reactiebody - Basic

Wanneer een aanvraag wordt verzonden met verificatiegegevens, bevat het antwoord deze verificatie-elementen.

| Element | Beschrijving | 
|---------|-------------|
| **verificatie** (bovenliggend element) | Het verificatieobject voor het gebruik van basisverificatie |
| **Type** | Het verificatietype. Voor basisverificatie is `Basic`de waarde . |
| **Gebruikersnaam** | De geverifieerde gebruikersnaam |
||| 

### <a name="sample-rest-request---basic"></a>Voorbeeld AANVRAAG REST - Basic

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>Voorbeeld RUST-respons - Basic

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>Aanvraagbody - Active Directory OAuth 

Wanneer u verificatie `ActiveDirectoryOAuth` toevoegt met het model, geeft u deze extra elementen op in de aanvraaginstantie.

| Element | Vereist | Beschrijving |
|---------|----------|-------------|
| **verificatie** (bovenliggend element) | Ja | Het verificatieobject voor het gebruik van ActiveDirectoryOAuth-verificatie |
| **Type** | Ja | Het verificatietype. Voor ActiveDirectoryOAuth-verificatie is `ActiveDirectoryOAuth`de waarde . |
| **Huurder** | Ja | De tenant-id voor de Azure AD-tenant. Voer uit in Azure PowerShell om `Get-AzureAccount` de tenant-id voor de Azure AD-tenant te vinden. |
| **Publiek** | Ja | Deze waarde is `https://management.core.windows.net/`ingesteld op . | 
| **clientId** | Ja | De client-id voor de Azure AD-toepassing | 
| **geheim** | Ja | Het geheim voor de client die het token aanvraagt | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Reactiebody - Active Directory OAuth

Wanneer een aanvraag wordt verzonden met verificatiegegevens, bevat het antwoord deze verificatie-elementen.

| Element | Beschrijving |
|---------|-------------|
| **verificatie** (bovenliggend element) | Het verificatieobject voor het gebruik van ActiveDirectoryOAuth-verificatie |
| **Type** | Het verificatietype. Voor ActiveDirectoryOAuth-verificatie is `ActiveDirectoryOAuth`de waarde . | 
| **Huurder** | De tenant-id voor de Azure AD-tenant |
| **Publiek** | Deze waarde is `https://management.core.windows.net/`ingesteld op . |
| **clientId** | De client-id voor de Azure AD-toepassing |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Voorbeeldaanvraag REST - Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>Voorbeeld VAN REST-respons - Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Scheduler-concepten en terminologie entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Azure Scheduler-limieten, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)
* [Naslaginformatie over REST API van Azure Scheduler](/rest/api/scheduler)
* [Naslaginformatie over Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md)