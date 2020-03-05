---
title: Zelf studie-een Jenkins-server maken in azure
description: In deze zelf studie installeert u Jenkins op een virtuele Azure Linux-machine uit de sjabloon voor de Jenkins-oplossing en maakt u een voor beeld van een Java-voorbeeld toepassing.
keywords: jenkins, azure, devops, portal, virtuele machine, oplossingssjabloon
ms.topic: tutorial
ms.date: 03/03/2020
ms.openlocfilehash: 3fa8dcbcc0edcc987fa9c4e76fbdb9d7a2f3396c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274898"
---
# <a name="tutorial-create-a-jenkins-server-on-an-azure-linux-vm"></a>Zelf studie: een Jenkins-server maken op een Azure Linux-VM 

In deze zelf studie ziet u hoe u [Jenkins](https://jenkins.io) op een Ubuntu Linux-VM installeert met de hulpprogram ma's en invoeg toepassingen die zijn geconfigureerd voor gebruik met Azure. Wanneer u klaar bent, beschikt u over een Jenkins-server die wordt uitgevoerd in Azure voor het bouwen van een Java-voorbeeld-app uit [GitHub](https://github.com).

> [!div class="checklist"]
> * Een Jenkins-server installeren en configureren op Azure
> * Toegang krijgen tot de Jenkins-console met behulp van een SSH-tunnel
> * Een freestyle-project maken
> * De code compileren en de voor beeld-app inpakken

[!INCLUDE [jenkins-install-solution-template-include](../../includes/jenkins-install-solution-template-steps.md)]