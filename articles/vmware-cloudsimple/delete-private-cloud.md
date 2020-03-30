---
title: Een Azure VMware-oplossing verwijderen door CloudSimple Private Cloud
description: Beschrijft hoe u een CloudSimple Private Cloud verwijdert.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024752"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Een CloudSimple Private Cloud verwijderen

CloudSimple biedt de flexibiliteit om een Private Cloud te verwijderen.  Een Private Cloud bestaat uit een of meer vSphere-clusters. Elk cluster kan 3 tot 16 knooppunten hebben. Wanneer u een Private Cloud verwijdert, worden alle clusters verwijderd.

## <a name="before-you-begin"></a>Voordat u begint

Als u een Private Cloud verwijdert, wordt de hele Private Cloud verwijderd.  Alle onderdelen van de Private Cloud worden verwijderd.  Als u een van de gegevens wilt bewaren, moet u ervoor zorgen dat u een back-up van de gegevens hebt gemaakt naar on-premises opslag of Azure-opslag.

De onderdelen van een Private Cloud zijn:

* CloudSimple-knooppunten
* Virtuele machines
* VLAN's/subnetten
* Alle gebruikersgegevens die zijn opgeslagen in de Private Cloud
* Alle firewallregelbijlagen bij een VLAN/Subnet

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="delete-a-private-cloud"></a>Een privécloud verwijderen

1. [Toegang tot de CloudSimple-portal](access-cloudsimple-portal.md).

2. Open de pagina **Resources.**

3. Klik op de Private Cloud die u wilt verwijderen

4. Klik op de overzichtspagina op **Verwijderen**.

    ![Private cloud verwijderen](media/delete-private-cloud.png)

5. Voer op de bevestigingspagina de naam van de private cloud in en klik op **Verwijderen**. 

    ![Private cloud verwijderen - bevestigen](media/delete-private-cloud-confirm.png)

De Private Cloud is gemarkeerd voor verwijdering.  Het verwijderingsproces begint na drie uur en verwijdert de Private Cloud.

> [!CAUTION]
> Knooppunten moeten worden verwijderd na verwijdering van de Private Cloud.  Het meten van knooppunten wordt voortgezet totdat knooppunten uit uw abonnement worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

* [Knooppunten verwijderen](delete-nodes.md)
