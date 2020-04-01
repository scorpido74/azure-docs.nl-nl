---
title: Fysieke servers beoordelen op migratie naar Azure met Azure Migrate Server Assessment
description: Beschrijft hoe u on-premises fysieke servers beoordelen op migratie naar Azure met Azure Migrate Server Assessment.
ms.topic: tutorial
ms.date: 11/18/2019
ms.openlocfilehash: c89c731712a625e5f3b7a1a7e9306f6a7480b96b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76990297"
---
# <a name="assess-physical-servers-with-azure-migrate-server-assessment"></a>Fysieke servers beoordelen met Azure Migrate: Serverbeoordeling

In dit artikel ziet u hoe u on-premises fysieke servers beoordelen met behulp van het hulpprogramma Azure Migrate: Server Assessment.

[Azure Migrate](migrate-services-overview.md) biedt een hub met hulpprogramma's waarmee u apps, infrastructuur en workloads ontdekken, beoordelen en migreren naar Microsoft Azure. De hub bevat Azure Migrate-hulpprogramma's en isv-aanbiedingen (independent software vendor) van derden.

Deze zelfstudie is de tweede in een reeks die laat zien hoe fysieke servers naar Azure kunnen worden beoordeeld en gemigreerd. In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Stel een Azure Migrate-project in.
> * Stel een Azure Migrate-toestel in dat on-premises wordt uitgevoerd om fysieke servers te beoordelen.
> * Start continue detectie van on-premises fysieke servers. Het toestel stuurt configuratie- en prestatiegegevens voor gedetecteerde servers naar Azure.
> * Groep ontdekte servers en beoordeel de servergroep.
> * Bekijk de beoordeling.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een proof-of-concept instellen. Tutorials maken gebruik van standaardopties waar mogelijk, en niet alle mogelijke instellingen en paden weer te geven. Bekijk de artikelen How-to voor gedetailleerde instructies.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.


## <a name="prerequisites"></a>Vereisten

- [Voltooi](tutorial-prepare-physical.md) de eerste tutorial in deze serie. Als u dat niet doet, werken de instructies in deze zelfstudie niet.
- Hier is wat je had moeten doen in de eerste tutorial:
    - [Azure-machtigingen instellen](tutorial-prepare-physical.md#prepare-azure) voor Azure Migreren.
    - [Bereid fysieke servers](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) voor op beoordeling. De toestelvereisten moeten worden geverifieerd. U moet ook een account hebben ingesteld voor het detecteren van fysieke servers. Vereiste poorten moeten beschikbaar zijn en u moet op de hoogte zijn van de URL's die nodig zijn voor toegang tot Azure.


## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate-project instellen

Ga als volgt te werk om een nieuw Azure Migrate-project op te zetten:

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. In **Overzicht**, onder **Servers ontdekken, evalueren en migreren**, klikt u op **Servers evalueren en migreren**.

    ![Servers ontdekken en beoordelen](./media/tutorial-assess-physical/assess-migrate.png)

4. Klik in **Aan de slag** op **Hulpprogramma's toevoegen**.
5. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.     
6. Geef in **Projectdetails**de projectnaam en de geografie op waarin u het project wilt maken. Azië, Europa, het Verenigd Koninkrijk en de Verenigde Staten worden ondersteund.

    - De projectgeografie wordt alleen gebruikt om de metagegevens op te slaan die zijn verzameld van on-premises servers.
    - Wanneer u een migratie uitvoert, kunt u elke gewenste doelregio selecteren.

    ![Een Azure-migratieproject maken](./media/tutorial-assess-physical/migrate-project.png)


7. Klik op **Volgende**.
8. Selecteer Azure **Migrate: Server Assessment** > **Next**in **Het beoordelingshulpprogramma selecteren.**

    ![Een Azure-migratieproject maken](./media/tutorial-assess-physical/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Bekijk in **Hulpmiddelen voor controleren + toevoegen**de instellingen en klik op Gereedschappen **toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.


## <a name="set-up-the-appliance"></a>Het apparaat instellen

Azure Migrate: Server Assessment draait een lichtgewicht toestel.

- Dit toestel voert fysieke serverdetectie uit en verzendt servermetagegevens en prestatiegegevens naar Azure Migrate Server Assessment.
- Ga als u het apparaat instelt:
    - Download een zip-bestand met Azure Migrate-installatiescript van de Azure-portal.
    - Haal de inhoud uit het ritsbestand. Start de PowerShell-console met beheerdersbevoegdheden.
    - Voer het PowerShell-script uit om de webtoepassing voor het toestel te starten.
    - Configureer het toestel voor de eerste keer en registreer het met het Azure Migrate-project.
- U meerdere apparaten instellen voor één Azure Migrate-project. In alle apparaten u een willekeurig aantal fysieke servers ontdekken. Per toestel kunnen maximaal 250 servers worden gedetecteerd.

### <a name="download-the-installer-script"></a>Het installatiescript downloaden

Download het zip-bestand voor het apparaat.

1. Klik in **Migration Goals** > **Servers** > **Azure Migrate: Server assessment**op **Ontdekken**.
2. In **Machines** > ontdekken**Worden uw machines gevirtualiseerd?**, klikt u op **Niet gevirtualiseerd/Ander.**
3. Klik **op Downloaden** om het zip-bestand te downloaden.

    ![Installatieprogramma downloaden](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Beveiliging verifiëren

Controleer of het zip-bestand veilig is, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256```

3.  Voor de nieuwste toestelversie moet de gegenereerde hash overeenkomen met deze instellingen.

  **Algoritme** | **Hash-waarde**
  --- | ---
  MD5 | 1e92ede3e87c03bd148e56a708cdd33f
  SHA256 | a3fa78edc8ff8aff9ab5ae6be1b64e6de7b9f475b6542beef114b20bfdac3c

### <a name="run-the-azure-migrate-installer-script"></a>Het installatiescript Azure Migrate uitvoeren

Het installatiescript doet het volgende:

- Installeert agents en een webtoepassing voor het detecteren en beoordelen van fysieke servers.
- Installeer Windows-rollen, waaronder Windows Activation Service, IIS en PowerShell ISE.
- Download en installeert een IIS herschrijfbare module. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Werkt een registersleutel (HKLM) bij met permanente instellingsdetails voor Azure Migrate.
- Hiermee maakt u de volgende bestanden onder het pad:
    - **Config-bestanden**: %ProgramData%\Microsoft Azure\Config
    - **Logboekbestanden**: %ProgramData%\Microsoft Azure\Logs

Voer het script als volgt uit:

1. Haal het ritsbestand uit naar een map op de server die het toestel host.
2. Start PowerShell op de bovenstaande server met beheerdersbevoegdheden (verhoogde bevoegdheid).
3. Wijzig de PowerShell-map in de map waar de inhoud is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script met de naam **AzureMigrateInstaller.ps1** uit door de volgende opdracht uit te voeren:
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
Het script start de webtoepassing van het toestel wanneer deze is voltooid.

In het geval van problemen hebt u toegang tot de scriptlogboeken op C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log voor het oplossen van problemen.

> [!NOTE]
> Voer het azure migrate-installatiescript niet uit op een bestaand Azure Migrate-toestel.

### <a name="verify-appliance-access-to-azure"></a>De toegang tot het toestel tot Azure verifiëren

Controleer of het toestel verbinding kan maken met [Azure-URL's.](migrate-appliance.md#url-access)


### <a name="configure-the-appliance"></a>Het toestel configureren

Stel het apparaat voor de eerste keer in.

1. Open een browser op elke machine die verbinding kan maken met het toestel en open de URL van de web-app van het toestel: **https://*toestelnaam of IP-adres*: 44368**.

   U de app afwisselend vanaf het bureaublad openen door op de snelkoppeling van de app te klikken.
2. Ga als volgt te werk in de web-app > **Vereiste voorwaarden instellen:**
    - **Licentie:** Accepteer de licentievoorwaarden en lees de informatie van derden.
    - **Connectiviteit**: De app controleert of de server toegang tot internet heeft. Als de server een proxy gebruikt:
        - Klik **op Proxy-instellingen**en geef het proxyadres http://ProxyIPAddress http://ProxyFQDNen de luisterpoort op in het formulier of .
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
    - **Tijdsynchronisatie**: De tijd wordt geverifieerd. De tijd op het toestel moet synchroon lopen met de internettijd om de detectie van de server goed te laten werken.
    - **Updates installeren:** Azure Migrate Server Assessment controleert of het toestel de nieuwste updates heeft geïnstalleerd.

### <a name="register-the-appliance-with-azure-migrate"></a>Het toestel registreren met Azure Migreren

1. Klik **op Aanmelden**. Als deze niet wordt weergegeven, controleert u of u de pop-upblokkering in de browser hebt uitgeschakeld.
2. Meld u op het nieuwe tabblad aan met uw Azure-referenties.
    - Meld u aan met uw gebruikersnaam en wachtwoord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Ga na het succesvol aanmelden terug naar de web-app.
4. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
5. Geef een naam op voor het toestel. De naam moet alfanumeriek zijn met 14 tekens of minder.
6. Klik **op Registreren**.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak nu verbinding van het apparaat met de te ontdekken fysieke servers en start de detectie.

1. Klik **op Referenties toevoegen** om de accountreferenties op te geven die het toestel zal gebruiken om servers te detecteren.  
2. Geef het **besturingssysteem**op, de vriendelijke naam voor de referenties, **gebruikersnaam** en **wachtwoord** en klik op **Toevoegen**.
U elk één set referenties toevoegen voor Windows- en Linux-servers.
4. Klik **op Server toevoegen**en geef servergegevens op: FQDN/IP-adres en een vriendelijke naam van referenties (één vermelding per rij) om verbinding te maken met de server.
3. Klik op **Valideren**. Na validatie wordt de lijst met servers die kunnen worden ontdekt weergegeven.
    - Als de validatie mislukt voor een server, controleert u de fout door boven het pictogram in de kolom **Status** te zweven. Los problemen op en valideer opnieuw.
    - Als u een server wilt verwijderen, selecteert u > **Verwijderen**.
4. Klik na validatie op **Opslaan en start detectie** om het detectieproces te starten.

Dit begint te ontdekken. Het duurt ongeveer 1,5 minuten per server voordat metagegevens van de gedetecteerde server in de Azure-portal worden weergegeven.

### <a name="verify-servers-in-the-portal"></a>Servers in de portal verifiëren

Na detectie u controleren of de servers worden weergegeven in de Azure-portal.

1. Open het Azure Migrate-dashboard.
2. Klik in **Azure Migrate - Servers** > **Azure Migrate: Server Assessment** pagina op het pictogram dat het aantal voor **gedetecteerde servers**weergeeft.

## <a name="set-up-an-assessment"></a>Een beoordeling instellen

Er zijn twee typen beoordelingen die u maken met Azure Migrate: Serverassessment.

**Beoordeling** | **Details** | **Gegevens**
--- | --- | ---
**Op prestaties gebaseerd** | Beoordelingen op basis van verzamelde prestatiegegevens | **Aanbevolen VM-grootte:** gebaseerd op cpu- en geheugengebruiksgegevens.<br/><br/> **Aanbevolen schijftype (standaard of premium beheerde schijf):** op basis van de IOPS en doorvoer van de on-premises schijven.
**Als on-premises** | Beoordelingen op basis van on-premises dimensionering. | **Aanbevolen VM-grootte:** op basis van de on-premises servergrootte<br/><br> **Aanbevolen schijftype:** op basis van de instelling voor het opslagtype die u voor de beoordeling selecteert.


### <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer een beoordeling als volgt uit:

1. Bekijk de [aanbevolen procedures](best-practices-assessment.md) voor het maken van beoordelingen.
2. Klik op het tabblad **Servers** in **Azure Migreren:** tegel Serverbeoordeling op **Beoordelen**.

    ![Evalueren](./media/tutorial-assess-physical/assess.png)

2. Geef **in Servers beoordelen**een naam op voor de beoordeling.
3. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Beoordelingseigenschappen](./media/tutorial-assess-physical/view-all.png)

3. Selecteer in **Een groep selecteren of maken**de optie Nieuw **maken**en geef een groepsnaam op. Een groep verzamelt een of meer servers voor beoordeling.
4. Selecteer in **Machines toevoegen aan de groep**servers die u aan de groep wilt toevoegen.
5. Klik **op Beoordeling maken** om de groep te maken en voer de beoordeling uit.

    ![Een evaluatie maken](./media/tutorial-assess-physical/assessment-create.png)

6. Nadat de beoordeling is gemaakt, bekijkt u deze in **Servers** > **Azure Migrate: Server Assessment** > **Assessments**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.



## <a name="review-an-assessment"></a>Een beoordeling beoordelen

Een beoordeling beschrijft:

- **Azure-gereedheid**: of servers geschikt zijn voor migratie naar Azure.
- **Schatting van de maandelijkse kosten**: de geschatte maandelijkse reken- en opslagkosten voor het uitvoeren van de servers in Azure.
- **Schatting van de maandelijkse opslagkosten**: Geschatte kosten voor schijfopslag na migratie.

### <a name="view-an-assessment"></a>Bekijk een beoordeling

1. Klik in **Servers voor migratiedoelen** >  **Servers**op **Beoordelingen** in **Azure Migreren: Serverbeoordeling**.
2. Klik in **Assessments**op een beoordeling om deze te openen.

    ![Evaluatiesamenvatting](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

1. Controleer in **Azure**of de servers klaar zijn voor migratie naar Azure.
2. Controleer de status:
    - **Klaar voor Azure:** Azure Migrate beveelt in de beoordeling een VM-grootte en kostenramingen voor VM's aan.
    - **Klaar met voorwaarden**: Toont problemen en voorgestelde herstel.
    - **Niet klaar voor Azure:** toont problemen en voorgestelde herstel.
    - **Gereedheid onbekend**: wordt gebruikt wanneer Azure Migrate de gereedheid niet kan beoordelen vanwege problemen met de beschikbaarheid van gegevens.

2. Klik op een **Azure-gereedheidsstatus.** U de gereedheidsgegevens van de server bekijken en inzoomen om servergegevens te bekijken, waaronder rekenkracht, opslag en netwerkinstellingen.



### <a name="review-cost-details"></a>Kostendetails bekijken

In deze weergave worden de geschatte reken- en opslagkosten weergegeven voor het uitvoeren van VM's in Azure.

1. Bekijk de maandelijkse reken- en opslagkosten. De kosten worden geaggregeerd voor alle servers in de beoordeelde groep.

    - Kostenramingen zijn gebaseerd op de grootteaanbevelingen voor een machine en de schijven en eigenschappen ervan.
    - Geschatte maandelijkse kosten voor rekenkracht en opslag worden weergegeven.
    - De kostenschatting is voor het uitvoeren van de on-premises servers als IaaS VM's. Azure Migrate Server Assessment houdt geen rekening met paas- of SaaS-kosten.

2. U schattingen van de maandelijkse opslagkosten bekijken. Deze weergave toont geaggregeerde opslagkosten voor de beoordeelde groep, verdeeld over verschillende soorten opslagschijven.
3. U inzoomen om details voor specifieke servers te bekijken.


### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Wanneer u op prestaties gebaseerde beoordelingen uitvoert, wordt een betrouwbaarheidsbeoordeling toegewezen aan de beoordeling.

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-physical/confidence-rating.png)

- Een beoordeling van 1-sterren (laagste) tot 5-sterren (hoogste) wordt toegekend.
- Met de betrouwbaarheidsclassificatie u de betrouwbaarheid inschatten van de aanbevelingen die door de beoordeling worden gegeven.
- De betrouwbaarheidsbeoordeling is gebaseerd op de beschikbaarheid van gegevenspunten die nodig zijn om de beoordeling te berekenen.

Vertrouwensbeoordelingen voor een beoordeling zijn als volgt.

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

[Meer informatie](best-practices-assessment.md#best-practices-for-confidence-ratings) over aanbevolen procedures voor vertrouwensbeoordelingen.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Azure Migrate-toestel instellen
> * Een beoordeling maken en herzien

Ga verder naar de derde zelfstudie in de reeks, voor meer informatie over het migreren van fysieke servers naar Azure met Azure Migrate: ServerMigratie.

> [!div class="nextstepaction"]
> [Fysieke servers migreren](./tutorial-migrate-physical-virtual-machines.md)
