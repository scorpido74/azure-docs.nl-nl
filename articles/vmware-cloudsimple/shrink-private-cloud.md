---
title: Azure VMware Solutions (AVS) Private Cloud verkleinen
description: Hierin wordt beschreven hoe u een Privécloud kunt verkleinen.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014263"
---
# <a name="shrink-an-avs-private-cloud"></a>Een Privécloud met een persoonlijke Cloud verkleinen

AVS biedt de flexibiliteit om een Privécloud in de Cloud dynamisch te verkleinen. Een automatische AVS-Cloud bestaat uit een of meer vSphere-clusters. Elk cluster kan 3 tot 16 knoop punten bevatten. Bij het verkleinen van een AVS-Privécloud verwijdert u een knoop punt uit het bestaande cluster of verwijdert u een volledig cluster. 

## <a name="before-you-begin"></a>Voordat u begint

Er moet aan de volgende voor waarden worden voldaan voordat een automatische AVS-Cloud wordt verkleind. Het beheer cluster (eerste cluster) wordt gemaakt tijdens het maken van de AVS-Privécloud. Deze kan niet worden verwijderd.

* Een vSphere-cluster moet drie knoop punten hebben. Een cluster met slechts drie knoop punten kan niet worden verkleind.
* Het totale aantal verbruikte opslag mag niet groter zijn dan de totale capaciteit na het verkleinen van het cluster.
* Controleer of een DRS-regel (Distributed resource Scheduler) voor komt dat vMotion van een virtuele machine wordt verhinderd. Als er regels aanwezig zijn, schakelt u de regels uit of verwijdert u deze. DRS-regels bevatten virtuele machine voor regels voor affiniteit met de host.


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="shrinking-an-avs-private-cloud"></a>Een persoonlijke cloud van AVS verkleinen

1. [Toegang tot de AVS-Portal](access-cloudsimple-portal.md).

2. Open de pagina **resources** .

3. Klik op de AVS-Privécloud die u wilt verkleinen

4. Klik op de pagina samen vatting op **verkleinen**.

    ![De Privécloud met persoonlijke Cloud verkleinen](media/shrink-private-cloud.png)

5. Selecteer het cluster dat u wilt verkleinen of verwijderen. 

    ![De persoonlijke AVS-Cloud verkleinen-cluster selecteren](media/shrink-private-cloud-select-cluster.png)

6. Selecteer **een knoop punt verwijderen** of **Verwijder het hele cluster**. 

7. De cluster capaciteit controleren

8. Klik op **verzenden** om de AVS-privécloud te verkleinen.

Het kleiner maken van de AVS-privécloud wordt gestart. U kunt de voortgang in taken bewaken. Het verkleinings proces kan enkele uren duren, afhankelijk van de gegevens, die opnieuw moeten worden gesynchroniseerd op vSAN.

> [!NOTE]
> 1. Als u een privécloud verkleint door de laatste of het enige cluster in het Data Center te verwijderen, wordt het Data Center niet verwijderd.
> 2. Als er sprake is van een schending van de DRS-regel, wordt het knoop punt niet verwijderd uit het cluster en wordt in de taak beschrijving aangegeven dat het verwijderen van een knoop punt in strijd is met DRS-regels op het cluster.    


## <a name="next-steps"></a>Volgende stappen

* [VMware-Vm's in azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [persoonlijke Clouds van AVS](cloudsimple-private-cloud.md)
