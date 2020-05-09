---
title: Azure Event Grid-probleemoplossings validatie van abonnementen
description: In dit artikel wordt beschreven hoe u de validatie van abonnementen kunt oplossen.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a052d4c268fadc60f754630156fe0bc0d33acf3b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630185"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Problemen met Azure Event Grid oplossen
In deze hand leiding vindt u informatie over het oplossen van validaties voor gebeurtenis abonnementen. 


## <a name="troubleshoot-event-subscription-validation"></a>Problemen met validatie van gebeurtenis abonnementen oplossen

Bij het maken van een gebeurtenis abonnement, als er een fout bericht wordt `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`weer gegeven, geeft dit aan dat er een fout is opgetreden in de validatie-handshake. U kunt deze fout oplossen door de volgende aspecten te controleren:

- Voer een HTTP POST naar uw webhook-URL uit met een voor beeld van een [SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) -aanvraag tekst met behulp van Postman of krul of een soortgelijk hulp programma.
- Als uw webhook het handshake-mechanisme voor synchrone validatie implementeert, controleert u of de ValidationCode als onderdeel van het antwoord wordt geretourneerd.
- Als uw webhook een asynchroon validatie-handshake-mechanisme implementeert, controleert u of u het HTTP POST-bericht 200 OK retourneert.
- Als uw webhook 403 (verboden) in het antwoord retourneert, controleert u of uw webhook zich achter een Azure-toepassing gateway of Web Application firewall bevindt. Als dat het geval is, moet u deze firewall regels uitschakelen en opnieuw een HTTP-POST doen:

  920300 (aanvraag mist een Accept-header, we kunnen dit oplossen)

  942430 (beperkte SQL-teken anomalie detectie (argumenten): aantal speciale tekens is overschreden (12))

  920230 (meerdere URL-code ring gedetecteerd)

  942130 (SQL-injectie aanval: SQL-Tautology gedetecteerd.)

  931130 (mogelijke RFI-aanval (Remote File Include) = off-domein verwijzing/koppeling)

> [!IMPORTANT]
> Zie [webhook Event Delivery](webhook-event-delivery.md)(Engelstalig) voor gedetailleerde informatie over eindpunt validatie voor webhooks.

## <a name="validate-event-grid-event-subscription-using-postman"></a>Event grid-gebeurtenis abonnement valideren met behulp van postman
Hier volgt een voor beeld van het gebruik van Postman voor het valideren van een webhook-abonnement van een Event Grid gebeurtenis: 

![Validatie van gebeurtenis abonnementen Event grid met behulp van postman](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Hier volgt een voor beeld van een JSON-SubscriptionValidationEvent:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Dit is het voor beeld van een geslaagde reactie:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Zie [endpoint validator with Event grid Events](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)(Engelstalig) voor meer informatie over gebeurtenis raster gebeurtenis validatie voor webhooks.

## <a name="validate-cloud-event-subscription-using-postman"></a>Cloud gebeurtenis abonnement valideren met behulp van postman
Hier volgt een voor beeld van het gebruik van Postman voor het valideren van een webhook-abonnement van een Cloud gebeurtenis: 

![Validatie van abonnement op Cloud gebeurtenis met behulp van postman](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Gebruik de **http-opties** methode voor validatie met Cloud gebeurtenissen. Zie [endpoint validator with Cloud Events](webhook-event-delivery.md#endpoint-validation-with-event-grid-events)(Engelstalig) voor meer informatie over de validatie van de Cloud gebeurtenis voor webhooks.

## <a name="event-grid-event-subscription-validation-using-curl"></a>Event grid-gebeurtenis abonnementen valideren met behulp van krul 
Hier volgt een voor beeld van een krul opdracht voor het valideren van een webhook-abonnement van een Event Grid gebeurtenis: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="next-steps"></a>Volgende stappen
Als u meer hulp nodig hebt, kunt u uw probleem in het [stack overflow forum](https://stackoverflow.com/questions/tagged/azure-eventgrid) plaatsen of een [ondersteunings ticket](https://azure.microsoft.com/support/options/)openen. 
