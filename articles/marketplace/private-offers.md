---
title: Persoonlijke aanbiedingen | Azure Marketplace
description: Persoonlijke aanbiedingen in azure Marketplace voor app-en service-uitgevers.
services: Azure, Marketplace, Compute
author: qianw211
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/1/2018
ms.author: pabutler
ms.openlocfilehash: 16903d4f9b1bad8a588b1a234ac84bc9c0150c4a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827850"
---
# <a name="private-offers"></a>Persoonlijke aanbiedingen

Met persoonlijke aanbiedingen op [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) kunnen uitgevers sku's maken die alleen zichtbaar zijn voor doel klanten.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Zakelijke deals met persoonlijke aanbiedingen ontgrendelen

Enter prise-klanten gebruiken vaker online markt plaatsen om cloud oplossingen te zoeken, te proberen en te kopen. Uitgevers kunnen nu met persoonlijke aanbiedingen gebruikmaken van Marketplace om persoonlijke oplossingen te delen met de doel klanten, met mogelijkheden die ondernemingen nodig hebben:

- Met de *overeengekomen prijzen* kunnen uitgevers kortingen en afwijkende prijzen uit openbaar beschik bare aanbiedingen uitbreiden.
- Met *persoonlijke voor* waarden kunnen uitgevers voor waarden aanpassen aan een specifieke klant.
- Met *gespecialiseerde configuraties* kunnen uitgevers hun virtual machines, Azure-toepassingen en SaaS-apps aanpassen aan de behoeften van een individuele klant. Met deze optie kunnen uitgevers ook preview-toegang bieden tot nieuwe product functies, voordat ze ruim uitgebreid worden gestart naar alle klanten.

Met persoonlijke aanbiedingen kunnen uitgevers profiteren van de schaal en wereld wijde Beschik baarheid van een open bare Marketplace, met de flexibiliteit en controle die nodig is om te onderhandelen en aangepaste deals en configuraties te leveren. Met deze functies wordt de deur van een krachtige Enter prise-acceptatie van Cloud marketplaces geopend.  Ondernemingen kunnen nu kopen en verkopen op hun verwachte en aanbod wijze.

Persoonlijke aanbiedingen zijn nu beschikbaar voor virtuele machines, Azure-toepassing (geïmplementeerd als oplossings sjablonen of beheerde toepassingen) en SaaS-apps bieden. Net als bij open bare aanbiedingen kunnen persoonlijke aanbiedingen worden gemaakt en beheerd via de [Cloud Partner-Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md).  Klanten kunnen binnen enkele minuten toegang krijgen of ingetrokken voor privé-aanbiedingen.

## <a name="creating-private-offers-using-skus-and-plans"></a>Persoonlijke aanbiedingen maken met Sku's en abonnementen

Voor *nieuwe of bestaande aanbiedingen met open bare sku's of abonnementen*kunnen uitgevers eenvoudig nieuwe, persoonlijke variaties maken door nieuwe sku's of plannen te maken en ze als privé te markeren.  [Persoonlijke sku's](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) en abonnementen zijn onderdelen van een aanbieding en zijn alleen zichtbaar en tevens door de doel klanten. Persoonlijke Sku's en abonnementen kunnen de basis installatie kopieën en/of de meta gegevens van de aanbieding die al zijn gepubliceerd voor een open bare SKU of abonnement hergebruiken. Met deze optie kunnen uitgevers meerdere persoonlijke variaties van een open bare aanbieding maken zonder dat ze meerdere versies van dezelfde basis installatie kopie hoeven te publiceren en meta gegevens te bieden. Voor alleen aanbiedingen voor virtuele machines en Azure-toepassingen, wanneer een persoonlijke SKU een basis installatie kopie deelt met een open bare SKU, worden wijzigingen in de basis installatie kopie van de aanbieding door gegeven aan alle open bare en persoonlijke Sku's met die basis installatie kopie.

Voor *nieuwe aanbiedingen die alleen particuliere sku's of abonnementen bevatten*, kunnen uitgevers hun aanbiedingen als een andere aanbieding maken en de sku's of plannen als privé markeren. De aanbiedingen die alleen persoonlijke Sku's of abonnementen hebben, kunnen niet worden gedetecteerd of toegankelijk via [Azure Marketplace](https://azuremarketplace.microsoft.com) of de [Azure Portal](https://azure.microsoft.com/features/azure-portal/) door klanten die niet aan de aanbieding zijn gekoppeld.

## <a name="targeting-customers-with-private-offers"></a>Klanten richten met persoonlijke aanbiedingen
Voor zowel nieuwe als bestaande persoonlijke aanbiedingen kunnen uitgevers klanten richten met behulp van abonnements-id's. Uitgevers die een virtuele machine of Azure-toepassing aanbieding gebruiken, kunnen de beschik baarheid van een privé-SKU beperken tot een afzonderlijke Azure-abonnements-ID of een CSV van Maxi maal 20.000 Azure-abonnement-Id's uploaden. Bij het gebruik van een SaaS-app-aanbieding kunnen uitgevers een Azure-abonnements-ID of een Tenant-ID koppelen om de beschik baarheid van een privé-abonnement te beperken met behulp van de hand matige of CSV-upload benadering.

Zodra een aanbieding is gecertificeerd en gepubliceerd, kunnen klanten binnen enkele minuten worden bijgewerkt of verwijderd uit de SKU of het abonnement met behulp van de functie Persoonlijke abonnementen synchroniseren. Met deze mogelijkheid kunnen uitgevers snel en eenvoudig de lijst met klanten waarop de privé-SKU of het plan wordt gepresenteerd, bijwerken zonder de aanbieding opnieuw te certificeren of opnieuw te publiceren.

## <a name="deploying-private-offers"></a>Persoonlijke aanbiedingen implementeren

Persoonlijke aanbiedingen kunnen alleen worden gedetecteerd via de [Azure Portal](https://azure.microsoft.com/features/azure-portal/) en worden niet weer gegeven via [Azure Marketplace](https://azuremarketplace.microsoft.com). Zodra klanten zijn aangemeld bij de Azure Portal, kunnen ze het navigatie-element voor Marketplace selecteren om toegang te krijgen tot hun persoonlijke aanbiedingen. Persoonlijke aanbiedingen worden ook weer gegeven in Zoek resultaten en kunnen worden geïmplementeerd via opdracht regel-en Azure Resource Manager sjablonen zoals andere aanbiedingen.

![[Persoonlijke aanbiedingen]](./media/marketplace-publishers-guide/private-offer.png)

Persoonlijke aanbiedingen worden ook weer gegeven in Zoek resultaten. Bekijk gewoon het logo ' persoonlijk '.

>[!Note]
>Privé aanbiedingen worden niet ondersteund met abonnementen die zijn gemaakt via een wederverkoper van het Cloud Solution Provider Program (CSP).

## <a name="next-steps"></a>Volgende stappen

Volg de stappen in de hand leiding voor [Private sku's en plannen](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) om te beginnen met het gebruik van privé aanbiedingen.
