---
title: Uw SQL Server-omgeving optimaliseren met Azure Monitor | Microsoft Documenten
description: Met Azure Monitor u de SQL Health Check-oplossing gebruiken om het risico en de status van uw omgevingen regelmatig te beoordelen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/28/2019
ms.openlocfilehash: ceaed0800df01bf2c44fee13d98b01b6e726200d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662481"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Uw SQL-omgeving optimaliseren met de SQL Server Health Check-oplossing in Azure Monitor

![SQL Health Check-symbool](./media/sql-assessment/sql-assessment-symbol.png)

U de SQL Health Check-oplossing gebruiken om het risico en de status van uw serveromgevingen regelmatig te beoordelen. In dit artikel u de oplossing installeren, zodat u corrigerende maatregelen nemen voor mogelijke problemen.

Deze oplossing biedt een geprioritteerde lijst met aanbevelingen die specifiek zijn voor uw geïmplementeerde serverinfrastructuur. De aanbevelingen zijn gecategoriseerd in zes focusgebieden die u helpen snel inzicht te krijgen in het risico en corrigerende maatregelen te nemen.

De aanbevelingen zijn gebaseerd op de kennis en ervaring die Microsoft-engineers hebben opgedaan met duizenden klantbezoeken. Elke aanbeveling geeft richtlijnen over waarom een probleem voor u van belang kan zijn en hoe u de voorgestelde wijzigingen implementeren.

U focusgebieden kiezen die het belangrijkst zijn voor uw organisatie en uw voortgang bijhouden in de richting van het uitvoeren van een risicovrije en gezonde omgeving.

Nadat u de oplossing hebt toegevoegd en een beoordeling is voltooid, wordt beknopte informatie voor focusgebieden weergegeven op het **SQL Health Check-dashboard** voor de infrastructuur in uw omgeving. In de volgende secties wordt beschreven hoe u de informatie in het **SQL Health Check-dashboard** gebruiken, waar u aanbevolen acties voor uw SQL Server-infrastructuur bekijken en vervolgens uitvoeren.

![afbeelding van de tegel SQL Health Check](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![afbeelding van sql health check-dashboard](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Vereisten

* De SQL Health Check-oplossing vereist een ondersteunde versie van .NET Framework 4.6.2 die is geïnstalleerd op elke computer waarop de Microsoft Monitoring Agent (MMA) is geïnstalleerd.  De MMA-agent wordt gebruikt door System Center 2016 - Operations Manager en Operations Manager 2012 R2 en Azure Monitor.  
* De oplossing ondersteunt SQL Server-versie 2012, 2014 en 2016.
* Een Log Analytics-werkruimte om de SQL Health Check-oplossing toe te voegen vanuit de Azure-marktplaats in de Azure-portal.  Als u de oplossing wilt installeren, moet u een beheerder of bijdrager zijn in het Azure-abonnement.

  > [!NOTE]
  > Nadat u de oplossing hebt toegevoegd, wordt het AdvisorAssessment.exe-bestand toegevoegd aan servers met agents. Configuratiegegevens worden gelezen en vervolgens naar Azure Monitor in de cloud verzonden voor verwerking. Logica wordt toegepast op de ontvangen gegevens en de gegevens worden vastgelegd door de cloudservice.
  >
  >

Als u de statuscontrole ten opzichte van uw SQL Server-servers wilt uitvoeren, hebben ze een agent en connectiviteit met Azure Monitor nodig met behulp van een van de volgende ondersteunde methoden:

1. Installeer de [Microsoft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) als de server nog niet is gecontroleerd door System Center 2016 - Operations Manager of Operations Manager 2012 R2.
2. Als het wordt gecontroleerd met System Center 2016 - Operations Manager of Operations Manager 2012 R2 en de beheergroep niet is geïntegreerd met Azure Monitor, kan de server worden gekoppeld aan Log Analytics om gegevens te verzamelen en door te sturen naar de service en gecontroleerd door Operations Manager.  
3. Als anders uw beheergroep Operations Manager is geïntegreerd met de service, moet u de domeincontrollers voor het verzamelen van gegevens toevoegen door de service die de stappen volgt onder [door agent beheerde computers toevoegen](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) nadat u de oplossing in uw werkruimte hebt ingeschakeld.  

De agent op uw SQL Server die rapporteert aan een operations manager-beheergroep, gegevens verzamelt, doorstuurt naar de toegewezen beheerserver en vervolgens rechtstreeks van een beheerserver naar Azure Monitor wordt verzonden.  De gegevens worden niet naar de Operations Manager-databases geschreven.  

Als de SQL Server wordt gecontroleerd door Operations Manager, moet u een Operations Manager Run As-account configureren. Zie [Operations Manager run-as accounts voor Azure Monitor](#operations-manager-run-as-accounts-for-log-analytics) hieronder voor meer informatie.

## <a name="sql-health-check-data-collection-details"></a>Details voor gegevensverzameling van SQL-statuscontrole
SQL Health Check verzamelt gegevens uit de volgende bronnen met behulp van de agent die u hebt ingeschakeld:

* Windows Management Instrumentation (WMI)
* Register
* Prestatiemeteritems
* Resultaten van sql Server dynamic management view

Gegevens worden verzameld op de SQL Server en elke zeven dagen doorgestuurd naar Log Analytics.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Uitvoeren als-accounts van Operations Manager voor Log Analytics
Log Analytics gebruikt de operations manager-agent en beheergroep om gegevens te verzamelen en te verzenden naar de Log Analytics-service. Log Analytics bouwt voort op managementpacks voor workloads om services met toegevoegde waarde te bieden. Elke werkbelasting vereist workload-specifieke bevoegdheden om beheerpakketten uit te voeren in een andere beveiligingscontext, zoals een domeingebruikersaccount. U moet referentiegegevens verstrekken door een Operations Manager Run As-account te configureren.

Gebruik de volgende gegevens om het Operations Manager Run As-account in te stellen voor SQL Health Check.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Het Uitvoeren als-account voor de SQL-statuscontrole instellen
 Als u het SQL Server-beheerpakket al gebruikt, moet u die configuratie uitvoeren als gebruiken.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Het SQL Run As-account configureren in de Operations-console
> [!NOTE]
> Standaard worden werkstromen in het beheerpakket uitgevoerd in de beveiligingscontext van het lokale systeemaccount. Als u de Microsoft Monitoring Agent gebruikt die rechtstreeks met de service is verbonden in plaats van rechtstreeks te rapporteren aan een beheergroep van Operations Manager, slaat u de stappen 1-5 hieronder over en voert u het T-SQL- of PowerShell-voorbeeld uit, waarbij NT AUTHORITY\SYSTEM als het gebruikersnaam.
>
>

1. Open in Operations Manager de operations-console en klik op **Beheer**.
2. Klik **onder Uitvoeren als configuratie**op **Profielen**en open SQL Assessment Run **As Profile**.
3. Klik op de pagina **Run As-accounts** op **Toevoegen**.
4. Selecteer een Windows Run As-account met de referenties die nodig zijn voor SQL Server of klik op **Nieuw** om er een te maken.

   > [!NOTE]
   > Het type Run As-account moet Windows zijn. Het Run As-account moet ook deel uitmaken van de groep Lokale beheerders op alle Windows Servers die SQL Server Instances hosten.
   >
   >
5. Klik op **Opslaan**.
6. Wijzig en voer vervolgens het volgende T-SQL-voorbeeld uit voor elke SQL Server-instantie om minimale machtigingen toe te kennen die nodig zijn voor het uitvoeren als account om de statuscontrole uit te voeren. U hoeft dit echter niet te doen als een Run As-account al deel uitmaakt van de sysadmin-serverrol op SQL Server-exemplaren.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Het SQL Run As-account configureren met Windows PowerShell
Open een PowerShell-venster en voer het volgende script uit nadat u het hebt bijgewerkt met uw gegevens:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Inzicht in hoe de prioriteit van aanbevelingen wordt vastgesteld
Elke aanbeveling wordt gegeven een wegingswaarde die het relatieve belang van de aanbeveling identificeert. Alleen de tien belangrijkste aanbevelingen worden getoond.

### <a name="how-weights-are-calculated"></a>Hoe gewicht wordt berekend
Wegingen zijn geaggregeerde waarden op basis van drie belangrijke factoren:

* De *kans* dat een probleem wordt geïdentificeerd, zal problemen veroorzaken. Een hogere waarschijnlijkheid komt overeen met een grotere algemene score voor de aanbeveling.
* De *impact* van het probleem op uw organisatie als dit een probleem veroorzaakt. Een hogere impact komt overeen met een grotere algemene score voor de aanbeveling.
* De *inspanning* die nodig is om de aanbeveling uit te voeren. Een hogere inspanning komt overeen met een kleinere algemene score voor de aanbeveling.

De weging voor elke aanbeveling wordt uitgedrukt als een percentage van de totale score die beschikbaar is voor elk aandachtsgebied. Als een aanbeveling in het focusgebied Beveiliging en Naleving bijvoorbeeld een score van 5% heeft, verhoogt het implementeren van die aanbeveling uw algehele beveiligings- en nalevingsscore met 5%.

### <a name="focus-areas"></a>Aandachtsgebieden
**Beveiliging en naleving** - Dit aandachtsgebied bevat aanbevelingen voor potentiële bedreigingen en schendingen van de beveiliging, bedrijfsbeleid en technische, wettelijke en regelgevende vereisten.

**Beschikbaarheid en bedrijfscontinuïteit** - Dit aandachtsgebied bevat aanbevelingen voor servicebeschikbaarheid, tolerantie van uw infrastructuur en bedrijfsbescherming.

**Prestaties en schaalbaarheid** - Dit focusgebied bevat aanbevelingen om de IT-infrastructuur van uw organisatie te helpen groeien, ervoor te zorgen dat uw IT-omgeving voldoet aan de huidige prestatievereisten en in staat is om te reageren op veranderende infrastructuurbehoeften.

**Upgrade, migratie en implementatie** : dit focusgebied bevat aanbevelingen waarmee u SQL Server upgraden, migreren en implementeren naar uw bestaande infrastructuur.

**Operations and Monitoring** - Dit focusgebied bevat aanbevelingen om uw IT-activiteiten te stroomlijnen, preventief onderhoud te implementeren en de prestaties te maximaliseren.

**Wijzigings- en configuratiebeheer** - Dit focusgebied bevat aanbevelingen om dagelijkse bewerkingen te beschermen, ervoor te zorgen dat wijzigingen geen negatieve invloed hebben op uw infrastructuur, om procedures voor wijzigingscontrole vast te stellen en systeemconfiguraties bij te houden en te controleren.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Moet u proberen een score van 100% te halen voor elk focusgebied?
Niet per se. De aanbevelingen zijn gebaseerd op de kennis en ervaringen die Microsoft-technici hebben opgedaan bij duizenden klantbezoeken. Er zijn echter geen twee serverinfrastructuren hetzelfde en specifieke aanbevelingen kunnen min of meer relevant voor u zijn. Sommige beveiligingsaanbevelingen zijn bijvoorbeeld minder relevant als uw virtuele machines niet worden blootgesteld aan internet. Sommige beschikbaarheidsaanbevelingen zijn mogelijk minder relevant voor services die ad hoc-gegevensverzameling en -rapportage met lage prioriteit bieden. Zaken die belangrijk zijn voor een volwassen bedrijf kunnen minder belangrijk zijn voor een start-up. U bepalen welke aandachtsgebieden uw prioriteiten zijn en vervolgens kijken hoe uw scores in de loop van de tijd veranderen.

Elke aanbeveling bevat richtlijnen over waarom het belangrijk is. U moet deze richtlijnen gebruiken om te beoordelen of de implementatie van de aanbeveling geschikt is voor u, gezien de aard van uw IT-services en de zakelijke behoeften van uw organisatie.

## <a name="use-health-check-focus-area-recommendations"></a>Aanbevelingen voor focusgebieden van statuscontrole gebruiken
Voordat u een beoordelingsoplossing in Azure Monitor gebruiken, moet u de oplossing hebben geïnstalleerd.  Nadat deze is geïnstalleerd, u de samenvatting van aanbevelingen bekijken met de tegel SQL Health Check op de **pagina Overzicht** voor Azure Monitor in de Azure-portal.

Bekijk de samengevatte nalevingsbeoordelingen voor uw infrastructuur en zoom vervolgens in op aanbevelingen.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aanbevelingen voor een aandachtsgebied bekijken en corrigerende maatregelen nemen
1. Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .
2. Klik in Azure Portal op **Meer services** in de linkerbenedenhoek. Typ **Monitor** in de lijst met resources. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Monitor**.
3. Selecteer **Meer**in het gedeelte **Inzichten** van het menu .  
4. Klik **op** de pagina Overzicht op de tegel **SQL Health Check.**
5. Bekijk op de pagina **Statuscontrole** de overzichtsinformatie in een van de focusgebiedbladen en klik vervolgens op een pagina om aanbevelingen voor dat focusgebied weer te geven.
6. Op een van de pagina's van het focusgebied u de geprioritteerde aanbevelingen voor uw omgeving bekijken. Klik op een aanbeveling onder **Getroffen objecten** om meer informatie te bekijken over waarom de aanbeveling wordt gedaan.<br><br> ![afbeelding van SQL Health Check-aanbevelingen](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. U corrigerende maatregelen nemen die worden voorgesteld in **voorgestelde acties**. Wanneer het item is aangepakt, worden in latere beoordelingen vastgelegd dat aanbevolen acties zijn uitgevoerd en wordt uw nalevingsscore verhoogd. Gecorrigeerde items worden weergegeven als **doorgegeven objecten**.

## <a name="ignore-recommendations"></a>Aanbevelingen negeren
Als u aanbevelingen hebt die u wilt negeren, u een tekstbestand maken dat Azure Monitor gebruikt om te voorkomen dat aanbevelingen in uw beoordelingsresultaten worden weergegeven.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Om aanbevelingen te identificeren die u negeert
1. Klik in het menu Azure Monitor op **Logboeken**.
2. Gebruik de volgende query om aanbevelingen op te sommen die zijn mislukt voor computers in uw omgeving.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Hier is een screenshot met de logquery:<br><br> ![mislukte aanbevelingen](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Kies aanbevelingen die u wilt negeren. U gebruikt de waarden voor RecommendationId in de volgende procedure.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Een tekstbestand IgnoreRecommendations.txt maken en gebruiken
1. Maak een bestand met de naam IgnoreRecommendations.txt.
2. Plak of typ elke RecommendationId voor elke aanbeveling die u wilt dat Azure Monitor op een afzonderlijke regel negeert en sla het bestand op en sluit het.
3. Plaats het bestand in de volgende map op elke computer waar u wilt dat Azure Monitor aanbevelingen negeert.
   * Op computers met de Microsoft Monitoring Agent (rechtstreeks verbonden of via Operations Manager) - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * Op de Operations Manager management server - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Op de beheerserver operations manager 2016 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Controleren of aanbevelingen worden genegeerd
1. Nadat de volgende geplande beoordeling is uitgevoerd, worden standaard om de 7 dagen de opgegeven aanbevelingen gemarkeerd als Genegeerd en worden ze niet weergegeven op het beoordelingsdashboard.
2. U de volgende query's voor logboekzoekopdrachten gebruiken om alle genegeerde aanbevelingen weer te geven.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Als u later besluit dat u genegeerde aanbevelingen wilt zien, verwijdert u alle ignorerecommendations.txt-bestanden of verwijdert u aanbevelingen ervan.

## <a name="sql-health-check-solution-faq"></a>Veelgestelde vragen over oplossingen van SQL-statuscontrole

*Welke controles worden uitgevoerd door de SQL Assessment-oplossing?*

* In de volgende query ziet u een beschrijving van alle controles die momenteel worden uitgevoerd:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
De resultaten kunnen vervolgens worden geëxporteerd naar Excel voor verdere beoordeling.


*Hoe vaak wordt een health check uitgevoerd?*

* De cheque loopt om de zeven dagen.

*Is er een manier om te configureren hoe vaak de controle wordt uitgevoerd?*

* Momenteel niet.

*Als er een andere server wordt ontdekt nadat ik de SQL Health Check-oplossing heb toegevoegd, wordt deze dan gecontroleerd?*

* Ja, zodra het wordt ontdekt wordt gecontroleerd vanaf dat dan, om de zeven dagen.

*Wanneer wordt een server buiten gebruik gesteld als deze buiten gebruik wordt gesteld?*

* Als een server gedurende 3 weken geen gegevens indient, wordt deze verwijderd.

*Wat is de naam van het proces dat de gegevensverzameling doet?*

* AdvisorAssessment.exe

*Hoe lang duurt het voordat gegevens worden verzameld?*

* De werkelijke gegevensverzameling op de server duurt ongeveer 1 uur. Het kan langer duren op servers met een groot aantal SQL-exemplaren of databases.

*Wat voor soort gegevens worden verzameld?*

* De volgende soorten gegevens worden verzameld:
  * WMI
  * Register
  * Prestatiemeteritems
  * SQL dynamic management views (DMV).

*Is er een manier om te configureren wanneer gegevens worden verzameld?*

* Momenteel niet.

*Waarom moet ik een Run As-account configureren?*

* Voor SQL Server wordt een klein aantal SQL-query's uitgevoerd. Om ze uit te voeren, moet een Run As-account met VIEW SERVER STATE-machtigingen voor SQL worden gebruikt.  Bovendien zijn lokale beheerdersreferenties vereist om WMI op te vragen.

*Waarom alleen de top 10 aanbevelingen weergeven?*

* In plaats van u een uitputtende overweldigende lijst met taken te geven, raden we u aan u eerst te concentreren op het aanpakken van de geprioritteerde aanbevelingen. Nadat u ze hebt aangepakt, worden aanvullende aanbevelingen beschikbaar. Als u de gedetailleerde lijst liever ziet, u alle aanbevelingen bekijken met behulp van de logboekzoekopdracht log van Logboekanalyse.

*Is er een manier om een aanbeveling te negeren?*

* Ja, zie Aanbevelingen hierboven [negeren.](#ignore-recommendations)

## <a name="next-steps"></a>Volgende stappen
* [Log query's](../log-query/log-query-overview.md) voor meer informatie over het analyseren van gedetailleerde SQL Health Check-gegevens en -aanbevelingen.
