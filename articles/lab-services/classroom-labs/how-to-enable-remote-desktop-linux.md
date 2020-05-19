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
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a40575340fc5b1c202be6b001807085954439f03
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588153"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Extern bureau blad voor virtuele Linux-machines in een Lab inschakelen in Azure Lab Services
In dit artikel leest u hoe u de volgende taken kunt uitvoeren:

- Extern bureau blad voor Linux VM inschakelen
- Hoe een docent verbinding kan maken met de sjabloon-VM via Verbinding met extern bureaublad (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Extern bureau blad voor Linux VM inschakelen
Tijdens het maken van een Lab kunnen docenten **verbinding met extern bureau blad** inschakelen voor **Linux** -installatie kopieën. De optie **verbinding met extern bureaublad inschakelen** wordt weer gegeven wanneer een Linux-installatie kopie voor de sjabloon is geselecteerd. Als deze optie is ingeschakeld, kunnen docenten verbinding maken met virtuele machine-en student Vm's via RDP (Extern bureaublad). 

![Verbinding met extern bureau blad inschakelen voor een Linux-installatie kopie](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Selecteer in het vak **verbinding met extern bureaublad bericht inschakelen** de optie **door gaan met extern bureaublad**. 

![Verbinding met extern bureau blad inschakelen voor een Linux-installatie kopie](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Het inschakelen van **verbinding met extern bureau blad** opent alleen de **RDP** -poort op Linux-machines. Als RDP al is geïnstalleerd en geconfigureerd op de installatie kopie van de virtuele machine, kunnen u/studenten verbinding maken met Vm's via RDP zonder dat u extra stappen hoeft te volgen.
> 
> Als RDP niet is geïnstalleerd en geconfigureerd op de installatie kopie van de virtuele machine, moet u voor het eerst verbinding maken met de Linux-computer en vervolgens RDP-en GUI-pakketten installeren, zodat u/studenten later met RDP verbinding kunnen maken met de Linux-machine. Zie [extern bureaublad installeren en configureren om verbinding te maken met een virtuele Linux-machine in azure](../../virtual-machines/linux/use-remote-desktop.md)voor meer informatie. Vervolgens publiceert u de installatie kopie zodat studenten RDP kunnen hebben in de student Linux-Vm's. 

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
Op dit moment wordt de verbinding met extern bureau blad ondersteund voor de volgende besturings systemen:

- openSUSE Schrikkel 42,3
- CentOS-gebaseerde 7,5
- Debian 9 "stretch"
- Ubuntu Server 16,04 LTS

## <a name="connect-to-the-template-vm"></a>Verbinding maken met de sjabloon-VM 
Docenten moeten eerst via SSH verbinding maken met de VM van de sjabloon en vervolgens RDP-en GUI-pakketten installeren. De docenten kunnen vervolgens RDP gebruiken om verbinding te maken met de VM van de sjabloon: 

1. Als u de **sjabloon aanpassen** op de werk balk ziet, selecteert u deze. Selecteer vervolgens **door gaan** in het dialoog venster **sjabloon aanpassen** . Met deze actie wordt de VM van de sjabloon gestart.  

    ![Sjabloon aanpassen](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Nadat de sjabloon van de virtuele machine is gestart, kunt u **verbinding maken sjabloon** selecteren en vervolgens **via SSH verbinding maken** op de werk balk. 

    ![Verbinding maken met sjabloon via RDP nadat het lab is gemaakt](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. U ziet het volgende: verbinding maken met het dialoog venster **virtuele machine** . Selecteer de knop **kopiëren** naast het tekstvak om deze naar het klem bord te kopiëren. Sla de SSH-connection string op. Gebruik deze connection string van een SSH-terminal (zoals [putty](https://www.putty.org/)) om verbinding te maken met de virtuele machine.
 
    ![SSH-connection string](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Installeer RDP-en GUI-pakketten zodat u/studenten later met RDP verbinding kunnen maken met de Linux-machine. Zie [extern bureaublad installeren en configureren om verbinding te maken met een virtuele Linux-machine in azure](../../virtual-machines/linux/use-remote-desktop.md)voor meer informatie. Vervolgens publiceert u de installatie kopie zodat studenten RDP kunnen hebben in de student Linux-Vm's.
5. Nadat deze pakketten zijn geïnstalleerd, kunt u de **verbinding met sjabloon** op de werk balk gebruiken en vervolgens **verbinding maken via RDP** selecteren om verbinding te maken met de sjabloon-VM via RDP. Sla het RDP-bestand op en gebruik het om verbinding te maken met de sjabloon-VM via RDP. 

## <a name="next-steps"></a>Volgende stappen
Nadat een docent de functie extern bureau blad-verbinding heeft ingeschakeld, kunnen studenten via RDP/SSH verbinding maken met hun Vm's. Zie [extern bureau blad voor Linux-Vm's gebruiken in een leslokaal Lab](how-to-use-remote-desktop-linux-student.md)voor meer informatie. 