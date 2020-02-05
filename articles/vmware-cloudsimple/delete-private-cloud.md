---
title: Een persoonlijke cloud van Azure VMware Solutions (AVS) verwijderen
description: Hierin wordt beschreven hoe u een Privécloud kunt verwijderen.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77cbfb19c3861bac517142f7491e6b1a5fb4ca27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024752"
---
# <a name="delete-an-avs-private-cloud"></a>Een Privécloud verwijderen

AVS biedt de flexibiliteit voor het verwijderen van een automatische AVS-Cloud. Een automatische AVS-Cloud bestaat uit een of meer vSphere-clusters. Elk cluster kan 3 tot 16 knoop punten bevatten. Wanneer u een Privécloud van een AVS verwijdert, worden alle clusters verwijderd.

## <a name="before-you-begin"></a>Voordat u begint

Als een automatische AVS-Cloud wordt verwijderd, wordt de volledige AVS-privécloud verwijderd. Alle onderdelen van de AVS-Privécloud worden verwijderd. Als u gegevens wilt bewaren, moet u ervoor zorgen dat u een back-up hebt gemaakt van de gegevens naar on-premises opslag of Azure Storage.

De onderdelen van een AVS-Privécloud zijn:

* AVS-knoop punten
* Virtuele machines
* VLAN'S/subnetten
* Alle gebruikers gegevens die zijn opgeslagen op de AVS-Privécloud
* Alle bijlagen van firewall regels naar een VLAN/subnet

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-an-avs-private-cloud"></a>Een Privécloud verwijderen

1. [Toegang tot de AVS-Portal](access-cloudsimple-portal.md).

2. Open de pagina **resources** .

3. Klik op de AVS-Privécloud die u wilt verwijderen

4. Klik op de pagina samen vatting op **verwijderen**.

    ![Privécloud verwijderen](media/delete-private-cloud.png)

5. Voer op de pagina Bevestiging de naam in van de Privécloud en klik op **verwijderen**. 

    ![Automatische AVS-Cloud verwijderen-bevestigen](media/delete-private-cloud-confirm.png)

De AVS-privécloud is gemarkeerd voor verwijdering. Het verwijderings proces wordt na drie uur gestart en de Privécloud is verwijderd.

> [!CAUTION]
> Knoop punten moeten worden verwijderd na het verwijderen van de Privécloud van de AVS. Het meten van knoop punten gaat door totdat knoop punten uit uw abonnement worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

* [Knoop punten verwijderen](delete-nodes.md)
