---
title: Certificaten gebruiken-Azure Batch
description: Certificaten gebruiken om verificatie van toepassingen in te scha kelen
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 4da5fad63b148fa054eefb7f13424b46dc43bf29
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83764336"
---
# <a name="using-certificates-with-batch"></a>Certificaten gebruiken met batch

Momenteel is het gebruik van certificaten met batch niet mogelijk als er toepassingen worden uitgevoerd in Pools die moeten worden geverifieerd met een eind punt. 

Als u nog geen certificaat hebt, kunt u een zelfondertekend certificaat maken met behulp van het `makecert` opdracht regel programma.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Certificaten hand matig uploaden via de Azure Portal

1. Selecteer **certificaten** uit het batch-account waarnaar u een certificaat wilt uploaden en selecteer vervolgens **toevoegen**. 

2. Upload het certificaat met de extensie. pfx of. cer. 

Zodra het certificaat is geüpload, wordt het toegevoegd aan een lijst met certificaten en kunt u de vinger afdruk controleren.

Wanneer u nu een batch-pool maakt, kunt u naar certificaten in de groep navigeren en het certificaat toewijzen dat u hebt geüpload naar die groep.

## <a name="next-steps"></a>Volgende stappen

Batch heeft een certificaat-API, [AZ batch Certificate Create](https://docs.microsoft.com/cli/azure/batch/certificate?view=azure-cli-latest#az-batch-certificate-create)

Zie voor meer informatie over het gebruik van Key Vault [beveiligde toegang Key Vault met batch](credential-access-key-vault.md).
