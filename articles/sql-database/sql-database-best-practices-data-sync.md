---
title: Best practices voor Data Sync
description: Meer informatie over aanbevolen procedures voor het configureren en uitvoeren van Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: ee929fa227cb105b73bc929c13a768aabef37ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771680"
---
# <a name="best-practices-for-sql-data-sync"></a>Aanbevolen procedures voor SQL Data Sync 

In dit artikel worden aanbevolen procedures voor Azure SQL Data Sync beschreven.

Zie [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-database-sync-data.md) voor een overzicht van SQL Data Sync.

> [!IMPORTANT]
> Azure SQL Data Sync biedt op dit moment **geen** ondersteuning voor beheerde exemplaren voor Azure SQL Database.

## <a name="security-and-reliability"></a><a name="security-and-reliability"></a>Veiligheid en betrouwbaarheid

### <a name="client-agent"></a>Cliëntagent

-   Installeer de clientagent met behulp van het minst bevoorrechte gebruikersaccount met toegang tot de netwerkservice.  
-   Installeer de clientagent op een computer die niet de on-premises SQL Server-computer is.  
-   Registreer geen on-premises database bij meer dan één agent.    
    -   Vermijd dit zelfs als u verschillende tabellen synchroniseert voor verschillende synchronisatiegroepen.  
    -   Het registreren van een on-premises database met meerdere clientagents brengt uitdagingen met zich mee wanneer u een van de synchronisatiegroepen verwijdert.

### <a name="database-accounts-with-least-required-privileges"></a>Databaseaccounts met de minst vereiste bevoegdheden

-   **Voor synchronisatie-instellingen**. Tabel maken/wijzigen; Database wijzigen; Procedure maken; Schema selecteren/ wijzigen; Door de gebruiker gedefinieerd type maken.

-   **Voor doorlopende synchronisatie**. Selecteer/ Invoegen/ Bijwerken/ Verwijderen op tabellen die zijn geselecteerd voor synchronisatie en op synchronisatiemetagegevens en trackingtabellen; Machtigingen uitvoeren voor opgeslagen procedures die door de service zijn gemaakt; Voer toestemming uit voor door de gebruiker gedefinieerde tabeltypen.

-   **Voor deprovisioning**. Wijzigen op tabellen deel van sync; Selecteren/ Verwijderen op tabellen voor synchronisatiemetagegevens; Controle over synchronisatietabellen, opgeslagen procedures en door de gebruiker gedefinieerde typen.

Azure SQL Database ondersteunt slechts één set referenties. Als u deze taken binnen deze beperking wilt uitvoeren, gaat u op de volgende opties:

-   Wijzig de referenties voor verschillende fasen (bijvoorbeeld *referenties1* voor installatie en *referenties2* voor lopende).  
-   De machtiging voor de referenties wijzigen (dat wil zeggen de machtiging wijzigen nadat synchronisatie is ingesteld).

## <a name="setup"></a>Instellen

### <a name="database-considerations-and-constraints"></a><a name="database-considerations-and-constraints"></a>Databaseoverwegingen en beperkingen

#### <a name="sql-database-instance-size"></a>SQL Database-instantiegrootte

Wanneer u een nieuwe SQL Database-instantie maakt, stelt u de maximale grootte zo in dat deze altijd groter is dan de database die u implementeert. Als u de maximale grootte niet instelt op groter dan de geïmplementeerde database, mislukt de synchronisatie. Hoewel SQL Data Sync geen automatische groei biedt, u de `ALTER DATABASE` opdracht uitvoeren om de grootte van de database te vergroten nadat deze is gemaakt. Zorg ervoor dat u binnen de groottelimieten van SQL Database-instantieblijft.

> [!IMPORTANT]
> SQL Data Sync slaat extra metagegevens op bij elke database. Zorg ervoor dat u deze metagegevens verwerkt wanneer u de benodigde ruimte berekent. De hoeveelheid toegevoegde overhead is gerelateerd aan de breedte van de tabellen (bijvoorbeeld smalle tabellen vereisen meer overhead) en de hoeveelheid verkeer.

### <a name="table-considerations-and-constraints"></a><a name="table-considerations-and-constraints"></a>Tabeloverwegingen en -beperkingen

#### <a name="selecting-tables"></a>Tabellen selecteren

U hoeft niet alle tabellen in een database op te nemen in een synchronisatiegroep. De tabellen die u opneemt in een synchronisatiegroep zijn van invloed op de efficiëntie en kosten. Neem tabellen en de tabellen waarop ze afhankelijk zijn alleen op in een synchronisatiegroep als dat nodig is voor het bedrijfsleven.

#### <a name="primary-keys"></a>Primaire toetsen

Elke tabel in een synchronisatiegroep moet een primaire sleutel hebben. De SQL Data Sync-service kan een tabel met geen primaire sleutel niet synchroniseren.

Voordat u SQL Data Sync in de productie gebruikt, test u de eerste en doorlopende synchronisatieprestaties.

#### <a name="empty-tables-provide-the-best-performance"></a>Lege tabellen bieden de beste prestaties

Lege tabellen bieden de beste prestaties op initialisatietijd. Als de doeltabel leeg is, gebruikt Data Sync bulkinsert om de gegevens te laden. Anders doet Data Sync een vergelijking en invoeging per rij om te controleren op conflicten. Als prestaties echter geen probleem zijn, u synchronisatie instellen tussen tabellen die al gegevens bevatten.

### <a name="provisioning-destination-databases"></a><a name="provisioning-destination-databases"></a>Inrichting van bestemmingsdatabases

SQL Data Sync biedt basisdatabaseautoprovisioning.

In deze sectie worden de beperkingen van de inrichting in SQL Data Sync besproken.

#### <a name="autoprovisioning-limitations"></a>Beperkingen voor automatisch inrichten

SQL Data Sync heeft de volgende beperkingen voor autoprovisioning:

-   Selecteer alleen de kolommen die zijn gemaakt in de doeltabel. Kolommen die geen deel uitmaken van de synchronisatiegroep, worden niet in de doeltabellen ingericht.
-   Indexen worden alleen gemaakt voor geselecteerde kolommen. Als de brontabelindex kolommen bevat die geen deel uitmaken van de synchronisatiegroep, worden deze indexen niet in de doeltabellen ingericht.  
-   Indexen op XML-typekolommen zijn niet ingericht.  
-   CHECK-beperkingen zijn niet ingericht.  
-   Bestaande triggers op de brontabellen zijn niet ingericht.  
-   Weergaven en opgeslagen procedures worden niet gemaakt in de doeldatabase.
-   OP UPDATE TRAPSGEWIJs en OP TRAPSWAARDE VERWIJDEREN- acties voor externe sleutelbeperkingen worden niet opnieuw gemaakt in de doeltabellen.
-   Als u decimale of numerieke kolommen hebt met een precisie groter dan 28, kan SQL Data Sync tijdens het synchroniseren een conversieoverloopprobleem tegenkomen. We raden u aan de precisie van decimale of numerieke kolommen te beperken tot 28 of minder.

#### <a name="recommendations"></a>Aanbevelingen

-   Gebruik de sql data sync-mogelijkheid alleen wanneer u de service uitprobeert.  
-   Voor de productie, informeer het databaseschema.

### <a name="where-to-locate-the-hub-database"></a><a name="locate-hub"></a>Waar u de hubdatabase vinden

#### <a name="enterprise-to-cloud-scenario"></a>Enterprise-to-cloud-scenario

Om de latentie te minimaliseren, houdt u de hubdatabase dicht bij de grootste concentratie van het databaseverkeer van de synchronisatiegroep.

#### <a name="cloud-to-cloud-scenario"></a>Cloud-to-cloud-scenario

-   Wanneer alle databases in een synchronisatiegroep zich in één datacenter bevinden, moet de hub zich in hetzelfde datacenter bevinden. Deze configuratie vermindert de latentie en de kosten van gegevensoverdracht tussen datacenters.
-   Wanneer de databases in een synchronisatiegroep zich in meerdere datacenters bevinden, moet de hub zich in hetzelfde datacenter bevinden als de meeste databases en databaseverkeer.

#### <a name="mixed-scenarios"></a>Gemengde scenario's

Pas de voorgaande richtlijnen toe op complexe synchronisatiegroepconfiguraties, zoals configuraties die een mix zijn van enterprise-to-cloud- en cloud-to-cloud-scenario's.

## <a name="sync"></a>Sync

### <a name="avoid-slow-and-costly-initial-sync"></a><a name="avoid-a-slow-and-costly-initial-synchronization"></a>Vermijd langzame en kostbare eerste synchronisatie

In deze sectie bespreken we de eerste synchronisatie van een synchronisatiegroep. Meer informatie over hoe u voorkomen dat een eerste synchronisatie langer duurt en duurder is dan nodig is.

#### <a name="how-initial-sync-works"></a>Hoe de eerste synchronisatie werkt

Wanneer u een synchronisatiegroep maakt, begint u met gegevens in slechts één database. Als u gegevens in meerdere databases hebt, behandelt SQL Data Sync elke rij als een conflict dat moet worden opgelost. Deze conflictoplossing zorgt ervoor dat de eerste synchronisatie langzaam verloopt. Als u gegevens in meerdere databases hebt, kan de eerste synchronisatie enkele dagen tot enkele maanden duren, afhankelijk van de grootte van de database.

Als de databases zich in verschillende datacenters bevinden, moet elke rij tussen de verschillende datacenters reizen. Dit verhoogt de kosten van een eerste synchronisatie.

#### <a name="recommendation"></a>Aanbeveling

Begin indien mogelijk met gegevens in slechts één van de databases van de synchronisatiegroep.

### <a name="design-to-avoid-sync-loops"></a><a name="design-to-avoid-synchronization-loops"></a>Ontwerp om synchronisatielussen te voorkomen

Er treedt een synchronisatielus op wanneer er kringverwijzingen zijn binnen een synchronisatiegroep. In dat scenario wordt elke wijziging in één database eindeloos en circulair gerepliceerd via de databases in de synchronisatiegroep.   

Zorg ervoor dat u synchronisatielussen vermijdt, omdat deze leiden tot prestatiedegradatie en de kosten aanzienlijk kunnen verhogen.

### <a name="changes-that-fail-to-propagate"></a><a name="handling-changes-that-fail-to-propagate"></a>Wijzigingen die niet worden doorgegeven

#### <a name="reasons-that-changes-fail-to-propagate"></a>Redenen waarom wijzigingen niet worden doorgegeven

Wijzigingen kunnen niet worden doorgegeven om een van de volgende redenen:

-   Incompatibiliteit van schema/gegevenstype.
-   Null invoegen in niet-niet-tedere kolommen.
-   Het schenden van buitenlandse belangrijke beperkingen.

#### <a name="what-happens-when-changes-fail-to-propagate"></a>Wat gebeurt er als wijzigingen niet worden doorgevoerd?

-   De groep Synchroniseren laat zien dat de status **Waarschuwing** zich bevindt.
-   Details worden vermeld in de portal UI log viewer.
-   Als het probleem 45 dagen niet is opgelost, is de database verouderd.

> [!NOTE]
> Deze veranderingen verspreiden zich nooit. De enige manier om te herstellen in dit scenario is door de synchronisatiegroep opnieuw te maken.

#### <a name="recommendation"></a>Aanbeveling

Controleer de synchronisatiegroep en databasestatus regelmatig via de portal- en loginterface.


## <a name="maintenance"></a>Onderhoud

### <a name="avoid-out-of-date-databases-and-sync-groups"></a><a name="avoid-out-of-date-databases-and-sync-groups"></a>Verouderde databases voorkomen en groepen synchroniseren

Een synchronisatiegroep of een database in een synchronisatiegroep kan verouderd raken. Wanneer de status van een synchronisatiegroep **verouderd**is, werkt deze niet meer. Wanneer de status van een database **verouderd**is, kunnen gegevens verloren gaan. Het is het beste om dit scenario te vermijden in plaats van te proberen om te herstellen van het.

#### <a name="avoid-out-of-date-databases"></a>Verouderde databases voorkomen

De status van een database is ingesteld op **verouderd** wanneer deze 45 dagen of langer offline is geweest. Als u een **verouderde** status in een database wilt voorkomen, moet u ervoor zorgen dat geen van de databases 45 dagen of langer offline is.

#### <a name="avoid-out-of-date-sync-groups"></a>Verouderde synchronisatiegroepen voorkomen

De status van een synchronisatiegroep is ingesteld op **verouderd** wanneer een wijziging in de synchronisatiegroep gedurende 45 dagen of langer niet wordt doorgegeven aan de rest van de synchronisatiegroep. Als u een verouderde status in een synchronisatiegroep wilt voorkomen, controleert u regelmatig het geschiedenislogboek **van** de synchronisatiegroep. Controleer of alle conflicten zijn opgelost en dat wijzigingen worden gepropageerd in de databases van de synchronisatiegroep.

Een synchronisatiegroep kan een wijziging om een van deze redenen niet toepassen:

-   Schema-onverenigbaarheid tussen tabellen.
-   Gegevensonverenigbaarheid tussen tabellen.
-   Een rij invoegen met een null-waarde in een kolom die geen null-waarden toestaat.
-   Een rij bijwerken met een waarde die in strijd is met een beperking van een buitenlandse sleutel.

Ga als u erop uit dat verouderde synchronisatiegroepen verouderd zijn:

-   Werk het schema bij om de waarden in de mislukte rijen toe te staan.
-   Werk de buitenlandse sleutelwaarden bij om de waarden op te nemen die in de mislukte rijen zijn opgenomen.
-   Werk de gegevenswaarden in de mislukte rij bij, zodat ze compatibel zijn met het schema of de buitenlandse sleutels in de doeldatabase.

### <a name="avoid-deprovisioning-issues"></a><a name="avoid-deprovisioning-issues"></a>Vermijd deprovisioning-problemen

In sommige omstandigheden kan het verwijderen van een database bij een clientagent ertoe leiden dat synchronisatie mislukt.

#### <a name="scenario"></a>Scenario

1. Synchronisatiegroep A is gemaakt met behulp van een SQL Database-exemplaar en een on-premises SQL Server-database, die is gekoppeld aan lokale agent 1.
2. Dezelfde on-premises database wordt geregistreerd bij lokale agent 2 (deze agent is niet gekoppeld aan een synchronisatiegroep).
3. Als u de on-premises database uit de lokale agent 2 verwijdert, worden de tracking- en metatabellen voor synchronisatiegroep A voor de on-premises database verwijderd.
4. Synchronisatiegroep A-bewerkingen mislukken met deze fout: "De huidige bewerking kan niet worden voltooid omdat de database niet is ingericht voor synchronisatie of u geen machtigingen hebt voor de synchronisatieconfiguratietabellen."

#### <a name="solution"></a>Oplossing

Als u dit scenario wilt voorkomen, registreert u een database niet bij meer dan één agent.

Ga als volgt te werk om te herstellen van dit scenario:

1. Verwijder de database uit elke synchronisatiegroep waartoe deze behoort.  
2. Voeg de database weer toe aan elke synchronisatiegroep waaruit u deze hebt verwijderd.  
3. Implementeer elke getroffen synchronisatiegroep (deze actie voorziet in de database).  

### <a name="modifying-a-sync-group"></a><a name="modifying-your-sync-group"></a>Een synchronisatiegroep wijzigen

Probeer een database niet uit een synchronisatiegroep te verwijderen en bewerk de synchronisatiegroep vervolgens zonder eerst een van de wijzigingen te implementeren.

Verwijder in plaats daarvan eerst een database uit een synchronisatiegroep. Implementeer vervolgens de wijziging en wacht tot de provisioning is voltooid. Wanneer de deprovisioning is voltooid, u de synchronisatiegroep bewerken en de wijzigingen implementeren.

Als u probeert een database te verwijderen en vervolgens een synchronisatiegroep bewerkt zonder eerst een van de wijzigingen te implementeren, mislukt de ene of de andere bewerking. De portalinterface kan inconsistent worden. Als dit gebeurt, vernieuwt u de pagina om de juiste status te herstellen.

### <a name="avoid-schema-refresh-timeout"></a>Time-out van schemavernieuwen voorkomen

Als u een complex schema moet synchroniseren, u tijdens een schemaverversing een 'bewerkingstime-out' tegenkomen als de synchronisatiemetagegevensdatabase een lagere SKU heeft (bijvoorbeeld: basic). 

#### <a name="solution"></a>Oplossing

Om dit probleem te verhelpen, moet u uw database met synchronisatiemetagegevens opschalen naar een hogere SKU, zoals S3. 

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over SQL Data Sync:

-   Overzicht: [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-database-sync-data.md)
-   Data Sync instellen
    - In de portal: [Zelfstudie: SQL Data Sync instellen om gegevens te synchroniseren tussen Azure SQL Database en SQL Server on-premises](sql-database-get-started-sql-data-sync.md)
    - Met PowerShell
        -  [PowerShell gebruiken om meerdere Azure SQL-databases te synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell gebruiken om te synchroniseren tussen een Azure SQL-database en een on-premises database](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data Sync-agent: [Data Sync-agent voor Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Monitor - [SQL-gegevenssynchronisatie met Azure Monitor-logboeken controleren](sql-database-sync-monitor-oms.md)
-   Problemen oplossen: [Problemen met Azure SQL Data Sync oplossen](sql-database-troubleshoot-data-sync.md)
-   Het synchronisatieschema bijwerken
    -   Met Transact-SQL: [De replicatie van schemawijzigingen in Azure SQL Data Sync automatiseren](sql-database-update-sync-schema.md)
    -   Met PowerShell: [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/sql-database-sync-update-schema.md)

Zie voor meer informatie over SQL Database:

-   [SQL-databaseoverzicht](sql-database-technical-overview.md)
-   [Beheer van de levenscyclus van de database](https://msdn.microsoft.com/library/jj907294.aspx)
