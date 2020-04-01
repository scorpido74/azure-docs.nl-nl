---
title: Toegang tot een leslokaallab in Azure Lab Services | Microsoft Docs
description: In deze zelfstudie hebt u toegang tot virtuele machines in een leslokaallab dat is ingesteld door een docent.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 27d79e28a986e929fb71dd77fc50b3c2cd32618f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77134042"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Zelfstudie: toegang tot een leslokaallab in Azure Lab Services
In deze zelfstudie maakt u als student verbinding met een virtuele machine (VM) in een leslokaallab. 

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Registreer u bij het lab
> * De virtuele machine starten
> * Verbinding maken met de virtuele machine

## <a name="register-to-the-lab"></a>Registreer u bij het lab

1. Navigeer naar de **registratie-URL** die u hebt ontvangen van de docent. U hoeft de registratie-URL niet meer te gebruiken nadat u de registratie hebt voltooid. Gebruik in plaats [https://labs.azure.com](https://labs.azure.com)daarvan de URL: . Internet Explorer 11 wordt nog niet ondersteund. 
1. Meld u aan bij de service met uw schoolaccount om de registratie te voltooien. 

    > [!NOTE]
    > Voor het gebruik van Azure Lab Services is een Microsoft-account vereist. Als u uw niet-Microsoft-account, zoals Yahoo- of Google-accounts, probeert te gebruiken om u aan te melden bij de portal, volgt u instructies om een Microsoft-account te maken dat wordt gekoppeld aan uw niet-Microsoft-account. Volg vervolgens de stappen om het registratieproces te voltooien. 
1. Controleer nadat u zich hebt geregistreerd of u de virtuele machines ziet voor het lab waartoe u toegang hebt. 
1. Wacht tot de virtuele machine klaar is. Let op de VM-tegel op de volgende velden:
    1. Aan de bovenkant van de tegel zie je de **naam van het lab.**
    1. Rechts ziet u het pictogram dat het **besturingssysteem (OS)** van de VM vertegenwoordigt. In dit voorbeeld is het Windows OS. 
    1. Op de voortgangsbalk op de tegel ziet u het aantal uren dat wordt gebruikt voor het aantal aan u toegewezen [quotumuren.](how-to-configure-student-usage.md#set-quotas-for-users) Deze tijd is de extra tijd toegewezen aan u in aanvulling op de geplande tijd voor het lab. 
    1. U ziet pictogrammen/knoppen onder aan de tegel om de vm te starten/stoppen en verbinding te maken met de VM. 
    1. Rechts van de knoppen ziet u de status van de VM. Controleer of u ziet dat de status van de vm is **gestopt.** 

        ![VM in gestopte status](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>De virtuele machine starten
1. **Start** de VM door de eerste knop te selecteren zoals weergegeven in de volgende afbeelding. Dit proces duurt enige tijd.  

    ![De virtuele machine starten](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Controleer of de status van de virtuele machine is ingesteld op **Actief .** 

    ![VM in lopende staat](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Merk op dat het pictogram van de eerste knop is gewijzigd om een **stopbewerking** weer te geven. U deze knop selecteren om de VM te stoppen. 

## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

1. Selecteer de tweede knop zoals weergegeven in de volgende afbeelding om verbinding te **maken met** de VM van het lab. 

    ![Verbinding maken met de virtuele machine](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Een van de volgende stappen uitvoeren: 
    1. Sla het **RDP-bestand** voor **virtuele** Windows-machines op de harde schijf op. Open het RDP-bestand om verbinding te maken met de virtuele machine. Gebruik de **gebruikersnaam** en **het wachtwoord** dat u van uw docent/professor krijgt om u aan te melden bij de machine. 
    3. Voor **virtuele Linux-machines** u **SSH** of **RDP** (als deze is ingeschakeld) gebruiken om verbinding met hen te maken. Zie [Verbinding met extern bureaublad inschakelen voor Linux-machines voor](how-to-enable-remote-desktop-linux.md)meer informatie. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een leslokaallab geopend met behulp van de registratiekoppeling die u van uw docent krijgt.

Als eigenaar van een lab wilt u zien wie zich heeft geregistreerd bij uw lab en wilt u het gebruik van virtuele machines bijhouden. Ga verder met de volgende zelfstudie voor meer informatie over het bijhouden van het gebruik van het lab:

> [!div class="nextstepaction"]
> [Gebruik van een lab bijhouden](tutorial-track-usage.md) 
