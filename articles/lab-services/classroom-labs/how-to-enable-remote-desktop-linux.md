---
title: Extern bureau blad inschakelen voor Linux in Azure Lab Services | Microsoft Docs
description: Meer informatie over het inschakelen van extern bureau blad voor virtuele Linux-machines in een lab in Azure Lab Services.
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
ms.openlocfilehash: 3d08105e78274300eb7ee0a8c0ad146a737d0ffa
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69644959"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Extern bureau blad voor virtuele Linux-machines in een Lab inschakelen in Azure Lab Services
In dit artikel leest u hoe u de volgende taken kunt uitvoeren:

- Extern bureau blad voor Linux VM inschakelen
- Hoe docenten verbinding kan maken met de sjabloon-VM via Verbinding met extern bureaublad (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Extern bureau blad voor Linux VM inschakelen
Tijdens het maken van een Lab kunnen docenten **verbinding met extern bureau blad** inschakelen voor **Linux** -installatie kopieën. De optie **verbinding met extern bureaublad inschakelen** wordt weer gegeven wanneer een Linux-installatie kopie voor de sjabloon is geselecteerd. Wanneer deze optie is ingeschakeld, kunnen docenten verbinding maken met virtuele machine-en student-Vm's via RDP (Extern bureaublad). 

![Verbinding met extern bureau blad inschakelen voor een Linux-installatie kopie](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Selecteer in het vak **verbinding met extern bureaublad bericht inschakelen** de optie **door gaan met extern bureaublad**. 

![Verbinding met extern bureau blad inschakelen voor een Linux-installatie kopie](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Het inschakelen van **verbinding met extern bureau blad** opent alleen de **RDP** -poort op Linux-machines. Als docent maakt u voor het eerst verbinding met de Linux-machine met behulp van SSH en installeert u RDP-en GUI-pakketten zodat u later met RDP verbinding kunt maken met de Linux-machine. Vervolgens **publiceert** u de installatie kopie zodat studenten RDP kunnen hebben in de student Linux-vm's. 

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
Op dit moment wordt de verbinding met extern bureau blad ondersteund voor de volgende besturings systemen:

- openSUSE Schrikkel 42,3
- CentOS-gebaseerde 7,5
- Debian 9 "stretch"
- Ubuntu Server 16,04 LTS

## <a name="teachers-connecting-to-the-template-vm-using-rdp"></a>Docenten die verbinding maken met de sjabloon-VM met behulp van RDP
Docenten moeten eerst via SSH verbinding maken met de VM van de sjabloon en vervolgens RDP-en GUI-pakketten installeren. Vervolgens kunnen de docenten de volgende stappen gebruiken om verbinding te maken met de virtuele Linux-machines met behulp van RDP: 

U ziet de **extern bureaublad** optie om verbinding te maken met de VM van de sjabloon op het moment van het maken van het lab. 

![Verbinding maken met sjabloon via RDP op het moment van maken](../media/how-to-enable-remote-desktop-linux/connect-at-creation.png)

U ziet de optie **extern bureaublad** op de start pagina van het lab nadat het lab is gemaakt en de sjabloon-VM wordt gestart. Start de sjabloon-VM als deze nog niet is gestart. 

![Verbinding maken met sjabloon via RDP nadat het lab is gemaakt](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 

Zie [verbinding maken via SSH of RDP] ((#connect-using-SSH-of-RDP) voor meer informatie over het maken van verbinding met de virtuele machine met behulp van SSH of RDP. 

## <a name="teachers-connecting-to-a-student-vm-using-rdp"></a>Docenten die verbinding maken met een student-VM met behulp van RDP
Een docent/docent kan verbinding maken met een student-VM door over te scha kelen naar de weer gave **virtual machines** en het pictogram **verbinding** te selecteren. Daarom moeten docenten de sjabloon installatie kopie **publiceren** met RDP-en GUI-Pakketten die erop zijn geïnstalleerd. 

![Docenten die verbinding maken met de student-VM](../media/how-to-enable-remote-desktop-linux/teacher-connect-to-student-vm.png)

Zie [verbinding maken via SSH of RDP] ((#connect-using-SSH-of-RDP) voor meer informatie over het maken van verbinding met de virtuele machine met behulp van SSH of RDP. 

## <a name="connect-using-ssh-or-rdp"></a>Verbinding maken via SSH of RDP
Als u de **SSH** -optie selecteert, ziet u het volgende in het dialoog venster **verbinding maken met uw virtuele machine** :  

![SSH-connection string](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Selecteer de knop **kopiëren** naast het tekstvak om deze naar het klem bord te kopiëren. Sla de SSH-connection string op. Gebruik deze connection string van een SSH-terminal ( [](https://www.putty.org/)zoals Putty) om verbinding te maken met de virtuele machine.

Als u de **RDP** -optie selecteert, wordt een RDP-bestand gedownload op uw computer. Sla het op en open het om verbinding te maken met de computer. 

## <a name="next-steps"></a>Volgende stappen
Nadat een docent de functie extern bureau blad-verbinding heeft ingeschakeld, kunnen studenten via RDP/SSH verbinding maken met hun Vm's. Zie [extern bureau blad voor Linux-Vm's gebruiken in een leslokaal Lab](how-to-use-remote-desktop-linux-student.md)voor meer informatie. 