---
title: Het formaat van de capaciteits groep of een volume voor Azure NetApp Files wijzigen | Microsoft Docs
description: Hierin wordt beschreven hoe u de grootte van een capaciteits groep of een volume wijzigt.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: c58ceef57b984f46b86bb2a8577c53b75082b78b
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "65794614"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Het formaat van een capaciteitspool of volume wijzigen
U kunt de grootte van een capaciteits groep of een volume zo nodig wijzigen. 

## <a name="resize-the-capacity-pool"></a>Grootte van de capaciteits groep wijzigen 

U kunt de grootte van de capaciteits groep wijzigen in stappen van 1-TiB of verlaagt. De grootte van de capaciteits groep mag echter niet kleiner zijn dan 4 TiB. Het wijzigen van de grootte van de capaciteits groep wijzigt de gekochte Azure NetApp Files capaciteit.

1. Klik op de Blade NetApp-account beheren op de capaciteits groep waarvan u de grootte wilt wijzigen. 
2. Klik met de rechter muisknop op de naam van de capaciteits groep of klik op de... pictogram aan het einde van de rij van de capaciteits groep om het context menu weer te geven. 
3. Gebruik de opties in het context menu om het formaat van de capaciteits groep te wijzigen of te verwijderen.

## <a name="resize-a-volume"></a>Het formaat van een volume wijzigen

U kunt de grootte van een volume indien nodig wijzigen. Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool.

1. Klik op de Blade NetApp-account beheren op **volumes**. 
2. Klik met de rechter muisknop op de naam van het volume waarvan u het formaat wilt wijzigen of klik op de... pictogram aan het einde van de rij van het volume om het context menu weer te geven.
3. Gebruik de opties in het context menu om het volume te verg Roten of verkleinen of verwijderen.

