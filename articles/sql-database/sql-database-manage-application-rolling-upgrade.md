---
title: Rolling upgrades van toepassingen
description: Meer informatie over het gebruik van geo-replicatie van Azure SQL Database ter ondersteuning van online upgrades van uw cloudtoepassing.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 02/13/2019
ms.openlocfilehash: 9c627c3e597fdcd3859ce02ea208fc7a8b5d612b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822862"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Rolling upgrades van cloudtoepassingen beheren met SQL Database actieve geo-replicatie

Meer informatie over het gebruik van [actieve georeplicatie](sql-database-auto-failover-group.md) in Azure SQL Database om rolling upgrades van uw cloudtoepassing mogelijk te maken. Omdat upgrades disruptieve bewerkingen zijn, moeten ze deel uitmaken van uw bedrijfscontinuïteitsplanning en -ontwerp. In dit artikel kijken we naar twee verschillende methoden voor het orkestreren van het upgradeproces en bespreken we de voordelen en afwegingen van elke optie. Voor de toepassing van dit artikel verwijzen we naar een toepassing die bestaat uit een website die is verbonden met één database als gegevenslaag. Ons doel is om versie 1 (V1) van de applicatie te upgraden naar versie 2 (V2) zonder noemenswaardige invloed op de gebruikerservaring.

Houd bij het evalueren van upgradeopties rekening met deze factoren:

* Impact op de beschikbaarheid van toepassingen tijdens upgrades, zoals hoe lang toepassingsfuncties beperkt of gedegradeerd kunnen zijn.
* Mogelijkheid om terug te draaien als de upgrade mislukt.
* Kwetsbaarheid van de toepassing als er een niet-gerelateerde, catastrofale fout optreedt tijdens de upgrade.
* Totale dollarkosten. Deze factor omvat extra databaseredundantie en incrementele kosten van de tijdelijke componenten die door het upgradeproces worden gebruikt.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Toepassingen upgraden die afhankelijk zijn van databaseback-ups voor herstel na noodgevallen

Als uw toepassing afhankelijk is van automatische databaseback-ups en geoherstel gebruikt voor herstel na noodgevallen, wordt deze geïmplementeerd in één Azure-regio. Als u de onderbreking van de gebruiker wilt minimaliseren, maakt u een faseringsomgeving in dat gebied met alle toepassingsonderdelen die bij de upgrade betrokken zijn. Het eerste diagram illustreert de operationele omgeving vóór het upgradeproces. Het eindpunt `contoso.azurewebsites.net` vertegenwoordigt een productieomgeving van de web-app. Als u de upgrade wilt terugdraaien, moet u een faseringsomgeving maken met een volledig gesynchroniseerde kopie van de database. Volg de volgende stappen om een faseringsomgeving voor de upgrade te maken:

1. Maak een secundaire database in dezelfde Azure-regio. Controleer het secundaire om te zien of het zaaiproces is voltooid (1).
2. Maak een nieuwe omgeving voor uw web-app en noem deze 'Staging'. Het wordt geregistreerd in Azure `contoso-staging.azurewebsites.net` DNS met de URL (2).

> [!NOTE]
> Deze voorbereidingsstappen hebben geen invloed op de productieomgeving, die in de modus volledig toegankelijk kan functioneren.

![SQL Database geo-replicatieconfiguratie voor clouddisaster recovery.](media/sql-database-manage-application-rolling-upgrade/option1-1.png)

Wanneer de voorbereidingsstappen zijn voltooid, is de toepassing klaar voor de daadwerkelijke upgrade. Het volgende diagram illustreert de stappen die betrokken zijn bij het upgradeproces:

1. Stel de primaire database in op alleen-lezen modus (3). Deze modus garandeert dat de productieomgeving van de web-app (V1) alleen-lezen blijft tijdens de upgrade, waardoor gegevensdivergentie tussen de V1- en V2-database-exemplaren wordt voorkomen.
2. Koppel de secundaire database los met de geplande beëindigingsmodus (4). Met deze actie wordt een volledig gesynchroniseerde, onafhankelijke kopie van de primaire database tot stand gekomen. Deze database wordt geüpgraded.
3. Draai de secundaire database om in de leesschrijfmodus en voer het upgradescript uit (5).

![SQL Database geo-replicatieconfiguratie voor clouddisaster recovery.](media/sql-database-manage-application-rolling-upgrade/option1-2.png)

Als de upgrade is voltooid, bent u nu klaar om gebruikers over te schakelen naar de bijgewerkte kopie van de toepassing, die een productieomgeving wordt. Schakelen gaat om nog een paar stappen, zoals geïllustreerd in het volgende diagram:

1. Een swapbewerking activeren tussen productie- en faseringsomgevingen van de web-app (6). Deze bewerking schakelt de URL's van de twee omgevingen. Wijst `contoso.azurewebsites.net` nu naar de V2-versie van de website en de database (productieomgeving). 
2. Als u de V1-versie, die na de swap een staging-kopie werd, niet meer nodig hebt, u de faseringsomgeving buiten bedrijf stellen (7).

![SQL Database geo-replicatieconfiguratie voor clouddisaster recovery.](media/sql-database-manage-application-rolling-upgrade/option1-3.png)

Als het upgradeproces mislukt (bijvoorbeeld als gevolg van een fout in het upgradescript), u de faseringsomgeving als gecompromitteerd beschouwen. Als u de toepassing wilt terugdraaien naar de status van pre-upgrade, draait u de toepassing in de productieomgeving terug naar volledige toegang. In het volgende diagram worden de reversiestappen weergegeven:

1. Stel de databasekopie in op de leesschrijfmodus (8). Met deze actie wordt de volledige V1-functionaliteit van het productieexemplaar hersteld.
2. Voer de analyse van de oorzaak uit en buiten gebruik de staging-omgeving (9).

Op dit moment is de toepassing volledig functioneel en u de upgradestappen herhalen.

> [!NOTE]
> Voor de terugdraaiing zijn geen DNS-wijzigingen vereist omdat u nog geen swapbewerking hebt uitgevoerd.

![SQL Database geo-replicatieconfiguratie voor clouddisaster recovery.](media/sql-database-manage-application-rolling-upgrade/option1-4.png)

Het belangrijkste voordeel van deze optie is dat u een toepassing in één regio upgraden door een reeks eenvoudige stappen te volgen. De dollar kosten van de upgrade is relatief laag. 

De belangrijkste afweging is dat, als er een catastrofale fout optreedt tijdens de upgrade, het herstel naar de pre-upgrade status gaat om het opnieuw implementeren van de toepassing in een andere regio en het herstellen van de database van back-up met behulp van geo-restore. Dit proces resulteert in aanzienlijke downtime.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Toepassingen upgraden die afhankelijk zijn van georeplicatie in de database voor herstel na noodgevallen

Als uw toepassing actieve georeplicatie- of auto-failovergroepen gebruikt voor bedrijfscontinuïteit, wordt deze geïmplementeerd in ten minste twee verschillende regio's. Er is een actieve, primaire database in een primaire regio en een alleen-lezen, secundaire database in een back-upregio. Samen met de factoren die aan het begin van dit artikel worden genoemd, moet het upgradeproces ook garanderen dat:

* De toepassing blijft te allen tijde tijdens het upgradeproces beschermd tegen catastrofale storingen.
* De georedundante componenten van de toepassing worden parallel met de actieve componenten geüpgraded.

Om deze doelen te bereiken, profiteert u niet alleen de Web Apps-omgevingen, maar profiteert u van Azure Traffic Manager door een failoverprofiel te gebruiken met één actief eindpunt en één back-upeindpunt. Het volgende diagram illustreert de operationele omgeving voorafgaand aan het upgradeproces. De websites `contoso-1.azurewebsites.net` `contoso-dr.azurewebsites.net` en vertegenwoordigen een productie-omgeving van de toepassing met volledige geografische redundantie. De productieomgeving omvat de volgende componenten:

* De productieomgeving van `contoso-1.azurewebsites.net` de web-app in de primaire regio (1)
* De primaire database in het primaire gebied (2)
* Een stand-by exemplaar van de web-app in het back-upgebied (3)
* De geo-gerepliceerde secundaire database in het back-upgebied (4)
* Een prestatieprofiel van Traffic Manager `contoso-1.azurewebsites.net` met een online eindpunt dat wordt genoemd en een offline eindpunt genaamd`contoso-dr.azurewebsites.net`

Om de upgrade terug te draaien, moet u een faseringsomgeving maken met een volledig gesynchroniseerde kopie van de toepassing. Omdat u ervoor moet zorgen dat de toepassing snel kan herstellen in het geval er een catastrofale fout optreedt tijdens het upgradeproces, moet de faseringsomgeving ook geo-redundant zijn. De volgende stappen zijn vereist om een faseringsomgeving voor de upgrade te maken:

1. Een faseringsomgeving van de web-app implementeren in de primaire regio (6).
2. Maak een secundaire database in het primaire Azure-gebied (7). Configureer de faseringsomgeving van de web-app om er verbinding mee te maken. 
3. Maak een andere georedundante secundaire database in het back-upgebied door de secundaire database in het primaire gebied te repliceren. (Deze methode wordt *geketende georeplicatie*genoemd .) (8).
4. Implementeer een faseringsomgeving van het web-app-exemplaar in het back-upgebied (9) en configureer deze om de georedundante secundaire database te verbinden die is gemaakt op (8).

> [!NOTE]
> Deze voorbereidingsstappen hebben geen invloed op de toepassing in de productieomgeving. Het blijft volledig functioneel in de lees-write-modus.

![SQL Database geo-replicatieconfiguratie voor clouddisaster recovery.](media/sql-database-manage-application-rolling-upgrade/option2-1.png)

Wanneer de voorbereidingsstappen zijn voltooid, is de faseringsomgeving klaar voor de upgrade. In het volgende diagram worden de volgende upgradestappen geïllustreerd:

1. Stel de primaire database in de productieomgeving in op alleen-lezen modus (10). Deze modus garandeert dat de productiedatabase (V1) niet verandert tijdens de upgrade, waardoor de gegevensdivergentie tussen de V1- en V2-database-exemplaren wordt voorkomen.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Bewerk georeplicatie door de secundaire (11) los te koppelen. Met deze actie wordt een onafhankelijke maar volledig gesynchroniseerde kopie van de productiedatabase gemaakt. Deze database wordt geüpgraded. In het volgende voorbeeld wordt Transact-SQL gebruikt, maar [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary?view=azps-1.5.0) is ook beschikbaar. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Voer het upgradescript uit tegen `contoso-1-staging.azurewebsites.net`, `contoso-dr-staging.azurewebsites.net`en de primaire database met tijdelijke bestanden (12). De databasewijzigingen worden automatisch gerepliceerd naar de secundaire fasering.

![SQL Database geo-replicatieconfiguratie voor clouddisaster recovery.](media/sql-database-manage-application-rolling-upgrade/option2-2.png)

Als de upgrade is voltooid, u nu overschakelen naar de V2-versie van de toepassing. Het volgende diagram illustreert de stappen die betrokken zijn:

1. Een swapbewerking activeren tussen productie- en faseringsomgevingen van de web-app in het primaire gebied (13) en in het back-upgebied (14). V2 van de toepassing wordt nu een productie-omgeving, met een redundante kopie in de back-up regio.
2. Als u de V1-toepassing (15 en 16) niet meer nodig hebt, u de faseringsomgeving buiten gebruik stellen.

![SQL Database geo-replicatieconfiguratie voor clouddisaster recovery.](media/sql-database-manage-application-rolling-upgrade/option2-3.png)

Als het upgradeproces mislukt (bijvoorbeeld als gevolg van een fout in het upgradescript), u de faseringsomgeving in een inconsistente status beschouwen. Als u de toepassing wilt terugdraaien naar de status van pre-upgrade, gaat u terug naar het gebruik van V1 van de toepassing in de productieomgeving. De vereiste stappen worden weergegeven in het volgende diagram:

1. Stel de primaire databasekopie in de productieomgeving in op de leesschrijfmodus (17). Deze actie herstelt de volledige V1-functionaliteit in de productieomgeving.
2. Voer de hoofdoorzaakanalyse uit en repareer of verwijder de faseringsomgeving (18 en 19).

Op dit moment is de toepassing volledig functioneel en u de upgradestappen herhalen.

> [!NOTE]
> Voor de terugdraaiing zijn geen DNS-wijzigingen vereist omdat u geen swapbewerking hebt uitgevoerd.

![SQL Database geo-replicatieconfiguratie voor clouddisaster recovery.](media/sql-database-manage-application-rolling-upgrade/option2-4.png)

Het belangrijkste voordeel van deze optie is dat u zowel de toepassing als de geo-redundante kopie parallel upgraden zonder uw bedrijfscontinuïteit tijdens de upgrade in gevaar te brengen.

De belangrijkste afweging is dat het vereist dubbele redundantie van elke toepassing component en dus hogere dollar kosten. Het gaat ook om een meer gecompliceerde workflow.

## <a name="summary"></a>Samenvatting

De twee upgrademethoden die in het artikel worden beschreven, verschillen in complexiteit en dollarkosten, maar ze richten zich allebei op het minimaliseren van hoe lang de gebruiker beperkt is tot alleen-lezen bewerkingen. Die tijd wordt direct bepaald door de duur van het upgradescript. Het is niet afhankelijk van de databasegrootte, de servicelaag die u hebt gekozen, de websiteconfiguratie of andere factoren die u niet gemakkelijk beheren. Alle voorbereidingsstappen zijn losgekoppeld van de upgradestappen en hebben geen invloed op de productietoepassing. De efficiëntie van het upgradescript is een belangrijke factor die de gebruikerservaring tijdens upgrades bepaalt. Dus, de beste manier om die ervaring te verbeteren is om uw inspanningen te richten op het maken van de upgrade script zo efficiënt mogelijk.

## <a name="next-steps"></a>Volgende stappen

* Zie [Bedrijfscontinuïteitsoverzicht](sql-database-business-continuity.md)voor een overzicht van bedrijfscontinuïteiten.
* Zie [Leesbare secundaire databases maken met actieve georeplicatie](sql-database-active-geo-replication.md)voor meer informatie over actieve georeplicatie in Azure SQL Database.
* Zie [Groepen voor automatische failover gebruiken gebruiken om een transparante en gecoördineerde failover van meerdere databases in te schakelen](sql-database-auto-failover-group.md)voor meer informatie over azure SQL Database-automatische failovergroepen.
* Zie [Tijdelijke omgevingen instellen in Azure App Service](../app-service/deploy-staging-slots.md)voor meer informatie over tijdelijke omgevingen in Azure App Service.
* Zie [Een Azure Traffic Manager-profiel beheren](../traffic-manager/traffic-manager-manage-profiles.md)voor meer informatie over Azure Traffic Manager-profielen.
