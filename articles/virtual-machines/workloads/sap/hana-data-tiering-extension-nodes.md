---
title: Gegevenslagen en extensieknooppunten voor SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Gegevenslagen en extensieknooppunten voor SAP HANA op Azure (Large Instances).
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
ms.openlocfilehash: c3553ac9704ac26d0bdaae0f93b89f41a87ac716
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617164"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Sap HANA-gegevenslagen en extensieknooppunten gebruiken

SAP ondersteunt een data tiering model voor SAP BW van verschillende SAP NetWeaver releases en SAP BW/4HANA. Zie het SAP-document [SAP BW/4HANA en SAP BW op HANA met SAP HANA-extensieknooppunten](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#)voor meer informatie over het datatiering-model.
Met HANA Large Instance u de optie-1-configuratie van SAP HANA-extensieknooppunten gebruiken, zoals uitgelegd in de FAQ- en SAP-blogdocumenten. Optie-2-configuraties kunnen worden ingesteld met de volgende HANA Large Instance SKU's: S72m, S192, S192m, S384 en S384m. 

Wanneer u de documentatie bekijkt, is het voordeel mogelijk niet meteen zichtbaar. Maar als je kijkt naar de SAP-dimensioneringsrichtlijnen, kun je een voordeel zien door gebruik te maken van optie-1 en optie-2 SAP HANA-extensieknooppunten. Hier volgen enkele voorbeelden:

- SAP HANA-dimensioneringsrichtlijnen vereisen meestal het dubbele van het hoeveelheid gegevensvolume als geheugen. Wanneer u uw SAP HANA-exemplaar uitvoert met de hot-gegevens, hebt u slechts 50 procent of minder van het geheugen gevuld met gegevens. De rest van het geheugen wordt idealiter gehouden voor SAP HANA doet zijn werk.
- Dat betekent dat in een HANA Large Instance S192-eenheid met 2 TB geheugen, het uitvoeren van een SAP BW-database, je slechts 1 TB hebt als datavolume.
- Als u een extra SAP HANA-extensieknooppunt van optie-1 gebruikt, ook een S192 HANA Large Instance SKU, geeft dit u een extra capaciteit van 2 TB voor gegevensvolume. In de optie-2-configuratie krijgt u een extra 4 TB voor warm gegevensvolume. In vergelijking met het hete knooppunt kan de volledige geheugencapaciteit van het "warme" extensieknooppunt worden gebruikt voor gegevensopslag voor optie-1. Dubbele het geheugen kan worden gebruikt voor gegevensvolume in optie-2 SAP HANA extensie knooppunt configuratie.
- U eindigt met een capaciteit van 3 TB voor uw gegevens en een warm-to-warm verhouding van 1:2 voor optie-1. Je hebt 5 TB aan gegevens en een 1:4-verhouding met de optie-2-extensieknooppuntconfiguratie.

Hoe hoger het gegevensvolume in vergelijking met het geheugen, hoe groter de kans is dat de warme gegevens die u vraagt worden opgeslagen op schijfopslag.

**Volgende stappen**
- [Sap HANA-architectuur (Large Instances) verwijzen op Azure](hana-architecture.md)