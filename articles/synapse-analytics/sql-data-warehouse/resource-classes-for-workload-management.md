---
title: Resourceklassen voor werkbelastingbeheer
description: Richtlijnen voor het gebruik van resourceklassen voor het beheren van gelijktijdigheid en rekenresources voor query's in Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 47fd30fbb6e6836d6edf18ac68164d515f3aeb93
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350734"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Workloadbeheer met resourceklassen in Azure Synapse Analytics

Richtlijnen voor het gebruik van resourceklassen voor het beheren van geheugen en gelijktijdigheid voor SQL Analytics-query's in Azure Synapse.  

## <a name="what-are-resource-classes"></a>Wat zijn resourceklassen

De prestatiecapaciteit van een query wordt bepaald door de resourceklasse van de gebruiker.  Resourceklassen zijn vooraf bepaalde resourcelimieten in SQL Analytics die bepalen van compute resources en gelijktijdigheid voor queryuitvoering. Resourceklassen kunnen u helpen resources voor uw query's te configureren door limieten in te stellen voor het aantal query's dat gelijktijdig wordt uitgevoerd en op de rekenbronnen die aan elke query zijn toegewezen.  Er is een afweging tussen geheugen en gelijktijdigheid.

- Kleinere resourceklassen verminderen het maximale geheugen per query, maar verhogen de gelijktijdigheid.
- Grotere resourceklassen verhogen het maximale geheugen per query, maar verminderen de gelijktijdigheid.

Er zijn twee soorten resourceklassen:

- Statische resourcesklassen, die zeer geschikt zijn voor meer gelijktijdigheid op een vaste grootte van de gegevensset.
- Dynamische resourceklassen, die zeer geschikt zijn voor gegevenssets die in omvang toenemen en betere prestaties nodig hebben naarmate het serviceniveau wordt opgeschaald.

Resourceklassen gebruiken gelijktijdige sleuven om het verbruik van resources te meten.  [Gelijktijdige slots](#concurrency-slots) worden later in dit artikel toegelicht.

- Zie [Geheugen- en gelijktijdigheidslimieten](memory-concurrency-limits.md)als u het resourcegebruik voor de resourceklassen wilt weergeven.
- Als u de resourceklasse wilt aanpassen, u de query onder een andere gebruiker uitvoeren of het lidmaatschap [van de resourceklasse van de huidige gebruiker wijzigen.](#change-a-users-resource-class)

### <a name="static-resource-classes"></a>Statische resourceklassen

Statische resourceklassen wijzen dezelfde hoeveelheid geheugen toe, ongeacht het huidige prestatieniveau, dat wordt gemeten in [gegevensmagazijneenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md). Aangezien query's dezelfde geheugentoewijzing krijgen, ongeacht [scaling out the data warehouse](quickstart-scale-compute-portal.md) het prestatieniveau, kunnen meer query's worden uitgevoerd binnen een resourceklasse.  Statische resourceklassen zijn ideaal als het gegevensvolume bekend en constant is.

De statische resourceklassen worden geïmplementeerd met deze vooraf gedefinieerde databaserollen:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dynamische resourceklassen

Dynamische resourceklassen wijzen een variabele hoeveelheid geheugen toe, afhankelijk van het huidige serviceniveau. Hoewel statische resourceklassen gunstig zijn voor hogere gelijktijdigheid en statische gegevensvolumes, zijn dynamische resourceklassen beter geschikt voor een groeiende of variabele hoeveelheid gegevens.  Wanneer u opschaalt naar een groter serviceniveau, krijgen uw query's automatisch meer geheugen.  

De dynamische resourceklassen worden geïmplementeerd met deze vooraf gedefinieerde databaserollen:

- smallrc smallrc
- mediumrc (mediumrc)
- largerc biggerc biggerc biggerc
- xlargerc xlargerc xlargerc

De geheugentoewijzing voor elke resourceklasse is als volgt. 

| Serviceniveau  | smallrc smallrc           | mediumrc (mediumrc)               | largerc biggerc biggerc biggerc                | xlargerc xlargerc xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70%                    |
| DW200c         | 12.5%             | 12.5%                  | 22%                    | 70%                    |
| DW300c         | 8%                | 10%                    | 22%                    | 70%                    |
| DW400c         | 6.25%             | 10%                    | 22%                    | 70%                    |
| DW500c         | 5%                | 10%                    | 22%                    | 70%                    |
| DW1000c naar<br> DW30000c | 3%       | 10%                    | 22%                    | 70%                    |



### <a name="default-resource-class"></a>Standaardresourceklasse

Standaard is elke gebruiker lid van de dynamische resourceklasse **smallrc**.

De resourceklasse van de servicebeheerder is vastgesteld op smallrc en kan niet worden gewijzigd.  De servicebeheerder is de gebruiker die tijdens het inrichtingsproces is gemaakt.  De servicebeheerder in deze context is de aanmelding die is opgegeven voor de 'Serveradmin login' bij het maken van een nieuw SQL Analytics-exemplaar met een nieuwe server.

> [!NOTE]
> Gebruikers of groepen die zijn gedefinieerd als Active Directory-beheerder, zijn ook servicebeheerders.
>
>

## <a name="resource-class-operations"></a>Resourceklassebewerkingen

Resourceklassen zijn ontworpen om de prestaties voor gegevensbeheer- en manipulatieactiviteiten te verbeteren. Complexe query's kunnen ook profiteren van het uitvoeren onder een grote resourceklasse. Queryprestaties voor grote joins en sorteert kunnen bijvoorbeeld verbeteren wanneer de resourceklasse groot genoeg is om de query in het geheugen uit te voeren.

### <a name="operations-governed-by-resource-classes"></a>Bewerkingen die worden geregeld door resourceklassen

Deze bewerkingen worden geregeld door resourceklassen:

- INVOEGEN-SELECTEREN, BIJWERKEN, VERWIJDEREN
- SELECT (bij het opvragen van gebruikerstabellen)
- INDEX WIJZIGEN - HERBOUWEN OF REORGANISEREN
- TABEL OPNIEUW WIJZIGEN
- CREATE INDEX
- GECLUSTERDE COLUMNSTORE-INDEX MAKEN
- TABEL MAKEN ALS SELECTEREN (CTAS)
- Gegevens laden
- Gegevensverplaatsingsoperaties uitgevoerd door de Data Movement Service (DMS)

> [!NOTE]  
> SELECT-instructies over dynamische beheerweergaven (DMVs) of andere systeemweergaven worden niet bepaald door een van de gelijktijdigheidslimieten. U het systeem controleren, ongeacht het aantal query's dat erop wordt uitgevoerd.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Bewerkingen die niet worden geregeld door resourceklassen

Sommige query's worden altijd uitgevoerd in de smallrc-resourceklasse, ook al is de gebruiker lid van een grotere resourceklasse. Deze vrijgestelde query's tellen niet mee voor de gelijktijdigheidslimiet. Als de gelijktijdigheidslimiet bijvoorbeeld 16 is, kunnen veel gebruikers uit systeemweergaven worden geselecteerd zonder dat dit gevolgen heeft voor de beschikbare gelijktijdigheidssleuven.

De volgende instructies zijn vrijgesteld van resourceklassen en worden altijd uitgevoerd in smallrc:

- TABEL MAKEN of NEERZETTEN
- WIJZIGING TABEL ... PARTITIE OVERSCHAKELEN, SPLITSEN of SAMENVOEGEN
- INDEX WIJZIGEN UITSCHAKELEN
- DROP INDEX
- STATISTIEKEN MAKEN, BIJWERKEN OF DROP
- Tabel Afkappen
- TOESTEMMING WIJZIGEN
- AANMELDING MAKEN
- GEBRUIKER MAKEN, WIJZIGEN of NEERZETTEN
- PROCEDURE MAKEN, WIJZIGEN of VALLEN
- WEERGAVE MAKEN of NEERZETTEN
- WAARDEN INVOEGEN
- SELECT uit systeemweergaven en DMVs
- Uitleggen
- Dbcc

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Gelijktijdigheidsleuven

Gelijktijdige sleuven zijn een handige manier om de beschikbare bronnen voor query-uitvoering bij te houden. Ze zijn als tickets die u zitplaatsen te reserveren bij een concert te reserveren kopen omdat zitplaatsen beperkt is. Het totale aantal gelijktijdige sleuven per datawarehouse wordt bepaald door het serviceniveau. Voordat een query kan worden uitgevoerd, moet deze voldoende gelijktijdigheidssleuven kunnen reserveren. Wanneer een query is voltooid, worden de gelijktijdige sleuven vrijgegeven.  

- Een query met 10 gelijktijdige sleuven heeft toegang tot 5 keer meer rekenresources dan een query die wordt uitgevoerd met 2 gelijktijdige sleuven.
- Als voor elke query 10 gelijktijdige sleuven nodig zijn en er 40 gelijktijdige sleuven zijn, kunnen slechts 4 query's gelijktijdig worden uitgevoerd.

Alleen resourcebeheerquery's verbruiken gelijktijdige sleuven. Systeemquery's en sommige triviale query's verbruiken geen slots. Het exacte aantal verbruikte gelijktijdige sleuven wordt bepaald door de resourceklasse van de query.

## <a name="view-the-resource-classes"></a>De resourceklassen weergeven

Resourceklassen worden geïmplementeerd als vooraf gedefinieerde databaserollen. Er zijn twee soorten resourceklassen: dynamisch en statisch. Als u een lijst met resourceklassen wilt weergeven, gebruikt u de volgende query:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>De resourceklasse van een gebruiker wijzigen

Resourceklassen worden geïmplementeerd door gebruikers toe te wijzen aan databaserollen. Wanneer een gebruiker een query uitvoert, wordt de query uitgevoerd met de resourceklasse van de gebruiker. Als een gebruiker bijvoorbeeld lid is van de statische rc10-databaserol, worden de query's uitgevoerd met kleine hoeveelheden geheugen. Als een databasegebruiker lid is van de xlargerc- of staticrc80-databaserollen, worden de query's uitgevoerd met grote hoeveelheden geheugen.

Als u de resourceklasse van een gebruiker wilt vergroten, gebruikt u [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) om de gebruiker toe te voegen aan een databaserol van een grote resourceklasse.  De onderstaande code voegt een gebruiker toe aan de grotere databaserol.  Elke aanvraag krijgt 22% van het systeemgeheugen.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Als u de resourceklasse wilt verlagen, gebruikt u [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  Als 'loaduser' geen lid of andere resourceklassen is, gaan ze naar de standaardklasse smallrc resource met een geheugensubsidie van 3%.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Prioriteit resourceklasse

Gebruikers kunnen lid zijn van meerdere resourceklassen. Wanneer een gebruiker tot meer dan één resourceklasse behoort:

- Dynamische resourceklassen hebben voorrang op statische resourceklassen. Als een gebruiker bijvoorbeeld lid is van zowel mediumrc(dynamic) als staticrc80 (statisch), worden query's uitgevoerd met mediumrc.
- Grotere resourceklassen hebben voorrang op kleinere resourceklassen. Als een gebruiker bijvoorbeeld lid is van mediumrc en biggerc, worden query's uitgevoerd met biggerc. Evenzo, als een gebruiker lid is van zowel staticrc20 als statirc80, worden query's uitgevoerd met statische resources allocatie rc80.

## <a name="recommendations"></a>Aanbevelingen

>[!NOTE]
>Overweeg gebruik te maken van workloadmanagementmogelijkheden[(workload isolation,](sql-data-warehouse-workload-isolation.md) [classificatie](sql-data-warehouse-workload-classification.md) en [belang)](sql-data-warehouse-workload-importance.md)voor meer controle over uw werkbelasting en voorspelbare prestaties.  
>
>

We raden u aan een gebruiker te maken die is toegewijd aan het uitvoeren van een specifiek type query of laadbewerking. Geef die gebruiker een permanente resourceklasse in plaats van de resourceklasse regelmatig te wijzigen. Statische resourceklassen bieden een grotere algemene controle over de werkbelasting, dus we raden u aan statische resourceklassen te gebruiken voordat dynamische resourceklassen worden overwogen.

### <a name="resource-classes-for-load-users"></a>Resourceklassen voor gebruikers van laden

`CREATE TABLE`gebruikt standaard geclusterde kolomarchiefindexen. Het comprimeren van gegevens in een kolomarchiefindex is een geheugenintensieve bewerking en geheugendruk kan de indexkwaliteit verminderen. Geheugendruk kan leiden tot het nodig hebben van een hogere resourceklasse bij het laden van gegevens. Als u ervoor wilt zorgen dat ladingen voldoende geheugen hebben, u een gebruiker maken die is aangewezen voor het uitvoeren van ladingen en die gebruiker aan een hogere resourceklasse toewijzen.

Het geheugen dat nodig is om belastingen efficiënt te verwerken, is afhankelijk van de aard van de geladen tabel en de grootte van de gegevens. Zie De kwaliteit van [de rijgroep maximaliseren](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)voor meer informatie over geheugenvereisten.

Zodra u de geheugenvereiste hebt bepaald, kiest u of u de gebruiker van de belasting wilt toewijzen aan een statische of dynamische resourceklasse.

- Gebruik een statische resourceklasse wanneer de vereisten voor tabelgeheugen binnen een specifiek bereik vallen. Ladingen worden uitgevoerd met het juiste geheugen. Wanneer u het gegevensmagazijn schaalt, hebben de ladingen niet meer geheugen nodig. Door een statische resourceklasse te gebruiken, blijven de geheugentoewijzingen constant. Deze consistentie bespaart het geheugen en zorgt ervoor dat meer query's gelijktijdig kunnen worden uitgevoerd. We raden aan dat nieuwe oplossingen eerst de statische resourceklassen gebruiken, omdat deze meer controle bieden.
- Gebruik een dynamische resourceklasse wanneer de vereisten voor tabelgeheugen sterk variëren. Voor belastingen is mogelijk meer geheugen nodig dan het huidige DWU- of cDWU-niveau biedt. Als u het gegevensmagazijn schaalt, wordt meer geheugen toegevoegd aan laadbewerkingen, waardoor ladingen sneller kunnen worden uitgevoerd.

### <a name="resource-classes-for-queries"></a>Resourceklassen voor query's

Sommige query's zijn rekenintensief en andere niet.  

- Kies een dynamische resourceklasse wanneer query's complex zijn, maar geen hoge gelijktijdigheid nodig hebben.  Het genereren van dagelijkse of wekelijkse rapporten is bijvoorbeeld af en toe een behoefte aan resources. Als de rapporten grote hoeveelheden gegevens verwerken, biedt het schalen van het gegevensmagazijn meer geheugen naar de bestaande resourceklasse van de gebruiker.
- Kies een statische resourceklasse wanneer de resourceverwachtingen gedurende de dag variëren. Een statische resourceklasse werkt bijvoorbeeld goed wanneer het gegevensmagazijn door veel mensen wordt opgevraagd. Bij het schalen van het gegevensmagazijn verandert de hoeveelheid geheugen die aan de gebruiker is toegewezen, niet. Hierdoor kunnen meer query's parallel op het systeem worden uitgevoerd.

De juiste geheugensubsidies zijn afhankelijk van vele factoren, zoals de hoeveelheid opgevraagde gegevens, de aard van de tabelschema's en verschillende joins-, select- en groepspredicaten. In het algemeen, toewijzing van meer geheugen kan query's sneller te voltooien, maar vermindert de totale gelijktijdigheid. Als gelijktijdigheid geen probleem is, schaadt overtoewijzing van geheugen de doorvoer niet.

Als u de prestaties wilt afstemmen, gebruikt u verschillende resourceklassen. De volgende sectie geeft een opgeslagen procedure die u helpt bij het achterhalen van de beste resourceklasse.

## <a name="example-code-for-finding-the-best-resource-class"></a>Voorbeeldcode voor het vinden van de beste resourceklasse

U de volgende opgegeven opgeslagen procedure gebruiken om concurrency en geheugensubsidie per resourceklasse op een bepaalde SLO en de beste resourceklasse voor geheugenintensieve CCI-bewerkingen op niet-gepartitioneerde CCI-tabel in een bepaalde resourceklasse te achterhalen:

Hier is het doel van deze opgeslagen procedure:

1. De gelijktijdigheid en geheugensubsidie per resourceklasse op een bepaalde SLO bekijken. De gebruiker moet NULL opgeven voor zowel schema als tabelnaam zoals in dit voorbeeld wordt weergegeven.  
2. De beste resourceklasse voor de geheugenintensieve CCI-bewerkingen (laden, kopieertabel, herbouwindex, enz.) op niet-gepartitioneerde CCI-tabel in een bepaalde resourceklasse weergeven. De opgeslagen proc maakt gebruik van tabelschema om de vereiste geheugensubsidie te achterhalen.

### <a name="dependencies--restrictions"></a>Afhankelijkheden & beperkingen

- Deze opgeslagen procedure is niet ontworpen om de geheugenvereiste voor een partitie-cci-tabel te berekenen.
- Deze opgeslagen procedure houdt geen rekening met geheugenvereisten voor het SELECT-gedeelte van CTAS/INSERT-SELECT en gaat ervan uit dat het een SELECT is.
- Deze opgeslagen procedure maakt gebruik van een tijdelijke tabel, die beschikbaar is in de sessie waar deze opgeslagen procedure is gemaakt.
- Deze opgeslagen procedure is afhankelijk van het huidige aanbod (bijvoorbeeld hardwareconfiguratie, DMS config), en als een van die wijzigingen verandert, werkt deze opgeslagen proc niet goed.  
- Deze opgeslagen procedure is afhankelijk van bestaande gelijktijdige limiet aanbiedingen en als deze veranderen dan is deze opgeslagen procedure niet correct zal werken.  
- Deze opgeslagen procedure is afhankelijk van bestaande resourceklasseaanbiedingen en als deze worden gewijzigd, werkt deze opgeslagen procedure niet goed.  

>[!NOTE]  
>Als u geen uitvoer krijgt na het uitvoeren van opgeslagen procedure met verstrekte parameters, dan kunnen er twee gevallen zijn.
>
>1. Een DW-parameter bevat een ongeldige SLO-waarde
>2. Er is ook geen overeenkomende resourceklasse voor de CCI-bewerking op de tabel.
>
>Bij DW100c is bijvoorbeeld de hoogste geheugensubsidie beschikbaar 1 GB en als het tabelschema breed genoeg is om de vereiste van 1 GB te overschrijden.

### <a name="usage-example"></a>Voorbeeld van gebruik

Syntaxis:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU:Geef een NULL-parameter op om de huidige DWU uit de DW DB te halen of om ondersteunde DWU's te voorzien in de vorm van 'DW100c'
2. @SCHEMA_NAME:Een schemanaam van de tabel opgeven
3. @TABLE_NAME:Geef een tabelnaam van de interesse op

Voorbeelden van het uitvoeren van deze opgeslagen proc:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Met de volgende instructie wordt tabel1 gemaakt die in de voorgaande voorbeelden wordt gebruikt.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definitie van opgeslagen procedures

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500 
  ELSE Mem*100 
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>Volgende stappen

Zie [Een database beveiligen in SQL Analytics](sql-data-warehouse-overview-manage-security.md)voor meer informatie over het beheren van databasegebruikers en beveiliging. Zie [Geheugenoptimalisaties voor compressie](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)van kolomarchief voor meer informatie over hoe grotere resourceklassen de kwaliteit van de geclusterde kolomarchiefindex kunnen verbeteren.

