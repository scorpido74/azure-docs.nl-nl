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
ms.topic: how-to
ms.custom: mvc
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: db1e60ccd7e05cb8b6923d6b2ec008cdfb76eaa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501926"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Toegang tot een leslokaallab in Azure Lab Services
In dit artikel wordt beschreven hoe u zich registreren bij een klaslokaallab, alle labs bekijken die u openen, een VM in het lab starten/stoppen en verbinding maken met de VM. 

## <a name="register-to-the-lab"></a>Registreer u bij het lab

1. Navigeer naar de **registratie-URL** die u hebt ontvangen van de docent. U hoeft de registratie-URL niet meer te gebruiken nadat u de registratie hebt voltooid. Gebruik in plaats [https://labs.azure.com](https://labs.azure.com)daarvan de URL: . Internet Explorer 11 wordt nog niet ondersteund. 
1. Meld u aan bij de service met uw schoolaccount om de registratie te voltooien. 

    > [!NOTE]
    > Voor het gebruik van Azure Lab Services is een Microsoft-account vereist. Als u uw niet-Microsoft-account, zoals Yahoo- of Google-accounts, probeert te gebruiken om u aan te melden bij de portal, volgt u instructies om een Microsoft-account te maken dat wordt gekoppeld aan uw niet-Microsoft-account. Volg vervolgens de stappen om het registratieproces te voltooien. 
1. Controleer nadat u zich hebt geregistreerd of u de virtuele machines ziet voor het lab waartoe u toegang hebt. 
1. Wacht tot de virtuele machine klaar is. Let op de VM-tegel op de volgende velden:
    1. Aan de bovenkant van de tegel zie je de **naam van het lab.**
    1. Rechts ziet u het pictogram dat het **besturingssysteem (OS)** van de VM vertegenwoordigt. In dit voorbeeld is het Windows OS. 
    1. U ziet pictogrammen/knoppen onder aan de tegel om de vm te starten/stoppen en verbinding te maken met de VM. 
    1. Rechts van de knoppen ziet u de status van de VM. Controleer of u ziet dat de status van de vm is **gestopt.**

        ![VM in gestopte status](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>De VM starten of stoppen
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
    1. Als u een **Mac** gebruikt om verbinding te maken met de lab-vm, volgt u de instructies in de volgende sectie. 

## <a name="progress-bar"></a>Voortgangsbalk 
Op de voortgangsbalk op de tegel ziet u het aantal uren dat wordt gebruikt voor het aantal aan u toegewezen [quotumuren.](how-to-configure-student-usage.md#set-quotas-for-users) Deze tijd is de extra tijd toegewezen aan u in aanvulling op de geplande tijd voor het lab. De kleur van de voortgangsbalk en de tekst onder de voortgangsbalk varieert per de volgende scenario's:

- Als een klasse aan de gang is (binnen het schema van de klasse), wordt de voortgangsbalk grijs weergegeven om aan te geven dat de quota-uren niet worden gebruikt. 

    ![Voortgangsbalk in grijze kleur](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Als een quotum niet is toegewezen (nul uur), wordt de tekst beschikbaar tijdens de klassen alleen weergegeven in plaats van de **voortgangsbalk.** 
    
    ![Status wanneer er geen quotum is ingesteld](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Als het **quotum op**is, is de kleur van de voortgangsbalk **rood**. 

    ![Voortgangsbalk in rode kleur](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- De kleur van de voortgangsbalk is **blauw** wanneer deze buiten de geplande tijd voor het lab valt en een deel van de quotumtijd is gebruikt. 

    ![Voortgangsbalk in blauwe kleur](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Alle leslokaallabs weergeven
Als u zich voor de labs registreert, kunt u alle leslokaallabs weergeven door de volgende stappen uit te voeren: 

1. Navigeer [https://labs.azure.com](https://labs.azure.com)naar . Internet Explorer 11 wordt nog niet ondersteund. 
2. Meld u aan bij de service met het gebruikersaccount waarmee u zich hebt geregistreerd voor het lab. 
3. Controleer of u alle labs ziet waarvoor u toegangsrechten hebt. 

    ![Alle labs weergeven](../media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labaccounts maken en beheren als beheerder](how-to-manage-lab-accounts.md)
- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
 