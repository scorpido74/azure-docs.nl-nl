---
title: Standaardcontract | Azure Marketplace
description: Standaardcontract voor Azure Marketplace en AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: facb500299f7196e0e1387c3a7267a917d13a3a6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681453"
---
# <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standaardcontract voor de commerciële marktplaats van Microsoft

Om het inkoopproces voor klanten te vereenvoudigen en de juridische complexiteit voor softwareleveranciers te verminderen, biedt Microsoft een standaardcontract voor de commerciële marktplaats van Microsoft om transacties op de markt te vergemakkelijken. In plaats van aangepaste algemene voorwaarden te maken, kunnen commerciële marktplaatsuitgevers ervoor kiezen om hun software aan te bieden in het kader van het standaardcontract, dat klanten slechts één keer hoeven te controleren en accepteren. Het standaardcontract is hier [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178)te vinden: .

De algemene voorwaarden voor een aanbieding worden gedefinieerd bij het maken van de aanbieding in partnercentrum. U ervoor kiezen om het standaardcontract voor de commerciële marktplaats van Microsoft te gebruiken in plaats van uw eigen aangepaste algemene voorwaarden te bieden.

>[!Note]
>Zodra u een aanbieding publiceert met behulp van het standaardcontract voor de commerciële marktplaats van Microsoft, u uw eigen aangepaste algemene voorwaarden niet meer gebruiken. Het is een "of" scenario. U biedt uw oplossing onder het standaardcontract *of* uw eigen algemene voorwaarden. Als u de voorwaarden van het standaardcontract wilt wijzigen, u dit doen via standaardcontractwijzigingen.

## <a name="standard-contract-amendments"></a>Standaardcontractwijzigingen

Met standaardcontractwijzigingen kunnen uitgevers het standaardcontract selecteren voor eenvoud en met aangepaste voorwaarden voor hun product of bedrijf. Klanten hoeven de wijzigingen in het contract alleen te bekijken als ze het Microsoft Standard-contract al hebben beoordeeld en geaccepteerd.

Er zijn twee soorten wijzigingen beschikbaar voor commerciële marktplaatsuitgevers:

* Universele wijzigingen: Deze wijzigingen worden universeel toegepast op het standaardcontract voor alle klanten. Universele wijzigingen worden getoond aan elke klant van het aanbod in de aankoopstroom. Klanten moeten de voorwaarden van het standaardcontract en de wijziging accepteren voordat ze uw aanbieding kunnen gebruiken.

* Aangepaste wijzigingen: deze wijzigingen zijn speciale wijzigingen in het standaardcontract die alleen zijn gericht op specifieke klanten via Azure-tenant-id's. Uitgevers kunnen de tenant kiezen die ze willen targeten. Alleen klanten van de tenant krijgen de aangepaste wijzigingsvoorwaarden in de aankoopstroom van de aanbieding te zien.  Klanten moeten de voorwaarden van het standaardcontract en de wijziging(en) accepteren voordat ze uw aanbieding kunnen gebruiken.

>[!Note]
>Deze twee soorten amendementen stapelen op elkaar. Klanten die zijn gericht op aangepaste wijzigingen krijgen ook de universele wijziging van het standaardcontract tijdens de aankoop.

U het standaardcontract voor de commerciële marktplaats van Microsoft gebruiken voor de volgende aanbiedingstypen: Azure-toepassingen (oplossingssjablonen en beheerde toepassingen), virtuele machines, containers, containertoepassingen, IoT-randmodules en SaaS.

## <a name="customer-experience"></a>Klantervaring

Tijdens de detectie-ervaring in Azure marketplace of AppSource kunnen klanten de voorwaarden zien die aan de aanbieding zijn gekoppeld als het standaardcontract voor de commerciële marktplaats van Microsoft en alle universele wijzigingen.

![De azure portal customer discovery experience.](media/marketplace-publishers-guide/azure-discovery-process.png)

Tijdens het aankoopproces in de Azure-portal kunnen klanten de voorwaarden zien die aan de aanbieding zijn gekoppeld als het standaardcontract voor de commerciële marktplaats van Microsoft en alle universele en/of tenantspecifieke wijzigingen.

![De inkoopervaring van de Azure-portal.](media/marketplace-publishers-guide/azure-purchase-process.png)

## <a name="api"></a>API

Klanten kunnen Get-AzureRmMarketplaceTerms gebruiken om de voorwaarden van een aanbieding op te halen en te accepteren. Het standaardcontract en de bijbehorende wijzigingen worden geretourneerd in de output van de cmdlet.
