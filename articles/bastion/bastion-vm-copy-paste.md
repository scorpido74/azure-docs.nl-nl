---
title: 'Kopiëren en plakken van en naar een virtuele machine: Azure Bastion'
description: Lees in dit artikel hoe kopiëren en plakken van en naar een Azure VM met Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 0aaf816cdfe7d42fd345eb4f010cf47b1615f462
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989532"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>Kopiëren en plakken naar een virtuele machine: Azure Bastion

Met dit artikel u tekst kopiëren en plakken van en naar virtuele machines wanneer u Azure Bastion gebruikt. Voordat u met een vm werkt, moet u ervoor zorgen dat u de stappen hebt gevolgd om [een Bastion-host](bastion-create-host-portal.md)te maken. Maak vervolgens verbinding met de VM waarmee u wilt werken met [RDP](bastion-connect-vm-rdp.md) of [SSH.](bastion-connect-vm-ssh.md)

Voor browsers die de geavanceerde Clipboard API-toegang ondersteunen, u tekst kopiëren en plakken tussen uw lokale apparaat en de externe sessie op dezelfde manier als u kopieert en plakt tussen toepassingen op uw lokale apparaat. Voor andere browsers u het palet van het gereedschap Bastion-klembordtoegangsgebruiken gebruiken.

   ![Klembord toestaan](./media/bastion-vm-manage/allow.png)

Alleen tekstkopiëren/plakken wordt ondersteund. Voor directe kopiëren en plakken kan uw browser u om toegang tot het klembord vragen wanneer de Bastion-sessie wordt geïnitialiseerd. **Geef** de webpagina toegang tot het klembord.

## <a name="copy-to-a-remote-session"></a><a name="to"></a>Kopiëren naar een externe sessie

Nadat u verbinding hebt gemaakt met de virtuele machine via de [Azure-portal, ](https://portal.azure.com)voert u de volgende stappen uit:

1. Kopieer de tekst/inhoud van het lokale apparaat naar het lokale klembord.
1. Start tijdens de externe sessie het gereedschapspalet Bastion-klembordtoegang door de twee pijlen te selecteren. De pijlen bevinden zich in het midden van de sessie.

   ![gereedschapspalet](./media/bastion-vm-manage/left.png)

   ![toetsenbord](./media/bastion-vm-manage/clipboard.png)
1. Meestal wordt de gekopieerde tekst automatisch weergegeven op het palet Bastion copy paste. Als uw tekst er niet is, plakt u de tekst in het tekstgebied op het palet.
1. Zodra de tekst zich in het tekstgebied bevindt, u deze plakken op de externe sessie.

   ![Plakken](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>Kopiëren vanuit een externe sessie

Nadat u verbinding hebt gemaakt met de virtuele machine via de [Azure-portal, ](https://portal.azure.com)voert u de volgende stappen uit:

1. Kopieer de tekst/inhoud van de externe sessie naar een extern klembord (met Ctrl-C).

   ![gereedschapspalet](./media/bastion-vm-manage/remote.png)
1. Start tijdens de externe sessie het gereedschapspalet Bastion-klembordtoegang door de twee pijlen te selecteren. De pijlen bevinden zich in het midden van de sessie.

   ![toetsenbord](./media/bastion-vm-manage/clipboard2.png)
1. Meestal wordt de gekopieerde tekst automatisch weergegeven op het palet Bastion copy paste. Als uw tekst er niet is, plakt u de tekst in het tekstgebied op het palet.
1. Zodra de tekst zich in het tekstgebied bevindt, u deze plakken op het lokale apparaat.

   ![Plakken](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>Volgende stappen

Lees de [Bastion FAQ](bastion-faq.md).