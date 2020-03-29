---
title: Grote berichten verwerken met chunking
description: Meer informatie over het omgaan met grote berichtformaten door chunking te gebruiken in geautomatiseerde taken en werkstromen die u maakt met Azure Logic Apps
services: logic-apps
ms.suite: integration
author: shae-hurst
ms.author: shhurst
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 81e7c12b04c1ebd9691c11d76f387f7d42490180
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456550"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Grote berichten verwerken met chunking in Azure Logic Apps

Bij het verwerken van berichten beperkt Logic Apps de inhoud van berichten tot een maximale grootte. Deze limiet helpt overhead te verminderen die wordt gemaakt door grote berichten op te slaan en te verwerken. Als u berichten wilt verwerken die groter zijn dan deze limiet, *kan* Logic Apps een groot bericht in kleinere berichten splitsen. Op die manier u nog steeds grote bestanden overbrengen met Behulp van Logic Apps onder specifieke voorwaarden. Bij het communiceren met andere services via connectors of HTTP kunnen Logische apps grote berichten gebruiken, maar *alleen* in brokken. Deze voorwaarde betekent dat connectors ook chunking moeten ondersteunen, of de onderliggende HTTP-berichtenuitwisseling tussen Logic Apps en deze services moet chunking gebruiken.

In dit artikel ziet u hoe u chunking instellen voor acties die berichten verwerken die groter zijn dan de limiet. Logic App-triggers ondersteunen geen chunking vanwege de toegenomen overhead van het uitwisselen van meerdere berichten. 

## <a name="what-makes-messages-large"></a>Wat maakt berichten "groot"?

Berichten zijn "groot" op basis van de service die deze berichten verwerkt. De exacte groottelimiet voor grote berichten verschilt per Logic Apps en connectoren. Zowel Logic Apps als connectors kunnen niet direct grote berichten verbruiken, die moeten worden vermorzegeld. Zie Limieten en configuratie van Logic Apps voor de limiet voor de berichtgrootte van Logic [Apps.](../logic-apps/logic-apps-limits-and-config.md)
Zie voor de limiet van de berichtgrootte van elke connector de specifieke technische details van de [connector.](../connectors/apis-list.md)

### <a name="chunked-message-handling-for-logic-apps"></a>Chunked message handling for Logic Apps

Logic Apps kunnen niet rechtstreeks uitvoer gebruiken van berichten die groter zijn dan de limiet voor de grootte van het bericht. Alleen acties die chunking ondersteunen, hebben toegang tot de inhoud van het bericht in deze uitvoer. Een actie die grote berichten verwerkt, moet dus *aan* deze criteria voldoen:

* Native ondersteuning chunking wanneer die actie behoort tot een connector. 
* Ondersteuning voor chunking ingeschakeld in de runtime-configuratie van die actie. 

Anders krijgt u een runtime-fout wanneer u toegang probeert te krijgen tot de uitvoer van grote inhoud. Zie Ondersteuning voor [chunking instellen](#set-up-chunking)inschakelen.

### <a name="chunked-message-handling-for-connectors"></a>Chunked message handling voor connectors

Services die communiceren met Logic Apps kunnen hun eigen limieten voor de grootte van berichten hebben. Deze limieten zijn vaak kleiner dan de limiet voor Logische apps. Als u bijvoorbeeld aanneemt dat een connector chunking ondersteunt, kan een connector een bericht van 30 MB als groot beschouwen, terwijl Logic Apps dat niet doet. Om aan de limiet van deze connector te voldoen, splitst Logic Apps elk bericht groter dan 30 MB in kleinere segmenten.

Voor connectors die chunking ondersteunen, is het onderliggende chunking-protocol onzichtbaar voor eindgebruikers. Niet alle connectors ondersteunen echter chunking, dus deze connectors genereren runtimefouten wanneer binnenkomende berichten de groottelimieten van de connectors overschrijden.

> [!NOTE]
> Voor acties die chunking gebruiken, u de triggerbody `@triggerBody()?['Content']` niet passeren of expressies gebruiken zoals in die acties. In plaats daarvan u voor tekst- of JSON-bestandsinhoud proberen de actie [ **Samenstellen** ](../logic-apps/logic-apps-perform-data-operations.md#compose-action) gebruiken of [een variabele maken](../logic-apps/logic-apps-create-variables-store-values.md) om die inhoud te verwerken. Als de triggerbody andere inhoudstypen bevat, zoals mediabestanden, moet u andere stappen uitvoeren om die inhoud te verwerken.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>Chunking instellen via HTTP

In algemene HTTP-scenario's u grote inhoudsdownloads en -uploads via HTTP opsplitsen, zodat uw logische app en een eindpunt grote berichten kunnen uitwisselen. U moet echter berichten vastwisselen op de manier die Logic Apps verwacht. 

Als een eindpunt chunking heeft ingeschakeld voor downloads of uploads, worden de HTTP-acties in uw logische app automatisch grote berichten vermorzen. Anders moet u ondersteuning voor chunking instellen op het eindpunt. Als u het eindpunt of de connector niet bezit of beheert, hebt u mogelijk niet de optie om chunking in te stellen.

Als een HTTP-actie al geen chunking inschakelt, moet u ook chunking instellen in de eigenschap van `runTimeConfiguration` de actie. U deze eigenschap in de actie instellen, rechtstreeks in de codeweergaveeditor zoals later beschreven, of in de Logic Apps Designer zoals hier beschreven:

1. Kies in de rechterbovenhoek van de HTTP-actie de knop ellips (**... )** en kies **Vervolgens Instellingen**.

   ![Open in de actie het instellingenmenu](./media/logic-apps-handle-large-messages/http-settings.png)

2. Stel **Chunking toestaan** onder **Inhoudsoverdracht**in op **.**

   ![Chunking inschakelen](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. Ga door met de volgende secties om chunking voor downloads of uploads in te stellen.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>Inhoud downloaden in brokken

Veel eindpunten verzenden automatisch grote berichten in stukjes wanneer ze worden gedownload via een HTTP GET-verzoek. Als u chunked-berichten wilt downloaden vanaf een eindpunt via HTTP, moet het eindpunt gedeeltelijke inhoudsaanvragen of *geblokte downloads*ondersteunen. Wanneer uw logische app een HTTP GET-verzoek verzendt naar een eindpunt voor het downloaden van inhoud en het eindpunt reageert met een statuscode '206', bevat het antwoord een inhoud met een stuk voor stuk. Logic Apps kan niet bepalen of een eindpunt gedeeltelijke aanvragen ondersteunt. Wanneer uw logica-app echter de eerste "206"-reactie krijgt, verzendt uw logische app automatisch meerdere verzoeken om alle inhoud te downloaden.

Als u wilt controleren of een eindpunt gedeeltelijke inhoud kan ondersteunen, stuurt u een HEAD-verzoek. Met deze aanvraag u `Accept-Ranges` bepalen of het antwoord de koptekst bevat. Op die manier u deze optie *voorstellen* door de `Range` koptekst in te stellen in uw HTTP GET-verzoek als het eindpunt een vast blok met de downloads ondersteunt, maar geen geblokte inhoud verzendt. 

In deze stappen wordt het gedetailleerde proces beschreven dat Logic Apps gebruikt voor het downloaden van geblokte inhoud van een eindpunt naar uw logische app:

1. Uw logica-app stuurt een HTTP GET-verzoek naar het eindpunt.

   De aanvraagkoptekst kan `Range` optioneel een veld bevatten dat een bytebereik beschrijft voor het aanvragen van inhoudssegmenten.

2. Het eindpunt reageert met de statuscode "206" en een HTTP-berichttekst.

    Details over de inhoud in dit segment `Content-Range` worden weergegeven in de koptekst van de reactie, inclusief informatie waarmee Logic Apps het begin en einde van de chunk kan bepalen, plus de totale grootte van de volledige inhoud voordat u chunking.

3. Uw logica-app verzendt automatisch follow-up HTTP GET-verzoeken.

    Uw logische app verzendt follow-up GET-aanvragen totdat de volledige inhoud is opgehaald.

Deze actiedefinitie toont bijvoorbeeld een HTTP GET-verzoek waarmee de `Range` koptekst wordt ingesteld. De *koptekst suggereert* dat het eindpunt moet reageren met geblokte inhoud:

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

Met de GET-aanvraag wordt de koptekst 'Bereik' ingesteld op 'bytes=0-1023', het bereik van bytes. Als het eindpunt aanvragen voor gedeeltelijke inhoud ondersteunt, wordt het eindpunt gereageerd met een inhoudssegment uit het gevraagde bereik. Op basis van het eindpunt kan de exacte indeling voor het koptekstveld 'Bereik' verschillen.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>Inhoud uploaden in brokken

Als u chunked-inhoud wilt uploaden van een HTTP-actie, moet `runtimeConfiguration` de actie ondersteuning voor chunking hebben ingeschakeld via de eigenschap van de actie. Met deze instelling kan de actie het chunking-protocol starten. Uw logica-app kan vervolgens een eerste bericht of PUT-bericht naar het doeleindpunt verzenden. Nadat het eindpunt reageert met een voorgestelde chunk-grootte, wordt uw logische app opgevolgd door HTTP PATCH-verzoeken te verzenden die de inhoudssegmenten bevatten.

In deze stappen wordt het gedetailleerde proces beschreven dat Logic Apps gebruikt voor het uploaden van gesegmenteerde inhoud van uw logische app naar een eindpunt:

1. Uw logische app verzendt een eerste HTTP-BERICHT of PUT-verzoek met een lege berichttekst. De hoofdtekst van het verzoek bevat deze informatie over de inhoud die uw logica-app in brokken wil uploaden:

   | Koptekstveld Logic Apps-aanvragen | Waarde | Type | Beschrijving |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-modus** | in stukken | Tekenreeks | Geeft aan dat de inhoud in brokken wordt geüpload |
   | **x-ms-inhoudslengte** | <*inhoudslengte*> | Geheel getal | De volledige inhoudsgrootte in bytes voordat u chunking |
   ||||

2. Het eindpunt reageert met "200" successtatuscode en deze optionele informatie:

   | Koptekst veld eindpuntrespons | Type | Vereist | Beschrijving |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-formaat** | Geheel getal | Nee | De voorgestelde chunk grootte in bytes |
   | **Locatie** | Tekenreeks | Ja | De URL-locatie waar de HTTP PATCH-berichten moeten worden verzonden |
   ||||

3. Uw logische app maakt en verzendt follow-up HTTP PATCH-berichten - elk met deze informatie:

   * Een inhoudsbrok op basis **van x-ms-chunk-grootte** of een intern berekende grootte totdat alle inhoud van de **totale x-ms-content-lengte** opeenvolgend wordt geüpload

   * Deze koptekstdetails over de inhoudsbrok die in elk PATCH-bericht wordt verzonden:

     | Koptekstveld Logic Apps-aanvragen | Waarde | Type | Beschrijving |
     |---------------------------------|-------|------|-------------|
     | **Inhoudsbereik** | <*Bereik*> | Tekenreeks | Het bytebereik voor de huidige inhoudsbrok, inclusief de beginwaarde, eindwaarde en de totale inhoudsgrootte, bijvoorbeeld: 'bytes=0-1023/10100' |
     | **Content-Type** | <*inhoudstype*> | Tekenreeks | Het type afgetelde inhoud |
     | **Inhoudslengte** | <*inhoudslengte*> | Tekenreeks | De lengte van de grootte in bytes van de huidige chunk |
     |||||

4. Na elk PATCH-verzoek bevestigt het eindpunt de ontvangst voor elk stuk door te reageren met de statuscode "200" en de volgende antwoordkoppen:

   | Koptekst veld eindpuntrespons | Type | Vereist | Beschrijving |
   |--------------------------------|------|----------|-------------|
   | **Bereik** | Tekenreeks | Ja | Het bytebereik voor inhoud die is ontvangen door het eindpunt, bijvoorbeeld: "bytes=0-1023" |   
   | **x-ms-chunk-formaat** | Geheel getal | Nee | De voorgestelde chunk grootte in bytes |
   ||||

Deze actiedefinitie toont bijvoorbeeld een HTTP POST-verzoek voor het uploaden van geblokte inhoud naar een eindpunt. In het pand `runTimeConfiguration` van `contentTransfer` de `transferMode` actie `chunked`stelt de accommodatie in op:

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
