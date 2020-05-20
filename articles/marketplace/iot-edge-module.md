---
title: Azure Marketplace IoT Edge-module aanbiedingen
description: Meer informatie over het publiceren van IoT Edge module-aanbiedingen in azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 49f86a79eb5358d27c15d93004db396436c3e680
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657952"
---
# <a name="iot-edge-modules"></a>IoT Edge-modules

Het [Azure IOT Edge](https://azure.microsoft.com/services/iot-edge/) -platform wordt ondersteund door Microsoft Azure.  Met dit platform kunnen gebruikers Cloud werkbelastingen implementeren die rechtstreeks op IoT-apparaten worden uitgevoerd.  Een IoT Edge module kan offline workloads uitvoeren en gegevens analyse lokaal doen. Met dit type aanbieding kunt u band breedte besparen, lokale en gevoelige gegevens beveiligen en een reactie tijd van lage latentie bieden.  U hebt nu de mogelijkheid om te profiteren van deze vooraf ontwikkelde werk belastingen. Tot nu toe zijn er slechts enkele oplossingen van de eerste partij beschikbaar.  U moest de tijd en resources investeren in het bouwen van uw eigen aangepaste IoT-oplossingen.

Met [IOT Edge modules in de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)hebben we nu één doel voor uitgevers om hun oplossingen beschikbaar te maken en te verkopen aan de IOT-doel groep. IoT-ontwikkel aars kunnen uiteindelijk mogelijkheden vinden en kopen om de ontwikkeling van oplossingen te versnellen.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Belangrijkste voor delen van IoT Edge modules in azure Marketplace

| **Voor uitgevers**    | **Voor klanten (IoT-ontwikkel aars)**  |
| :------------------- | :-------------------|
| Bereik miljoenen ontwikkel aars die IoT Edge-oplossingen willen bouwen en implementeren.  | Stel een IoT Edge oplossing met het vertrouwen van het gebruik van beveiligde en geteste onderdelen. |
| Publiceer één keer en voer uit op alle IoT Edge hardware die containers ondersteunt. | Verminder de tijd tot de markt door 1ste en van derden IoT Edge modules te zoeken en te implementeren voor specifieke behoeften. |
| Geld verdienen met flexibele facturerings opties <ul> <li> Maak en til uw eigen licentie (BYOL). </li> </ul> | Maak aankopen met uw favoriete facturerings modellen. <ul> <li> Maak en til uw eigen licentie (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Wat is een IoT Edge module?

Met Azure IoT Edge kunt u bedrijfs logica op de rand in de vorm van modules implementeren en beheren. Azure IoT Edge-modules zijn de kleinste reken eenheden die worden beheerd door IoT Edge, en kunnen micro soft-Services (zoals Azure Stream Analytics), services van derden of uw eigen oplossing-specifieke code bevatten. Zie [Azure IOT Edge modules begrijpen](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)voor meer informatie over IOT Edge-modules.

**Wat is het verschil tussen een type container aanbieding en een IoT Edge module-aanbod type?**

Het IoT Edge-module aanbod type is een specifiek type container dat wordt uitgevoerd op een IoT Edge-apparaat. Het wordt geleverd met de standaard configuratie-instellingen die in de IoT Edge context worden uitgevoerd en maakt optioneel gebruik van de IoT Edge module SDK om te worden geïntegreerd met de IoT Edge runtime.

## <a name="publishing-your-iot-edge-module"></a>Uw IoT Edge-module publiceren

**De juiste winkel selecteren**

IoT Edge-modules worden alleen gepubliceerd naar Azure Marketplace. AppSource is niet van toepassing.  Zie [de publicatie optie voor uw oplossing bepalen](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)voor meer informatie over de verschillen en doel publiek op diverse winkels.
 
**Facturerings opties**

De Marketplace ondersteunt momenteel **gratis** en **BYOL-facturerings opties (uw eigen licentie)** voor IOT Edge modules.
 
**Publicatie opties**

In alle gevallen moeten IoT Edge modules de optie voor het publiceren van **Transact** selecteren.  Zie [een publicatie optie kiezen](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) voor meer informatie over publicatie opties.  

## <a name="eligibility-criteria"></a>Geschiktheids criteria

Alle voor waarden van de Microsoft Azure Marketplace overeenkomsten en beleids regels zijn van toepassing op IoT Edge module aanbiedingen.  Daarnaast zijn er vereisten en technische vereisten voor IoT Edge modules.  

**Vereisten**

Als u een IoT Edge module naar Azure Marketplace wilt publiceren, moet u aan de volgende vereisten voldoen:

- Toegang tot het partner centrum. Zie de [publicatie handleiding voor Azure Marketplace en AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)voor meer informatie.
- Host uw IoT Edge-module in een Azure Container Registry. 
- De meta gegevens van uw IoT Edge-module gereed maken, zoals (niet-limitatieve lijst): 
    - Een titel
    - Een beschrijving (in HTML-indeling)
    - Een logo afbeelding (PNG-indeling en vaste afbeeldings grootten, waaronder 40x40px, 90x90px, 115x115px, 255x115px)
    - Een gebruiks voorwaarden en privacybeleid
    - Standaard module configuratie (route, dubbele gewenste eigenschappen, createOptions, omgevings variabelen)
    - Documentatie
    - Contactpersonen voor ondersteuning

**Technische vereisten**

De belangrijkste technische vereisten voor een IoT Edge module, zodat IT kan worden gecertificeerd en gepubliceerd op de Azure Marketplace, worden beschreven in de [technische activa van uw IOT Edge module voorbereiden](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="documentation-and-resources"></a>Documentatie en bronnen

[Maak een IOT Edge module-aanbod](./partner-center-portal/azure-iot-edge-module-creation.md) : de stappen voor het publiceren van een nieuwe IOT Edge module-aanbieding in het partner centrum.

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan,

- [Meer informatie](https://azuremarketplace.microsoft.com/sell) over Marketplace.

Als u zich wilt registreren in Partner Center en wilt beginnen met het maken van een nieuwe aanbieding of het werken met een bestaand aanbod,

- Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) om uw aanbieding te maken of te volt ooien.
- Zie [een IOT Edge module maken](./partner-center-portal/azure-iot-edge-module-creation.md) voor meer informatie over het publiceren van een IOT Edge module-aanbod.
