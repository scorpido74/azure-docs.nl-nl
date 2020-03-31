---
title: Azure IoT Edge-modules
description: De IoT Edge-moduleaanbieding in de Azure Marketplace voor app- en service-uitgevers.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: dsindona
ms.openlocfilehash: aadbf33914f919e393a5ec88cf6fc0a6103911b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286146"
---
# <a name="iot-edge-modules"></a>IoT Edge-modules

Het [Azure IoT Edge-platform](https://azure.microsoft.com/services/iot-edge/) wordt ondersteund door Azure Cloud.  Dit platform stelt gebruikers in staat om cloudworkloads te implementeren om direct op IoT-apparaten te worden uitgevoerd.  Een IoT Edge-module kan offline workloads uitvoeren en lokaal gegevensanalyses uitvoeren. Dit aanbiedingstype helpt bandbreedte te besparen, lokale en gevoelige gegevens te beveiligen en biedt een responstijd met een lage latentie.  U hebt nu de opties om te profiteren van deze vooraf gebouwde workloads. Tot nu toe waren slechts een handvol first-party oplossingen van Microsoft beschikbaar.  Je moest de tijd en middelen investeren in het bouwen van je eigen op maat gemaakte IoT-oplossingen.

Door de Introductie van de [IoT Edge-modules in de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)hebben we nu één bestemming voor uitgevers om hun oplossingen bloot te leggen en te verkopen aan het IoT-publiek. IoT-ontwikkelaars kunnen uiteindelijk mogelijkheden vinden en kopen om hun oplossingsontwikkeling te versnellen.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Belangrijkste voordelen van IoT Edge-modules in Azure Marketplace:

| **Voor uitgevers**    | **Voor klanten (IoT-ontwikkelaars)**  |
| :------------------- | :-------------------|
| Bereik miljoenen ontwikkelaars die IoT Edge-oplossingen willen bouwen en implementeren.  | Stel een IoT Edge-oplossing samen met het vertrouwen van het gebruik van veilige en geteste componenten. |
| Publiceer één keer en voer alle IoT Edge-hardware uit die containers ondersteunt. | Verminder de time-to-market door IoT Edge-modules van 1e en 3rd party te vinden en te implementeren voor specifieke behoeften. |
| Inkomsten genereren met flexibele factureringsopties <ul> <li> Gratis en breng uw eigen licentie (BYOL). </li> </ul> | Doe aankopen met uw keuze van factureringsmodellen. <ul> <li> Gratis en breng uw eigen licentie (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Wat is een IoT Edge-module?

Met Azure IoT Edge u bedrijfslogica op de rand implementeren en beheren in de vorm van modules. Azure IoT Edge-modules zijn de kleinste rekeneenheden die worden beheerd door IoT Edge en kunnen Microsoft-services bevatten (zoals Azure Stream Analytics), services van derden of uw eigen oplossingsspecifieke code. Zie [Azure IoT Edge-modules begrijpen](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)voor meer informatie over IoT Edge-modules.

**Wat is het verschil tussen een containeraanbiedingstype en een IoT Edge-moduleaanbiedingstype?**

Het type IoT Edge-modulebiedt een specifiek type container dat wordt uitgevoerd op een IoT Edge-apparaat. Het wordt geleverd met standaardconfiguratie-instellingen om uit te voeren in de IoT Edge-context en maakt optioneel gebruik van de IoT Edge-module SDK om te worden geïntegreerd met de IoT Edge-runtime.

## <a name="publishing-your-iot-edge-module"></a>Uw IoT Edge-module publiceren

**De juiste etalage selecteren**

IoT Edge Modules worden alleen gepubliceerd op de Azure Marketplace, AppSource is niet van toepassing.  Zie voor meer informatie over de verschillen en doelgroepen in de etalages de [publicatieoptie voor uw oplossing bepalen.](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)
 
**Factureringsopties**

De marktplaats ondersteunt momenteel byol-factureringsopties **(Free** and **Bring Your Own License)** voor IoT Edge-modules.
 
**Publicatieopties**

In alle gevallen moeten IoT Edge-modules de optie **Transact** publishing selecteren.  Zie [een publicatieoptie kiezen](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) voor meer informatie over publicatieopties.  

## <a name="eligibility-criteria"></a>Subsidiabiliteitscriteria

Alle voorwaarden van de Microsoft Azure Marketplace-overeenkomsten en -beleidsregels zijn van toepassing op IoT Edge-moduleaanbiedingen.  Daarnaast zijn er vereisten en technische vereisten voor IoT Edge-modules.  

**Vereisten**

Als u een IoT Edge-module wilt publiceren in de Azure Marketplace, moet u aan de volgende vereisten voldoen:

- Toegang tot de Cloud Partner Portal (CPP). Zie [publicatiegids azure marketplace en appsource voor](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)meer informatie.
- Host uw IoT Edge-module in een Azure Container Registry. 
- Maak uw IoT Edge-modulemetagegevens klaar, zoals (niet-uitputtende lijst): 
    - Een titel
    - Een beschrijving (in HTML-indeling)
    - Een logoafbeelding (PNG-indeling en vaste afbeeldingsformaten, waaronder 40x40px, 90x90px, 115x115px, 255x115px)
    - Een gebruiksduur en privacybeleid
    - Standaardmoduleconfiguratie (route, dubbele gewenste eigenschappen, createOptions, omgevingsvariabelen)
    - Documentatie
    - Contactpersonen voor ondersteuning

**Technische voorschriften**

De primaire technische vereisten voor een IoT Edge-module, zodat deze gecertificeerd en gepubliceerd kan worden in de Azure Marketplace, worden beschreven in de technische elementen van de [IoT Edge-module voorbereiden.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)  

## <a name="documentation-and-resources"></a>Documentatie en bronnen

[Maak een IoT Edge-moduleaanbieding](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-offer) - De stappen voor het publiceren van een nieuwe IoT Edge-moduleaanbieding met de Cloud Publishing Portal.

## <a name="next-steps"></a>Volgende stappen

Als je dat nog niet gedaan hebt,

- [Registreer](https://azuremarketplace.microsoft.com/sell) op de marktplaats.

Als u bent geregistreerd en een nieuwe aanbieding maakt of aan een bestaand aanbod werkt,

- Meld u aan bij [Cloud Partner Portal](https://cloudpartner.azure.com/) om uw aanbieding te maken of te voltooien.
- Zie [IoT Edge-module biedt publicatieoverzicht](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) voor informatie over het publiceren van een IoT Edge-moduleaanbieding.
