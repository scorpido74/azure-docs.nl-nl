---
title: Tabblad Instellingen voor virtuele machineaanbieding in de Cloud Partner Portal voor Azure Marketplace
description: Beschrijft het tabblad Aanbiedingsinstellingen dat wordt gebruikt bij het maken van een Azure Marketplace VM-aanbieding.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 94be2e5d3c2c941ab17401a743ea86acbe8b6252
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273797"
---
# <a name="virtual-machine-offer-settings-tab"></a>Tabblad Instellingen voor virtuele machine-aanbieding

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar partnercentrum. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Een Azure Virtual Machine-aanbieding maken](https://aka.ms/CreateAzureVMoffer) om uw gemigreerde aanbiedingen te beheren.

De pagina **Nieuwe aanbieding** voor virtuele machines wordt geopend op het eerste tabblad met de naam **Aanbiedingsinstellingen**.  

![Nieuwe aanbiedingspagina voor virtuele machines](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Aanbiedingsinstellingen ( velden)

Op het tabblad **Aanbiedingsinstellingen** moet u de volgende velden opgeven.  Een toegevoegd sterretje (*) op de veldnaam geeft aan dat dit vereist is. 

|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
| **Aanbiedings-id\***   | Een unieke id (binnen een uitgeversprofiel) voor de aanbieding. Deze id is zichtbaar in product-URL's, Azure Resource Manager-sjablonen en factureringsrapporten. Het heeft een maximale lengte van 50 tekens, kan alleen worden samengesteld uit kleine alfanumerieke tekens en streepjes (-), maar kan niet eindigen in een streepje. Dit veld kan niet worden gewijzigd nadat een aanbieding live is gegaan. <br> Als Contoso bijvoorbeeld een aanbieding met **voorbeeld-vm**voor aanbiedings-id `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` publiceert, krijgt deze de URL van Azure Marketplace toegewezen . |
| **Publisher\***  | De unieke id van uw organisatie in de Azure Marketplace. Al uw aanbiedingen moeten worden gekoppeld aan uw uitgever-id. Deze waarde kan niet worden gewijzigd nadat de aanbieding is opgeslagen. |
| **Naam\***       | Geef de naam van uw aanbieding weer. Deze naam wordt weergegeven in de Azure Marketplace en in de Cloud Partner Portal. De naam mag maximaal 50 tekens bevatten. Begeleiding hier is om een herkenbare merknaam voor uw product op te nemen. Vermeld hier de naam van uw organisatie niet, tenzij deze op die manier op de markt wordt gebracht. Als u deze aanbieding op de markt zet in andere websites en publicaties, zorg er dan voor dat de naam in alle publicaties precies hetzelfde is. |
|   |   |
 
Klik **op Opslaan** nadat u alle velden hebt opgegeven. 


## <a name="next-steps"></a>Volgende stappen

In het volgende tabblad voegt u [SKU's](./cpp-skus-tab.md) toe aan uw aanbieding.
