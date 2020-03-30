---
title: Azure Backup Server gebruiken om back-ups te maken
description: In dit artikel vindt u informatie over hoe u uw omgeving voorbereidt op het beveiligen en back-ups maken van workloads met Behulp van Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: dd506668f9d75523ff7494bccb2979bf0785990d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273408"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Azure Backup Server installeren en upgraden

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM (SCDPM)](backup-azure-dpm-introduction.md)
>
>

> Van toepassing op: MABS v3. (MABS v2 wordt niet meer ondersteund. Als u een versie eerder gebruikt dan MABS v3, u upgraden naar de nieuwste versie.)

In dit artikel wordt uitgelegd hoe u uw omgeving voorbereidt op een back-up van workloads met Microsoft Azure Backup Server (MABS). Met Azure Backup Server u toepassingsworkloads zoals Hyper-V VM's, Microsoft SQL Server, SharePoint Server, Microsoft Exchange en Windows-clients vanaf één console beveiligen.

> [!NOTE]
> Azure Backup Server kan nu VMware VM's beschermen en biedt verbeterde beveiligingsmogelijkheden. Installeer het product zoals uitgelegd in de onderstaande secties en de nieuwste Azure Backup Agent. Zie het artikel Azure Backup Server gebruiken om een [back-up te maken van een VMware-server voor](backup-azure-backup-server-vmware.md)meer informatie over het maken van back-ups van VMware-servers met Azure Backup Server. Raadpleeg de documentatie van [Azure-back-upbeveiligingsfuncties](backup-azure-security-feature.md)voor meer informatie over beveiligingsmogelijkheden.
>
>

MABS geïmplementeerd in een Azure VM kan back-ups maken van VM's in Azure, maar ze moeten zich in hetzelfde domein bevinden om back-upbewerking in te schakelen. Het proces om een Azure VM te ondersteunen blijft hetzelfde als het back-ups van VM's on-premises, maar het implementeren van MABS in Azure heeft een aantal beperkingen. Zie [DPM als een virtuele Azure-machine voor](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) meer informatie over beperking

> [!NOTE]
> Azure heeft twee implementatiemodellen voor het maken en werken met resources: [Resource Manager en klassiek.](../azure-resource-manager/management/deployment-models.md) In dit artikel vindt u de informatie en procedures voor het herstellen van VM's die zijn geïmplementeerd met behulp van het Resource Manager-model.
>
>

Azure Backup Server neemt een groot deel van de workload back-upfunctionaliteit over van Data Protection Manager (DPM). In dit artikel wordt links naar DPM-documentatie gekoppeld om een deel van de gedeelde functionaliteit uit te leggen. Hoewel Azure Backup Server veel van dezelfde functionaliteit heeft als DPM, maakt Azure Backup Server geen back-up op tape en wordt het ook niet geïntegreerd met System Center.

## <a name="choose-an-installation-platform"></a>Kies een installatieplatform

De eerste stap om de Azure Backup Server operationeel te krijgen, is het instellen van een Windows Server. Uw server kan zich in Azure of on-premises bevinden.

### <a name="using-a-server-in-azure"></a>Een server gebruiken in Azure

Wanneer u een server kiest voor het uitvoeren van Azure Backup Server, wordt u aangeraden te beginnen met een galerijafbeelding van Windows Server 2016 Datacenter of Windows Server 2019 Datacenter. Het artikel, [Maak uw eerste Virtuele Windows-machine in de Azure-portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), biedt een zelfstudie om aan de slag te gaan met de aanbevolen virtuele machine in Azure, zelfs als u Azure nog nooit eerder hebt gebruikt. De aanbevolen minimumvereisten voor de virtuele server (VM) moeten zijn: Standard_A4_v2 met vier cores en 8 GB RAM.

Het beveiligen van workloads met Azure Backup Server heeft veel nuances. Het artikel, [Installeer DPM als een Azure virtuele machine,](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))helpt deze nuances te verklaren. Lees dit artikel volledig voordat u de machine implementeert.

### <a name="using-an-on-premises-server"></a>Een on-premises server gebruiken

Als u de basisserver niet in Azure wilt uitvoeren, u de server uitvoeren op een Hyper-V VM, een Vm vmware of een fysieke host. De aanbevolen minimumvereisten voor de serverhardware zijn twee cores en 8 GB RAM. De ondersteunde besturingssystemen worden vermeld in de volgende tabel:

| Besturingssysteem | Platform | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2016 en de nieuwste SP's |64 bits |Standard, Datacenter, Essentials  |

U de DPM-opslag dedupliceren met Windows Server Deduplicatie. Meer informatie over hoe [DPM en ontdubbeling](https://docs.microsoft.com/system-center/dpm/deduplicate-dpm-storage?view=sc-dpm-2019) samenwerken wanneer ze worden geïmplementeerd in Hyper-V VM's.

> [!NOTE]
> Azure Backup Server is ontworpen om te draaien op een speciale server voor één doel. U Azure Backup Server niet installeren op:
>
> * Een computer die wordt uitgevoerd als een domeincontroller
> * Een computer waarop de toepassingsserverfunctie is geïnstalleerd
> * Een computer die een System Center Operations Manager-beheerserver is
> * Een computer waarop Exchange Server wordt uitgevoerd
> * Een computer die een knooppunt van een cluster is
>
> Het installeren van Azure Backup Server wordt niet ondersteund op Windows Server Core of Microsoft Hyper-V Server.

Sluit u altijd aan bij Azure Backup Server in een domein. Als u van plan bent de server naar een ander domein te verplaatsen, installeert u azure backup server eerst en sluit u vervolgens aan bij de server naar het nieuwe domein. Een bestaande Azure Backup Server-machine verplaatsen naar een nieuw domein nadat de implementatie *niet wordt ondersteund.*

Of u nu back-upgegevens naar Azure verzendt of lokaal bewaart, Azure Backup Server moet zijn geregistreerd bij een vault van Recovery Services.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Opslagreplicatie instellen

U kunt met de optie voor opslagreplicatie kiezen tussen geografisch redundante opslag en lokaal redundante opslag. Standaard gebruiken Vaults van Recovery Services georedundante opslag. Als deze kluis uw primaire kluis is, laat u de opslagoptie in op georedundante opslag. Kies lokaal redundante opslag als u een goedkopere optie wilt die niet zo duurzaam is. Lees meer over [georedundante](../storage/common/storage-redundancy-grs.md) en [lokaal redundante](../storage/common/storage-redundancy-lrs.md) opslagopties in het overzicht van Azure [Storage-replicatie](../storage/common/storage-redundancy.md).

De instelling voor opslagreplicatie bewerken:

1. Klik op de blade **Recovery Services-kluizen** op de nieuwe kluis. Klik **onder** de sectie Instellingen op **Eigenschappen**.
2. Klik in **Eigenschappen**onder **Back-upconfiguratie**op **Bijwerken**.

3. Selecteer het type opslagreplicatie en klik op **Opslaan**.

     ![De opslagconfiguratie voor nieuwe kluis instellen](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Softwarepakket

### <a name="downloading-the-software-package"></a>Het softwarepakket downloaden

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Als u al een kluis van Recovery Services hebt geopend, gaat u verder met stap 3. Als u geen vault van Recovery Services hebt geopend, maar zich in de Azure-portal bevindt, klikt u in het hoofdmenu op **Bladeren**.

   * Typ in de lijst met resources **Recovery Services**.
   * Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Wanneer **Recovery Services-kluizen** wordt weergegeven, klikt u erop.

     ![Een Recovery Services-kluis maken, stap 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     De lijst met Recovery Services-kluizen wordt weergegeven.
   * Selecteer in de lijst met Recovery Services-kluizen een kluis.

     Het geselecteerde kluisdashboard wordt geopend.

     ![Blade Kluis openen](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Het **mes Instellingen** wordt standaard geopend. Als deze is gesloten, klikt u op **Instellingen** om het pagina-blad instellingen te openen.

    ![Blade Kluis openen](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Klik **op Back-up** om de wizard Aan de slag te openen.

    ![Back-up aan de slag](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    In **het back-upblad** dat wordt geopend, worden **back-updoelen** automatisch geselecteerd.

    ![Back-up-doelen-standaard geopend](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Selecteer in het blade **Back-updoel** in het menu **Waar is uw werkbelasting actief,** de optie **On-premises**.

    ![on-premises en workloads als doelen](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Selecteer in het vervolgkeuzemenu **Wat wilt u een back-up maken?** **OK**

    Met de wizard **Aan de slag met back-upschakelt** u de optie Infrastructuur **voorbereiden** om back-ups van workloads naar Azure te maken.

   > [!NOTE]
   > Als u alleen een back-up wilt maken van bestanden en mappen, raden we u aan de Azure Backup-agent te gebruiken en de richtlijnen in het artikel te volgen, [Eerste blik: back-upvan bestanden en mappen](backup-try-azure-backup-in-10-mins.md). Als u meer wilt beschermen dan bestanden en mappen, of als u van plan bent om de beveiligingsbehoeften in de toekomst uit te breiden, selecteert u deze workloads.
   >
   >

    ![Wizard Wijziging van de wizard Aan de slag](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Klik **in** het beheer van de infrastructuur voorbereiden dat wordt geopend op de **koppelingen downloaden** voor Azure Backup Server installeren en Vault-referenties downloaden. U gebruikt de vault credentials tijdens de registratie van Azure Backup Server in de kluis van herstelservices. De links brengen u naar het Downloadcenter waar het softwarepakket kan worden gedownload.

    ![Infrastructuur voorbereiden voor Azure Backup Server](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Selecteer alle bestanden en klik op **Volgende**. Download alle bestanden die binnenkomen van de downloadpagina van Microsoft Azure Backup en plaats alle bestanden in dezelfde map.

    ![Downloadcentrum 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Aangezien de downloadgrootte van alle bestanden samen > 3G is, kan het op een downloadlink van 10 Mbps tot 60 minuten duren voordat de download is voltooid.

### <a name="extracting-the-software-package"></a>Het softwarepakket extraheren

Nadat u alle bestanden hebt gedownload, klikt u op **MicrosoftAzureBackupInstaller.exe**. Hiermee wordt de **wizard Microsoft Azure Backup Setup gestart** om de installatiebestanden te extraheren naar een door u opgegeven locatie. Ga door de wizard en klik op de knop **Uitpakken** om het extractieproces te starten.

> [!WARNING]
> Ten minste 4 GB vrije ruimte is vereist om de installatiebestanden te extraheren.
>
>

![Microsoft Azure Backup Setup Wizard](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Zodra het extractieproces is voltooid, schakelt u het selectievakje in om de vers geëxtraheerde *setup.exe* te starten om te beginnen met het installeren van Microsoft Azure Backup Server en op de knop **Voltooien** te klikken.

### <a name="installing-the-software-package"></a>Het softwarepakket installeren

1. Klik op **Microsoft Azure Backup** om de wizard Setup te starten.

    ![Microsoft Azure Backup Setup Wizard](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Klik op het welkomstscherm op de knop **Volgende.** Dit brengt u naar de sectie *Vereiste controles.* Klik op dit scherm op **Controleren** om te bepalen of aan de vereisten voor hardware en software voor Azure Backup Server is voldaan. Als aan alle voorwaarden is voldaan, ziet u een bericht dat de machine aan de vereisten voldoet. Klik op de knop **Volgende.**

    ![Azure Backup Server - Controle op welkomst- en vereisten](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Het installatiepakket azure backup server wordt geleverd met de benodigde SQL Server-binaire bestanden. Wanneer u een nieuwe Azure Backup Server-installatie start, kiest u de optie **Nieuwe instantie van SQL Server installeren met deze instelling** en klikt u op de knop Controleren en **installeren.** Zodra de vereisten zijn geïnstalleerd, klikt u op **Volgende**.

    >[!NOTE]
    >Als u uw eigen SQL-server wilt gebruiken, zijn de ondersteunde SQL Server-versies SQL Server 2014 SP1 of hoger, 2016 en 2017.  Alle SQL Server-versies moeten standaard of Enterprise 64-bits zijn.
    >Azure Backup Server werkt niet met een extern SQL Server-exemplaar. De instantie die wordt gebruikt door Azure Backup Server moet lokaal zijn. Als u een bestaande SQL-server voor MABS gebruikt, ondersteunt de MABS-installatie alleen het gebruik van *benoemde exemplaren* van SQL-server.

    ![Azure Backup Server - SQL-controle](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Als er een fout optreedt met een aanbeveling om de machine opnieuw op te starten, doe dit en klik je op **Opnieuw controleren**. Als er SQL-configuratieproblemen zijn, configureert u SQL opnieuw volgens de SQL-richtlijnen en probeert u MABS opnieuw te installeren/upgraden met behulp van de bestaande instantie van SQL.

   **Handmatige configuratie**

   Wanneer u uw eigen exemplaar sql gebruikt, moet u ervoor zorgen dat u ingebouwde\administrators toevoegt aan de sysadmin-rol om DB onder de knie te krijgen.

    **SSRS-configuratie met SQL 2017**

    Wanneer u uw eigen exemplaar van SQL 2017 gebruikt, moet u SSRS handmatig configureren. Controleer na de SSRS-configuratie of de eigenschap *IsInitialized* van SSRS is ingesteld op *True.* Wanneer dit is ingesteld op True, gaat MABS ervan uit dat SSRS al is geconfigureerd en slaat het de SSRS-configuratie over.

    Gebruik de volgende waarden voor SSRS-configuratie:
    * Serviceaccount: 'Gebruik ingebouwd account' moet Netwerkservice zijn
    * Web Service URL: 'Virtual Directory' moet worden ReportServer_\<SQLInstanceName>
    * Database: DatabaseName moet ReportServer$\<SQLInstanceName>
    * WebPortal URL: 'Virtual Directory'\<moet worden Reports_ SQLInstanceName>

    [Meer informatie](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) over ssrs-configuratie.

    > [!NOTE]
    > Licenties voor SQL Server die worden gebruikt als de database voor MABS, vallen onder [de Microsoft Online Services Terms](https://www.microsoft.com/licensing/product-licensing/products) (OST). Volgens OST kan SQL Server, gebundeld met MABS, alleen worden gebruikt als database voor MABS.

4. Geef een locatie op voor de installatie van Microsoft Azure Backup-serverbestanden en klik op **Volgende**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    De kraslocatie is een vereiste voor een back-up van Azure. Zorg ervoor dat de kraslocatie ten minste 5% van de geplande gegevens is die zijn gepland om een back-up naar de cloud te maken. Voor schijfbeveiliging moeten afzonderlijke schijven worden geconfigureerd zodra de installatie is voltooid. Zie [Opslagpools en schijfopslag configureren](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12))voor meer informatie over opslaggroepen.
5. Geef een sterk wachtwoord op voor beperkte lokale gebruikersaccounts en klik op **Volgende**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selecteer of u *Microsoft Update* wilt gebruiken om te controleren op updates en klik op **Volgende**.

   > [!NOTE]
   > We raden u aan windows update door te verwijzen naar Microsoft Update, dat beveiliging en belangrijke updates biedt voor Windows en andere producten zoals Microsoft Azure Backup Server.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Bekijk het *overzicht van instellingen* en klik op **Installeren**.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. De installatie gebeurt gefaseerd. In de eerste fase wordt de Microsoft Azure Recovery Services Agent op de server geïnstalleerd. De wizard controleert ook op internetverbinding. Als er een internetverbinding beschikbaar is, u doorgaan met de installatie, zo niet, dan moet u proxygegevens opgeven om verbinding te maken met internet.

    De volgende stap is het configureren van de Microsoft Azure Recovery Services Agent. Als onderdeel van de configuratie moet u uw kluisreferenties verstrekken om de machine te registreren bij de kluis van de herstelservices. U verstrekt ook een wachtwoordzin om de gegevens die tussen Azure en uw lokalen worden verzonden te versleutelen/decoderen. U automatisch een wachtwoordzin genereren of uw eigen wachtwoordzin van minimaal 16 tekens geven. Ga verder met de wizard totdat de agent is geconfigureerd.

    ![Azure Backup Server PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Zodra de registratie van de Microsoft Azure Backup-server is voltooid, gaat de wizard Algehele installatie over tot de installatie en configuratie van SQL Server en de Azure Backup Server-componenten. Zodra de installatie van de SQL Server-component is voltooid, worden de Azure Backup Server-componenten geïnstalleerd.

    ![Azure Backup-server](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Wanneer de installatiestap is voltooid, zijn ook de bureaubladpictogrammen van het product gemaakt. Dubbelklik op het pictogram om het product te starten.

### <a name="add-backup-storage"></a>Back-upopslag toevoegen

De eerste back-up wordt bewaard op opslag die is gekoppeld aan de Azure Backup Server-machine. Zie [Opslaggroepen en schijfopslag configureren](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage)voor meer informatie over het toevoegen van schijven.

> [!NOTE]
> U moet back-upopslag toevoegen, zelfs als u van plan bent gegevens naar Azure te verzenden. In de huidige architectuur van Azure Backup Server bevat de azure backup-kluis de *tweede* kopie van de gegevens, terwijl de lokale opslag de eerste (en verplichte) back-upkopie bevat.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>De beveiligingsagent voor gegevensbeschermingsbeheer installeren en bijwerken

MABS maakt gebruik van de system center data protection manager-beveiligingsagent. [Dit zijn de stappen om](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) de beveiligingsagent op uw beveiligingsservers te installeren.

In de volgende secties wordt beschreven hoe beveiligingsagents voor clientcomputers kunnen worden bijgewerkt.

1. Selecteer **beheeragents** > in de administratorconsole van back-upserver .**Agents**

2. Selecteer in het weergavevenster de clientcomputers waarvoor u de beveiligingsagent wilt bijwerken.

   > [!NOTE]
   > De kolom **Agent Updates** geeft aan wanneer een update van een beveiligingsagent beschikbaar is voor elke beveiligde computer. In het deelvenster **Handelingen** is de actie **Bijwerken** alleen beschikbaar wanneer een beveiligde computer is geselecteerd en er updates beschikbaar zijn.
   >
   >

3. Als u bijgewerkte beveiligingsagents op de geselecteerde computers wilt installeren, selecteert u in het deelvenster **Handelingen** de optie **Bijwerken**.

4. Voor een clientcomputer die niet is verbonden met het netwerk, wordt in de kolom **Agentstatus** de status **van Update in behandeling**weergegeven totdat de computer is verbonden met het netwerk.

   Nadat een clientcomputer is verbonden met het netwerk, wordt in de kolom **Agent updates** voor de clientcomputer de status **Bijwerken weergegeven.**

## <a name="move-mabs-to-a-new-server"></a>MABS naar een nieuwe server verplaatsen

Dit zijn de stappen als u MABS naar een nieuwe server moet verplaatsen, met behoud van de opslag. Dit kan alleen worden gedaan als alle gegevens op moderne back-upopslag staan.

  > [!IMPORTANT]
  >
  > * De nieuwe servernaam moet dezelfde naam hebben als het oorspronkelijke Exemplaar van Azure Backup Server. U de naam van het nieuwe Azure Backup Server-exemplaar niet wijzigen als u de vorige opslaggroep en MABS-database (DPMDB) wilt gebruiken om herstelpunten te behouden.
  > * U moet een back-up hebben van de MABS Database (DPMDB). U moet de database herstellen.

1. Selecteer in het weergavevenster de clientcomputers waarvoor u de beveiligingsagent wilt bijwerken.
2. Sluit de oorspronkelijke Azure-back-upserver af of haal deze van de draad.
3. Het machineaccount opnieuw instellen in active directory.
4. Installeer Server 2016 op een nieuwe machine en noem deze dezelfde machinenaam als de oorspronkelijke Azure Backup-server.
5. Lid worden van het domein
6. Azure Backup server V3 of hoger installeren (MABS Storage Pool disks van oude server verplaatsen en importeren)
7. Herstel de DPMDB genomen in stap 1.
8. Voeg de opslag van de oorspronkelijke back-upserver toe aan de nieuwe server.
9. Van SQL De DPMDB herstellen
10. Van beheeropdrachtregel op nieuwe server-cd tot Microsoft Azure Backup-installatielocatie en opslaglocatiemap

    Voorbeeld van pad: C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Naar Azure-back-up, DPMSYNC -SYNC uitvoeren

    Als u nieuwe schijven hebt toegevoegd aan de dpm-opslaggroep in plaats van de oude schijven te verplaatsen, voert u DPMSYNC -Reallocatereplica uit

## <a name="network-connectivity"></a>Netwerkconnectiviteit

Azure Backup Server vereist connectiviteit met de Azure Backup-service om het product succesvol te laten werken. Als u wilt valideren of de machine ```Get-DPMCloudConnection``` de verbinding met Azure heeft, gebruikt u de cmdlet in de PowerShell-console van Azure Backup Server. Als de uitvoer van de cmdlet WAAR is, bestaat er connectiviteit, anders is er geen verbinding.

Tegelijkertijd moet het Azure-abonnement in een gezonde staat zijn. Meld u aan bij de [abonnementsportal](https://account.windowsazure.com/Subscriptions)om de status van uw abonnement te achterhalen en te beheren.

Zodra u de status van de Azure-connectiviteit en van het Azure-abonnement weet, u de onderstaande tabel gebruiken om de impact op de aangeboden back-up-/herstelfunctionaliteit te achterhalen.

| Verbindingsstatus | Azure-abonnement | Back-up naar Azure | Back-ups maken naar schijf | Herstellen vanuit Azure | Herstellen uit een schijf |
| --- | --- | --- | --- | --- | --- |
| Verbonden |Actief |Toegestaan |Toegestaan |Toegestaan |Toegestaan |
| Verbonden |Verlopen |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbonden |Gedeprovisioneerd |Gestopt |Gestopt |Gestopt e-trainingspunten en Azure-herstelpunten verwijderd |Gestopt |
| Verloren connectiviteit > 15 dagen |Actief |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verloren connectiviteit > 15 dagen |Verlopen |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verloren connectiviteit > 15 dagen |Gedeprovisioneerd |Gestopt |Gestopt |Gestopt e-trainingspunten en Azure-herstelpunten verwijderd |Gestopt |

### <a name="recovering-from-loss-of-connectivity"></a>Herstellen van verlies van connectiviteit

Als u een firewall of een proxy hebt die de toegang tot Azure verhindert, moet u de volgende domeinadressen in het firewall/proxyprofiel toestaan:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Als u ExpressRoute Microsoft-peering gebruikt, selecteert u de volgende services/regio's:

* Azure Active Directory (12076:5060)
* Microsoft Azure-regio (volgens de locatie van uw vault van Herstelservices)
* Azure Storage (volgens de locatie van uw vault recovery services)

Ga voor meer informatie naar [de routevereisten van ExpressRoute.](https://docs.microsoft.com/azure/expressroute/expressroute-routing)

Zodra de verbinding met Azure is hersteld met de Azure Backup Server-machine, worden de bewerkingen die kunnen worden uitgevoerd bepaald door de azure-abonnementsstatus. De bovenstaande tabel bevat details over de toegestane bewerkingen zodra de machine is "Verbonden".

### <a name="handling-subscription-states"></a>Abonnementsstatus behandeling

Het is mogelijk om een Azure-abonnement van een *verlopen* of *gedeprovisioneerde* status naar de *status Actief* te nemen. Dit heeft echter enkele gevolgen voor het productgedrag, terwijl de status niet *actief*is:

* Een *gedeprovisioneerd* abonnement verliest functionaliteit voor de periode waarin het is gedeprovisioneerd. Bij het inschakelen van *Active*wordt de productfunctionaliteit van back-up/restore nieuw leven ingeblazen. De back-upgegevens op de lokale schijf kunnen ook worden opgehaald als deze met een voldoende grote bewaarperiode zijn bewaard. De back-upgegevens in Azure gaan echter onherroepelijk verloren zodra het abonnement de status *Deprovisioned* invoert.
* Een *verlopen* abonnement verliest alleen functionaliteit voor totdat het opnieuw *Actief* is gemaakt. Back-ups die zijn gepland voor de periode dat het abonnement is *verlopen,* worden niet uitgevoerd.

## <a name="upgrade-mabs"></a>MABS upgraden

Gebruik de volgende procedures om MABS te upgraden.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Upgrade van MABS V2 naar V3

> [!NOTE]
>
> MABS V2 is geen voorwaarde voor het installeren van MABS V3. U echter alleen upgraden naar MABS V3 vanaf MABS V2.

Gebruik de volgende stappen om MABS te upgraden:

1. Als u wilt upgraden van MABS V2 naar MABS V3, u uw besturingssysteem upgraden naar Windows Server 2016 of Windows Server 2019 indien nodig.

2. Upgrade uw server. De stappen zijn vergelijkbaar met [de installatie](#install-and-upgrade-azure-backup-server). Voor SQL-instellingen krijgt u echter een optie om uw SQL-exemplaar te upgraden naar SQL 2017 of om uw eigen exemplaar van SQL-server 2017 te gebruiken.

   > [!NOTE]
   >
   > Sluit niet af terwijl uw SQL-instantie wordt geüpgraded, als u de SQL-rapportage-instantie verwijdert en een poging om MABS opnieuw te upgraden, mislukt.

   > [!IMPORTANT]
   >
   >  Als onderdeel van de SQL 2017-upgrade maken we een back-up van de SQL-versleutelingssleutels en verwijderen we de rapportageservices. Na de upgrade van de SQL-server wordt de rapportageservice(14.0.6827.4788) geïnstalleerd & versleutelingssleutels worden hersteld.
   >
   > Raadpleeg bij het handmatig configureren van SQL 2017 de *SSRS-configuratie met sql 2017-sectie* onder Installatie-instructies.

3. Werk de beveiligingsagents op de beveiligde servers bij.
4. Back-ups moeten worden voortgezet zonder dat u uw productieservers opnieuw hoeft op te starten.
5. U nu beginnen met het beschermen van uw gegevens. Als u een upgrade uitvoert naar moderne back-upopslag, terwijl u beschermt, u ook de volumes kiezen die u wilt opslaan in de back-ups en controleren op onder de ingerichte ruimte. [Meer informatie](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Problemen oplossen

Als microsoft Azure Backup-server tijdens de installatiefase (of back-up of herstel) niet met fouten optreedt, raadpleegt u dit [document met foutcodes](https://support.microsoft.com/kb/3041338) voor meer informatie.
U ook verwijzen naar [veelgestelde vragen](backup-azure-backup-faq.md) met Azure Backup

## <a name="next-steps"></a>Volgende stappen

U hier gedetailleerde informatie krijgen over [de voorbereiding van uw omgeving voor DPM.](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-2019) Het bevat ook informatie over ondersteunde configuraties waarop Azure Backup Server kan worden geïmplementeerd en gebruikt. U een reeks [PowerShell-cmdlet](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) gebruiken voor het uitvoeren van verschillende bewerkingen.

U deze artikelen gebruiken om een beter inzicht te krijgen in de beveiliging van workloads met behulp van microsoft Azure Backup-server.

* [SQL Server-back-up](backup-azure-backup-sql.md)
* [Back-up van sharePoint-server](backup-azure-backup-sharepoint.md)
* [Alternatieve serverback-up](backup-azure-alternate-dpm-server.md)
