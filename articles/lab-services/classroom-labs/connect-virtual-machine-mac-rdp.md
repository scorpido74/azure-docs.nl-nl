---
title: Verbinding maken met een Azure Lab Services VM van Mac | Microsoft Documenten
description: Dit artikel
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503086"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Verbinding maken met een vm met RDP op een Mac
In dit gedeelte ziet u hoe een student vanaf een Mac verbinding kan maken met een lab-vm in een klaslokaal met RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Microsoft Remote Desktop installeren op een Mac
1. Open de App Store op uw Mac en zoek naar **Microsoft Remote Desktop**.

    ![Microsoft Extern bureaublad](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Installeer de nieuwste versie van Microsoft Remote Desktop. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Toegang tot de VM vanaf uw Mac met RDP
1. Open het **RDP-bestand** dat op uw computer is gedownload en **Microsoft Extern bureaublad** is geïnstalleerd. Het moet beginnen met het verbinden met de VM. 

    ![Verbinding maken met de virtuele machine](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Selecteer **Doorgaan** als u de volgende waarschuwing ontvangt. 

    ![Certificaatwaarschuwing](../media/how-to-use-classroom-lab/certificate-error.png)
1. Je zou de VM moeten zien. 

    > [!NOTE]
    > Het volgende voorbeeld is voor een CentOS Linux VM. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Volgende stappen
Zie [Extern bureaublad gebruiken voor virtuele Linux-machines](how-to-use-remote-desktop-linux-student.md) voor meer informatie over het maken van verbinding met Linux-VM's met RDP


