---
title: Systeemcentrum Operations Manager beoordelen met Azure Monitor
description: U de Health Check-oplossing van Het Systeemcentrum Operations Manager gebruiken om het risico en de status van uw omgevingen regelmatig te beoordelen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 94251dfa2d9fa732912ed20d825e64f542d79188
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055416"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>De omgeving optimaliseren met behulp van System Center Operations Manager-statuscontrole (preview)

![Symbool statuscontrole van Systeemcentrum Operations Manager](./media/scom-assessment/scom-assessment-symbol.png)

U de Health Check-oplossing voor Het Systeemcentrum Operations Manager gebruiken om het risico en de status van uw beheergroep System Center Operations Manager regelmatig te beoordelen. In dit artikel u de oplossing installeren, configureren en gebruiken, zodat u corrigerende maatregelen nemen voor mogelijke problemen.

Deze oplossing biedt een geprioritteerde lijst met aanbevelingen die specifiek zijn voor uw geïmplementeerde serverinfrastructuur. De aanbevelingen zijn gecategoriseerd in vier focusgebieden, die u helpen snel inzicht in het risico en corrigerende maatregelen te nemen.

De aanbevelingen zijn gebaseerd op de kennis en ervaring die Microsoft-engineers hebben opgedaan met duizenden klantbezoeken. Elke aanbeveling geeft richtlijnen over waarom een probleem voor u van belang kan zijn en hoe u de voorgestelde wijzigingen implementeren.

U focusgebieden kiezen die het belangrijkst zijn voor uw organisatie en uw voortgang bijhouden in de richting van het uitvoeren van een risicovrije en gezonde omgeving.

Nadat u de oplossing hebt toegevoegd en een beoordeling is uitgevoerd, wordt beknopte informatie voor focusgebieden weergegeven in het dashboard **van De Statuscontrole van het Systeemcentrum Operations Manager** voor uw infrastructuur. In de volgende secties wordt beschreven hoe u de informatie gebruikt in het dashboard **Statuscontrole van het Systeemcentrum Operations Manager,** waar u aanbevolen acties voor uw Operations Manager-omgeving bekijken en vervolgens uitvoeren.

![Tegel oplossingsbeheer systeemcentrum Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![Dashboard Statuscontrole van Systeemcentrum Operations Manager](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>De oplossing installeren en configureren

De oplossing werkt met Microsoft System Center 2012 Operations Manager Service Pack 1, Microsoft System Center 2012 R2 Operations Manager, Microsoft System Center 2016 Operations Manager, Microsoft System Center 2016 Operations Manager en Microsoft System Center Operations Manager 1807. Op elke beheerserver moet een ondersteunde versie van .NET Framework 4.6.2 worden geïnstalleerd.

Gebruik de volgende informatie om de oplossing te installeren en configureren.

- Voordat u de Health Check-oplossing in Log Analytics gebruiken, moet u de oplossing hebben geïnstalleerd. Installeer de oplossing vanuit [Azure marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview)

- Nadat u de oplossing aan de werkruimte hebt toegevoegd, wordt in de tegel Statuscontrole van **Het Systeemcentrum Operations Manager** op het dashboard een extra configuratiebericht weergegeven. Klik op de tegel en volg de configuratiestappen die op de pagina worden vermeld

  ![Dashboardtegel Van Systeemcentrum Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> Configuratie van System Center Operations Manager kan worden gedaan met behulp van een script door de stappen te volgen die worden vermeld op de configuratiepagina van de oplossing in Log Analytics.

 Voer de onderstaande stappen uit in de volgende volgorde om de beoordeling te configureren via de Operations Operations-console:
1. [Het run as-account instellen voor de statuscontrole van System Center Operations Manager](#operations-manager-run-as-accounts-for-log-analytics)  
2. De regel statuscontrole van Systeemcentrum Operations Manager configureren

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>Details voor gegevensverzameling van System Center Operations Manager-statuscontrole

De oplossing voor statuscontrole van Het Systeemcentrum Operations Manager verzamelt gegevens uit de volgende bronnen:

* Register
* Windows Management Instrumentation (WMI)
* Gebeurtenislogboek
* Bestandsgegevens
* Rechtstreeks vanuit Operations Manager met PowerShell- en SQL-query's, vanaf een beheerserver die u hebt opgegeven.  

Gegevens worden verzameld op de beheerserver en elke zeven dagen doorgestuurd naar Log Analytics.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Uitvoeren als-accounts van Operations Manager voor Log Analytics

Log Analytics bouwt voort op beheerpakketten voor workloads om services met toegevoegde waarde te bieden. Elke werkbelasting vereist workload-specifieke bevoegdheden om beheerpakketten uit te voeren in een andere beveiligingscontext, zoals een domeingebruikersaccount. Een Operations Manager Run As-account configureren met geprivilegieerde referenties. Zie [Een Run As-account maken](https://technet.microsoft.com/library/hh321655(v=sc.12).aspx) in de documentatie operations manager voor meer informatie.

Gebruik de volgende gegevens om de Operations Manager Run As-account in te stellen voor Statuscontrole van System Center Operations Manager.

### <a name="set-the-run-as-account"></a>Instellen als Uitvoeren als-account

Het Run As-account moet voldoen aan de volgende vereisten voordat u verdergaat:

* Een domeingebruikersaccount dat lid is van de lokale groep Administrators op alle servers die een Operations Manager-rol ondersteunen : Beheerserver, SQL Server die het operationele, gegevensmagazijn en ACS-database, Rapportage, Webconsole en Gateway-server host.
* Operation Manager Administrator Rol voor de beheergroep die wordt beoordeeld
* Als het account geen SQL-sysadmin-rechten heeft, voert u het [script](#sql-script-to-grant-granular-permissions-to-the-run-as-account) uit om gedetailleerde machtigingen toe te kennen aan het account op elk SQL Server-exemplaar dat een of alle Operations Manager-databases host.

1. Selecteer in de Operations Manager Console de navigatieknop **Beheer.**
2. Klik **onder Uitvoeren als configuratie**op **Accounts**.
3. Klik in de wizard **Uitvoeren als account maken** op de pagina **Inleiding** op **Volgende**.
4. Selecteer **op** de pagina Algemene eigenschappen **de** optie Windows in het **type Uitvoeren als account:** lijst.
5. Typ een weergavenaam in het tekstvak **Weergavenaam** en typ eventueel een beschrijving in het vak **Beschrijving** en klik op **Volgende**.
6. Selecteer op de pagina **Distributiebeveiliging** de optie **Veiliger**.
7. Klik **op Maken**.  

Nu het Run As-account is gemaakt, moet het beheerservers in de beheergroep targeten en zijn gekoppeld aan een vooraf gedefinieerd Run As-profiel, zodat werkstromen worden uitgevoerd met behulp van de referenties.  

1. Dubbelklik onder **Uitvoeren als configuratie**, **accounts**in het resultatenvenster op het account dat u eerder hebt gemaakt.
2. Klik op het tabblad **Distributie** op **Toevoegen** voor het vak **Geselecteerde computers** en voeg de beheerserver toe om het account naar te distribueren.  Klik twee keer op **OK** om de wijzigingen op te slaan.
3. Klik **onder Uitvoeren als configuratie**op **Profielen**.
4. Zoek naar het *SCOM-beoordelingsprofiel*.
5. De profielnaam moet zijn: *Microsoft System Center Operations Manager Health Check Run As Profile*.
6. Klik met de rechtermuisknop en werk de eigenschappen ervan bij en voeg de onlangs gemaakte Run As-account toe die u eerder hebt gemaakt.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>SQL-script voor het verlenen van gedetailleerde machtigingen voor het Uitvoeren als-account

Voer het volgende SQL-script uit om vereiste machtigingen toe te kennen aan het Run As-account op het SQL Server-exemplaar dat wordt gebruikt door Operations Manager als host van de operationele, gegevensmagazijn en ACS-database.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>De statuscontroleregel configureren

Het beheerpakket van de System Center Operations Manager Health Check-oplossing bevat een regel met de naam *Microsoft System Center Operations Manager Run Health Check Rule*. Deze regel is verantwoordelijk voor het uitvoeren van de statuscontrole. Gebruik de onderstaande procedures om de regel in te schakelen en de frequentie te configureren.

Standaard is de regel statuscontrole van Microsoft System Center Operations Manager run uitgeschakeld. Als u de statuscontrole wilt uitvoeren, moet u de regel op een beheerserver inschakelen. Voer de volgende stappen uit.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>De regel voor een specifieke beheerserver inschakelen

1. Zoek in de **werkruimte Voor ontwerpen** van de Operations-console Operations naar de regel Microsoft System Center Operations Manager Voer *statuscontroleregel uit* in het deelvenster **Regels.**
2. Selecteer in de zoekresultaten de tekst *Tekst: Management Server*.
3. Klik met de rechtermuisknop op de regel en klik vervolgens op **Overschrijven** > **Voor een specifiek object van klasse: Management Server**.
4.  Selecteer in de lijst met beschikbare beheerservers de beheerserver waar de regel moet worden uitgevoerd.  Dit moet dezelfde beheerserver zijn die u eerder hebt geconfigureerd om het Run As-account aan te koppelen.
5.  Zorg ervoor dat u de overschrijfwaarde wijzigt in **True** voor de **parameterwaarde Ingeschakeld.**<br><br> ![parameter overschrijven](./media/scom-assessment/rule.png)

    Configureer de voerfrequentie met de volgende procedure terwijl u zich nog in dit venster begeeft.

#### <a name="configure-the-run-frequency"></a>De uitvoeringsfrequentie configureren

De beoordeling is geconfigureerd om standaard elke 10.080 minuten (of zeven dagen) uit te voeren. U de waarde overschrijven tot een minimumwaarde van 1440 minuten (of één dag). De waarde vertegenwoordigt de minimale tijdsverschil die nodig is tussen opeenvolgende beoordelingsuitvoeringen. Als u het interval wilt overschrijven, gebruikt u de onderstaande stappen.

1. Zoek in de **werkruimte Ontwerpen** van de Operations Manager naar de regel Microsoft System Center Operations Manager Run Health *Check Rule* in de sectie **Regels.**
2. Selecteer in de zoekresultaten de tekst *Tekst: Management Server*.
3. Klik met de rechtermuisknop op de regel en klik vervolgens op **Regel** > overschrijven voor alle objecten van de**klasse: Beheerserver**.
4. Wijzig de **parameterwaarde Interval** in de gewenste intervalwaarde. In het onderstaande voorbeeld wordt de waarde ingesteld op 1440 minuten (één dag).<br><br> ![intervalparameter](./media/scom-assessment/interval.png)<br>  

    Als de waarde is ingesteld op minder dan 1440 minuten, wordt de regel uitgevoerd op een interval van één dag. In dit voorbeeld negeert de regel de intervalwaarde en wordt uitgevoerd op een frequentie van één dag.


## <a name="understanding-how-recommendations-are-prioritized"></a>Inzicht in hoe de prioriteit van aanbevelingen wordt vastgesteld

Elke aanbeveling wordt gegeven een wegingswaarde die het relatieve belang van de aanbeveling identificeert. Alleen de 10 belangrijkste aanbevelingen worden getoond.

### <a name="how-weights-are-calculated"></a>Hoe gewicht wordt berekend

Wegingen zijn geaggregeerde waarden op basis van drie belangrijke factoren:

- De *kans* dat een probleem wordt geïdentificeerd, zal problemen veroorzaken. Een hogere waarschijnlijkheid komt overeen met een grotere algemene score voor de aanbeveling.
- De *impact* van het probleem op uw organisatie als dit een probleem veroorzaakt. Een hogere impact komt overeen met een grotere algemene score voor de aanbeveling.
- De *inspanning* die nodig is om de aanbeveling uit te voeren. Een hogere inspanning komt overeen met een kleinere algemene score voor de aanbeveling.

De weging voor elke aanbeveling wordt uitgedrukt als een percentage van de totale score die beschikbaar is voor elk aandachtsgebied. Als een aanbeveling in het focusgebied Beschikbaarheid en Bedrijfscontinuïteit bijvoorbeeld een score van 5% heeft, verhoogt het implementeren van die aanbeveling uw algemene score voor beschikbaarheid en bedrijfscontinuïteit met 5%.

### <a name="focus-areas"></a>Aandachtsgebieden

**Beschikbaarheid en bedrijfscontinuïteit** - Dit aandachtsgebied bevat aanbevelingen voor servicebeschikbaarheid, tolerantie van uw infrastructuur en bedrijfsbescherming.

**Prestaties en schaalbaarheid** - Dit focusgebied bevat aanbevelingen om de IT-infrastructuur van uw organisatie te helpen groeien, ervoor te zorgen dat uw IT-omgeving voldoet aan de huidige prestatievereisten en in staat is om te reageren op veranderende infrastructuurbehoeften.

**Upgrade, migratie en implementatie** : dit focusgebied bevat aanbevelingen waarmee u SQL Server upgraden, migreren en implementeren naar uw bestaande infrastructuur.

**Operations and Monitoring** - Dit focusgebied bevat aanbevelingen om uw IT-activiteiten te stroomlijnen, preventief onderhoud te implementeren en de prestaties te maximaliseren.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Moet u proberen een score van 100% te halen voor elk focusgebied?

Niet per se. De aanbevelingen zijn gebaseerd op de kennis en ervaringen die Microsoft-technici hebben opgedaan bij duizenden klantbezoeken. Er zijn echter geen twee serverinfrastructuren hetzelfde en specifieke aanbevelingen kunnen min of meer relevant voor u zijn. Sommige beveiligingsaanbevelingen zijn bijvoorbeeld minder relevant als uw virtuele machines niet worden blootgesteld aan internet. Sommige beschikbaarheidsaanbevelingen zijn mogelijk minder relevant voor services die ad hoc-gegevensverzameling en -rapportage met lage prioriteit bieden. Zaken die belangrijk zijn voor een volwassen bedrijf kunnen minder belangrijk zijn voor een start-up. U bepalen welke aandachtsgebieden uw prioriteiten zijn en vervolgens kijken hoe uw scores in de loop van de tijd veranderen.

Elke aanbeveling bevat richtlijnen over waarom het belangrijk is. Gebruik deze richtlijnen om te beoordelen of de implementatie van de aanbeveling geschikt is voor u, gezien de aard van uw IT-services en de zakelijke behoeften van uw organisatie.

## <a name="use-health-check-focus-area-recommendations"></a>Aanbevelingen voor het focusgebied van de statuscontrole gebruiken

Voordat u een health check-oplossing gebruiken in Log Analytics, moet u de oplossing hebben geïnstalleerd. Zie [Een beheeroplossing installeren](../../azure-monitor/insights/solutions.md)voor meer informatie over het installeren van oplossingen. Nadat deze is geïnstalleerd, u de samenvatting van aanbevelingen bekijken met de tegel Statuscontrole van Systeemcentrum Operations Manager op de **pagina Overzicht** voor uw werkruimte in de Azure-portal.

Bekijk de samengevatte nalevingsbeoordelingen voor uw infrastructuur en zoom vervolgens in op aanbevelingen.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aanbevelingen voor een aandachtsgebied bekijken en corrigerende maatregelen nemen
1. Log in bij de [https://portal.azure.com](https://portal.azure.com)Azure-portal op .
2. Klik in Azure Portal op **Meer services** in de linkerbenedenhoek. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics**.
3. Selecteer in het deelvenster Logboekanalyseabonnementen een werkruimte en klik vervolgens op het **menu-item Werkruimteoverzicht.**  
4. Klik op de pagina **Overzicht** op de tegel **Statuscontrole systeemcentrum Operations Manager.**
5. Bekijk op de pagina **Health Check van Het Systeemcentrum Operations Manager** de overzichtsinformatie in een van de focusgebiedbladen en klik vervolgens op een pagina om aanbevelingen voor dat focusgebied weer te geven.
6. Op een van de pagina's van het focusgebied u de geprioritteerde aanbevelingen voor uw omgeving bekijken. Klik op een aanbeveling onder **Getroffen objecten** om meer informatie te bekijken over waarom de aanbeveling wordt gedaan.<br><br> ![focusgebied](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. U corrigerende maatregelen nemen die worden voorgesteld in **voorgestelde acties**. Wanneer het item is aangepakt, worden in latere beoordelingen vastgelegd dat aanbevolen acties zijn uitgevoerd en wordt uw nalevingsscore verhoogd. Gecorrigeerde items worden weergegeven als **doorgegeven objecten**.

## <a name="ignore-recommendations"></a>Aanbevelingen negeren

Als u aanbevelingen hebt die u wilt negeren, u een tekstbestand maken dat Wordt gebruikt om te voorkomen dat aanbevelingen worden weergegeven in uw beoordelingsresultaten.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>Aanbevelingen identificeren die u wilt negeren
1. Klik in de Azure-portal op de werkruimtepagina Log Analytics voor uw geselecteerde werkruimte op het **menu-item Zoeken bij logboeken.**
2. Gebruik de volgende query om aanbevelingen op te sommen die zijn mislukt voor computers in uw omgeving.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Als uw werkruimte is geüpgraded naar de [nieuwe querytaal Log Analytics,](../../azure-monitor/log-query/log-query-overview.md)wordt de bovenstaande query gewijzigd in het volgende.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Hier is een screenshot met de query Log Search:<br><br> ![zoeken in logboeken](./media/scom-assessment/scom-log-search.png)<br>

3. Kies aanbevelingen die u wilt negeren. U gebruikt de waarden voor RecommendationId in de volgende procedure.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Een tekstbestand IgnoreRecommendations.txt maken en gebruiken

1. Maak een bestand met de naam IgnoreRecommendations.txt.
2. Plak of typ elke RecommendationId voor elke aanbeveling die u wilt dat Log Analytics op een afzonderlijke regel negeert en sla het bestand op en sluit het.
3. Plaats het bestand in de volgende map op elke computer waar u wilt dat Log Analytics aanbevelingen negeert.
4. Op de Operations Manager management server - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>Controleren of aanbevelingen worden genegeerd

1. Nadat de volgende geplande beoordeling is uitgevoerd, worden standaard om de zeven dagen de opgegeven aanbevelingen gemarkeerd als Genegeerd en worden ze niet weergegeven op het dashboard voor statuscontrole.
2. U de volgende query's voor logboekzoekopdrachten gebruiken om alle genegeerde aanbevelingen weer te geven.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Als uw werkruimte is geüpgraded naar de [nieuwe querytaal Log Analytics,](../../azure-monitor/log-query/log-query-overview.md)wordt de bovenstaande query gewijzigd in het volgende.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Als u later besluit dat u genegeerde aanbevelingen wilt zien, verwijdert u alle ignorerecommendations.txt-bestanden of verwijdert u aanbevelingen ervan.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>Veelgestelde vragen over de oplossing van System Center Operations Manager-statuscontrole

*Ik heb de Health Check-oplossing toegevoegd aan mijn Log Analytics-werkruimte. Maar ik zie de aanbevelingen niet. Waarom niet?* Nadat u de oplossing hebt toegevoegd, gebruikt u de volgende stappen met de aanbevelingen op het dashboard Log Analytics.  

- [Het run as-account instellen voor de statuscontrole van System Center Operations Manager](#operations-manager-run-as-accounts-for-log-analytics)  
- [De regel statuscontrole van Systeemcentrum Operations Manager configureren](#configure-the-health-check-rule)


*Is er een manier om te configureren hoe vaak de controle wordt uitgevoerd?* Ja. Zie [De voerfrequentie configureren](#configure-the-run-frequency).

*Als er een andere server wordt ontdekt nadat ik de System Center Operations Manager Health Check-oplossing heb toegevoegd, wordt deze dan gecontroleerd?* Ja, na ontdekking wordt het vanaf dat tijd gecontroleerd, standaard om de zeven dagen.

*Wat is de naam van het proces dat de gegevensverzameling doet?* AdvisorAssessment.exe

*Waar loopt het AdvisorAssessment.exe-proces?* AdvisorAssessment.exe wordt uitgevoerd onder het HealthService-proces van de beheerserver waar de regel voor statuscontrole is ingeschakeld. Met behulp van dat proces wordt het ontdekken van uw hele omgeving bereikt door het verzamelen van externe gegevens.

*Hoe lang duurt het voor het verzamelen van gegevens?* Het verzamelen van gegevens op de server duurt ongeveer een uur. Het kan langer duren in omgevingen met veel Operations Manager-exemplaren of databases.

*Wat gebeurt er als ik het interval van de beoordeling instel op minder dan 1440 minuten?* De beoordeling is vooraf geconfigureerd om maximaal één keer per dag te worden uitgevoerd. Als u de intervalwaarde overschrijft tot een waarde van minder dan 1440 minuten, gebruikt de beoordeling 1440 minuten als intervalwaarde.

*Hoe weet je of er voorwaarden zijn?* Als de statuscontrole is uitgevoerd en u geen resultaten ziet, is het waarschijnlijk dat sommige van de vereisten voor de controle zijn mislukt. U query's uitvoeren: `Operation Solution=SCOMAssessment` en `SCOMAssessmentRecommendation FocusArea=Prerequisites` in Log Zoeken om de mislukte vereisten te bekijken.

*Er is `Failed to connect to the SQL Instance (….).` een bericht in de vereiste mislukkingen. Wat is het probleem?* AdvisorAssessment.exe, het proces dat gegevens verzamelt, wordt uitgevoerd onder het HealthService-proces op de beheerserver. Als onderdeel van de statuscontrole probeert het proces verbinding te maken met de SQL Server waar de Operations Manager-database aanwezig is. Deze fout kan optreden wanneer firewallregels de verbinding met de SQL Server-instantie blokkeren.

*Wat voor soort gegevens worden verzameld?* De volgende typen gegevens worden verzameld: - WMI-gegevens - Registergegevens - EventLog-gegevens - Operations Manager-gegevens via Windows PowerShell, SQL Queries en Verzamelaar van bestandsinformatie.

*Waarom moet ik een Run As-account configureren?* Met Operations Manager worden verschillende SQL-query's uitgevoerd. Als u ze wilt uitvoeren, moet u een Run As-account gebruiken met de benodigde machtigingen. Bovendien zijn lokale beheerdersreferenties vereist om WMI op te vragen.

*Waarom alleen de top 10 aanbevelingen weergeven?* In plaats van u een uitputtende, overweldigende lijst met taken te geven, raden we u aan u eerst te concentreren op het aanpakken van de geprioritteerde aanbevelingen. Nadat u ze hebt aangepakt, worden aanvullende aanbevelingen beschikbaar. Als u de gedetailleerde lijst liever ziet, u alle aanbevelingen bekijken met Logboekzoeken.

*Is er een manier om een aanbeveling te negeren?* Ja, zie de [aanbevelingen negeren.](#ignore-recommendations)


## <a name="next-steps"></a>Volgende stappen

- [Zoeklogboeken](../../azure-monitor/log-query/log-query-overview.md) voor meer informatie over het analyseren van gedetailleerde gegevens en aanbevelingen van System Center Operations Manager Health Check.
