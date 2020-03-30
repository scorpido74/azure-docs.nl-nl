---
title: Bestanden en mappen herstellen vanuit Azure VM-back-up
description: In dit artikel vindt u informatie over het herstellen van bestanden en mappen vanaf een herstelpunt voor virtuele azure-machines.
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 0e3061ea8fc26adcf39fe415cd9a662de739543a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273304"
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Bestanden herstellen vanaf back-up van de virtuele machine van Azure

Azure Backup biedt de mogelijkheid om [Virtuele Azure-machines (VM's) en schijven](./backup-azure-arm-restore-vms.md) te herstellen van Azure VM-back-ups, ook wel herstelpunten genoemd. In dit artikel wordt uitgelegd hoe u bestanden en mappen herstellen van een Azure VM-back-up. Het herstellen van bestanden en mappen is alleen beschikbaar voor Azure VM's die zijn geïmplementeerd met behulp van het Resource Manager-model en zijn beveiligd met een kluis van Herstelservices.

> [!NOTE]
> Deze functie is beschikbaar voor Azure VM's die zijn geïmplementeerd met behulp van het Resource Manager-model en is beveiligd tot een vault van Recovery Services.
> Bestandsherstel van een versleutelde VM-back-up wordt niet ondersteund.
>

## <a name="mount-the-volume-and-copy-files"></a>Het volume monteren en bestanden kopiëren

Als u bestanden of mappen wilt herstellen vanaf het herstelpunt, gaat u naar de virtuele machine en kiest u het gewenste herstelpunt.

1. Meld u aan bij de [Azure-portal](https://portal.Azure.com) en klik in het linkerdeelvenster op **Virtuele machines**. Selecteer in de lijst met virtuele machines de virtuele machine om het dashboard van die virtuele machine te openen.

2. Klik in het menu van de virtuele machine op **Back-up** om het back-updashboard te openen.

    ![Back-upitem voor kluisherstelservices openen](./media/backup-azure-restore-files-from-vm/open-vault-for-vm.png)

3. Klik in het menu Back-updashboard op **Bestandsherstel**.

    ![Knop Bestandsherstel](./media/backup-azure-restore-files-from-vm/vm-backup-menu-file-recovery-button.png)

    Het menu **Bestandsherstel** wordt geopend.

    ![Menu Bestandsherstel](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Selecteer in het vervolgkeuzemenu **Herstelpunt** selecteren het herstelpunt met de gewenste bestanden. Standaard is het laatste herstelpunt al geselecteerd.

5. Als u de software wilt downloaden die wordt gebruikt om bestanden vanaf het herstelpunt te kopiëren, klikt u op **Uitvoer downloaden** (voor Windows Azure VM's) of **Script downloaden** (voor Linux Azure VM's wordt een python-script gegenereerd).

    ![Gegenereerd wachtwoord](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure downloadt het uitvoerbare of script naar de lokale computer.

    ![downloadbericht voor het uitvoerbare of script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Als u het uitvoerbare of script als beheerder wilt uitvoeren, wordt voorgesteld het gedownloade bestand op uw computer op te slaan.

6. Het uitvoerbare of script is beveiligd met een wachtwoord en vereist een wachtwoord. Klik in het menu **Bestandsherstel** op de knop Kopiëren om het wachtwoord in het geheugen te laden.

    ![Gegenereerd wachtwoord](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. Klik vanaf de downloadlocatie (meestal de map Downloads) met de rechtermuisknop op het uitvoerbare of script en voer deze uit met administratorreferenties. Typ het wachtwoord of plak het wachtwoord uit het geheugen wanneer u daarom wordt gevraagd en druk op **Enter**. Zodra het geldige wachtwoord is ingevoerd, maakt het script verbinding met het herstelpunt.

    ![Menu Bestandsherstel](./media/backup-azure-restore-files-from-vm/executable-output.png)

8. Voor Linux-machines wordt een python-script gegenereerd. Men moet het script downloaden en kopiëren naar de relevante / compatibele Linux-server. Mogelijk moet u de machtigingen wijzigen ```chmod +x <python file name>```om deze uit te voeren met . Voer vervolgens het ```./<python file name>```python-bestand uit met .

Raadpleeg de sectie [Toegangsvereisten](#access-requirements) om te zien of het script wordt uitgevoerd.

### <a name="identifying-volumes"></a>Volumes identificeren

#### <a name="for-windows"></a>Voor Windows

Wanneer u het uitvoerbare uitvoert, monteert het besturingssysteem de nieuwe volumes en wijst het aandrijfletters toe. U Windows Verkenner of Verkenner gebruiken om door deze stations te bladeren. De aan de volumes toegewezen stations kunnen niet dezelfde letters zijn als de oorspronkelijke virtuele machine. De naam van het volume blijft echter behouden. Als het volume op de oorspronkelijke virtuele machine bijvoorbeeld`\`"Gegevensschijf (E: )" was, kan dat volume op`\`de lokale computer worden aangesloten als 'Gegevensschijf ('Elke letter': ). Blader door alle volumes die in de scriptuitvoer worden genoemd totdat u uw bestanden of map hebt gevonden.  

   ![Menu Bestandsherstel](./media/backup-azure-restore-files-from-vm/volumes-attached.png)

#### <a name="for-linux"></a>Voor Linux

In Linux worden de volumes van het herstelpunt gemonteerd op de map waar het script wordt uitgevoerd. De bijgevoegde schijven, volumes en de bijbehorende bergpaden worden dienovereenkomstig weergegeven. Deze bevestigingspaden zijn zichtbaar voor gebruikers met rootniveautoegang. Blader door de volumes die in de scriptuitvoer worden genoemd.

  ![Menu Linux File recovery](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)

## <a name="closing-the-connection"></a>De verbinding sluiten

Nadat u de bestanden hebt geïdentificeerd en naar een lokale opslaglocatie hebt gekopieerd, verwijdert u de extra stations (of ontkoppelt). Als u de stations wilt ontkoppelen, klikt u in het menu **Bestandsherstel** in de Azure-portal op **Schijven ontmonteren**.

![Schijven loskoppelen](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Zodra de schijven zijn losgemaakt, ontvangt u een bericht. Het kan enkele minuten duren voordat de verbinding is vernieuwd, zodat u de schijven verwijderen.

In Linux verwijdert het besturingssysteem de bijbehorende bergpaden niet automatisch nadat de verbinding met het herstelpunt is verbroken. De mount paden bestaan als "wees" volumes en zijn zichtbaar, maar gooi een fout wanneer u toegang / schrijven van de bestanden. Ze kunnen handmatig worden verwijderd. Het script identificeert bij het uitvoeren van dergelijke volumes die bestaan van eerdere herstelpunten en ruimt ze op na toestemming.

## <a name="special-configurations"></a>Speciale configuraties

### <a name="dynamic-disks"></a>Dynamische schijven

Als de beveiligde Azure VM volumes heeft met een of beide van de volgende kenmerken, u het uitvoerbare script niet op dezelfde VM uitvoeren.

- Volumes die meerdere schijven omvatten (overspannen en gestreepte volumes)
- Fouttolerante volumes (gespiegelde en RAID-5-volumes) op dynamische schijven

Voer in plaats daarvan het uitvoerbare script uit op elke andere computer met een compatibel besturingssysteem.

### <a name="windows-storage-spaces"></a>Windows-opslagruimten

Windows Storage Spaces is een Windows-technologie waarmee u opslag virtualiseren. Met Windows-opslagruimten u standaardschijven groeperen in opslaggroepen. Vervolgens gebruikt u de beschikbare ruimte in die opslagpools om virtuele schijven te maken, de zogenaamde opslagruimten.

Als de beveiligde Azure VM Windows-opslagruimten gebruikt, u het uitvoerbare script niet op dezelfde VM uitvoeren. Voer in plaats daarvan het uitvoerbare script uit op elke andere machine met een compatibel besturingssysteem.

### <a name="lvmraid-arrays"></a>LVM/RAID-arrays

In Linux worden Logische volumebeheer (LVM) en/of software RAID Arrays gebruikt om logische volumes over meerdere schijven te beheren. Als de beveiligde Linux-VM LVM en/of RAID-arrays gebruikt, u het script niet op dezelfde VM uitvoeren. Voer het script in plaats daarvan uit op een andere machine met een compatibel besturingssysteem en dat het bestandssysteem van de beveiligde VM ondersteunt.

In de volgende scriptuitvoer worden de schijven LVM- en/of RAID-arrays en de volumes met het partitietype weergegeven.

   ![Menu Linux LVM Output](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)

Voer de opdrachten uit in de volgende secties om deze partities online te zetten.

#### <a name="for-lvm-partitions"></a>Voor LVM-partities

Ga als lid van de volumegroep onder een fysiek volume:

```bash
#!/bin/bash
pvs <volume name as shown above in the script output>
```

Ga als lid van het nieuwe overzicht van alle logische volumes, namen en hun paden in een volumegroep:

```bash
#!/bin/bash
lvdisplay <volume-group-name from the pvs command's results>
```

Ga als volgt te werk om de logische volumes aan het pad van uw keuze te monteren:

```bash
#!/bin/bash
mount <LV path> </mountpath>
```

#### <a name="for-raid-arrays"></a>Voor RAID-arrays

In de volgende opdracht worden details over alle raid-schijven weergegeven:

```bash
#!/bin/bash
mdadm –detail –scan
```

 De relevante RAID-schijf wordt weergegeven als`/dev/mdm/<RAID array name in the protected VM>`

Gebruik de opdracht monteren als de RAID-schijf fysieke volumes heeft:

```bash
#!/bin/bash
mount [RAID Disk Path] [/mountpath]
```

Als er op de RAID-schijf een andere LVM is geconfigureerd, gebruikt u de vorige procedure voor LVM-partities, maar gebruikt u de volumenaam in plaats van de naam RAID-schijf.

## <a name="system-requirements"></a>Systeemvereisten

### <a name="for-windows-os"></a>Voor Windows OS

In de volgende tabel ziet u de compatibiliteit tussen server- en computerbesturingssystemen. Wanneer u bestanden herstelt, u bestanden niet herstellen naar een vorige of toekomstige versie van het besturingssysteem. U bijvoorbeeld een bestand niet herstellen van een Windows Server 2016-vm naar Windows Server 2012 of een Windows 8-computer. U bestanden van een vm herstellen naar hetzelfde serverbesturingssysteem of naar het compatibele besturingssysteem van de client.

|Serverbesturingssysteem | Compatibel client-besturingssysteem  |
| --------------- | ---- |
| Windows Server 2019    | Windows 10 |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux-os"></a>Voor Linux OS

In Linux moet het besturingssysteem van de computer dat wordt gebruikt om bestanden te herstellen, het bestandssysteem van de beveiligde virtuele machine ondersteunen. Wanneer u een computer selecteert om het script uit te voeren, moet u ervoor zorgen dat de computer een compatibel besturingssysteem heeft en een van de versies gebruikt die in de volgende tabel zijn geïdentificeerd:

|Linux OS | Versies  |
| --------------- | ---- |
| Ubuntu | 12.04 uur en ouder |
| CentOS | 6.5 en hoger  |
| RHEL | 6.7 en hoger |
| Debian | 7 en hoger |
| Oracle Linux | 6.4 en hoger |
| SLES | 12 en hoger |
| openSUSE | 42.2 en hoger |

> [!NOTE]
> We hebben een aantal problemen gevonden bij het uitvoeren van het bestandsherstelscript op machines met SLES 12 SP4 OS en we onderzoeken het met het SLES-team.
> Momenteel werkt het bestandsherstelscript op machines met SLES 12 SP2- en SP3 OS-versies.
>

Het script vereist ook Python- en bash-componenten om veilig verbinding te maken met het herstelpunt.

|Onderdeel | Versie  |
| --------------- | ---- |
| bash | 4 en hoger |
| python | 2.6.6 en hoger  |
| TLS | 1.2 Moet worden ondersteund  |

## <a name="access-requirements"></a>Vereisten voor toegang

Als u het script uitvoert op een computer met beperkte toegang, moet u ervoor zorgen dat er toegang is tot:

- `download.microsoft.com`
- URL's van herstelservice (geonaam verwijst naar het gebied waar de kluis van de herstelservice zich bevindt)
  - `https://pod01-rec2.geo-name.backup.windowsazure.com`(Voor openbare geo's van Azure)
  - `https://pod01-rec2.geo-name.backup.windowsazure.cn`(Voor Azure China 21Vianet)
  - `https://pod01-rec2.geo-name.backup.windowsazure.us`(Voor Azure Us Government)
  - `https://pod01-rec2.geo-name.backup.windowsazure.de`(Voor Azure Duitsland)
- Uitgaande poorten 53 (DNS), 443, 3260

> [!NOTE]
>
> - De gedownloade scriptbestandsnaam heeft de **geonaam** die moet worden ingevuld in de URL. Voor exampple: De gedownloade \'scriptnaam\'\_\'begint\'met\'VMname geoname _ GUID,\'zoals *ContosoVM_wcus_12345678*
> - De URL <https://pod01-rec2.wcus.backup.windowsazure.com>zou " zijn
>

Voor Linux vereist het script 'open-iscsi' en 'lshw' componenten om verbinding te maken met het herstelpunt. Als de onderdelen niet bestaan op de computer waar het script wordt uitgevoerd, vraagt het script om toestemming om de componenten te installeren. Geef toestemming om de benodigde onderdelen te installeren.

De toegang `download.microsoft.com` tot de download onderdelen die worden gebruikt om een veilig kanaal te bouwen tussen de machine waar het script wordt uitgevoerd en de gegevens in het herstelpunt.

U het script uitvoeren op elke machine met hetzelfde (of compatibele) besturingssysteem als de back-up VM. Zie de [tabel Compatibel besturingssysteem](backup-azure-restore-files-from-vm.md#system-requirements) voor compatibele besturingssystemen. Als de beveiligde Virtuele Azure-machine Windows-opslagruimten (voor Windows Azure VM's) of LVM/RAID-arrays (voor Linux-VM's) gebruikt, u het uitvoerbare of script niet uitvoeren op dezelfde virtuele machine. Voer in plaats daarvan het uitvoerbare of script uit op een andere machine met een compatibel besturingssysteem.

## <a name="file-recovery-from-virtual-machine-backups-having-large-disks"></a>Bestandsherstel van virtuele machineback-ups met grote schijven

In dit gedeelte wordt uitgelegd hoe u bestandsherstel uitvoert van back-ups van Azure Virtual-machines met meer dan 16 schijven en dat elke schijfgrootte groter is dan 32 TB.

Aangezien het bestandsherstelproces alle schijven van de back-up hecht, worden de volgende actiepunten aanbevolen wanneer een groot aantal schijven (>16) of grote schijven (> elk 32 TB) wordt gebruikt:

- Houd een aparte herstelserver (Azure VM D2v3 VM's) voor bestandsherstel. U dat alleen gebruiken voor bestandsherstel en vervolgens afsluiten wanneer dit niet nodig is. Herstellen op de originele machine wordt niet aanbevolen, omdat het aanzienlijke invloed zal hebben op de VM zelf.
- Voer het script vervolgens één keer uit om te controleren of de bestandsherstelbewerking slaagt.
- Als het bestandsherstelproces vastloopt (de schijven zijn nooit gemonteerd of ze zijn gemonteerd, maar volumes worden niet weergegeven), voert u de volgende stappen uit.
  - Als de herstelserver een Windows-vm is:
    - Zorg ervoor dat het besturingssysteem WS 2012 of hoger is.
    - Zorg ervoor dat de registersleutels zijn ingesteld zoals hieronder wordt voorgesteld in de herstelserver en zorg ervoor dat de server opnieuw wordt opgestart. Het aantal naast guid kan van 0001-0005 variëren. In het volgende voorbeeld is het 0004. Navigeer door het registersleutelpad tot de sectie parameters.

    ![iscsi-reg-key-changes.png](media/backup-azure-restore-files-from-vm/iscsi-reg-key-changes.png)

```registry
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Disk\TimeOutValue – change this from 60 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\SrbTimeoutDelta – change this from 15 to 1200
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\EnableNOPOut – change this from 0 to 1
- HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Control\Class\{4d36e97b-e325-11ce-bfc1-08002be10318}\0003\Parameters\MaxRequestHoldTime - change this from 60 to 1200
```

- Als de herstelserver een Linux-vm is:
  - Wijzig in het bestand /etc/iscsi/iscsid.conf de instelling van:
    - node.conn[0].timeo.noop_out_timeout = 5 tot node.conn[0].timeo.noop_out_timeout = 30
- Nadat u de bovenstaande wijziging hebt gemaakt, voert u het script opnieuw uit. Met deze wijzigingen is het zeer waarschijnlijk dat het herstel van het bestand zal slagen.
- Elke keer dat de gebruiker een script downloadt, initieert Azure Backup het proces van het voorbereiden van het herstelpunt voor download. Bij grote schijven zal dit proces veel tijd in beslag nemen. Als er opeenvolgende uitbarstingen van verzoeken zijn, zal de doelvoorbereiding in een downloadspiraal gaan. Daarom is het raadzaam om een script te downloaden van Portal / Powershell / CLI, wachten voor 20-30 minuten (een heuristische) en vervolgens uit te voeren. Tegen die tijd is het doel naar verwachting klaar voor verbinding vanuit het script.
- Zorg ervoor dat u na het herstel van het bestand teruggaat naar de portal en op **Schijven ontkoppelen** voor herstelpunten waar u geen volumes hebt kunnen monteren. In wezen zal deze stap alle bestaande processen/ sessies reinigen en de kans op herstel vergroten.

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen ondervindt bij het herstellen van bestanden van de virtuele machines, raadpleegt u de volgende tabel voor aanvullende informatie.

| Foutbericht / Scenario | Waarschijnlijke oorzaak | Aanbevolen actie |
| ------------------------ | -------------- | ------------------ |
| Exe-uitvoer: *uitzondering die wordt gemaakt tijdens het verbinding maken met doel* | Het script heeft geen toegang tot het herstelpunt    | Controleer of de machine voldoet aan de [vorige toegangsvereisten.](#access-requirements) |  
| Exe-uitvoer: *Het doel is al aangemeld via een iSCSI-sessie.* | Het script is al uitgevoerd op dezelfde machine en de schijven zijn bevestigd | De volumes van het herstelpunt zijn reeds bijgevoegd. Ze mogen NIET worden gemonteerd met dezelfde drive letters van de originele VM. Blader door alle beschikbare volumes in de verkenner voor uw bestand. |
| Exe-uitvoer: *Dit script is ongeldig omdat de schijven zijn gedemonteerd via portal/overschreden de limiet van 12 uur. Download een nieuw script van de portal.* |    De schijven zijn gedemonteerd van het portaal of de 12-uurs limiet werd overschreden | Deze specifieke exe is nu ongeldig en kan niet worden uitgevoerd. Als u toegang wilt krijgen tot de bestanden van dat herstelpunt-in-time, gaat u naar de portal voor een nieuwe exe.|
| Op de machine waar de exe wordt uitgevoerd: De nieuwe volumes worden niet gedemonteerd nadat de demonteerknop is geklikt | De iSCSI-initiator op de machine reageert/vernieuwt de verbinding met het doel niet en onderhoudt de cache niet. |  Na het klikken op **Demonteren,** wacht een paar minuten. Als de nieuwe volumes niet worden gedemonteerd, bladert u door alle volumes. Als u door alle volumes bladert, dwingt de initiator de verbinding te vernieuwen en wordt het volume gedemonteerd met een foutbericht dat de schijf niet beschikbaar is.|
| Exe-uitvoer: het script wordt uitgevoerd, maar 'Nieuwe volumes zijn toegevoegd' worden niet weergegeven op de scriptuitvoer |    Dit is een tijdelijke fout    | De volumes zullen al zijn aangesloten. Open Explorer om te bladeren. Als u elke keer dezelfde machine gebruikt voor het uitvoeren van scripts, u overwegen de machine opnieuw op te starten en moet de lijst worden weergegeven in de volgende exe-uitvoeringen. |
| Linux specifiek: Niet in staat om de gewenste volumes te bekijken | Het besturingssysteem van de machine waar het script wordt uitgevoerd, herkent mogelijk het onderliggende bestandssysteem van de beveiligde VM niet | Controleer of het herstelpunt crashconsistent of bestandconsistent is. Als het script consistent is, voert u het script uit op een andere machine waarvan het besturingssysteem het bestandssysteem van de beveiligde virtuele machine herkent. |
| Windows-specifiek: niet in staat om de gewenste volumes te bekijken | De schijven zijn mogelijk bevestigd, maar de volumes zijn niet geconfigureerd | Identificeer in het scherm schijfbeheer de extra schijven die betrekking hebben op het herstelpunt. Als een van deze schijven zich offline bevindt, probeert u ze online te brengen door met de rechtermuisknop op de schijf te klikken en op **Online**te klikken.|

## <a name="security"></a>Beveiliging

In dit gedeelte worden de verschillende beveiligingsmaatregelen besproken die zijn genomen voor de implementatie van bestandsherstel van Azure VM-back-ups.

### <a name="feature-flow"></a>Functiestroom

Deze functie is gebouwd om toegang te krijgen tot de VM-gegevens zonder dat u de volledige VM- of VM-schijven hoeft te herstellen en met het minimumaantal stappen. Toegang tot VM-gegevens wordt geboden door een script (dat het herstelvolume monteert wanneer het wordt uitgevoerd zoals hieronder wordt weergegeven) en vormt de hoeksteen van alle beveiligingsimplementaties:

  ![Stroom beveiligingsfuncties](./media/backup-azure-restore-files-from-vm/vm-security-feature-flow.png)

### <a name="security-implementations"></a>Beveiligingsimplementaties

#### <a name="select-recovery-point-who-can-generate-script"></a>Herstelpunt selecteren (wie kan script genereren)

Het script biedt toegang tot VM-gegevens, dus het is belangrijk om te regelen wie het in de eerste plaats kan genereren. U moet zich aanmelden bij de Azure-portal en [rbac zijn geautoriseerd](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) om het script te genereren.

Voor bestandsherstel is hetzelfde machtigingsniveau nodig als vereist voor het herstellen van vm's en het herstellen van schijven. Met andere woorden, alleen geautoriseerde gebruikers kunnen de VM-gegevens bekijken en het script genereren.

Het gegenereerde script is ondertekend met het officiële Microsoft-certificaat voor de Azure Backup-service. Elke manipulatie met het script betekent dat de handtekening is verbroken en elke poging om het script uit te voeren wordt gemarkeerd als een potentieel risico door het besturingssysteem.

#### <a name="mount-recovery-volume-who-can-run-script"></a>Herstelvolume monteren (wie kan script uitvoeren)

Alleen een beheerder kan het script uitvoeren en het moet in de verhoogde modus worden uitgevoerd. Het script voert alleen een vooraf gegenereerde set stappen uit en accepteert geen invoer van externe bronnen.

Om het script uit te voeren, is een wachtwoord vereist dat alleen wordt weergegeven aan de geautoriseerde gebruiker op het moment van het genereren van script in de Azure-portal of PowerShell/CLI. Dit is om ervoor te zorgen dat de geautoriseerde gebruiker die het script downloadt ook verantwoordelijk is voor het uitvoeren van het script.

#### <a name="browse-files-and-folders"></a>Bladeren door bestanden en mappen

Om door bestanden en mappen te bladeren, gebruikt het script de iSCSI-initiator in de machine en maakt het verbinding met het herstelpunt dat is geconfigureerd als een iSCSI-doel. Hier u zich voorstellen scenario's waar men probeert te imiteren / spoof ofwel / alle componenten.

We gebruiken een wederzijds CHAP-verificatiemechanisme, zodat elk onderdeel de andere verifieert. Dit betekent dat het uiterst moeilijk is voor een nep-initiator om verbinding te maken met het iSCSI-doel en dat een nepdoel wordt aangesloten op de machine waar het script wordt uitgevoerd.

De gegevensstroom tussen de herstelservice en de machine wordt beschermd door het bouwen van een veilige TLS-tunnel via TCP[(TLS 1.2 moet worden ondersteund](#system-requirements) in de machine waar het script wordt uitgevoerd).

Alle bestandstoegangsbeheerlijst (ACL) die aanwezig is in de bovenliggende/back-upVM, blijft ook in het gemonteerde bestandssysteem behouden.

Het script geeft alleen-lezen toegang tot een herstelpunt en is slechts 12 uur geldig. Als u de toegang eerder wilt verwijderen, meldt u zich aan bij Azure Portal/PowerShell/CLI en voert **u de losmakende schijven** uit voor dat specifieke herstelpunt. Het script wordt onmiddellijk ongeldig verklaard.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de sectie [Probleemoplossing](#troubleshooting) voor eventuele problemen tijdens het herstellen van bestanden
- Meer informatie over het [herstellen van bestanden via Powershell](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#restore-files-from-an-azure-vm-backup)
- Meer informatie over het [herstellen van bestanden via Azure CLI](https://docs.microsoft.com/azure/backup/tutorial-restore-files)
- Nadat VM is hersteld, leert u hoe [u back-ups beheert](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms)
