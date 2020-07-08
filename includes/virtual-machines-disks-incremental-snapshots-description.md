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
ms.openlocfilehash: 83c391c0d92f8d4a0ed4b44bc3a90273db51b412
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81539512"
---
Incrementele moment opnamen zijn Point-in-time back-ups voor Managed disks die, wanneer ze worden gemaakt, alleen van alle wijzigingen bestaan sinds de laatste moment opname. Wanneer u probeert een incrementele moment opname te downloaden of anderszins te gebruiken, wordt de volledige VHD gebruikt. Deze nieuwe mogelijkheid voor moment opnamen van beheerde schijven maakt het mogelijk rendabeler te zijn, omdat, tenzij u dat doet, u de volledige schijf niet hoeft op te slaan met elke afzonderlijke moment opname. Net als bij gewone moment opnamen kunnen incrementele moment opnamen worden gebruikt om een volledige beheerde schijf te maken of om een gewone moment opname te maken.

Er zijn enkele verschillen tussen een incrementele moment opname en een reguliere moment opname. In incrementele moment opnamen wordt altijd standaard Hdd's-opslag gebruikt, ongeacht het opslag type van de schijf, terwijl reguliere moment opnamen Premium Ssd's kunnen gebruiken. Als u gewone moment opnamen op Premium Storage gebruikt voor het opschalen van VM-implementaties, raden we u aan om aangepaste installatie kopieën te gebruiken voor standaard opslag in de [Galerie met gedeelde afbeeldingen](../articles/virtual-machines/linux/shared-image-galleries.md). Het helpt u bij het bezorgen van een enorme schaal met lagere kosten. Daarnaast bieden incrementele moment opnamen mogelijk betere betrouw baarheid met [zone-redundante opslag](../articles/storage/common/storage-redundancy-zrs.md) (ZRS). Als ZRS beschikbaar is in de geselecteerde regio, wordt ZRS automatisch door een incrementele moment opname gebruikt. Als ZRS niet beschikbaar is in de regio, wordt de moment opname standaard ingesteld op [lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md) (LRS). U kunt dit gedrag negeren en het hand matig selecteren, maar dit wordt niet aangeraden.

Incrementele moment opnamen bieden ook een andere mogelijkheid, alleen beschikbaar voor beheerde schijven. Hiermee kunt u de wijzigingen ophalen tussen twee incrementele moment opnamen van dezelfde beheerde schijven, tot aan het blok niveau. U kunt deze mogelijkheid gebruiken om uw gegevensfootprint te verminderen bij het kopiëren van moment opnamen tussen regio's.  U kunt bijvoorbeeld de eerste incrementele moment opname downloaden als een basis-Blob in een andere regio. Voor de volgende incrementele moment opnamen kunt u alleen de wijzigingen kopiëren sinds de laatste moment opname naar de basis-blob. Nadat u de wijzigingen hebt gekopieerd, kunt u moment opnamen maken op de basis-blob die uw moment opname van de back-up van de schijf in een andere regio vertegenwoordigen. U kunt uw schijf herstellen met behulp van de basis-BLOB of van een moment opname op de basis-Blob in een andere regio.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagram waarin incrementele moment opnamen worden weer gegeven die in verschillende regio's zijn gekopieerd. Moment opnamen maken diverse API-aanroepen tot uiteindelijk pagina-blobs per moment opname.":::

U kunt de gebruikte grootte van uw momentopnamen bekijken in het [Azure-gebruiksrapport](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). Als de gebruikte gegevensgrootte van een momentopname bijvoorbeeld 10 GiB is, wordt in het **dagelijkse** gebruiksrapport 10 GiB/(31 dagen) = 0,3226 als verbruikte hoeveelheid weergegeven.
