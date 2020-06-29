---
title: Fysieke servers beoordelen voor migratie naar Azure met Azure Migrate-serverevaluatie
description: 'Beschrijft hoe u met behulp van de service Azure Migrate: Serverevaluatie on-premises fysieke servers kunt detecteren en evalueren voor migratie naar Azure.'
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: ee88f9058abc89a671fa846a67c22a752f0d05e4
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052205"
---
# <a name="assess-physical-servers-with-azure-migrateserver-assessment"></a>Fysieke servers evalueren met Azure Migrate: serverevaluatie

In dit artikel leest u hoe u on-premises fysieke servers kunt beoordelen met behulp van het Azure Migrate: Serverevaluatie-tool.

[Azure Migrate](migrate-services-overview.md) biedt een hub aan hulpprogramma's waarmee u apps, infrastructuur en workloads op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogramma's en externe aanbiedingen van onafhankelijke softwareleveranciers (ISV’s).

Deze zelfstudie is de tweede in een serie die laat zien hoe u fysieke servers kunt beoordelen en migreren naar Azure. In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een Azure Migrate-project instellen.
> * Stel een Azure Migrate-apparaat in waarop on-premises opdrachten wordt uitgevoerd om fysieke servers te evalueren.
> * Continue detectie van on-premises fysieke servers starten. Het apparaat verzendt configuratie- en prestatiegegevens voor gedetecteerde servers naar Azure.
> * Gedetecteerde servers groeperen en de servergroep evalueren.
> * De evaluatie controleren.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een haalbaarheidstest kunt instellen. Waar mogelijk maken zelfstudies gebruik van standaardopties en niet alle mogelijke instellingen en paden worden weergegeven. Raadpleeg de artikelen met procedures voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

- [Voltooi](tutorial-prepare-physical.md) de eerste zelfstudie in de serie. Als u dit niet doet, werken de instructies in deze zelfstudie niet.
- In de eerste zelfstudie hebt u het volgende gedaan:
    - [Stel de Azure-machtigingen in](tutorial-prepare-physical.md) voor Azure Migrate.
    - [Fysieke servers voorbereiden](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) op evaluatie. De vereisten voor het apparaat moeten worden geverifieerd. U moet ook een account instellen voor detectie van fysieke servers. De vereiste poorten moeten beschikbaar zijn en u moet rekening houden met de URL's die nodig zijn voor toegang tot Azure.




## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate-project instellen

Ga als volgt te werk om een nieuw Azure Migrate-project op te zetten:

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. In **Overzicht**, onder **Servers ontdekken, evalueren en migreren**, klikt u op **Servers evalueren en migreren**.

    ![Servers detecteren en evalueren](./media/tutorial-assess-physical/assess-migrate.png)

4. Klik in **Aan de slag** op **Hulpprogramma's toevoegen**.
5. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.  
6. Geef in **Projectdetails** de projectnaam en het geografische gebied op waarin u het project wilt maken. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).

    - Het geografische gebied voor het project wordt alleen gebruikt om de metagegevens op te slaan die worden verzameld van on-premises servers.
    - Wanneer u een migratie uitvoert, kunt u elke gewenste doelregio selecteren.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-physical/migrate-project.png)


7. Klik op **Volgende**.
8. In **Evaluatieprogramma selecteren** selecteert u **Azure Migrate: Serverevaluatie** > **Volgende**.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-physical/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Controleer in **Evalueren en hulpprogramma's toevoegen** de instellingen en klik op **Hulpmiddelen toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.


## <a name="set-up-the-appliance"></a>Het apparaat instellen

Azure Migrate: Serverevaluatie voert een lichtgewicht toepassing in.

- Dit apparaat voert fysieke serverdetectie uit en verzendt metagegevens en prestatiegegevens van de server naar Azure Migrate Serverevaluatie.
- Om het apparaat in te stellen, moet u het volgende doen:
    - Download een zip-bestand met Azure Migrate-installatiescript uit de Azure-portal.
    - Pak de inhoud uit het zip-bestand uit. Start PowerShell-console met beheerdersbevoegdheden.
    - Voer het PowerShell-script uit om de webtoepassing voor het apparaat te starten.
    - Configureer het apparaat voor het eerst en registreer het bij het Azure Migrate-project.
- U kunt meerdere apparaten instellen voor één Azure Migrate-project. U kunt elk aantal fysieke servers detecteren op alle apparaten. Er kunnen maximaal 250 servers worden gedetecteerd per apparaat.

### <a name="download-the-installer-script"></a>Download het installatiescript

Download het zip-bestand voor het apparaat.

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Serverevaluatie** klikt u op **Ontdekken**.
2. In **Machines ontdekken** > **Zijn uw machines gevirtualiseerd?** selecteert u **Niet gevirtualiseerd/Anders**.
3. Klik op **Downloaden** om het zip-bestand te downloaden.

    ![Installatieprogramma downloaden](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het zip-bestand veilig is voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Gebruik de volgende opdracht om de hash voor het zip-bestand te genereren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Voorbeeld van gebruik voor openbare cloud: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - Voorbeeld van gebruik voor overheidscloud: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Controleer de meest recente versie van het apparaat en de hashwaarden:
    - Voor de openbare cloud:

        **Scenario** | **Downloaden*** | **Hash-waarde**
        --- | --- | ---
        Fysiek (63.1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0

    - Voor Azure Government:

        **Scenario** | **Downloaden*** | **Hash-waarde**
        --- | --- | ---
        Fysiek (63.1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1

### <a name="run-the-azure-migrate-installer-script"></a>Het Azure Migrate-installatiescript uitvoeren

Het installatiescript doet het volgende:

- Installeert agents en een webtoepassing voor detectie en evaluatie van fysieke servers.
- Installeer Windows-rollen, waaronder Windows-activeringsservice, IIS en PowerShell ISE.
- Een herschrijfbare module van IIS downloaden en installeren. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een registersleutel (HKLM) bij met permanente instellingsgegevens voor Azure Migrate.
- Maakt de volgende bestanden onder het pad:
    - **Configuratiebestanden**: %Programdata%\Microsoft Azure\Config
    - **Logboekbestanden**: %Programdata%\Microsoft Azure\Logs

Voer het script als volgt uit:

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat.  Zorg ervoor dat u het script niet uitvoert op een machine op een bestaand Azure Migrate-apparaat.
2. Start PowerShell op de bovenstaande server met beheerdersbevoegdheden (verhoogde bevoegdheden).
3. Wijzig de PowerShell-map in de map waarin de inhoud is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer de volgende opdracht uit om het script uit te voeren met de naam **AzureMigrateInstaller.ps1**:

    - Voor de openbare cloud: ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Voor Azure Government: ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    Met het script wordt de webtoepassing voor het apparaat gestart wanneer deze succesvol is voltooid.

Als u problemen ondervindt, kunt u het script Logboeken openen op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Timestamp</em>.log voor probleemoplossing.

### <a name="verify-appliance-access-to-azure"></a>Apparaattoegang tot Azure controleren

Zorg ervoor dat het apparaat verbinding kan maken met Azure-URL's voor [openbare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls)clouds.


### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

1. Open een browser op een computer die verbinding kan maken met het apparaat en open de URL van de web-app van het apparaat: **https://*apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureaublad door te klikken op de snelkoppeling naar de app.
2. Ga als volgt te werk in de web-app > **Vereisten instellen**:
    - **Licentie**: Accepteer de licentievoorwaarden en lees de informatie van derden.
    - **Connectiviteit**: De app controleert of de server toegang heeft tot internet. Als de server gebruikmaakt van een proxy:
        - Klik op **Proxyinstellingen** en geef het proxyadres en de controlepoort op in het formulier http://ProxyIPAddress of http://ProxyFQDN.
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
    - **Tijdsynchronisatie**: Tijd is geverifieerd. De tijd op het apparaat moet zijn gesynchroniseerd met internettijd zodat serverdetectie goed werkt.
    - **Updates installeren**: Azure Migrate-serverevaluatie controleert of de meest recente updates zijn geïnstalleerd op het apparaat.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Klik op **Aanmelden**. Als dit niet wordt weergegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
2. Meld u aan met uw Azure-referenties op het nieuwe tabblad.
    - Meld u aan met uw gebruikersnaam en wachtwoord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
4. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
5. Geef een naam op voor het apparaat. De naam moet alfanumeriek zijn met 14 tekens of minder.
6. Klik op **Registreren**.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak nu verbinding vanaf het apparaat met de fysieke servers die moeten worden gedetecteerd en start de detectie.

1. Klik op **Referenties toevoegen** om de accountreferenties op te geven die door het apparaat worden gebruikt om servers te detecteren.  
2. Geef het **besturingssysteem**, een beschrijvende naam voor de referenties en de gebruikersnaam en het wachtwoord op. Klik vervolgens op **Toevoegen**.
U kunt voor Windows-en Linux-servers meerdere referenties toevoegen.
4. Klik op **Server toevoegen** en geef serverdetails-FQDN/IP-adres en beschrijvende naam van referenties (één vermelding per rij) op om verbinding te maken met de server.
3. Klik op **Valideren**. Na validatie wordt de lijst met servers weergegeven die kunnen worden gedetecteerd.
    - Als de validatie mislukt voor een server, raadpleegt u de fout door te klikken op het pictogram in de kolom **Status**. Los problemen op en valideer opnieuw.
    - Als u een server wilt verwijderen, selecteert u > **Verwijderen**.
4. Klik na validatie op **Opslaan en detectie starten** om het detectieproces te starten.

De detectie wordt gestart. Het duurt ongeveer 1,5 minuten per server voordat de metagegevens van de gedetecteerde server worden weergegeven in de Azure-portal.

### <a name="verify-servers-in-the-portal"></a>Verifieer servers in de portal

Na de detectie kunt u controleren of de servers worden weergegeven in de Azure-portal.

1. Open het Azure Migrate-dashboard.
2. Klik op de pagina **Azure Migrate - Servers** > **Azure Migrate: Serverevaluatie** op het pictogram dat het aantal voor **Gedetecteerde servers** weergeeft.

## <a name="set-up-an-assessment"></a>Een evaluatie instellen

Er zijn twee soorten evaluaties die u kunt maken met behulp van Azure Migrate: Server-evaluatie.

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatiegegevens | **Aanbevolen VM-grootte**: Gebaseerd op verbruiksgegevens voor CPU en geheugen.<br/><br/> **Aanbevolen schijftype (standaard of premium beheerde schijf)** : Op basis van de IOPS en doorvoer van de on-premises schijven.
**Zoals on-premises** | Evaluaties op basis van on-premises grootte aanpassen. | **Aanbevolen VM-grootte**: Op basis van de on-premises servergrootte<br/><br> **Aanbevolen schijftype**: Op basis van de instelling voor het opslagtype die u voor de evaluatie selecteert.


### <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer als volgt een evaluatie uit:

1. Bekijk de [best practices](best-practices-assessment.md) voor het maken van evaluaties.
2. Op het tabblad **Servers** in de tegel **Azure Migrate: Serverevaluatie**, klikt u op **Evalueren**.

    ![Evalueren](./media/tutorial-assess-physical/assess.png)

2. Geef bij **Servers evalueren** een naam op voor de evaluatie.
3. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Evaluatie-eigenschappen](./media/tutorial-assess-physical/view-all.png)

3. In **Een groep selecteren of maken** selecteert u **Nieuwe maken** en geeft u een groepsnaam op. Een groep verzamelt een of meer servers bij de evaluatie.
4. Selecteer in **Machines toevoegen aan de groep** de servers die aan de groep moeten worden toegevoegd.
5. Klik op **Evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

    ![Een evaluatie maken](./media/tutorial-assess-physical/assessment-create.png)

6. Nadat de evaluatie is gemaakt, kunt u deze bekijken in **Servers** > **Azure Migrate: Serverevaluatie** > **Evaluaties**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.



## <a name="review-an-assessment"></a>Een evaluatie beoordelen

Een evaluatie beschrijft het volgende:

- **Azure-gereedheid**: Hiermee wordt aangegeven of servers geschikt zijn voor migratie naar Azure.
- **Schatting maandelijkse kosten**: De geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de servers in Azure.
- **Schatting maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.

### <a name="view-an-assessment"></a>Een evaluatie weergeven

1. Klik in **Migratiedoelen** >  **Servers**, op **Evaluaties** in **Azure Migrate: Serverevaluatie**.
2. Klik in **Evaluaties** op een evaluatie om deze te openen.

    ![Evaluatie-overzicht](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid beoordelen

1. Controleer in **Azure-gereedheid** of de servers gereed zijn voor migratie naar Azure.
2. Controleer de status:
    - **Gereed voor Azure**: Azure Migrate raadt een VM-grootte en schattingen voor de kosten aan voor VM's in de evaluatie.
    - **Gereed met voorwaarden**: Geeft problemen en voorgesteld herstel weer.
    - **Niet gereed voor Azure**: Geeft problemen en voorgesteld herstel weer.
    - **Gereedheid onbekend**: Wordt gebruikt wanneer Azure Migrate de gereedheid niet kan evalueren door problemen met de beschikbaarheid van gegevens.

2. Klik op een **Azure-gereedheid**-status. U kunt details van de servergereedheid weergeven en inzoomen op de details van de server, inclusief berekenings-, opslag- en netwerkinstellingen.



### <a name="review-cost-details"></a>Gedetailleerde kosten beoordelen

Deze weergave toont de geschatte berekenings- en opslagkosten om virtuele machines in Azure uit te voeren.

1. Controleer de maandelijkse berekenings- en opslagkosten. De kosten worden geaggregeerd voor alle servers in de geëvalueerde groep.

    - Schattingen van kosten zijn gebaseerd op de grootte-aanbevelingen voor een machine en de schijven en eigenschappen.
    - De geschatte maandelijkse kosten voor berekening en opslag worden weergegeven.
    - De schatting van de kosten is voor het uitvoeren van de on-premises servers als IaaS-VM's. Azure Migrate Serverevaluatie houdt geen rekening met PaaS-of SaaS-kosten.

2. U kunt de schatting van de maandelijkse opslagkosten controleren. In deze weergave worden geaggregeerde opslagkosten voor de geëvalueerde groep weergegeven, gesplitst over verschillende typen opslagschijven.
3. U kunt inzoomen om de details voor specifieke servers te bekijken.


### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Wanneer u evaluaties op basis van prestaties uitvoert, wordt er een betrouwbaarheidsclassificatie aan de evaluatie toegewezen.

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-physical/confidence-rating.png)

- Er wordt een classificatie van 1 ster (laagst) tot 5 sterren (hoogst) toegekend.
- De betrouwbaarheidsclassificatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte die de evaluatie geeft.
- De betrouwbaarheidsclassificatie is op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.

De betrouwbaarheidsclassificatie voor een evaluatie zijn als volgt.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

[Meer informatie](best-practices-assessment.md#best-practices-for-confidence-ratings) over de aanbevolen procedures voor betrouwbaarheidsclassificaties.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Azure Migrate-apparaat ingesteld
> * Een evaluatie gemaakt en gecontroleerd

Ga door naar de derde zelfstudie in de serie om te leren hoe u fysieke servers naar Azure migreert met Azure Migrate: Server Migration.

> [!div class="nextstepaction"]
> [Fysieke servers migreren](./tutorial-migrate-physical-virtual-machines.md)
