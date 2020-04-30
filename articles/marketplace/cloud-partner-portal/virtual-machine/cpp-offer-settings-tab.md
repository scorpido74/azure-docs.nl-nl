---
title: Tabblad Instellingen voor de virtuele machine in de Cloud Partner-portal voor Azure Marketplace
description: Hierin wordt het tabblad Instellingen voor de aanbieding beschreven die worden gebruikt voor het maken van een Azure Marketplace-VM-aanbieding.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: abeadf5f3c0a19212a12256e06602e840c9d1fa9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146818"
---
# <a name="virtual-machine-offer-settings-tab"></a>Tabblad Instellingen voor de virtuele machine

> [!IMPORTANT]
> Vanaf 13 april 2020 gaan we het beheer van uw Azure Virtual Machine-aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [een Azure virtual machine-aanbieding maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) om uw gemigreerde aanbiedingen te beheren.

De **nieuwe aanbiedings** pagina voor virtuele machines wordt geopend op het eerste tabblad met de naam **instellingen van aanbod**.  

![Nieuwe aanbiedings pagina voor virtuele machines](./media/publishvm_004.png)


## <a name="offer-settings-fields"></a>Velden voor aanbiedings instellingen

Op het tabblad Instellingen voor de **aanbieding** moet u de volgende velden opgeven.  Een toegevoegd sterretje (*) op de veld naam geeft aan dat deze vereist is. 

|  **Veld**       |     **Beschrijving**                                                          |
|  ---------       |     ---------------                                                          |
| **Aanbiedings-id\***   | Een unieke id (binnen een Publisher-profiel) voor de aanbieding. Deze id is zichtbaar in product-Url's, Azure Resource Manager sjablonen en facturerings rapporten. De maximale lengte van 50 tekens mag alleen bestaan uit kleine letters, alfanumerieke tekens en streepjes (-), maar mag niet eindigen op een streepje. Dit veld kan niet worden gewijzigd nadat een aanbieding Live is. <br> Als contoso bijvoorbeeld een aanbieding met aanbiedings-ID voor **beeld publiceert-VM**, wordt de URL `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview` van de Azure Marketplace toegewezen. |
| **Uitgever\***  | De unieke id van uw organisatie in azure Marketplace. Al uw aanbiedingen moeten worden gekoppeld aan uw uitgevers-ID. Deze waarde kan niet worden gewijzigd nadat de aanbieding is opgeslagen. |
| **Naam\***       | Weergave naam voor uw aanbieding. Deze naam wordt weer gegeven in de Azure Marketplace en in de Cloud Partner-portal. De naam mag maximaal 50 tekens bevatten. Hier volgt een uitleg van een herken bare merk naam voor uw product. Neem hier de naam van uw organisatie niet op, tenzij deze wordt gemarketd. Als u dit aanbod in de handel wilt brengen in andere websites en publicaties, moet u ervoor zorgen dat de naam precies hetzelfde is in alle publicaties. |
|   |   |
 
Klik op **Opslaan** nadat u alle velden hebt opgegeven. 


## <a name="next-steps"></a>Volgende stappen

Op het volgende tabblad gaat u [sku's](./cpp-skus-tab.md) toevoegen aan uw aanbieding.
