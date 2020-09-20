---
title: Servers beoordelen door geïmporteerde servergegevens te gebruiken met Azure Migrate-serverevaluatie
description: 'Beschrijft hoe u met behulp van de service Azure Migrate: Serverevaluatie en geïmporteerde gegevens on-premises servers kunt detecteren en evalueren voor migratie naar Azure.'
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: ff7e423063859a6cdc1a4362fb030c0deb75eb32
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658695"
---
# <a name="assess-servers-by-using-imported-data"></a>Servers evalueren met geïmporteerde gegevens

In dit artikel wordt uitgelegd hoe u on-premises servers kunt beoordelen met het hulpprogramma [Azure Migrate: Serverevaluatie](migrate-services-overview.md#azure-migrate-server-assessment-tool), door metagegevens van de server te importeren in CSV-indeling (door komma's gescheiden waarden). Voor deze evaluatiemethode hoeft u het Azure Migrate-apparaat niet in te stellen om een evaluatie te maken. Dit is handig als:

- U een snelle, initiële evaluatie wilt maken voordat u het apparaat implementeert.
- U het Azure Migrate-apparaat niet in uw organisatie kunt implementeren.
- U geen referenties kunt delen die toegang bieden tot on-premises servers.
- Uanwege beveiligingsbeperkingen geen gegevens kunt verzamelen en verzenden die zijn verzameld door het apparaat naar Azure. U kunt de gegevens die u in een geïmporteerd bestand deelt, beheren. Ook zijn veel van de gegevens (bijvoorbeeld IP-adressen opgeven) optioneel.

## <a name="before-you-start"></a>Voordat u begint

Houd rekening met de volgende punten:

- U kunt maximaal 20.000 servers in één CSV-bestand toevoegen.
- U kunt maximaal 20.000 servers in een Azure Migrate-project toevoegen met behulp van CSV.
- U kunt servergegevens meerdere keren naar de serverevaluatie uploaden met behulp van CSV.
- Het verzamelen van toepassingsgegevens is handig bij het evalueren van uw on-premises omgeving voor migratie. Serverevaluatie voert momenteel echter geen evaluatie op toepassingsniveau uit en houdt geen rekening met toepassingen bij het maken van een evaluatie.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Azure Migrate-project instellen.
> * Vul een CSV-bestand in met servergegevens.
> * Importeer het bestand om servergegevens toe te voegen aan de serverevaluatie.
> * Een evaluatie maken en beoordelen.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg de handleidingen met procedures voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="set-azure-permissions-for-azure-migrate"></a>Stel Azure-machtigingen in voor Azure Migrate

Uw Azure-account moet gemachtigd zijn om een Azure Migrate-project te maken.

1. Open in de Azure-portal het abonnement en selecteer **Toegangsbeheer (IAM)**.
2. In **Toegang controleren**, zoekt u het relevante account en selecteert u dit om machtigingen weer te geven.
3. Zorg dat u de machtigingen **Inzender** of **Eigenaar** hebt.
    - Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de rol toe te wijzen.

## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate-project instellen

Stel als volgt een nieuw Azure Migrate-project in:

1. Zoek in de Azure-portal, in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Selecteer in **Overzicht** onder **Servers ontdekken, evalueren en migreren** de optie **Servers evalueren en migreren**.

    ![Servers detecteren en evalueren](./media/tutorial-assess-import/assess-migrate.png)

4. Selecteer in **Aan de slag** **Hulpprogramma(‘s) toevoegen**.
5. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken. Voor meer informatie:

    - Bekijk de ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).
    - Wanneer u een migratie uitvoert, kunt u elke gewenste doelregio selecteren.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-import/migrate-project.png)

7. Selecteer **Next**.
8. In **Evaluatieprogramma selecteren** selecteert u **Azure Migrate: Serverevaluatie** > **Volgende**.

    ![Een Azure Migrate-evaluatie maken](./media/tutorial-assess-import/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Controleer in **Beoordelen en hulpprogramma's toevoegen** de instellingen en selecteer vervolgens **Hulpprogramma's toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt vervolgens naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.

## <a name="prepare-the-csv"></a>De CSV voorbereiden

De CSV-sjabloon downloaden en er servergegevens aan toevoegen.

### <a name="download-the-template"></a>De sjabloon downloaden

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** selecteert u **Detecteren**.
2. Selecteer **Importeren met behulp van CSV-bestand** in **Computers detecteren**.
3. Selecteer **Download** om de CSV-sjabloon te downloaden. U kunt deze ook [direct downloaden](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![Download de CSV-sjabloon](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Serverinformatie toevoegen

Verzamel servergegevens en voeg deze toe aan het CSV-bestand.

- Om gegevens te verzamelen, kunt u deze exporteren vanuit hulpprogramma's die u gebruikt voor het beheer van uw on-premises servers, zoals VMware vSphere of uw CMDB (configuratiebeheerdatabase).
- Als u voorbeeldgegevens wilt bekijken, downloadt u ons [voorbeeldbestand](https://go.microsoft.com/fwlink/?linkid=2108405).

De volgende tabel bevat een overzicht van de bestandsvelden die moeten worden ingevuld:

**Veldnaam** | **Verplicht** | **Details**
--- | --- | ---
**Servernaam** | Ja | U kunt het beste de Fully Qualified Domain Name (FQDN) opgeven.
**IP-adres** | Nee | Serveradres.
**Kernen** | Ja | Het aantal processorkernen dat aan de server is toegewezen.
**Geheugen** | Ja | Het totale RAM-geheugen (in MB) dat aan de server is toegewezen.
**Naam van besturingssysteem** | Ja | Besturingssysteem van de server. <br/> Namen van besturingssystemen die overeenkomen met de namen in [deze](#supported-operating-system-names) lijst worden herkend door de evaluatie.
**Versie van het besturingssysteem** | Nee | Versie van serverbesturingssysteem.
**Architectuur van besturingssysteem** | Nee | Architectuur van serverbesturingssysteem <br/> Geldige waarden zijn: x64, x86, amd64, 32-bits of 64-bits
**Aantal schijven** | Nee | Niet nodig als er details van de individuele schijf worden gegeven.
**Grootte van schijf 1**  | Nee | De maximale grootte van de schijf, in GB.<br/>U kunt gegevens voor meer schijven toevoegen door in de sjabloon [kolommen toe te voegen](#add-multiple-disks). U kunt maximaal acht schijven toevoegen.
**Schijf 1 bewerkingen per seconde lezen** | Nee | Schijf gelezen bewerkingen per seconde.
**Schijf 1 bewerkingen per seconde schrijven** | Nee | Schijf geschreven bewerkingen per seconde.
**Schijf 1 leesdoorvoer** | Nee | Gegevens die per seconde van de schijf worden gelezen, in MB per seconde.
**Schijf 1 schrijfdoorvoer** | Nee | Gegevens die per seconde naar de schijf worden geschreven, in MB per seconde.
**Gebruikspercentage van CPU** | Nee | Gebruikt percentage CPU-gebruik.
**Percentage voor geheugengebruik** | Nee | Gebruikt percentage RAM-gebruik.
**Totaal door schijf gelezen bewerkingen per seconde** | Nee | Leesbewerkingen voor alle schijven gecombineerd. <br/> Gebruik dit veld als u geen gegevens op schijfniveau kunt opgeven. 
**Totaal door schijf geschreven bewerkingen per seconde** | Nee | Schrijfbewerkingen per seconde voor alle schijven gecombineerd. <br/> Gebruik dit veld als u geen gegevens op schijfniveau kunt opgeven.
**Totaal door schijf gelezen doorvoer** | Nee | Gelezen gegevens van alle schijven, in MB per seconde. <br/> Gebruik dit veld als u geen gegevens op schijfniveau kunt opgeven. 
**Totaal door schijf geschreven doorvoer** | Nee | Gegevens die naar alle schijven worden geschreven, in MB per seconde. <br/> Gebruik dit veld als u geen gegevens op schijfniveau kunt opgeven.
**Netwerk in doorvoer** | Nee | Gegevens ontvangen door de server, in MB per seconde.
**Netwerk uitgaande doorvoer** | Nee | Gegevens verzonden door de server, in MB per seconde.
** Type firmware** | Nee | Serverfirmware. Waarden kunnen "BIOS" of "UEFI" zijn.
**MAC-adres**| Nee | MAC-adres van server.


### <a name="add-operating-systems"></a>Besturingssystemen toevoegen

Evaluatie herkent specifieke namen van besturingssystemen. Een naam die u opgeeft, moet exact overeenkomen met een van de tekenreeksen in de [lijst met ondersteunde namen](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Meerdere schijven toevoegen

De sjabloon biedt standaardvelden voor de eerste schijf. U kunt vergelijkbare kolommen voor maximaal acht schijven toevoegen.

Als u bijvoorbeeld alle velden voor een tweede schijf wilt opgeven, voegt u deze kolommen toe:

- Schijf 2
- Schijf 2 bewerkingen per seconde lezen
- Schijf 2 bewerkingen per seconde schrijven
- Schijf 2 leesdoorvoer
- Schijf 2 schrijfdoorvoer

Als u geen gegevens op schijfniveau kunt opgeven, kunt u de gegevens van schijfprestaties per server opgeven met de volgende velden. Raadpleeg [dit](#add-server-information) gedeelte voor meer informatie over elk veld.
- Totaal door schijf gelezen bewerkingen per seconde
- Totaal door schijf geschreven bewerkingen per seconde
- Totaal door schijf gelezen doorvoer
- Totaal door schijf geschreven doorvoer

## <a name="import-the-server-information"></a>De servergegevens importeren

Nadat u informatie aan de CSV-sjabloon hebt toegevoegd, importeert u de servers in de Serverevaluatie.

1. Ga in Azure Migrate naar **Computers detecteren** naar de voltooide sjabloon.
2. Selecteer **Importeren**.
3. De importstatus wordt weergegeven.
    - Als er waarschuwingen worden weergegeven in de status, kunt u deze herstellen of doorgaan zonder ze te adresseren.
    - Als u de nauwkeurigheid van de evaluatie wilt verbeteren, kunt u de servergegevens verbeteren, zoals wordt voorgesteld in waarschuwingen.
    - Als u waarschuwingen wilt weergeven en herstellen, selecteert u **Waarschuwingsdetails downloaden. CSV**. Met deze bewerking wordt het CSV-bestand gedownload met de opgenomen waarschuwingen. Bekijk de waarschuwingen en los zo nodig problemen op.
    - Als fouten worden weergegeven in de status zodat de status van de import **Mislukt**, moet u deze fouten oplossen voordat u door kunt gaan met het importeren:
        1. Download de CSV, die nu foutgegevens bevat.
        1. Controleer de fouten en los ze op. 
        1. Upload het gewijzigde bestand opnieuw.
4. Wanneer de status van de import **Voltooid** is, zijn de gegevens van de server geïmporteerd.

## <a name="update-server-information"></a>Serverinformatie updaten

U kunt de gegevens voor een server bijwerken door de gegevens voor de server opnieuw te importeren met dezelfde **Servernaam**. U kunt het veld **Servernaam** niet wijzigen. Servers verwijderen wordt momenteel niet ondersteund.

## <a name="verify-servers-in-the-portal"></a>Verifieer servers in de portal

U kunt controleren of de servers worden weergegeven in de Azure-portal na de detectie:

1. Open het Azure Migrate-dashboard.
2. Op de **Azure Migrate - Servers** > **Azure Migrate: Serverevaluatie**-pagina, selecteert u het pictogram dat het aantal voor **Gedetecteerde servers** weergeeft.
3. Selecteer het tabblad **Gebaseerd op importeren**.

## <a name="set-up-and-run-an-assessment"></a>Een evaluatie instellen en uitvoeren

U kunt twee soorten evaluaties maken met behulp van Serverevaluatie.


**Evaluatietype** | **Details**
--- | --- 
**Azure VM** | Evaluaties om uw on-premises servers te migreren naar virtuele Azure-machine. <br/><br/> U kunt uw on-premises [VMware-VM's](how-to-set-up-appliance-vmware.md), [Hyper-V-VM's](how-to-set-up-appliance-hyper-v.md) en [fysieke servers](how-to-set-up-appliance-physical.md) evalueren voor migratie naar Azure met dit evaluatietype.
**Azure VMware Solution (AVS)** | Evaluaties om uw on-premises servers te migreren naar [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> U kunt uw on-premises [VMware-VM’s](how-to-set-up-appliance-vmware.md) evalueren voor migratie naar Azure VMware Solution (AVS) met dit evaluatietype. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)

### <a name="sizing-criteria"></a>Criteria voor het aanpassen van de grootte

Server Assessment biedt twee opties voor criteria voor het aanpassen van de grootte:

**Criteria voor het aanpassen van de grootte** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties die aanbevelingen doen op basis van verzamelde prestatiegegevens | **Azure-VM-evaluatie**: Aanbeveling voor VM-grootte is gebaseerd op verbruiksgegevens voor CPU en geheugen.<br/><br/> Aanbeveling voor schijftype (standaard HDD/SSD of premium-beheerde schijven) is gebaseerd op de IOPS en doorvoer van de on-premises schijven.<br/><br/> **AVS-evaluatie (Azure VMware Solution)** : Aanbeveling voor AVS-knooppunten is gebaseerd op verbruiksgegevens voor CPU en geheugen.
**As-is on-premises** | Evaluaties die geen gebruik maken van prestatiegegevens voor aanbevelingen. | **Azure-VM-evaluatie**: Aanbeveling voor VM-grootte is gebaseerd op de on-premises VM-grootte<br/><br> De aanbeveling voor het schijftype is gebaseerd op de instelling die u selecteert bij het opslagtype voor de evaluatie.<br/><br/> **AVS-evaluatie (Azure VMware Solution)**: Aanbeveling voor AVS-knooppunten is gebaseerd op de on-premises VM-grootte.


Om de evaluatie uit te voeren:

1. Bekijk de [best practices](best-practices-assessment.md) voor het maken van evaluaties.
2. Op het tabblad **Servers** in de tegel **Azure Migrate: Serverevaluatie** selecteert u **Evalueren**.

    ![Evalueren](./media/tutorial-assess-physical/assess.png)

3. Geef bij **Servers evalueren** de naam van de evaluatie op en selecteer het **evaluatie**type *Azure VM* als u Azure VM-evaluaties wilt uitvoeren of *Azure VMware Solution (AVS)* als u SAVS-evaluaties wilt uitvoeren.

    ![Basisprincipes evaluatie](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Selecteer in **Detectiebron** **Machines die zijn toegevoegd via importeren naar Azure Migrate**.

5. Selecteer **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Evaluatie-eigenschappen](./media/tutorial-assess-physical/view-all.png)

6. Klik op **volgende** om **machines te selecteren om te evalueren**. In **Een groep selecteren of maken** selecteert u **Nieuwe maken** en geeft u een groepsnaam op. Een groep verzamelt een of meer VM's voor evaluatie.
7. Selecteer in **Machines toevoegen aan de groep** de servers die aan de groep moeten worden toegevoegd.
8. Klik op **volgende** en **Controleren en evaluatie maken** om de details van de evaluatie te controleren.
9. Klik op **Evaluatie maken** om de groep te maken en vervolgens de evaluatie uit te voeren.

    ![Een evaluatie maken](./media/tutorial-assess-physical/assessment-create.png)

9. Nadat de evaluatie is gemaakt, kunt u deze bekijken in **Servers** > **Azure Migrate: Serverevaluatie** > **Evaluaties**.
10. Selecteer **Evaluatie exporteren** om deze te downloaden als een Microsoft Excel-bestand.

Kijk [hier](how-to-create-azure-vmware-solution-assessment.md) voor meer informatie over **Azure VMware Solution (AVS)**. 

## <a name="review-an-azure-vm-assessment"></a>Een Azure VM-evaluatie controleren

Een Azure VM-evaluatie beschrijft:

- **Azure-gereedheid**: Hiermee wordt aangegeven of servers geschikt zijn voor migratie naar Azure.
- **Schatting maandelijkse kosten**: Geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de servers in Azure.
- **Schatting maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.

### <a name="view-an-assessment"></a>Een evaluatie weergeven

1. Selecteer in **Migratiedoelen** > **Servers** de optie **Evaluaties** in **Azure Migrate: Serverevaluatie**.
2. Selecteer in **Evaluaties** een evaluatie om deze te openen.

    ![Evaluatie-overzicht](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid beoordelen

1. Bekijk in **Azure-gereedheid** of de servers gereed zijn voor migratie naar Azure.
2. Controleer de status:
    - **Gereed voor Azure**: Azure Migrate raadt een VM-grootte en schattingen voor de kosten aan voor VM's in de evaluatie.
    - **Gereed met voorwaarden**: Geeft problemen en voorgesteld herstel weer.
    - **Niet gereed voor Azure**: Geeft problemen en voorgesteld herstel weer.
    - **Gereedheid onbekend**: Azure Migrate kan de gereedheid niet evalueren door problemen met de beschikbaarheid van gegevens.

3. Selecteer een status voor **Azure-gereedheid**. U kunt details van de servergereedheid weergeven en inzoomen op de details van de server, inclusief berekenings-, opslag- en netwerkinstellingen.

### <a name="review-cost-details"></a>Gedetailleerde kosten beoordelen

Deze weergave toont de geschatte berekenings- en opslagkosten om virtuele machines in Azure uit te voeren. U kunt:

- Controleer de maandelijkse berekenings- en opslagkosten. De kosten worden geaggregeerd voor alle servers in de geëvalueerde groep.

    - Schattingen van kosten zijn gebaseerd op de grootte-aanbevelingen voor een machine en de schijven en eigenschappen.
    - De geschatte maandelijkse kosten voor berekening en opslag worden weergegeven.
    - De kostenschatting geldt voor het uitvoeren van de on-premises servers als IaaS-VM’s (Infrastructure as a service). Serverevaluatie houdt geen rekening met PaaS- of SaaS-kosten (Platform as a service of Software as a service).

- De schatting van de maandelijkse opslagkosten controleren. In deze weergave worden geaggregeerde opslagkosten voor de geëvalueerde groep weergegeven, gesplitst over verschillende typen opslagschijven.
- Zoom in om de details voor specifieke VM’s te bekijken.

> [!NOTE]
> Betrouwbaarheidsclassificaties worden niet toegewezen aan beoordelingen van servers die worden geïmporteerd in Serverevaluatie met behulp van CSV.

## <a name="supported-operating-system-names"></a>Ondersteunde namen van besturingssysteem

De namen van besturingssystemen in de CSV moeten overeenkomen of de namen in deze lijst bevatten. Dit is nodig om de opgegeven namen als geldig te herkennen door de evaluatie.

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A - H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      MS-DOS
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S - T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U - Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Drempelwaarde voor Windows Server<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Geïmporteerde servers in Azure Migrate: Serverevaluatie door gebruik te maken van CSV.
> * Een evaluatie gemaakt en gecontroleerd.

Kies nu [Een apparaat implementeren](./migrate-appliance.md) voor nauwkeurige evaluaties en servers in groepen verzamelen voor een diepgaande beoordeling met behulp van [Afhankelijkheidsanalyse](./concepts-dependency-visualization.md).
