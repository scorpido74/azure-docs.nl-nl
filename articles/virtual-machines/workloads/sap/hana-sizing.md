---
title: Grootte van SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Grootte van SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2540a0f9ea702ff620ccd4b68208cbb2e70aafd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101149"
---
# <a name="sizing"></a>Grootte aanpassen

De grootte van HANA grote instanties wijkt niet af van de grootte van HANA in het algemeen. Voor bestaande en geïmplementeerde systemen die u wilt verplaatsen van andere RDBMS naar HANA, biedt SAP een aantal rapporten die worden uitgevoerd op uw bestaande SAP-systemen. Als de data base wordt verplaatst naar HANA, controleren deze rapporten de gegevens en berekenen we de geheugen vereisten voor het HANA-exemplaar. Lees de volgende SAP-opmerkingen voor meer informatie over het uitvoeren van deze rapporten en het verkrijgen van de meest recente patches of versies:

- [SAP-notitie #1793345-grootte voor SAP Suite op HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP-notitie #1872170-Suite op HANA en S/4 HANA-formaat rapport](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP-Opmerking #2121330-Veelgestelde vragen: SAP BW in HANA-formaat rapport](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP-notitie #1736976-formaat rapport voor BW op HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP-notitie #2296290: nieuw formaat rapport voor BW op HANA](https://launchpad.support.sap.com/#/notes/2296290)

Voor de implementaties van een groen veld is de snelle grootte van SAP beschikbaar voor het berekenen van de geheugen vereisten van de implementatie van SAP-software bovenop HANA.

Geheugenvereisten voor HANA verhogen naarmate het gegevensvolume toeneemt. Houd rekening met het huidige geheugen gebruik om u te helpen te voors pellen wat het in de toekomst zal zijn. Op basis van de geheugen vereisten kunt u uw vraag vervolgens toewijzen aan een van de HANA-Sku's voor grote instanties.

**Volgende stappen**
- Raadpleeg de [vereisten voor](hana-onboarding-requirements.md) onboarding