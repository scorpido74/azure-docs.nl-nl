---
title: Een Azure VMware-oplossing verwijderen via CloudSimple Private Cloud
description: Hierin wordt beschreven hoe u een CloudSimple-Privécloud verwijdert.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972812"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Een CloudSimple-Privécloud verwijderen

CloudSimple biedt de flexibiliteit voor het verwijderen van een Privécloud.  Een Privécloud bestaat uit een of meer vSphere-clusters. Elk cluster kan 3 tot 16 knoop punten bevatten. Wanneer u een Privécloud verwijdert, worden alle clusters verwijderd.

## <a name="before-you-begin"></a>Voordat u begint

Als een Privécloud wordt verwijderd, wordt de volledige Privécloud verwijderd.  Alle onderdelen van de Privécloud worden verwijderd.  Als u gegevens wilt bewaren, moet u ervoor zorgen dat u een back-up hebt gemaakt van de gegevens naar on-premises opslag of Azure Storage.

De onderdelen van een Privécloud zijn:

* CloudSimple-knoop punten
* Virtuele machines
* VLAN'S/subnetten
* Alle gebruikers gegevens die in de Privécloud zijn opgeslagen
* Alle bijlagen van firewall regels naar een VLAN/subnet

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Een Privécloud verwijderen

1. [Toegang tot de CloudSimple-Portal](access-cloudsimple-portal.md).

2. Open de pagina **resources** .

3. Klik op de Privécloud die u wilt verwijderen

4. Klik op de pagina samen vatting op **verwijderen**.

    ![Privécloud verwijderen](media/delete-private-cloud.png)

5. Voer op de pagina Bevestiging de naam van de Privécloud in en klik op **verwijderen**. 

    ![Privécloud verwijderen-bevestigen](media/delete-private-cloud-confirm.png)

De Privécloud is gemarkeerd voor verwijdering.  Het verwijderings proces wordt na drie uur gestart en de Privécloud wordt verwijderd.

> [!CAUTION]
> Knoop punten moeten worden verwijderd na het verwijderen van de Privécloud.  Door knoop punten te meten, worden de kassa knooppunten verwijderd uit uw abonnement.

## <a name="next-steps"></a>Volgende stappen

* [Knoop punten verwijderen](delete-nodes.md)
