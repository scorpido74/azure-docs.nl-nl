---
title: Optimaliseer uw SQL Server-omgeving met Azure Monitor | Microsoft Docs
description: Met Azure Monitor kunt u de SQL Health Check-oplossing gebruiken om het risico en de status van uw omgevingen op een regel matig interval te evalueren.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 03/28/2019
ms.openlocfilehash: 7808ead7ec4191bdf17e3ab225aeaa909abd7d08
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900670"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Optimaliseer uw SQL-omgeving met de SQL Server Health Check-oplossing in Azure Monitor

![Symbool voor SQL-status controle](./media/sql-assessment/sql-assessment-symbol.png)

U kunt de SQL Health Check-oplossing gebruiken om het risico en de status van uw server omgevingen een regel matig interval te evalueren. Dit artikel helpt u bij het installeren van de oplossing, zodat u corrigerende maat regelen kunt nemen voor potentiële problemen.

Deze oplossing biedt een lijst met aanbevelingen die specifiek zijn voor uw geïmplementeerde server infrastructuur. De aanbevelingen worden gecategoriseerd op zes focus gebieden die u helpen om het risico snel te begrijpen en corrigerende maat regelen te nemen.

De aanbevelingen zijn gebaseerd op de kennis en ervaring die micro soft-technici hebben opgedaan vanuit duizenden klant bezoeken. Elke aanbeveling bevat richt lijnen voor de reden waarom een probleem te voor u is en hoe u de voorgestelde wijzigingen kunt implementeren.

U kunt focus gebieden kiezen die het belangrijkst zijn voor uw organisatie en uw voortgang volgen met betrekking tot het uitvoeren van een risico vrije en goede omgeving.

Nadat u de oplossing hebt toegevoegd en een evaluatie is voltooid, wordt de samenvattings informatie voor de focus gebieden weer gegeven op het dash board voor de **SQL-status controle** voor de infra structuur in uw omgeving. In de volgende secties wordt beschreven hoe u de informatie in het dash board van de **SQL-status controle** gebruikt, waar u de aanbevolen acties voor uw SQL Server-infra structuur kunt bekijken en weer geven.

![afbeelding van de tegel SQL-status controle](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![afbeelding van het dash board van de SQL-status controle](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Vereisten

* Voor de oplossing SQL Health check is een ondersteunde versie van .NET Framework 4 geïnstalleerd op elke computer waarop micro soft Monitoring Agent (MMA) is geïnstalleerd.  De MMA-agent wordt gebruikt door System Center 2016-Operations Manager en Operations Manager 2012 R2 en Azure Monitor.  
* De oplossing ondersteunt SQL Server versie 2012, 2014 en 2016.
* Een Log Analytics-werk ruimte om de SQL Health Check-oplossing toe te voegen vanuit Azure Marketplace in de Azure Portal.  Als u de oplossing wilt installeren, moet u een beheerder of Inzender zijn in het Azure-abonnement.

  > [!NOTE]
  > Nadat u de oplossing hebt toegevoegd, wordt het bestand AdvisorAssessment. exe toegevoegd aan servers met agents. Configuratie gegevens worden gelezen en vervolgens naar Azure Monitor in de Cloud verzonden voor verwerking. Logica wordt toegepast op de ontvangen gegevens en de gegevens worden vastgelegd door de cloudservice.
  >
  >

Voor het uitvoeren van de status controle op uw SQL Server-servers, hebben ze een agent en connectiviteit tot Azure Monitor met een van de volgende ondersteunde methoden:

1. Installeer de [micro soft Monitoring Agent (MMA)](../../azure-monitor/platform/agent-windows.md) als de server nog niet wordt bewaakt door System Center 2016-Operations Manager of Operations Manager 2012 R2.
2. Als het wordt bewaakt met System Center 2016-Operations Manager of Operations Manager 2012 R2 en de beheer groep is niet geïntegreerd met Azure Monitor, kan de server meerdere locaties hebben met Log Analytics om gegevens te verzamelen en door te sturen naar de service en nog steeds bewaakt door Operations Manager.  
3. Als uw Operations Manager-beheer groep is geïntegreerd met de service, moet u de domein controllers voor het verzamelen van gegevens door de service toevoegen volgens de stappen onder door [agents beheerde computers toevoegen](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) nadat u de oplossing hebt ingeschakeld in uw werk ruimte.  

De agent op uw SQL Server die aan een Operations Manager beheer groep rapporteert, verzamelt gegevens, stuurt deze door naar de toegewezen beheer server en wordt vervolgens rechtstreeks van een beheer server naar Azure Monitor verzonden.  De gegevens worden niet naar de Operations Manager-data bases geschreven.  

Als de SQL Server wordt bewaakt door Operations Manager, moet u een Operations Manager uitvoeren als-account configureren. Zie [Operations Manager run-as-accounts voor Azure monitor](#operations-manager-run-as-accounts-for-log-analytics) hieronder voor meer informatie.

## <a name="sql-health-check-data-collection-details"></a>Details van de gegevens verzameling voor SQL-status controle
Met de SQL-status controle worden gegevens verzameld uit de volgende bronnen met behulp van de agent die u hebt ingeschakeld:

* Windows Management Instrumentation (WMI)
* Register
* Prestatiemeteritems
* Resultaten van dynamische beheer weergave SQL Server

Gegevens worden op het SQL Server verzameld en naar Log Analytics om de zeven dagen doorgestuurd.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Operations Manager uitvoeren als-accounts voor Log Analytics
Log Analytics gebruikt de Operations Manager agent en beheer groep om gegevens te verzamelen en te verzenden naar de Log Analytics-service. Log Analytics bouwt voort op de Management Packs voor werk belastingen om services te kunnen toevoegen. Elke workload vereist systeemspecifieke bevoegdheden om Management Packs uit te voeren in een andere beveiligings context, zoals een domein gebruikers account. U moet referentie gegevens opgeven door een Operations Manager uitvoeren als-account te configureren.

Gebruik de volgende informatie om de Operations Manager uitvoeren als-account in te stellen voor de SQL-status controle.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Het run as-account voor de SQL-status controle instellen
 Als u de SQL Server-management pack al gebruikt, moet u die run as-configuratie gebruiken.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Het SQL run as-account configureren in de operations-console
> [!NOTE]
> Standaard worden werk stromen in de management pack uitgevoerd in de beveiligings context van het lokale systeem account. Als u micro soft monitoring agent gebruikt die rechtstreeks is verbonden met de service en niet rechtstreeks aan een Operations Manager-beheer groep meldt, slaat u de stappen 1-5 hieronder over en voert u het T-SQL-of Power shell-voor beeld uit, waarbij u NT AUTHORITY\SYSTEM opgeeft als gebruikers naam.
>
>

1. Open de operations-console in Operations Manager en klik vervolgens op **beheer**.
2. Klik onder **Run as-configuratie**op **profielen**en open **SQL-evaluatie uitvoeren als-profiel**.
3. Klik op de pagina **uitvoeren als-accounts** op **toevoegen**.
4. Selecteer een Windows run as-account dat de referenties bevat die nodig zijn voor SQL Server, of klik op **Nieuw** om er een te maken.

   > [!NOTE]
   > Het type run as-account moet Windows zijn. Het run as-account moet ook deel uitmaken van de lokale groep Administrators op alle Windows-servers die als host fungeren voor SQL Server exemplaren.
   >
   >
5. Klik op **Opslaan**.
6. Wijzig en voer vervolgens het volgende T-SQL-voor beeld uit op elk SQL Server-exemplaar om minimale machtigingen te verlenen die vereist zijn voor het uitvoeren als-account om de status controle uit te voeren. U hoeft dit echter niet te doen als een uitvoeren als-account al deel uitmaakt van de serverrol sysadmin op SQL Server exemplaren.

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

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Het SQL run as-account configureren met Windows Power shell
Open een Power shell-venster en voer het volgende script uit nadat u het hebt bijgewerkt met uw gegevens:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>Uitleg over de prioriteit van aanbevelingen
Aan elke aanbevolen aanbeveling wordt een wegings waarde gegeven die het relatieve belang van de aanbeveling identificeert. Alleen de tien belangrijkste aanbevelingen worden weer gegeven.

### <a name="how-weights-are-calculated"></a>Hoe de wegingen worden berekend
Wegingen zijn aggregatie waarden op basis van drie belang rijke factoren:

* De *kans* dat een probleem is geïdentificeerd, veroorzaakt problemen. Een hogere kans komt overeen met een grotere algemene score voor de aanbeveling.
* De *impact* van het probleem in uw organisatie als er een probleem is. Een hogere impact komt overeen met een grotere algemene score voor de aanbeveling.
* De *inspanningen* die nodig zijn om de aanbeveling te implementeren. Een hogere inspanning is gelijk aan een kleinere algemene score voor de aanbeveling.

De weging voor elke aanbeveling wordt uitgedrukt als een percentage van de totale beschik bare score voor elk focus gebied. Als bijvoorbeeld een aanbeveling in het focus gebied beveiliging en naleving een Score van 5% heeft, verhoogt de implementatie van die aanbeveling de algehele score voor beveiliging en naleving met 5%.

### <a name="focus-areas"></a>Focus gebieden
**Beveiliging en naleving** : dit focus gebied bevat aanbevelingen voor mogelijke beveiligings dreigingen en inbreuken, bedrijfs beleid en technische, juridische en wettelijke nalevings vereisten.

**Beschik baarheid en bedrijfs continuïteit** : dit focus gebied bevat aanbevelingen voor de beschik baarheid van de service, de tolerantie van uw infra structuur en zakelijke beveiliging.

**Prestaties en schaal baarheid** : dit focus gebied bevat aanbevelingen om de IT-infra structuur van uw organisatie te verg Roten, zodat uw IT-omgeving voldoet aan de huidige prestatie vereisten en kan reageren op veranderende infrastructuur behoeften.

**Upgrade, migratie en implementatie** : dit focus gebied bevat aanbevelingen om u te helpen bij het upgraden, migreren en implementeren van SQL Server naar uw bestaande infra structuur.

**Bewerkingen en controle** : dit focus gebied bevat aanbevelingen om uw IT-activiteiten te stroom lijnen, preventief onderhoud te implementeren en de prestaties te maximaliseren.

**Wijzigings-en configuratie beheer** : dit focus gebied bevat aanbevelingen voor het beveiligen van dagelijkse bewerkingen, zodat de wijzigingen geen negatieve invloed hebben op uw infra structuur, het bepalen van wijzigings beheer procedures en het bijhouden en controleren van het systeem configuraties.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Moet u de Score 100% in elk focus gebied richten?
Dat hoeft niet. De aanbevelingen zijn gebaseerd op de kennis en ervaringen van micro soft-technici over duizenden klant bezoeken. Er zijn echter geen twee server infrastructuren hetzelfde, en specifieke aanbevelingen kunnen meer of minder relevant zijn voor u. Sommige beveiligings aanbevelingen kunnen bijvoorbeeld minder relevant zijn als uw virtuele machines niet worden blootgesteld aan Internet. Enkele Beschik baarheid van aanbevelingen kan minder relevant zijn voor services die ad-hoc gegevens verzameling en-rapportage met lage prioriteit bieden. Problemen die belang rijk zijn voor een volwassen bedrijf zijn mogelijk minder belang rijk voor het opstarten. U kunt ook bepalen welke focus gebieden uw prioriteiten hebben en vervolgens kijken hoe uw scores in de loop van de tijd veranderen.

Elke aanbeveling bevat richt lijnen over waarom het belang rijk is. Gebruik deze richt lijnen om te evalueren of implementatie van de aanbeveling geschikt is voor u, gezien de aard van uw IT-Services en de zakelijke behoeften van uw organisatie.

## <a name="use-health-check-focus-area-recommendations"></a>Aanbevelingen voor het focus gebied status controleren gebruiken
Voordat u een beoordelings oplossing in Azure Monitor kunt gebruiken, moet u de oplossing hebben geïnstalleerd.  Nadat deze is geïnstalleerd, kunt u de samen vatting van de aanbevelingen bekijken met behulp van de tegel SQL-status controle op de pagina **overzicht** voor Azure monitor in het Azure Portal.

Bekijk de samen vatting van de nalevings evaluaties voor uw infra structuur en vervolgens inzoomen op aanbevelingen.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aanbevelingen voor een focus gebied weer geven en corrigerende actie ondernemen
1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Klik in Azure Portal op **Meer services** in de linkerbenedenhoek. Typ **Monitor** in de lijst met resources. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Monitor**.
3. Selecteer in de sectie **inzichten** van het menu **meer**.  
4. Klik op de pagina **overzicht** op de tegel **SQL-status controle** .
5. Controleer op de pagina **status controle** de samenvattings informatie op een van de Blades van het focus gebied en klik vervolgens op een van de aanbevelingen voor het focus gebied weer te geven.
6. Op een van de focus gebied pagina's kunt u de prioriteiten weer geven die zijn gemaakt voor uw omgeving. Klik op een aanbeveling onder **betrokken objecten** om details weer te geven over waarom de aanbeveling is gedaan.<br><br> ![installatie kopie van de aanbevelingen voor de SQL-status controle](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. U kunt corrigerende acties uitvoeren die worden voorgesteld in **aanbevolen acties**. Wanneer het item is opgelost, worden in latere evaluaties vastgelegd dat de aanbevolen acties zijn uitgevoerd en wordt de nalevings Score verhoogd. Gecorrigeerde items worden weer gegeven als **door gegeven objecten**.

## <a name="ignore-recommendations"></a>Aanbevelingen negeren
Als u aanbevelingen hebt die u wilt negeren, kunt u een tekst bestand maken dat Azure Monitor gebruikt om te voor komen dat aanbevelingen worden weer gegeven in de evaluatie resultaten.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Om te bepalen welke aanbevelingen u wilt negeren
1. Klik in het menu Azure Monitor op **Logboeken**.
2. Gebruik de volgende query om de aanbevelingen weer te geven die zijn mislukt voor computers in uw omgeving.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    Hier ziet u een scherm opname met de logboek query:<br><br> ![mislukte aanbevelingen](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Kies de aanbevelingen die u wilt negeren. In de volgende procedure gebruikt u de waarden voor RecommendationId.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Een IgnoreRecommendations. txt-tekst bestand maken en gebruiken
1. Maak een bestand met de naam IgnoreRecommendations. txt.
2. Plak of typ elke RecommendationId voor elke aanbeveling die Azure Monitor wilt negeren op een afzonderlijke regel en sla het bestand op en sluit het.
3. Plaats het bestand in de volgende map op elke computer waar u wilt dat Azure Monitor aanbevelingen negeert.
   * Op computers met micro soft Monitoring Agent (rechtstreeks verbonden of via Operations Manager)- *Systeem station*: \Program Files\Microsoft monitoring Agent\Agent
   * Op de Operations Manager-beheer server- *System Drive*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Op de Operations Manager 2016-beheer server- *System Drive*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Controleren of de aanbevelingen worden genegeerd
1. Nadat de volgende geplande evaluatie standaard elke zeven dagen wordt uitgevoerd, worden de opgegeven aanbevelingen gemarkeerd als genegeerd en worden ze niet weer gegeven in het beoordelings dashboard.
2. U kunt de volgende zoek query's in het logboek gebruiken om alle genegeerde aanbevelingen weer te geven.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Als u later besluit dat u genegeerde aanbevelingen wilt zien, verwijdert u de IgnoreRecommendations. txt-bestanden of verwijdert u RecommendationIDs van de andere.

## <a name="sql-health-check-solution-faq"></a>Veelgestelde vragen over de SQL Health Check-oplossing
*Hoe vaak wordt een status controle uitgevoerd?*

* De controle wordt elke zeven dagen uitgevoerd.

*Is er een manier om te configureren hoe vaak de controle wordt uitgevoerd?*

* Op dit moment niet.

*Als er een andere server wordt gedetecteerd nadat ik de SQL Health Check-oplossing heb toegevoegd, wordt deze gecontroleerd?*

* Ja, zodra deze is gedetecteerd, wordt dit na elke zeven dagen gecontroleerd.

*Als een server buiten gebruik wordt gesteld, wordt deze verwijderd uit de status controle?*

* Als een server drie weken geen gegevens verzendt, wordt deze verwijderd.

*Wat is de naam van het proces dat het verzamelen van gegevens doet?*

* AdvisorAssessment. exe

*Hoe lang duurt het voordat gegevens worden verzameld?*

* De werkelijke gegevens verzameling op de server duurt ongeveer 1 uur. Het kan langer duren op servers met een groot aantal SQL-exemplaren of-data bases.

*Welk type gegevens worden verzameld?*

* De volgende typen gegevens worden verzameld:
  * WMI
  * Register
  * Prestatiemeteritems
  * Dynamische beheer weergaven van SQL (DMV).

*Is er een manier om te configureren wanneer gegevens worden verzameld?*

* Op dit moment niet.

*Waarom moet ik een uitvoeren als-account configureren?*

* Voor SQL Server wordt een klein aantal SQL-query's uitgevoerd. Om ze te kunnen uitvoeren, moet een uitvoeren als-account met de machtigingen voor de weergave SERVER status voor SQL worden gebruikt.  Daarnaast zijn lokale beheerders referenties vereist om te kunnen zoeken in WMI.

*Waarom worden alleen de tien beste aanbevelingen weer gegeven?*

* In plaats van een uitgebreide lijst met taken te geven, raden we u aan om eerst te focussen op de aanbevelingen met prioriteit. Nadat u ze hebt geadresseerd, worden er extra aanbevelingen beschikbaar. Als u de gedetailleerde lijst wilt zien, kunt u alle aanbevelingen weer geven met behulp van de Log Analytics logboeken zoeken.

*Is er een manier om een aanbeveling te negeren?*

* Ja, zie de sectie [aanbevelingen negeren](#ignore-recommendations) hierboven.

## <a name="next-steps"></a>Volgende stappen
* [Meld query's](../log-query/log-query-overview.md) om te leren hoe u gedetailleerde gegevens en aanbevelingen voor de SQL Health Checker kunt analyseren.
