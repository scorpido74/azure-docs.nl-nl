---
title: Wachtwoorden instellen voor VM's in Azure Lab Services | Microsoft Documenten
description: Meer informatie over het instellen en opnieuw instellen van wachtwoorden voor virtuele machines (VM's) in klaslabs van Azure Lab Services.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 0ff464936025a20cb6925adc7ef6eb44c2fe1f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933805"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>VM-pool instellen en beheren 
In dit artikel ziet u hoe u de volgende taken uitvoert:

- Verhoog het aantal virtuele machines (VM's) in het lab
- Alle VM's of geselecteerde VM's starten 
- VM's opnieuw instellen

## <a name="update-the-lab-capacity"></a>De labcapaciteit bijwerken
Als u de labcapaciteit (aantal virtuele machines in een lab) wilt vergroten of verkleinen, gaat u de volgende stappen uitvoeren:

1. Selecteer op de pagina **Virtuele machinegroep** de optie ** &lt;Labcapaciteit: getalmachines&gt; **.
2. Voer het nieuwe **aantal VM's** in dat u in het lab wilt invoeren. Dit aantal moet groter zijn dan of gelijk zijn aan het aantal gebruikers dat in het lab is geregistreerd. 
3. Selecteer vervolgens **Opslaan**. 

    ![Knop Alles starten](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Als u de capaciteit hebt vergroot, u zien dat de VM's of VM's worden gemaakt. Als u de nieuwe virtuele machine niet in de lijst ziet, vernieuwt u de pagina. 

    ![VM wordt gemaakt](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>VM's starten

### <a name="start-ot-stop-all-vms"></a>Start ot stop alle VM's
1. Overschakelen naar de pagina **Virtuele machinepool.** 
2. Selecteer **Alles starten** op de werkbalk. 

    ![Knop Alles starten](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Nadat alle VM's zijn gestart, u alle VM's stoppen door de knop **Alles stoppen** op de werkbalk te selecteren. 

    ![Knop Alles stoppen](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Geselecteerde VM's starten
Er zijn twee manieren om geselecteerde VM's (een of meer) te starten. De eerste manier is om de VM of VM's in de lijst te selecteren en vervolgens **Start** op de werkbalk te selecteren. 

De tweede manier is om een of meer VM's in de lijst te selecteren en de knop in de **kolom Status** in te schakelen. 

![Geselecteerde VM's starten](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

U ook een of meer VM's stoppen door de knop in de **kolom Status** te schakelen of **Stoppen** op de werkbalk te selecteren. 

> [!NOTE]
> Wanneer een docent een student-vm inschakelt, wordt het quotum voor de student niet beïnvloed. Quota voor een gebruiker geeft het aantal laburen op dat beschikbaar is voor de gebruiker buiten de geplande lestijd. Zie [Quota voor gebruikers instellen voor](how-to-configure-student-usage.md?#set-quotas-for-users)meer informatie over quota.

## <a name="reset-vms"></a>VM's opnieuw instellen
Als u een of meer VM's opnieuw wilt instellen, selecteert u deze in de lijst en selecteert u **Opnieuw instellen** op de werkbalk. 

![Geselecteerde VM's opnieuw instellen](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Selecteer Opnieuw instellen in het dialoogvenster **Virtuele machine(s)** **opnieuw instellen**. 

![Dialoogvenster VM opnieuw instellen](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Wachtwoord instellen voor VM's
Een labeigenaar (docent) kan het wachtwoord voor VM's instellen/opnieuw instellen op het moment dat het lab wordt gemaakt (wizard voor het maken van het lab) of nadat het lab op de **sjabloonpagina is** gemaakt. 

### <a name="set-password-at-the-time-of-lab-creation"></a>Wachtwoord instellen op het moment van het maken van het lab
Een labeigenaar (docent) kan een wachtwoord instellen voor VM's in het lab op de pagina **Virtuele machinereferenties** van de wizard Voor het maken van het lab.

![Nieuw laboratoriumvenster](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Door het zelfde **wachtwoord gebruiken voor alle virtuele machines** op deze pagina in te schakelen/uit te schakelen, kan een docent ervoor kiezen om hetzelfde wachtwoord te gebruiken voor alle VM's in het lab of studenten in staat te stellen wachtwoorden in te stellen voor hun VM's. Standaard is deze instelling ingeschakeld voor alle windows- en Linux-besturingssysteemafbeeldingen, behalve Ubuntu. Wanneer deze instelling is uitgeschakeld, wordt studenten gevraagd een wachtwoord in te stellen wanneer ze voor de eerste keer verbinding proberen te maken met de vm. 

### <a name="reset-password-later"></a>Wachtwoord later opnieuw instellen

1. Selecteer op de **sjabloonpagina** van het lab het **wachtwoord opnieuw instellen** op de werkbalk. 
1. Voer in het dialoogvenster **Wachtwoord opnieuw instellen** een wachtwoord in en selecteer Wachtwoord opnieuw **instellen**.
    
    ![Dialoogvenster Wachtwoord instellen](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Verbinding maken met vm's van studenten
De maker van het lab (instructeur/professor) kan verbinding maken met een student-VM als aan de volgende voorwaarden is voldaan: 

- De optie **Hetzelfde wachtwoord gebruiken voor alle virtuele machines** is geselecteerd bij het maken van het lab
- De VM wordt uitgevoerd 

 Als u verbinding wilt maken met de student-VM, houdt u de muis op de VM in de lijst en selecteert u de computerknop.  

![Knop Verbinding maken met de VM-student](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Wanneer de professor de VM start en ermee verbinding maakt, blijft het studentenquotum onaangetast. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Lijst met virtuele machines exporteren naar een CSV-bestand

1. Overschakelen naar het tabblad **Virtuele machinepool.**
2. Selecteer **...** (ellips) op de werkbalk en selecteer **CSV exporteren**. 

    ![Lijst van virtuele machines exporteren](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel: [Studentengebruik configureren](how-to-configure-student-usage.md)voor meer informatie over andere opties voor het gebruik van studenten die u (als eigenaar van een lab) configureren.

Zie Wachtwoord instellen of opnieuw instellen voor [virtuele machines in klaslabs (studenten)](how-to-set-virtual-machine-passwords-student.md)voor meer informatie over hoe studenten wachtwoorden voor hun VM's kunnen resetten.