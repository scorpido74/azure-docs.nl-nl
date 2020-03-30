---
title: Grootte van SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Grootte van SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 404f8318816edcc2cfd1c50ca42304ff6ec93039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616896"
---
# <a name="sizing"></a>Grootte aanpassen

Dimensionering voor HANA Large Instance is niet anders dan het dimensioneren voor HANA in het algemeen. Voor bestaande en geïmplementeerde systemen die u van andere RDBMS naar HANA wilt verplaatsen, biedt SAP een aantal rapporten die op uw bestaande SAP-systemen worden uitgevoerd. Als de database wordt verplaatst naar HANA, controleren deze rapporten de gegevens en berekenen ze de geheugenvereisten voor de HANA-instantie. Lees de volgende SAP Notes voor meer informatie over het uitvoeren van deze rapporten en het verkrijgen van hun meest recente patches of versies:

- [SAP Note #1793345 - Dimensionering voor SAP Suite op HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suite op HANA en S/4 HANA-dimensioneringsrapport](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - FAQ: SAP BW op HANA-maatrapport](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - Maatrapport voor BW op HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - Nieuw maatrapport voor BW op HANA](https://launchpad.support.sap.com/#/notes/2296290)

Voor implementaties in het groene veld is SAP Quick Sizer beschikbaar om de geheugenvereisten van de implementatie van SAP-software bovenop HANA te berekenen.

Geheugenvereisten voor HANA nemen toe naarmate het gegevensvolume toeneemt. Wees je bewust van je huidige geheugenverbruik om je te helpen voorspellen wat het in de toekomst zal zijn. Op basis van geheugenvereisten u uw vraag vervolgens in kaart brengen in een van de HANA Large Instance SKU's.

**Volgende stappen**
- [Onboarding-vereisten doorverwijzen](hana-onboarding-requirements.md)