---
title: Problemen met systeemstatusback-up oplossen
description: In dit artikel vindt u meer informatie over het oplossen van problemen in System State Backup voor on-premises Windows-servers.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 28647b72334d592692c5fe1b031735330d1a0509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969579"
---
# <a name="troubleshoot-system-state-backup"></a>Problemen met systeemstatusback-up oplossen

In dit artikel worden oplossingen beschreven voor problemen die u tegenkomen tijdens het gebruik van System State Backup.

## <a name="basic-troubleshooting"></a>Eenvoudige probleemoplossing

We raden u aan de onderstaande validatie uit te voeren voordat u begint met het oplossen van problemen met systeemstatusback-ups:

- [Controleren of de MARS-agent (Microsoft Azure Recovery Services) up-to-date is](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Zorg ervoor dat er netwerkverbinding is tussen de MARS-agent en Azure](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Controleer of Microsoft Azure Recovery Services wordt uitgevoerd (in Service-console). Indien nodig de bewerking opnieuw opstarten en opnieuw proberen
- [Zorg ervoor dat er 5-10% ruimte vrij is op de locatie van de scratch-map](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Controleer of er geen ander proces of antivirussoftware conflicten veroorzaakt met Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Geplande back-up mislukt, maar handmatige back-up werkt](https://docs.microsoft.com/azure/backup/backup-azure-mars-troubleshoot#backups-dont-run-according-to-schedule)
- Zorg ervoor dat uw besturingssysteem helemaal is bijgewerkt
- [Ervoor zorgen dat niet-ondersteunde stations en bestanden met niet-ondersteunde kenmerken geen back-up smaken](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Ervoor zorgen dat **systeemklok** op het beveiligde systeem is geconfigureerd om de tijdzone te corrigeren <br>
- [Zorg ervoor dat op de server ten minste .Net Framework-versie 4.5.2 of hoger is geïnstalleerd](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Als u uw **server opnieuw** wilt registreren bij een kluis, gaat u als: <br>
  - Zorg ervoor dat de agent is verwijderd op de server en deze is verwijderd uit de portal <br>
  - Dezelfde wachtwoordzin gebruiken die in eerste instantie is gebruikt voor het registreren van de server <br>
- Als dit een offline back-up is, moet u ervoor zorgen dat Azure PowerShell-versie 3.7.0 is geïnstalleerd op zowel de bron- als kopieercomputer voordat u begint met de offline back-upbewerking
- [Overweging wanneer back-upagent wordt uitgevoerd op een virtuele Azure-machine](https://docs.microsoft.com/azure/backup/backup-azure-troubleshoot-slow-backup-performance-issue#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Beperking

- Het herstellen van bestanden naar andere hardware met behulp van systeemstatusherstel wordt niet aanbevolen door Microsoft
- System State backup ondersteunt momenteel 'on-premises' Windows-servers. Deze functionaliteit is niet beschikbaar voor Azure VM's.

## <a name="prerequisites"></a>Vereisten

Voordat we problemen met systeemstatusbackup met Azure Backup oplossen, voert u de onderstaande vereiste controle uit.  

### <a name="verify-windows-server-backup-is-installed"></a>Controleren of Windows Server Backup is geïnstalleerd

Controleer of Windows Server Backup is geïnstalleerd en ingeschakeld in de server. Voer de opdracht PowerShell uit om de installatiestatus te controleren:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Als de uitvoer de **installatiestatus** weergeeft als **beschikbaar,** betekent dit dat windows server-back-upfunctie beschikbaar is voor de installatie, maar niet op de server is geïnstalleerd. Als Windows Server Backup echter niet is geïnstalleerd, gebruikt u een van de onderstaande methoden om deze te installeren.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Methode 1: Windows Server Backup installeren met PowerShell

Voer de opdracht Windows Server Backup met PowerShell uit:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Methode 2: Windows Server Backup installeren met Serverbeheer

Als u Windows Server Backup wilt installeren met Serverbeheer, voert u de onderstaande stappen uit:

1. Klik in **Servermanger**op **Rollen en functies toevoegen**. De **wizard Rollen en functies toevoegen** wordt weergegeven.

    ![Dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selecteer **Installatietype** en klik op **Volgende**.

    ![Type installatie](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selecteer een server in de servergroep en klik op **Volgende**. Laat in de serverrol de standaardselectie achter en klik op **Volgende**.
4. Selecteer **Windows Server Back-up** op het tabblad **Onderdelen** en klik op **Volgende**.

    ![functies](./media/backup-azure-system-state-troubleshoot/features.png)

5. Klik op het tabblad **Bevestiging** op **Installeren** om het installatieproces te starten.
6. Op het tabblad **Resultaten** wordt de functie Windows Server Backup weergegeven die is geïnstalleerd op uw Windows Server.

    ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Machtigingen voor systeemvolumegegevens

Controleer of het lokale systeem de volledige controle heeft over de map **Systeemvolumegegevens** in het volume waarin Windows is geïnstalleerd. Meestal is dit **C:\Systeemvolumeinformatie**. Windows Server-back-up kan mislukken als de bovenstaande machtigingen niet correct zijn ingesteld

### <a name="dependent-services"></a>Afhankelijke diensten

Controleer of de onderstaande services in de werkstaat zijn:

**Servicenaam** | **Opstarttype**
--- | ---
RPC(Remote Procedure Call) | Automatisch
COM+ Event System(EventSystem) | Automatisch
Meldingsservice voor systeemgebeurtenissen(SENS) | Automatisch
Volumeschaduwkopie(VSS) | Handmatig
Microsoft Software Shadow Copy Provider (SWPRV) | Handmatig

### <a name="validate-windows-server-backup-status"></a>Status van Windows Server Backup valideren

Voer de volgende stappen uit om de status van Windows Server Backup te valideren:

- Ervoor zorgen dat WSB PowerShell actief is

  - Ren `Get-WBJob` vanaf een verhoogde PowerShell en zorg ervoor dat de volgende fout niet wordt weergegeven:

    > [!WARNING]
    > Get-WBJob: De term 'Get-WBJob' wordt niet herkend als de naam van een cmdlet, functie, scriptbestand of bedienbaar programma. Controleer de spelling van de naam of als er een pad is opgenomen, controleer of het pad correct is en probeer het opnieuw.

    - Als deze fout mislukt, installeert u de windows serverback-upfunctie opnieuw op de servermachine zoals vermeld in stap 1 van de vereisten.

  - Zorg ervoor dat de WSB-back-up naar behoren werkt door de onderstaande opdracht van een opdrachtprompt met verhoogde bevoegdheid uit te voeren:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Vervang X door de stationsletter van het volume waarin u de systeemstatus een back-upafbeelding wilt opslaan.

    - Controleer periodiek de status van `Get-WBJob` de taak door de opdracht uit te voeren vanuit de verhoogde PowerShell
    - Nadat de back-uptaak is voltooid, `Get-WBJob -Previous 1` controleert u de uiteindelijke status van de taak door de opdracht uit te voeren

Als de taak mislukt, geeft dit een WSB-probleem aan dat zou resulteren in een fout van de SYSTEEMstatusbacks van MARS-agent System.

## <a name="common-errors"></a>Algemene fouten

### <a name="vss-writer-timeout-error"></a>TIME-out VSS-schrijver: fout

| Symptoom | Oorzaak | Oplossing
| -- | -- | --
| - MARS-agent mislukt met foutmelding: "WSB-taak is mislukt met VSS-fouten. Vss-gebeurtenislogboeken controleren om de fout op te lossen"<br/><br/> - Volgende foutlogboek is aanwezig in VSS Application event logs: "Een VSS-schrijver heeft een gebeurtenis met fout 0x800423f2 afgewezen, de time-out van de schrijver is verlopen tussen de gebeurtenissen Freeze en Thaw."| VSS schrijver is niet in staat om te voltooien in de tijd als gevolg van gebrek aan CPU en geheugen middelen op de machine <br/><br/> Een andere back-up software is al met behulp van de VSS schrijver, als gevolg snapshot operatie niet kon voltooien voor deze back-up | Wacht tot CPU/geheugen wordt vrijgemaakt op het systeem of aborteer de processen die te veel geheugen / CPU en probeer de bewerking opnieuw. <br/><br/>  Wacht tot de doorlopende back-up is voltooid en probeer de bewerking op een later moment wanneer er geen back-ups op de machine worden uitgevoerd.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Onvoldoende schijfruimte om schaduwkopieën te laten groeien

| Symptoom | Oplossing
| -- | --
| - MARS-agent mislukt met foutbericht: Back-up mislukt omdat het volume van de schaduwkopie niet kon groeien vanwege onvoldoende schijfruimte op volumes die systeembestanden bevatten <br/><br/> - Volgende fout/waarschuwingslogboek is aanwezig in volsnap-systeemgebeurtenislogboeken: "Er was onvoldoende schijfruimte op volume C: om de schaduwkopieopslag voor schaduwkopieën van C te laten groeien: als gevolg van deze storing lopen alle schaduwkopieën van volume C: lopen het risico te worden verwijderd" | - Maak ruimte vrij in het gemarkeerde volume in het gebeurtenislogboek, zodat er voldoende ruimte is voor schaduwkopieën om te groeien terwijl de back-up aan de gang is <br/><br/> - Tijdens het configureren van schaduwkopieerruimte kunnen we de hoeveelheid ruimte die wordt gebruikt voor schaduwkopie beperken. Zie dit [artikel](https://docs.microsoft.com/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage) voor meer informatie

### <a name="efi-partition-locked"></a>EFI-partitie vergrendeld

| Symptoom | Oplossing
| -- | --
| MARS-agent mislukt met foutmelding: "Back-up van systeemstatus is mislukt omdat de EFI-systeempartitie is vergrendeld. Dit kan te wijten zijn aan toegang tot de systeempartitie door een beveiliging van derden of back-upsoftware" | - Als het probleem te wijten is aan een beveiligingssoftware van derden, moet u contact opnemen met de antivirusleverancier, zodat ze MARS-agent kunnen toestaan <br/><br/> - Als een back-upsoftware van derden wordt uitgevoerd, wacht dan tot deze is voltooid en probeer dan opnieuw een back-up

## <a name="next-steps"></a>Volgende stappen

- Zie [Back-up maken van de status van windows server-systeem](backup-azure-system-state.md) voor meer informatie over de status van het Windows-systeem in De implementatie van ResourceManager
