---
title: Gebeurtenisschema azure-gebeurtenisraster
description: Beschrijft de eigenschappen die worden geleverd voor abonnementsgebeurtenissen met Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 4994063dfc3bce88489f70969c06bf36b591f907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561673"
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Azure Event Grid-gebeurtenisschema voor abonnementen

In dit artikel worden de eigenschappen en het schema voor Azure-abonnementsgebeurtenissen weergeven.Zie Azure Event Grid-gebeurtenisschema voor een inleiding tot gebeurtenisschema ['Azure Event Grid'.](event-schema.md)

Azure-abonnementen en resourcegroepen zenden dezelfde gebeurtenistypen uit. De gebeurtenistypen zijn gerelateerd aan resourcewijzigingen of acties. Het belangrijkste verschil is dat resourcegroepen gebeurtenissen uitzenden voor resources binnen de resourcegroep en Azure-abonnementen gebeurtenissen uitzenden voor bronnen in het hele abonnement.

Resourcegebeurtenissen worden gemaakt voor put-, PATCH-, POST- en VERWIJDERbewerkingen die worden verzonden naar `management.azure.com`. GET-bewerkingen maken geen gebeurtenissen. Bewerkingen die naar het `myaccount.blob.core.windows.net`gegevensvlak worden verzonden (zoals ) maken geen gebeurtenissen. De actiegebeurtenissen bieden gebeurtenisgegevens voor bewerkingen, zoals het weergeven van de sleutels voor een resource.

Wanneer u zich abonneert op gebeurtenissen voor een Azure-abonnement, ontvangt uw eindpunt alle gebeurtenissen voor dat abonnement. De gebeurtenissen kunnen gebeurtenissen bevatten die u wilt zien, zoals het bijwerken van een virtuele machine, maar ook gebeurtenissen die misschien niet belangrijk voor u zijn, zoals het schrijven van een nieuw item in de implementatiegeschiedenis. U alle gebeurtenissen op uw eindpunt ontvangen en code schrijven die de gebeurtenissen verwerkt die u wilt verwerken. U ook een filter instellen bij het maken van het gebeurtenisabonnement.

Als u gebeurtenissen programmatisch wilt afhandelen, `operationName` kunt u gebeurtenissen sorteren door naar de waarde te kijken. Uw gebeurteniseindpunt verwerkt bijvoorbeeld alleen gebeurtenissen voor bewerkingen `Microsoft.Compute/virtualMachines/write` `Microsoft.Storage/storageAccounts/write`die gelijk zijn aan of .

Het gebeurtenisonderwerp is de resource-id van de resource die het doel van de bewerking is. Als u gebeurtenissen voor een resource wilt filteren, geeft u die resource-id op bij het maken van het gebeurtenisabonnement. Als u wilt filteren op een resourcetype, gebruikt u een waarde in de volgende indeling:`/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

Zie bron van [Azure-abonnementen voor](event-sources.md#azure-subscriptions)een lijst met voorbeeldscripts en -zelfstudies.

## <a name="available-event-types"></a>Beschikbare gebeurtenistypen

Azure-abonnementen zenden beheergebeurtenissen uit vanuit Azure Resource Manager, zoals wanneer een VM wordt gemaakt of een opslagaccount wordt verwijderd.

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionGeannuleerd | Verhoogd wanneer actie op resource wordt geannuleerd. |
| Microsoft.Resources.ResourceActionFailure | Verhoogd wanneer actie op resource mislukt. |
| Microsoft.Resources.ResourceActionSucces | Verhoogd wanneer actie op resource slaagt. |
| Microsoft.Resources.ResourceDeleteAnnuleren | Verhoogd wanneer de bewerking wordt geannuleerd. Deze gebeurtenis vindt plaats wanneer een sjabloonimplementatie wordt geannuleerd. |
| Microsoft.Resources.ResourceDeleteFailure | Verhoogd wanneer de bewerking wordt verwijderd. |
| Microsoft.Resources.ResourceDeleteSuccess | Verhoogd wanneer de bewerking wordt verwijderd. |
| Microsoft.Resources.ResourceWriteAnnuleren | Verhoogd wanneer de bewerking voor maken of bijwerken wordt geannuleerd. |
| Microsoft.Resources.ResourceWriteFailure | Verhoogd wanneer de bewerking voor maken of bijwerken mislukt. |
| Microsoft.Resources.ResourceWriteSuccess | Verhoogd wanneer de bewerking voor maken of bijwerken slaagt. |

## <a name="example-event"></a>Voorbeeldgebeurtenis

In het volgende voorbeeld wordt het schema voor een **gebeurtenis ResourceWriteSuccess** weergegeven. Hetzelfde schema wordt gebruikt voor **ResourceWriteFailure** en **ResourceWriteCancel** gebeurtenissen met verschillende waarden voor `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

In het volgende voorbeeld wordt het schema voor een **gebeurtenis ResourceDeleteSuccess** weergegeven. Hetzelfde schema wordt gebruikt voor **ResourceDeleteFailure** en **ResourceDeleteAnnuleren gebeurtenissen** met verschillende waarden voor `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

In het volgende voorbeeld wordt het schema voor een **gebeurtenis ResourceActionSuccess** weergegeven. Hetzelfde schema wordt gebruikt voor **ResourceActionFailure** en **ResourceActionAnnuleren** gebeurtenissen met verschillende waarden voor `eventType`.

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}" 
}]
```

## <a name="event-properties"></a>Gebeurtenis-eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledig resourcepad naar de gebeurtenisbron. Dit veld is niet schrijfbaar. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | Unieke id voor de gebeurtenis. |
| data | object | Gegevens over abonnementsgebeurtenissen. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| autorisatie | object | De gevraagde vergunning voor de bewerking. |
| claims | object | De eigenschappen van de claims. Zie [JWT-specificatie](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)voor meer informatie. |
| correlationId | tekenreeks | Een bedrijfs-ID voor het oplossen van problemen. |
| httpRequest httpRequest httpRequest httpRequest | object | De details van de operatie. Dit object wordt alleen opgenomen bij het bijwerken van een bestaande bron of het verwijderen van een resource. |
| resourceProvider | tekenreeks | De resourceprovider voor de bewerking. |
| resourceUri | tekenreeks | De URI van de resource in de bewerking. |
| operationName | tekenreeks | De operatie die werd genomen. |
| status | tekenreeks | De status van de bewerking. |
| subscriptionId | tekenreeks | De abonnements-ID van de resource. |
| tenantId | tekenreeks | De tenant-id van de resource. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster?](overview.md)
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
