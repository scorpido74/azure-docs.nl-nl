---
title: Serverloos
description: In dit artikel wordt de nieuwe serverloze compute-laag beschreven en wordt deze vergeleken met de bestaande ingerichte compute-laag
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 3/11/2020
ms.openlocfilehash: 8a72d3dc7f7b3fddf66df8111b1e92116a62883a
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474020"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL Database serverless

Azure SQL Database serverless is een compute tier voor afzonderlijke databases die automatisch compute schalen op basis van workloadvraag en facturen voor de hoeveelheid compute die per seconde wordt gebruikt. De serverless compute-laag pauzeert ook automatisch databases tijdens inactieve perioden wanneer alleen opslag wordt gefactureerd en hervat automatisch databases wanneer de activiteit terugkeert.

## <a name="serverless-compute-tier"></a>Serverloze compute-laag

De serverloze compute-laag voor één database wordt geparameteriseerd door een compute-automatischscaling-bereik en een vertraging voor automatisch onderbreken.  De configuratie van deze parameters vormt de ervaring met de databaseprestaties en rekenkosten.

![serverloze facturering](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Prestatieconfiguratie

- De **minimale vCores** en **maximale vCores** zijn configureerbare parameters die het bereik van de rekencapaciteit bepalen dat beschikbaar is voor de database. Geheugen- en IO-limieten zijn evenredig aan het opgegeven vCore-bereik.  
- De **vertraging voor automatisch onderbreken** is een configureerbare parameter die de periode definieert die de database moet inactief zijn voordat deze automatisch wordt onderbroken. De database wordt automatisch hervat wanneer de volgende aanmelding of andere activiteit plaatsvindt.  U ook automatisch pauzeren uitschakelen.

### <a name="cost"></a>Kosten

- De kosten voor een serverloze database zijn de optelsom van de rekenkosten en opslagkosten.
- Wanneer het rekengebruik tussen de geconfigureerde min- en max-limieten ligt, zijn de rekenkosten gebaseerd op vCore en het gebruikte geheugen.
- Wanneer het rekengebruik lager is dan de geconfigureerde min-limieten, zijn de rekenkosten gebaseerd op het geconfigureerde min vCores- en min-geheugen.
- Wanneer de database wordt onderbroken, zijn de rekenkosten nul en worden alleen opslagkosten gemaakt.
- De opslagkosten worden op dezelfde manier bepaald als in de ingerichte compute-laag.

Zie [Facturering](sql-database-serverless.md#billing)voor meer kostengegevens.

## <a name="scenarios"></a>Scenario's

Serverless is prijs-prestatiegericht geoptimaliseerd voor afzonderlijke databases met intermitterende, onvoorspelbare gebruikspatronen die zich enige vertraging kunnen veroorloven bij het berekenen van opwarmperioden na idle gebruiksperioden. De ingerichte compute-laag is daarentegen prijsprestaties geoptimaliseerd voor afzonderlijke databases of meerdere databases in elastische pools met een hoger gemiddeld gebruik die zich geen vertraging kunnen veroorloven bij de opwarmfase.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenario's die geschikt zijn voor serverless compute

- Enkele databases met intermitterende, onvoorspelbare gebruikspatronen afgewisseld met perioden van inactiviteit en een lager gemiddeld rekengebruik in de loop van de tijd.
- Afzonderlijke databases in de ingerichte compute-laag die vaak opnieuw worden geschaald en klanten die de voorkeur geven aan het delegeren van compute rescaling aan de service.
- Nieuwe afzonderlijke databases zonder gebruiksgeschiedenis waarbij compute sizing moeilijk of niet mogelijk is om te schatten voorafgaand aan implementatie in SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenario's die geschikt zijn voor ingerichte compute

- Afzonderlijke databases met meer regelmatige, voorspelbare gebruikspatronen en een hoger gemiddeld rekengebruik in de loop van de tijd.
- Databases die geen prestatietrade-offs kunnen verdragen als gevolg van het vaker bijsnijden van het geheugen of vertraging bij het automatisch opnieuw opbouwen van een onderbroken status.
- Meerdere databases met intermitterende, onvoorspelbare gebruikspatronen die kunnen worden geconsolideerd in elastische pools voor een betere optimalisatie van de prijs-prestatieprestaties.

## <a name="comparison-with-provisioned-compute-tier"></a>Vergelijking met de ingerichte compute-laag

In de volgende tabel wordt het onderscheid tussen de serverless compute-laag en de ingerichte compute-laag samengevat:

| | **Serverloze compute** | **Ingerichte rekenkracht** |
|:---|:---|:---|
|**Databasegebruikspatroon**| Intermitterend, onvoorspelbaar gebruik met een lager gemiddeld rekengebruik in de loop van de tijd. |  Meer regelmatige gebruikspatronen met een hoger gemiddeld rekengebruik in de loop van de tijd of meerdere databases met elastische pools.|
| **Prestatiebeheer inspanning** |Lower|Hoger|
|**Schalen berekenen**|Automatisch|Handmatig|
|**Reactievermogen berekenen**|Lager na inactieve perioden|Onmiddellijke|
|**Granulariteit van facturering**|Per seconde|Per uur|

## <a name="purchasing-model-and-service-tier"></a>Inkoopmodel en servicelaag

SQL Database serverless wordt momenteel alleen ondersteund in de categorie Algemeen Doel op Generatie 5-hardware in het vCore-inkoopmodel.

## <a name="autoscaling"></a>Automatisch schalen

### <a name="scaling-responsiveness"></a>Responsiviteit schalen

In het algemeen worden serverloze databases uitgevoerd op een machine met voldoende capaciteit om te voldoen aan de vraag naar resources zonder onderbreking voor elke hoeveelheid compute die wordt gevraagd binnen de grenzen die zijn ingesteld door de maximale vCores-waarde. Af en toe vindt het taakverdelingautomatisch plaats als de machine niet binnen een paar minuten aan de vraag naar resources kan voldoen. Als de resourcevraag bijvoorbeeld 4 vCores is, maar er slechts 2 vCores beschikbaar zijn, kan het tot een paar minuten duren voordat de balans wordt geladen voordat 4 vCores worden geleverd. De database blijft online tijdens het balanceren van de belasting, behalve voor een korte periode aan het einde van de bewerking wanneer verbindingen worden verwijderd.

### <a name="memory-management"></a>Geheugenbeheer

Geheugen voor serverloze databases wordt vaker teruggewonnen dan voor ingerichte compute databases. Dit gedrag is belangrijk om de kosten in serverless te beheersen en kan de prestaties beïnvloeden.

#### <a name="cache-reclamation"></a>Cacheterugwinning

In tegenstelling tot ingerichte compute databases wordt het geheugen uit de SQL-cache teruggewonnen uit een serverloze database wanneer het cpu- of cachegebruik laag is.

- Het gebruik van de cache wordt als laag beschouwd wanneer de totale grootte van de meest recent gebruikte cachevermeldingen gedurende een bepaalde periode onder een drempelwaarde valt.
- Wanneer cacheterugwinning wordt geactiveerd, wordt de grootte van de doelcache stapsgewijs teruggebracht tot een fractie van de vorige grootte en wordt het terugwinnen alleen voortgezet als het gebruik laag blijft.
- Wanneer cacheterugwinning plaatsvindt, is het beleid voor het selecteren van cache-items die moeten worden verwijderd hetzelfde selectiebeleid als voor ingerichte compute databases wanneer de geheugendruk hoog is.
- De cachegrootte wordt nooit verlaagd onder de min-geheugenlimiet zoals gedefinieerd door min vCores die kunnen worden geconfigureerd.

In zowel serverloze als ingerichte compute databases kunnen cache-items worden verwijderd als al het beschikbare geheugen wordt gebruikt.

#### <a name="cache-hydration"></a>Hydratatie in de cache

De SQL-cache groeit naarmate gegevens op dezelfde manier en met dezelfde snelheid van de schijf worden opgehaald als voor ingerichte databases. Wanneer de database bezet is, mag de cache ongebonden groeien tot de maximale geheugenlimiet.

## <a name="autopausing-and-autoresuming"></a>Automatisch onderbreken en autoreoptellen

### <a name="autopausing"></a>Automatisch pauzeren

Automatisch onderbreken wordt geactiveerd als alle volgende voorwaarden waar zijn voor de duur van de autopauzevertraging:

- Aantalsessies = 0
- CPU = 0 voor gebruikersworkload die wordt uitgevoerd in de gebruikersgroep

Er is een optie om automatisch pauzeren uit te schakelen indien gewenst.

De volgende functies ondersteunen automatisch pauzeren niet.  Dat wil zeggen, als een van de volgende functies worden gebruikt, dan blijft de database online, ongeacht de duur van database inactiviteit:

- Geo-replicatie (actieve georeplicatie- en auto-failovergroepen).
- Lange termijn back-upretentie (LTR).
- De synchronisatiedatabase die wordt gebruikt in SQL-gegevenssynchronisatie.  In tegenstelling tot synchronisatiedatabases ondersteunen hub- en liddatabases automatisch pauzeren.
- De taakdatabase die wordt gebruikt in elastische taken.

Automatisch onderbreken wordt tijdelijk voorkomen tijdens de implementatie van bepaalde service-updates waarvoor de database online moet zijn.  In dergelijke gevallen wordt automatisch onderbreken opnieuw toegestaan zodra de service-update is voltooid.

### <a name="autoresuming"></a>Autoresuming

Autoresuming wordt geactiveerd als een van de volgende voorwaarden op enig moment waar is:

|Functie|Trigger automatisch hervatten|
|---|---|
|Verificatie en autorisatie|Aanmelden|
|Detectie van bedreigingen|Instellingen voor bedreigingsdetectie in- of uitschakelen op database- of serverniveau.<br>Instellingen voor bedreigingsdetectie wijzigen op database- of serverniveau.|
|Gegevensdetectie en -classificatie|Labels voor het toevoegen, wijzigen, verwijderen of weergeven van gevoeligheidslabels|
|Controleren|Controlerecords weergeven.<br>Controlebeleid bijwerken of weergeven.|
|Gegevensmaskering|Regels voor het maskeren van gegevens toevoegen, wijzigen, verwijderen of weergeven|
|Transparent Data Encryption|Status of status van transparante gegevensversleuteling weergeven|
|Querygegevensarchief (prestatie)|Instellingen voor queryopslag wijzigen of weergeven|
|Autotuning|Toepassing en verificatie van aanbevelingen voor autotuning, zoals auto-indexering|
|Database kopiëren|Maak database als kopie.<br>Exporteren naar een BACPAC-bestand.|
|SQL-gegevenssynchronisatie|Synchronisatie tussen hub- en liddatabases die volgens een configureerbaar schema worden uitgevoerd of handmatig worden uitgevoerd|
|Bepaalde databasemetagegevens wijzigen|Nieuwe databasetags toevoegen.<br>Het wijzigen van max vCores, min vCores of autopause vertraging.|
|SQL Server Management Studio (SSMS)|Als u SSMS-versies eerder dan 18.1 gebruikt en een nieuw queryvenster opent voor een database in de server, wordt elke automatisch onderbroken database in dezelfde server hervat. Dit gedrag treedt niet op als u SSMS-versie 18.1 of hoger gebruikt.|

Autoresuming wordt ook geactiveerd tijdens de implementatie van een aantal service-updates waarvoor de database online moet zijn.

### <a name="connectivity"></a>Connectiviteit

Als een serverloze database wordt onderbroken, wordt de database hervat en wordt een fout weergegeven waarin staat dat de database niet beschikbaar is met foutcode 40613. Zodra de database is hervat, moet de login opnieuw worden geprobeerd om de verbinding vast te stellen. Databaseclients met de logica voor het opnieuw proberen van verbindingen hoeven niet te worden gewijzigd.

### <a name="latency"></a>Latentie

De latentie om een serverloze database automatisch te hervatten en automatisch te pauzeren, is over het algemeen een volgorde van 1 minuut om automatisch te hervatten en 1-10 minuten om automatisch te pauzeren.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Door de klant beheerde transparante gegevensversleuteling (BYOK)

Als de [door de klant beheerde transparante gegevensversleuteling](transparent-data-encryption-byok-azure-sql.md) (BYOK) wordt gebruikt en de serverloze database automatisch wordt onderbroken wanneer de sleutel wordt verwijderd of opnieuw wordt ingetrokken, blijft de database in de automatisch onderbroken status.  In dit geval, wanneer de database volgende wordt hervat, blijft de database online totdat de status na ongeveer 10 minuten of minder ontoegankelijk is.  Zodra de database ontoegankelijk wordt, is het herstelproces hetzelfde als voor ingerichte compute databases.  Als de serverloze database online is wanneer sleutelverwijdering of intrekking plaatsvindt, wordt de database ook ontoegankelijk na ongeveer 10 minuten of minder op dezelfde manier als bij ingerichte compute databases.

## <a name="onboarding-into-serverless-compute-tier"></a>Onboarding in serverless compute-laag

Het maken van een nieuwe database of het verplaatsen van een bestaande database naar een serverloze compute-laag volgt hetzelfde patroon als het maken van een nieuwe database in de ingerichte compute-laag en omvat de volgende twee stappen.

1. Geef de naam van de servicedoelstelling op. De servicedoelstelling schrijft de servicelaag, de hardwaregeneratie en de max vCores voor. In de volgende tabel worden de opties voor servicedoelstelling weergegeven:

   |Naam servicedoelstelling|Servicelaag|Hardwaregeneratie|Max.|
   |---|---|---|---|
   |GP_S_Gen5_1|Algemeen gebruik|Gen5 Gen5|1|
   |GP_S_Gen5_2|Algemeen gebruik|Gen5 Gen5|2|
   |GP_S_Gen5_4|Algemeen gebruik|Gen5 Gen5|4|
   |GP_S_Gen5_6|Algemeen gebruik|Gen5 Gen5|6|
   |GP_S_Gen5_8|Algemeen gebruik|Gen5 Gen5|8|
   |GP_S_Gen5_10|Algemeen gebruik|Gen5 Gen5|10|
   |GP_S_Gen5_12|Algemeen gebruik|Gen5 Gen5|12|
   |GP_S_Gen5_14|Algemeen gebruik|Gen5 Gen5|14|
   |GP_S_Gen5_16|Algemeen gebruik|Gen5 Gen5|16|

2. Geef eventueel de min vCores en de vertraging automatisch onderbreken op om hun standaardwaarden te wijzigen. In de volgende tabel worden de beschikbare waarden voor deze parameters weergegeven.

   |Parameter|Waardekeuzes|Standaardwaarde|
   |---|---|---|---|
   |Min vCores|Afhankelijk van max vCores geconfigureerd - zie [resource limieten](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).|0,5 vCores|
   |Vertraging automatisch onderbreken|Minimum: 60 minuten (1 uur)<br>Maximum: 10080 minuten (7 dagen)<br>Stappen: 60 minuten<br>Automatisch pauzeren uitschakelen: -1|60 minuten|


### <a name="create-new-database-in-serverless-compute-tier"></a>Nieuwe database maken in de serverless compute-laag 

In de volgende voorbeelden wordt een nieuwe database gemaakt in de serverloze compute-laag. De voorbeelden geven expliciet de min vCores, max vCores en autopause vertraging.

#### <a name="use-azure-portal"></a>Azure Portal gebruiken

Zie [Snelstart: maak één database in Azure SQL Database met de Azure-portal.](sql-database-single-database-get-started.md)


#### <a name="use-powershell"></a>PowerShell gebruiken

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>Azure CLI gebruiken

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Transact-SQL (T-SQL) gebruiken

In het volgende voorbeeld wordt een nieuwe database gemaakt in de serverloze compute-laag.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Zie DATABASE [MAKEN](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)voor meer informatie .  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Database verplaatsen van ingerichte compute-laag naar serverloze compute-laag

In de volgende voorbeelden wordt een database van de ingerichte compute-laag verplaatst naar de serverloze compute-laag. De voorbeelden geven expliciet de min vCores, max vCores en autopause vertraging.

#### <a name="use-powershell"></a>PowerShell gebruiken


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>Azure CLI gebruiken

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Transact-SQL (T-SQL) gebruiken

In het volgende voorbeeld wordt een database van de ingerichte compute-laag verplaatst naar de serverloze compute-laag.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Zie ALTER [DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)voor meer informatie.

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Database verplaatsen van serverloze compute-laag naar ingerichte compute-laag

Een serverloze database kan op dezelfde manier worden verplaatst naar een ingerichte compute-laag als het verplaatsen van een ingerichte compute database naar een serverloze compute-laag.

## <a name="modifying-serverless-configuration"></a>Serverloze configuratie wijzigen

### <a name="use-powershell"></a>PowerShell gebruiken

Het wijzigen van de maximale of minimale vCores en de vertraging automatisch onderbreken, wordt `MaxVcore` `MinVcore`uitgevoerd `AutoPauseDelayInMinutes` met de opdracht [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) in PowerShell met behulp van de , en argumenten.

### <a name="use-azure-cli"></a>Azure CLI gebruiken

Het wijzigen van de maximale of minimale vCores en de vertraging automatisch onderbreken, `capacity`wordt `min-capacity`uitgevoerd `auto-pause-delay` met de opdracht AZ [SQL DB Update](/cli/azure/sql/db#az-sql-db-update) in Azure CLI met behulp van de , en argumenten.


## <a name="monitoring"></a>Bewaking

### <a name="resources-used-and-billed"></a>Gebruikte en gefactureerde resources

De bronnen van een serverloze database zijn ingekapseld door app-pakket, SQL-instantie en entiteiten voor gebruikersbronnen.

#### <a name="app-package"></a>App-pakket

Het app-pakket is de buitenste grens voor resourcebeheer voor een database, ongeacht of de database zich in een serverloze of ingerichte compute-laag bevindt. Het app-pakket bevat de SQL-instantie en externe services die alle gebruikers- en systeembronnen die door een database in SQL Database worden gebruikt, samen orde bieden. Voorbeelden van externe diensten zijn R en full-text search. De SQL-instantie domineert over het algemeen het algemene resourcegebruik in het app-pakket.

#### <a name="user-resource-pool"></a>Gebruikersbrongroep

De gebruikersbrongroep is de meest interne bronbeheergrens voor een database, ongeacht of de database zich in een serverloze of ingerichte compute-laag bevindt. De gebruikersbrongroep bevat CPU- en IO-scopes voor gebruikersworkloads die worden gegenereerd door DDL-query's, zoals CREATE- en ALTER- en DML-query's zoals SELECT, INSERT, UPDATE en Delete. Deze query's vertegenwoordigen over het algemeen het grootste deel van het gebruik binnen het app-pakket.

### <a name="metrics"></a>Metrische gegevens

Statistieken voor het bewaken van het resourcegebruik van het app-pakket en de gebruikersgroep van een serverloze database worden weergegeven in de volgende tabel:

|Entiteit|Gegevens|Beschrijving|Eenheden|
|---|---|---|---|
|App-pakket|app_cpu_percent|Percentage vCores dat door de app wordt gebruikt ten opzichte van max vCores toegestaan voor de app.|Percentage|
|App-pakket|app_cpu_billed|De hoeveelheid rekengegevens die tijdens de rapportageperiode voor de app is gefactureerd. Het bedrag dat tijdens deze periode wordt betaald, is het product van deze statistiek en de vCore-eenheidsprijs. <br><br>Waarden van deze statistiek worden bepaald door na verloop van tijd het maximum aan gebruikte CPU en geheugen per seconde te aggregeren. Als het gebruikte bedrag lager is dan het minimumbedrag dat is ingesteld door het min vCores- en min-geheugen, wordt het minimale ingerichte bedrag gefactureerd.Om CPU te vergelijken met geheugen voor factureringsdoeleinden, wordt het geheugen genormaliseerd in eenheden van vCores door de hoeveelheid geheugen in GB opnieuw op te schalen met 3 GB per vCore.|vCore-seconden|
|App-pakket|app_memory_percent|Percentage geheugen dat door de app wordt gebruikt ten opzichte van het maximaal geheugen dat is toegestaan voor de app.|Percentage|
|Gebruikersgroep|cpu_percent|Percentage vCores dat wordt gebruikt door de gebruikersworkload ten opzichte van max vCores toegestaan voor gebruikersworkload.|Percentage|
|Gebruikersgroep|data_IO_percent|Percentage gegevens IOPS gebruikt door de gebruikersworkload ten opzichte van max data IOPS toegestaan voor de gebruiker workload.|Percentage|
|Gebruikersgroep|log_IO_percent|Percentage log-MB/s dat wordt gebruikt door de werkbelasting van de gebruiker ten opzichte van max log MB/s toegestaan voor gebruikersworkload.|Percentage|
|Gebruikersgroep|workers_percent|Percentage werknemers dat wordt gebruikt door de werkbelasting van de gebruiker ten opzichte van max-werknemers die zijn toegestaan voor de werkbelasting van de gebruiker.|Percentage|
|Gebruikersgroep|sessions_percent|Percentage sessies dat wordt gebruikt door de werkbelasting van de gebruiker ten opzichte van max-sessies die zijn toegestaan voor de werkbelasting van de gebruiker.|Percentage|

### <a name="pause-and-resume-status"></a>Status onderbreken en hervatten

In de Azure-portal wordt de databasestatus weergegeven in het overzichtsvenster van de server met de databases die deze bevat. De databasestatus wordt ook weergegeven in het overzichtsvenster voor de database.

Met behulp van de volgende opdrachten om de pauze- en hervattingsstatus van een database op te vragen:

#### <a name="use-powershell"></a>PowerShell gebruiken

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-azure-cli"></a>Azure CLI gebruiken

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Bronlimieten

Zie [serverless compute-laag](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)voor resourcelimieten.

## <a name="billing"></a>Billing

De hoeveelheid compute gefactureerd is het maximum van cpu gebruikt en geheugen gebruikt elke seconde. Als de gebruikte hoeveelheid CPU en het gebruikte geheugen lager is dan het minimumbedrag dat voor elk wordt ingericht, wordt het ingerichte bedrag gefactureerd. Om CPU te vergelijken met geheugen voor factureringsdoeleinden, wordt het geheugen genormaliseerd in eenheden van vCores door de hoeveelheid geheugen in GB opnieuw op te schalen met 3 GB per vCore.

- **Bron gefactureerd:** CPU en geheugen
- **Gefactureerd bedrag**: vCore eenheidsprijs * max (min vCores, vCores gebruikt, min geheugen GB * 1/3, geheugen GB gebruikt * 1/3) 
- **Factureringsfrequentie**: Per seconde

De vCore eenheidsprijs is de kosten per vCore per seconde. Raadpleeg de [prijspagina azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) voor specifieke eenheidsprijzen in een bepaalde regio.

De hoeveelheid compute billed wordt weergegeven door de volgende statistiek:

- **Metrische**: app_cpu_billed (vCore seconden)
- **Definitie**: max (min vCores, vCores gebruikt, min geheugen GB * 1/3, geheugen GB gebruikt * 1/3)
- **Rapportagefrequentie**: Per minuut

Deze hoeveelheid wordt elke seconde berekend en verdeeld over 1 minuut.

Overweeg een serverloze database geconfigureerd met 1 min vCore en 4 max vCores.  Dit komt overeen met ongeveer 3 GB geheugen en 12 GB max geheugen.  Stel dat de vertraging voor automatisch onderbreken is ingesteld op 6 uur en de databasewerkbelasting actief is gedurende de eerste 2 uur van een periode van 24 uur en anderszins inactief.    

In dit geval wordt de database gefactureerd voor rekenkracht en opslag gedurende de eerste 8 uur.  Hoewel de database inactief is vanaf het tweede uur, wordt deze nog steeds gefactureerd voor compute in de volgende 6 uur op basis van de minimale compute-inrichting die is ingericht terwijl de database online is.  Alleen opslag wordt gefactureerd gedurende de rest van de periode van 24 uur terwijl de database wordt onderbroken.

Meer precies, de compute bill in dit voorbeeld wordt als volgt berekend:

|Tijdsinterval|vCores gebruikt elke seconde|GB gebruikt elke seconde|Rekendimensie gefactureerd|vCore-seconden gefactureerd na tijdsinterval|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores gebruikt|4 vCores * 3600 seconden = 14400 vCore seconden|
|1:00-2:00|1|12|Geheugen gebruikt|12 GB * 1/3 * 3600 seconden = 14400 vCore seconden|
|2:00-8:00|0|0|Min geheugen ingericht|3 GB * 1/3 * 21600 seconden = 21600 vCore seconden|
|8:00-24:00|0|0|Geen compute gefactureerd tijdens het pauzeren|0 vCore-seconden|
|Totaal aantal vCore-seconden dat meer dan 24 uur is gefactureerd||||50400 vCore-seconden|

Stel dat de rekeneenheidsprijs $ 0,000145/vCore/seconde is.  Dan is de compute gefactureerd voor deze periode van 24 uur is het product van de compute unit prijs en vCore seconden gefactureerd: $0.000145/vCore/second * 50400 vCore seconden ~ $7.31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure Hybrid Benefit en gereserveerde capaciteit

Azure Hybrid Benefit (AHB) en gereserveerde capaciteitskortingen zijn niet van toepassing op de serverless compute-laag.

## <a name="available-regions"></a>Beschikbare regio's

De serverless compute-laag is wereldwijd beschikbaar, behalve de volgende regio's: China East, China North, Germany Central, Germany Northeast, UK North, UK South 2, West Central US en US Gov Central (Iowa).

## <a name="next-steps"></a>Volgende stappen

- Zie [Snelstart: Eén database maken in Azure SQL Database met de Azure-portal](sql-database-single-database-get-started.md)om aan de slag te gaan.
- Zie [Serverless compute tier resource limits](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)voor resourcelimieten voor resources.
