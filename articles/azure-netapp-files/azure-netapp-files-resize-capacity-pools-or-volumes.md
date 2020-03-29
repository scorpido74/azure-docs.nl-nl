---
title: Het formaat van de capaciteitsgroep of een volume voor Azure NetApp-bestanden wijzigen | Microsoft Documenten
description: Beschrijft hoe u de grootte van een capaciteitsgroep of een volume wijzigen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65794614"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Het formaat van een capaciteitspool of volume wijzigen
U de grootte van een capaciteitsgroep of een volume indien nodig wijzigen. 

## <a name="resize-the-capacity-pool"></a>Het formaat van de capaciteitsgroep wijzigen 

U de grootte van de capaciteitspool wijzigen in stappen of decrements van 1-TiB. De grootte van de capaciteitspool mag echter niet kleiner zijn dan 4 TiB. Als u het formaat van de capaciteitsgroep wijzigt, wordt de aangeschafte capaciteit van Azure NetApp-bestanden gewijzigd.

1. Klik in het netapp-accountblad beheren op de capaciteitsgroep die u wilt aanpassen. 
2. Klik met de rechtermuisknop op de naam van de capaciteitsgroep of klik op de "..." pictogram aan het einde van de rij capaciteitpool om het contextmenu weer te geven. 
3. Gebruik de opties voor het contextmenu om het formaat van de capaciteitsgroep te wijzigen of te verwijderen.

## <a name="resize-a-volume"></a>Het formaat van een volume wijzigen

U de grootte van een volume zo nodig wijzigen. Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool.

1. Klik in het blad NetApp-account beheren op **Volumes**. 
2. Klik met de rechtermuisknop op de naam van het volume waarvan u het formaat wilt wijzigen of klik op de "..." pictogram aan het einde van de rij van het volume om het contextmenu weer te geven.
3. Gebruik de opties van het contextmenu om het formaat van het volume te wijzigen of te verwijderen.

