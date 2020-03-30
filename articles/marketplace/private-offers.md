---
title: Particuliere aanbiedingen | Azure Marketplace
description: Privéaanbiedingen in de Azure Marketplace voor app- en service-uitgevers.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/1/2018
ms.author: dsindona
ms.openlocfilehash: 67aba077304117ad357d4e004ce7bdb25ac58352
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285041"
---
# <a name="private-offers"></a>Privéaanbiedingen

Met privéaanbiedingen op [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) kunnen uitgevers SKU's maken die alleen zichtbaar zijn voor gerichte klanten.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Sluit zakelijke deals met privéaanbiedingen vrij

Zakelijke klanten gebruiken steeds vaker online marktplaatsen om cloudoplossingen te vinden, uit te proberen en te kopen. Nu met privéaanbiedingen kunnen uitgevers marketplace gebruiken om op maat gemaakte oplossingen privé te delen met gerichte klanten met mogelijkheden die bedrijven nodig hebben:

- *Met onderhandelde prijzen* kunnen uitgevers kortingen en prijzen buiten de lijst uitbreiden van openbaar beschikbare aanbiedingen.
- *Met privévoorwaarden* kunnen uitgevers de algemene voorwaarden afstemmen op een specifieke klant.
- *Met gespecialiseerde configuraties* kunnen uitgevers hun virtuele machines, Azure-toepassingen en SaaS-apps aanpassen aan de behoeften van een individuele klant. Met deze optie kunnen uitgevers ook preview-toegang tot nieuwe productfuncties bieden, voordat ze breder worden gestart voor alle klanten.

Met privéaanbiedingen kunnen uitgevers profiteren van de schaal en wereldwijde beschikbaarheid van een openbare marktplaats, met de flexibiliteit en controle die nodig zijn om te onderhandelen en aangepaste deals en configuraties te leveren. Samen openen deze functies de deur naar een sterke acceptatie van cloudmarkten door bedrijven.  Ondernemingen kunnen nu kopen en verkopen op manieren die ze verwachten en de vraag.

Privéaanbiedingen zijn nu beschikbaar voor Virtual Machine, Azure Application (geïmplementeerd als oplossingssjablonen of beheerde toepassingen) en SaaS Apps-aanbiedingen. Net als openbare aanbiedingen kunnen privéaanbiedingen worden gemaakt en beheerd via de [Cloud Partner Portal.](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)  Klanten kunnen binnen enkele minuten toegang krijgen tot privé-aanbiedingen of ingetrokken.

## <a name="creating-private-offers-using-skus-and-plans"></a>Privéaanbiedingen maken met SKU's en plannen

Voor *nieuwe of bestaande aanbiedingen met openbare SKU's of plannen*kunnen uitgevers eenvoudig nieuwe, privévariaties maken door nieuwe SKU's of plannen te maken en ze als privé te markeren.  [Particuliere SKU's](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) en plannen zijn onderdelen van een aanbod en zijn alleen zichtbaar en purchasable door de beoogde klanten. Privé-SKU's en -plannen kunnen de basisafbeeldingen en/of metadata die al zijn gepubliceerd voor een openbare SKU of -abonnement opnieuw gebruiken. Met deze optie kunnen uitgevers meerdere privévariaties van een openbaar aanbod maken zonder dat ze meerdere versies van dezelfde basisafbeelding hoeven te publiceren en metagegevens hoeven aan te bieden. Voor virtual machine- en Azure-toepassingsaanbiedingen worden alleen aangeboden wanneer een privé-SKU een basisafbeelding deelt met een openbare SKU, worden eventuele wijzigingen in de basisafbeelding van de aanbieding vooralle openbare en particuliere SKU's verspreid met behulp van die basisafbeelding.

Voor *nieuwe aanbiedingen die alleen privé SKU's of plannen bevatten,* kunnen uitgevers hun aanbiedingen maken als elke andere aanbieding en vervolgens de SKU's of plannen als privé markeren. De aanbiedingen die alleen privé SKU's of -abonnementen hebben, zijn niet vindbaar of toegankelijk via [Azure Marketplace](https://azuremarketplace.microsoft.com) of de [Azure-portal](https://azure.microsoft.com/features/azure-portal/) door klanten die niet aan de aanbieding zijn gekoppeld.

## <a name="targeting-customers-with-private-offers"></a>Klanten targeten met privéaanbiedingen
Voor zowel nieuwe als bestaande privéaanbiedingen kunnen uitgevers klanten targeten met abonnements-id's. Uitgevers die een Virtual Machine- of Azure-toepassingsaanbieding gebruiken, kunnen de beschikbaarheid van een privé-SKU beperken tot een afzonderlijke Azure-abonnements-id of een CSV van maximaal 20.000 Azure-abonnements-id's uploaden. Tijdens het gebruik van een privéaanbieding voor SaaS-app kunnen uitgevers een Azure-abonnements-id of een tenant-id koppelen om de beschikbaarheid van een privé-abonnement te beperken, met behulp van de handmatige of CSV-uploadbenadering.

Zodra een aanbieding is gecertificeerd en gepubliceerd, kunnen klanten binnen enkele minuten worden bijgewerkt of verwijderd uit de SKU of plannen met de functie Privéabonnementen synchroniseren. Met deze mogelijkheid kunnen uitgevers snel en eenvoudig de lijst met klanten bijwerken waaraan de privé-SKU of het privé-plan wordt gepresenteerd zonder de aanbieding opnieuw te certificeren of opnieuw te publiceren.

## <a name="deploying-private-offers"></a>Privéaanbiedingen implementeren

Privéaanbiedingen zijn alleen vindbaar via de [Azure-portal](https://azure.microsoft.com/features/azure-portal/) en worden niet gepresenteerd via [Azure Marketplace.](https://azuremarketplace.microsoft.com) Zodra ze zijn aangemeld bij de Azure-portal, kunnen klanten het Marketplace-navigatieelement selecteren om toegang te krijgen tot hun privéaanbiedingen. Privéaanbiedingen worden ook weergegeven in zoekresultaten en kunnen worden geïmplementeerd via opdrachtregel- en Azure Resource Manager-sjablonen, net als alle andere aanbiedingen.

![[Privé-aanbiedingen]](./media/marketplace-publishers-guide/private-offer.png)

Privéaanbiedingen worden ook weergegeven in zoekresultaten. Kijk uit voor de "Private" badge.

>[!Note]
>Privé-aanbiedingen worden niet ondersteund met abonnementen die zijn ingesteld via een reseller van het Cloud Solution Provider-programma (CSP).

## <a name="next-steps"></a>Volgende stappen

Volg de stappen in de handleiding [Privé-SKU's en plannen](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) om privéaanbiedingen te gebruiken.
