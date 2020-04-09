---
title: Vereisten voor Azure IoT Edge-module | Azure Marketplace
description: Voorwaarden voor het publiceren van een IoT Edge-module.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 9a54b31725d14a3ff54bd2d945cd69f4b8769b87
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983174"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Vereisten voor het publiceren van IoT Edge-modules

>[!Important]
>Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw IoT Edge-moduleaanbiedingen naar Partner Center. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Een IoT Edge-moduleaanbieding maken](https://aka.ms/AzureCreateIoT) om uw gemigreerde aanbiedingen te beheren.

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
