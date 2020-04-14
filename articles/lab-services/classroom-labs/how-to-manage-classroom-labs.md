---
title: Klaslabs beheren in Azure Lab Services | Microsoft Documenten
description: Leer hoe u een klaslokaallab maakt en configureert, alle klaslokalen bekijkt, de registratiekoppeling deelt met een labgebruiker of een lab verwijdert.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 46c53c99c12ade986ab913bf013b652a931a4d22
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257739"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Klaslabs beheren in Azure Lab Services 
In dit artikel wordt beschreven hoe u een klaslokaallab maakt en verwijdert. Het laat je ook zien hoe je alle klaslokalen in een labaccount bekijken. 

## <a name="prerequisites"></a>Vereisten
Als u een leslokaallab in een labaccount instelt, moet u lid zijn van de rol **Labmaker** in het labaccount. Het account dat u hebt gebruikt voor het maken van een labaccount wordt automatisch toegevoegd aan deze rol. Een labeigenaar kan andere gebruikers toevoegen aan de rol Labmaker met behulp van de stappen in het volgende artikel: [Een gebruiker toevoegen aan de rol Labmaker](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Een leslokaallab maken

1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com). Internet Explorer 11 wordt nog niet ondersteund. 
2. Selecteer **Aanmelden** en voer uw referenties in. Selecteer of voer een **gebruikersnaam** in die lid is van de **labcreatorrol** in het labaccount en voer een wachtwoord in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 
3. Selecteer **Nieuw lab**. 
    
    ![Een leslokaallab maken](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. Voer in het venster **Nieuw lab** de volgende acties uit: 
    1. Geef een **naam** voor uw lab op. 
    2. Selecteer de **grootte van de virtuele machines die** u nodig hebt voor de klasse. Zie de sectie [VM-formaten voor](#vm-sizes) de beschikbare lijst met formaten. 
    3. Selecteer de **virtuele machineafbeelding** die u wilt gebruiken voor het klaslokaallab. Als u een Linux-afbeelding selecteert, ziet u een optie om verbinding met extern bureaublad hiervoor in te schakelen. Zie Externe [bureaubladverbinding inschakelen voor Linux voor](how-to-enable-remote-desktop-linux.md)meer informatie.

        Als u zich hebt aangemeld met referenties van de eigenaar van het labaccount, ziet u een optie om meer afbeeldingen voor het lab in te schakelen. Zie [Afbeeldingen inschakelen op het moment van het maken van het lab](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)voor meer informatie.
    4. Bekijk de **totale prijs per uur** die op de pagina wordt weergegeven. 
    6. Selecteer **Opslaan**.

        ![Nieuw laboratoriumvenster](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > U ziet een optie om een locatie voor uw lab te selecteren als het lab-account is geconfigureerd zodat de maker van het lab de optie [lablocatie kan kiezen.](allow-lab-creator-pick-lab-location.md) 
4. Geef op de pagina **Standaardreferenties** voor virtuele machine op voor alle VM's in het lab.
    1. Geef de **naam van de gebruiker** op voor alle virtuele machines in het lab.
    2. Geef het **wachtwoord** voor de gebruiker op. 

        > [!IMPORTANT]
        > Noteer de gebruikersnaam en het wachtwoord. Deze worden niet opnieuw weergegeven.
    3. **Schakel hetzelfde wachtwoord voor alle virtuele machines** optie als u wilt dat studenten hun eigen wachtwoorden in te stellen. Deze stap is **optioneel**. 

        Een docent kan ervoor kiezen om hetzelfde wachtwoord te gebruiken voor alle VM's in het lab, of studenten toestaan om wachtwoorden in te stellen voor hun VM's. Standaard is deze instelling ingeschakeld voor alle Windows- en Linux-afbeeldingen, behalve voor Ubuntu. Wanneer u **Ubuntu** VM selecteert, is deze instelling uitgeschakeld, zodat de studenten wordt gevraagd een wachtwoord in te stellen wanneer ze zich voor de eerste keer aanmelden.  

        ![Nieuw laboratoriumvenster](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Selecteer **vervolgens Volgende** op de pagina **Virtuele machinereferenties.** 
5. Ga op de pagina **Lab-beleid** de volgende stappen uit:
    1. Voer het aantal toegewezen uren voor elke gebruiker **(quotum voor elke gebruiker)** in buiten de geplande tijd voor het lab. 
    2. Geef voor de optie **Automatisch afsluiten van virtuele machines** op of u wilt dat de vm automatisch wordt afgesloten wanneer de verbinding tussen de gebruiker en de gebruiker wordt verbroken. U ook opgeven hoe lang de VM moet wachten tot de gebruiker opnieuw verbinding maakt voordat deze automatisch wordt afgesloten.. Zie [Automatisch afsluiten van VM's inschakelen bij het verbreken van de verbinding](how-to-enable-shutdown-disconnect.md)voor meer informatie .
    3. Selecteer vervolgens **Voltooien**. 

        ![Quotum voor elke gebruiker](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. U ziet het volgende scherm met de status van het maken van de sjabloon-vm. Het maken van de sjabloon in het lab duurt maximaal 20 minuten. 

    ![Status van het maken van de sjabloon-vm](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Voer op de pagina **Sjabloon** de volgende stappen uit: de volgende stappen zijn **optioneel** voor de zelfstudie.

    2. De sjabloon-VM verbinden door **Verbinding maken** te selecteren. Als het een VM voor Linux-sjablonen is, kiest u of u verbinding wilt maken met SSH of RDP (als RDP is ingeschakeld).
    1. Selecteer **Wachtwoord opnieuw instellen** om het wachtwoord voor de vm opnieuw in te stellen. 
    1. Software installeren en configureren op uw sjabloon-VM. 
    1. **Stop** de virtuele machine.  
    1. Een **Beschrijving** voor de sjabloon invoeren
10. Selecteer op de pagina **Sjabloon** de optie **Publiceren** op de werkbalk. 

    ![Knop Sjabloon publiceren](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Zodra u de sjabloon hebt gepubliceerd, kan dit niet ongedaan worden gemaakt. 
8. Voer **op** de pagina Sjabloon publiceren het aantal virtuele machines in dat u in het lab wilt maken en selecteer **Vervolgens Publiceren**. 

    ![Sjabloon publiceren - aantal VM's](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. U ziet de **status van het publiceren van** de sjabloon op de pagina. Dit proces duurt maximaal een uur. 

    ![Sjabloon publiceren - voortgang](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Ga naar de **poolpagina virtuele machines** door virtuele machines te selecteren in het linkermenu of door de tegel Virtuele machines te selecteren. Controleer of u virtuele machines ziet met de status **Niet-toegewezen**. Deze virtuele machines zijn nog niet toegewezen aan studenten. Deze horen de status **Gestopt** te hebben. Op deze pagina kunt u een student-VM starten, verbinding maken met de virtuele machine, de virtuele machine stoppen en de virtuele machine verwijderen. U kunt de virtuele machines zelf starten vanaf deze pagina of ze laten starten door de studenten. 

    ![Virtuele machines met de status Gestopt](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    U doet de volgende taken op deze pagina (doe deze stappen niet voor de zelfstudie. Deze stappen zijn alleen voor uw informatie.): 
    
    1. Als u de labcapaciteit (aantal VM's in het lab) wilt wijzigen, selecteert u **Labcapaciteit** op de werkbalk.
    2. Als u alle VM's tegelijk wilt starten, selecteert u **Alles starten** op de werkbalk. 
    3. Als u een specifieke virtuele machine wilt starten, selecteert u de pijl-omlaag in de **status**en selecteert u **Start**. U een vm ook starten door een vm in de eerste kolom te selecteren en vervolgens **Start** op de werkbalk te selecteren.                

### <a name="vm-sizes"></a>Formaten van virtuele machines  

| Grootte | Kernen | RAM | Beschrijving | 
| ---- | ----- | --- | ----------- | 
| Klein | 2 | 3,5 GB | Deze grootte is het meest geschikt voor opdrachtregel, het openen van webbrowser, low traffic webservers, kleine tot middelgrote databases. |
| Middelgroot | 4 | 7 GB | Deze grootte is het meest geschikt voor relationele databases, in-memory caching en analytics | 
| Medium (Geneste virtualisatie) | 4 | 16 GB | Deze grootte is het meest geschikt voor relationele databases, in-memory caching en analytics. Deze grootte ondersteunt ook geneste virtualisatie. <p>Deze grootte kan worden gebruikt in scenario's waarin elke student meerdere VM's nodig heeft. Docenten kunnen geneste virtualisatie gebruiken om een paar kleine geneste virtuele machines in de virtuele machine in te stellen. </p> |
| Groot | 8 | 32 GB | Dit formaat is het meest geschikt voor toepassingen die snellere CPU's nodig hebben, betere lokale schijfprestaties, grote databases, grote geheugencaches. Deze grootte ondersteunt ook geneste virtualisatie |  
| Kleine GPU (visualisatie) | 6 | 56 GB | Dit formaat is het meest geschikt voor externe visualisatie, streaming, gaming, codering met behulp van frameworks zoals OpenGL en DirectX. | 
| Kleine GPU (Berekenen) | 6 | 56 GB | Deze grootte is het meest geschikt voor rekenintensieve en netwerkintensieve toepassingen zoals kunstmatige intelligentie en deep learning-toepassingen. | 
| Middelgrote GPU (visualisatie) | 12 | 112 GB | Dit formaat is het meest geschikt voor externe visualisatie, streaming, gaming, codering met behulp van frameworks zoals OpenGL en DirectX. | 

> [!NOTE]
> Azure Lab Services installeert en configureert automatisch de benodigde GPU-stuurprogramma's voor u wanneer u een lab maakt met GPU-afbeeldingen.  

## <a name="view-all-classroom-labs"></a>Alle klaslokalen bekijken
1. Navigeer naar [azure lab services-portal](https://labs.azure.com).
2. Selecteer **Aanmelden**. Selecteer of voer een **gebruikersnaam** in die lid is van de **labcreatorrol** in het labaccount en voer een wachtwoord in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 
3. Controleer of u alle labs in het geselecteerde labaccount ziet. Op de tegel van het lab ziet u het aantal virtuele machines in het lab en het quotum voor elke gebruiker (buiten de geplande tijd).

    ![Alle laboratoria](../media/how-to-manage-classroom-labs/all-labs.png)
3. Gebruik de vervolgkeuzelijst bovenaan om een ander labaccount te selecteren. U ziet labs in het geselecteerde labaccount. 

## <a name="delete-a-classroom-lab"></a>Een klaslokaallab verwijderen
1. Selecteer op de tegel voor het lab drie puntjes (...) in de hoek en selecteer **Verwijderen**. 

    ![De knop Verwijderen](../media/how-to-manage-classroom-labs/delete-button.png)
3. Selecteer **Verwijderen** om door te gaan met verwijderen in het dialoogvenster **Lab verwijderen.** 

## <a name="switch-to-another-classroom-lab"></a>Overschakelen naar een ander klaslokaallab
Als u vanaf de huidige basis wilt overschakelen naar een ander laboratorium in de klas, selecteert u de vervolgkeuzelijst met labs in het labaccount bovenaan.

![Selecteer het lab in de vervolgkeuzelijst bovenaan](../media/how-to-manage-classroom-labs/switch-lab.png)

U ook een nieuw lab maken met behulp van het **nieuwe lab** in deze vervolgkeuzelijst. 

> [!NOTE]
> U ook de Az.LabServices PowerShell-module (preview) gebruiken om labs te beheren. Zie voor meer informatie de [startpagina van Az.LabServices op GitHub.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)

Als u wilt overschakelen naar een ander labaccount, selecteert u de vervolgkeuzelijst naast het labaccount en selecteert u het andere labaccount. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als labgebruiker toegang tot klaslokalen](how-to-use-classroom-lab.md)

