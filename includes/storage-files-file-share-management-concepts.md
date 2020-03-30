---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597794"
---
Azure-bestandsshares worden geïmplementeerd in *opslagaccounts,* objecten op het hoogste niveau die een gedeelde opslaggroep vertegenwoordigen. Deze opslaggroep kan worden gebruikt om meerdere bestandsshares te implementeren, evenals andere opslagbronnen zoals blobcontainers, wachtrijen of tabellen. Alle opslagbronnen die in een opslagaccount worden geïmplementeerd, delen de limieten die van toepassing zijn op dat opslagaccount. Zie [Schaalbaarheid en prestatiedoelen](../articles/storage/files/storage-files-scale-targets.md)voor Azure Files voor de huidige limieten voor een opslagaccount.

Er zijn twee hoofdtypen opslagaccounts die u gebruikt voor Azure Files-implementaties: 
- **GPv2-opslagaccounts (Version 2) voor**algemeen gebruik: met GPv2-opslagaccounts u Azure-bestandsshares implementeren op standaard/harde schijfgebaseerde (HDD-gebaseerde) hardware. Naast het opslaan van Azure-bestandsshares kunnen GPv2-opslagaccounts andere opslagbronnen opslaan, zoals blobcontainers, wachtrijen of tabellen. 
- **FileStorage-opslagaccounts:** met opslagaccounts voor Bestandsopslag u Azure-bestandsshares implementeren op hardware op basis van schijfgebaseerde schijfgebaseerde schijfgebaseerde schijven op basis van premium/solid-state. FileStorage-accounts kunnen alleen worden gebruikt om Azure-bestandsshares op te slaan. er kunnen geen andere opslagbronnen (blobcontainers, wachtrijen, tabellen, enz.) worden geïmplementeerd in een FileStorage-account.

Er zijn verschillende andere typen opslagaccountdie u tegenkomen in de Azure-portal, PowerShell of CLI. Twee opslagaccounttypen, BlockBlobStorage- en BlobStorage-opslagaccounts, kunnen geen Azure-bestandsshares bevatten. De andere twee opslagaccounttypen die u zien, zijn versie 1 (GPv1) voor algemeen gebruik en klassieke opslagaccounts, die beide Azure-bestandsshares kunnen bevatten. Hoewel GPv1- en klassieke opslagaccounts Azure-bestandsshares kunnen bevatten, zijn de meeste nieuwe functies van Azure-bestanden alleen beschikbaar in opslagaccounts voor GPv2 en FileStorage. We raden daarom aan om alleen GPv2- en FileStorage-opslagaccounts te gebruiken voor nieuwe implementaties en gpv1- en klassieke opslagaccounts te upgraden als ze al in uw omgeving bestaan.  