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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597794"
---
Azure-bestands shares worden geïmplementeerd in *opslag accounts*. Dit zijn objecten op het hoogste niveau die een gedeelde opslag groep vertegenwoordigen. Deze opslag groep kan worden gebruikt voor het implementeren van meerdere bestands shares en andere opslag resources, zoals BLOB-containers, wacht rijen of tabellen. Alle opslag resources die in een opslag account worden geïmplementeerd, delen de limieten die van toepassing zijn op dat opslag account. Zie [Azure files schaal baarheid en prestatie doelen](../articles/storage/files/storage-files-scale-targets.md)voor een overzicht van de huidige limieten voor een opslag account.

Er zijn twee hoofd typen opslag accounts die u kunt gebruiken voor Azure Files-implementaties: 
- **GPv2-opslag accounts (General version 2)** : met GPv2-opslag accounts kunt u Azure-bestands shares implementeren op standaard/harde schijf (op basis van HDD). Naast het opslaan van Azure-bestands shares kunnen GPv2-opslag accounts andere opslag resources, zoals BLOB-containers, wacht rijen of tabellen, opslaan. 
- **FileStorage-opslag accounts**: met FileStorage-opslag accounts kunt u Azure-bestands shares implementeren op op schijven gebaseerde en op basis van een op SSD (op apparaat gebaseerde) harde hardware. FileStorage-accounts kunnen alleen worden gebruikt voor het opslaan van Azure-bestands shares. Er kunnen geen andere opslag resources (BLOB-containers, wacht rijen, tabellen, enzovoort) worden geïmplementeerd in een FileStorage-account.

Er zijn verschillende typen opslag accounts die u kunt aankomen in de Azure Portal, Power shell of CLI. Twee opslag account typen, BlockBlobStorage-en BlobStorage-opslag accounts, kunnen geen Azure-bestands shares bevatten. De andere twee typen opslag accounts die u mogelijk ziet zijn algemene versie 1 (GPv1) en klassieke opslag accounts, beide kunnen Azure-bestands shares bevatten. Hoewel GPv1 en klassieke opslag accounts Azure-bestands shares kunnen bevatten, zijn de meeste nieuwe functies van Azure Files alleen beschikbaar in GPv2-en FileStorage-opslag accounts. Daarom raden we u aan om alleen GPv2-en FileStorage-opslag accounts te gebruiken voor nieuwe implementaties en om GPv1-en klassieke opslag accounts bij te werken als deze al in uw omgeving aanwezig zijn.  