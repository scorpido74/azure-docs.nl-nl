---
title: Fysieke servers beoordelen voor migratie naar Azure met Azure Migrate server-evaluatie
description: Hierin wordt beschreven hoe u on-premises fysieke servers kunt beoordelen voor migratie naar Azure met behulp van Azure Migrate server-evaluatie.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 7f840a99ab76548c16389955cf49791036cc63a2
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158359"
---
# <a name="assess-physical-servers-with-azure-migrate-server-assessment"></a>Fysieke servers beoordelen met Azure Migrate: Server evaluatie

In dit artikel leest u hoe u on-premises fysieke servers kunt beoordelen met behulp van het Azure Migrate: Server assessment tool.

[Azure migrate](migrate-services-overview.md) biedt een hub aan hulpprogram ma's waarmee u apps, infra structuur en werk belastingen op Microsoft Azure kunt detecteren, evalueren en migreren. De hub bevat Azure Migrate-hulpprogram ma's en ISV-aanbiedingen (Independent Software Vendor) van derden.

Deze zelf studie is de tweede in een serie die laat zien hoe u fysieke servers kunt beoordelen en migreren naar Azure. In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Stel een Azure Migrate project in.
> * Stel een Azure Migrate apparaat in waarop on-premises wordt uitgevoerd om fysieke servers te beoordelen.
> * Continue detectie van on-premises fysieke servers starten. Het apparaat verzendt configuratie-en prestatie gegevens voor gedetecteerde servers naar Azure.
> * Gedetecteerde servers groeperen en de Server groep evalueren.
> * Bekijk de evaluatie.

> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de artikelen met procedures voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

- [Voltooi](tutorial-prepare-physical.md) de eerste zelf studie in deze serie. Als dat niet het geval is, werken de instructies in deze zelf studie niet.
- In de eerste zelf studie hebt u het volgende gedaan:
    - [Azure-machtigingen instellen](tutorial-prepare-physical.md#prepare-azure) voor Azure Migrate.
    - [Fysieke servers voorbereiden](tutorial-prepare-physical.md#prepare-for-physical-server-assessment) voor evaluatie. De vereisten voor het apparaat moeten worden geverifieerd. U moet ook een account instellen voor detectie van fysieke servers. De vereiste poorten moeten beschikbaar zijn en u moet rekening houden met de Url's die nodig zijn voor toegang tot Azure.


## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate project instellen

Ga als volgt te werk om een nieuw Azure Migrate-project op te zetten:

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. In **Overzicht**, onder **Servers ontdekken, evalueren en migreren**, klikt u op **Servers evalueren en migreren**.

    ![Servers detecteren en beoordelen](./media/tutorial-assess-physical/assess-migrate.png)

4. Klik in **Aan de slag** op **Hulpprogramma's toevoegen**.
5. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.     
6. Geef in **Project Details**de project naam en de geografie op waarin u het project wilt maken. Azië, Europa, UK en de Verenigde Staten worden ondersteund.

    - Het project geografie wordt alleen gebruikt om de meta gegevens op te slaan die zijn verzameld van on-premises servers.
    - Wanneer u een migratie uitvoert, kunt u elke gewenste doelregio selecteren.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-physical/migrate-project.png)


7. Klik op **Volgende**.
8. Selecteer in **hulp programma voor beoordeling selecteren**de optie **Azure migrate: Server evaluatie** > **volgende**.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-physical/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Controleer in **Evalueren en hulpprogramma's toevoegen** de instellingen en klik op **Hulpmiddelen toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.


## <a name="set-up-the-appliance"></a>Het apparaat instellen

Azure Migrate: Server bepaling voert een licht gewicht in.

- Dit apparaat voert fysieke server detectie uit en verzendt meta gegevens en prestatie gegevens van de server naar Azure Migrate server evaluatie.
- Als u het apparaat wilt instellen, doet u het volgende:
    - Down load een zip-bestand met Azure Migrate-installatie script uit de Azure Portal.
    - Pak de inhoud uit het zip-bestand uit. Start de Power shell-console met beheerders bevoegdheden.
    - Voer het Power shell-script uit om de web-app voor het toestel te starten.
    - Configureer het apparaat voor de eerste keer en registreer het bij het Azure Migrate-project.
- U kunt meerdere toestellen instellen voor één Azure Migrate project. U kunt elk aantal fysieke servers detecteren op alle apparaten. Er kunnen Maxi maal 250 servers worden gedetecteerd per apparaat.

### <a name="download-the-installer-script"></a>Het installatie script downloaden

Down load het zip-bestand voor het apparaat.

1. In **migratie doelen** > **servers** > **Azure migrate: Server evaluatie**, klikt u op **ontdekken**.
2. Op **computers detecteren** > **zijn uw machines gevirtualiseerd? klikt u**op **niet gevirtualiseerd/Overig**.
3. Klik op **downloaden** om het zip-bestand te downloaden.

    ![Installatie programma downloaden](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het gecomprimeerde bestand is beveiligd, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.
2. Voer de volgende opdracht uit om de hash voor het zip-bestand te genereren
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller\AzureMigrateInstaller.ps1 SHA256```

3.  Voor de toestel versie 1.19.05.10 moet de gegenereerde hash overeenkomen met deze instellingen.

  **Algoritme** | **Hash-waarde**
  --- | ---
  MD5 | 5b98cf68dad495696c789bdad8f0d91b
  SHA256 | c2364887738875a31634eb0cf5defd0398f813d41069875976ca076297071e6f

### <a name="run-the-azure-migrate-installer-script"></a>Het Azure Migrate-installatie script uitvoeren

Het installatie script doet het volgende:

- Installeert agents en een webtoepassing voor detectie en evaluatie van fysieke servers.
- Installeer Windows-rollen, waaronder Windows Activation service, IIS en Power shell ISE.
- Een herschrijf bare module van IIS downloaden en installeren. [Meer informatie](https://www.microsoft.com/download/details.aspx?id=7435).
- Hiermee werkt u een register sleutel (HKLM) bij met permanente instellings gegevens voor Azure Migrate.
- Maakt de volgende bestanden onder het pad:
    - **Configuratie bestanden**:%ProgramData%\Microsoft Azure\Config
    - **Logboek bestanden**:%ProgramData%\Microsoft Azure\Logs

Voer het script als volgt uit:

1. Pak het zip-bestand uit naar een map op de server die als host moet fungeren voor het apparaat.
2. Start Power shell op de bovenstaande server met beheerders bevoegdheden (met verhoogde bevoegdheden).
3. Wijzig de Power shell-map in de map waarin de inhoud is geëxtraheerd uit het gedownloade zip-bestand.
4. Voer het script met de naam **AzureMigrateInstaller. ps1** uit door de volgende opdracht uit te voeren:
    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1
    ```
Met het script wordt de web-app voor het toestel gestart wanneer deze is voltooid.

In het geval van problemen kunt u de script Logboeken openen op C:\ProgramData\Microsoft Azure\Logs\ AzureMigrateScenarioInstaller_<em>Time Stamp</em>. log voor het oplossen van problemen.

> [!NOTE]
> Voer het Azure Migrate-installatie script niet uit op een bestaand Azure Migrate apparaat.

### <a name="verify-appliance-access-to-azure"></a>Toestel toegang tot Azure controleren

Zorg ervoor dat het apparaat verbinding kan maken met [Azure-url's](migrate-support-matrix-physical.md#assessment-appliance-url-access).


### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

1. Open een browser op een computer die verbinding kan maken met het apparaat en open de URL van de Web-App van het apparaat: **https:// *-apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook openen vanaf het bureau blad door te klikken op de snelkoppeling naar de app.
2. Ga als volgt te werk in de web-app > vereisten in te **stellen**:
    - **Licentie**: accepteer de licentie voorwaarden en lees de informatie van derden.
    - **Connectiviteit**: de app controleert of de server toegang heeft tot internet. Als de server gebruikmaakt van een proxy:
        - Klik op **proxy-instellingen**en geef het proxy adres en de luister poort op in de notatie http://ProxyIPAddress of http://ProxyFQDN.
        - Geef referenties op als de proxy verificatie nodig heeft.
        - Alleen HTTP-proxy wordt ondersteund.
    - **Tijd synchronisatie**: tijd wordt gecontroleerd. De tijd op het apparaat moet zijn gesynchroniseerd met internet tijd zodat server detectie goed werkt.
    - **Updates installeren**: Azure migrate server beoordeling controleert of de meest recente updates zijn geïnstalleerd op het apparaat.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Klik op **Aanmelden**. Als deze niet wordt weer gegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
2. Meld u aan met uw Azure-referenties op het tabblad Nieuw.
    - Meld u aan met uw gebruikers naam en wacht woord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
4. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
5. Geef een naam op voor het apparaat. De naam moet alfanumeriek zijn met 14 tekens of minder.
6. Klik op **registreren**.


## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak nu verbinding van het apparaat met de fysieke servers die moeten worden gedetecteerd en start de detectie.

1. Klik op **referenties toevoegen** om de account referenties op te geven die door het apparaat worden gebruikt om servers te detecteren.  
2. Geef het **besturings systeem**, de beschrijvende naam voor de referenties, de **gebruikers naam** en het **wacht woord** op en klik op **toevoegen**.
U kunt elk voor Windows-en Linux-servers één set referenties toevoegen.
4. Klik op **server toevoegen**en geef Server Details-FQDN/IP-adres en beschrijvende naam van referenties (één vermelding per rij) op om verbinding te maken met de server.
3. Klik op **valideren**. Na validatie wordt de lijst met servers weer gegeven die kunnen worden gedetecteerd.
    - Als de validatie mislukt voor een server, controleert u de fout door de muis aanwijzer boven het pictogram in de kolom **status** te bewegen. Los problemen op en valideer opnieuw.
    - Als u een server wilt verwijderen, selecteert u > **verwijderen**.
4. Klik na validatie op **opslaan en start de detectie** om het detectie proces te starten.

De detectie wordt gestart. Het duurt ongeveer 1,5 minuten per server voor de meta gegevens van de gedetecteerde server die worden weer gegeven in de Azure Portal.

### <a name="verify-servers-in-the-portal"></a>Servers in de portal controleren

Na de detectie kunt u controleren of de servers worden weer gegeven in de Azure Portal.

1. Open het Azure Migrate dash board.
2. Klik in **Azure migrate-Servers** > pagina **Azure migrate: Server beoordeling** op het pictogram met het aantal voor **gedetecteerde servers**.

## <a name="set-up-an-assessment"></a>Een evaluatie instellen

Er zijn twee soorten evaluaties die u kunt maken met behulp van Azure Migrate: Server Assessment.

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatie gegevens | **Aanbevolen VM-grootte**: op basis van gegevens van CPU en geheugen gebruik.<br/><br/> **Aanbevolen schijf type (Standard of Premium Managed disk)** : op basis van de IOPS en door Voer van de on-premises schijven.
**Als on-premises** | Evaluaties op basis van on-premises grootte. | **Aanbevolen VM-grootte**: op basis van de on-premises server grootte<br/><br> **Aanbevolen schijf type**: op basis van de instelling voor het opslag type die u voor de evaluatie selecteert.


### <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer een evaluatie als volgt uit:

1. Bekijk de [Aanbevolen procedures](best-practices-assessment.md) voor het maken van evaluaties.
2. Klik op het tabblad **servers** in **Azure migrate: tegel server bepaling** op **evalueren**.

    ![Evalueren](./media/tutorial-assess-physical/assess.png)

2. Geef in **servers beoordelen**een naam op voor de evaluatie.
3. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Eigenschappen van beoordeling](./media/tutorial-assess-physical/view-all.png)

3. In **een groep selecteren of maken**, selecteert u **nieuwe maken**en geeft u een groeps naam op. Een groep verzamelt een of meer servers bij de evaluatie.
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
3. U kunt inzoomen om de details van specifieke servers te bekijken.


### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Wanneer u evaluaties op basis van prestaties uitvoert, wordt een betrouwbaarheids classificatie aan de evaluatie toegewezen.

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-physical/confidence-rating.png)

- Er wordt een classificatie van 1 ster (laagste) tot 5 sterren (hoogst) toegekend.
- De betrouwbaarheids classificatie helpt u bij het schatten van de betrouw baarheid van de grootte aanbevelingen van de evaluatie.
- De betrouwbaarheids classificatie is gebaseerd op de beschik baarheid van gegevens punten die nodig zijn om de evaluatie te berekenen.

De betrouwbaarheids classificaties voor een evaluatie zijn als volgt.

**Beschik baarheid van gegevens punt** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

Meer [informatie](best-practices-assessment.md#best-practices-for-confidence-ratings) over best practices voor betrouwbaarheids classificaties.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Azure Migrate apparaat instellen
> * Een evaluatie gemaakt en geëvalueerd

Ga door naar de derde zelf studie in de reeks om te leren hoe u fysieke servers naar Azure migreert met Azure Migrate: Server migratie.

> [!div class="nextstepaction"]
> [Fysieke servers migreren](./tutorial-migrate-physical-virtual-machines.md)
