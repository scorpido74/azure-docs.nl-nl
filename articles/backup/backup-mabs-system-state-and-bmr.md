---
title: Systeemstatus en bare-metal terugwinningsbeveiliging
description: Gebruik Azure Backup Server om een back-up van uw systeemstatus te maken en BMR-bescherming (bare-metal recovery) te bieden.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 358a1c96d598788170993fc48e60daae2b6b036c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505880"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Back-ups maken van de systeemstatus en deze herstellen naar bare metal met Azure Backup Server

Azure Backup Server maakt een back-up van de systeemstatus en biedt BMR-bescherming (bare-metal recovery).

* **Back-up van systeemstatus:** maakt een back-up van bestanden van het besturingssysteem. Met deze back-up u herstellen wanneer een computer wordt gestart, maar worden systeembestanden en het register verloren gegaan. Een back-up van de systeemstatus bevat de volgende elementen:
  * Domeinlid: opstartbestanden, registratiedatabase van COM+-klasse, register
  * Domeincontroller: Windows Server Active Directory (NTDS), opstartbestanden, COM+-klasseregistratiedatabase, register, systeemvolume (SYSVOL)
  * Computer die clusterservices uitvoert: metagegevens van clusterservers
  * Computer die certificaatservices uitvoert: certificaatgegevens
* **Bare-metal back-up:** maakt een back-up van besturingssysteembestanden en alle gegevens over kritieke volumes, behalve gebruikersgegevens. Per definitie bevat een BMR-back-up een back-up van de systeemstatus. Het biedt bescherming wanneer een computer niet start en je moet alles herstellen.

In de volgende tabel wordt samengevat wat u back-ups maken en herstellen. [Zie Wat maakt een back-up](backup-mabs-protection-matrix.md)van Azure Backup Server voor informatie over app-versies die systeemstatus en BMR kunnen beveiligen.

|Back-up|Probleem|Herstellen van Azure Backup Server-back-up|Herstellen vanaf systeemstatusback-up|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Bestandsgegevens**<br /><br />Standaard gegevensback-up<br /><br />BMR/systeemstatusback-up|Verloren bestandsgegevens|J|N|N|
|**Bestandsgegevens**<br /><br />Back-up van bestandsgegevens van Azure Backup Server<br /><br />BMR/systeemstatusback-up|Verloren of beschadigd besturingssysteem|N|J|J|
|**Bestandsgegevens**<br /><br />Back-up van bestandsgegevens van Azure Backup Server<br /><br />BMR/systeemstatusback-up|Verloren server (gegevensvolumes intact)|N|N|J|
|**Bestandsgegevens**<br /><br />Back-up van bestandsgegevens van Azure Backup Server<br /><br />BMR/systeemstatusback-up|Verloren server (gegevensvolumes verloren)|J|N|J<br /><br />BMR, gevolgd door regelmatige herstel van back-up bestandsgegevens|
|**SharePoint-gegevens**<br /><br />Azure Backup Server back-up van farmgegevens<br /><br />BMR/systeemstatusback-up|Verloren site, lijsten, lijstitems, documenten|J|N|N|
|**SharePoint-gegevens**<br /><br />Azure Backup Server back-up van farmgegevens<br /><br />BMR/systeemstatusback-up|Verloren of beschadigd besturingssysteem|N|J|J|
|**SharePoint-gegevens**<br /><br />Azure Backup Server back-up van farmgegevens<br /><br />BMR/systeemstatusback-up|Herstel na noodgeval|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server back-up van Hyper-V host of gast<br /><br />BMR/systeemstatusback-up van host|Verloren virtuele machine|J|N|N|
|Hyper-V<br /><br />Azure Backup Server back-up van Hyper-V host of gast<br /><br />BMR/systeemstatusback-up van host|Verloren of beschadigd besturingssysteem|N|J|J|
|Hyper-V<br /><br />Azure Backup Server back-up van Hyper-V host of gast<br /><br />BMR/systeemstatusback-up van host|Verloren Hyper-V-host (virtuele machines intact)|N|N|J|
|Hyper-V<br /><br />Azure Backup Server back-up van Hyper-V host of gast<br /><br />BMR/systeemstatusback-up van host|Verloren Hyper-V-host (verloren virtuele machines)|N|N|J<br /><br />BMR, gevolgd door regelmatig Azure Backup Server-herstel|
|SQL Server/Exchange<br /><br />Back-up van de Azure Backup Server-app<br /><br />BMR/systeemstatusback-up|Verloren appgegevens|J|N|N|
|SQL Server/Exchange<br /><br />Back-up van de Azure Backup Server-app<br /><br />BMR/systeemstatusback-up|Verloren of beschadigd besturingssysteem|N|J|J|
|SQL Server/Exchange<br /><br />Back-up van de Azure Backup Server-app<br /><br />BMR/systeemstatusback-up|Verloren server (database/transactielogboekbestanden intact)|N|N|J|
|SQL Server/Exchange<br /><br />Back-up van de Azure Backup Server-app<br /><br />BMR/systeemstatusback-up|Verloren server (verloren database/transactielogboekbestanden)|N|N|J<br /><br />BMR-herstel, gevolgd door regelmatig Azure Backup Server-herstel|

## <a name="how-system-state-backup-works"></a>Hoe een systeemstatusback-up werkt

Wanneer een back-up van de systeemstatus wordt uitgevoerd, communiceert Backup Server met Windows Server Backup om een back-up van de systeemstatus van de server aan te vragen. Standaard gebruiken Backup Server en Windows Server Backup het station met de meest beschikbare vrije ruimte. U vindt informatie over dit station opgeslagen in het *PSDataSourceConfig.xml-bestand.* 

U het station aanpassen dat back-ups van de back-up van de systeemstatus gebruiken: 

1. Ga op de beveiligde server naar *C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources*. 
1. Open het *PSDataSourceConfig.xml-bestand* voor bewerking. 
1. Wijzig \<de waarde\> FilesToProtect voor de stationsletter. 
1. Sla het bestand op en sluit het. 

Als een beveiligingsgroep is ingesteld om de systeemstatus van de computer te beschermen, voert u een consistentiecontrole uit. Als er een waarschuwing wordt gegenereerd, selecteert u **De beveiligingsgroep wijzigen** in de waarschuwing en voltooit u de pagina's in de wizard. Voer vervolgens nog een consistentiecontrole uit.

Als de beveiligingsserver zich in een cluster bevindt, kan een clusterstation worden geselecteerd als het station met de meeste vrije ruimte. Als dat station eigendom is overgeschakeld naar een ander knooppunt en een systeem status back-up wordt uitgevoerd, dan is het station niet beschikbaar en de back-up mislukt. Wijzig in dit scenario *PSDataSourceConfig.xml* om naar een lokaal station te verwijzen.

Vervolgens maakt Windows Server Backup een map met de naam *WindowsImageBackup* in de hoofdmap van de herstelmap. Terwijl Windows Server Backup de back-up maakt, worden alle gegevens in deze map geplaatst. Wanneer de back-up is voltooid, wordt het bestand overgebracht naar de back-upservercomputer. Let op de volgende informatie:

* Deze map en de inhoud ervan worden niet opgeschoond wanneer de back-up of overdracht is voltooid. De beste manier om dit te bedenken is dat de ruimte is gereserveerd voor de volgende keer dat een back-up is voltooid.
* De map is gemaakt voor elke back-up. De tijd- en datumstempel weerspiegelen de tijd van de laatste back-up van de systeemstatus.

## <a name="how-bmr-backup-works"></a>Hoe BMR-back-up werkt

Voor BMR (inclusief een back-up van de systeemstatus) wordt de back-uptaak rechtstreeks opgeslagen in een share op de back-upservercomputer. Deze wordt niet opgeslagen in een map op de beveiligde server.

Backup Server roept Windows Server Backup aan en deelt het replicavolume voor die BMR-back-up. In dit geval hoeft windows serverback-up niet het station te gebruiken dat de meeste vrije ruimte heeft. In plaats daarvan wordt het aandeel gebruikt dat is gemaakt voor de taak.

Wanneer de back-up is voltooid, wordt het bestand overgebracht naar de back-upservercomputer. Logboeken worden opgeslagen in *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Vereisten en beperkingen

* BMR wordt niet ondersteund voor computers waarop Windows Server 2003 wordt uitgevoerd of voor computers waarop een clientbesturingssysteem wordt uitgevoerd.

* U kunt niet BMR en systeemstatus voor dezelfde computer in verschillende beveiligingsgroepen beveiligen.

* Een back-upserverkan zichzelf niet beschermen voor BMR.

* Bescherming op korte termijn naar tape (disk-to-tape of D2T) wordt niet ondersteund voor BMR. Langdurige opslag naar tape (disk-to-disk to tape of D2D2T) wordt ondersteund.

* Voor BMR-beveiliging moet Windows Server Backup op de beveiligde computer zijn geïnstalleerd.

* Voor BMR-beveiliging heeft Backup Server, in tegenstelling tot systeemstatusbeveiliging, geen ruimtevereisten op de beveiligde computer. Windows Server Backup brengt back-ups rechtstreeks over naar de back-upserver. De taak voor back-upoverdracht wordt niet weergegeven in de weergave **Taken van** back-upserver.

* Backup Server reserveert 30 GB ruimte op het replicavolume voor BMR. U deze ruimtetoewijzing wijzigen op de pagina **Schijftoewijzing** in de wizard Beveiligingsgroep wijzigen. U ook de cmdlets Get-DatasourceDiskAllocation en Set-DatasourceDiskAllocation PowerShell gebruiken. Op het volume van het herstelpunt vereist BMR-bescherming ongeveer 6 GB voor een retentie van vijf dagen.
  * U de grootte van het replicavolume niet verkleinen tot minder dan 15 GB.
  * Backup Server berekent niet de grootte van de BMR-gegevensbron. Het veronderstelt 30 GB voor alle servers. Wijzig de waarde op basis van de grootte van BMR-back-ups die u in uw omgeving verwacht. U ruwweg de grootte van een BMR-back-up berekenen als de som van de gebruikte ruimte op alle kritieke volumes. Kritieke volumes = opstartvolume + systeemvolume + statusgegevens van het volumehostingsysteem, zoals Active Directory.

* Als u overstapt van systeemstatusbeveiliging naar BMR-beveiliging, heeft BMR-bescherming minder ruimte nodig op het volume van het *herstelpunt.* De extra ruimte op het volume wordt echter niet teruggewonnen. U de volumegrootte handmatig verkleinen op de pagina **Schijftoewijzing wijzigen** van de wizard Beveiligingsgroep wijzigen. U ook de cmdlets Get-DatasourceDiskAllocation en Set-DatasourceDiskAllocation PowerShell gebruiken.

    Als u overstapt van systeemstatusbeveiliging naar BMR-beveiliging, vereist BMR-beveiliging meer ruimte op het *replicavolume.* Het volume wordt automatisch verlengd. Als u de standaardtoewijzingen van ruimte wilt wijzigen, gebruikt u de PowerShell-cmdlet Wijzigen-DiskAllocation.

* Als u overstapt van BMR-beveiliging naar systeemstatusbeveiliging, hebt u meer ruimte nodig op het volume van het herstelpunt. Backup Server kan proberen om het volume automatisch te verhogen. Als de opslaggroep niet voldoende ruimte heeft, treedt er een fout op.

    Als u overstapt van BMR-beveiliging naar systeemstatusbeveiliging, hebt u ruimte op de beveiligde computer nodig. U hebt de ruimte nodig omdat de beveiliging van de systeemstatus eerst de replica naar de lokale computer schrijft en vervolgens de replica overdraagt naar de back-upservercomputer.

## <a name="before-you-begin"></a>Voordat u begint

1. **Azure Backup Server implementeren**. Controleer of back-upserver correct is geïmplementeerd. Zie voor meer informatie:
    * [Systeemvereisten voor Azure Backup Server](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Back-upserverbeveiligingsmatrix](backup-mabs-protection-matrix.md)

1. **Opslag instellen**. U back-upgegevens opslaan op schijf, op tape en in de cloud met Azure. Zie [Gegevensopslag voorbereiden voor](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage)meer informatie .

1. **Stel de beveiligingsagent in.** Installeer de beveiligingsagent op de computer waarvan u een back-up wilt maken. Zie [De DPM-beveiligingsagent implementeren](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent)voor meer informatie.

## <a name="back-up-system-state-and-bare-metal"></a>Systeemstatus- en bare metalback-ups

Om een back-up systeem staat en bare metal:

1. Als u de wizard Nieuwe beveiligingsgroep maken wilt openen, selecteert u in de administratorconsole back-upserver de optie > **Beveiligingsgroep** > **maken.** **Protection**

1. Selecteer op de pagina **Type beveiligingsgroep** selecteren de optie **Servers**en selecteer **Volgende**.

1. Vouw op de pagina **Groepsleden selecteren** de computer uit en selecteer vervolgens **BMR** of **systeemstatus**.

    Houd er rekening mee dat u niet beschermen zowel BMR en systeemstatus voor dezelfde computer in verschillende groepen. Wanneer u BMR selecteert, wordt ook de systeemstatus automatisch ingeschakeld. Zie [Beveiligingsgroepen implementeren](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups)voor meer informatie .

1. Kies op de pagina **Methode voor gegevensbescherming** selecteren hoe u back-up suptijd op korte termijn en back-up op lange termijn moet afhandelen. 

    Back-up op korte termijn is altijd eerst op de schijf, met de optie om een back-up te maken van de schijf naar Azure met azure backup (korte of lange termijn). Een alternatief voor langdurige back-up sup naar de cloud is het instellen van een back-up op lange termijn voor een standalone tapeapparaat of tapebibliotheek die is verbonden met back-upserver.

1. Kies op de pagina **Korte termijn doelen** selecteren hoe u een back-up maken van opslag op korte termijn op schijf:
    * Kies **bij Bewaarbereik**hoe lang de gegevens op schijf moeten worden bewaard.
    * Kies **bij Synchronisatiefrequentie**hoe vaak een incrementele back-up naar de schijf moet worden uitgevoerd. Als u geen back-upinterval wilt instellen, u **net voor een herstelpunt**selecteren. Backup Server voert een volledige back-up met een uitdrukkelijke back-up uit vlak voordat elk herstelpunt is gepland.

1. Als u gegevens op tape wilt opslaan voor langdurige opslag, kiest u op de pagina **Langetermijndoelen opgeven** hoe lang tapegegevens (1 tot 99 jaar) moeten worden bijhouden.
    1. Kies **voor Frequentie van back-up,** hoe vaak back-upnaar tape moet worden uitgevoerd. De frequentie is gebaseerd op het bewaarbereik dat u hebt geselecteerd:
        * Wanneer het bewaarbereik 1 tot 99 jaar is, u dagelijks, wekelijks, tweewekelijks, maandelijks, driemaandelijks, halfjaarlijks of jaarlijks een back-up maken.
        * Wanneer het bewaarbereik 1 tot 11 maanden is, u dagelijks, wekelijks, tweewekelijks of maandelijks een back-up maken.
        * Wanneer het bewaarbereik 1 tot 4 weken is, u dagelijks of wekelijks een back-up maken.

    1. Selecteer op de pagina **Tape- en bibliotheekdetails selecteren** de tape en de bibliotheek die u wilt gebruiken. Kies ook of gegevens moeten worden gecomprimeerd en versleuteld.

1. Controleer op de pagina **Schijftoewijzing controleren** de schijfruimte voor de opslaggroep die beschikbaar is voor de beveiligingsgroep.

    * **De totale gegevensgrootte** is de grootte van de gegevens waarvan u een back-up wilt maken.
    * **Schijfruimte die moet worden ingericht op Azure Backup Server** is de ruimte die Back-upserver aanbeveelt voor de beveiligingsgroep. Backup Server gebruikt deze instellingen om het ideale back-upvolume te kiezen. U de back-upvolumeopties bewerken in **de toewijzingsgegevens van schijf**.
    * Selecteer voor workloads in het vervolgkeuzemenu de gewenste opslag. Met uw bewerkingen worden de waarden voor **Totale opslag** en **Vrije opslagruimte** in het deelvenster **Beschikbare schijfopslag** gewijzigd. Ondergebiedingsruimte is de hoeveelheid opslagruimte die back-upserver voorstelt die u aan het volume toevoegt om vloeiende back-ups te garanderen.

1. Selecteer op de pagina **Methode voor het maken van replica's** kiezen de optie voor het verwerken van de eerste replicatie met volledige gegevens. 

    Als u ervoor kiest om te repliceren via het netwerk, raden we u aan een daluren te kiezen. Voor grote hoeveelheden gegevens of voor netwerkomstandigheden die minder dan optimaal zijn, u overwegen de gegevens offline te repliceren met behulp van verwisselbare media.

1. Selecteer op de pagina **Opties voor consistentiecontrole kiezen** de optie voor het automatiseren van consistentiecontroles. 

    U ervoor kiezen om een controle alleen uit te voeren wanneer replicagegevens inconsistent worden of volgens een schema. Als u geen automatische consistentiecontrole wilt configureren, u op elk gewenst moment een handmatige controle uitvoeren. Als u een handmatige controle wilt uitvoeren, klikt u in het **gebied Beveiliging** van de beheerderconsole van de back-upserver met de rechtermuisknop op de beveiligingsgroep en selecteert u **Consistentiecontrole uitvoeren**.

1. Als u ervoor kiest om een back-up te maken naar de cloud met Azure Backup, selecteert u op de pagina **Onlinebeveiligingsgegevens opgeven** de workloads waarvan u een back-up wilt maken naar Azure.

1. Selecteer op de pagina **Online back-upplanning opgeven** hoe vaak u stapsgewijs een back-up wilt maken naar Azure. 

    U back-ups plannen die elke dag, week, maand en jaar worden uitgevoerd. U ook de tijd en datum selecteren waarop back-ups moeten worden uitgevoerd. U kunt maximaal twee keer per dag back-ups uitvoeren. Elke keer dat een back-up wordt uitgevoerd, wordt in Azure een gegevensherstelpunt gemaakt op basis van de kopie van de back-upgegevens die zijn opgeslagen op de back-upserverschijf.

1. Selecteer op de pagina **Onlineretentiebeleid opgeven** hoe de herstelpunten die worden gemaakt op basis van de dagelijkse, wekelijkse, maandelijkse en jaarlijkse back-ups in Azure worden bewaard.

1. Selecteer op de pagina **Onlinereplicatie kiezen** de optie hoe de eerste volledige replicatie van gegevens plaatsvindt. 

    U repliceren via het netwerk of offline een back-up maken (offline zaaien). Een offline back-up maakt gebruik van de functie Azure Import. Zie [Offline back-upwerkstroom in Azure Backup voor](offline-backup-azure-data-box.md)meer informatie.

1. Bekijk **op** de pagina Overzicht uw instellingen. Nadat u **Groep maken**hebt geselecteerd, vindt de eerste replicatie van de gegevens plaats. Wanneer de gegevensreplicatie is voltooid, is op de pagina **Status** de status van de beveiligingsgroep **OK**. Back-ups gebeuren dan volgens de instellingen van de beveiligingsgroep.

## <a name="recover-system-state-or-bmr"></a>Systeemstatus of BMR herstellen

U kunt BMR of systeemstatus naar een netwerklocatie herstellen. Als u een back-up van BMR hebt gemaakt, gebruikt u Windows Recovery Environment (WinRE) om uw systeem te starten en aan te sluiten op het netwerk. Gebruik vervolgens Windows Server Back-up om vanaf de netwerklocatie te herstellen. Als u een back-up van de systeemstatus hebt gemaakt, gebruikt u Windows Server Backup om te herstellen van de netwerklocatie.

### <a name="restore-bmr"></a>BMR herstellen

Ga als een back-upop de back-upserver uit:

1. Zoek in het **deelvenster Herstel** de computer die u wilt herstellen. Selecteer vervolgens **Bare Metal Recovery**.

1. Beschikbare herstelpunten worden vet weergegeven in de kalender. Selecteer de datum en tijd voor het herstelpunt dat u wilt gebruiken.

1. Selecteer op de pagina **Hersteltype selecteren** de optie **Kopiëren naar een netwerkmap**.

1. Selecteer **op de** pagina Bestemming opgeven de bestemming voor de gekopieerde gegevens. 

    Vergeet niet dat de bestemming voldoende ruimte moet hebben voor de gegevens. We raden u aan een nieuwe map voor de bestemming te maken.

1. Selecteer op de pagina **Herstelopties opgeven** de beveiligingsinstellingen. Selecteer vervolgens of u hardwaremomentopnamen (Storage Area Network) wilt gebruiken voor sneller herstel. Deze optie is alleen beschikbaar als:
    * U hebt een SAN die deze functionaliteit biedt. 
    * U een kloon maken en splitsen om deze te schrijfbaar te maken.
    * De beveiligde computer en back-upserver zijn verbonden met hetzelfde netwerk.

1. Systeemsysteem voor meldingen instellen. 
1. Selecteer **op** de pagina Bevestiging de optie **Herstellen**.

Ga als u de locatie voor delen instelt:

1. Ga op de herstellocatie naar de map met de back-up.

1. Deel de map die één niveau boven *WindowsImageBackup ligt,* zodat de hoofdmap van de gedeelde map de *map WindowsImageBackup* is. 

    Als u deze map niet deelt, wordt de back-up van Restore niet gevonden. Om verbinding te maken met WinRE, heb je een aandeel nodig dat je in WinRE openen met het juiste IP-adres en referenties.

Ga als volgt te werk om het systeem te herstellen:

1. Start de computer waarop u de afbeelding wilt herstellen met de Windows-dvd voor het systeem dat u herstelt.

1. Controleer op de eerste pagina de instellingen voor taal en landinstelling. Selecteer **uw** computer **herstellen**op de pagina Installeren .

1. Selecteer op de pagina **Systeemherstelopties** de optie **Uw computer herstellen met een systeemafbeelding die u eerder hebt gemaakt.**

1. Selecteer op de pagina **Een systeemafbeelding back-up** selecteren de optie **Een systeemafbeelding** > **geavanceerd** > **zoeken naar een systeemafbeelding in het netwerk selecteren**. Als er een waarschuwing wordt weergegeven, selecteert u **Ja**. Ga naar het deelpad, voer de referenties in en selecteer vervolgens het herstelpunt. Het systeem scant op specifieke back-ups die beschikbaar zijn in dat herstelpunt. Selecteer het herstelpunt dat u wilt gebruiken.

1. Selecteer op de **pagina Kiezen hoe u de back-uppagina herstelt,** de optie Schijven **opmaken en opnieuw verdelen**. Controleer op de volgende pagina de instellingen.

1. Als u het herstel wilt starten, selecteert u **Voltooien**. Een herstart is vereist.

### <a name="restore-system-state"></a>Systeemstatus herstellen

Herstel uitvoeren in back-upserver:

1. Zoek in het deelvenster **Herstel** de computer die u wilt herstellen en selecteer **Bare Metal Recovery**.

1. Beschikbare herstelpunten worden vet weergegeven in de kalender. Selecteer de datum en tijd voor het herstelpunt dat u wilt gebruiken.

1. Selecteer op de pagina **Hersteltype selecteren** de optie **Kopiëren naar een netwerkmap**.

1. Selecteer **op de** pagina Bestemming opgeven waar u de gegevens wilt kopiëren. 

    Vergeet niet dat de bestemming die u selecteert, voldoende ruimte voor de gegevens moet hebben. We raden u aan een nieuwe map voor de bestemming te maken.

1. Selecteer op de pagina **Herstelopties opgeven** de beveiligingsinstellingen. Selecteer vervolgens of u hardwaremomentopnamen op basis van SAN wilt gebruiken voor sneller herstel. Deze optie is alleen beschikbaar als: 
    * U hebt een SAN die deze functionaliteit biedt.
    * U een kloon maken en splitsen om deze te schrijfbaar te maken. 
    * De beveiligde computer en back-upserver server zijn verbonden met hetzelfde netwerk.

1. Systeemsysteem voor meldingen instellen. 
1. Selecteer **op** de pagina Bevestiging de optie **Herstellen**.

Windows Server Backup uitvoeren:

1. Selecteer **Acties** > **Herstellen** > **deze server** > **volgende**.

1. Selecteer **Een andere server,** selecteer de pagina **Locatietype opgeven** en selecteer vervolgens Externe gedeelde **map**. Voer het pad in naar de map die het herstelpunt bevat.

1. Selecteer **systeemstatus**op de pagina **Hersteltype selecteren** .

1. Selecteer op de pagina **Locatie selecteren voor systeemstatusherstel** de optie **Oorspronkelijke locatie**.

1. Selecteer **op** de pagina Bevestiging de optie **Herstellen**. 

1. Start de server na het herstellen opnieuw.

U de systeemstatus ook herstellen bij een opdrachtprompt: 

1. Start Windows Server Backup op de computer die u wilt herstellen. 

1. Voer bij een opdrachtprompt de versieidentifer in:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Gebruik de versie-id om de systeemstatus te herstellen. Voer in de opdrachtregel in: 

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Bevestig dat u het herstel wilt starten. U het proces zien in het venster Opdrachtprompt. Er wordt een herstellogboek gemaakt. 

1. Start de server na het herstellen opnieuw.
