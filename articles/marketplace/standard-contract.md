---
title: Standard-contract | Azure
description: Standard-contract in azure Marketplace en AppSource
services: Azure, Marketplace, Compute, Storage, Networking
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/05/2019
ms.author: ellacroi
ms.openlocfilehash: 17c1bf9d20b6f2e3ec450ff7bfb54fe61494ff09
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819501"
---
# <a name="standard-contract"></a>Standaardcontract

Om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te verminderen, biedt micro soft een standaard contract sjabloon om een trans actie in de Marketplace te vergemakkelijken. In plaats van aangepaste voor waarden te gebruiken, kunnen uitgevers van Azure Marketplace ervoor kiezen hun software aan te bieden onder het Standard-contract, wat klanten alleen hoeven te bevestigen en één keer te accepteren. Het standaard contract vindt u hier: [https://go.microsoft.com/fwlink/?linkid=2041178](https://go.microsoft.com/fwlink/?linkid=2041178). 

De voor waarden voor een aanbieding worden op het tabblad Marketplace gedefinieerd wanneer u een aanbieding maakt in de Cloud Partner-portal. De standaard contract optie wordt ingeschakeld door de instelling te wijzigen in Ja.

![Optie standaard contract inschakelen](media/marketplace-publishers-guide/standard-contract.png)

>[!Note] 
>Als u ervoor kiest om het standaard contract te gebruiken, zijn er nog afzonderlijke voor waarden vereist voor het kanaal van de [Cloud Solution Provider](./cloud-solution-providers.md) .

## <a name="standard-contract-amendments"></a>Wijzigingen in het standaard contract

Met de wijzigingen in het standaard contract kunnen uitgevers het standaard contract voor eenvoud selecteren en met aangepaste voor waarden voor hun product of bedrijf.  Klanten hoeven alleen de wijzigingen in het contract door te nemen als ze het micro soft Standard-contract al hebben gecontroleerd en geaccepteerd.

Er zijn twee soorten wijzigingen beschikbaar voor uitgevers van Azure Marketplace:

* Universele wijzigingen: deze wijzigingen worden universeel toegepast op het Standard-contract voor alle klanten. Universele wijzigingen worden weer gegeven aan elke klant van het product in de inkoop stroom.

![Universele wijzigingen](media/marketplace-publishers-guide/universal-amendaments.png)

* Aangepaste wijzigingen: Azure Marketplace heeft ook een voorziening voor aangepaste wijzigingen die zijn gericht op tenants. Ze zijn speciale wijzigingen in het standaard contract die alleen bestemd zijn voor bepaalde klanten. Uitgevers kunnen de Tenant kiezen die ze willen instellen. Klanten van deze Tenant kopen het product onder het standaard contract en de doel wijzigingen.

![Aangepaste wijzigingen](media/marketplace-publishers-guide/custom-amendaments.png)

>[!Note] 
>Klanten die zijn gericht op aangepaste wijzigingen, krijgen tijdens de aankoop ook de universele wijzigingen in de standaard termen.

>[!Note]
>De volgende aanbiedings typen ondersteunen wijzigingen in het standaard contract: Azure-toepassingen (oplossings sjablonen en beheerde toepassingen), Virtual Machines, containers, container toepassingen.

### <a name="customer-experience"></a>Gebruikers ervaring

Tijdens het aankoop proces in de Azure Portal kunnen klanten de voor waarden zien die zijn gekoppeld aan het product als het micro soft Standard-contract en de wijzigingen.

![Het Azure Portal klant ervaring.](media/marketplace-publishers-guide/ibiza-customer-experience.png)

### <a name="api"></a>API

Klanten kunnen `Get-AzureRmMarketplaceTerms` gebruiken om de voor waarden van een aanbieding op te halen en deze te accepteren. Het standaard contract en de bijbehorende wijzigingen worden geretourneerd in de uitvoer van de cmdlet.

---
