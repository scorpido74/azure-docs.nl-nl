---
title: Handleiding voor het beheren van het afsluiten van Windows-gedrag in Azure Lab Services | Microsoft Documenten
description: Stappen om een niet-actieve virtuele Windows-machine automatisch af te sluiten en de windows-uitschakelopdracht te verwijderen.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 39ff4f42457451dfa4aae90b281d6b163c56b4cd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522236"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Handleiding voor het beheren van het afsluiten van Windows-gedrag

Azure Lab Services biedt verschillende kostenbesturingselementen om ervoor te zorgen dat virtuele Windows-machines (VM's) niet onverwacht worden uitgevoerd:
 - [Een schema instellen](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Quota instellen voor gebruikers](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [De functie Automatisch afsluiten wanneer de verbinding is verbroken inschakelen](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Zelfs met deze kostenbesturingselementen zijn er situaties waarin een Windows-VM onverwacht kan blijven draaien; en als gevolg daarvan, aftrekken van het quotum van de student:

- **RDP-venster blijft open**
  
    Wanneer een student verbinding maakt met zijn VM via RDP, kan het per ongeluk het RDP-venster open laten staan.  Zolang het RDP-venster open blijft, wordt de automatische afsluiting bij de **loskoppelensinstelling** nooit van kracht omdat het pas wordt geactiveerd nadat de RDP-sessie is losgekoppeld.

- **Windows-afsluiten, opdracht wordt gebruikt om de VM uit te schakelen**
  
    Een student kan de opdracht voor afsluiten van Windows of andere afsluitmechanismen in Windows gebruiken om de VM uit te schakelen in plaats van de stopknop van Azure Lab Services te [gebruiken.](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)  Wanneer dit gebeurt, vanuit het perspectief van Azure Lab Services, wordt de VM nog steeds gebruikt.
    
Om te voorkomen dat deze situaties zich voordoen, biedt deze handleiding stappen om een inactieve Windows-vm automatisch af te sluiten en de opdracht Windows-afsluiten uit het **menu Start** te verwijderen.  

> [!NOTE]
> Een VM kan ook onverwacht aftrekken van het quotum wanneer de student zijn VM start, maar nooit daadwerkelijk verbinding maakt met rdp.  Deze handleiding gaat momenteel *niet* in op dit scenario.  In plaats daarvan moeten studenten eraan worden herinnerd om onmiddellijk verbinding te maken met hun VM met behulp van RDP nadat ze deze hebben gestart; of ze moeten de VM stoppen.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Automatische RDP-verbinding verbreken en afsluiten voor niet-actieve VM

Windows biedt instellingen **voor lokaal groepsbeleid** die u gebruiken om een tijdslimiet in te stellen om een RDP-sessie automatisch los te koppelen wanneer deze niet actief is.  Een sessie wordt bepaald als niet-actief wanneer er *geen* muis\toetsenbordinvoer is.  Alle langlopende activiteiten waarbij geen muis\toetsenbordinvoer is betrokken, zorgen ervoor dat de VM in een niet-actieve status verkeert.  Dit omvat het uitvoeren van een lange query, streaming video, compileren, enz.  Afhankelijk van de behoeften van uw klas u ervoor kiezen om de niet-actieve tijdslimiet in te stellen, zodat deze lang genoeg is om dit soort activiteiten te verwerken.  U de inactieve tijdslimiet bijvoorbeeld instellen op 1 of meer uren indien nodig.

Hier is de ervaring van de student wanneer u de **inactieve sessielimiet** configureert in combinatie met de [**automatische afsluiting bij de instelling voor het verbreken:**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)
 1. De student maakt verbinding met hun Windows VM via RDP.
 2. Wanneer de student zijn RDP-venster open laat staan en de VM niet actief is voor de **inactieve sessielimiet** die u hebt opgegeven (zoals 5 minuten), ziet de student het volgende dialoogvenster:

    ![Dialoogvenster Inactieve tijdsperiode verlopen](../media/how-to-windows-shutdown/idle-time-expired.png)

1. Als de student *niet* op **OK**klikt, wordt de RDP-sessie na 2 minuten automatisch verbroken.
2. Nadat de RDP-sessie is verbroken, wordt de VM automatisch afgesloten door Azure Lab Services nadat het opgegeven tijdsbestek voor de **automatische afsluiting bij de loskoppelen-instelling** is bereikt.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Inactieve sessietijdslimiet RDP instellen op de sjabloon-VM

Als u de inactieve tijdslimiet voor RDP-sessies wilt instellen, u verbinding maken met de sjabloon-VM en het onderstaande PowerShell-script uitvoeren.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 90

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
U er ook voor kiezen om deze handmatige stappen te volgen met de sjabloon-VM:

1. Druk op de Windows-toets, typ **gpedit**en selecteer **Groepsbeleid bewerken (configuratiescherm).**

1. Ga naar **ComputerConfiguratie >-beheersjablonen > Windows-onderdelen > Extern bureaublad-services > extern bureaublad-sessiehost > sessietijdslimieten**.  

    ![Editor voor lokaal groepsbeleid](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Klik met de rechtermuisknop op **Tijdslimiet instellen voor actieve, maar niet-actieve sessies met Extern bureaublad-services**en klik op **Bewerken**.

1. Voer de volgende instellingen in en klik op **OK:**
   1. Selecteer **Ingeschakeld**.
   1. Geef **onder Opties**de limiet voor **inactieve sessies op**.

    ![Inactieve sessielimiet](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Ten slotte moet u stappen volgen in het artikel: Automatische uitschakeling van [VM's inschakelen bij het verbreken](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)van de **verbinding.**

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Opdracht Windows afsluiten verwijderen uit menu Start

Met de instellingen **voor lokaal groepsbeleid** van Windows u ook de opdracht Afsluiten verwijderen uit het menu **Start.**

Als u de opdracht afsluiten wilt verwijderen, u verbinding maken met de sjabloon-VM en het onderstaande PowerShell-script uitvoeren.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

U er ook voor kiezen om deze handmatige stappen te volgen met de sjabloon-VM:

1. Druk op de Windows-toets, typ **gpedit**en selecteer **Groepsbeleid bewerken (configuratiescherm).**

1. Ga naar **computerconfiguratie > beheersjablonen > startmenu en taakbalk**.  

    ![Editor voor lokaal groepsbeleid](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Klik met de rechtermuisknop op **De opdrachten Afsluiten, Opnieuw starten, Slaapen en Sluimeren**verwijderen en voorkomen en klik op **Bewerken**.

1. Selecteer de **instelling Ingeschakeld** en klik op **OK:**
 
   ![Instelling afsluiten](../media/how-to-windows-shutdown/edit-shutdown.png)

1. De opdracht Afsluiten wordt niet meer weergegeven onder het menu **Start van** Windows. alleen de opdracht **Verbinding verbreken** wordt weergegeven.

    ![Afsluiten, opdracht](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over het voorbereiden van een Windows-sjabloonVM: [handleiding voor het instellen van een Windows-sjabloonmachine in Azure Lab Services](how-to-prepare-windows-template.md)