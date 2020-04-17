---
title: Servers beoordelen met behulp van geïmporteerde servergegevens met Azure Migrate Server Assessment
description: Beschrijft hoe u on-premises servers beoordelen op migratie naar Azure met Azure Migrate Server Assessment met behulp van geïmporteerde gegevens.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 9e6b451901da00243c458c06d51ba19fb64c0fdc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530450"
---
# <a name="assess-servers-by-using-imported-data"></a>Servers beoordelen met behulp van geïmporteerde gegevens

In dit artikel wordt uitgelegd hoe u on-premises servers beoordeelt met het azure [migrate: serverassessmenttool](migrate-services-overview.md#azure-migrate-server-assessment-tool) door servermetagegevens te importeren in de CSV-indeling (comma-separate values). Voor deze beoordelingsmethode hoeft u het Azure Migrate-toestel niet in te stellen om een beoordeling te maken. Het is handig als:

- U wilt een snelle, eerste beoordeling maken voordat u het apparaat implementeert.
- U het Azure Migrate-toestel niet implementeren in uw organisatie.
- U geen referenties delen die toegang bieden tot on-premises servers.
- Beveiligingsbeperkingen voorkomen dat u gegevens verzamelt en verzendt die door het toestel naar Azure worden verzameld. U de gegevens beheren die u deelt in een geïmporteerd bestand. Ook veel van de gegevens (bijvoorbeeld het verstrekken van IP-adressen) is optioneel.

## <a name="before-you-start"></a>Voordat u begint

Wees je bewust van deze punten:

- U maximaal 20.000 servers toevoegen in één CSV-bestand.
- U maximaal 20.000 servers toevoegen aan een Azure Migrate-project met CSV.
- U servergegevens meerdere keren uploaden naar Serverassessment met CSV.
- Het verzamelen van toepassingsinformatie is handig bij het evalueren van uw on-premises omgeving voor migratie. Serverassessment voert momenteel echter geen beoordeling op toepassingsniveau uit of houdt geen rekening met toepassingen bij het maken van een beoordeling.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Stel een Azure Migrate-project in.
> * Vul een CSV-bestand in met servergegevens.
> * Importeer het bestand om servergegevens toe te voegen aan Serverbeoordeling.
> * Maak en beoordeel een beoordeling.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een proof of concept instellen. Tutorials maken gebruik van standaardopties waar mogelijk, en niet alle mogelijke instellingen en paden weer te geven. Bekijk de handleidingen voor gedetailleerde instructies.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.

## <a name="set-azure-permissions-for-azure-migrate"></a>Azure-machtigingen instellen voor Azure Migreren

Uw Azure-account heeft machtigingen nodig om een Azure Migrate-project te maken.

1. Open het abonnement in de Azure-portal en selecteer **Toegangsbeheer (IAM)**.
2. Zoek **in Toegang controleren**het relevante account en selecteer het om machtigingen weer te geven.
3. Zorg ervoor dat je machtigingen **voor inzender** of **eigenaar** hebt.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, werkt u samen met de eigenaar om de rol toe te wijzen.

## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate-project instellen

Ga als volgende voor een nieuw Azure Migrate-project:

1. Zoek in de Azure-portal in **Alle services**naar **Azure Migreren**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Selecteer in **Overzicht**onder **Servers ontdekken, beoordelen en migreren**de optie Servers beoordelen en **migreren**.

    ![Servers ontdekken en beoordelen](./media/tutorial-assess-import/assess-migrate.png)

4. Selecteer in **Aan de slag**het **gereedschap(en) toevoegen**.
5. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
6. Geef in **PROJECTDETAILS**de projectnaam en de geografie op waarin u het project wilt maken. Voor meer informatie:

    - Bekijk de ondersteunde regio's voor [publieke](migrate-support-matrix.md#supported-geographies-public-cloud) en [overheidswolken.](migrate-support-matrix.md#supported-geographies-azure-government)
    - Wanneer u een migratie uitvoert, kunt u elke gewenste doelregio selecteren.

    ![Een Azure-migratieproject maken](./media/tutorial-assess-import/migrate-project.png)

7. Selecteer **Next**.
8. Selecteer Azure **Migrate: Server Assessment** > **Next**in **Het beoordelingshulpprogramma selecteren.**

    ![Een azure-migratiebeoordeling maken](./media/tutorial-assess-import/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Bekijk in **Controle + hulpprogramma's de**instellingen en selecteer Gereedschappen **toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt dan naar de projectpagina gebracht. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.

## <a name="prepare-the-csv"></a>De CSV voorbereiden

Download de CSV-sjabloon en voeg er servergegevens aan toe.

### <a name="download-the-template"></a>De sjabloon downloaden

1. Selecteer in **Migratiedoelenservers** > **Servers** > **Azure Migreren: Serverbeoordeling**, selecteer **Ontdekken**.
2. Selecteer **Importeren met CSV**in Machines **ontdekken**.
3. Selecteer **Downloaden** om de CSV-sjabloon te downloaden. U [het ook rechtstreeks downloaden.](https://go.microsoft.com/fwlink/?linkid=2109031)

    ![CSV-sjabloon downloaden](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Servergegevens toevoegen

Servergegevens verzamelen en toevoegen aan het CSV-bestand.

- Als u gegevens wilt verzamelen, u deze exporteren vanuit hulpprogramma's die u gebruikt voor on-premises serverbeheer, zoals VMware vSphere of uw configuratiebeheerdatabase (CMDB).
- Als u voorbeeldgegevens wilt bekijken, downloadt u ons [voorbeeldbestand](https://go.microsoft.com/fwlink/?linkid=2108405).

In de volgende tabel worden de bestandsvelden samengevat die moeten worden ingevuld:

**Veldnaam** | **Verplicht** | **Details**
--- | --- | ---
**Servernaam** | Ja | We raden u aan de volledig gekwalificeerde domeinnaam (FQDN) op te geven.
**IP-adres** | Nee | Serveradres.
**Kernen** | Ja | Aantal processorkernen dat aan de server is toegewezen.
**Geheugen** | Ja | Totaal RAM, in MB, toegewezen aan de server.
**Os-naam** | Ja | Server besturingssysteem. <br/> Namen van het besturingssysteem die overeenkomen met of de namen in [deze](#supported-operating-system-names) lijst bevatten, worden herkend door de beoordeling.
**Versie van het besturingssysteem** | Nee | Server-besturingssysteemversie.
**Aantal schijven** | Nee | Niet nodig als er individuele schijfgegevens worden verstrekt.
**Schijf 1-formaat**  | Nee | Maximale grootte van de schijf, in GB.<br/>U details voor meer schijven toevoegen door [kolommen toe](#add-multiple-disks) te voegen aan de sjabloon. U maximaal acht schijven toevoegen.
**Schijf 1-aflezingsops** | Nee | Schijfleesbewerkingen per seconde.
**Schijf 1 schrijfops** | Nee | Schijfschrijfbewerkingen per seconde.
**Leesdoorvoer van schijf 1** | Nee | Gegevens die worden gelezen vanaf de schijf per seconde, in MB per seconde.
**Schrijfdoorvoer van schijf 1** | Nee | Gegevens die naar de schijf per seconde worden geschreven, in MB per seconde.
**CPU-gebruikspercentage** | Nee | Percentage cpu gebruikt.
**Percentage geheugengebruik** | Nee | Percentage ram gebruikt.
**Totaal aantal schijven lezen ops** | Nee | Schijfleesbewerkingen per seconde.
**Totaal schijven schrijven ops** | Nee | Schijfschrijfbewerkingen per seconde.
**Totaal aantal schijven leesdoorvoer** | Nee | Gegevens die vanaf de schijf worden gelezen, in MB per seconde.
**Totaal aantal schrijfdoorvoer van schijven** | Nee | Gegevens die naar de schijf zijn geschreven, in MB per seconde.
**Netwerk in doorvoer** | Nee | Gegevens die door de server worden ontvangen, in MB per seconde.
**Netwerkuitdoorvoer** | Nee | Gegevens die door de server worden verzonden, in MB per seconde.
**Firmwaretype** | Nee | Serverfirmware. Waarden kunnen "BIOS" of "UEFI" zijn.
**MAC-adres**| Nee | Server MAC-adres.


### <a name="add-operating-systems"></a>Besturingssystemen toevoegen

Assessment herkent specifieke namen van het besturingssysteem. Elke naam die u opgeeft, moet exact overeenkomen met een van de tekenreeksen in de [lijst met ondersteunde namen](#supported-operating-system-names).

### <a name="add-multiple-disks"></a>Meerdere schijven toevoegen

De sjabloon bevat standaardvelden voor de eerste schijf. U vergelijkbare kolommen toevoegen voor maximaal acht schijven.

Als u bijvoorbeeld alle velden voor een tweede schijf wilt opgeven, voegt u de volgende kolommen toe:

- Schijf 2-formaat
- Schijf 2-afleesops
- Schijf 2 schrijfops
- Schijf 2 leesdoorvoer
- Schrijfdoorvoer van schijf 2


## <a name="import-the-server-information"></a>De servergegevens importeren

Nadat u informatie aan de CSV-sjabloon hebt toegevoegd, importeert u de servers in Serverbeoordeling.

1. Ga in Azure Migrate in **Machines ontdekken**naar de voltooide sjabloon.
2. Selecteer **Importeren**.
3. De importstatus wordt weergegeven.
    - Als waarschuwingen in de status worden weergegeven, u deze oplossen of doorgaan zonder ze aan te pakken.
    - Om de nauwkeurigheid van de beoordeling te verbeteren, verbetert u de servergegevens zoals voorgesteld in waarschuwingen.
    - Als u waarschuwingen wilt weergeven en oplossen, selecteert u **Waarschuwingsgegevens downloaden . CSV**. Deze bewerking downloadt de CSV met inbegrepen waarschuwingen. Bekijk de waarschuwingen en los problemen op als dat nodig is.
    - Als er fouten in de status worden weergegeven, zodat de importstatus **is mislukt,** moet u deze fouten herstellen voordat u verder gaan met het importeren:
        1. Download de CSV, die nu foutgegevens bevat.
        1. De fouten zo nodig controleren en aanpakken. 
        1. Upload het gewijzigde bestand opnieuw.
4. Wanneer de importstatus is **voltooid,** is de servergegevens geïmporteerd.

## <a name="update-server-information"></a>Servergegevens bijwerken

U de gegevens voor een server bijwerken door de gegevens voor de server opnieuw te importeren met dezelfde **servernaam.** U het veld **Servernaam** niet wijzigen. Het verwijderen van servers wordt momenteel niet ondersteund.

## <a name="verify-servers-in-the-portal"></a>Servers in de portal verifiëren

Ga als volgt te werk om na detectie te controleren of de servers na detectie in de Azure-portal worden weergegeven:

1. Open het Azure Migrate-dashboard.
2. Selecteer op de pagina **Azure Migrate - Servers** > **Azure Migrate: Server Assessment** het pictogram waarop het aantal **gedetecteerde servers**wordt weergegeven.
3. Selecteer het tabblad **Importeren op basis van** invoer.

## <a name="set-up-and-run-an-assessment"></a>Een beoordeling instellen en uitvoeren

U twee typen beoordelingen maken met serverbeoordeling.

**Beoordelingstype** | **Details** | **Gegevens**
--- | --- | ---
**Op prestaties gebaseerd** | Beoordelingen op basis van opgegeven prestatiegegevenswaarden. | **Aanbevolen VM-grootte:** gebaseerd op cpu- en geheugengebruiksgegevens.<br/><br/> **Aanbevolen schijftype (standaard of premium beheerde schijf):** op basis van de input/output per seconde (IOPS) en doorvoer van de on-premises schijven.
**Als on-premises** | Beoordelingen op basis van on-premises dimensionering. | **Aanbevolen VM-grootte:** op basis van de opgegeven servergrootte.<br/><br> **Aanbevolen schijftype:** op basis van de instelling van het opslagtype die u voor de beoordeling selecteert.

Ga als een assessment als een beoordeling:

1. Bekijk de [aanbevolen procedures](best-practices-assessment.md) voor het maken van beoordelingen.
2. Selecteer **op** het tabblad Servers in de tegel **Azure Migreren: Serverbeoordeling** de optie **Beoordelen**.

    ![Evalueren](./media/tutorial-assess-physical/assess.png)

3. Geef **in Servers beoordelen**een naam op voor de beoordeling.
4. Selecteer in **detectiebron** **Machines die via import zijn toegevoegd aan Azure Migreren**.
5. Selecteer **Alles weergeven** om de beoordelingseigenschappen te controleren.

    ![Beoordelingseigenschappen](./media/tutorial-assess-physical/view-all.png)

6. Selecteer in **Een groep selecteren of maken**de optie Nieuw **maken**en geef een groepsnaam op. Een groep verzamelt een of meer VM's voor beoordeling.
7. Selecteer in **Machines toevoegen aan de groep**servers die u aan de groep wilt toevoegen.
8. Selecteer **Beoordeling maken** om de groep te maken en voer de beoordeling uit.

    ![Een evaluatie maken](./media/tutorial-assess-physical/assessment-create.png)

9. Nadat de beoordeling is gemaakt, bekijkt u deze in **Servers** > **Azure Migrate: Server Assessment** > **Assessments**.
10. Selecteer **Beoordeling exporteren** om het te downloaden als een Microsoft Excel-bestand.

## <a name="review-an-assessment"></a>Een beoordeling beoordelen

Een beoordeling beschrijft:

- **Azure-gereedheid**: of servers geschikt zijn voor migratie naar Azure.
- **Schatting van de maandelijkse kosten:** Geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de servers in Azure.
- **Schatting van de maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.

### <a name="view-an-assessment"></a>Bekijk een beoordeling

1. Selecteer In **Servers voor migratiedoelen** > **Servers**de optie **Beoordelingen** in **Azure Migreren: Serverbeoordeling**.
2. Selecteer in **Assessments**een beoordeling om deze te openen.

    ![Evaluatiesamenvatting](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

1. Bepaal in **Azure-gereedheid**of de servers klaar zijn voor migratie naar Azure.
2. Controleer de status:
    - **Klaar voor Azure:** Azure Migrate beveelt in de beoordeling een VM-grootte en kostenramingen voor VM's aan.
    - **Klaar met voorwaarden**: Toont problemen en voorgestelde sanering.
    - **Niet klaar voor Azure:** toont problemen en voorgestelde herstel.
    - **Gereedheid onbekend**: Azure Migrate kan de gereedheid niet beoordelen vanwege problemen met de beschikbaarheid van gegevens.

3. Selecteer een **Azure-gereedheidsstatus.** U de gegevens over de gereedheid van de server bekijken en inzoomen om servergegevens te bekijken, waaronder rekenkracht, opslag en netwerkinstellingen.

### <a name="review-cost-details"></a>Kostendetails bekijken

In deze weergave worden de geschatte reken- en opslagkosten weergegeven voor het uitvoeren van VM's in Azure. U kunt:

- Bekijk de maandelijkse reken- en opslagkosten. De kosten worden geaggregeerd voor alle servers in de beoordeelde groep.

    - Kostenramingen zijn gebaseerd op de grootteaanbevelingen voor een machine en de schijven en eigenschappen ervan.
    - Geschatte maandelijkse kosten voor rekenkracht en opslag worden weergegeven.
    - De kostenraming is voor het uitvoeren van de on-premises servers als Infrastructure-as-a-service (IaaS) VM's. Server Assessment houdt geen rekening met de kosten van platform-as-a-service (PaaS) of software-as-a-service (SaaS).

- Bekijk de maandelijkse opslagkostenramingen. Deze weergave toont geaggregeerde opslagkosten voor de beoordeelde groep, verdeeld over verschillende soorten opslagschijven.
- Inzoomen om details voor specifieke VM's te zien.

> [!NOTE]
> Vertrouwensclassificaties worden niet toegewezen aan beoordelingen van servers die zijn geïmporteerd in Serverbeoordeling met behulp van CSV.

## <a name="supported-operating-system-names"></a>Ondersteunde namen van het besturingssysteem

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A - H**
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
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      Ms-dos
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
      Rode Hoed Fedora
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
      SUSE opentSUSE
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
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Geïmporteerde servers in Azure Migrate: Serverassessment met BEHULP van CSV.
> * Een beoordeling maken en herzien.

Implementeer [nu een toestel](./migrate-appliance.md) voor nauwkeurigere beoordelingen en verzamel servers in groepen voor een diepere beoordeling met behulp van [afhankelijkheidsanalyse.](./concepts-dependency-visualization.md)
