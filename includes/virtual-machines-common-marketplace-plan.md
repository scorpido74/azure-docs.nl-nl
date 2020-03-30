---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174944"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Een afbeelding implementeren met Marketplace-termen

Sommige VM-afbeeldingen in de Azure Marketplace hebben aanvullende licentie- en aankoopvoorwaarden die u moet accepteren voordat u ze programmatisch implementeren.  

Als u een vm uit een dergelijke afbeelding wilt implementeren, moet u zowel de voorwaarden van de afbeelding accepteren als programmatische implementatie inschakelen. U hoeft dit slechts één keer per abonnement te doen. Daarna moet u elke keer dat u een VM programmatisch implementeert vanuit de afbeelding, ook *parameters voor aankoopplannen* opgeven.

In de volgende secties wordt uitgelegd hoe u:

* Controleren of een Marketplace-afbeelding aanvullende licentievoorwaarden heeft 
* Accepteer de voorwaarden programmatisch
* Parameters voor aankoopplannen opgeven wanneer u een VM programmatisch implementeert

