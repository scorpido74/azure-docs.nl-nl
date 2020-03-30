---
title: Vereisten voor Azure IoT Edge-module | Azure Marketplace
description: Voorwaarden voor het publiceren van een IoT Edge-module.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b6e021fc452b45edd7b1be9fd5afd77b792b4853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286537"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Vereisten voor het publiceren van IoT Edge-modules

In dit artikel worden de vereisten beschreven voor het publiceren van een IoT Edge-moduleaanbieding.  Als u dit nog niet hebt gedaan, bekijkt u de [publicatiehandleiding voor IoT Edge-modules.](../..//iot-edge-module.md)


## <a name="publishing-prerequisites"></a>Publicatievereisten

Als u een IoT Edge-module wilt publiceren in de Azure Marketplace, moet u aan de volgende voorwaarden voldoen:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Toegang tot de [Cloud Partner Portal](https://cloudpartner.azure.com/). Zie [publicatiegids azure marketplace en appsource voor](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)meer informatie.
- Overeenkomst met de [Azure Marketplace-voorwaarden](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Host uw technische asset van de IoT Edge-module in een Azure Container Registry.  Zie voor meer informatie [hoe u uw technische asset van de IoT Edge-module voorbereiden](./cpp-create-technical-assets.md)
- Maak uw IoT Edge-modulemetagegevens klaar voor gebruik. Bereid bijvoorbeeld de volgende activa voor:
    - Een titel
    - Een beschrijving (in HTML-indeling)
    - Een logoafbeelding (PNG-indeling en vaste afbeeldingsformaten, waaronder 40x40px, 90x90px, 115x115px, 255x115px)
    - Een gebruiksduur en privacybeleid
    - Een standaardmoduleconfiguratie die omvat: routes, dubbele gewenste eigenschappen, createOptions en omgevingsvariabelen.
    - Moduledocumentatie
    - Contactpersonen voor ondersteuning


## <a name="next-steps"></a>Volgende stappen

Zodra u [uw technische asset van de IoT Edge-module](./cpp-create-technical-assets.md)hebt voorbereid, bent u klaar om uw [IoT Edge-moduleaanbieding](./cpp-create-offer.md)te maken. 
