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
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536477"
---
Standaard kunnen standaard bestandsshares slechts tot 5 TiB overspannen, hoewel de limiet voor het aandeel kan worden verhoogd tot 100 TiB. Hiervoor moet *de functie voor grote bestandsshare-functies* zijn ingeschakeld op het niveau van de opslagaccount. Premium opslagaccounts *(FileStorage-opslagaccounts)* hebben niet de vlag van de grote bestandsshare-functie, omdat alle premium bestandsshares al zijn ingeschakeld voor het inrichten van maximaal de volledige 100 TiB-capaciteit.

U alleen grote bestandsshares inschakelen op lokaal redundante of zoneredundante standaardopslagaccounts. Nadat u de vlag van de grote bestandssharefunctie hebt ingeschakeld, u het redundantieniveau niet wijzigen in georedundante of geozoneredundante opslag.

Als u grote bestandsshares op een bestaand opslagaccount wilt inschakelen, navigeert u naar de **configuratieweergave** in de inhoudsopgave van het opslagaccount en schakelt u de grote schakelaar voor het delen van bestanden naar ingeschakeld:

![Een schermafbeelding van de schakelaar voor het inschakelen van grote bestandssharerockers in de Azure-portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

U ook 100 TiB-bestandsshares inschakelen via [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) de PowerShell-cmdlet en de opdracht Azure CLI. Zie grote bestandsshares inschakelen en maken voor gedetailleerde instructies over het inschakelen van grote [bestandsshares.](../articles/storage/files/storage-files-how-to-create-large-file-share.md)

Zie [Een Azure-bestandsshare maken](../articles/storage/files/storage-how-to-create-file-share.md)voor meer informatie over het maken van bestandsshares op nieuwe opslagaccounts.
