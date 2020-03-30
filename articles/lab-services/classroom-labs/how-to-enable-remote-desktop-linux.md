---
title: Extern bureaublad voor Linux inschakelen in Azure Lab Services | Microsoft Documenten
description: Meer informatie over het inschakelen van externe desktop voor virtuele Linux-machines in een lab in Azure Lab Services.
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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: cb9a3e2b9ddcd0f74bfa4978f0bc3f4eb0688257
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270860"
---
# <a name="enable-remote-desktop-for-linux-virtual-machines-in-a-lab-in-azure-lab-services"></a>Extern bureaublad voor virtuele Linux-machines inschakelen in een lab in Azure Lab Services
In dit artikel ziet u hoe u de volgende taken uitvoert:

- Extern bureaublad inschakelen voor Linux VM
- Hoe de docent verbinding kan maken met de sjabloon-VM via Extern bureaublad-verbinding (RDP).

## <a name="enable-remote-desktop-for-linux-vm"></a>Extern bureaublad inschakelen voor Linux VM
Tijdens het maken van het lab kunnen docenten verbinding met **externe bureaubladen** inschakelen voor **Linux-afbeeldingen.** De optie **Extern bureaublad-verbinding inschakelen** wordt weergegeven wanneer een Linux-afbeelding is geselecteerd voor de sjabloon. Wanneer deze optie is ingeschakeld, kunnen docenten verbinding maken met vm-sjabloon- en leerling-VM's via RDP (Extern bureaublad). 

![Verbinding met extern bureaublad inschakelen voor een Linux-afbeelding](../media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

Selecteer **Doorgaan met Extern bureaublad**in het bericht item Extern **bureaublad-verbinding** inschakelen . 

![Verbinding met extern bureaublad inschakelen voor een Linux-afbeelding](../media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

> [!IMPORTANT] 
> Als **u een verbinding met extern bureaublad** inschakelt, wordt de **RDP-poort** alleen geopend op Linux-machines. Als RDP al is geïnstalleerd en geconfigureerd op de virtuele machineafbeelding (bijvoorbeeld: Ubuntu Data Science Virtual Machine image), u/studenten verbinding maken met VM's via RDP zonder aanvullende stappen te volgen.
> 
> Als de VM-afbeelding geen RDP heeft geïnstalleerd en geconfigureerd, moet u voor het eerst verbinding maken met de Linux-machine via SSH en RDP- en GUI-pakketten installeren, zodat u/of studenten later verbinding kunnen maken met de Linux-machine via RDP. Zie [Extern bureaublad installeren en configureren om verbinding te maken met een Linux-vm in Azure](../../virtual-machines/linux/use-remote-desktop.md)voor meer informatie. Vervolgens publiceer je de afbeelding zodat studenten RDP kunnen indestudent Linux VM's. 

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen
Momenteel wordt de verbinding met extern bureaublad ondersteund voor de volgende besturingssystemen:

- openSUSE Leap 42.3
- CentOS-gebaseerde 7,5
- Debian 9 "Stretch"
- Ubuntu Server 16.04 LTS

## <a name="connect-to-the-template-vm"></a>Verbinding maken met de sjabloon-VM 
Docenten moeten eerst verbinding maken met de sjabloon-VM met SSH en rdp- en GUI-pakketten installeren. Vervolgens kunnen de docenten RDP gebruiken om verbinding te maken met de sjabloon-VM: 

1. Als u **sjabloon aanpassen** op de werkbalk ziet, selecteert u deze. Selecteer vervolgens **Doorgaan** in het dialoogvenster **Sjabloon aanpassen.** Met deze actie wordt de sjabloon-VM gestart.  

    ![Sjabloon aanpassen](../media/how-to-enable-remote-desktop-linux/customize-template.png)
2. Nadat de sjabloon-VM is gestart, u **Sjabloon Verbinden** selecteren en vervolgens verbinding maken **via SSH** op de werkbalk. 

    ![Verbinding maken met sjabloon via RDP nadat het lab is gemaakt](../media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
3. U ziet het volgende dialoogvenster **Verbinding maken met uw virtuele machine.** Selecteer de knop **Kopiëren** naast het tekstvak om het naar het klembord te kopiëren. Sla de SSH-verbindingstekenreeks op. Gebruik deze verbindingstekenreeks van een SSH-terminal (zoals [Putty)](https://www.putty.org/)om verbinding te maken met de virtuele machine.
 
    ![SSH-verbindingstekenreeks](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)
4. Installeer RDP- en GUI-pakketten zodat u/u/u later verbinding maken met de Linux-machine via RDP. Zie [Extern bureaublad installeren en configureren om verbinding te maken met een Linux-vm in Azure](../../virtual-machines/linux/use-remote-desktop.md)voor meer informatie. Vervolgens publiceer je de afbeelding zodat studenten RDP kunnen indestudent Linux VM's.
5. Nadat deze pakketten zijn geïnstalleerd, u de **sjabloon Verbinding maken** met de werkbalk gebruiken en vervolgens Verbinding maken via **RDP** selecteren om verbinding te maken met de sjabloon-VM via RDP. Sla het RDP-bestand op en gebruik het om verbinding te maken met de sjabloon-VM via RDP. 

## <a name="next-steps"></a>Volgende stappen
Nadat een cursusleider de functie voor verbinding met extern bureaublad heeft ingeschakeld, kunnen studenten verbinding maken met hun VM's via RDP/SSH. Zie [Remote desktop gebruiken voor Linux-vm's in een klaslokaallab voor](how-to-use-remote-desktop-linux-student.md)meer informatie. 