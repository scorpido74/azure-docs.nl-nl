---
title: Privé aanbiedingen in micro soft Commercial Marketplace
description: Persoonlijke aanbiedingen in micro soft Commercial Marketplace voor app-en service-Publishers.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/19/2020
ms.author: dsindona
ms.openlocfilehash: e449f65ca65a679aa6c6823938fa561b84e38368
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82872393"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Persoonlijke aanbiedingen in micro soft Commercial Marketplace

Met persoonlijke aanbiedingen in [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) kunnen uitgevers plannen maken die alleen zichtbaar zijn voor doel klanten. In dit artikel vindt u meer informatie over de opties en voor delen van privé aanbiedingen.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Zakelijke deals met persoonlijke aanbiedingen ontgrendelen

Enter prise-klanten gebruiken vaker online markt plaatsen om cloud oplossingen te zoeken, te proberen en te kopen. Uitgevers kunnen nu met persoonlijke aanbiedingen gebruikmaken van Marketplace om persoonlijke oplossingen te delen met de doel klanten, met mogelijkheden die ondernemingen nodig hebben:

- Met de *overeengekomen prijzen* kunnen uitgevers kortingen en afwijkende prijzen uit openbaar beschik bare aanbiedingen uitbreiden.
- Met *persoonlijke voor* waarden kunnen uitgevers voor waarden aanpassen aan een specifieke klant.
- Met *gespecialiseerde configuraties* kunnen uitgevers hun virtual machines, Azure-toepassingen en SaaS-apps aanpassen aan de behoeften van een individuele klant. Met deze optie kunnen uitgevers ook preview-toegang bieden tot nieuwe product functies, voordat ze ruim uitgebreid worden gestart naar alle klanten.

Met persoonlijke aanbiedingen kunnen uitgevers profiteren van de schaal en wereld wijde Beschik baarheid van een open bare Marketplace, met de flexibiliteit en controle die nodig is om te onderhandelen en aangepaste deals en configuraties te leveren. Met deze functies wordt de deur van een krachtige Enter prise-acceptatie van Cloud marketplaces geopend. Ondernemingen kunnen nu kopen en verkopen op hun verwachte en aanbod wijze.

Persoonlijke aanbiedingen zijn nu beschikbaar voor virtuele machines, Azure-toepassing (geïmplementeerd als oplossings sjablonen of beheerde toepassingen) en SaaS-apps bieden. 

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-skus-and-plans"></a>Persoonlijke aanbiedingen maken met Sku's en abonnementen

Voor *nieuwe of bestaande aanbiedingen met open bare sku's of abonnementen*kunnen uitgevers eenvoudig nieuwe, persoonlijke variaties maken door nieuwe sku's of plannen te maken en ze als privé te markeren.  

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) ---> 

Persoonlijke Sku's en abonnementen zijn onderdelen van een aanbieding en zijn alleen zichtbaar en tevens door de doel klanten. Persoonlijke Sku's en abonnementen kunnen de basis installatie kopieën en/of de meta gegevens van de aanbieding die al zijn gepubliceerd voor een open bare SKU of abonnement hergebruiken. Met deze optie kunnen uitgevers meerdere persoonlijke variaties van een open bare aanbieding maken zonder dat ze meerdere versies van dezelfde basis installatie kopie hoeven te publiceren en meta gegevens te bieden. Voor alleen aanbiedingen voor virtuele machines en Azure-toepassingen, wanneer een persoonlijke SKU een basis installatie kopie deelt met een open bare SKU, worden wijzigingen in de basis installatie kopie van de aanbieding door gegeven aan alle open bare en persoonlijke Sku's met die basis installatie kopie.

Voor *nieuwe aanbiedingen die alleen particuliere sku's of abonnementen bevatten*, kunnen uitgevers hun aanbiedingen als een andere aanbieding maken en de sku's of plannen als privé markeren. De aanbiedingen die alleen persoonlijke Sku's of abonnementen hebben, kunnen niet worden gedetecteerd of toegankelijk via micro soft Commercial Marketplace of de [Azure Portal](https://azure.microsoft.com/features/azure-portal/) door klanten die niet aan de aanbieding zijn gekoppeld.

## <a name="targeting-customers-with-private-offers"></a>Klanten richten met persoonlijke aanbiedingen

Voor zowel nieuwe als bestaande persoonlijke aanbiedingen kunnen uitgevers klanten richten met behulp van abonnements-id's. Uitgevers die een virtuele machine of Azure-toepassing aanbieding gebruiken, kunnen de beschik baarheid van een privé-SKU beperken tot een afzonderlijke Azure-abonnements-ID of een CSV van Maxi maal 20.000 Azure-abonnement-Id's uploaden. Bij het gebruik van een SaaS-app-aanbieding kunnen uitgevers een Tenant-ID koppelen om de beschik baarheid van een privé-abonnement te beperken met behulp van de hand matige of CSV-upload methode.

Zodra een aanbieding is gecertificeerd en gepubliceerd, kunnen klanten binnen enkele minuten worden bijgewerkt of verwijderd uit de SKU of het abonnement met behulp van de functie Persoonlijke abonnementen synchroniseren. Met deze mogelijkheid kunnen uitgevers snel en eenvoudig de lijst met klanten waarop de privé-SKU of het plan wordt gepresenteerd, bijwerken zonder dat u de aanbieding opnieuw moet certificeren of publiceren.

## <a name="deploying-private-offers"></a>Persoonlijke aanbiedingen implementeren

Persoonlijke aanbiedingen kunnen alleen worden gedetecteerd via de [Azure Portal](https://azure.microsoft.com/features/azure-portal/) en worden niet weer gegeven in [Microsoft AppSource](https://appsource.microsoft.com/) of [Azure Marketplace](https://azuremarketplace.microsoft.com). Zie [uw publicatie optie bepalen](./determine-your-listing-type.md)voor meer informatie over het publiceren naar de verschillende Commercial Marketplace-winkel.

Zodra klanten zijn aangemeld bij de Azure Portal, kunnen ze het navigatie-element voor Marketplace selecteren om toegang te krijgen tot hun persoonlijke aanbiedingen. Persoonlijke aanbiedingen worden ook weer gegeven in Zoek resultaten en kunnen worden geïmplementeerd via opdracht regel-en Azure Resource Manager sjablonen zoals andere aanbiedingen.

![[Persoonlijke aanbiedingen]](./media/marketplace-publishers-guide/private-offer.png)

Persoonlijke aanbiedingen worden ook weer gegeven in Zoek resultaten. U ziet gewoon het logo ' persoonlijk '.

>[!Note]
>Privé aanbiedingen worden niet ondersteund met abonnementen die zijn gemaakt via een wederverkoper van het Cloud Solution Provider Program (CSP).

<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.

--->