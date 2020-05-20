---
title: Verbinding maken met een Azure Lab Services-VM vanuit Mac | Microsoft Docs
description: Meer informatie over het verbinden van een Mac met een virtuele machine in Azure Lab Services.
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
ms.openlocfilehash: 9087e4fb7989f89c19a70afd9ee8f061f9061166
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704462"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Verbinding maken met een virtuele machine met behulp van Remote Desktop Protocol op een Mac
In deze sectie wordt uitgelegd hoe een student via RDP verbinding kan maken met een leslokaal-VM van een klas hoofd omgeving.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Microsoft Extern bureaublad installeren op een Mac
1. Open de App Store op uw Mac en zoek naar **Microsoft extern bureaublad**.

    ![Microsoft Extern bureaublad](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Installeer de meest recente versie van Microsoft Extern bureaublad. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Toegang tot de virtuele machine vanaf uw Mac met RDP
1. Open het **RDP** -bestand dat op uw computer is gedownload met **Microsoft extern bureaublad** geïnstalleerd. Het moet beginnen met het maken van verbinding met de virtuele machine. 

    ![Verbinding maken met de virtuele machine](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Selecteer **door gaan** als u de volgende waarschuwing ontvangt. 

    ![Certificaat waarschuwing](../media/how-to-use-classroom-lab/certificate-error.png)
1. De virtuele machine wordt weer geven. 

    > [!NOTE]
    > Het volgende voor beeld is voor een CentOS Linux-VM. 

    ![VM](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Volgende stappen
Zie [extern bureau blad gebruiken voor virtuele Linux-machines](how-to-use-remote-desktop-linux-student.md) voor meer informatie over het maken van verbinding met Linux-vm's met behulp van RDP.


