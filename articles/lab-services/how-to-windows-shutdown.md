---
title: Hand leiding voor het beheren van het afsluit gedrag van Windows in Azure Lab Services | Microsoft Docs
description: Stappen voor het automatisch afsluiten van een niet-actieve Windows-virtuele machine en het verwijderen van de Windows-opdracht shutdown.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e17f6e79c3d18d82dd206954dcfb0e06b02b4d53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445165"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Hand leiding voor het beheren van het afsluit gedrag van Windows

Azure Lab Services biedt verschillende kosten controles om ervoor te zorgen dat virtuele Windows-machines (Vm's) niet onverwacht worden uitgevoerd:
 - [Een planning instellen](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Quota instellen voor gebruikers](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [De functie Automatisch afsluiten wanneer de verbinding is verbroken inschakelen](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

Zelfs met deze kosten controles zijn er situaties waarin een Windows-VM onverwacht kan worden uitgevoerd. en als gevolg hiervan wordt het quotum van de student afgetrokken:

- **Het RDP-venster is geopend**
  
    Wanneer een student verbinding maakt met hun VM met behulp van RDP, kunnen ze per ongeluk het RDP-venster openen.  Zolang het RDP-venster geopend blijft, wordt de instelling **automatisch afsluiten bij verbinding verbreken nooit van** kracht, omdat deze alleen wordt geactiveerd nadat de verbinding met de RDP-sessie is verbroken.

- **Windows shutdown opdracht wordt gebruikt om de virtuele machine uit te scha kelen**
  
    Een student kan Windows shutdown opdracht of andere afsluit mechanismen van Windows gebruiken om de virtuele machine uit te scha kelen in plaats van [Azure Lab Services ' Stop knop '](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  Als dit gebeurt, wordt de VM in het perspectief van Azure Lab Services nog steeds gebruikt.
    
Deze hand leiding bevat stappen voor het automatisch afsluiten van een niet-actieve Windows-VM en het verwijderen van de Windows-opdracht shutdown uit het **Start** menu om te voor komen dat deze situaties zich voordoen.  

> [!NOTE]
> Een virtuele machine kan ook onverwacht van het quotum aftrekken wanneer de student hun virtuele machine start, maar er geen verbinding mee maakt met behulp van RDP.  Deze hand leiding biedt momenteel *geen* ondersteuning voor dit scenario.  In plaats daarvan moeten studenten worden aangeraden om direct verbinding te maken met hun VM met behulp van RDP nadat ze deze hebben gestart. of ze moeten de virtuele machine stoppen.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Automatisch RDP-verbinding verbreken en afsluiten voor niet-actieve VM'S

Windows biedt **lokale groepsbeleid** -instellingen die u kunt gebruiken om een tijds limiet in te stellen voor het automatisch verbreken van een RDP-sessie wanneer deze inactief wordt.  Een sessie wordt als niet-actief beschouwd als er *geen* mouse\keyboard-invoer is.  Langlopende activiteiten waarbij geen mouse\keyboard-invoer is betrokken, hebben als gevolg dat de virtuele machine niet actief is.  Dit omvat het uitvoeren van een lange query, het streamen van video, het compileren, enzovoort.  Afhankelijk van de behoeften van uw klasse kunt u ervoor kiezen om de limiet voor niet-actieve tijd in te stellen, zodat deze lang genoeg is om dit soort activiteiten af te handelen.  U kunt bijvoorbeeld de limiet voor niet-actieve tijd instellen op 1 of meer uur als dat nodig is.

Dit is de ervaring van de student bij het configureren van de **limiet voor niet-actieve sessies** in combi natie met de instelling [**automatisch afsluiten bij het verbreken van de verbinding**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) :
 1. De student maakt verbinding met hun Windows-VM met behulp van RDP.
 2. Wanneer de student hun RDP-venster geopend laat en de virtuele machine niet actief is voor de **limiet voor niet-actieve sessies** die u hebt opgegeven (bijvoorbeeld 5 minuten), ziet de student het volgende dialoog venster:

    ![Dialoog venster niet-actieve tijds limiet verlopen](./media/how-to-windows-shutdown/idle-time-expired.png)

1. Als de student *niet* op **OK**klikt, wordt de RDP-sessie na twee minuten automatisch verbroken.
2. Nadat de verbinding met de RDP-sessie is verbroken, wordt de VM automatisch afgesloten door Azure Lab Services nadat het opgegeven tijds bestek is bereikt voor het **automatisch afsluiten bij het verbreken van de verbinding** .

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Tijds limiet voor inactieve sessie van RDP instellen op de sjabloon-VM

Als u de time-outlimiet van de RDP-sessie wilt instellen, kunt u verbinding maken met de VM van de sjabloon en het onderstaande Power shell-script uitvoeren.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
U kunt er ook voor kiezen om deze hand matige stappen te volgen met behulp van de sjabloon-VM:

1. Druk op de Windows-toets, typ **gpedit**en selecteer vervolgens **groeps beleid bewerken (configuratie scherm)**.

1. Ga naar **computer configuratie > Beheersjablonen > Windows-onderdelen > Extern bureaublad-services > extern bureaublad sessie Host > sessie tijds limieten**.  

    ![Editor voor lokaal groepsbeleid](./media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Klik met de rechter muisknop op **tijds limiet instellen voor actief, maar niet-actieve Extern bureaublad-services sessies**en klik op **bewerken**.

1. Voer de volgende instellingen in en klik op **OK**:
   1. Selecteer **Ingeschakeld**.
   1. Geef onder **Opties**de **limiet voor niet-actieve sessies**op.

    ![Limiet voor niet-actieve sessies](./media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Ten slotte moet u de stappen in het artikel How-to [gebruiken](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)om dit gedrag samen te voegen met de instelling **voor automatisch afsluiten** bij het verbreken van de verbinding.

> [!WARNING]
> Nadat u deze instelling hebt geconfigureerd met behulp van Power shell om de register instelling rechtstreeks of hand matig te wijzigen met behulp van de groepsbeleid editor, moet u eerst de virtuele machine opnieuw opstarten om de instellingen van kracht te laten worden.  Als u de instelling in het REGI ster configureert, wordt de groepsbeleid editor niet altijd vernieuwd om wijzigingen aan de register instelling weer te geven. de instelling van het REGI ster is echter nog steeds van toepassing op de verwachte manier en u ziet dat de RDP-sessie is losgekoppeld wanneer deze niet actief is gedurende de periode die u hebt opgegeven.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>De opdracht Windows afsluiten uit het start menu verwijderen

Met Windows **Local Groepsbeleid** -instellingen kunt u ook de afsluit opdracht verwijderen uit het **Start** menu.

Als u de afsluit opdracht wilt verwijderen, kunt u verbinding maken met de VM van de sjabloon en het onderstaande Power shell-script uitvoeren.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

U kunt er ook voor kiezen om deze hand matige stappen te volgen met behulp van de sjabloon-VM:

1. Druk op de Windows-toets, typ **gpedit**en selecteer vervolgens **groeps beleid bewerken (configuratie scherm)**.

1. Ga naar **computer configuratie > Beheersjablonen > menu Start en de taak balk**.  

    ![Editor voor lokaal groepsbeleid](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Klik met de rechter muisknop op **verwijderen en voorkom de toegang tot de opdrachten afsluiten, opnieuw opstarten, slaap stand en sluimer stand**en klik op **bewerken**.

1. Selecteer de **ingeschakelde** instelling en klik vervolgens op **OK**:
 
   ![Instelling voor afsluiten](./media/how-to-windows-shutdown/edit-shutdown.png)

1. U ziet dat de afsluit opdracht niet meer wordt weer gegeven onder het menu **Start** van Windows. alleen de opdracht **Disconnect** wordt weer gegeven.

    ![Opdracht shutdown](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Volgende stappen
Zie het artikel over het voorbereiden van een Windows-sjabloon-VM: [hand leiding voor het instellen van een Windows-sjabloon computer in Azure Lab Services](how-to-prepare-windows-template.md)