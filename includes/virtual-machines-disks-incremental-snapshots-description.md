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
ms.openlocfilehash: 4d2c0a02a48c0e04b501f136f66c28b3f532e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79485913"
---
Incrementele momentopnamen zijn point-in-time back-ups voor beheerde schijven die, wanneer ze worden genomen, alleen bestaan uit alle wijzigingen sinds de laatste momentopname. Wanneer u een incrementele momentopname probeert te downloaden of anderszins te gebruiken, wordt de volledige VHD gebruikt. Deze nieuwe mogelijkheid voor beheerde schijfmomentopnamen stelt hen mogelijk in staat om kosteneffectiever te zijn, omdat u, tenzij u ervoor kiest, niet de hele schijf bij elke afzonderlijke momentopname hoeft op te slaan. Net als gewone momentopnamen kunnen incrementele momentopnamen worden gebruikt om een volledig beheerde schijf te maken of om een gewone momentopname te maken.

Er zijn een paar verschillen tussen een incrementele momentopname en een gewone momentopname. Incrementele momentopnamen gebruiken altijd standaard HDD's-opslag, ongeacht het opslagtype van de schijf, terwijl gewone momentopnamen premium-ssd's kunnen gebruiken. Als u gewone momentopnamen in Premium Storage gebruikt om VM-implementaties op te schalen, raden we u aan aangepaste afbeeldingen te gebruiken in standaardopslag in de [gedeelde afbeeldingsgalerie.](../articles/virtual-machines/linux/shared-image-galleries.md) Het zal u helpen een meer massale schaal te bereiken met lagere kosten. Bovendien bieden incrementele momentopnamen mogelijk een betere betrouwbaarheid met [zrs (zoneredundante opslag).](../articles/storage/common/storage-redundancy-zrs.md) Als ZRS beschikbaar is in de geselecteerde regio, wordt zrs automatisch gebruikt in een incrementele momentopname. Als ZRS niet beschikbaar is in de regio, wordt de momentopname standaard ingesteld op [lokaal redundante opslag](../articles/storage/common/storage-redundancy-lrs.md) (LRS). U dit gedrag overschrijven en er handmatig een selecteren, maar dat raden we niet aan.

Incrementele momentopnamen bieden ook een differentiële mogelijkheid, alleen beschikbaar voor beheerde schijven. Hiermee u de wijzigingen tussen twee incrementele momentopnamen van dezelfde beheerde schijven, tot op blokniveau krijgen. U deze mogelijkheid gebruiken om uw gegevensvoetafdruk te verkleinen bij het kopiëren van momentopnamen in verschillende regio's.  U bijvoorbeeld de eerste incrementele momentopname downloaden als een basisblob in een andere regio. Voor de volgende incrementele momentopnamen u alleen de wijzigingen kopiëren sinds de laatste momentopname naar de basisblob. Nadat u de wijzigingen hebt gekopieerd, u momentopnamen maken op de basisblob die uw back-up van de schijf in een andere regio weergeven. U uw schijf herstellen vanaf de basisblob of vanaf een momentopname op de basisblob in een andere regio.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagram met incrementele momentopnamen die over verschillende regio's zijn gekopieerd. Snapshots maken verschillende API-aanroepen totdat uiteindelijk de vorming van pagina blobs per elke momentopname.":::