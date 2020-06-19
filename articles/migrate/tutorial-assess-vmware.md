---
title: VMware-VM's evalueren met Azure Migrate-serverevaluatie
description: Hier wordt beschreven hoe u met behulp van Azure Migrate-serverevaluatie on-premises VMware-VM's kunt evalueren voor migratie naar Azure.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 231daff5972e9b2f115df9e6184c43a553f55b83
ms.sourcegitcommit: 99d016949595c818fdee920754618d22ffa1cd49
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84771305"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>VMware-VM’s evalueren met Serverevaluatie

In dit artikel leest u hoe u on-premises virtuele VMware-machines (VM's) kunt evalueren met behulp van het hulpprogramma [Azure Migrate-serverevaluatie](migrate-services-overview.md#azure-migrate-server-assessment-tool).


Deze zelfstudie is de tweede in een reeks die laat zien hoe u VMware-VM's kunt evalueren en migreren naar Azure. In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Azure Migrate-project instellen.
> * Een Azure Migrate-apparaat instellen waarop on-premises opdrachten worden uitgevoerd om VM's te evalueren.
> * Continue detectie van on-premises VM's starten. Het apparaat verzendt configuratie- en prestatiegegevens voor gedetecteerde VM's naar Azure.
> * Gedetecteerde VM's groeperen en de VM-groep evalueren.
> * De evaluatie controleren.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg de artikelen met procedures voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- De eerste zelfstudie in deze reeks [voltooien](tutorial-prepare-vmware.md). Als u dit niet doet, werken de instructies in deze zelfstudie niet.
- In de eerste zelfstudie hebt u het volgende gedaan:
    - [Azure voorbereiden](tutorial-prepare-vmware.md#prepare-azure) om te werken met Azure Migrate.
    - [VMware voorbereiden voor evaluatie](tutorial-prepare-vmware.md#prepare-for-assessment). Dit omvat het controleren van VMware-instellingen en het instellen van een account dat Azure Migrate kan gebruiken om toegang te krijgen tot vCenter Server.
    - [Controleren](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment) wat u nodig hebt om het Azure Migrate-apparaat voor VMware-evaluatie te implementeren.

## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate-project instellen

Ga als volgt te werk om een nieuw Azure Migrate-project in te stellen:

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
1. Onder **Services** selecteert u **Azure Migrate**.
1. Selecteer in **Overzicht** onder **Servers ontdekken, evalueren en migreren** de optie **Servers evalueren en migreren**.

   ![Knop om servers te evalueren en te migreren](./media/tutorial-assess-vmware/assess-migrate.png)

1. Selecteer in **Aan de slag** de optie **Hulpprogramma's toevoegen**.
1. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.     
1. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Vakken voor projectnaam en regio](./media/tutorial-assess-vmware/migrate-project.png)

1. Selecteer **Next**.
1. In **Evaluatieprogramma selecteren** selecteert u **Azure Migrate: Serverevaluatie** > **Volgende**.

   ![Selectie voor het hulpprogramma voor serverevaluatie](./media/tutorial-assess-vmware/assessment-tool.png)

1. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
1. Controleer in **Beoordelen en hulpprogramma's toevoegen** de instellingen en selecteer **Hulpprogramma's toevoegen**.
1. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.

## <a name="set-up-the-azure-migrate-appliance"></a>Het Azure Migrate-apparaat instellen

Azure Migrate-serverevaluatie maakt gebruik van een lichtgewicht Azure Migrate-apparaat. Het apparaat voert VM-detectie uit en verzendt metagegevens en prestatiegegevens van de virtuele machine naar Azure Migrate. Het apparaat kan op verschillende manieren worden ingesteld.

- Ingesteld op een virtuele VMware-machine met behulp van een gedownloade OVA-sjabloon. Dit is de methode die in deze zelfstudie wordt gebruikt.
- Ingesteld op een VMware-VM of fysieke machine met een PowerShell-installatiescript. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u een virtuele machine niet kunt instellen met behulp van een OVA-sjabloon of als u zich in Azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of er verbinding kan worden gemaakt met Azure Migrate-serverevaluatie, configureert u het voor de eerste keer en registreert u het bij het Azure Migrate-project.


### <a name="download-the-ova-template"></a>De OVA-sjabloon downloaden

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** selecteert u **Detecteren**.
1. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met VMWare vSphere-hypervisor**.
1. Selecteer **Downloaden** om het OVA-sjabloonbestand te downloaden.

   ![Selecties voor het downloaden van een OVA-bestand](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>Beveiliging controleren

Controleer of het OVA-bestand veilig is voordat u het implementeert:

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Gebruik de volgende opdracht om de hash voor het OVA-bestand te genereren:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. Controleer de meest recente versie van het apparaat en de hashwaarden:

    - Voor de openbare Azure-cloud:
    
        **Algoritme** | **Downloaden** | **SHA256**
        --- | --- | ---
        VMware (10,9 GB) | [Nieuwste versie](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd

    - Voor Azure Government:
    
        **Algoritme** | **Downloaden** | **SHA256**
        --- | --- | ---
        VMware (63,1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de


### <a name="create-the-appliance-vm"></a>Het VM-apparaat maken

Importeer het gedownloade bestand en maak een virtuele machine:

1. Selecteer in de vSphere-clientconsole de optie **Bestand** > **OVF-sjabloon implementeren**.

   ![Menuopdracht voor het implementeren van een OVF-sjabloon](./media/tutorial-assess-vmware/deploy-ovf.png)

1. Geef in de wizard OVF-sjabloon implementeren > **Bron** de locatie van het OVA-bestand op.
1. Geef bij **Naam** en **Locatie** een beschrijvende naam op voor de virtuele machine. Selecteer het inventarisobject waarin de VM wordt gehost.
1. Geef bij **Host/Cluster** de host of het cluster op waarop de VM wordt uitgevoerd.
1. Geef bij **Opslag** de opslaglocatie voor de VM op.
1. Geef in **Schijfindeling** het schijftype en de schijfgrootte op.
1. Geef bij **Netwerktoewijzing** het netwerk op waarmee de VM verbinding maakt. Het netwerk heeft internetconnectiviteit nodig om metagegevens naar Azure Migrate-serverevaluatie te verzenden.
1. Controleer en bevestig de instellingen en selecteer vervolgens **Voltooien**.

## <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat de apparaat-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.

### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

> [!NOTE]
> Als u het apparaat instelt met behulp van een [PowerShell-script](deploy-appliance-script.md) in plaats van de gedownloade OVA, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in de vSphere-clientconsole met de rechtermuisknop op de VM en selecteer **Console openen**.
1. Geef de taal, de tijdzone en een wachtwoord op voor het apparaat.
1. Open een browser op een computer die verbinding kan maken met de VM en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad van het apparaat door de snelkoppeling naar de app te selecteren.
1. Ga als volgt te werk in de web-app > **Vereisten instellen**:
   - **Licentie**: Accepteer de licentievoorwaarden en lees de informatie van derden.
   - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine een proxy gebruikt:
     - Selecteer **Proxyinstellingen** en geef het proxyadres en de controlepoort op in het formulier http://ProxyIPAddress of http://ProxyFQDN.
     - Geef referenties op als de proxy verificatie nodig heeft.
     - Alleen HTTP-proxy wordt ondersteund.
   - **Tijdsynchronisatie**: de tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat detectie goed werkt.
   - **Updates installeren**: het apparaat zorgt ervoor dat de meest recente updates zijn geïnstalleerd.
   - **VDDK installeren**: het apparaat controleert of VMWare vSphere Virtual Disk Development Kit (VDDK) is geïnstalleerd. Als dat niet het geval is, downloadt u VDDK 6.7 van VMware en extraheert u de inhoud van het gedownloade zipbestand naar de opgegeven locatie op het apparaat.

     Azure Migrate-servermigratie gebruikt de VDDK om computers te repliceren tijdens migratie naar Azure.       

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Selecteer **Aanmelden**. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Meld u op het nieuwe tabblad aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
1. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
1. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt en selecteer vervolgens het project.
1. Geef een naam op voor het apparaat. De naam moet alfanumeriek zijn met 14 tekens of minder.
1. Selecteer **Registreren**.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Het apparaat moet verbinding maken met vCenter Server om de configuratie- en prestatiegegevens van de virtuele machines te detecteren.

### <a name="specify-vcenter-server-details"></a>vCenter Server-gegevens opgeven
1. Geef in **vCenter Server-gegevens opgeven** de naam (FQDN) of het IP-adres van de vCenter Server-instantie op. U kunt de standaardpoort laten staan of een aangepaste poort opgeven waarop vCenter Server luistert.
2. Geef bij **Gebruikersnaam** en **Wachtwoord** de vCenter-accountreferenties op die het apparaat gebruikt om virtuele machines op het vCenter Server-exemplaar te detecteren. 

    - U moet een account met de vereiste machtigingen hebben ingesteld in de [vorige zelfstudie](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Als u het detectiebereik wilt beperken tot specifieke VMware-objecten (vCenter Server-datacenters, clusters, een map met clusters, hosts, een map met hosts of afzonderlijke VM's), raadpleegt u de instructies in [dit artikel](set-discovery-scope.md) om het account dat wordt gebruikt door Azure Migrate te begrenzen.

3. Selecteer **Verbinding valideren** om ervoor te zorgen dat het apparaat verbinding kan maken met vCenter Server.
4. Klik bij **Toepassingen en afhankelijkheden detecteren op VM's** desgewenst op **Referenties toevoegen** en geef het besturingssysteem op waarvoor de referenties relevant zijn, en de gebruikersnaam en het wachtwoord voor de referenties. Klik vervolgens op **Toevoegen**.

    - U kunt hier eventueel referenties toevoegen als u een account hebt gemaakt dat u wilt gebruiken voor de functie voor [toepassingsdetectie](how-to-discover-applications.md) of de [functie voor analyse van afhankelijkheden zonder agent](how-to-create-group-machine-dependencies-agentless.md).
    - Als u deze functies niet gebruikt, kunt u deze instelling overslaan.
    - Controleer de referenties die nodig zijn voor [toepassingsdetectie](migrate-support-matrix-vmware.md#application-discovery-requirements) of voor [analyse zonder agent](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. **Sla op en start detectie** om VM-detectie te starten.

Detectie werkt als volgt:
- Het duurt ongeveer 15 minuten voordat gedetecteerde VM-metagegevens worden weergegeven in de portal.
- Detectie van geïnstalleerde toepassingen, functies en onderdelen kan enige tijd duren. De duur is afhankelijk van het aantal VM's dat wordt gedetecteerd. Voor 500 VM's duurt het ongeveer een uur voordat de inventaris van toepassingen wordt weergegeven in de Azure Migrate-portal.

### <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Na de detectie kunt u controleren of de VM's worden weergegeven in de Azure-portal:

1. Open het Azure Migrate-dashboard.
1. Klik op de pagina **Azure Migrate - Servers** > **Azure Migrate: Serverevaluatie** selecteert u het pictogram dat het aantal voor **Gedetecteerde servers** weergeeft.

## <a name="set-up-an-assessment"></a>Een evaluatie instellen

U kunt twee soorten evaluaties maken met behulp van Azure Migrate-serverevaluatie:

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatiegegevens | **Aanbevolen VM-grootte**: Gebaseerd op verbruiksgegevens voor CPU en geheugen.<br/><br/> **Aanbevolen schijftype (standaard of premium beheerde schijf)** : Op basis van de IOPS en doorvoer van de on-premises schijven.
**Zoals on-premises** | Evaluaties op basis van on-premises grootte aanpassen | **Aanbevolen VM-grootte**: Op basis van de on-premises VM-grootte.<br/><br> **Aanbevolen schijftype**: Op basis van de instelling voor het opslagtype die u voor de evaluatie selecteert.

## <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer als volgt een evaluatie uit:

1. Bekijk de [best practices](best-practices-assessment.md) voor het maken van evaluaties.
1. Op het tabblad **Servers** op de tegel **Azure Migrate: Serverevaluatie** selecteert u **Evalueren**.

   ![Locatie van de knop Evalueren](./media/tutorial-assess-vmware/assess.png)

1. Geef bij **Servers evalueren** een naam op voor de evaluatie.
1. Selecteer **Alles weergeven** en controleer vervolgens de evaluatie-eigenschappen.

   ![Evaluatie-eigenschappen](./media/tutorial-assess-vmware/view-all.png)

1. In **Een groep selecteren of maken** selecteert u **Nieuwe maken** en geeft u een groepsnaam op. Een groep verzamelt een of meer VM's voor evaluatie.
1. Selecteer in **Machines toevoegen aan de groep** de VM's die aan de groep moeten worden toegevoegd.
1. Selecteer **Evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

   ![Servers evalueren](./media/tutorial-assess-vmware/assessment-create.png)

1. Nadat de evaluatie is gemaakt, kunt u deze bekijken in **Servers** > **Azure Migrate: Serverevaluatie** > **Evaluaties**.
1. Selecteer **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.

## <a name="review-an-assessment"></a>Een evaluatie beoordelen

Een evaluatie beschrijft het volgende:

- **Azure-gereedheid**: Hiermee wordt aangegeven of VM's geschikt zijn voor migratie naar Azure.
- **Schatting maandelijkse kosten**: De geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de VM's in Azure.
- **Schatting maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.

U geeft een evaluatie als volgt weer:

1. Selecteer in **Migratiedoelen** > **Servers** de optie **Evaluaties** in **Azure Migrate: Serverevaluatie**.
1. Selecteer in **Evaluaties** een evaluatie om deze te openen.

   ![Evaluatie-overzicht](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid beoordelen

1. Controleer in **Azure-gereedheid** of de VM's gereed zijn voor migratie naar Azure.
1. Controleer de VM-status:
    - **Gereed voor Azure**: Wordt gebruikt wanneer Azure Migrate een VM-grootte en schattingen voor de kosten aanraadt voor VM's in de evaluatie.
    - **Gereed met voorwaarden**: Geeft problemen en voorgesteld herstel weer.
    - **Niet gereed voor Azure**: Geeft problemen en voorgesteld herstel weer.
    - **Gereedheid onbekend**: Wordt gebruikt wanneer Azure Migrate de gereedheid niet kan evalueren vanwege problemen met de beschikbaarheid van gegevens.

1. Selecteer een status voor **Azure-gereedheid**. U kunt details van de VM-gereedheid weergeven. U kunt ook inzoomen op de details van de VM, met inbegrip van reken-, opslag- en netwerkinstellingen.

### <a name="review-cost-details"></a>Gedetailleerde kosten beoordelen

Het evaluatieoverzicht toont de geschatte reken- en opslagkosten om virtuele machines in Azure uit te voeren. De kosten worden geaggregeerd voor alle VM's in de geëvalueerde groep. U kunt inzoomen om de kostendetails voor specifieke VM's te bekijken.

> [!NOTE]
> Schattingen van kosten zijn gebaseerd op de grootte-aanbevelingen voor een machine, de schijven en de eigenschappen ervan. Schattingen zijn voor het uitvoeren van de on-premises VM's als IaaS-VM's. Azure Migrate Serverevaluatie houdt geen rekening met PaaS-of SaaS-kosten.

De geaggregeerde opslagkosten voor de geëvalueerde groep worden gesplitst over verschillende typen opslagschijven. 

### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Azure Migrate-serverevaluatie wijst een betrouwbaarheidsclassificatie toe aan een evaluatie op basis van de prestaties, van één ster (laagste) tot vijf sterren (hoogste).

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-vmware/confidence-rating.png)

De betrouwbaarheidsclassificatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte. De classificatie is op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen:

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

[Meer informatie over de aanbevolen procedures](best-practices-assessment.md#best-practices-for-confidence-ratings) voor betrouwbaarheidsclassificaties.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie stelt u een Azure Migrate apparaat in. U hebt ook een evaluatie gemaakt en beoordeeld.

Ga door naar de derde zelfstudie in de reeks om te leren hoe u VMware-VM's naar Azure migreert met behulp van Azure Migrate Server Migration:

> [!div class="nextstepaction"]
> [Virtuele VMware-machines migreren](./tutorial-migrate-vmware.md)
