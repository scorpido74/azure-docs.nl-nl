---
title: Azure Backup Server installeren op Azure Stack
description: In dit artikel vindt u informatie over het gebruik van Azure Backup Server om workloads in Azure Stack te beveiligen of een back-up te maken.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: b78e5a662bdcf23ad38cb33292658d4d2455e579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583432"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Azure Backup Server installeren op Azure Stack

In dit artikel wordt uitgelegd hoe u Azure Backup Server installeert op Azure Stack. Met Azure Backup Server u Infrastructure as a Service-workloads (IaaS) beveiligen, zoals virtuele machines die in Azure Stack worden uitgevoerd. Een voordeel van het gebruik van Azure Backup Server om uw workloads te beschermen, is dat u alle workloadbescherming vanaf één console beheren.

> [!NOTE]
> Raadpleeg azure [backup-beveiligingsfuncties documentatie](backup-azure-security-feature.md)voor meer informatie over beveiligingsmogelijkheden.
>

## <a name="azure-backup-server-protection-matrix"></a>Beveiligingsmatrix voor Azure Backup Server

Azure Backup Server beschermt de volgende Azure Stack-workloads voor virtuele machines.

| Beveiligde gegevensbron | Beveiliging en herstel |
| --------------------- | ----------------------- |
| Windows Server Semi-jaarlijks kanaal - Datacenter/Enterprise/Standard | Volumes, bestanden, mappen |
| Windows Server 2016 - Datacenter/Enterprise/Standard | Volumes, bestanden, mappen |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | Volumes, bestanden, mappen |
| Windows Server 2012 - Datacenter/Enterprise/Standard | Volumes, bestanden, mappen |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | Volumes, bestanden, mappen |
| SQL Server 2016 | Database |
| SQL Server 2014 | Database |
| SQL Server 2012 SP1 | Database |
| SharePoint 2016 | Farm, database, frontend, webserver |
| SharePoint 2013 | Farm, database, frontend, webserver |
| SharePoint 2010 | Farm, database, frontend, webserver |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Vereisten voor de Azure Backup Server-omgeving

Houd rekening met de aanbevelingen in deze sectie bij het installeren van Azure Backup Server in uw Azure Stack-omgeving. Het installatieprogramma Azure Backup Server controleert of uw omgeving de vereiste vereisten heeft, maar u bespaart tijd door u voor te bereiden voordat u installeert.

### <a name="determining-size-of-virtual-machine"></a>De grootte van virtuele machine bepalen

Als u Azure Backup Server wilt uitvoeren op een virtuele Azure Stack-machine, gebruikt u grootte A2 of groter. Download de [azure stack VM-formaatcalculator](https://www.microsoft.com/download/details.aspx?id=56832)voor hulp bij het kiezen van een virtuele machinegrootte.

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuele netwerken op virtuele azure stack-machines

Alle virtuele machines die worden gebruikt in een Azure Stack-werkbelasting moeten deel uitmaken van hetzelfde Azure-virtuele netwerk en Azure-abonnement.

### <a name="azure-backup-server-vm-performance"></a>Azure Backup Server VM-prestaties

Als deze worden gedeeld met andere virtuele machines, hebben de grootte van het opslagaccount en de IOPS-limiet invloed op de VM-prestaties van Azure Backup Server. Daarom moet u een apart opslagaccount gebruiken voor de virtuele azure backup server. De Azure Backup-agent die wordt uitgevoerd op de Azure Backup Server heeft tijdelijke opslag nodig voor:

- eigen gebruik (een cachelocatie),
- gegevens die vanuit de cloud zijn hersteld (lokaal faseringsgebied)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Azure Backup tijdelijke schijfopslag configureren

Elke Azure Stack virtuele machine wordt geleverd met tijdelijke schijfopslag, die beschikbaar is voor de gebruiker als volume. `D:\` De lokale faseringsruimte die nodig is voor `D:\`Azure Backup kan worden `C:\`geconfigureerd om zich in te verblijven en de cachelocatie kan worden geplaatst op . Op deze manier hoeft er geen opslag te worden uitgesneden van de gegevensschijven die zijn gekoppeld aan de virtuele machine van Azure Backup Server.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Back-upgegevens opslaan op lokale schijf en in Azure

Azure Backup Server slaat back-upgegevens op Azure-schijven op die zijn gekoppeld aan de virtuele machine, voor operationeel herstel. Zodra de schijven en opslagruimte aan de virtuele machine zijn gekoppeld, beheert Azure Backup Server de opslag voor u. De hoeveelheid back-upgegevensopslag is afhankelijk van het aantal en de grootte van de schijven die zijn gekoppeld aan elke [virtuele Azure Stack-machine.](/azure-stack/user/azure-stack-storage-overview) Elke grootte van Azure Stack VM heeft een maximum aantal schijven die kunnen worden gekoppeld aan de virtuele machine. A2 is bijvoorbeeld vier schijven. A3 is acht schijven. A4 is 16 schijven. Nogmaals, de grootte en het aantal schijven bepaalt de totale back-upopslaggroep.

> [!IMPORTANT]
> U mag **niet** langer dan vijf dagen operationele herstelgegevens (back-upgegevens) bewaren op schijven die aan azure backup server zijn gekoppeld.
>

Het opslaan van back-upgegevens in Azure vermindert de back-upinfrastructuur op Azure Stack. Als gegevens meer dan vijf dagen oud zijn, moet deze worden opgeslagen in Azure.

Als u back-upgegevens wilt opslaan in Azure, maakt of gebruikt u een kluis van Recovery Services. Wanneer u een back-up van de Azure Backup Server-workload maakt, [configureert](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault)u de kluis Recovery Services. Eenmaal geconfigureerd, elke keer dat een back-up taak wordt uitgevoerd, een herstelpunt wordt gemaakt in de kluis. Elke vault van Recovery Services biedt plaats aan 9999 herstelpunten. Afhankelijk van het aantal herstelpunten dat is gemaakt en hoe lang ze worden bewaard, u back-upgegevens jarenlang bewaren. U bijvoorbeeld maandelijkse herstelpunten maken en deze vijf jaar behouden.

### <a name="scaling-deployment"></a>Implementatie schalen

Als u uw implementatie wilt schalen, hebt u de volgende opties:

- Opschalen - Verhoog de grootte van de virtuele azure backupserver van de A-serie naar de D-serie en verhoog de lokale opslag [volgens de instructies voor de virtuele machine van Azure Stack.](/azure-stack/user/azure-stack-manage-vm-disks)
- Gegevens uitladen : stuur oudere gegevens naar Azure en bewaar alleen de nieuwste gegevens over de opslag die is gekoppeld aan de Azure Backup Server.
- Uitschalen - Voeg meer Azure Backup Servers toe om de workloads te beschermen.

### <a name="net-framework"></a>.NET Framework

.NET Framework 3.5 SP1 of hoger moet op de virtuele machine worden geïnstalleerd.

### <a name="joining-a-domain"></a>Lid worden van een domein

De virtuele machine Azure Backup Server moet worden samengevoegd met een domein. Een domeingebruiker met beheerdersbevoegdheden moet Azure Backup Server installeren op de virtuele machine.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Een IaaS-vm gebruiken in Azure Stack

Wanneer u een server voor Azure Backup Server kiest, begint u met een Windows Server 2012 R2-datacenter of een datacentergalerieafbeelding van Windows Server 2016. Het artikel, [Maak uw eerste Virtuele Windows-machine in de Azure-portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), biedt een zelfstudie om aan de slag te gaan met de aanbevolen virtuele machine. De aanbevolen minimumvereisten voor de virtuele server (VM) moeten zijn: A2 Standard met twee cores en 3,5 GB RAM.

Het beveiligen van workloads met Azure Backup Server heeft veel nuances. Het artikel, [Installeer DPM als een Azure virtuele machine,](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))helpt deze nuances te verklaren. Lees dit artikel volledig voordat u de machine implementeert.

> [!NOTE]
> Azure Backup Server is ontworpen om te draaien op een speciale virtuele machine voor één voor de andere doeleinden. U Azure Backup Server niet installeren op:
>
> - Een computer die wordt uitgevoerd als een domeincontroller
> - Een computer waarop de toepassingsserverfunctie is geïnstalleerd
> - Een computer waarop Exchange Server wordt uitgevoerd
> - Een computer die een knooppunt van een cluster is

Sluit u altijd aan bij Azure Backup Server in een domein. Als u Azure Backup Server naar een ander domein moet verplaatsen, installeert u eerst Azure Backup Server en vervolgens naar het nieuwe domein. Zodra u Azure Backup Server implementeert, u deze niet verplaatsen naar een nieuw domein.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Opslagreplicatie instellen

Met de optie voor opslagreplicatie van Recovery Services u kiezen tussen georedundante opslag en lokaal redundante opslag. Standaard gebruiken Vaults van Recovery Services georedundante opslag. Als deze kluis uw primaire kluis is, laat u de opslagoptie in op georedundante opslag. Kies lokaal redundante opslag als u een goedkopere optie wilt die minder duurzaam is. Lees meer over [georedundante](../storage/common/storage-redundancy-grs.md) en [lokaal redundante](../storage/common/storage-redundancy-lrs.md) opslagopties in het overzicht van Azure [Storage-replicatie](../storage/common/storage-redundancy.md).

De instelling voor opslagreplicatie bewerken:

1. Selecteer uw kluis om het kluisdashboard en het menu Instellingen te openen. Als het menu **Instellingen** niet wordt geopend, klikt u op **Alle instellingen** in het kluisdashboard.
2. Klik **in** het menu Instellingen op **Back-upconfiguratie van back-ups** > **Backup Configuration** om het menu **Back-upconfiguratie** te openen. Kies in het menu **Back-upconfiguratie** de optie opslagreplicatie voor uw kluis.

    ![Lijst met back-upkluizen](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Installatieprogramma Azure Backup Server downloaden

Er zijn twee manieren om het installatieprogramma azure backup server te downloaden. U het installatieprogramma Azure Backup Server downloaden vanuit het [Microsoft Downloadcentrum.](https://www.microsoft.com/download/details.aspx?id=55269) U ook het installatieprogramma azure backup server downloaden terwijl u een vault voor Herstelservices configureert. De volgende stappen doorlopen u door het downloaden van het installatieprogramma van de Azure-portal tijdens het configureren van een Vault recovery Services.

1. Meld u vanaf uw virtuele Azure Stack [aan bij uw Azure-abonnement in de Azure-portal.](https://portal.azure.com/)
2. Selecteer **Alle services**in het linkermenu .

    ![De optie Alle services kiezen in het hoofdmenu](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. Typ *Herstelservices*in het dialoogvenster **Alle services** . Terwijl u begint met typen, filtert uw invoer de lijst met bronnen. Zodra u het ziet, selecteert u **Vaults van Recovery Services**.

    ![Herstelservices typen in het dialoogvenster Alle services](./media/backup-mabs-install-azure-stack/all-services.png)

    De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

4. Selecteer in de lijst met vaults van Recovery Services uw kluis om het dashboard te openen.

    ![Herstelservices typen in het dialoogvenster Alle services](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. Klik in het menu Aan de slag in de kluis op **Back-up** om de wizard Aan de slag te openen.

    ![Back-up aan de slag](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Het back-upmenu wordt geopend.

    ![Back-up-doelen-standaard geopend](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. Selecteer in het back-upmenu in het menu **Waar is uw werkbelasting actief** **.** Selecteer in het vervolgkeuzemenu **Wat wilt u een back-up maken?** Als u niet zeker weet welke workloads u wilt selecteren, kiest u **Hyper-V Virtuele Machines** en klikt u op **Infrastructuur voorbereiden**.

    ![on-premises en workloads als doelen](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Het menu **Infrastructuur voorbereiden** wordt geopend.

7. Klik **in** het menu Infrastructuur voorbereiden op **Downloaden** om een webpagina te openen om installatiebestanden van Azure Backup Server te downloaden.

    ![Wizard Wijziging van de wizard Aan de slag](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    De Microsoft-webpagina met de downloadbare bestanden voor Azure Backup Server wordt geopend.

8. Selecteer op de downloadpagina van Microsoft Azure Backup Server een taal en klik op **Downloaden**.

    ![Downloadcentrum wordt geopend](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Het installatieprogramma Azure Backup Server bestaat uit acht bestanden: een installatieprogramma en zeven .bin-bestanden. Controleer **Bestandsnaam** om alle vereiste bestanden te selecteren en klik op **Volgende**. Download alle bestanden naar dezelfde map.

    ![Downloadcentrum 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    De downloadgrootte van alle installatiebestanden is groter dan 3 GB. Op een downloadlink van 10 Mbps kan het downloaden van alle installatiebestanden tot 60 minuten duren. De bestanden worden gedownload naar uw opgegeven downloadlocatie.

## <a name="extract-azure-backup-server-install-files"></a>Installatiebestanden van Azure Backup Server extraheren

Nadat u alle bestanden naar uw virtuele Azure Stack-machine hebt gedownload, gaat u naar de downloadlocatie. De eerste fase van het installeren van Azure Backup Server is het extraheren van de bestanden.

![Downloadcentrum 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Als u de installatie wilt starten, klikt u in de lijst met gedownloade bestanden op **MicrosoftAzureBackupserverInstaller.exe**.

    > [!WARNING]
    > Ten minste 4 GB vrije ruimte is vereist om de installatiebestanden te extraheren.
    >

2. Klik in de wizard Azure Backup Server op **Volgende** om door te gaan.

    ![Microsoft Azure Backup Setup Wizard](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Kies het pad voor de Azure Backup Server-bestanden en klik op **Volgende**.

   ![Microsoft Azure Backup Setup Wizard](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Controleer de extractielocatie en klik op **Uitpakken.**

   ![Microsoft Azure Backup Setup Wizard](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. De wizard haalt de bestanden uit en leest het installatieproces uit.

   ![Microsoft Azure Backup Setup Wizard](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Zodra het extractieproces is voltooid, klikt u op **Voltooien**. Standaard is **Execute setup.exe** geselecteerd. Wanneer u op **Voltooien**klikt, installeert Setup.exe Microsoft Azure Backup Server op de opgegeven locatie.

   ![Microsoft Azure Backup Setup Wizard](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Het softwarepakket installeren

In de vorige stap klikte u op **Voltooien** om de extractiefase te beëindigen en start u de wizard Azure Backup Server setup.

![Microsoft Azure Backup Setup Wizard](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server deelt code met Gegevensbeschermingsbeheer. U ziet verwijzingen naar Gegevensbeschermingsbeheer en DPM in het installatieprogramma Azure Backup Server. Hoewel Azure Backup Server en Data Protection Manager afzonderlijke producten zijn, zijn deze producten nauw met elkaar verbonden.

1. Als u de wizard Setup wilt starten, klikt u op **Microsoft Azure Backup Server**.

   ![Microsoft Azure Backup Setup Wizard](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Klik in het scherm **Welkom** op **Volgende**.

    ![Azure Backup Server - Controle op welkomst- en vereisten](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Klik **in** het scherm Vereiste controle op **Controleren** om te bepalen of aan de vereisten voor hardware en software voor Azure Backup Server is voldaan.

    ![Azure Backup Server - Controle op welkomst- en vereisten](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Als uw omgeving de vereiste vereisten heeft, ziet u een bericht dat de machine aan de vereisten voldoet. Klik op **Volgende**.  

    ![Azure Backup Server - Vereiste voorwaarden controleren geslaagd](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Als uw omgeving niet aan de vereiste voorwaarden voldoet, worden de problemen gespecificeerd. De voorwaarden die niet zijn voldaan, worden ook vermeld in dpmsetup.log. Los de vereistefouten op en voer **Opnieuw controleren**uit. De installatie kan pas doorgaan als aan alle voorwaarden is voldaan.

    ![Azure Backup Server - installatievereisten niet voldaan](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup Server vereist SQL Server. Het installatiepakket azure backup server wordt geleverd met de juiste SQL Server-binaire bestanden. Als u uw eigen SQL-installatie wilt gebruiken, u dat doen. De aanbevolen keuze is echter dat het installatieprogramma een nieuw exemplaar van SQL Server toevoegen. Als u wilt weten of uw keuze werkt met uw omgeving, klikt u op **Controleren en installeren.**

   > [!NOTE]
   > Azure Backup Server werkt niet met een extern SQL Server-exemplaar. De instantie die door Azure Backup Server wordt gebruikt, moet lokaal zijn.
   >

    ![Azure Backup Server - Controle op welkomst- en vereisten](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Als de virtuele machine de vereiste vereisten heeft om Azure Backup Server te installeren, klikt u na controle op **Volgende**.

    ![Azure Backup Server - Controle op welkomst- en vereisten](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Als er een storing optreedt met een aanbeveling om de machine opnieuw op te starten, start u de machine opnieuw op. Nadat u de machine opnieuw hebt opgestart, start u het installatieprogramma opnieuw op en klikt u op **Opnieuw controleren**wanneer u bij het scherm **SQL-instellingen** bent.

5. Geef in de **installatie-instellingen**een locatie op voor de installatie van Microsoft Azure Backup-serverbestanden en klik op **Volgende**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    De kraslocatie is vereist om een back-up te maken van Azure. Zorg ervoor dat de grootte van de kraslocatie gelijk is aan ten minste 5% van de gegevens die gepland zijn om een back-up te maken van Azure. Voor schijfbeveiliging moeten afzonderlijke schijven worden geconfigureerd zodra de installatie is voltooid. Zie [Opslagpools en schijfopslag configureren](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12))voor meer informatie over opslaggroepen.

6. Geef in het scherm **Beveiligingsinstellingen** een sterk wachtwoord op voor beperkte lokale gebruikersaccounts en klik op **Volgende**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Selecteer in het scherm **Microsoft Update Opt-In** of u *Microsoft Update* wilt gebruiken om te controleren op updates en klik op **Volgende**.

   > [!NOTE]
   > We raden u aan windows update door te verwijzen naar Microsoft Update, dat beveiliging en belangrijke updates biedt voor Windows en andere producten zoals Microsoft Azure Backup Server.
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Bekijk het *overzicht van instellingen* en klik op **Installeren**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Wanneer Azure Backup Server klaar is met de installatie, start het installatieprogramma onmiddellijk het installatieprogramma van de Microsoft Azure Recovery Services-agent.

9. Het installatieprogramma van microsoft Azure Recovery Services Agent wordt geopend en controleert op internetverbinding. Als er een internetverbinding beschikbaar is, gaat u verder met de installatie. Als er geen verbinding is, geeft u proxygegevens om verbinding te maken met internet. Nadat u uw proxy-instellingen hebt opgegeven, klikt u op **Volgende**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Als u de Microsoft Azure Recovery Services Agent wilt installeren, klikt u op **Installeren**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    De Microsoft Azure Recovery Services-agent, ook wel de Azure Backup-agent genoemd, configureert de Azure Backup Server naar de kluis Recovery Services. Eenmaal geconfigureerd, maakt Azure Backup Server altijd een back-up van gegevens naar dezelfde vault van Recovery Services.

11. Zodra de Microsoft Azure Recovery Services-agent klaar is met de installatie, klikt u op **Volgende** om de volgende fase te starten: Azure Backup Server registreren met de kluis Herstelservices.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Het installatieprogramma lanceert de **wizard Registerserver**.

12. Schakel over naar uw Azure-abonnement en uw Vault Recovery Services. Klik **in** het menu Infrastructuur voorbereiden op **Downloaden** om vault-referenties te downloaden. Als de knop **Downloaden** in stap 2 niet actief is, selecteert u **Al gedownload of gebruikt u de nieuwste Azure Backup Server-installatie** om de knop te activeren. De vault credentials worden gedownload naar de locatie waar u downloads opslaat. Wees je bewust van deze locatie, want je hebt het nodig voor de volgende stap.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. Klik in het menu **Vault Identification** op **Bladeren** om de vault credentials van Recovery Services te vinden.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    Ga in het dialoogvenster **Vault Credentials selecteren** naar de downloadlocatie, selecteer uw vault credentials en klik op **Openen**.

    Het pad naar de referenties wordt weergegeven in het menu Vault Identification. Klik **op Volgende** om door te gaan naar de versleutelingsinstelling.

14. Geef in het dialoogvenster **Versleutelingsinstelling** een wachtwoordzin op voor de back-upversleuteling en een locatie om de wachtwoordzin op te slaan en klik op **Volgende**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    U uw eigen wachtwoordzin, of gebruik maken van de wachtwoordzin generator om een voor u te maken. De wachtwoordzin is van u en Microsoft slaat deze wachtwoordzin niet op of beheert deze niet. Als u zich wilt voorbereiden op een ramp, slaat u uw wachtwoordzin op een toegankelijke locatie op.

    Zodra u op **Volgende**klikt, wordt de Azure Backup Server geregistreerd bij de kluis Recovery Services. Het installatieprogramma blijft SQL Server en de Azure Backup Server installeren.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Wanneer het installatieprogramma is voltooid, geeft de status aan dat alle software is geïnstalleerd.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Wanneer de installatie is voltooid, worden de Azure Backup Server-console en de PowerShell-pictogrammen azure backup server gemaakt op het serverbureaublad.

## <a name="add-backup-storage"></a>Back-upopslag toevoegen

De eerste back-up wordt bewaard op opslag die is gekoppeld aan de Azure Backup Server-machine. Zie [Moderne back-upopslag toevoegen](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801)voor meer informatie over het toevoegen van schijven.

> [!NOTE]
> U moet back-upopslag toevoegen, zelfs als u van plan bent gegevens naar Azure te verzenden. In de Azure Backup Server-architectuur bevat de kluis Recovery Services de *tweede* kopie van de gegevens, terwijl de lokale opslag de eerste (en verplichte) back-upbevat.
>
>

## <a name="network-connectivity"></a>Netwerkconnectiviteit

Azure Backup Server vereist connectiviteit met de Azure Backup-service om het product succesvol te laten werken. Als u wilt valideren of de machine ```Get-DPMCloudConnection``` de verbinding met Azure heeft, gebruikt u de cmdlet in de PowerShell-console van Azure Backup Server. Als de uitvoer van de cmdlet WAAR is, bestaat er connectiviteit, anders is er geen verbinding.

Tegelijkertijd moet het Azure-abonnement in een gezonde staat zijn. Meld u aan bij de [abonnementsportal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)om de status van uw abonnement te achterhalen en te beheren.

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

Als een firewall of proxy de toegang tot Azure verhindert, voegt u de volgende domeinadressen toe in de lijst firewall/proxyprofiel:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Zodra de verbinding met Azure is hersteld met de Azure Backup Server, bepaalt de Azure-abonnementsstatus de bewerkingen die kunnen worden uitgevoerd. Zodra de server is **verbonden,** gebruikt u de tabel in [netwerkconnectiviteit](backup-mabs-install-azure-stack.md#network-connectivity) om de beschikbare bewerkingen te bekijken.

### <a name="handling-subscription-states"></a>Abonnementsstatus behandeling

Het is mogelijk om een Azure-abonnement te wijzigen van *verlopen* of *de ingerichte* status in *actieve* status. Hoewel de abonnementsstatus niet *actief*is:

- Terwijl een abonnement is *Gedeprovisioneerd,* verliest het functionaliteit. Als u het abonnement herstelt naar *Active,* wordt de back-up-/herstelfunctionaliteit nieuw leven ingeblazen. Als back-upgegevens op de lokale schijf zijn bewaard met een voldoende grote bewaarperiode, kunnen die back-upgegevens worden opgehaald. Back-upgegevens in Azure gaan echter onherroepelijk verloren zodra het abonnement de status *Deprovisioned* invoert.
- Terwijl een abonnement *is verlopen,* verliest het functionaliteit. Geplande back-ups worden niet uitgevoerd terwijl een abonnement is *verlopen.*

## <a name="troubleshooting"></a>Problemen oplossen

Als de Microsoft Azure Backup-server tijdens de installatiefase (of back-up of herstel) niet met fouten optreedt, raadpleegt u het [document foutcodes](https://support.microsoft.com/kb/3041338).
U ook verwijzen naar [veelgestelde vragen](backup-azure-backup-faq.md) met Azure Backup

## <a name="next-steps"></a>Volgende stappen

Het artikel, [Voorbereiding van uw omgeving voor DPM,](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801)bevat informatie over ondersteunde Azure Backup Server-configuraties.

U de volgende artikelen gebruiken om een beter inzicht te krijgen in de beveiliging van workloads met Microsoft Azure Backup Server.

- [SQL Server-back-up](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Back-up van sharePoint-server](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternatieve serverback-up](backup-azure-alternate-dpm-server.md)
