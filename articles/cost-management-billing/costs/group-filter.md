---
title: Groeperings-en filteropties in Azure Cost Management
description: In dit artikel wordt uitgelegd hoe u de groeperings- en filteropties in Azure Cost Management kunt gebruiken.
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: f67cc019e8e7d4da4b0db1c0bbfdb6ee1cd495d7
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561866"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>Groeperings- en filteropties in kostenanalyse

Kostenanalyse heeft veel opties voor groeperen en filteren. In dit artikel wordt uitgelegd wanneer u deze kunt gebruiken.

Als u een video wilt bekijken over de opties voor groeperen en filteren, bekijkt u de video [Cost Management-rapportage per dimensie en tag](https://www.youtube.com/watch?v=2Vx7V17zbmk). Als u andere video’s wilt bekijken, gaat u naar het [YouTube-kanaal voor Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>Groeperings- en filteropties

De volgende tabel bevat een aantal van de meest gebruikte groeperings- en filteropties in de kostenanalyse en informatie over wanneer u deze kunt gebruiken.

| Eigenschap | Wanneer gebruikt u dit? | Opmerkingen |
| --- | --- | --- |
| **Beschikbaarheidszones** | AWS-kosten opsplitsen per beschikbaarheidszone. | Alleen van toepassing op AWS-bereiken en beheergroepen. Azure-gegevens omvatten geen beschikbaarheidszone en worden als **Niet van toepassing** weergegeven. |
| **Factureringsperiode** | Kosten voor betalen per gebruik opsplitsen per maand dat ze zijn (of worden) gefactureerd. | Gebruik **Factureringsperiode** om een nauwkeurige weergave van gefactureerde kosten voor betalen per gebruik te zien. Voeg twee extra dagen vóór en na de factureringsperiode toe als u wilt filteren op een aangepast datumbereik. Door u te beperken tot de exacte datums van de factureringsperiode, komt het datumbereik niet overeen met dat op de factuur. De kosten van alle facturen in de factureringsperiode worden weergegeven. Gebruik **Factuur-ID** om op een specifieke factuur te filteren. Alleen van toepassing op Betalen per gebruik-abonnementen omdat EA en MCA per kalendermaand worden gefactureerd. Bij EA/MCA-accounts kunnen kalendermaanden worden gebruikt in de datumkiezer of kan maandelijkse granulariteit worden gebruikt om hetzelfde doel te bereiken. |
| **Kostentype** | Gebruikskosten, aankoopkosten, restitutiekosten en kosten voor ongebruikte reserveringen opsplitsen. | Reserveringsaankopen en restituties zijn alleen beschikbaar wanneer u werkelijke kosten gebruikt en niet wanneer u afgeschreven kosten gebruikt. Kosten voor ongebruikte reserveringen zijn alleen beschikbaar wanneer u de afgeschreven kosten bekijkt. |
| **Afdeling** | Kosten opsplitsen per EA-afdeling. | Alleen beschikbaar voor EA- en beheergroepen. Betalen per gebruik-abonnementen hebben geen afdeling en worden weergegeven als **Niet van toepassing** of **Niet-toegewezen**. |
| **Inschrijvingsaccount** | Kosten opsplitsen per eigenaar van EA-account. | Alleen beschikbaar voor EA-factureringsaccounts, afdelingen en beheergroepen. Betalen per gebruik-abonnementen hebben geen EA-inschrijvingsaccounts en worden weergegeven als **Niet van toepassing** of **Niet-toegewezen**. |
| **Frequentie** | Op gebruik gebaseerde, eenmalige en terugkerende kosten opsplitsen. | |
| **Factuur-id** | Kosten opsplitsen per gefactureerde factuur. | Niet-gefactureerde kosten hebben nog geen factuur-id en EA-kosten bevatten geen factuurgegevens, zij worden daarom weergegeven als **Niet van toepassing**.  |
| **Meter** | Kosten opsplitsen per gebruiksmeter. | Aankopen en het gebruik van Marketplace worden weergegeven als **Niet van toepassing**. Raadpleeg **Kostentype** om aankopen aan te duiden en **Type van uitgever** om kosten voor Marketplace aan te duiden. |
| **Bewerking** | Kosten voor AWS opsplitsen per bewerking. | Alleen van toepassing op AWS-bereiken en beheergroepen. Azure-gegevens omvatten geen bewerking en worden als **Niet van toepassing** weergegeven, gebruik in de plaats daarvan **Meter**. |
| **Prijsmodel** | Kosten opsplitsen per on-demand-gebruik, reservering of spotgebruik. | Aankopen worden weergegeven als **OnDemand-** . Als u **Niet van toepassing** ziet, groepeert u op **Reservering** om te bepalen of het bij het gebruik om een reservering gaat of om on-demand-gebruik, en groepeert u op **Kostentype** als het om aankopen gaat.
| **Provider** | Kosten opsplitsen per kosten voor AWS en voor Azure. | Alleen beschikbaar voor beheergroepen. |
| **Type van uitgever** | De kosten voor AWS, Azure en Marketplace opsplitsen. |  |
| **Reservering** | Kosten opsplitsen per reservering. | Alle gebruik of aankopen die niet aan een reservering zijn gekoppeld, worden weergegeven als **Niet van toepassing**. Groeperen op **Type van uitgever** om andere aankopen via Azure, AWS of Marketplace aan te duiden. |
| **Resource** | Kosten opsplitsen per resource. | Aankopen worden weergegeven als **Niet van toepassing** omdat ze worden toegepast op het niveau van een EA-/Betalen per gebruik-factureringsrekening of MCA-factureringsprofiel en niet aan een specifieke resource zijn gekoppeld. Groeperen op **Type van uitgever** om andere aankopen via Azure, AWS of Marketplace aan te duiden. |
| **Resourcegroep** | Kosten opsplitsen per resourcegroep. | Aankopen, tenantresources die niet zijn gekoppeld aan abonnementen, abonnementsresources die niet zijn geïmplementeerd naar een resourcegroep en klassieke resources hebben geen resourcegroep en worden weergegeven als **Overige**, **Klassieke services**, **$system** of **Niet van toepassing**. |
| **Resourcetype** | Kosten opsplitsen per resourcetype. | Aankopen en Klassieke services hebben geen Azure Resource Manager-resourcetype en worden weergegeven als **Overige**, **Klassieke services** of **Niet van toepassing**. |
| **Resourcelocatie** | Kosten opsplitsen per locatie of regio. | Aankopen en het gebruik van Marketplace kunnen worden weergegeven als **Niet-toegewezen**, **Onbekend**, **Toewijzing opgeheven** of **Niet van toepassing**. |
| **Servicenaam** of **metercategorie** | Kosten opsplitsen per Azure-service. | Aankopen en het gebruik van Marketplace worden weergegeven als **Niet van toepassing** of **Niet-toegewezen**. |
| **Servicelaag** of **Subcategorie van de meter** | Kosten opsplitsen per subclassificatie van de Azure-gebruiksmeter. | Aankopen en het gebruik van Marketplace worden weergegeven als **Niet van toepassing** of **Niet-toegewezen**. |
| **Abonnement** | Kosten opsplitsen per Azure-abonnement en gekoppeld AWS-account. | Aankopen en tenantresources worden mogelijk als **Niet van toepassing** weergegeven. |
| **Tag** | Kosten opsplitsen per tagwaarde voor een specifieke tagsleutel. | Tags zijn niet beschikbaar voor: aankopen, tenantresources die niet zijn gekoppeld aan abonnementen, abonnementsresources die niet zijn geïmplementeerd naar een resourcegroep, of klassieke resources. Sommige services bevatten geen tags in gebruiksgegevens. Meer informatie over de [ondersteuning voor tags voor elk resourcetype](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support). |

Zie [Meer informatie over de gebruiksvoorwaarden in uw bestand voor Azure-gebruik en -kosten](../understand/understand-usage.md) voor meer informatie over de gebruiksvoorwaarden.

## <a name="next-steps"></a>Volgende stappen

- [Beginnen met kosten analyseren](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis).
