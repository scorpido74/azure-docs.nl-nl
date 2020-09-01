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
ms.openlocfilehash: 5f7d547ad549b2747ae41182ee4058b001d9e78a
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146382"
---
# <a name="using-certificates-with-batch"></a>Certificaten gebruiken met Batch

Momenteel is het gebruik van certificaten met batch niet mogelijk als er toepassingen worden uitgevoerd in Pools die moeten worden geverifieerd met een eind punt. 

Als u nog geen certificaat hebt, kunt u een zelfondertekend certificaat maken met behulp van het `makecert` opdracht regel programma.

## <a name="upload-certificates-manually-through-the-azure-portal"></a>Certificaten hand matig uploaden via de Azure Portal

1. Selecteer **certificaten** uit het batch-account waarnaar u een certificaat wilt uploaden en selecteer vervolgens **toevoegen**. 

2. Upload het certificaat met de extensie. pfx of. cer. 

Zodra het certificaat is geüpload, wordt het toegevoegd aan een lijst met certificaten en kunt u de vinger afdruk controleren.

Wanneer u nu een batch-pool maakt, kunt u naar certificaten in de groep navigeren en het certificaat toewijzen dat u hebt geüpload naar die groep.

## <a name="next-steps"></a>Volgende stappen

Batch heeft een certificaat-API, [AZ batch Certificate Create](/cli/azure/batch/certificate)

Zie voor meer informatie over het gebruik van Key Vault [beveiligde toegang Key Vault met batch](credential-access-key-vault.md).
