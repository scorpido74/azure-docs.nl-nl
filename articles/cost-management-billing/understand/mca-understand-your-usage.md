---
title: Bestand met Azure-gebruik en -kosten voor een Microsoft-klantovereenkomst
description: Lees hier alles over de secties van het CSV-bestand met gegevens van Azure-gebruik en -kosten voor uw factureringsprofiel.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 826217091b3297da0c068336ff67a0a58841716d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690456"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Terminologie in het bestand met Azure-gebruik en -kosten voor een Microsoft-klantovereenkomst

Dit artikel is van toepassing op een factureringsaccount voor een Microsoft-klantovereenkomst. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement).

Het CSV-bestand met gegevens van Azure-gebruik en -kosten bevat dagelijks gebruikskosten en gebruikskosten op meterniveau voor de huidige factureringsperiode.

Zie [Uw Azure-gebruik en -kosten weergeven en downloaden](download-azure-daily-usage.md) om dit bestand te downloaden. Het is beschikbaar in de CSV-bestandsindeling (met door komma's gescheiden waarden) die u kunt openen in een werkbladtoepassing.

Gebruikskosten zijn de totale **maandelijkse** kosten voor een abonnement. In de gebruikskosten wordt geen rekening gehouden met eventuele tegoeden of kortingen.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Wijzigingen voor gebruik en kosten van Azure EA

Als u een EA-klant bent, zult u merken dat de terminologie in het CSV-bestand met gegevens van Azure-gebruik voor uw factureringsprofiel verschillen van de terminologie die wordt gebruikt in het CSV-bestand met gegevens van het Azure EA-gebruik. In de onderstaande tabel zijn de gebruikte termen van EA gekoppeld aan die van het factureringsprofiel:

| CSV met gegevens van Azure EA-gebruik | CSV met Azure-gebruik en -kosten voor Microsoft-klantovereenkomst |
| --- | --- |
| Date | date |
| Month| date |
| Day | date |
| Year | date |
| Product | product |
| MeterId | meterId |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | kosten |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Tags | tags |
| StoreServiceIdentifier | N.v.t. |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Gedetailleerde termen en beschrijvingen

De volgende termen worden gebruikt in het bestand met gegevens van Azure-gebruik en -kosten.

Termijn | Beschrijving
--- | ---
InvoiceId | De unieke document-id die op de factuur-PDF wordt vermeld
previousInvoiceId | Verwijzing naar een oorspronkelijke factuur als dit regelitem een restitutie is
billingAccountName | Naam van het factureringsaccount
billingAccountId | Unieke id voor het hoofdfactureringsaccount
billingProfileId | Naam van het factureringsprofiel waaraan de gefactureerde kosten worden toegerekend
billingProfileName | Unieke id van het factureringsprofiel waaraan de gefactureerde kosten worden toegerekend
invoiceSectionId | Unieke id voor de factuursectie
invoiceSectionName | Naam van de factuursectie
costCenter | De kostenplaats die voor het abonnement is gedefinieerd voor het bijhouden van kosten (alleen beschikbaar in geopende factureringsperioden)
billingPeriodStartDate | De begindatum van de factureringsperiode waarvoor de factuur wordt gegenereerd
billingPeriodEndDate | De einddatum van de factureringsperiode waarvoor de factuur wordt gegenereerd
servicePeriodStartDate | De begindatum van de classificatieperiode waarin de prijzen voor de verbruikte of aangeschafte service zijn gedefinieerd en vergrendeld
servicePeriodEndDate | De einddatum van de classificatieperiode waarin de prijzen voor de verbruikte of aangeschafte service zijn gedefinieerd en vergrendeld
date | Voor kosten voor Azure en Marketplace op basis van gebruik is dit de classificatiedatum. Voor eenmalige aankopen (reserveringen, Marketplace) of vaste terugkerende kosten (ondersteuningsaanbiedingen) is dit de aankoopdatum.
serviceFamily | Servicereeks waarvan de service deel uitmaakt
productOrderId | Unieke id voor de productorder
productOrderName | Unieke naam voor de productorder
consumedService | Naam van de verbruikte service
meterId | De unieke id voor de meter
meterName | De naam van de meter
meterCategory | Naam van de classificatiecategorie voor de meter. Bijvoorbeeld *Cloudservices*, *Netwerken* enz.
meterSubCategory | Naam van de sub-classificatiecategorie voor de meter
meterRegion | Naam van de regio waar de meter voor de service beschikbaar is. Identificeert de locatie van het datacenter voor bepaalde services waarbij de prijs is gebaseerd op de locatie van het datacenter.
offer | Naam van de aangeschafte aanbieding
PayGPrice | Verkoopprijs voor de resource.
PricingModel | Id die aangeeft hoe de meter is geprijsd (waarden: op aanvraag, reservering, spot)
productId | Unieke id voor het product waarop de kosten betrekking hebben
product | Naam van het product waarop de kosten betrekking hebben
subscription ID | Unieke id voor het abonnement waarop de kosten betrekking hebben
subscriptionName | Naam van het abonnement waarop de kosten betrekking hebben
reservationId | Unieke id voor de aangeschafte reserveringsinstantie
reservationName | Naam van de aangeschafte reserveringsinstantie
publisherType | Type uitgever (waarden: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Uitgever voor Marketplace-services
resourceGroupId | Unieke id voor de resourcegroep die is gekoppeld aan de resource
resourceGroupName | Naam van de resourcegroep die is gekoppeld aan de resource
resourceId | Unieke id voor de resource-instantie
resourceType | Type resource-instantie
resourceLocation | Hiermee wordt de locatie van het datacenter aangegeven waarin de resource wordt uitgevoerd.
location | Genormaliseerde locatie van de resource als er verschillende resourcelocaties zijn geconfigureerd voor dezelfde regio's
quantity | Het aantal eenheden dat is aangeschaft of verbruikt
unitOfMeasure | De maateenheid voor facturering van de service. Computeservices worden bijvoorbeeld per uur gefactureerd.
chargeType | Het type kosten. Waarden: <ul><li>AsCharged-Usage: Kosten die het gevolg zijn van het gebruik van een Azure-service. Dit omvat het gebruik van virtuele machines dat niet in rekening word gebracht vanwege gereserveerde instanties.</li><li>AsCharged-PurchaseMarketplace: Eenmalige of vaste terugkerende kosten van Marketplace-aankopen</li><li>AsCharged-UsageMarketplace: Kosten voor Marketplace-services die worden gefactureerd op basis van verbruikseenheden</li></ul>
isAzureCreditEligible | Vlag die aangeeft of de kosten voor de service in aanmerking komen om te worden betaald met Azure-tegoed (waarden: True, False)
serviceInfo1 | Servicespecifieke metagegevens
serviceInfo2 | Verouderd veld met optionele, servicespecifieke metagegevens
additionalInfo | Aanvullende servicespecifieke metagegevens.
tags | Tags die u toewijst aan de resource

### <a name="make-sure-that-charges-are-correct"></a>Zorg ervoor dat de kosten juist zijn

Als u er zeker van wilt zijn dat de kosten in uw gedetailleerde gebruiksbestand juist zijn, kunt u ze controleren. Zie [Inzicht in de kosten op de factuur van uw Microsoft-klantovereenkomst](review-customer-agreement-bill.md).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Uw Microsoft Azure-factuur weergeven en downloaden](download-azure-invoice.md)
- [Uw Microsoft Azure-gebruik en -kosten weergeven en downloaden](download-azure-daily-usage.md)
