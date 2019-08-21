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
ms.date: 08/19/2019
ms.author: spelluru
ms.openlocfilehash: 769cb2c6a3ae0e5b94ffd65425634ac9dd0221d0
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639998"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Zelfstudie: Toegang tot een leslokaallab in Azure Lab Services
In deze zelfstudie maakt u als student verbinding met een virtuele machine (VM) in een leslokaallab. 

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Registreren bij het lab
> * De virtuele machine starten
> * Verbinding maken met de virtuele machine

## <a name="register-to-the-lab"></a>Registreren bij het lab

1. Navigeer naar de **registratie-URL** die u hebt ontvangen van de docent. U hoeft de registratie-URL niet meer te gebruiken nadat u de registratie hebt voltooid. In plaats daarvan gebruikt u deze URL: [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 wordt nog niet ondersteund. 
1. Meld u aan bij de service met uw schoolaccount om de registratie te voltooien. 
2. Controleer nadat u zich hebt geregistreerd of u de virtuele machines ziet voor het lab waartoe u toegang hebt. 
3. Wacht tot de virtuele machine klaar is. Let op de volgende velden op de VM-tegel:
    1. Boven aan de tegel ziet u de **naam van het lab**.
    1. Aan de rechter kant ziet u het pictogram voor het **besturings systeem (OS)** van de virtuele machine. In dit voor beeld is het Windows-besturings systeem. 
    1. De voortgangs balk op de tegel toont het aantal uren dat is gebruikt voor het aantal aan u toegewezen [quota-uren](how-to-configure-student-usage.md#set-quotas-for-users) . Deze tijd is de extra tijd die aan u is toegewezen, naast de geplande tijd voor het lab. 
    1. U ziet pictogrammen/knoppen onder aan de tegel om de virtuele machine te starten/stoppen en verbinding te maken met de virtuele machine. 
    1. Rechts van de knoppen ziet u de status van de virtuele machine. Controleer of de status van de virtuele machine is **gestopt**. 

        ![VM is gestopt](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>De virtuele machine starten
1. **Start** de virtuele machine door de eerste knop te selecteren, zoals wordt weer gegeven in de volgende afbeelding. Dit proces duurt enige tijd.  

    ![De virtuele machine starten](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Controleer of de status van de virtuele machine is ingesteld op **actief**. 

    ![VM in uitvoerings status](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    U ziet dat het pictogram van de eerste knop is gewijzigd om een **Stop** bewerking weer te geven. U kunt deze knop selecteren om de virtuele machine te stoppen. 

## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

1. Selecteer de tweede knop zoals weer gegeven in de volgende afbeelding om **verbinding te maken** met de VM van het lab. 

    ![Verbinding maken met de virtuele machine](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Voer een van de volgende stappen uit: 
    1. Sla het **RDP** -bestand op de harde schijf op voor virtuele **Windows** -machines. Open het RDP-bestand om verbinding te maken met de virtuele machine. Gebruik de **gebruikers naam** en het **wacht woord** die u van uw docent/docent krijgt om u aan te melden bij de computer. 
    3. Voor virtuele **Linux** -machines kunt u **SSH** of **RDP** gebruiken (als deze is ingeschakeld) om er verbinding mee te maken. Zie [verbinding met extern bureau blad inschakelen voor Linux-machines](how-to-enable-remote-desktop-linux.md)voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een leslokaallab geopend met behulp van de registratiekoppeling die u van uw docent krijgt.

Als eigenaar van een lab wilt u zien wie zich heeft geregistreerd bij uw lab en wilt u het gebruik van virtuele machines bijhouden. Ga verder met de volgende zelfstudie voor meer informatie over het bijhouden van het gebruik van het lab:

> [!div class="nextstepaction"]
> [Gebruik van een lab bijhouden](tutorial-track-usage.md) 
