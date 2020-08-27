---
title: Begrippen in uw prijzenoverzicht voor een Microsoft-klantovereenkomst - Azure
description: Lees hoe u meer inzicht kunt krijgen in uw gebruik en facturen voor een Microsoft-klantovereenkomst.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 6205867cbcb65a3b5720fefd1356b36bcd8c6838
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682719"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Begrippen in uw prijzenoverzicht voor een Microsoft-klantovereenkomst

Dit artikel is van toepassing op een Azure-factureringsaccount voor een Microsoft-klantovereenkomst. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement).

Als u een Eigenaar, Inzender, Lezer of Factuurbeheerder van een factureringsprofiel bent, kunt u het prijzenoverzicht van uw organisatie downloaden vanaf de Azure-portal. Zie [Prijzen voor uw organisatie weergeven en downloaden](ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Begrippen en beschrijvingen in uw prijzenoverzicht

De volgende sectie beschrijft de belangrijkste begrippen die in het prijzenoverzicht van uw Microsoft-klantovereenkomst staan.

| **Veldnaam**   | **Beschrijving**   |
| --- | --- |
| basePrice  | De marktprijs op het moment dat de klant zich aanmeldt of dat de servicemeter wordt gestart na aanmelding.   |
| billingAccountId  | Unieke identificatie voor het factureringsaccount.   |
| billingAccountName  | Naam van het factureringsaccount.  |
| billingCurrency | Valuta waarin kosten worden geregistreerd |
| billingProfileId  | Unieke identificatie voor het factureringsprofiel.   |
| billingProfileName  | Naam van het factureringsprofiel dat is ingesteld om facturen te ontvangen. De prijzen op het prijzenoverzicht zijn gekoppeld aan dit factureringsprofiel. |
| currency | Valuta waarin alle prijzen worden weergegeven. |
| discount | De prijskorting die wordt aangeboden voor de Graduation-laag, Free-laag, Inbegrepen hoeveelheid of Afgesproken kortingen, indien van toepassing. Wordt weergegeven als een percentage. |
| effectiveEndDate  | Einddatum van de werkelijke prijs. |
| effectiveStartDate  | Begindatum waarop de prijs van kracht wordt. |
| includedQuantity | Hoeveelheden van een specifieke service waar de klant recht op heeft, zonder incrementele kosten. |
| marketPrice | De huidige, geldende marktprijs voor een bepaalde service. |
| meterId  | Unieke identificatie voor de meter. |
| meterCategory  | Naam van de classificatiecategorie voor de meter. Bijvoorbeeld _Cloudservices_, _Netwerken_ enz. |
| meterName  | Naam van de meter. De meter vertegenwoordigt de implementeerbare resource van een Azure-service. |
| meterSubCategory  | Naam van de sub-classificatiecategorie voor de meter.  |
| meterType  |  Naam van het metertype. |
| meterRegion  | Naam van de regio waar de meter voor de service beschikbaar is. De datacenterlocatie voor bepaalde services waarbij de prijs is gebaseerd op de datacenterlocatie.    |
| Product  | Naam van het product waarmee de kosten worden berekend, bijv.: Basic SQL DB versus Standard SQL DB  |
| productId  | Unieke identificatie voor het product waarvan de meter wordt verbruikt. |
| productOrderName  | Naam van het aangekochte productabonnement. |
| serviceFamily  | Type Azure-service, bijv.: berekening, analyse, veiligheid |
| tierMinimumUnits  | Bepaalt de ondergrens van het categoriebereik waarvoor prijzen zijn vastgesteld. Als het bereik bijvoorbeeld 0 tot 100 is, is tierMinimumUnits 0.  |
| unitOfMeasure  | Bepaalt de meeteenheid voor facturering van de service. Computeservices worden bijvoorbeeld per uur gefactureerd. |
| unitPrice  | Prijs per eenheid op het moment van facturering (niet de werkelijke gemengde prijs) specifiek voor een meter en productordernaam.  Opmerking: De eenheidsprijs is niet hetzelfde als de werkelijke prijs zoals aangegeven in de gedownloade gebruiksgegevens als het gaat om services met verschillende prijzen in verschillende categorieën.  In het geval van services met een meerlagige prijsstelling is de werkelijke prijs een gemengd tarief voor de verschillende categorieën, zonder dat er sprake is van een categoriespecifieke eenheidsprijs. De gemengde prijs of werkelijke prijs is de nettoprijs voor de verbruikte hoeveelheid in de verschillende categorieën (waarbij elke categorie een specifieke eenheidsprijs heeft). |


## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Prijzen voor uw organisatie weergeven en downloaden](ea-pricing.md)
