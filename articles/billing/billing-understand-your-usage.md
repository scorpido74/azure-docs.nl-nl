---
title: Informatie over uw gedetailleerde gebruik en kosten | Microsoft Docs
description: Meer informatie over het lezen en begrijpen van uw gedetailleerde gebruik en kosten
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 1842d32a838470d9b2af3a778c44c37464d32294
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "68954344"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Meer informatie over de gebruiksvoorwaarden in uw bestand voor Azure-gebruik en -kosten

Het gedetailleerde bestand voor Azure-gebruik en -kosten bevat het dagelijks beoordeelde gebruik op basis van overeengekomen tarieven, aankopen (bijvoorbeeld reserveringen en Marketplace-kosten) en restituties voor de opgegeven periode.
Krediet, btw of andere kosten of kortingen vallen niet onder deze kosten.
In de volgende tabel ziet u welke kosten voor elk accounttype zijn inbegrepen.

Accounttype | Azure-gebruik | Marketplace-gebruik | Aankopen | Restituties
--- | --- | --- | --- | ---
Enterprise Agreement (EA) | Ja | Ja | Ja | Nee
Microsoft-klantovereenkomst (MCA) | Ja | Ja | Ja | Ja
Betalen per gebruik (PAYG) | Ja | Ja | Nee | Nee

Zie [Meer informatie over kosten voor uw externe Azure-services](billing-understand-your-azure-marketplace-charges.md) voor meer informatie over Marketplace-orders (ook wel externe services genoemd).

Zie [Uw Azure-factuur en de dagelijkse gebruiksgegevens downloaden](billing-download-azure-invoice-daily-usage-date.md) voor downloadinstructies.
U kunt uw CSV-bestand met gebruik en kosten openen in Microsoft Excel of in een andere werkbladtoepassing.

## <a name="list-of-terms-and-descriptions"></a>Lijst met gebruiksvoorwaarden en beschrijvingen

In de volgende tabel worden de belangrijke gebruiksvoorwaarden beschreven die in de nieuwste versie van het bestand voor Azure-gebruik en -kosten worden gebruikt.
In deze lijst worden betalen per gebruik-accounts (PAYG), Enterprise Agreement-accounts (EA) en Microsoft-klantovereenkomstaccounts (MCA) besproken.

Termijn | Accounttype | Beschrijving
--- | --- | ---
AccountName | EA, PAYG | De weergavenaam van het EA-inschrijvingsaccount of het PAYG-factureringsaccount.
AccountOwnerId<sup>1</sup> | EA, PAYG | De unieke id voor het EA-inschrijvingsaccount of het PAYG-factureringsaccount.
AdditionalInfo | Alle | Servicespecifieke metagegevens. Bijvoorbeeld een installatiekopie voor een virtuele machine.
BillingAccountId<sup>1</sup> | Alle | Unieke id voor het hoofdfactureringsaccount.
BillingAccountName | Alle | De naam van het factureringsaccount.
BillingCurrency | Alle | De valuta die aan het factureringsaccount is gekoppeld.
BillingPeriod | EA, PAYG | De factureringsperiode voor de kosten.
BillingPeriodEndDate | Alle | De einddatum van de factureringsperiode.
BillingPeriodStartDate | Alle | De begindatum van de factureringsperiode.
BillingProfileId<sup>1</sup> | Alle | De unieke id van de EA-inschrijving, het PAYG-abonnement, het MCA-factureringsprofiel of het geconsolideerde AWS-account.
BillingProfileName | Alle | De naam van de EA-inschrijving, het PAYG-abonnement, het MCA-factureringsprofiel of het geconsolideerde AWS-account.
ChargeType | Alle | Hiermee wordt aangegeven of de kosten betrekking hebben op het gebruik (**Usage**), een aankoop (**Purchase**) of een restitutie (**Refund**).
ConsumedService | Alle | De naam van de service waaraan de kosten zijn gekoppeld.
CostCenter<sup>1</sup> | EA, MCA | De kostenplaats die voor het abonnement is gedefinieerd voor het bijhouden van de kosten (alleen beschikbaar in openstaande factureringsperioden voor MCA-accounts).
Kosten | EA, PAYG | Zie CostInBillingCurrency.
CostInBillingCurrency | MCA | De kostprijs van de kosten in de factureringsvaluta, vóór aftrek van krediet of btw.
CostInPricingCurrency | MCA | De kostprijs van de kosten in de prijsvaluta, vóór aftrek van krediet of btw.
Valuta | EA, PAYG | Zie BillingCurrency.
Date<sup>1</sup> | Alle | De datum waarop het gebruik of de aankoop zijn betaald.
EffectivePrice | Alle | De gemengde eenheidsprijs voor de periode. Het gemiddelde van de gemengde prijzen als gevolg van schommelingen in de eenheidsprijs, zoals gestaffelde lagen, waardoor de prijs daalt naarmate de hoeveelheid in de loop van de tijd toeneemt.
ExchangeRateDate | MCA | De datum waarop de wisselkoers is bepaald.
ExchangeRatePricingToBilling | MCA | De wisselkoers die is gebruikt om de kosten in de prijseenheid om te zetten in de factureringsvaluta.
Frequency | Alle | Geeft aan of kan worden verwacht dat de kosten worden herhaald. Kosten kunnen eenmalig in rekening worden gebracht (**OneTime**), herhaaldelijk per maand of per jaar (**Recurring**) of op basis van het gebruik (**UsageBased**).
InvoiceId | PAYG, MCA | De unieke document-id die op de factuur-PDF wordt vermeld.
InvoiceSection | MCA | Zie InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | De unieke id voor de EA-afdeling of het MCA-factuurgedeelte.
InvoiceSectionName | EA, MCA | De naam voor de EA-afdeling of het MCA-factuurgedeelte.
IsAzureCreditEligible | Alle | Geeft aan of de kosten in aanmerking komen om te worden betaald met Azure-tegoed (waarden: True, False).
Locatie | MCA | De locatie van het datacenter waarop de resource wordt uitgevoerd.
MeterCategory | Alle | De naam van de classificatiecategorie voor de meter. Bijvoorbeeld *Cloudservices* en *Netwerken*.
MeterId<sup>1</sup> | Alle | De unieke id voor de meter.
MeterName | Alle | De naam van de meter.
MeterRegion | Alle | De naam van de datacenterlocatie voor services waarvan de prijs wordt bepaald aan de hand van de locatie. Zie Locatie.
MeterSubCategory | Alle | De naam van de subclassificatiecategorie voor de meter.
OfferId<sup>1</sup> | Alle | De naam van de aangeschafte aanbieding.
PartNumber<sup>1</sup> | EA, PAYG | De id die wordt gebruikt om prijzen voor specifieke meters op te halen.
PlanName | EA, PAYG | De naam van het Marketplace-abonnement.
PreviousInvoiceId | MCA | Verwijzing naar de oorspronkelijke factuur als dit regelitem een restitutie is.
PricingCurrency | MCA | De valuta die wordt gebruikt bij beoordeling op basis van overeengekomen prijzen.
Product | Alle | De naam van het product.
ProductId<sup>1</sup> | MCA | De unieke id voor het product.
ProductOrderId | Alle | De unieke id voor de productorder.
ProductOrderName | Alle | De unieke naam voor de productorder.
PublisherName | Alle | De uitgever voor Marketplace-services.
PublisherType | Alle | Het type uitgever (waarden: **Azure**, **AWS**, **Marketplace**).
Aantal | Alle | Het aantal eenheden dat is aangeschaft of verbruikt.
ReservationId | EA, MCA | De unieke id voor de aangeschafte reserveringsinstantie.
ReservationName | EA, MCA | De naam voor de aangeschafte reserveringsinstantie.
ResourceGroup | Alle | De naam van de [resourcegroep](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) waarin de resource zich bevindt.
ResourceId<sup>1</sup> | Alle | De unieke id van de [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources)-resource.
ResourceLocation | Alle | De locatie van het datacenter waarop de resource wordt uitgevoerd. Zie Locatie.
ResourceName | EA, PAYG | De naam van de resource.
ResourceType | MCA | Het type resource-instantie.
ServiceFamily | MCA | De servicereeks waarvan de service deel uitmaakt.
ServiceInfo1 | Alle | Servicespecifieke metagegevens.
ServiceInfo2 | Alle | Een verouderd veld met optionele, servicespecifieke metagegevens.
ServicePeriodEndDate | MCA | De einddatum van de beoordelingsperiode waarin de prijzen voor de verbruikte of aangeschafte service zijn gedefinieerd en vergrendeld.
ServicePeriodStartDate | MCA | De begindatum van de beoordelingsperiode waarin de prijzen voor de verbruikte of aangeschafte service zijn gedefinieerd en vergrendeld.
SubscriptionId<sup>1</sup> | Alle | De unieke id voor het Azure-abonnement.
SubscriptionName | Alle | De naam van het Azure-abonnement.
Tags<sup>1</sup> | Alle | De tags die aan de resource zijn toegewezen. Dit is exclusief tags voor resourcegroepen. Kan worden gebruikt om kosten voor interne terugstorting te groeperen of te distribueren. Zie [Uw Azure-resources organiseren met tags](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) voor meer informatie.
Termijn | Alle | Hiermee wordt de geldigheidstermijn van de aanbieding weergegeven. Bijvoorbeeld: In het geval van gereserveerde instanties wordt 12 maanden als termijn weergegeven. Voor eenmalige aankopen of terugkerende aankopen is de termijn 1 maand (SaaS, Marketplace-ondersteuning). Dit is niet van toepassing op Azure-verbruik.
UnitOfMeasure | Alle | De maateenheid voor facturering van de service. Computeservices worden bijvoorbeeld per uur gefactureerd.
UnitPrice | EA, PAYG | De prijs per eenheid voor de kosten.

_<sup>**1**</sup> Velden die worden gebruikt om een unieke id te maken voor één kostenrecord._

Opmerking: het hoofdlettergebruik en de ruimte tussen accounttypen is mogelijk niet voor alle velden gelijk.
Oudere versies van gebruiksbestanden voor betalen per gebruik hebben aparte secties voor het overzicht en het dagelijkse gebruik.

### <a name="list-of-terms-from-older-apis"></a>Lijst met termen uit oudere API's
In de volgende tabel worden termen die in oudere API’s zijn gebruikt, gekoppeld aan de nieuwe termen. Raadpleeg de bovenstaande tabel voor die beschrijvingen.

Oude term | Nieuwe term
--- | ---
ConsumedQuantity | Aantal
IncludedQuantity | N.v.t.
InstanceId | ResourceId
Tarief | EffectivePrice
Eenheid | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>Controleer of de kosten juist zijn

Voor meer informatie over gedetailleerd gebruik en gedetailleerde kosten, leest u hoe u uw [betalen per gebruik-factuur](./billing-understand-your-bill.md) of [factuur voor uw Microsoft-klantenovereenkomst](billing-mca-understand-your-bill.md) moet interpreteren.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Uw Microsoft Azure-factuur weergeven en downloaden](billing-download-azure-invoice.md)
- [Uw Microsoft Azure-gebruik en -kosten weergeven en downloaden](billing-download-azure-daily-usage.md)
