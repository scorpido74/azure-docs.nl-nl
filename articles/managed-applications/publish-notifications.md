---
title: Azure Managed Applications met meldingen
description: Configureer een beheerde toepassing met webhook-eind punten voor het ontvangen van meldingen over het maken, bijwerken, verwijderen en fouten op de exemplaren van de beheerde toepassing.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 8cf9fc0b3d9c13ebc5309be6d27c7be0f2e60878
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805685"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure Managed Applications met meldingen

Met Azure Managed Application Notifications kunnen uitgevers acties automatiseren op basis van levenscyclus gebeurtenissen van de beheerde toepassings exemplaren. Uitgevers kunnen aangepaste e-mailhook-eind punten opgeven om gebeurtenis meldingen over nieuwe en bestaande exemplaren van beheerde toepassingen te ontvangen. De uitgever kan aangepaste werk stromen instellen op het moment dat de toepassing wordt ingericht, bijgewerkt en verwijderd.

## <a name="getting-started"></a>Aan de slag
Als u beheerde toepassingen wilt ontvangen, moet u een openbaar HTTPS-eind punt opwaarderen en opgeven wanneer u de toepassings definitie van de Service catalogus of de Marketplace-aanbieding publiceert.

Hier volgen de aanbevolen reeks stappen om snel aan de slag te gaan:
1. Maak een openbaar HTTPS-eind punt dat de binnenkomende POST-aanvragen registreert en retourneert `200 OK`.
2. Voeg het eind punt toe aan de service catalog Application Definition of Marketplace-aanbieding zoals hieronder wordt uitgelegd.
3. Maak een exemplaar van een beheerde toepassing dat verwijst naar de toepassings definitie of de Marketplace-aanbieding.
4. Controleer of de meldingen correct zijn ontvangen.
5. Schakel autorisatie in zoals beschreven in de sectie **eindpunt verificatie** hieronder.
6. Volg de onderstaande documentatie over het **meldings schema** voor het parseren van de meldings aanvragen en het implementeren van uw bedrijfs logica op basis van de melding.

## <a name="adding-service-catalog-application-definition-notifications"></a>Service catalogus-toepassings definitie meldingen toevoegen
#### <a name="azure-portal"></a>Azure Portal
Lees [een service catalogus toepassing publiceren via Azure Portal](./publish-portal.md) om aan de slag te gaan.

![Meldingen over de toepassings definitie van de Service catalogus op de portal](./media/publish-notifications/service-catalog-notifications.png)
#### <a name="rest-api"></a>REST-API

> [!NOTE]
> Momenteel wordt slechts één eind punt ondersteund als onderdeel van de **notificationEndpoints** in de eigenschappen van de toepassings definitie

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="adding-marketplace-managed-application-notifications"></a>Meldingen over beheerde toepassingen voor Marketplace toevoegen
Zie [een Azure-toepassings aanbieding maken](../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)voor meer informatie.

![Meldingen over de toepassings definitie van de Service catalogus op de portal](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Gebeurtenistriggers
In de volgende tabel worden alle mogelijke combi Naties van Event type + ProvisioningState en de triggers beschreven:

EventType | ProvisioningState | Trigger voor melding
---|---|---
PUT | Geaccepteerd | De beheerde resource groep is gemaakt en geprojecteerd nadat de toepassing is geplaatst. (Voordat de implementatie binnen de beheerde RG wordt gestart.)
PUT | Geslaagd | Volledige inrichting van de beheerde toepassing is voltooid na een PUT.
PUT | Mislukt | Fout bij het inrichten van een toepassings exemplaar op enig moment.
PATCH | Geslaagd | Na een geslaagde PATCH voor een exemplaar van de beheerde toepassing voor het bijwerken van tags, JIT-toegangs beleid of beheerde identiteit.
DELETE | Deleting | Zodra de gebruiker een exemplaar van een beheerde app verwijdert.
DELETE | Deleted | Na de volledige en geslaagde verwijdering van de beheerde toepassing.
DELETE | Mislukt | Na een fout tijdens het ongedaan maken van de inrichting die de verwijdering blokkeert.
## <a name="notification-schema"></a>Meldings schema
Wanneer u het webhook-eind punt voor het afhandelen van meldingen hebt ingesteld, moet u de payload parseren om belang rijke eigenschappen op te halen om vervolgens te reageren op de melding. De Service catalogus en de meldingen van beheerde toepassingen op Marketplace bieden veel van dezelfde eigenschappen met het kleine verschil dat hieronder wordt beschreven.

#### <a name="service-catalog-application-notification-schema"></a>Schema voor toepassings meldingen van Service catalogus
Hier volgt een voor beeld van een service catalogus melding na een geslaagde inrichting van een exemplaar van een beheerde toepassing.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

Als het inrichten mislukt, wordt een melding met de fout gegevens verzonden naar het opgegeven eind punt.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="marketplace-application-notification-schema"></a>Schema voor toepassings meldingen in Marketplace

Hier volgt een voor beeld van een service catalogus melding na een geslaagde inrichting van een exemplaar van een beheerde toepassing.
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

Als het inrichten mislukt, wordt een melding met de fout gegevens verzonden naar het opgegeven eind punt.

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

Parameter | Beschrijving
---|---
eventType | Het type gebeurtenis dat de melding heeft geactiveerd. (bijvoorbeeld ' PUT ', ' PATCH ', ' DELETE ')
applicationId | De volledig gekwalificeerde resource-id van de beheerde toepassing waarvoor de melding is geactiveerd. 
eventTime | De tijds tempel van de gebeurtenis die de melding heeft geactiveerd. (Datum en tijd in UTC ISO 8601-indeling.)
ProvisioningState | De inrichtings status van het exemplaar van de beheerde toepassing. (bijvoorbeeld ' geslaagd ', ' mislukt ', ' verwijderen ', ' verwijderd ')
billingDetails | De facturerings gegevens van het exemplaar van de beheerde toepassing. Bevat de resourceUsageId die kunnen worden gebruikt om een query uit te zoeken op Marketplace voor gebruiks gegevens.
error | *Wordt alleen opgegeven als de provisioningState is mislukt*. Bevat de fout code, het bericht en de details van het probleem dat de fout heeft veroorzaakt.
applicationDefinitionId | *Alleen opgegeven voor door de service catalog beheerde toepassingen*. Hiermee wordt de volledig gekwalificeerde resource-id van de toepassings definitie aangegeven waarvoor het exemplaar van de beheerde toepassing is ingericht.
plannen | *Alleen opgegeven voor toepassingen die door Marketplace worden beheerd*. Vertegenwoordigt de uitgever, aanbieding, SKU en versie van het exemplaar van de beheerde toepassing.

## <a name="endpoint-authentication"></a>Eindpunt verificatie
Het webhook-eind punt beveiligen en de authenticiteit van de melding controleren:
- Geef een query parameter op boven op de webhook-URI, zoals https://your-endpoint.com?sig=Guid. Voer bij elke melding een snelle controle uit om te controleren of de query parameter `sig` de verwachte waarde bevat `Guid`.
- Geef een GET-aanvraag op het beheerde toepassings exemplaar met applicationId. Controleer of de provisioningState overeenkomt met de provisioningState van de melding om consistentie te garanderen.

## <a name="notification-retries"></a>Meldingen opnieuw proberen

De service Managed Application notification verwacht een `200 OK` reactie van het webhook-eind punt naar de melding. De meldings service probeert opnieuw als het webhook-eind punt een HTTP-fout code retourneert > = 500, 429 of als het eind punt tijdelijk niet bereikbaar is. Als het webhook-eind punt binnen 10 uur niet beschikbaar is, wordt het meldings bericht verwijderd en wordt de nieuwe poging gestopt.

