---
title: Inzicht in de begrippen in uw prijzenoverzicht voor een Microsoft-klantovereenkomst - Azure
description: Lees hoe u meer inzicht kunt krijgen in uw gebruik en facturen voor een Microsoft-klantovereenkomst.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 4d83228fbec395d604e5ce3f988d2a6157f21eed
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "67490653"
---
# <a name="terms-in-your-microsoft-customer-agreement-price-sheet"></a>Begrippen in uw prijzenoverzicht voor een Microsoft-klantovereenkomst

Dit artikel is van toepassing op een Azure-factureringsaccount voor een Microsoft-klantovereenkomst. [Controleer of u toegang hebt tot een Microsoft-klantovereenkomst](#check-access-to-a-microsoft-customer-agreement).

Als u een Eigenaar, Inzender, Lezer of Factuurbeheerder van een factureringsprofiel bent, kunt u het prijzenoverzicht van uw organisatie downloaden vanaf de Azure-portal. Zie [Prijzen voor uw organisatie weergeven en downloaden](billing-ea-pricing.md).

## <a name="terms-and-descriptions-in-your-price-sheet"></a>Begrippen en beschrijvingen in uw prijzenoverzicht

De volgende sectie beschrijft de belangrijkste begrippen die in het prijzenoverzicht van uw Microsoft-klantovereenkomst staan.

| **Veldnaam**   | **Beschrijving**   |
| --- | --- |
| billingAccountId  | Unieke identificatie voor het factureringsaccount.   |
| billingAccountName  | Naam van het factureringsaccount.  |
| billingProfileId  | Unieke identificatie voor het factureringsprofiel.   |
| billingProfileName  | Naam van het factureringsprofiel dat is ingesteld om facturen te ontvangen. De prijzen op het prijzenoverzicht zijn gekoppeld aan dit factureringsprofiel. |
| productOrderName  | Naam van het aangekochte productabonnement. |
| serviceFamily  | Type Azure-service, bijv.: berekening, analyse, veiligheid |
| Product  | Naam van het product waarmee de kosten worden berekend, bijv.: Basic SQL DB versus Standard SQL DB  |
| productId  | Unieke identificatie voor het product waarvan de meter wordt verbruikt. |
| unitOfMeasure  | Bepaalt de meeteenheid voor facturering van de service. Berekeningsservices worden bijvoorbeeld per uur gefactureerd. |
| meterId  | Unieke identificatie voor de meter. |
| meterName  | Naam van de meter. De meter vertegenwoordigt de implementeerbare resource van een Azure-service. |
| meterCategory  | Naam van de classificatiecategorie voor de meter. Bijvoorbeeld _Cloudservices_ en _Netwerken_ enz. |
| meterType  |  Naam van het metertype. |
| meterSubCategory  | Naam van de sub-classificatiecategorie voor de meter.  |
| meterRegion  | Naam van de regio waar de meter voor de service beschikbaar is. De datacenterlocatie voor bepaalde services waarbij de prijs is gebaseerd op de datacenterlocatie.    |
| tierId  | Verwijst naar de prijscategorie, indien van toepassing. Dit werkt in combinatie met tierMinimumUnits voor het instellen van gelaagde prijzen wanneer de prijzen variëren op basis van het aantal verbruikte eenheden.    |
| tierMinimumUnits  | Bepaalt de ondergrens van het categoriebereik waarvoor prijzen zijn vastgesteld. Als het bereik bijvoorbeeld 0 tot 100 is, is tierMinimumUnits 0.  |
| effectiveStartDate  | Begindatum waarop de prijs van kracht wordt. |
| effectiveEndDate  | Einddatum van de werkelijke prijs. |
| unitPrice  | Prijs per eenheid op het moment van facturering (niet de werkelijke gemengde prijs) specifiek voor een meter en productordernaam.  Opmerking: De eenheidsprijs is niet hetzelfde als de werkelijke prijs zoals aangegeven in de gedownloade gebruiksgegevens als het gaat om services met verschillende prijzen in verschillende categorieën.  In het geval van services met een meerlagige prijsstelling is de werkelijke prijs een gemengd tarief voor de verschillende categorieën, zonder dat er sprake is van een categoriespecifieke eenheidsprijs. De gemengde prijs of werkelijke prijs is de nettoprijs voor de verbruikte hoeveelheid in de verschillende categorieën (waarbij elke categorie een specifieke eenheidsprijs heeft). |
| basePrice  | De marktprijs op het moment dat de klant zich aanmeldt of dat de servicemeter wordt gestart na aanmelding.   |

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Toegang tot een Microsoft-klantovereenkomst controleren
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Prijzen voor uw organisatie weergeven en downloaden](billing-ea-pricing.md)
