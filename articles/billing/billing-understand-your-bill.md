---
title: Meer informatie over uw Azure-factuur
description: Meer informatie over het gebruik van en de factuur voor uw Azure-abonnement.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 9486d56a723bb311c05ab7aa776060dfa9561aae
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223041"
---
# <a name="understand-your-microsoft-azure-bill"></a>Meer informatie over uw factuur voor Microsoft Azure
Om inzicht te krijgen in uw Azure-factuur, vergelijkt u deze met het bestand met de gedetailleerde dagelijkse gebruiksgegevens en de kostenbeheerrapporten in de Azure-portal.

Dit artikel is niet van toepassing op de volgende klanten:
- Azure-klanten met een Enterprise Agreement (EA-klanten). Als u een EA-klant bent, raadpleegt u[Meer informatie over uw factuur voor Azure-klanten met een Enterprise Agreement](billing-understand-your-bill-ea.md).
- Azure-klanten met een [Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement). Als u een Microsoft-klantovereenkomst hebt, raadpleegt u [Understand the Azure charges on your Microsoft Customer Agreement invoice](billing-mca-understand-your-bill.md) (Inzicht in de Azure-kosten op uw factuur voor Microsoft-klantovereenkomst).

Voor uitleg over facturering in het programma Azure Cloud Solution Provider (Azure CSP), met inbegrip van de factureringscyclus, prijzen en het gebruik, raadpleegt u: [Azure CSP Billing Overview](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/) (Factureringsoverzicht van Azure CSP).

## <a name="charges"></a>Uw kosten controleren

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Als er kosten op uw factuur staan waarover u meer informatie wilt, kunt u het gebruik en de kosten vergelijken met behulp van het gebruiksbestand of de Azure-portal.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Optie 1: Gebruik en kosten vergelijken met gebruiksbestand

In het uitgebreide CSV-gebruiksbestand worden uw kosten weergegeven per factureringsperiode en dagelijks gebruik. Om dit bestand te downloaden of weergeven, raadpleegt u [Get your Azure billing invoice and daily usage data](billing-download-azure-invoice-daily-usage-date.md) (Uw Azure-factuur en de dagelijkse gebruiksgegevens downloaden).

Uw gebruikskosten worden weergegeven op meterniveau. De volgende termen betekenen hetzelfde in de factuur en het gedetailleerde gebruiksbestand. De factureringscyclus op de factuur is bijvoorbeeld hetzelfde als de factureringsperiode die wordt weergegeven in het gedetailleerde gebruiksbestand.

 | Factuur (PDF) | Gedetailleerd gebruik (CSV)|
 | --- | --- |
|Factureringscyclus | Factureringsperiode |
 |Naam |De categorie van de meter |
 |Type |Subcategorie van de meter |
 |Resource |De naam van de meter |
 |Regio |De regio van de meter |
 |Verbruikt |Verbruikt aantal |
 |Inbegrepen |Inbegrepen hoeveelheid |
 |Factureerbaar |Overschreden hoeveelheid |

In het gedeelte **Gebruikskosten** van uw factuur ziet u de totale waarde voor elke meter die tijdens uw factureringsperiode is verbruikt. De volgende afbeelding toont bijvoorbeeld gebruikskosten voor de Azure Scheduler-service.

![Factuur gebruikskosten](./media/billing-understand-your-bill/1.png)

In het gedeelte **Overzicht** van uw gedetailleerde CSV-gebruiksbestand worden dezelfde kosten weergegeven. Het bedrag voor *Gebruikt* en de *Waarde* komen overeen met de factuur.

![Gebruikskosten in CSV](./media/billing-understand-your-bill/2.png)

Ga naar de sectie **Dagelijks gebruik** van het CSV-bestand voor een dagelijkse uitsplitsing van de kosten. Filter op *Scheduler* bij *Metercategorie*. U kunt zien op welke dagen de meter is gebruikt en hoeveel er is verbruikt. De gegevens voor de *Resource* en *Resourcegroep* worden ter vergelijking ook weergegeven. De totale waarden voor *Gebruikt* moeten overeenkomen met wat er op de factuur wordt weergegeven.

![De sectie Dagelijks gebruik in het CSV-bestand](./media/billing-understand-your-bill/3.png)

Als u de kosten per dag wilt berekenen, vermenigvuldigt u de bedragen voor *Gebruikt* met de waarde voor *Tarief* uit de sectie **Overzicht**.

Voor meer informatie zie:

- [Informatie over uw Azure-factuur](billing-understand-your-invoice.md)
- [Informatie over uw uitgebreide Azure-gebruik](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Optie 2: Het gebruik en de kosten vergelijken in de Azure-portal

U kunt ook de Azure-portal gebruiken om uw kosten te controleren. Als u een beknopt overzicht wilt krijgen van het gebruik en de kosten op uw factuur, bekijkt u de grafieken voor kostenbeheer.

1. Ga in de Azure-portal naar [Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecteer uw abonnement > **Kostenanalyse**.
1. Filter op **Periode**.
1. Om het vorige voorbeeld weer op te pakken, ziet u hier gebruikskosten voor de Azure Scheduler-service.

   ![Kostenanalyseweergave in Azure-portal](./media/billing-understand-your-bill/4.png)

1. Selecteer de rij met de kosten om de dagelijkse kostenanalyse weer te geven.

   ![Weergave van kostengeschiedenis in Azure-portal](./media/billing-understand-your-bill/5.png)

Raadpleeg voor meer informatie [Onverwachte kosten voorkomen met Azure-facturering en -kostenbeheer](billing-getting-started.md#costs).

## <a name="external"></a>Externe services worden afzonderlijk gefactureerd

Externe services of Marketplace-kosten zijn voor resources die zijn gemaakt door externe softwareleveranciers. Deze resources zijn beschikbaar voor gebruik via Azure Marketplace. Een Barracuda-firewall is bijvoorbeeld een Azure Marketplace-resource die wordt aangeboden door een externe partij. Alle kosten voor de firewall en de bijbehorende meters worden als externe servicekosten weergegeven.

Externe servicekosten worden afzonderlijk in rekening gebracht. Deze kosten worden niet weergegeven op uw Azure-factuur. Raadpleeg voor meer informatie [Inzicht in Azure-facturering voor externe servicekosten](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Resources die worden gefactureerd op basis van gebruiksmeters

Azure factureert niet rechtstreeks op basis van de resourcekosten. Kosten voor een resource worden berekend met behulp van een of meer meters. Meters worden gebruikt om het gebruik van een resource bij te houden gedurende de levensduur. Deze meters worden vervolgens gebruikt om de factuur te berekenen.

Wanneer u bijvoorbeeld één Azure-resource maakt, zoals een virtuele machine, worden er een of meer meterexemplaren gemaakt. Meters worden gebruikt om het gebruik van de resource gedurende een periode bij te houden. Elke meter verzendt gebruiksrecords die door Azure worden gebruikt om de factuur te berekenen.

Een virtuele machine (VM) die in Azure is gemaakt, kan bijvoorbeeld de volgende meters hebben om het gebruik bij te houden:

- Rekenuren
- Uren IP-adres
- Inkomende gegevensoverdracht
- Uitgaande gegevensoverdracht
- Standard - Beheerde schijf
- Standard - Beheerde schijfbewerkingen
- Standard - IO - Schijf
- Standard - IO - Lezen blok-blob
- Standard - IO - Schrijven blok-blob
- Standard - IO - Verwijderen blok-blob

Wanneer de virtuele machine wordt gemaakt, begint elke meter gebruiksrecords te verzenden. Dit gebruik en de metertarieven worden bijgehouden in het Azure-metersysteem.

## <a name="payment"></a>Uw factuur betalen

Als u een creditcard hebt ingesteld als uw betalingswijze, wordt de betaling automatisch binnen 10 dagen na het einde van de factureringsperiode in rekening gebracht. Op uw creditcardoverzicht wordt het regelitem weergegeven als **MSFT Azure**.

Zie [Een creditcard toevoegen, bijwerken of verwijderen voor Azure](billing-how-to-change-credit-card.md) als u de creditcard die wordt gebruikt, wilt wijzigen.

Als u [betaalt per factuur](billing-how-to-pay-by-invoice.md), verstuurt u uw betaling met de gegevens die onder aan uw factuur worden weergegeven.

Als u de status van uw betaling wilt controleren, [maakt u een ondersteuningsticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Tips voor kostenbeheer

- Schat kosten met behulp van de:
  - [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/)
  - [Berekening total cost of ownership (TCO)](https://aka.ms/azure-tco-calculator)
  - [Gedetailleerde prijsinformatie voor iedere service](https://azure.microsoft.com/pricing/)
- [Controleer uw gebruik en de kosten regelmatig in de Azure-portal](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u een vraag wilt stellen of hulp nodig hebt, maakt u een [ondersteuningsaanvraag](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Meer informatie

- [Uw Azure-factuur en de dagelijkse gebruiksgegevens ophalen](billing-download-azure-invoice-daily-usage-date.md)
- [Meer informatie over begrippen op uw factuur voor Microsoft Azure](billing-understand-your-invoice.md)
- [Meer informatie over begrippen voor gedetailleerd gebruik van Microsoft Azure](billing-understand-your-usage.md)
- [Kostenbeheer in Azure-portal](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Onverwachte kosten voorkomen met Azure-facturering en -kostenbeheer](billing-getting-started.md#costs)
