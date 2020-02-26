---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8a2e5defd0672516d52d4f3477641f39eca63368
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597788"
---
Standaard-bestands shares kunnen Maxi maal 5 TiB omvatten, hoewel de limiet voor delen kan worden verhoogd tot 100 TiB. Hiervoor moet de functie voor *grote bestands shares* worden ingeschakeld op het niveau van het opslag account. Premium Storage-accounts (*FileStorage* -opslag accounts) beschikken niet over de functie voor het delen van grote bestanden omdat alle Premium-bestands shares al zijn ingeschakeld voor het inrichten van maxi maal 100 TIB capaciteit.

U kunt grote bestands shares alleen inschakelen op lokaal redundante of zone redundante standaard opslag accounts. Wanneer u de functie vlag voor grote bestands shares hebt ingeschakeld, kunt u het redundantie niveau niet wijzigen in geografisch redundante opslag of geo-zone-redundantie.

Als u grote bestands shares wilt inschakelen voor een bestaand opslag account, gaat u naar de **configuratie** weergave van de inhouds opgave van het opslag account en schakelt u de schakel optie voor grote bestands shares op ingeschakeld:

![Een scherm opname van de schakel optie grote bestands shares beschikbaar maken in de Azure Portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

U kunt ook 100 TiB-bestands shares inschakelen via de [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) Power shell-cmdlet en de [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) Azure cli-opdracht.

Zie [een Azure-bestands share maken](../articles/storage/files/storage-how-to-create-file-share.md)voor meer informatie over het inschakelen van grote bestands shares voor nieuwe opslag accounts.