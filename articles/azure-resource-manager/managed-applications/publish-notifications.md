---
title: Beheerde apps met meldingen
description: Beheerde toepassingen configureren met webhook-eindpunten om meldingen over maken, updates, verwijderingen en fouten op de beheerde toepassingsinstanties te ontvangen.
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715681"
---
# <a name="azure-managed-applications-with-notifications"></a>Azure-beheerde toepassingen met meldingen

Met azure-beheerde toepassingsmeldingen kunnen uitgevers acties automatiseren op basis van levenscyclusgebeurtenissen van de beheerde toepassingsinstanties. Uitgevers kunnen aangepaste webhook-eindpunten voor meldingen van meldingen voor meldingen van meldingen over nieuwe en bestaande beheerde toepassingsinstanties opgeven. Uitgevers kunnen aangepaste werkstromen instellen op het moment dat de toepassing wordt ingericht, bijgewerkt en verwijderd.

## <a name="getting-started"></a>Aan de slag
Als u beheerde toepassingen wilt ontvangen, draait u een openbaar HTTPS-eindpunt op en geeft u het op wanneer u de definitie van servicecatalogustoepassingen of Azure Marketplace-aanbieding publiceert.

Hier volgen de aanbevolen stappen om snel aan de slag te gaan:
1. Draai een openbaar HTTPS-eindpunt op dat de `200 OK`binnenkomende POST-aanvragen en retourzendingen registreert.
2. Voeg het eindpunt toe aan de definitie van servicecatalogustoepassingen of Azure Marketplace-aanbieding, zoals later in dit artikel wordt uitgelegd.
3. Maak een beheerde toepassingsinstantie die verwijst naar de toepassingsdefinitie of Azure Marketplace-aanbieding.
4. Controleer of de meldingen worden ontvangen.
5. Autorisatie inschakelen zoals uitgelegd in de sectie **Endpoint-verificatie** van dit artikel.
6. Volg de instructies in de sectie **Meldingsschema** van dit artikel om de meldingsaanvragen te ontlopen en uw bedrijfslogica te implementeren op basis van de melding.

## <a name="add-service-catalog-application-definition-notifications"></a>Meldingen voor de definitie van servicecatalogustoepassingen toevoegen
#### <a name="azure-portal"></a>Azure Portal
Zie [Een servicecatalogustoepassing publiceren via Azure-portal](./publish-portal.md)om aan de slag te gaan.

![Meldingen voor de definitie van servicecatalogustoepassingen in de Azure-portal](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> Momenteel u slechts één eindpunt `notificationEndpoints` leveren in de eigenschappen van de toepassingsdefinitie.

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
## <a name="add-azure-marketplace-managed-application-notifications"></a>Azure Marketplace-beheerde toepassingsmeldingen toevoegen
Zie [Een Azure-toepassingsaanbieding maken](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)voor meer informatie.

![Azure Marketplace-beheerde toepassingsmeldingen in de Azure-portal](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>Gebeurtenistriggers
In de volgende tabel worden alle mogelijke combinaties van EventType en ProvisioningState en de triggers beschreven:

EventType (EventType) | VoorzieningStaat | Trigger voor melding
---|---|---
PUT | Geaccepteerd | Beheerde resourcegroep is gemaakt en geprojecteerd na toepassing PUT (voordat de implementatie binnen de beheerde resourcegroep wordt gestart).
PUT | Geslaagd | Volledige inrichting van de beheerde aanvraag geslaagd na een PUT.
PUT | Mislukt | Falen van PUT van toepassing instantie inrichting op elk punt.
Patch | Geslaagd | Na een succesvolle PATCH op de beheerde toepassingsinstantie om tags bij te werken, het JIT-toegangsbeleid of beheerde identiteit.
DELETE | Verwijderen | Zodra de gebruiker een delete van een beheerde app-instantie initieert.
DELETE | Verwijderen | Na de volledige en succesvolle verwijdering van de beheerde toepassing.
DELETE | Mislukt | Na een fout tijdens het deprovisioningproces dat de verwijdering blokkeert.
## <a name="notification-schema"></a>Meldingsschema
Wanneer u uw webhook-eindpunt opdraait om meldingen te verwerken, moet u de payload ontschepen om belangrijke eigenschappen te krijgen om vervolgens op de melding te reageren. Servicecatalogus en Azure Marketplace beheerde toepassingsmeldingen bieden veel van dezelfde eigenschappen. Twee kleine verschillen worden beschreven in de tabel die volgt op de monsters.

#### <a name="service-catalog-application-notification-schema"></a>Servicecatalogustoepassingsmeldingsschema
Hier vindt u een melding van een voorbeeldservicecatalogus na het succesvol inrichten van een beheerde toepassingsinstantie:
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

Als de inrichting mislukt, wordt een melding met de foutgegevens naar het opgegeven eindpunt verzonden.

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

#### <a name="azure-marketplace-application-notification-schema"></a>Azure Marketplace-toepassingsmeldingsschema

Hier vindt u een melding van een voorbeeldservicecatalogus na het succesvol inrichten van een beheerde toepassingsinstantie:
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

Als de inrichting mislukt, wordt een melding met de foutgegevens naar het opgegeven eindpunt verzonden.

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
eventType | Het type gebeurtenis dat de melding heeft geactiveerd. (Bijvoorbeeld PUT, PATCH, DELETE.)
applicationId | De volledig gekwalificeerde resource-id van de beheerde toepassing waarvoor de melding is geactiveerd.
eventTime | De tijdstempel van de gebeurtenis die de melding heeft geactiveerd. (Datum en tijd in UTC ISO 8601-notatie.)
provisioningStaat | De inrichtingsstatus van de beheerde toepassingsinstantie. (Bijvoorbeeld Geslaagd, Mislukt, Verwijderen, Verwijderen.)
error | *Alleen opgegeven wanneer de provisioningState is mislukt*. Bevat de foutcode, het bericht en de details van het probleem dat de fout heeft veroorzaakt.
applicationDefinitionId | *Alleen opgegeven voor beheerde toepassingen voor servicecatalogus*. Vertegenwoordigt het volledig gekwalificeerde resource-id van de toepassingsdefinitie waarvoor de beheerde toepassingsinstantie is ingericht.
plannen | *Alleen opgegeven voor azure marketplace-beheerde toepassingen*. Vertegenwoordigt de uitgever, aanbieding, SKU en versie van de beheerde toepassingsinstantie.
factureringDetails | *Alleen opgegeven voor azure Marketplace-beheerde toepassingen.* De factureringsgegevens van de beheerde toepassingsinstantie. Bevat de resourceUsageId die u gebruiken om Azure Marketplace op te vragen voor gebruiksgegevens.

## <a name="endpoint-authentication"></a>Eindpuntverificatie
Ga als het gaat om het webhook-eindpunt en om de authenticiteit van de melding te waarborgen:
1. Geef een queryparameter op boven op de webhook URI, zoals deze: https\:/your-endpoint.com?sig=Guid. Controleer bij elke melding of `sig` de queryparameter de verwachte waarde `Guid`heeft.
2. Geef een GET op de beheerde toepassingsinstantie met behulp van applicationId. Valideren dat de provisioningState overeenkomt met de provisioningState van de kennisgeving om consistentie te garanderen.

## <a name="notification-retries"></a>Meldingspogingen

De service Managed Application `200 OK` Notification verwacht een antwoord van het webhook-eindpunt op de melding. De meldingsservice probeert opnieuw als het webhook-eindpunt een HTTP-foutcode retourneert die groter is dan of gelijk is aan 500, als er een foutcode van 429 wordt geretourneerd of als het eindpunt tijdelijk onbereikbaar is. Als het webhook-eindpunt niet binnen 10 uur beschikbaar is, wordt het meldingsbericht verwijderd en worden de nieuwe pogingen gestopt.
