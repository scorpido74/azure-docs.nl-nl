---
title: Azure VMware-oplossing krimpen door CloudSimple Private Cloud
description: Beschrijft hoe u een CloudSimple Private Cloud krimpen.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014263"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Een CloudSimple Private Cloud verkleinen

CloudSimple biedt de flexibiliteit om een Private Cloud dynamisch te verkleinen.  Een Private Cloud bestaat uit een of meer vSphere-clusters. Elk cluster kan 3 tot 16 knooppunten hebben. Wanneer u een private cloud inkrimpt, verwijdert u een knooppunt uit het bestaande cluster of verwijdert u een heel cluster. 

## <a name="before-you-begin"></a>Voordat u begint

Aan de volgende voorwaarden moet worden voldaan voor de krimp van een Private Cloud.  Beheercluster (eerste cluster) dat is gemaakt toen een Private Cloud is gemaakt, kan niet worden verwijderd.

* Een vSphere-cluster moet drie knooppunten hebben.  Een cluster met slechts drie knooppunten kan niet worden gekrompen.
* De totale verbruikte opslag mag de totale capaciteit na de inkrimping van het cluster niet overschrijden.
* Controleer of een DRS-regels (Distributed Resource Scheduler) vMotion van een virtuele machine voorkomt.  Als er regels zijn, schakelt u de regels uit of verwijdert u deze.  DRS-regels omvatten virtuele machine om affiniteitsregels te hosten.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="shrink-a-private-cloud"></a>Een privécloud verkleinen

1. [Toegang tot de CloudSimple-portal](access-cloudsimple-portal.md).

2. Open de pagina **Resources.**

3. Klik op de Private Cloud die u wilt verkleinen

4. Klik op de overzichtspagina op **Krimpen**.

    ![Private cloud krimpen](media/shrink-private-cloud.png)

5. Selecteer het cluster dat u wilt verkleinen of verwijderen. 

    ![Private cloud verkleinen - cluster selecteren](media/shrink-private-cloud-select-cluster.png)

6. Selecteer **Eén knooppunt verwijderen** of Het hele cluster **verwijderen**. 

7. Controleer de clustercapaciteit

8. Klik **op Verzenden** om de private cloud te verkleinen.

Krimp van de Private Cloud begint.  U de voortgang van taken controleren.  Het krimpproces kan een paar uur duren, afhankelijk van de gegevens, die moeten worden gesynchroniseerd op vSAN.

> [!NOTE]
> 1. Als u een private cloud krimpt door het laatste of enige cluster in het datacenter te verwijderen, wordt het datacenter niet verwijderd.
> 2. Als er een OVERTREDING van de DRS-regel optreedt, wordt knooppunt niet uit het cluster verwijderd en toont de taakbeschrijving aan dat het verwijderen van een knooppunt in strijd is met DRS-regels in het cluster.    


## <a name="next-steps"></a>Volgende stappen

* [VMware-VM's in Azure gebruiken](quickstart-create-vmware-virtual-machine.md)
* Meer informatie over [Private Clouds](cloudsimple-private-cloud.md)
