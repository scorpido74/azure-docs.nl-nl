---
title: Zelfstudie - Een Jenkins-server maken op Azure
description: In deze zelfstudie installeert u Jenkins op een virtuele Azure Linux-machine vanuit de jenkins-oplossingssjabloon en bouwt u een voorbeeld-Java-toepassing.
keywords: jenkins, azure, devops, portal, virtuele machine, oplossingssjabloon
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274898"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Zelfstudie: Een Jenkins-server maken op een Azure Linux VM 

In deze zelfstudie ziet u hoe [jenkins](https://jenkins.io) op een Ubuntu Linux VM worden geïnstalleerd met de hulpprogramma's en plug-ins die zijn geconfigureerd om met Azure te werken. Wanneer u klaar bent, beschikt u over een Jenkins-server die wordt uitgevoerd in Azure voor het bouwen van een Java-voorbeeld-app uit [GitHub](https://github.com).

> [!div class="checklist"]
> * Een Jenkins-server installeren en configureren op Azure
> * Toegang tot de Jenkins-console met een SSH-tunnel
> * Een Freestyle-project maken
> * De code compileren en de voorbeeld-app verpakken

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]