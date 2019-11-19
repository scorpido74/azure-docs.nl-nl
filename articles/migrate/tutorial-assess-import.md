---
title: Servers met behulp van geïmporteerde server gegevens beoordelen met Azure Migrate server-evaluatie
description: Hierin wordt beschreven hoe u on-premises servers kunt evalueren voor migratie naar Azure met Azure Migrate server evaluatie met behulp van geïmporteerde gegevens.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 7bf47731f2a3621e7bbdc1b104d94e97f2d03099
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158647"
---
# <a name="assess-servers-using-imported-data"></a>Servers beoordelen met behulp van geïmporteerde gegevens

In dit artikel wordt uitgelegd hoe u on-premises servers kunt beoordelen met [Azure migrate: Server evaluatie](migrate-services-overview.md#azure-migrate-server-assessment-tool)door meta gegevens van de server te importeren met CSV. Bij deze evaluatie methode hoeft u het Azure Migrate apparaat niet in te stellen om een evaluatie te maken. Dit is handig als:

- U wilt een snelle, initiële evaluatie maken voordat u het apparaat implementeert.
- U kunt het Azure Migrate apparaat niet in uw organisatie implementeren.
- U kunt geen referenties delen die toegang bieden tot on-premises servers.
- Vanwege beveiligings beperkingen kunt u geen gegevens verzamelen en verzenden die zijn verzameld door het apparaat naar Azure. Met een geïmporteerd bestand kunt u de gegevens beheren die u deelt, en veel gegevens (zoals het opgeven van IP-adressen) optioneel zijn.


## <a name="before-you-start"></a>Voordat u begint

Opmerking:

- U kunt Maxi maal 20000 servers in één CSV-bestand toevoegen.
- U kunt Maxi maal 20000 servers in een Azure Migrate project toevoegen met behulp van CSV.
- U kunt Server gegevens meerdere keren met CSV uploaden naar Azure Migrate server-evaluatie.
- Hoewel het verzamelen van toepassings gegevens handig is bij het evalueren van uw on-premises omgeving voor migratie, voert Azure Migrate server beoordeling momenteel geen evaluatie op toepassings niveau uit en worden er geen toepassingen meer in rekening gebracht wanneer een evaluatie maken.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Stel een Azure Migrate project in.
> * Vul een CSV-bestand in met server gegevens.
> * Importeer het bestand om Server gegevens toe te voegen aan de evaluatie van Azure Migrate server.
> * Een evaluatie maken en bekijken.

> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de artikelen met procedures voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="set-azure-permissions-for-azure-migrate"></a>Azure-machtigingen instellen voor Azure Migrate

Uw Azure-account moet machtigingen hebben om een Azure Migrate project te maken.

1. Open in het Azure Portal het abonnement en selecteer **toegangs beheer (IAM)** .
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.
3. U moet machtigingen voor **Inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samen werken om de rol toe te wijzen.


## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate project instellen

Ga als volgt te werk om een nieuw Azure Migrate-project op te zetten:

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. In **Overzicht**, onder **Servers ontdekken, evalueren en migreren**, klikt u op **Servers evalueren en migreren**.

    ![Servers detecteren en beoordelen](./media/tutorial-assess-import/assess-migrate.png)

4. Klik in **Aan de slag** op **Hulpprogramma's toevoegen**.
5. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.     
6. Geef in **Project Details**de project naam en de geografie op waarin u het project wilt maken.

    - Ondersteunde geographs [bekijken](migrate-support-matrix.md#supported-geographies) . Het geografische gebied voor het project wordt alleen gebruikt om de metagegevens op te slaan die worden verzameld van on-premises virtuele machines.
    - Wanneer u een migratie uitvoert, kunt u elke gewenste doelregio selecteren.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-import/migrate-project.png)


7. Klik op **Volgende**.
8. Selecteer in **hulp programma voor beoordeling selecteren**de optie **Azure migrate: Server evaluatie** > **volgende**.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-import/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Controleer in **Evalueren en hulpprogramma's toevoegen** de instellingen en klik op **Hulpmiddelen toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.


## <a name="prepare-the-csv"></a>De CSV voorbereiden

Down load de CSV-sjabloon en voeg er Server gegevens aan toe.


### <a name="download-the-template"></a>De sjabloon downloaden

1. In **migratie doelen** > **servers** > **Azure migrate: Server evaluatie**, klikt u op **ontdekken**.
2. In **computers detecteren**selecteert **u importeren met. CSV**.
3. Klik op **downloaden** om de te downloaden. CSV-sjabloon. U kunt [deze ook rechtstreeks downloaden](https://go.microsoft.com/fwlink/?linkid=2108404).

    ![Update. CSV-sjabloon](./media/tutorial-assess-import/download-template.png)


### <a name="add-server-information"></a>Server gegevens toevoegen

Verzamel Server gegevens en voeg deze toe aan het CSV-bestand.

- Als u gegevens wilt verzamelen, kunt u deze exporteren vanuit hulpprogram ma's die u gebruikt voor on-premises server beheer, zoals VMware vSphere of uw CMDB (Configuration Management data base).
- Down load ons [voorbeeld bestand](https://go.microsoft.com/fwlink/?linkid=2108405)om voorbeeld gegevens te bekijken.


De volgende tabel bevat een overzicht van de bestands velden die moeten worden ingevuld.

**Veld naam** | **Ingevuld** | **Details**
--- | --- | ---
**Servernaam** | Ja | U wordt aangeraden de FQDN op te geven.
**IP-adres** | Nee | Server adres.
**Aantal kernen** | Ja | Het aantal processor kernen dat aan de server is toegewezen.
**Geheugenmetabase** | Ja | Totale hoeveelheid RAM-geheugen (MB) die aan de server is toegewezen.
**Naam van besturings systeem** | Ja | Besturings systeem van de server.
**Versie van het besturingssysteem** | Nee | Versie van het besturings systeem van de server.
**Aantal schijven** | Nee | Niet nodig als er details van de afzonderlijke schijf worden gegeven.
**Grootte van schijf 1**  | Nee | Maximale grootte van schijf (GB)<br/> U kunt Details voor meer schijven toevoegen door [kolommen toe te voegen](#add-multiple-disks) aan de sjabloon. U kunt Maxi maal acht schijven toevoegen.
**Schijf 1 read OPS** | Nee | Lees bewerkingen op de schijf per seconde.
**Write OPS schijf 1** | Nee | Schrijf bewerkingen op de schijf per seconde.
**Schijf 1 Lees doorvoer** | Nee | Gegevens die per seconde uit de schijf worden gelezen, in MB per seconde.
**Schrijf doorvoer schijf 1** | Nee | Gegevens geschreven naar schijf per seconde in MB per seconde.
**Percentage CPU-gebruik** | Nee | Percentage gebruik van CPU.
**Percentage geheugen gebruik** | Nee | Het percentage van het gebruik van het RAM-geheugen.
**Totaal aantal schijven gelezen OPS** | Nee | Lees bewerkingen op de schijf per seconde.
**Totaal aantal schijven schrijven OPS** | Nee | Schrijf bewerkingen op de schijf per seconde.
**Totale Lees doorvoer van schijven** | Nee | Gegevens die worden gelezen van de schijf in MB per seconde.
**Totale schrijf doorvoer van schijven** | Nee | Gegevens die naar de schijf worden geschreven in MB per seconde.
**Netwerk-inkomend doorvoer** | Nee | Gegevens ontvangen door de server in MB per seconde.
**Netwerk-uitgaand doorvoer** | Nee | Gegevens die door de server worden verzonden in MB per seconde.
**Type firmware** | Nee | Server firmware. Waarden kunnen "BIOS" of "UEFI" zijn
**Servertype** | Nee | Waarden kunnen fysiek of virtueel zijn.
**Hypervisor** | Nee | Hyper Visor waarop een machine wordt uitgevoerd. <br/> Waarden kunnen "VMware", "Hyper-V", "xen", "AWS", "GCP" of "other" zijn.
**Versie nummer van Hyper Visor** | Nee | Versie van Hyper Visor.
**ID van virtuele machine** | Nee | VM-id. Dit is de **InstanceUUid** voor de VMware VCENTER-VM of de **hyper-v-VM-id** voor Hyper-V.
**Virtual Machine Manager-ID** | Nee | Dit is de **InstanceUUid** voor VMware vCenter. Niet nodig voor Hyper-V.
**MAC-adres**| Nee | MAC-adres van de server.
**BIOS-ID** | Nee | Server-BIOS-ID.
**Aangepaste Server-ID**| Nee | Lokale unieke server-Id's on-premises. <br/> Nuttig voor het bijhouden van de geïmporteerde server op lokale ID.
**Naam toepassing 1** | Nee | Naam van workloads die worden uitgevoerd op de server.<br/> U kunt Details voor meer apps toevoegen door [kolommen toe te voegen](#add-multiple-applications) aan de sjabloon. U kunt Maxi maal vijf toepassingen toevoegen.
**Type toepassing 1** | Nee | Type werk belasting die wordt uitgevoerd op de server
**Versie van toepassing 1** | Nee | De versie van de werk belasting die wordt uitgevoerd op de server.
**Verval datum van de licentie voor toepassing 1** | Nee | De licentie voor de werk belasting is verlopen (indien van toepassing).
**Bedrijfs eenheid** | Nee | De bedrijfs eenheid waartoe de server behoort.
**Zakelijke eigenaar** | Nee | Eigenaar van Business Unit.
**Naam van zakelijke toepassing** | Nee | De naam van de toepassing waartoe de app behoort.
**Locatie** | Nee | Data Center waarin de server zich bevindt.
**Datum server buiten gebruik stellen** | Nee | Datum buiten gebruik stellen van fysieke server of de onderliggende fysieke server van de virtuele server

### <a name="add-operating-systems"></a>Besturings systemen toevoegen

Beoordeling herkent specifieke namen van besturings systemen. De naam van het besturings systeem dat u opgeeft, moet overeenkomen met een van de opties in de lijst [ondersteunde namen](#supported-operating-system-names) .


### <a name="add-multiple-disks"></a>Meerdere schijven toevoegen

De sjabloon biedt standaard velden voor de eerste schijf.  U kunt vergelijk bare kolommen voor Maxi maal 8 schijven toevoegen.

Als u bijvoorbeeld alle velden voor een tweede schijf wilt opgeven, voegt u de kolommen toe:

Schijf 2 grootte schijf 2 lezen OPS-schijf 2 write OPS-schijf 2 Lees doorvoer schijf 2 Schrijf doorvoer

U kunt desgewenst alleen specifieke velden toevoegen voor een schijf.


### <a name="add-multiple-applications"></a>Meerdere toepassingen toevoegen

De sjabloon biedt velden voor één toepassing. U kunt soort gelijke kolommen toevoegen voor Maxi maal vijf apps.  

Als u bijvoorbeeld alle velden voor een tweede app wilt opgeven, voegt u de kolommen toe:

Toepassing 2 naam toepassing 2 type Application 2-versie toepassing 2 licentie verval


U kunt desgewenst alleen specifieke velden voor een app toevoegen.

> [!NOTE]
> App-gegevens zijn handig bij het evalueren van uw on-premises omgeving voor migratie. Azure Migrate server beoordeling voert momenteel echter geen evaluatie op app-niveau uit en er worden geen apps in rekening gebracht bij het maken van een evaluatie.


## <a name="upload-the-server-information"></a>De server gegevens uploaden

Nadat u informatie aan de CSV-sjabloon hebt toegevoegd, importeert u de servers in Azure Migrate: Server Assessment.

1. Blader in Azure Migrate > **computers detecteren**de ingevulde sjabloon.
2. Klik op **Import**.
3. De import status wordt weer gegeven.
    - Als er waarschuwingen worden weer gegeven in de status, kunt u deze corrigeren of door gaan zonder ze te adresseren.
    - Het verbeteren van de server gegevens zoals aanbevolen in waarschuwingen verbetert de nauw keurigheid van de beoordeling.
    - Klik op waarschuwings Details downloaden om waarschuwingen weer te geven en te herstellen als deze worden weer gegeven **. CSV**. Hiermee downloadt u het CSV-bestand met waarschuwingen die zijn toegevoegd. U kunt de waarschuwingen bekijken en zo nodig problemen oplossen.
    Als fouten worden weer gegeven in de status (de import status is **mislukt**), moet u deze oplossen voordat u kunt door gaan met het importeren. U kunt dit doen door het CSV-bestand te downloaden. er zijn nu fout gegevens toegevoegd. Controleer de fouten en los ze op. Upload het gewijzigde bestand vervolgens opnieuw.
4. Wanneer de status van importeren is **voltooid**, worden de server gegevens geïmporteerd.


> [!NOTE]
> Als u de server gegevens die naar Azure Migrate zijn geüpload, wilt bijwerken, uploadt u de gegevens voor de server opnieuw met dezelfde **Server naam**. Houd er rekening mee dat het veld **Server naam** niet kan worden gewijzigd nadat de sjabloon is geïmporteerd. Het verwijderen van servers wordt momenteel niet ondersteund.

## <a name="updating-server-information"></a>Server gegevens bijwerken

U kunt een server gegevens bijwerken door de gegevens voor de server opnieuw te uploaden met dezelfde **Server naam**. U kunt het veld **Server naam** niet wijzigen.

Het verwijderen van servers wordt momenteel niet ondersteund.

### <a name="verify-servers-in-the-portal"></a>Servers in de portal controleren

Na de detectie kunt u controleren of de servers worden weer gegeven in de Azure Portal.

1. Open het Azure Migrate dash board.
2. Klik in **Azure migrate-Servers** > pagina **Azure migrate: Server beoordeling** op het pictogram met het aantal voor **gedetecteerde servers**.
3. Klik op het tabblad op **basis van import** .

## <a name="set-up-an-assessment"></a>Een evaluatie instellen

Er zijn twee soorten evaluaties die u kunt maken met behulp van Azure Migrate: Server Assessment.

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van de opgegeven prestatie gegevens waarden | **Aanbevolen VM-grootte**: op basis van gegevens van CPU en geheugen gebruik.<br/><br/> **Aanbevolen schijf type (Standard of Premium Managed disk)** : op basis van de IOPS en door Voer van de on-premises schijven.
**Als on-premises** | Evaluaties op basis van on-premises grootte. | **Aanbevolen VM-grootte**: op basis van de opgegeven server grootte<br/><br> **Aanbevolen schijf type**: op basis van de instelling voor het opslag type die u voor de evaluatie selecteert.


### <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer een evaluatie als volgt uit:

1. Bekijk de [Aanbevolen procedures](best-practices-assessment.md) voor het maken van evaluaties.
2. Klik op het tabblad **servers** in **Azure migrate: tegel server bepaling** op **evalueren**.

    ![Evalueren](./media/tutorial-assess-physical/assess.png)

2. Geef in **servers beoordelen**een naam op voor de evaluatie.
3. Selecteer in **detectie bron**de optie **machines die zijn toegevoegd via importeren in azure migrate**
3. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Eigenschappen van beoordeling](./media/tutorial-assess-physical/view-all.png)

3. In **een groep selecteren of maken**, selecteert u **nieuwe maken**en geeft u een groeps naam op. Een groep verzamelt een of meer Vm's samen voor evaluatie.
4. Selecteer op **computers toevoegen aan de groep de**optie servers die aan de groep moeten worden toegevoegd.
5. Klik op **evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

    ![Een evaluatie maken](./media/tutorial-assess-physical/assessment-create.png)

6. Nadat de evaluatie is gemaakt, bekijkt u deze in **Servers** > **Azure migrate: Server evaluatie** > - **evaluaties**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.



## <a name="review-an-assessment"></a>Een evaluatie controleren

Een evaluatie beschrijft:

- **Azure-gereedheid**: of servers geschikt zijn voor migratie naar Azure.
- **Schatting van maandelijkse kosten**: de geschatte maandelijkse reken-en opslag kosten voor het uitvoeren van de servers in Azure.
- **Schatting maandelijkse opslag kosten**: geschatte kosten voor schijf opslag na migratie.

### <a name="view-an-assessment"></a>Een evaluatie weer geven

1. In **migratie doelen** >  **servers**, klikt u op **evaluaties** in **Azure migrate: Server evaluatie**.
2. Klik in **beoordelingen**op een evaluatie om deze te openen.

    ![Evaluatie samenvatting](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

1. Controleer in **Azure Readiness**of de servers gereed zijn voor migratie naar Azure.
2. Controleer de status:
    - **Gereed voor Azure**: Azure migrate RAADT een VM-grootte en schattingen voor de kosten aan voor vm's in de evaluatie.
    - **Klaar met voor waarden**: toont problemen en voorgestelde herbemiddeling.
    - **Niet gereed voor Azure**: toont problemen en voorgestelde herbemiddeling.
    - **Gereedheid onbekend**: wordt gebruikt wanneer Azure migrate de gereedheid niet kan beoordelen door problemen met de beschik baarheid van gegevens.

2. Klik op de status van een **Azure-gereedheid** . U kunt details van de server gereedheids weer geven en inzoomen op de details van de server, inclusief compute-, opslag-en netwerk instellingen.

### <a name="review-cost-details"></a>Details van kosten bekijken

In deze weer gave ziet u de geschatte berekenings-en opslag kosten voor het uitvoeren van Vm's in Azure.

1. Bekijk de maandelijkse reken-en opslag kosten. De kosten worden geaggregeerd voor alle servers in de geraamde groep.

    - Schattingen van kosten zijn gebaseerd op de grootte aanbevelingen voor een machine en de schijven en eigenschappen.
    - De geschatte maandelijkse kosten voor Compute en opslag worden weer gegeven.
    - De schatting van de kosten is voor het uitvoeren van de on-premises servers als IaaS Vm's. Azure Migrate server-evaluatie houdt geen rekening met PaaS-of SaaS-kosten.

2. U kunt de maandelijkse schattingen voor de opslag kosten bekijken. In deze weer gave worden geaggregeerde opslag kosten voor de geschatte groep weer gegeven, gesplitst over verschillende typen opslag schijven.
3. U kunt inzoomen om de details van specifieke Vm's te bekijken.

> [!NOTE]
> Vertrouwens classificaties worden niet toegewezen aan beoordelingen van servers die zijn geïmporteerd in Azure Migrate server evaluatie met behulp van CSV.


## <a name="supported-operating-system-names"></a>Ondersteunde namen van besturings systemen

Naam | Naam
--- | ---
**A-H** |
Apple Mac OS X 10 | Asianux 3<br/>Asianux 4<br/>Asianux 5
CentOS<br/>CentOS 4/5 | CoreOS Linux
Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8 | FreeBSD
**I-R** |
IBM-BESTURINGS SYSTEEM/2 | opdracht |
Novell NetWare 5<br/>Novell NetWare 6 | Oracle Linux<br/> Oracle Linux 4/5<br/>Oracle Solaris 10<br/> Oracle Solaris 11
Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora |
**S-T** |
SCO Openserver 5<br/>SCO Openserver 6<br/>SCO UnixWare 7 | Serenity Systems eComStation 1<br/>Serenity Systems eComStation 2
Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9 | SUSE Linux Enter prise 10<br/> SUSE Linux Enter prise 11<br/>SUSE Linux Enter prise 12<br/>SUSE Linux Enter prise 8/9<br/>SUSE Linux Enter prise 11<br/>SUSE openSUSE
**U-Z** |
Ubuntu Linux | VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6
Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003 | Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Drempel waarde voor Windows Server<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Geïmporteerde servers naar Azure Migrate: Server evaluatie met CSV.
> * Een evaluatie gemaakt en geëvalueerd

Implementeer nu [een apparaat](./migrate-appliance.md) voor nauw keurige evaluaties en verzamel servers in groepen voor een diep gaande beoordeling met behulp van [afhankelijkheids analyse](./concepts-dependency-visualization.md).
