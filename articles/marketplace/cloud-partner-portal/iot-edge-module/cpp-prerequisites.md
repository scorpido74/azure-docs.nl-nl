---
title: Azure IoT Edge module vereisten | Azure Marketplace
description: Vereisten voor het publiceren van een IoT Edge module.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142374"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Vereisten voor het publiceren van IoT Edge-module

>[!Important]
>Vanaf 13 april 2020 gaan we het beheer van uw IoT Edge module-aanbiedingen naar het partner centrum verplaatsen. Na de migratie maakt en beheert u uw aanbiedingen in partner centrum. Volg de instructies in [een IOT Edge-module maken](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) voor het beheren van uw gemigreerde aanbiedingen.

In dit artikel worden de vereisten beschreven voor het publiceren van een IoT Edge module-aanbieding.  Als u dit nog niet hebt gedaan, raadpleegt u de [publicatie handleiding voor IOT Edge modules](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Vereisten voor publicatie

Als u een IoT Edge module naar Azure Marketplace wilt publiceren, moet u aan de volgende vereisten voldoen:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Toegang tot de [Cloud Partner-Portal](https://cloudpartner.azure.com/). Zie de [publicatie handleiding voor Azure Marketplace en AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)voor meer informatie.
- Overeenkomst met de [Azure Marketplace-voor waarden](https://azure.microsoft.com/support/legal/marketplace-terms/)
- De technische activa van uw IoT Edge-module hosten in een Azure Container Registry.  Zie [de technische Asset van uw IOT Edge-module voorbereiden](./cpp-create-technical-assets.md) voor meer informatie.
- Laat de meta gegevens van uw IoT Edge module gereed voor gebruik. U kunt bijvoorbeeld de volgende assets voorbereiden:
    - Een titel
    - Een beschrijving (in HTML-indeling)
    - Een logo afbeelding (PNG-indeling en vaste afbeeldings grootten, waaronder 40x40px, 90x90px, 115x115px, 255x115px)
    - Een gebruiks voorwaarden en privacybeleid
    - Een standaard module configuratie met onder andere: routes, dubbele gewenste eigenschappen, createOptions en omgevings variabelen.
    - Documentatie over modules
    - Contactpersonen voor ondersteuning


## <a name="next-steps"></a>Volgende stappen

Als u [uw IOT Edge-module technische activa hebt voor bereid](./cpp-create-technical-assets.md), kunt u [uw IOT Edge module-aanbod maken](./cpp-create-offer.md). 
