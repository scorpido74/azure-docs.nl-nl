---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 1f094cd78e6708fec4bda1e5510379b11df14dcd
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299158"
---
Incrementele moment opnamen zijn Point-in-time back-ups voor Managed disks die, wanneer ze worden gemaakt, alleen van alle wijzigingen bestaan sinds de laatste moment opname. Wanneer u probeert een incrementele moment opname te downloaden of anderszins te gebruiken, wordt de volledige VHD gebruikt. Deze nieuwe mogelijkheid voor moment opnamen van beheerde schijven maakt het mogelijk rendabeler te zijn, omdat, tenzij u dat doet, u de volledige schijf niet hoeft op te slaan met elke afzonderlijke moment opname. Net als bij gewone moment opnamen kunnen incrementele moment opnamen worden gebruikt om een volledige beheerde schijf te maken of om een gewone moment opname te maken.

Er zijn enkele verschillen tussen een incrementele moment opname en een reguliere moment opname. In incrementele moment opnamen wordt altijd standaard Hdd's-opslag gebruikt, ongeacht het opslag type van de schijf, terwijl reguliere moment opnamen Premium Ssd's kunnen gebruiken. Als u gewone moment opnamen op Premium Storage gebruikt voor het opschalen van VM-implementaties, raden we u aan om aangepaste installatie kopieën te gebruiken voor standaard opslag in de [Galerie met gedeelde afbeeldingen](../articles/virtual-machines/linux/shared-image-galleries.md). Het helpt u bij het bezorgen van een enorme schaal met lagere kosten. Daarnaast bieden incrementele moment opnamen mogelijk betere betrouw baarheid met [zone-redundante opslag](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Als ZRS beschikbaar is in de geselecteerde regio, wordt ZRS automatisch door een incrementele moment opname gebruikt. Als ZRS niet beschikbaar is in de regio, wordt de moment opname standaard ingesteld op [lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md) (LRS). U kunt dit gedrag negeren en het hand matig selecteren, maar dit wordt niet aangeraden.

Incrementele moment opnamen bieden ook een andere mogelijkheid, alleen beschikbaar voor beheerde schijven. Hiermee kunt u de wijzigingen ophalen tussen twee incrementele moment opnamen van dezelfde beheerde schijven, tot aan het blok niveau. U kunt deze mogelijkheid gebruiken om uw gegevensfootprint te verminderen bij het kopiëren van moment opnamen tussen regio's.
