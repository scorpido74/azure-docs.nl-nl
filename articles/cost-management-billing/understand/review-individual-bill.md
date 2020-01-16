---
title: Uw afzonderlijke Azure-factuur bekijken
description: Meer informatie over het gebruik van uw factuur en resources en voor het controleren van de kosten voor uw afzonderlijke Azure-abonnement.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2019
ms.author: banders
ms.openlocfilehash: 1249758e5dd5b7e823c11890e57549195da22529
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987346"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>Zelf studie: uw afzonderlijke Azure-factuur controleren

Dit artikel helpt u bij het begrijpen en controleren van uw Azure-factuur. Voor elke facturerings periode ontvangt u normaal gesp roken een factuur per e-mail. De factuur is een representatie van uw Azure-factuur. De kosten informatie op de factuur is beschikbaar in het Azure Portal. In deze zelf studie gaat u uw factuur vergelijken met het gedetailleerde dagelijks gebruiks bestand en met de kosten analyse in de Azure Portal.

Deze zelf studie is alleen van toepassing op Azure-klanten met een individueel abonnement. Algemene afzonderlijke abonnementen zijn die met betalen per gebruik-tarieven die rechtstreeks zijn aangeschaft via de Azure-website.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Gefactureerde kosten vergelijken met het gebruiks bestand
> * Kosten en gebruik in kostprijs analyse vergelijken

## <a name="prerequisites"></a>Vereisten

U moet een betaald *micro soft Online Services-programma* facturerings account hebben. Het account wordt gemaakt wanneer u zich aanmeldt voor Azure via de Azure-website. Als u bijvoorbeeld een [account hebt met betalen naar](https://azure.microsoft.com/offers/ms-azr-0003p/) gebruik-tarieven of als u een [Visual Studio-abonnee](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)bent.

Facturen voor [gratis Azure-accounts](https://azure.microsoft.com/offers/ms-azr-0044p/) worden alleen gemaakt wanneer het maandelijkse credit bedrag wordt overschreden.

Deze moet meer dan 30 dagen na de dag waarop u bent geabonneerd op Azure. Azure factureert u aan het einde van uw factureringsperiode.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

- Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="compare-invoiced-charges-with-usage-file"></a>Gefactureerde kosten vergelijken met het gebruiks bestand

<a name="charges"></a>

De eerste stap voor het vergelijken van het gebruik en de kosten is het downloaden van uw factuur-en gebruiks bestanden. In het uitgebreide CSV-gebruiksbestand worden uw kosten weergegeven per factureringsperiode en dagelijks gebruik. Het bevat geen BTW-gegevens. Als u de bestanden wilt downloaden, moet u een account beheerder zijn of de rol eigenaar hebben.

Typ in het Azure Portal *abonnementen* in het zoekvak en klik vervolgens op [abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

![Naar abonnementen navigeren](./media/review-individual-bill/navigate-subscriptions.png)

Klik in de lijst met abonnementen op het abonnement.

Klik onder **facturering**op **facturen**.

Zoek in de lijst met facturen naar het bestand dat u wilt downloaden en klik vervolgens op het Download symbool. Mogelijk moet u de time span wijzigen om oudere facturen weer te geven. Het kan enkele minuten duren voordat het bestand met gebruiks gegevens en de factuur worden gegenereerd.

![Schermopname van de factureringsperioden, de downloadoptie en de totale kosten voor elke factureringsperiode](./media/review-individual-bill/download-invoice.png)

Klik in het venster gebruik downloaden en kosten op **CSV downloaden** en **factuur downloaden**.

![Schermopname met de pagina voor factuur en gebruik downloaden](./media/review-individual-bill/usageandinvoice.png)

Als deze **niet beschikbaar is** , zijn er verschillende redenen waarom u geen gebruiks gegevens of een factuur ziet:

- Het is minder dan 30 dagen geleden dat u zich geabonneerd hebt op Azure.
- Er is geen gebruik voor de facturerings periode.
- Er wordt nog geen factuur gegenereerd. Wacht tot het einde van de factureringsperiode.
- U bent niet gemachtigd om facturen te bekijken. U ziet mogelijk geen oude facturen tenzij u de account beheerder bent. Zie [Toegang tot factureringsgegevens voor Azure beheren](../manage/manage-billing-access.md) voor meer informatie over het verkrijgen van toegang tot factureringsgegevens.
- Als u een gratis proefversie gebruikt of een maandelijks tegoed met uw abonnement dat u niet overschrijdt, ontvangt u geen factuur tenzij u een Microsoft-klantovereenkomst hebt.

Vervolgens controleert u de kosten. Uw factuur bevat waarden voor belastingen en uw gebruiks kosten.

![Voor beeld van Azure-factuur](./media/review-individual-bill/invoice-usage-charge.png)

Open het CSV-gebruiks bestand dat u hebt gedownload. Tel aan het einde van het bestand de waarde voor alle items in de kolom *cost* .

![Voor beeld van een gebruiks bestand met opgetelde kosten](./media/review-individual-bill/usage-file-usage-charges.png)

 De opgetelde *kosten* waarde moet nauw keurig overeenkomen met de kosten voor de *gebruiks* kosten op uw factuur.

Uw gebruikskosten worden weergegeven op meterniveau. De volgende termen betekenen hetzelfde in de factuur en het gedetailleerde gebruiksbestand. De factureringscyclus op de factuur is bijvoorbeeld hetzelfde als de factureringsperiode die wordt weergegeven in het gedetailleerde gebruiksbestand.

| Factuur (PDF) | Gedetailleerd gebruik (CSV)|
| --- | --- |
|Factureringscyclus | BillingPeriodStartDate BillingPeriodEndDate |
|Name |Metercategorie |
|Type |Subcategorie van de meter |
|Bron |MeterName |
|Regio |MeterRegion |
|Verbruikt | Hoeveelheid |
|Inbegrepen |Inbegrepen hoeveelheid |
|Factureerbaar |Overschreden hoeveelheid |
|Tarief | EffectivePrice|
| Waarde | Kosten |

In het gedeelte **gebruiks kosten** van uw factuur wordt de totale waarde (kosten) weer gegeven voor elke meter die tijdens uw facturerings periode is verbruikt. In de volgende afbeelding ziet u bijvoorbeeld een gebruiks kosten voor de Azure Storage-service voor de bron *P10-schijven* .

![Factuur gebruikskosten](./media/review-individual-bill/invoice-usage-charges.png)

Filter in uw CSV-gebruiks bestand op naam *meter* voor de bijbehorende resource die op uw factuur wordt weer gegeven. Vervolgens de som van de *kosten* waarde voor items in de kolom. Hier volgt een voorbeeld.

![Waarde van de som van het gebruiks bestand voor de meting van de meter](./media/review-individual-bill/usage-file-usage-charge-resource.png)

De opgetelde *kosten* waarde moet nauw keurig overeenkomen met de kosten voor het *gebruik* van kosten voor de afzonderlijke resources die in rekening worden gebracht op uw factuur.

Zie [inzicht in uw Azure-factuur](understand-invoice.md) en [inzicht krijgen in uw uitgebreide Azure-gebruik](understand-usage.md)voor meer informatie.

## <a name="compare-charges-and-usage-in-cost-analysis"></a>Kosten en gebruik in kostprijs analyse vergelijken

Kosten analyse in het Azure Portal kan u ook helpen bij het controleren van uw kosten. Selecteer uw abonnement op de [pagina Abonnementen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in de Azure Portal om een snel overzicht te krijgen van uw gefactureerd gebruik en kosten. Klik vervolgens op **kosten analyse** en klik vervolgens in de lijst weer gaven op **factuur Details**.

![Voor beeld van factuur Details selecteren](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Selecteer vervolgens in de lijst datum bereik een periode voor de factuur. Voeg een filter toe voor het factuur nummer en selecteer vervolgens de InvoiceNumber die overeenkomt met de overeenkomst op uw factuur. In kosten analyse worden de kosten voor de gefactureerde items weer gegeven.

![Voor beeld van gefactureerde kosten Details in kostprijs analyse](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

De kosten die worden weer gegeven in de kosten analyse moeten nauw keurig overeenkomen met de kosten voor het *gebruik* van kosten voor de afzonderlijke resources die op uw factuur worden toegepast.

![Factuur gebruikskosten](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external"></a>Externe services worden afzonderlijk gefactureerd

Externe services of Marketplace-kosten zijn voor resources die zijn gemaakt door externe softwareleveranciers. Deze resources zijn beschikbaar voor gebruik via Azure Marketplace. Een Barracuda-firewall is bijvoorbeeld een Azure Marketplace-resource die wordt aangeboden door een externe partij. Alle kosten voor de firewall en de bijbehorende meters worden als externe servicekosten weergegeven.

Externe servicekosten worden afzonderlijk in rekening gebracht. Deze kosten worden niet weergegeven op uw Azure-factuur. Raadpleeg voor meer informatie [Inzicht in Azure-facturering voor externe servicekosten](understand-azure-marketplace-charges.md).

### <a name="resources-billed-by-usage-meters"></a>Resources die worden gefactureerd op basis van gebruiksmeters

Azure factureert niet rechtstreeks op basis van de resourcekosten. Kosten voor een resource worden berekend met behulp van een of meer meters. Meters worden gebruikt om het gebruik van een resource bij te houden gedurende de levensduur. Deze meters worden vervolgens gebruikt om de factuur te berekenen.

Wanneer u één Azure-resource maakt, zoals een virtuele machine, zijn er een of meer meter exemplaren gemaakt. Meters worden gebruikt om het gebruik van de resource gedurende een periode bij te houden. Elke meter verzendt gebruiksrecords die door Azure worden gebruikt om de factuur te berekenen.

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

U kunt de meters weer geven die zijn gebruikt voor het berekenen van uw factuur in het CSV-gebruiks bestand.

## <a name="payment"></a>Uw factuur betalen

Als u een credit card instelt als uw betalings wijze, wordt de betaling automatisch binnen 10 dagen na afloop van de facturerings periode in rekening gebracht. Op uw creditcardoverzicht wordt het regelitem weergegeven als **MSFT Azure**.

Zie [Een creditcard toevoegen, bijwerken of verwijderen voor Azure](../manage/change-credit-card.md) als u de creditcard die wordt gebruikt, wilt wijzigen.

Als u [betaalt per factuur](../manage/pay-by-invoice.md), verstuurt u uw betaling met de gegevens die onder aan uw factuur worden weergegeven.

Als u de status van uw betaling wilt controleren, [maakt u een ondersteuningsticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Gefactureerde kosten vergelijken met het gebruiks bestand
> * Kosten en gebruik in kostprijs analyse vergelijken

Voltooi de Snelstartgids om te beginnen met het gebruik van kosten analyse.

> [!div class="nextstepaction"]
> [Beginnen met kosten analyseren](../costs/quick-acm-cost-analysis.md)
