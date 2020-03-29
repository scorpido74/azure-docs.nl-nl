---
title: Levering en nieuwe poging van Azure Event Grid
description: Beschrijft hoe Azure Event Grid gebeurtenissen en hoe het omgaat met niet-geleverde berichten.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: spelluru
ms.openlocfilehash: dda2fd98c4c0d330059156a5ec00baa97ffaf627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921059"
---
# <a name="event-grid-message-delivery-and-retry"></a>Levering en opnieuw proberen van gebeurtenisrasterberichten

In dit artikel wordt beschreven hoe Azure Event Grid omgaat met gebeurtenissen wanneer de levering niet wordt erkend.

Event Grid biedt duurzame levering. Het levert elk bericht ten minste eenmaal voor elk abonnement. Gebeurtenissen worden onmiddellijk naar het geregistreerde eindpunt van elk abonnement verzonden. Als een eindpunt de ontvangst van een gebeurtenis niet bevestigt, wordt de weergave van de gebeurtenis opnieuw ingeroepen.

## <a name="batched-event-delivery"></a>Batched event delivery

Event Grid kan standaard elke gebeurtenis afzonderlijk naar abonnees verzenden. De abonnee ontvangt een array met één gebeurtenis. U Gebeurtenisraster configureren op batchgebeurtenissen voor levering voor verbeterde HTTP-prestaties in scenario's met een hoge doorvoer.

Batched delivery heeft twee instellingen:

* **Maximale gebeurtenissen per batch** - Maximaal aantal gebeurtenissen Event Grid zal leveren per batch. Dit aantal zal nooit worden overschreden, maar er mogen minder evenementen worden geleverd als er geen andere evenementen beschikbaar zijn op het moment van publicatie. Gebeurtenisraster vertraagt gebeurtenissen niet om een batch te maken als er minder gebeurtenissen beschikbaar zijn. Moet tussen de 1 en 5.000 zijn.
* **Voorkeursbatchgrootte in kilobytes** - Doelplafond voor batchgrootte in kilobytes. Net als bij max-gebeurtenissen kan de batchgrootte kleiner zijn als er op het moment van publicatie niet meer gebeurtenissen beschikbaar zijn. Het is mogelijk dat een batch groter is dan de gewenste batchgrootte *als* een enkele gebeurtenis groter is dan de gewenste grootte. Als de gewenste grootte bijvoorbeeld 4 KB is en een gebeurtenis van 10 KB wordt gepusht naar Gebeurtenisraster, wordt de gebeurtenis 10 KB nog steeds in de eigen batch geleverd in plaats van te worden verwijderd.

Batched delivery in configured on a per-event subscription basis via de portal, CLI, PowerShell of SDKs.

### <a name="azure-portal"></a>Azure Portal: 
![Instellingen voor batchbezorging](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure-CLI
Gebruik bij het maken van een gebeurtenisabonnement de volgende parameters: 

- **max-events-per-batch** - Maximaal aantal gebeurtenissen in een batch. Moet een getal tussen 1 en 5000 zijn.
- **preferred-batch-size-in-kilobytes** - Voorkeursbatchgrootte in kilobytes. Moet een getal tussen 1 en 1024 zijn.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Zie [Route-opslaggebeurtenissen naar webeindpunt met Azure CLI](../storage/blobs/storage-blob-event-quickstart.md)voor meer informatie over het gebruik van Azure CLI met gebeurtenisraster.

## <a name="retry-schedule-and-duration"></a>Schema en duur opnieuw proberen

Event Grid wacht 30 seconden op een antwoord na het afleveren van een bericht. Als het eindpunt na 30 seconden niet heeft gereageerd, wordt het bericht in de wachtrij geplaatst voor het opnieuw proberen. Event Grid gebruikt een exponentieel backoff retry-beleid voor het leveren van gebeurtenissen. Gebeurtenisgrid herzendingen levering op de volgende schema op een best effort basis:

- 10 seconden
- 30 seconden
- 1 minuut
- 5 minuten
- 10 minuten
- 30 minuten
- 1 uur
- Uurtijd tot 24 uur

Als het eindpunt binnen 3 minuten reageert, probeert Gebeurtenisgrid de gebeurtenis op basis van de beste inspanning uit de wachtrij voor opnieuw proberen te verwijderen, maar kunnen er nog steeds duplicaten worden ontvangen.

Event Grid voegt een kleine randomisatie toe aan alle stappen opnieuw proberen en kan op opportunistische wijze bepaalde nieuwe pogingen overslaan als een eindpunt consistent ongezond is, voor een lange periode omlaag of overweldigd lijkt te zijn.

Stel voor deterministisch gedrag de gebeurtenistijd in op live- en max-leveringspogingen in het [beleid voor het opnieuw proberen van abonnementen](manage-event-delivery.md).

Standaard verloopt gebeurtenisraster alle gebeurtenissen die niet binnen 24 uur worden geleverd. U [het beleid voor opnieuw proberen aanpassen](manage-event-delivery.md) bij het maken van een gebeurtenisabonnement. U geeft het maximum aantal afleverpogingen op (standaard is 30) en de gebeurtenis time-to-live (standaard is 1440 minuten).

## <a name="delayed-delivery"></a>Vertraagde levering

Aangezien een eindpunt leveringsfouten ondervindt, begint Gebeurtenisgrid de levering en het opnieuw proberen van gebeurtenissen naar dat eindpunt uit te stellen. Als bijvoorbeeld de eerste 10 gebeurtenissen die zijn gepubliceerd op een eindpunt mislukken, gaat Gebeurtenisgrid ervan uit dat het eindpunt problemen ondervindt en worden alle volgende nieuwe pogingen *en nieuwe* leveringen enige tijd uitgesteld - in sommige gevallen tot enkele uren.

Het functionele doel van vertraagde levering is het beschermen van ongezonde eindpunten en het Event Grid-systeem. Zonder back-off en vertraging van de levering aan ongezonde eindpunten, kunnen de beleid- en volumemogelijkheden van Event Grid een systeem gemakkelijk overweldigen.

## <a name="dead-letter-events"></a>Dode-letter gebeurtenissen

Wanneer Event Grid geen gebeurtenis kan leveren, kan het de niet-geleverde gebeurtenis naar een opslagaccount sturen. Dit proces staat bekend als dead-lettering. Standaard schakelt Event Grid geen dead-lettering in. Als u dit wilt inschakelen, moet u een opslagaccount opgeven om niet-geleverde gebeurtenissen vast te houden bij het maken van het gebeurtenisabonnement. U haalt gebeurtenissen uit dit opslagaccount om leveringen op te lossen.

Event Grid stuurt een gebeurtenis naar de dode-letter locatie wanneer het al zijn pogingen tot nieuwe pogingen heeft geprobeerd. Als Event Grid een responscode van 400 (Bad Request) of 413 (Request Entity Too Large) ontvangt, wordt de gebeurtenis onmiddellijk naar het eindpunt van de dode letter gestuurd. Deze antwoordcodes geven aan dat de levering van de gebeurtenis nooit zal slagen.

Er is een vertraging van vijf minuten tussen de laatste poging om een gebeurtenis te leveren en wanneer deze op de dode-letterlocatie wordt afgeleverd. Deze vertraging is bedoeld om het aantal Blob-opslagbewerkingen te verminderen. Als de dode-letterlocatie vier uur niet beschikbaar is, wordt de gebeurtenis verwijderd.

Voordat u de dode-letterlocatie instelt, moet u een opslagaccount bij een container hebben. U geeft het eindpunt voor deze container op bij het maken van het gebeurtenisabonnement. Het eindpunt is in de vorm van:`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

U een melding ontvangen wanneer een gebeurtenis naar de locatie van de dode letter is verzonden. Als u Gebeurtenisraster wilt gebruiken om te reageren op niet-geleverde gebeurtenissen, [maakt u een gebeurtenisabonnement](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) voor de opslag van de dode letterblob. Elke keer dat uw dode-letter blob-opslag een niet-geleverde gebeurtenis ontvangt, waarschuwt Event Grid uw handler. De handler reageert met acties die u wilt uitvoeren voor het combineren van niet-geleverde gebeurtenissen.

Zie [Dode letter en beleid opnieuw proberen](manage-event-delivery.md)voor een voorbeeld van het instellen van een dode letterlocatie.

## <a name="message-delivery-status"></a>Status van berichtbezorging

Event Grid gebruikt HTTP-antwoordcodes om de ontvangst van gebeurtenissen te bevestigen. 

### <a name="success-codes"></a>Succescodes

Event Grid beschouwt **alleen** de volgende HTTP-antwoordcodes als geslaagde leveringen. Alle andere statuscodes worden beschouwd als mislukte leveringen en worden opnieuw geprobeerd of naar gelang van het geval opnieuw gebriefd. Na ontvangst van een succesvolle statuscode beschouwt Event Grid de levering als voltooid.

- 200 OK
- 201 Gemaakt
- 202 Geaccepteerd
- 203 Niet-gezaghebbende informatie
- 204 Geen inhoud

### <a name="failure-codes"></a>Foutcodes

Alle andere codes die niet in de bovenstaande set (200-204) staan, worden als mislukt beschouwd en worden opnieuw geprobeerd. Sommige hebben specifieke retry beleid gebonden aan hen hieronder beschreven, alle anderen volgen de standaard exponentiële back-off model. Het is belangrijk om in gedachten te houden dat als gevolg van de sterk parallelle aard van de architectuur van Event Grid, het retry-gedrag niet-deterministisch is. 

| Statuscode | Gedrag opnieuw proberen |
| ------------|----------------|
| 400 Slecht verzoek | Opnieuw proberen na 5 minuten of meer (Deadletter onmiddellijk als deadletter setup) |
| 401 Ongeautoriseerd | Probeer het opnieuw na 5 minuten of meer |
| 403 Verboden | Probeer het opnieuw na 5 minuten of meer |
| 404 Niet gevonden | Probeer het opnieuw na 5 minuten of meer |
| 408 Time-out van aanvraag | Probeer het opnieuw na 2 minuten of meer |
| 413 Entiteit te groot aanvragen | Opnieuw proberen na 10 seconden of meer (Deadletter onmiddellijk als deadletter setup) |
| 503 Service niet beschikbaar | Probeer het opnieuw na 30 seconden of meer |
| Alle anderen | Probeer het opnieuw na 10 seconden of meer |


## <a name="next-steps"></a>Volgende stappen

* Zie [Gebeurtenisnetbezorging controleren](monitor-event-delivery.md)om de status van gebeurtenisleveringen te bekijken.
* Zie [Dode letter en beleid opnieuw proberen](manage-event-delivery.md)als u opties voor het bezorgen van gebeurtenissen wilt aanpassen.
