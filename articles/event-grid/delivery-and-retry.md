---
title: Azure Event Grid levering en probeer het opnieuw
description: Hierin wordt beschreven hoe Azure Event Grid gebeurtenissen levert en hoe er niet-bezorgde berichten worden verwerkt.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e565bbc8592dc2818e3573672e6e3035c3c8983a
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86113833"
---
# <a name="event-grid-message-delivery-and-retry"></a>Bericht bezorging Event Grid en probeer het opnieuw

In dit artikel wordt beschreven hoe Azure Event Grid gebeurtenissen afhandelt wanneer de levering niet wordt bevestigd.

Event Grid biedt een duurzame levering. Het levert elk bericht ten minste één keer per abonnement. Gebeurtenissen worden direct naar het geregistreerde eind punt van elk abonnement verzonden. Als een eind punt de ontvangst van een gebeurtenis niet bevestigt, Event Grid nieuwe pogingen van de gebeurtenis.

## <a name="batched-event-delivery"></a>Levering van batch gebeurtenissen

Event Grid standaard elke gebeurtenis afzonderlijk naar abonnees verzenden. De abonnee ontvangt een matrix met één gebeurtenis. U kunt Event Grid voor de levering van batch gebeurtenissen configureren voor betere HTTP-prestaties in scenario's met hoge door voer.

De batch levering heeft twee instellingen:

* **Max. gebeurtenissen per batch** -maximum aantal gebeurtenissen Event grid per batch worden geleverd. Dit aantal wordt nooit overschreden. er kunnen echter minder gebeurtenissen worden geleverd als er geen andere gebeurtenissen beschikbaar zijn op het moment van publiceren. Event Grid vertraging gebeurtenissen niet op om een batch te maken als er minder gebeurtenissen beschikbaar zijn. Moet tussen 1 en 5.000.
* De **voor Keurs-Batch grootte in kilo bytes** -doel plafond voor Batch grootte in kB. Net als bij de maximale gebeurtenissen kan de Batch grootte kleiner zijn als er meer gebeurtenissen niet beschikbaar zijn op het moment van publiceren. Het is mogelijk dat een batch groter is dan de voorkeurs Batch grootte *als* één gebeurtenis groter is dan de voorkeurs grootte. Als de voorkeurs grootte bijvoorbeeld 4 KB is en een gebeurtenis van 10 KB naar Event Grid wordt gepusht, wordt de gebeurtenis met 10 KB nog steeds in een eigen batch opgenomen, in plaats van dat deze wordt verwijderd.

Batch levering is geconfigureerd op basis van per gebeurtenis abonnement via de portal, CLI, Power shell of Sdk's.

### <a name="azure-portal"></a>Azure Portal: 
![Instellingen voor batch levering](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
Gebruik de volgende para meters bij het maken van een gebeurtenis abonnement: 

- **Max-gebeurtenissen-per batch** -maximum aantal gebeurtenissen in een batch. Moet een getal tussen 1 en 5000 zijn.
- **voor keur-Batch-grootte-in-kilo bytes** -de gewenste Batch grootte in kB. Moet een getal tussen 1 en 1024 zijn.

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

Voor meer informatie over het gebruik van Azure CLI met Event Grid raadpleegt [u opslag gebeurtenissen naar een webeindpunt routeren met Azure cli](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Schema en duur van opnieuw proberen

Event Grid 30 seconden wachten op een reactie na het afleveren van een bericht. Als het eind punt 30 seconden niet heeft gereageerd, wordt het bericht in de wachtrij geplaatst voor opnieuw proberen. Event Grid gebruikt een exponentiële uitstel beleid voor opnieuw proberen voor gebeurtenis levering. Event Grid nieuwe pogingen op het volgende schema worden uitgevoerd op basis van de beste inspanningen:

- 10 seconden
- 30 seconden
- 1 minuut
- 5 minuten
- 10 minuten
- 30 minuten
- 1 uur
- Maxi maal 24 uur per uur

Als het eind punt binnen drie minuten reageert, probeert Event Grid de gebeurtenis te verwijderen uit de wachtrij voor nieuwe pogingen, maar zijn er dubbele items mogelijk wel ontvangen.

Event Grid voegt een kleine wille keurige stap toe aan alle stappen voor opnieuw proberen en kan eventueel bepaalde nieuwe pogingen overs Laan als een eind punt zich in de consistente status bevindt, gedurende een lange periode niet actief is of niet wordt overbelast.

Stel voor deterministisch gedrag de gebeurtenis tijd in op Live en Maxi maal bezorgings pogingen in het [beleid voor opnieuw proberen](manage-event-delivery.md)van het abonnement.

Event Grid verloopt standaard alle gebeurtenissen die binnen 24 uur niet worden geleverd. U kunt [het beleid voor opnieuw proberen](manage-event-delivery.md) aan te passen bij het maken van een gebeurtenis abonnement. U geeft het maximum aantal bezorgings pogingen op (standaard 30) en de gebeurtenis time-to-Live (de standaard waarde is 1440 minuten).

## <a name="delayed-delivery"></a>Vertraagde levering

Als een eind punt uitvalt, worden de levering en het opnieuw proberen van gebeurtenissen naar dat eind punt door Event Grid uitgesteld. Als bijvoorbeeld de eerste 10 gebeurtenissen die naar een eind punt zijn gepubliceerd, mislukt, Event Grid ervan uitgaan dat het eind punt problemen ondervindt en alle volgende pogingen *en nieuwe* leveringen gedurende een bepaalde periode tot enkele uren in enkele gevallen vertragen.

Het functionele doel van een vertraagde levering is het beveiligen van beschadigde eind punten en het Event Grid systeem. Als er geen back-up wordt uitgevoerd en er wordt uitgegaan van de levering aan de beschadigde eind punten, kan het beleid voor opnieuw proberen van Event Grid en kunnen de volume mogelijkheden eenvoudig een systeem overbelasten.

## <a name="dead-letter-events"></a>Onbestelbare gebeurtenissen

Als Event Grid geen gebeurtenis kan leveren, kan deze de niet-bezorgde gebeurtenis verzenden naar een opslag account. Dit proces wordt onbestelbare berichten genoemd. Standaard wordt door Event Grid geen onbestelbare berichten ingeschakeld. Als u deze functie wilt inschakelen, moet u een opslag account opgeven om niet-bezorgde gebeurtenissen te bewaren tijdens het maken van het gebeurtenis abonnement. U haalt gebeurtenissen uit dit opslag account op om leveringen op te lossen.

Event Grid verzendt een gebeurtenis naar de locatie van de onbestelbare berichten wanneer deze alle nieuwe pogingen heeft geprobeerd uit te voeren. Als Event Grid een respons code van 400 (ongeldige aanvraag) of 413 (te grote aanvraag entiteit) ontvangt, wordt de gebeurtenis onmiddellijk naar het eind punt voor onbestelbare berichten verzonden. Met deze antwoord codes wordt aangegeven dat de levering van de gebeurtenis nooit slaagt.

Er is een vertraging van vijf minuten tussen de laatste poging om een gebeurtenis te leveren en wanneer deze wordt geleverd aan de locatie van de onbestelbare berichten. Deze vertraging is bedoeld om het aantal Blob Storage-bewerkingen te verminderen. Als de locatie voor onbestelbare berichten vier uur niet beschikbaar is, wordt de gebeurtenis verwijderd.

Voordat u de locatie van de onbestelbare letter instelt, moet u een opslag account hebben met een container. U geeft het eind punt voor deze container op bij het maken van het gebeurtenis abonnement. Het eind punt heeft de volgende indeling:`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Mogelijk wilt u een melding ontvangen wanneer een gebeurtenis naar de locatie van de onbestelbare brief is verzonden. Als u Event Grid wilt gebruiken om te reageren op niet-bezorgde gebeurtenissen, [maakt u een gebeurtenis abonnement](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) voor de onbestelbare Blob-opslag. Telkens wanneer uw dead-letter-Blob-opslag een niet-bezorgd gebeurtenis ontvangt, wordt Event Grid een melding verzonden naar uw handler. De handler reageert met de acties die u wilt uitvoeren voor het afstemmen van niet-bezorgde gebeurtenissen.

Zie [Dead-letter en beleid voor opnieuw proberen](manage-event-delivery.md)voor een voor beeld van het instellen van een locatie met een onbestelbare letter.

## <a name="message-delivery-status"></a>Leverings status van bericht

Event Grid maakt gebruik van HTTP-antwoord codes om de ontvangst van gebeurtenissen te bevestigen. 

### <a name="success-codes"></a>Geslaagde codes

Event Grid beschouwt **alleen** de volgende HTTP-antwoord codes als geslaagde leveringen. Alle andere status codes worden beschouwd als mislukte leveringen en worden indien nodig opnieuw geprobeerd of deadlettered. Wanneer de status code is ontvangen, wordt de levering van Event Grid beschouwd als voltooid.

- 200 OK
- 201 gemaakt
- 202 geaccepteerd
- 203 niet-bindende informatie
- 204 geen inhoud

### <a name="failure-codes"></a>Fout codes

Alle andere codes die zich niet in de bovenstaande set (200-204) bevinden, worden beschouwd als fouten en worden opnieuw geprobeerd. Enkele van de specifieke beleids regels voor opnieuw proberen die hieronder worden beschreven, volgen alle andere voor beelden van het standaard exponentiële uitstel model. Het is belang rijk dat u er rekening mee houdt dat het gedrag van de nieuwe poging niet-deterministisch is als gevolg van de zeer parallelle aard van de architectuur van Event Grid. 

| Statuscode | Gedrag voor opnieuw proberen |
| ------------|----------------|
| 400 ongeldige aanvraag | Na 5 minuten of meer opnieuw proberen (Deadletter direct bij het instellen van Deadletter) |
| 401 niet gemachtigd | Opnieuw proberen na 5 minuten of langer |
| 403 Verboden | Opnieuw proberen na 5 minuten of langer |
| 404 Niet gevonden | Opnieuw proberen na 5 minuten of langer |
| 408 Time-out van aanvraag | Opnieuw proberen na twee minuten of langer |
| de 413-aanvraag entiteit is te groot | Na 10 seconden of meer opnieuw proberen (Deadletter onmiddellijk als Deadletter is ingesteld) |
| 503 Service niet beschikbaar | Opnieuw proberen na 30 seconden of langer |
| Alle andere | Opnieuw proberen na 10 seconden of langer |


## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid bericht bezorging bewaken](monitor-event-delivery.md)als u de status van de gebeurtenis bezorgingen wilt weer geven.
* Zie [Dead-letter en beleid voor opnieuw proberen](manage-event-delivery.md)om de bezorgings opties voor gebeurtenissen aan te passen.
