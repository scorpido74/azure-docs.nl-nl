---
title: VMware-VM's evalueren met Azure Migrate-serverevaluatie
description: Hier wordt beschreven hoe u met behulp van Azure Migrate-serverevaluatie on-premises VMware-VM's kunt evalueren voor migratie naar Azure.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: abd8aafebe9cc02b8ee88cce88616389c82af83f
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514098"
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
2. Onder **Services** selecteert u **Azure Migrate**.
3. Selecteer in **Overzicht** onder **Servers ontdekken, evalueren en migreren** de optie **Servers evalueren en migreren**.

   ![Knop om servers te evalueren en te migreren](./media/tutorial-assess-vmware/assess-migrate.png)

4. Selecteer in **Aan de slag** de optie **Hulpprogramma's toevoegen**.
5. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.     
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Vakken voor projectnaam en regio](./media/tutorial-assess-vmware/migrate-project.png)

7. Selecteer **Next**.
8. In **Evaluatieprogramma selecteren** selecteert u **Azure Migrate: Serverevaluatie** > **Volgende**.

   ![Selectie voor het hulpprogramma voor serverevaluatie](./media/tutorial-assess-vmware/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Controleer in **Beoordelen en hulpprogramma's toevoegen** de instellingen en selecteer **Hulpprogramma's toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.

## <a name="set-up-the-azure-migrate-appliance"></a>Het Azure Migrate-apparaat instellen

Azure Migrate-serverevaluatie maakt gebruik van een lichtgewicht Azure Migrate-apparaat. Het apparaat voert VM-detectie uit en verzendt metagegevens en prestatiegegevens van de virtuele machine naar Azure Migrate. Het apparaat kan op verschillende manieren worden ingesteld.

- Ingesteld op een virtuele VMware-machine met behulp van een gedownloade OVA-sjabloon. **Dit is de methode die in deze zelfstudie wordt gebruikt.**
- Ingesteld op een VMware-VM of fysieke machine met een PowerShell-installatiescript. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u een virtuele machine niet kunt instellen met behulp van een OVA-sjabloon of als u zich in Azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of er verbinding kan worden gemaakt met Azure Migrate-serverevaluatie, configureert u het voor de eerste keer en registreert u het bij het Azure Migrate-project.


### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate-projectsleutel genereren

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** selecteert u **Detecteren**.
2. In **Machines ontdekken** > **Zijn de machines gevirtualiseerd?** selecteert u **Ja, met VMware vSphere-hypervisor**.
3. In **1: Azure Migrate-projectsleutel genereren** geeft u een naam op voor het Azure Migrate-apparaat dat u voor de detectie van VMware-VM's wilt instellen. De naam moet alfanumeriek zijn en 14 tekens of minder bevatten.
1. Klik op **Sleutel genereren** om de vereiste Azure-resources te gaan maken. Sluit de pagina Computers detecteren niet tijdens het maken van resources.
1. Nadat de Azure-resources zijn gemaakt, wordt er een **Azure Migrate-projectsleutel** gegenereerd.
1. Kopieer de sleutel, omdat u deze nodig hebt om de registratie van het apparaat tijdens de configuratie te voltooien.

### <a name="download-the-ova-template"></a>De OVA-sjabloon downloaden
In **2: Azure Migrate-apparaat downloaden**, selecteert u het OVA-bestand en klikt u op **Downloaden**. 


   ![Selecties voor Computers detecteren](./media/tutorial-assess-vmware/servers-discover.png)


   ![Selecties voor Sleutel genereren](./media/tutorial-assess-vmware/generate-key-vmware.png)


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
        VMware (11,6 GB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Voor Azure Government:
    
        **Algoritme** | **Downloaden** | **SHA256**
        --- | --- | ---
        VMware (85 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2140337) | 47179f47eba2842337bbe533c424dd1da56baccdcf68b1d87b71a5a4280108c2


### <a name="create-the-appliance-vm"></a>Het VM-apparaat maken

Importeer het gedownloade bestand en maak een virtuele machine:

1. Selecteer in de vSphere-clientconsole de optie **Bestand** > **OVF-sjabloon implementeren**.

   ![Menuopdracht voor het implementeren van een OVF-sjabloon](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Geef in de wizard OVF-sjabloon implementeren > **Bron** de locatie van het OVA-bestand op.
3. Geef bij **Naam** en **Locatie** een beschrijvende naam op voor de virtuele machine. Selecteer het inventarisobject waarin de VM wordt gehost.
4. Geef bij **Host/Cluster** de host of het cluster op waarop de VM wordt uitgevoerd.
5. Geef bij **Opslag** de opslaglocatie voor de VM op.
6. Geef in **Schijfindeling** het schijftype en de schijfgrootte op.
7. Geef bij **Netwerktoewijzing** het netwerk op waarmee de VM verbinding maakt. Het netwerk heeft internetconnectiviteit nodig om metagegevens naar Azure Migrate-serverevaluatie te verzenden.
8. Controleer en bevestig de instellingen en selecteer vervolgens **Voltooien**.

## <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat de apparaat-VM verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.

### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

> [!NOTE]
> Als u het apparaat instelt met behulp van een [PowerShell-script](deploy-appliance-script.md) in plaats van de gedownloade OVA, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in de vSphere-clientconsole met de rechtermuisknop op de VM en selecteer **Console openen**.
2. Geef de taal, de tijdzone en een wachtwoord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met de VM en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad van het apparaat door de snelkoppeling naar de app te selecteren.
1. Accepteer de **licentievoorwaarden** en lees de informatie van derden.
1. Ga als volgt te werk in de web-app > **Vereisten instellen**:
   - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine een proxy gebruikt:
     - Klik op **Proxy instellen** en geef het proxyadres (in het formulier http://ProxyIPAddress of http://ProxyFQDN) ) en de controlepoort op.
     - Geef referenties op als de proxy verificatie nodig heeft.
     - Alleen HTTP-proxy wordt ondersteund.
     - Als u proxydetails hebt toegevoegd of de proxy en/of de verificatie hebt uitgeschakeld, klikt u op **Opslaan** om de connectiviteitscontrole opnieuw te activeren.
   - **Tijdsynchronisatie**: de tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat detectie goed werkt.
   - **Updates installeren**: het apparaat zorgt ervoor dat de meest recente updates zijn geïnstalleerd. Als de controle is voltooid, kunt u op **Apparaatservices weergeven** klikken om de status en versies te zien van de onderdelen die op het apparaat worden uitgevoerd.
   - **VDDK installeren**: het apparaat controleert of VMware vSphere Virtual Disk Development Kit (VDDK) is geïnstalleerd. Als dat niet het geval is, downloadt u VDDK 6.7 van VMware en extraheert u de inhoud van het gedownloade zipbestand naar de opgegeven locatie op het apparaat. Zie voor meer informatie de **installatie-instructies**.

     Azure Migrate-servermigratie gebruikt de VDDK om computers te repliceren tijdens migratie naar Azure. 
1. Als u wilt, kunt u **de vereisten op elk gewenst moment opnieuw uitvoeren** tijdens de configuratie van het apparaat om te controleren of het apparaat nog steeds voldoet aan alle vereisten.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Plak de **Azure Migrate-projectsleutel** die u in de portal hebt gekopieerd. Als u de sleutel niet hebt, gaat u naar **Serverevaluatie > Detecteren > Bestaande apparaten beheren**, selecteert u de naam van het apparaat die u hebt ingevoerd op het moment dat de sleutel werd gegenereerd en kopieert u de bijbehorende sleutel.
1. Klik op **Aanmelden**. Er wordt een Azure-aanmeldingsprompt geopend in een nieuw browsertabblad. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
1. Meld u op het nieuwe tabblad aan met de gebruikersnaam en het wachtwoord van Azure.
   
   Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app. 
4. Als het Azure-gebruikersaccount dat wordt gebruikt voor logboekregistratie de juiste [machtigingen ](tutorial-prepare-vmware.md#prepare-azure) heeft voor de Azure-resources die tijdens het genereren van de sleutel zijn gemaakt, wordt de registratie van het apparaat gestart.
1. Nadat het apparaat is geregistreerd, kunt u de registratiedetails zien door op **Details weergeven** te klikken.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Het apparaat moet verbinding maken met vCenter Server om de configuratie- en prestatiegegevens van de virtuele machines te detecteren.

1. In **Stap 1: Geef referenties voor vCenter Server op**, klik op **Referenties toevoegen** om een beschrijvende naam voor de referenties op te geven, voeg een **gebruikersnaam** en een **wachtwoord** toe voor het VCenter Server-account dat door het apparaat wordt gebruikt voor het detecteren van VM's op de instantie van vCenter Server.
    - U moet een account met de vereiste machtigingen hebben ingesteld in de [vorige zelfstudie](tutorial-prepare-vmware.md#set-up-permissions-for-assessment).
    - Als u het detectiebereik wilt beperken tot specifieke VMware-objecten (vCenter Server-datacenters, clusters, een map met clusters, hosts, een map met hosts of afzonderlijke VM's), raadpleegt u de instructies in [dit artikel](set-discovery-scope.md) om het account dat wordt gebruikt door Azure Migrate te begrenzen.
1. In **Stap 2: vCenter Server-gegevens opgeven**, klikt u op **Detectiebron toevoegen** om de beschrijvende naam voor referenties te selecteren in de vervolgkeuzelijst en geeft u het **IP-adres/FQDN** van de vCenter Server-instantie op. U kunt **Poort** op 443 laten staan (de standaardinstelling) of een aangepaste poort opgeven waarop vCenter Server luistert en op **Opslaan** klikken.
1. Wanneer u op Opslaan klikt, probeert het apparaat de verbinding met vCenter Server te valideren met de opgegeven referenties en wordt de **validatiestatus** in de tabel weergegeven voor het IP-adres of de FQDN van vCenter Server.
1. Voordat u de detectie start, kunt u de connectiviteit met vCenter Server altijd **opnieuw valideren**.
1. In **stap 3: VM-referenties opgeven voor het detecteren van geïnstalleerde toepassingen en voor het uitvoeren van afhankelijkheidstoewijzing zonder agent** klikt u op **Referenties toevoegen**en geeft u het besturingssysteem op waarvoor de referenties zijn opgegeven, evenals een beschrijvende naam voor de referenties en een **gebruikersnaam** en **wachtwoord**. Klik vervolgens op **Opslaan**.

    - U kunt hier eventueel referenties toevoegen als u een account hebt gemaakt dat u wilt gebruiken voor de functie voor [toepassingsdetectie](how-to-discover-applications.md) of de [functie voor analyse van afhankelijkheden zonder agent](how-to-create-group-machine-dependencies-agentless.md).
    - Als u deze functies niet wilt gebruiken, klikt u op de schuifregelaar om de stap over te slaan. U kunt de intentie op elk gewenst moment ongedaan maken.
    - Controleer de referenties die nodig zijn voor [toepassingsdetectie](migrate-support-matrix-vmware.md#application-discovery-requirements) of voor [afhankelijkheidsanalyse zonder agent](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).

5. Klik op **Detectie starten** om VM-detectie te starten. Nadat de detectie is gestart, kunt u de detectiestatus voor het IP-adres of de FQDN van vCenter Server controleren in de tabel.

Detectie werkt als volgt:
- Het duurt ongeveer 15 minuten voordat gedetecteerde VM-metagegevens worden weergegeven in de portal.
- Detectie van geïnstalleerde toepassingen, functies en onderdelen kan enige tijd duren. De duur is afhankelijk van het aantal VM's dat wordt gedetecteerd. Voor 500 VM's duurt het ongeveer een uur voordat de inventaris van toepassingen wordt weergegeven in de Azure Migrate-portal.

### <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Na de detectie kunt u controleren of de VM's worden weergegeven in de Azure-portal:

1. Open het Azure Migrate-dashboard.
2. Klik op de pagina **Azure Migrate - Servers** > **Azure Migrate: Serverevaluatie** selecteert u het pictogram dat het aantal voor **Gedetecteerde servers** weergeeft.

## <a name="set-up-an-assessment"></a>Een evaluatie instellen

U kunt twee soorten evaluaties maken met behulp van Azure Migrate-serverevaluatie:

**Evaluatietype** | **Details**
--- | --- 
**Azure VM** | Evaluaties om uw on-premises servers te migreren naar virtuele Azure-machine. <br/><br/> U kunt uw on-premises [VMware-VM's](how-to-set-up-appliance-vmware.md), [Hyper-V-VM's](how-to-set-up-appliance-hyper-v.md) en [fysieke servers](how-to-set-up-appliance-physical.md) evalueren voor migratie naar Azure met dit evaluatietype. [Meer informatie](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Evaluaties om uw on-premises servers te migreren naar [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> U kunt uw on-premises [VMware-VM’s](how-to-set-up-appliance-vmware.md) evalueren voor migratie naar Azure VMware Solution (AVS) met dit evaluatietype. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)

Server Assessment biedt twee opties voor criteria voor het aanpassen van de grootte:

**Criteria voor het aanpassen van de grootte** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties die aanbevelingen doen op basis van verzamelde prestatiegegevens | **Azure-VM-evaluatie**: Aanbeveling voor VM-grootte is gebaseerd op verbruiksgegevens voor CPU en geheugen.<br/><br/> Aanbeveling voor schijftype (standaard HDD/SSD of premium-beheerde schijven) is gebaseerd op de IOPS en doorvoer van de on-premises schijven.<br/><br/> **AVS-evaluatie (Azure VMware Solution)** : Aanbeveling voor AVS-knooppunten is gebaseerd op verbruiksgegevens voor CPU en geheugen.
**As-is on-premises** | Evaluaties die geen gebruik maken van prestatiegegevens voor aanbevelingen. | **Azure-VM-evaluatie**: Aanbeveling voor VM-grootte is gebaseerd op de on-premises VM-grootte<br/><br> De aanbeveling voor het schijftype is gebaseerd op de instelling die u selecteert bij het opslagtype voor de evaluatie.<br/><br/> **AVS-evaluatie (Azure VMware Solution)** : Aanbeveling voor AVS-knooppunten is gebaseerd op de on-premises VM-grootte.

## <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer als volgt een *Azure VM-evaluatie* uit:

1. Bekijk de [best practices](best-practices-assessment.md) voor het maken van evaluaties.
2. Op het tabblad **Servers** op de tegel **Azure Migrate: Serverevaluatie** selecteert u **Evalueren**.

   ![Locatie van de knop Evalueren](./media/tutorial-assess-vmware/assess.png)

3. Selecteer bij **Servers evalueren** het evaluatietype 'Azure VM', kies de detectiebron en geef de evaluatienaam op.

    ![Basisprincipes evaluatie](./media/tutorial-assess-vmware/assess-servers-azurevm.png)
 
4. Selecteer **Alles weergeven** en controleer vervolgens de evaluatie-eigenschappen.

   ![Evaluatie-eigenschappen](./media/tutorial-assess-vmware/view-all.png)

5. Klik op **volgende** om **machines te selecteren om te evalueren**. In **Een groep selecteren of maken** selecteert u **Nieuwe maken** en geeft u een groepsnaam op. Een groep verzamelt een of meer VM's voor evaluatie.
6. Selecteer in **Machines toevoegen aan de groep** de VM's die aan de groep moeten worden toegevoegd.
7. Klik op **volgende** en **Controleren en evaluatie maken** om de details van de evaluatie te controleren.
8. Selecteer **Evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

   ![Servers evalueren](./media/tutorial-assess-vmware/assessment-create.png)

8. Nadat de evaluatie is gemaakt, kunt u deze bekijken in **Servers** > **Azure Migrate: Serverevaluatie** > **Evaluaties**.
9. Selecteer **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.

Als u een **Azure VMware Solution (AVS)-evaluatie** wilt uitvoeren, volgt u de stappen die [hier](how-to-create-azure-vmware-solution-assessment.md) beschreven worden.


## <a name="review-an-assessment"></a>Een evaluatie beoordelen

Een evaluatie beschrijft het volgende:

- **Azure-gereedheid**: Hiermee wordt aangegeven of VM's geschikt zijn voor migratie naar Azure.
- **Schatting maandelijkse kosten**: De geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de VM's in Azure.
- **Schatting maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.

U geeft een evaluatie als volgt weer:

1. Selecteer in **Migratiedoelen** > **Servers** de optie **Evaluaties** in **Azure Migrate: Serverevaluatie**.
2. Selecteer in **Evaluaties** een evaluatie om deze te openen.

   ![Evaluatie-overzicht](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid beoordelen

1. Controleer in **Azure-gereedheid** of de VM's gereed zijn voor migratie naar Azure.
2. Controleer de VM-status:
    - **Gereed voor Azure**: Wordt gebruikt wanneer Azure Migrate een VM-grootte en schattingen voor de kosten aanraadt voor VM's in de evaluatie.
    - **Gereed met voorwaarden**: Geeft problemen en voorgesteld herstel weer.
    - **Niet gereed voor Azure**: Geeft problemen en voorgesteld herstel weer.
    - **Gereedheid onbekend**: Wordt gebruikt wanneer Azure Migrate de gereedheid niet kan evalueren vanwege problemen met de beschikbaarheid van gegevens.

3. Selecteer een status voor **Azure-gereedheid**. U kunt details van de VM-gereedheid weergeven. U kunt ook inzoomen op de details van de VM, met inbegrip van reken-, opslag- en netwerkinstellingen.

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
