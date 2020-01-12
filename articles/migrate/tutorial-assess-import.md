---
title: Servers beoordelen door geïmporteerde server gegevens te gebruiken met Azure Migrate server-evaluatie
description: Hierin wordt beschreven hoe u on-premises servers kunt evalueren voor migratie naar Azure met Azure Migrate server evaluatie met behulp van geïmporteerde gegevens.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 060399952545c903fec8ecf08d99e438883c9fd1
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75902539"
---
# <a name="assess-servers-by-using-imported-data"></a>Servers beoordelen met behulp van geïmporteerde gegevens

In dit artikel wordt uitgelegd hoe u on-premises servers kunt beoordelen met de [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool door meta gegevens van de server te importeren in CSV-indeling (door komma's gescheiden waarden). Voor deze evaluatie methode hoeft u het Azure Migrate apparaat niet in te stellen om een evaluatie te maken. Het is handig als:

- U wilt een snelle, initiële evaluatie maken voordat u het apparaat implementeert.
- U kunt het Azure Migrate apparaat niet in uw organisatie implementeren.
- U kunt geen referenties delen die toegang bieden tot on-premises servers.
- Vanwege beveiligings beperkingen kunt u geen gegevens verzamelen en verzenden die zijn verzameld door het apparaat naar Azure. U kunt de gegevens die u in een geïmporteerd bestand deelt, beheren. Ook zijn veel van de gegevens (bijvoorbeeld IP-adressen opgeven) optioneel.

## <a name="before-you-start"></a>Voordat u begint

Houd rekening met de volgende punten:

- U kunt Maxi maal 20.000 servers in één CSV-bestand toevoegen.
- U kunt Maxi maal 20.000 servers in een Azure Migrate project toevoegen met behulp van CSV.
- U kunt Server gegevens meerdere keren naar de server beoordeling uploaden met behulp van CSV.
- Het verzamelen van toepassings gegevens is handig bij het evalueren van uw on-premises omgeving voor migratie. Server evaluatie voert momenteel echter geen evaluatie op toepassings niveau uit of zet toepassingen bij het maken van een evaluatie.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Stel een Azure Migrate project in.
> * Vul een CSV-bestand in met server gegevens.
> * Importeer het bestand om Server gegevens toe te voegen aan de server evaluatie.
> * Een evaluatie maken en bekijken.

> [!NOTE]
> Zelf studies laten u het eenvoudigste installatiepad voor een scenario zien, zodat u snel een taal concept kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de hand leidingen voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="set-azure-permissions-for-azure-migrate"></a>Azure-machtigingen instellen voor Azure Migrate

Uw Azure-account moet machtigingen hebben om een Azure Migrate project te maken.

1. Open in het Azure Portal het abonnement en selecteer **toegangs beheer (IAM)** .
2. In **toegang controleren**, zoek het relevante account en selecteer dit om de machtigingen weer te geven.
3. Zorg ervoor dat u de machtigingen **Inzender** of **eigenaar** hebt.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samen werken om de rol toe te wijzen.

## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate project instellen

Een nieuw Azure Migrate project instellen:

1. Zoek in de Azure Portal in **alle services**naar **Azure migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Selecteer in **overzicht**onder **servers detecteren, evalueren en migreren**de optie **servers beoordelen en migreren**.

    ![Servers detecteren en beoordelen](./media/tutorial-assess-import/assess-migrate.png)

4. Selecteer **hulp programma (s) toevoegen**in **aan de slag**.
5. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
6. Geef in **Project Details**de project naam en het geografie op waarin u het project wilt maken. Voor meer informatie:

    - [Ondersteunde geographs](migrate-support-matrix.md#supported-geographies)bekijken. Het geografische gebied voor het project wordt alleen gebruikt om de metagegevens op te slaan die worden verzameld van on-premises virtuele machines.
    - Wanneer u een migratie uitvoert, kunt u elke gewenste doelregio selecteren.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-import/migrate-project.png)

7. Selecteer **Next**.
8. Selecteer in **hulp programma voor beoordeling selecteren**de optie **Azure migrate: Server evaluatie** > **volgende**.

    ![Een Azure Migrate-evaluatie maken](./media/tutorial-assess-import/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Controleer de instellingen in **hulp middelen voor beoordeling en toevoegen**en selecteer vervolgens **hulp middelen toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. Vervolgens wordt u naar de project pagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.

## <a name="prepare-the-csv"></a>De CSV voorbereiden

De CSV-sjabloon downloaden en er Server gegevens aan toevoegen.

### <a name="download-the-template"></a>De sjabloon downloaden

1. In **migratie doelen** > **servers** > **Azure migrate: Server evaluatie**selecteert u **detecteren**.
2. Selecteer in **computers detecteren**de optie **importeren met CSV**.
3. Selecteer **downloaden** om de CSV-sjabloon te downloaden. U kunt [deze ook rechtstreeks downloaden](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![CSV-sjabloon downloaden](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Server gegevens toevoegen

Verzamel Server gegevens en voeg deze toe aan het CSV-bestand.

- Als u gegevens wilt verzamelen, kunt u deze exporteren vanuit hulpprogram ma's die u gebruikt voor on-premises server beheer, zoals VMware vSphere of uw CMDB (Configuration Management data base).
- Down load ons [voorbeeld bestand](https://go.microsoft.com/fwlink/?linkid=2108405)om voorbeeld gegevens te bekijken.

De volgende tabel bevat een overzicht van de bestands velden die moeten worden ingevuld:

**Veld naam** | **Ingevuld** | **Details**
--- | --- | ---
**Servernaam** | Ja | U kunt het beste de Fully Qualified Domain Name (FQDN) opgeven.
**IP-adres** | Nee | Server adres.
**Kernen** | Ja | Het aantal processor kernen dat aan de server is toegewezen.
**Geheugen** | Ja | Het totale RAM-geheugen (in MB) dat aan de server is toegewezen.
**Naam van besturings systeem** | Ja | Besturings systeem van de server.
**Versie van het besturingssysteem** | Nee | Versie van het besturings systeem van de server.
**Aantal schijven** | Nee | Niet nodig als er details van de afzonderlijke schijf worden gegeven.
**Grootte van schijf 1**  | Nee | De maximale grootte van de schijf, in GB.<br/>U kunt Details voor meer schijven toevoegen door [kolommen toe te voegen](#add-multiple-disks) aan de sjabloon. U kunt Maxi maal acht schijven toevoegen.
**Schijf 1 read OPS** | Nee | Lees bewerkingen op de schijf per seconde.
**Write OPS schijf 1** | Nee | Schrijf bewerkingen op de schijf per seconde.
**Schijf 1 Lees doorvoer** | Nee | Gegevens die per seconde van de schijf worden gelezen, in MB per seconde.
**Schrijf doorvoer schijf 1** | Nee | Gegevens geschreven naar schijf per seconde, in MB per seconde.
**Percentage CPU-gebruik** | Nee | Percentage CPU-gebruik.
**Percentage geheugen gebruik** | Nee | Percentage van het gebruikte RAM-geheugen.
**Totaal aantal schijven gelezen OPS** | Nee | Lees bewerkingen per seconde voor de schijf.
**Totaal aantal schijven schrijven OPS** | Nee | Schrijf bewerkingen per seconde voor de schijf.
**Totale Lees doorvoer van schijven** | Nee | Gegevens die van de schijf worden gelezen, in MB per seconde.
**Totale schrijf doorvoer van schijven** | Nee | Gegevens die naar de schijf worden geschreven, in MB per seconde.
**Netwerk in door Voer** | Nee | Gegevens ontvangen door de server, in MB per seconde.
**Netwerk-door Voer** | Nee | Gegevens die door de server worden verzonden, in MB per seconde.
**Type firmware** | Nee | Server firmware. Waarden kunnen "BIOS" of "UEFI" zijn.
**Servertype** | Nee | Waarden kunnen fysiek of virtueel zijn.
**Hypervisor** | Nee | Hyper Visor waarop een machine wordt uitgevoerd. <br/> Waarden kunnen "VMware", "Hyper-V", "xen", "AWS", "GCP" of "other" zijn.
**Versie nummer van Hyper Visor** | Nee | Versie van Hyper Visor.
**ID van virtuele machine** | Nee | VM-id. Dit is de **InstanceUUid** -waarde voor een VMware VCENTER-VM of de **hyper-v-VM-id** voor hyper-v.
**Virtual Machine Manager-ID** | Nee | Dit is de **InstanceUUid** -waarde voor VMware vCenter. Het is niet nodig voor Hyper-V.
**MAC-adres**| Nee | MAC-adres van de server.
**BIOS-ID** | Nee | Server-BIOS-ID.
**Aangepaste Server-ID** | Nee | Lokale, unieke Server-ID on-premises. <br/> Nuttig voor het bijhouden van de geïmporteerde server op lokale ID.
**Naam toepassing 1** | Nee | Naam van de werk belasting die wordt uitgevoerd op de server.<br/>U kunt Details voor meer apps toevoegen door [kolommen toe te voegen](#add-multiple-applications) aan de sjabloon. U kunt Maxi maal vijf toepassingen toevoegen.
**Type toepassing 1** | Nee | Type werk belasting die wordt uitgevoerd op de server
**Versie van toepassing 1** | Nee | De versie van de werk belasting die wordt uitgevoerd op de server.
**Verval datum van de licentie voor toepassing 1** | Nee | De licentie voor het vervallen van de werk belasting (indien van toepassing).
**Bedrijfs eenheid** | Nee | De bedrijfs eenheid waartoe de server behoort.
**Zakelijke eigenaar** | Nee | Eigenaar van Business Unit.
**Naam van zakelijke toepassing** | Nee | De naam van de toepassing waartoe de app behoort.
**Locatie** | Nee | Data Center waarin de server zich bevindt.
**Datum server buiten gebruik stellen** | Nee | Datum buiten gebruik stellen van de fysieke server of van de onderliggende fysieke server van de virtuele server.

### <a name="add-operating-systems"></a>Besturings systemen toevoegen

Beoordeling herkent specifieke namen van besturings systemen. Een naam die u opgeeft, moet exact overeenkomen met een van de teken reeksen in de [lijst met ondersteunde namen](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Meerdere schijven toevoegen

De sjabloon biedt standaard velden voor de eerste schijf. U kunt vergelijk bare kolommen voor Maxi maal acht schijven toevoegen.

Als u bijvoorbeeld alle velden voor een tweede schijf wilt opgeven, voegt u deze kolommen toe:

- Grootte van schijf 2
- Schijf 2 OPS lezen
- Schijf 2-schrijf bewerkingen
- Schijf 2 Lees doorvoer
- Schrijf doorvoer schijf 2

### <a name="add-multiple-applications"></a>Meerdere toepassingen toevoegen

De sjabloon biedt velden voor één toepassing. U kunt soort gelijke kolommen toevoegen voor Maxi maal vijf apps.  

Als u bijvoorbeeld alle velden voor een tweede app wilt opgeven, voegt u deze kolommen toe:

- Naam toepassing 2
- Type toepassing 2
- Versie van toepassing 2
- Verval datum toepassing 2-licentie

> [!NOTE]
> App-gegevens zijn handig voor het evalueren van uw on-premises omgeving voor migratie. Azure Migrate server-evaluatie voert momenteel echter geen evaluatie op app-niveau uit of zet apps in het account bij het maken van een evaluatie.

## <a name="import-the-server-information"></a>De server gegevens importeren

Nadat u informatie aan de CSV-sjabloon hebt toegevoegd, importeert u de servers in de server evaluatie.

1. Ga in Azure Migrate naar de voltooide sjabloon op de **computer detecteren**.
2. Selecteer **Importeren**.
3. De import status wordt weer gegeven.
    - Als er waarschuwingen worden weer gegeven in de status, kunt u deze herstellen of door gaan zonder ze te adresseren.
    - Als u de nauw keurigheid van de beoordeling wilt verbeteren, kunt u de server gegevens verbeteren, zoals wordt voorgesteld in waarschuwingen
    - Als u waarschuwingen wilt weer geven en herstellen, selecteert u **waarschuwings Details downloaden. CSV**. Met deze bewerking wordt het CSV-bestand gedownload met de waarschuwingen opgenomen. Bekijk de waarschuwingen en Los zo nodig problemen op.
    - Als fouten worden weer gegeven in de status zodat de import status **mislukt**, moet u deze fouten oplossen voordat u kunt door gaan met het importeren:
        1. Down load de CSV, die nu fout gegevens bevat.
        1. Controleer de fouten en los ze op. 
        1. Upload het gewijzigde bestand opnieuw.
4. Wanneer de status van importeren is **voltooid**, zijn de gegevens van de server geïmporteerd.

## <a name="update-server-information"></a>Server gegevens bijwerken

U kunt de gegevens voor een server bijwerken door de gegevens voor de server opnieuw te importeren met dezelfde **Server naam**. U kunt het veld **Server naam** niet wijzigen. Het verwijderen van servers wordt momenteel niet ondersteund.

## <a name="verify-servers-in-the-portal"></a>Servers in de portal controleren

Controleren of de servers worden weer gegeven in de Azure Portal na detectie:

1. Open het Azure Migrate dash board.
2. Selecteer op de pagina **Azure migrate-Servers** > **Azure migrate: Server evaluatie** het pictogram dat het aantal voor **gedetecteerde servers**weergeeft.
3. Selecteer het tabblad **op basis van import** .

## <a name="set-up-and-run-an-assessment"></a>Een evaluatie instellen en uitvoeren

U kunt twee soorten evaluaties maken met behulp van server evaluatie.

**Beoordelings type** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van de opgegeven prestatie gegevens waarden. | **Aanbevolen VM-grootte**: op basis van gegevens van CPU en geheugen gebruik.<br/><br/> **Aanbevolen schijf type (Standard of Premium Managed disk)** : op basis van de invoer/uitvoer per seconde (IOPS) en de door Voer van de on-premises schijven.
**Als on-premises** | Evaluaties op basis van on-premises grootte. | **Aanbevolen VM-grootte**: op basis van de opgegeven server grootte.<br/><br> **Aanbevolen schijf type**: op basis van de opslag type-instelling die u voor de evaluatie selecteert.

Een evaluatie uitvoeren:

1. Bekijk de [Aanbevolen procedures](best-practices-assessment.md) voor het maken van evaluaties.
2. Selecteer op het tabblad **servers** in de tegel **Azure migrate: Server bepaling** de optie **evalueren**.

    ![Beoordelen](./media/tutorial-assess-physical/assess.png)

3. Geef in **servers beoordelen**een naam op voor de evaluatie.
4. Selecteer in **detectie bron**de optie **machines die zijn toegevoegd via importeren in azure migrate**.
5. Selecteer **alles weer geven** om de eigenschappen van de evaluatie te bekijken.

    ![Eigenschappen van beoordeling](./media/tutorial-assess-physical/view-all.png)

6. In **een groep selecteren of maken**, selecteert u **nieuwe maken**en geeft u een groeps naam op. Een groep verzamelt een of meer Vm's samen voor evaluatie.
7. Selecteer op **computers toevoegen aan de groep de**optie servers die aan de groep moeten worden toegevoegd.
8. Selecteer **evaluatie maken** om de groep te maken en voer vervolgens de evaluatie uit.

    ![Een evaluatie maken](./media/tutorial-assess-physical/assessment-create.png)

9. Nadat de evaluatie is gemaakt, bekijkt u deze in **Servers** > **Azure migrate: Server evaluatie** > - **evaluaties**.
10. Selecteer **evaluatie exporteren** om deze te downloaden als een micro soft Excel-bestand.

## <a name="review-an-assessment"></a>Een evaluatie controleren

Een evaluatie beschrijft:

- **Azure-gereedheid**: of servers geschikt zijn voor migratie naar Azure.
- **Schatting maandelijkse kosten**: geschatte maandelijkse reken-en opslag kosten voor het uitvoeren van de servers in Azure.
- **Schatting maandelijkse opslag kosten**: geschatte kosten voor schijf opslag na migratie.

### <a name="view-an-assessment"></a>Een evaluatie weer geven

1. In **migratie doelen** > **servers**selecteert u **evaluaties** in **Azure migrate: Server evaluatie**.
2. Selecteer in **beoordelingen**een evaluatie om deze te openen.

    ![Evaluatie samenvatting](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

1. Bepaal in **Azure Readiness**of de servers gereed zijn voor migratie naar Azure.
2. Controleer de status:
    - **Gereed voor Azure**: Azure migrate RAADT een VM-grootte en schattingen voor de kosten aan voor vm's in de evaluatie.
    - **Klaar met voor waarden**: toont problemen en voorgestelde herstel.
    - **Niet gereed voor Azure**: toont problemen en voorgestelde herbemiddeling.
    - **Gereedheid onbekend**: Azure migrate kan de gereedheid niet beoordelen vanwege problemen met de beschik baarheid van gegevens.

3. Selecteer een **Azure Readiness** -status. U kunt details over de server voorbereiding bekijken en inzoomen op de details van de server, inclusief compute-, opslag-en netwerk instellingen.

### <a name="review-cost-details"></a>Details van kosten bekijken

In deze weer gave ziet u de geschatte berekenings-en opslag kosten voor het uitvoeren van Vm's in Azure. U kunt het volgende doen:

- Bekijk de maandelijkse reken-en opslag kosten. De kosten worden geaggregeerd voor alle servers in de geraamde groep.

    - Schattingen van kosten zijn gebaseerd op de grootte aanbevelingen voor een machine en de schijven en eigenschappen.
    - De geschatte maandelijkse kosten voor Compute en opslag worden weer gegeven.
    - De kosten raming is voor het uitvoeren van de on-premises servers als IaaS-Vm's (Infrastructure-as-a-Service). De evaluatie van de server houdt geen rekening met PaaS (platform-as-a-Service) of SaaS-kosten (Software-as-a-Service).

- Bekijk de maandelijkse schattingen voor de opslag kosten. In deze weer gave worden de geaggregeerde opslag kosten voor de geraamde groep weer gegeven, verdeeld over verschillende typen opslag schijven.
- Inzoomen om de details van specifieke Vm's weer te geven.

> [!NOTE]
> Vertrouwens classificaties worden niet toegewezen aan beoordelingen van servers die worden geïmporteerd in Server evaluatie met behulp van CSV.

## <a name="supported-operating-system-names"></a>Ondersteunde namen van besturings systemen

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A-H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
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
      **I-R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM-BESTURINGS SYSTEEM/2
   :::column-end:::
   :::column span="":::
      opdracht
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
      **S-T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO Openserver 5<br/>
      SCO Openserver 6<br/>
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
      SUSE Linux Enter prise 10<br/>
      SUSE Linux Enter prise 11<br/>
      SUSE Linux Enter prise 12<br/>
      SUSE Linux Enter prise 8/9<br/>
      SUSE Linux Enter prise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U-Z**
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
      Drempel waarde voor Windows Server<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Geïmporteerde servers in Azure Migrate: Server analyse met behulp van CSV.
> * Een evaluatie gemaakt en geëvalueerd.

Implementeer nu [een apparaat](./migrate-appliance.md) voor nauw keurige evaluaties en verzamel servers in groepen voor een diep gaande beoordeling door gebruik te maken van [afhankelijkheids analyse](./concepts-dependency-visualization.md).
