---
title: Metrische gegevens weer geven met Azure Monitor
titleSuffix: Azure Digital Twins
description: Zie de metrische gegevens van Azure Digital Apparaatdubbels weer geven in Azure Monitor.
author: baanders
ms.author: baanders
ms.date: 7/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c6db571d64b0fd276519f15a3984848e80c4e18a
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387673"
---
# <a name="view-and-understand-azure-digital-twins-metrics"></a>Azure Digital Apparaatdubbels-metrische gegevens weer geven en begrijpen

Deze metrische gegevens geven u informatie over de status van Azure Digital Apparaatdubbels-resources in uw Azure-abonnement. Met de metrische gegevens van Azure Digital Apparaatdubbels kunt u de algemene status van de Azure Digital Apparaatdubbels-service en de gekoppelde resources beoordelen. Met deze gebruikers gerichte statistieken kunt u zien wat er gebeurt met uw Azure Digital Apparaatdubbels en kunt u de hoofd oorzaak van problemen analyseren zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

Metrische gegevens zijn standaard ingeschakeld. U kunt de metrische gegevens van de Azure Digital Apparaatdubbels bekijken via de [Azure Portal](https://portal.azure.com).

## <a name="how-to-view-azure-digital-twins-metrics"></a>De metrische gegevens van Azure Digital Apparaatdubbels weer geven

1. Maak een Azure Digital Apparaatdubbels-exemplaar. U kunt instructies vinden over het instellen van een Azure Digital Apparaatdubbels-instantie in [*How-to: een exemplaar en verificatie instellen*](how-to-set-up-instance-scripted.md).

2. Zoek uw Azure Digital Apparaatdubbels-exemplaar in de [Azure Portal](https:/portal.azure.com) (u kunt de pagina hiervoor openen door de naam ervan te typen in de zoek balk van de portal). 

    Selecteer in het menu van de instantie **metrische gegevens**.
   
    :::image type="content" source="media/how-to-view-metrics/azure-digital-twins-metrics.png" alt-text="Scherm afbeelding van de pagina met metrische gegevens voor Azure Digital Apparaatdubbels":::

    Op deze pagina worden de metrische gegevens weer gegeven voor uw Azure Digital Apparaatdubbels-exemplaar. U kunt ook aangepaste weer gaven van uw metrische gegevens maken door de items te selecteren die u wilt weer geven in de lijst.
    
3. U kunt ervoor kiezen om uw metrische gegevens te verzenden naar een Event Hubs-eind punt of een Azure Storage-account door te klikken op **Diagnostische instellingen** in het menu en vervolgens **Diagnostische instelling toevoegen**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Scherm opname van de pagina met Diagnostische instellingen en de knop die u wilt toevoegen":::

    Zie voor meer informatie over dit proces [*probleem oplossing: diagnostische gegevens instellen*](troubleshoot-diagnostics.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure Digital Apparaatdubbels-metrische gegevens en hoe u deze kunt gebruiken

Azure Digital Apparaatdubbels biedt diverse metrische gegevens om u een overzicht te geven van de status van uw exemplaar en de bijbehorende resources. U kunt ook informatie uit meerdere metrische gegevens combi neren om een grotere afbeelding van de status van uw exemplaar te tekenen. 

In de volgende tabellen worden de metrische gegevens beschreven die worden bijgehouden door elk Azure Digital Apparaatdubbels-exemplaar, en wordt uitgelegd hoe elke metriek in verhouding staat tot de algemene status van uw exemplaar.

#### <a name="api-request-metrics"></a>Metrische gegevens van API-aanvraag

Metrische gegevens met API-aanvragen:

| Gegevens | Weergave naam voor metrische gegevens | Eenheid | Aggregatietype| Beschrijving | Dimensies |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API-aanvragen (preview-versie) | Aantal | Totaal | Het aantal API-aanvragen voor digitale-Apparaatdubbels voor lezen, schrijven, verwijderen en query bewerkingen. |  Verificatie </br>Bewerking </br>Protocol </br>Statuscode </br>Klasse status code </br>Status tekst |
| ApiRequestsLatency | Latentie aanvragen van API (preview-versie) | Milliseconden | Average | De reactie tijd voor API-aanvragen. Dit is de tijd tussen het moment waarop de aanvraag wordt ontvangen door Azure Digital Apparaatdubbels totdat de service een geslaagd/mislukt resultaat verzendt voor digitale Apparaatdubbels lezen, schrijven, verwijderen en query bewerkingen. | Verificatie </br>Bewerking </br>Protocol |
| ApiRequestsFailureRate | Aantal mislukte API-aanvragen (preview-versie) | Percentage | Average | Het percentage van de API-aanvragen dat door de service wordt ontvangen voor uw exemplaar en die een interne fout (500) reactie code geven voor digitale Apparaatdubbels-Lees-, schrijf-, verwijder-en query bewerkingen. | Verificatie </br>Bewerking </br>Protocol </br>Statuscode </br>Klasse status code </br>Status tekst

#### <a name="routing-metrics"></a>Routerings metrieken

Metrische gegevens die moeten worden uitgevoerd met route ring:

| Gegevens | Weergave naam voor metrische gegevens | Eenheid | Aggregatietype| Beschrijving | Dimensies |
| --- | --- | --- | --- | --- | --- |
| Routering | Route ring (preview-versie) | Aantal | Totaal | Het aantal berichten dat wordt doorgestuurd naar een Azure-service voor eind punten, zoals Event hub, Service Bus of Event Grid. | Bewerking </br>Resultaat |
| RoutingLatency | Routerings latentie (preview-versie) | Milliseconden | Average | De tijd die is verstreken tussen een gebeurtenis die wordt gerouteerd van Azure Digital Apparaatdubbels naar het moment dat deze wordt geplaatst in de Azure-service voor eind punten, zoals Event hub, Service Bus of Event Grid. | Bewerking </br>Resultaat |
| RoutingFailureRate | Aantal mislukte routeringen (preview-versie) | Percentage | Average | Het percentage gebeurtenissen dat resulteert in een fout wanneer ze worden doorgestuurd van Azure Digital Apparaatdubbels naar een Azure-service voor eind punten, zoals Event hub, Service Bus of Event Grid. | Bewerking </br>Resultaat |

#### <a name="billing-metrics"></a>Metrische facturerings gegevens

Metrische gegevens met facturering:

>[!NOTE]
> Tijdens de preview-periode **is de facturerings kosten nul**. Hoewel deze metrische gegevens nog steeds worden weer gegeven in de lijst die kan worden geselecteerd, worden ze niet toegepast tijdens de preview en blijven ze nul totdat de service verder gaat dan de preview-versie.

| Gegevens | Weergave naam voor metrische gegevens | Eenheid | Aggregatietype| Beschrijving | Dimensies |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Facturering-API-bewerkingen (preview-versie) | Aantal | Totaal | Facturerings metriek voor het aantal API-aanvragen dat is gedaan voor de Azure Digital Apparaatdubbels-service. | `MeterId` |
| BillingQueryUnits | Facturerings query eenheden (preview-versie) | Aantal | Totaal | Het aantal query-eenheden, een intern berekende meting van het gebruik van de service resource, die wordt gebruikt om query's uit te voeren. Er is ook een helper-API beschikbaar voor het meten van query-eenheden: [klasse QueryChargeHelper](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) | `MeterId` |
| BillingMessagesProcessed | Verwerkte facturerings berichten (preview-versie) | Aantal | Totaal | Facturerings metriek voor het aantal berichten dat vanuit Azure Digital Apparaatdubbels naar externe eind punten wordt verzonden. | `MeterId` |

## <a name="dimensions"></a>Dimensies

Met dimensies kunt u meer informatie over de metrische gegevens vaststellen. Sommige para meters voor route ring bieden informatie per eind punt. De volgende tabel bevat mogelijke waarden voor deze dimensies.

| Dimensie | Waarden |
| --- | --- |
| Verificatie | OAuth |
| Bewerking (voor API-aanvragen) | Micro soft. DigitalTwins/DigitalTwins/verwijderen</br>Micro soft. DigitalTwins/DigitalTwins/schrijven</br>Micro soft. DigitalTwins/DigitalTwins/lezen </br>Micro soft. DigitalTwins/eventroutes/lezen </br>Micro soft. DigitalTwins/eventroutes/schrijven </br>Micro soft. DigitalTwins/eventroutes/verwijderen </br>Micro soft. DigitalTwins/modellen/lezen </br>Micro soft. DigitalTwins/modellen/schrijven </br>Micro soft. DigitalTwins/modellen/verwijderen </br>Micro soft. DigitalTwins/query/actie |
Bewerking (voor route ring) | Event Grid </br>Event Hub </br>Service Bus |
| Protocol | HTTPS |
| Resultaat | Geslaagd </br>Fout |
| Statuscode | 200, 404, 500, enzovoort. |
| Klasse status code | 2xx, 4xx, 5xx, enzovoort. |
| Status tekst | Er is een interne server fout, niet gevonden, enzovoort. |

## <a name="next-steps"></a>Volgende stappen

Zie [*probleem oplossing: diagnostische gegevens instellen*](troubleshoot-diagnostics.md)voor meer informatie over het beheren van vastgelegde metrische gegevens voor Azure Digital apparaatdubbels.

Nu u een overzicht van de metrische gegevens van de Azure Digital Apparaatdubbels hebt gezien, volgt u deze koppelingen voor meer informatie over het beheren van Azure Digital Apparaatdubbels:

* [*Instructies: de Azure Digital Apparaatdubbels-Api's en Sdk's gebruiken*](how-to-use-apis-sdks.md)
* [*Instructies: Aangepaste modellen beheren*](how-to-manage-model.md)
* [*Instructies: digitale apparaatdubbels beheren*](how-to-manage-twin.md)
* [*Instructies: de dubbele grafiek met relaties beheren*](how-to-manage-graph.md)
* [*Instructies: eind punten en routes beheren*](how-to-manage-routes.md)