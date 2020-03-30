---
title: Ondersteuning voor Rendermanager - Azure Batch
description: Azure Batch render manager-integratie gebruiken. Meer informatie over ingebouwde ondersteuning of add-ons voor populaire rendermanagers.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449685"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Azure Batch gebruiken met renderfarmmanagers

Als u een bestaand on-premises renderfarm gebruikt, is het zeer waarschijnlijk dat een rendermanager de rendercapaciteit van de farm beheert en taken rendert.

Azure biedt ingebouwde ondersteuning of add-ons voor populaire rendermanagers. Vervolgens u Azure VM's toevoegen en verwijderen, inclusief VM's met de pay-for-use toepassingslicenties en VM's met lage prioriteit.

De volgende rendermanagers worden ondersteund:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Koninklijke Render](https://www.royalrender.de/)
* [Thinkbox Deadline](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Azure Render Hub

Azure Render Hub vereenvoudigt het maken en beheren van Azure render farms.  Render Hub heeft native ondersteuning voor PipelineFx Qube en Deadline 10.  Zie voor meer informatie en gedetailleerde instructies [de GitHub repository.](https://github.com/Azure/azure-render-hub)

## <a name="using-azure-with-pipelinefx-qube"></a>Azure gebruiken met PipelineFX Qube

Azure Render Hub ondersteunt populaire rendermanagers, waaronder Deadline.  Zie [de GitHub-repository](https://github.com/Azure/azure-render-hub)voor instructies over het implementeren en gebruiken van Render Hub.

Scripts en instructies om Azure Batch-vm's te kunnen gebruiken als Qube-werknemers zijn ook beschikbaar in [de GitHub-repository.](https://github.com/Azure/azure-qube)

## <a name="using-azure-with-royal-render"></a>Azure gebruiken met Royal Render

Royal Render heeft Azure- en Azure Batch-integratie ingebouwd, zodat u een renderfarm uitbreiden met vm's op basis van Azure. Zie [de Help-bestanden voor](https://www.royalrender.de/help8/index.html?Cloudrendering.html)een overzicht .

Zie het [klantverhaal](https://customers.microsoft.com/story/jellyfishpictures)van Jellyfish Pictures voor een voorbeeld van een Royal Render-klant die de Azure-integratie gebruikt.

## <a name="using-azure-with-thinkbox-deadline"></a>Azure gebruiken met Thinkbox Deadline

Azure Render Hub ondersteunt populaire rendermanagers, waaronder Deadline.  Zie [de GitHub-repository](https://github.com/Azure/azure-render-hub)voor instructies over het implementeren en gebruiken van Render Hub.

## <a name="next-steps"></a>Volgende stappen

Probeer de Azure Batch-integratie voor uw rendermanager, met behulp van de juiste plug-in en instructies op GitHub, indien van toepassing.
