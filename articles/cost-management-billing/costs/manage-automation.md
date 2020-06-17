---
title: Azure-kosten beheren met automatisering
description: In dit artikel wordt uitgelegd hoe u Azure-kosten kunt beheren met automatisering.
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449828"
---
# <a name="manage-costs-with-automation"></a>Kosten beheren met automatisering

U kunt Cost Management-automatisering gebruiken om een aangepaste set oplossingen te maken waarmee kostengegevens kunnen worden opgehaald en beheerd. In dit artikel worden algemene scenario's voor Cost Management-automatisering en beschikbare opties op basis van uw situatie behandeld. Als u wilt ontwikkelen met behulp van API's, worden voorbeelden van veelvoorkomende API-aanvragen weergegeven om uw ontwikkelproces te versnellen.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Ophalen van kostengegevens automatiseren voor offline analyse

Mogelijk moet u uw Azure-kostengegevens downloaden om deze samen te voegen met andere gegevenssets. Of u moet kostengegevens integreren in uw eigen systemen. Er zijn verschillende opties beschikbaar, afhankelijk van de hoeveelheid betrokken gegevens. U moet beschikken over Cost Management-machtigingen op het juiste bereik om API's en hulpprogramma's te kunnen gebruiken. Zie [Toegang tot gegevens toewijzen](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data) voor meer informatie.

## <a name="suggestions-for-handling-large-datasets"></a>Suggesties voor het verwerken van grote gegevenssets

Als uw organisatie over veel Azure-resources of -abonnementen beschikt, hebt u een grote hoeveelheid gebruiksgegevens. Excel kan vaak niet zulke grote bestanden laden. In deze situatie worden de volgende opties aanbevolen:

**Power BI**

Power BI wordt gebruikt om grote hoeveelheden gegevens op te nemen en af te handelen. Als u een Enterprise Agreement-klant bent, kunt u de Power BI-sjabloon-app gebruiken om de kosten voor uw factureringsaccount te analyseren. Het rapport bevat belangrijke weergaven die door klanten worden gebruikt. Zie [Azure-kosten analyseren met de app Power BI-sjabloon-app](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app) voor meer informatie.

**Power BI-gegevensconnector**

Als u uw gegevens dagelijks wilt analyseren, kunt u het beste de [Power BI-gegevensconnector](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management) gebruiken om gegevens op te halen voor gedetailleerde analyse. Alle rapporten die u maakt, worden bijgewerkt door de connector naarmate er meer kosten worden gegenereerd.

**Cost Management-exports**

Mogelijk hoeft u de gegevens niet dagelijks te analyseren. Als dit wel het geval is, kunt u de functie [Exports](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) van Cost Management gebruiken om gegevensexports naar een Azure Storage-account te plannen. Vervolgens kunt u de gegevens naar Power BI laden of in Excel analyseren als het bestand klein genoeg is. Exports zijn beschikbaar in Azure Portal of u kunt exports configureren met de [Exports-API](https://docs.microsoft.com/rest/api/cost-management/exports).

**API voor gedetailleerde gebruiksgegevens**

U kunt de [API voor gedetailleerde gebruiksgegevens](https://docs.microsoft.com/rest/api/consumption/usageDetails) gebruiken als u een kleine kostengegevensset hebt. Als u een grote hoeveelheid kostengegevens hebt, moet u de kleinste hoeveelheid gebruiksgegevens voor een periode aanvragen. Dit kunt u doen door een klein tijdsbereik op te geven of een filter in uw aanvraag te gebruiken. In een scenario waarin u bijvoorbeeld drie jaar aan kostengegevens nodig hebt, werkt de API beter wanneer u meerdere aanroepen voor verschillende tijdsbereiken maakt in plaats van één aanroep. Van daaruit kunt u de gegevens in Excel laden voor verdere analyse.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatisch ophalen met API voor gedetailleerde gebruiksgegevens

Met de [API voor gedetailleerde gebruiksgegevens](https://docs.microsoft.com/rest/api/consumption/usageDetails) kunt u eenvoudig ruwe, niet-samengevoegde kostengegevens ophalen die overeenkomen met uw Azure-factuur. De API is handig wanneer uw organisatie een programmatische oplossing voor het ophalen van gegevens nodig heeft. U kunt de API gebruiken als u kleinere gegevenssets wilt analyseren. Voor grotere gegevenssets moet u echter andere oplossingen gebruiken die eerder zijn geïdentificeerd. De gegevens in Gebruiksgegevens worden per meter per dag verstrekt. Dit wordt gebruikt bij het berekenen van uw maandelijkse factuur. De GA-versie (algemene beschikbaarheid) van de API's is `2019-10-01`. Gebruik `2019-04-01-preview` voor toegang tot de preview-versie voor reservering en Azure Marketplace-aankopen met de API's.

### <a name="usage-details-api-suggestions"></a>Suggesties van de API voor gedetailleerde gebruiksgegevens

**Aanvraagschema**

U wordt aangeraden _niet meer dan één aanvraag_ te maken met de API voor gedetailleerde gebruiksgegevens. Zie [Inzicht in gegevens van Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule) voor meer informatie over hoe vaak kostengegevens worden vernieuwd en hoe afronding wordt verwerkt.

**Bereiken op het hoogste niveau bepalen zonder te filteren**

Gebruik de API om alle gegevens op te halen die u nodig hebt voor het bereik op het hoogste niveau. Wacht totdat alle benodigde gegevens zijn opgenomen voordat u een filter, groepering of samengevoegde analyse uitvoert. De API is specifiek geoptimaliseerd om grote hoeveelheden niet-samengevoegde ruwe kostengegevens te leveren. Zie [Bereiken begrijpen en gebruiken](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes) in Cost Management voor meer informatie over het werken met bereiken. Wanneer u de benodigde gegevens voor een bereik hebt gedownload, gebruikt u Excel om de gegevens verder te analyseren met filters en draaitabellen.

## <a name="example-usage-details-api-requests"></a>Voorbeeld van API voor gedetailleerde gebruiksgegevens

De volgende voorbeeldaanvragen worden door Microsoft-klanten gebruikt voor het oplossen van veelvoorkomende scenario's.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Gebruiksgegevens voor een bereik tijdens een bepaald datumbereik ophalen

De gegevens die door de aanvraag worden geretourneerd, komen overeen met de datum waarop het gebruik is ontvangen door het factureringssysteem. Dit kan kosten van meerdere facturen bevatten.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>Gegevens van afgeschreven kosten ophalen

Als u de werkelijke kosten wilt zien omdat u de aankopen wilt zien op het moment dat ze worden samengevoegd, wijzigt u de *metrische waarde*  in `ActualCost` in de volgende aanvraag. Als u afgeschreven en werkelijke kosten wilt gebruiken, moet u de `2019-04-01-preview`-versie gebruiken. De huidige API-versie werkt op dezelfde wijze als de `2019-10-01`-versie, met uitzondering van het nieuwe type/metrisch-kenmerk en de gewijzigde eigenschapsnamen. Als u een Microsoft-klantovereenkomst hebt, zijn uw filters `startDate` en `endDate` in het volgende voorbeeld.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Grote kosten gegevenssets herhaaldelijk ophalen met Exports

De functie Exports is een oplossing voor het regelmatig plannen van dumps van de kostengegevens. Het wordt aanbevolen om niet-samengevoegde kostengegevens op te halen voor organisaties waarvan de gebruiksbestanden te groot zijn voor een betrouwbare aanroep en het downloaden van gegevens met behulp van de API voor gedetailleerde gebruiksgegevens. De gegevens worden opgeslagen in een Azure Storage-account naar keuze. Van daaruit kunt u ze in uw eigen systemen laden en naar behoefte laten analyseren door uw teams. Zie [Gegevens exporteren](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) om exports in Azure Portal te configureren.

## <a name="data-latency-and-rate-limits"></a>Gegevenslatentie en frequentielimieten

Het is raadzaam de API's niet vaker dan één keer per dag aan te roepen. Cost Management-gegevens worden elke vier uur vernieuwd, wanneer nieuwe gebruiksgegevens van Azure-resourceproviders worden ontvangen. Als u vaker aanroept, worden er geen aanvullende gegevens verstrekt. In plaats daarvan wordt de lading groter. Zie [Inzicht in gegevens van Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data) voor meer informatie over hoe vaak gegevens worden gewijzigd en hoe gegevenslatentie wordt verwerkt.

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Foutcode 429: het aantal aanroepen heeft de frequentielimieten overschreden

Om een consistente ervaring voor alle Cost Management-abonnees mogelijk te maken, zijn frequentielimieten ingesteld voor Cost Management API's. Wanneer u de limiet bereikt, ontvangt u de HTTP-statuscode `429: Too many requests`. De huidige doorvoerlimieten voor onze API's zijn als volgt:

- 30 aanroepen per minuut: dit gebeurt per bereik, gebruiker of toepassing.
- 200 aanroepen per minuut: dit gebeurt per tenant, gebruiker of toepassing.

## <a name="next-steps"></a>Volgende stappen

- [Azure-kosten analyseren met de Power BI-sjabloon-app](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).
- [Geëxporteerde gegevens maken en beheren](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) met Exports.
- Meer informatie over de [API voor gedetailleerde gebruiksgegevens](https://docs.microsoft.com/rest/api/consumption/usageDetails).