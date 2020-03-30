---
title: Extern bureaublad voor Linux gebruiken in Azure Lab Services | Microsoft Documenten
description: Meer informatie over het gebruik van externe desktop voor virtuele Linux-machines in een lab in Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73585079"
---
# <a name="use-remote-desktop-for-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Externe desktop voor virtuele Linux-machines gebruiken in een klaslokaallab van Azure Lab Services
In dit artikel ziet u hoe studenten verbinding kunnen maken met een Virtuele Linux-machine (VM) in een lab met behulp van RDP/SSH. 

Een cursusleider moet de functie voor verbinding met extern bureaublad inschakelen voordat leerlingen verbinding kunnen maken met de VM van het classroom lab. Zie [Extern bureaublad inschakelen voor virtuele Linux-machines](how-to-enable-remote-desktop-linux.md)voor instructies over hoe een cursusleider de functie voor verbinding met extern bureaublad kan inschakelen.

> [!IMPORTANT] 
> Als **u een verbinding met extern bureaublad** inschakelt, wordt de **RDP-poort** alleen geopend op Linux-machines. Een instructeur kan voor het eerst verbinding maken met de Linux-machine via SSH en RDP- en GUI-pakketten installeren, zodat u later verbinding maken met de Linux-machine via RDP. 

## <a name="connect-to-the-student-vm"></a>Verbinding maken met de student-VM
Studenten kunnen RDP in hun Linux VM's na het lab eigenaar (leraar / professor) **publiceert** de sjabloon VM met RDP en GUI pakketten geïnstalleerd op de machine. Dit zijn de stappen: 

1. Wanneer een student zich rechtstreeks aanmeldt`https://labs.azure.com`bij de Portal labs`https://labs.azure.com/register/<registrationCode>`() of via een registratielink ( ), wordt een tegel voor elk lab waartoe de student toegang heeft weergegeven. 
2. Schakel op de tegel de knop in om de vm te starten als deze in de gestopte status staat. 
3. Selecteer **Verbinden**. U ziet twee opties om verbinding te maken met de VM: **SSH** en **Extern bureaublad.**

    ![Student VM - verbindingsopties](../media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

## <a name="connect-using-ssh-or-rdp"></a>Verbinding maken met SSH of RDP
Als u de **optie SSH** selecteert, ziet u het volgende dialoogvenster **Verbinding maken met uw virtuele machine:**  

![SSH-verbindingstekenreeks](../media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

Selecteer de knop **Kopiëren** naast het tekstvak om het naar het klembord te kopiëren. Sla de SSH-verbindingstekenreeks op. Gebruik deze verbindingstekenreeks van een SSH-terminal (zoals [Putty)](https://www.putty.org/)om verbinding te maken met de virtuele machine.

Als u de **RDP-optie** selecteert, wordt een RDP-bestand gedownload naar uw machine. Sla het op en open het om verbinding te maken met de machine. 

## <a name="next-steps"></a>Volgende stappen
Zie [Extern bureaublad inschakelen voor Virtuele Linux-machines voor](how-to-enable-remote-desktop-linux.md)meer informatie over het inschakelen van de functie voor verbinding met extern bureaublad voor Linux-vm's in een klaslokaal. 

