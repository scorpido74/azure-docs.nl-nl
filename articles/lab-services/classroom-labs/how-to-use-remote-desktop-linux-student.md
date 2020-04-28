---
title: Extern bureau blad voor Linux gebruiken in Azure Lab Services | Microsoft Docs
description: Meer informatie over het gebruik van extern bureau blad voor virtuele Linux-machines in een lab in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: 0f45af2730b05998fc82212c63778c89bb16b6ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73585079"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Extern bureau blad voor virtuele Linux-machines gebruiken in een leslokaal Lab van Azure Lab Services
In dit artikel wordt beschreven hoe studenten via RDP/SSH verbinding kunnen maken met een virtuele Linux-machine (VM) in een lab. 

Een docent moet de functie verbinding met extern bureau blad inschakelen voordat studenten verbinding kunnen maken met de VM van het klas Lab. Zie [extern bureau blad inschakelen voor virtuele Linux-machines](how-to-enable-remote-desktop-linux.md)voor instructies over hoe een docent de functie verbinding met extern bureau blad kan inschakelen.

> [!IMPORTANT] 
> Het inschakelen van **verbinding met extern bureau blad** opent alleen de **RDP** -poort op Linux-machines. Een docent kan voor het eerst via SSH verbinding maken met de Linux-machine en vervolgens RDP-en GUI-pakketten installeren, zodat u later met RDP verbinding kunt maken met de Linux-machine. 

## <a name="connect-to-the-student-vm"></a>Verbinding maken met de student-VM
Studenten kunnen RDP in de Linux-Vm's hebben nadat de eigenaar van het lab (docent/Professor) de VM van de sjabloon heeft **gepubliceerd** met RDP-en GUI-Pakketten die op de computer zijn geïnstalleerd. Dit zijn de stappen: 

1. Wanneer een student zich rechtstreeks aanmeldt bij de Labs`https://labs.azure.com`-Portal () of door een registratie`https://labs.azure.com/register/<registrationCode>`koppeling () te gebruiken, wordt er een tegel voor elk lab waartoe de student toegang heeft, weer gegeven. 
2. Schakel op de tegel de knop in om de virtuele machine te starten als deze de status gestopt heeft. 
3. Selecteer **Verbinden**. U ziet twee opties om verbinding te maken met de VM: **SSH** en **extern bureaublad**.

    ![VM van student-verbindings opties](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Verbinding maken via SSH of RDP
Als u de **SSH** -optie selecteert, ziet u het volgende in het dialoog venster **verbinding maken met uw virtuele machine** :  

![SSH-connection string](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Selecteer de knop **kopiëren** naast het tekstvak om deze naar het klem bord te kopiëren. Sla de SSH-connection string op. Gebruik deze connection string van een SSH-terminal (zoals [putty](https://www.putty.org/)) om verbinding te maken met de virtuele machine.

Als u de **RDP** -optie selecteert, wordt een RDP-bestand gedownload op uw computer. Sla het op en open het om verbinding te maken met de computer. 

## <a name="next-steps"></a>Volgende stappen
Zie [extern bureau blad inschakelen voor virtuele Linux-machines](how-to-enable-remote-desktop-linux.md)voor meer informatie over het inschakelen van de functie verbinding met extern bureau blad voor Linux vm's in een leslokaal Lab. 

