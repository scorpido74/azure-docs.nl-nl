---
title: Automatisch instellen inschakelen
description: U kunt het automatisch afstemmen op uw Azure SQL Database eenvoudig inschakelen.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 0abf4bb015be52a10178423a566433b87127a167
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821915"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Automatisch afstemmen inschakelen om query's te bewaken en de prestaties van de werk belasting te verbeteren

Azure SQL Database is een automatisch beheerde gegevens service waarmee uw query's voortdurend worden gecontroleerd en de actie wordt geïdentificeerd die u kunt uitvoeren om de prestaties van uw workload te verbeteren. U kunt aanbevelingen bekijken en deze hand matig Toep assen of Azure SQL Database corrigerende acties automatisch Toep assen. dit wordt ook wel de **automatische afstemmings modus**genoemd.

Automatisch afstemmen kan worden ingeschakeld op de server of het database niveau via de [Azure Portal](sql-database-automatic-tuning-enable.md#azure-portal), [rest API](sql-database-automatic-tuning-enable.md#rest-api) aanroepen en [T-SQL-](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current) opdrachten.

> [!NOTE]
> Voor een beheerd exemplaar kan de ondersteunde optie FORCE_LAST_GOOD_PLAN alleen worden geconfigureerd via [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) . De opties op basis van Portal configuratie en automatische index afstemming die in dit artikel worden beschreven, zijn niet van toepassing op een beheerd exemplaar.

> [!NOTE]
> Het configureren van opties voor automatisch afstemmen via ARM (Azure Resource Manager) wordt op dit moment niet ondersteund.

## <a name="enable-automatic-tuning-on-server"></a>Automatisch afstemmen op server inschakelen

Op server niveau kunt u kiezen voor het overnemen van de automatische afstemmings configuratie van ' Azure defaults ' of de configuratie niet overnemen. De standaard waarden van Azure zijn FORCE_LAST_GOOD_PLAN ingeschakeld, CREATE_INDEX is ingeschakeld en DROP_INDEX is uitgeschakeld.

### <a name="azure-portal"></a>Azure Portal

Als u automatisch afstemmen op Azure SQL Database logische **Server**wilt inschakelen, navigeert u naar de-server in azure Portal en selecteert u vervolgens **automatisch afstemmen** in het menu.

![Server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Houd er rekening mee dat **DROP_INDEX** optie op dit moment niet compatibel is met toepassingen die gebruikmaken van partitie-switches en index hints en niet in deze gevallen mogen worden ingeschakeld. Het verwijderen van niet-gebruikte indexen wordt niet ondersteund voor Premium-en Bedrijfskritiek-service lagen.
>

Selecteer de opties voor automatisch afstemmen die u wilt inschakelen en selecteer **Toep assen**.

Opties voor automatisch afstemmen op een server worden toegepast op alle data bases op deze server. Standaard nemen alle data bases de configuratie over van de bovenliggende server, maar dit kan worden overschreven en voor elke Data Base afzonderlijk worden opgegeven.

### <a name="rest-api"></a>REST-API

Meer informatie over het gebruik van REST API om automatisch afstemmen op een server in te scha kelen, raadpleegt u [SQL Server Automatic tuning update en HTTP-methoden ophalen](https://docs.microsoft.com/rest/api/sql/serverautomatictuning).

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Automatisch afstemmen inschakelen voor een afzonderlijke data base

Met de Azure SQL Database kunt u de automatische afstemmings configuratie voor elke Data Base afzonderlijk opgeven. Op het niveau van de Data Base kunt u ervoor kiezen om de automatische afstemmings configuratie over te nemen van de bovenliggende server, "Azure defaults" of de configuratie niet overnemen. De standaard waarden van Azure worden ingesteld op FORCE_LAST_GOOD_PLAN is ingeschakeld, CREATE_INDEX is ingeschakeld en DROP_INDEX is uitgeschakeld.

> [!TIP]
> De algemene aanbeveling is de configuratie voor automatisch afstemmen op **server niveau** te beheren, zodat dezelfde configuratie-instellingen automatisch kunnen worden toegepast op elke Data Base. Configureer automatisch afstemmen voor een afzonderlijke Data Base alleen als u die data base andere instellingen moet hebben dan andere de instellingen overnemen van dezelfde server.
>

### <a name="azure-portal"></a>Azure Portal

Als u automatisch afstemmen wilt inschakelen voor **één data base**, gaat u naar de data base in azure Portal en selecteert u **automatisch afstemmen**.

Individuele instellingen voor automatisch afstemmen kunnen afzonderlijk worden geconfigureerd voor elke Data Base. U kunt hand matig een afzonderlijke optie voor automatisch afstemmen configureren of opgeven dat een optie de instellingen van de server overneemt.

![Database](./media/sql-database-automatic-tuning-enable/database.png)

Houd er rekening mee dat DROP_INDEX optie op dit moment niet compatibel is met toepassingen die gebruikmaken van partitie-switches en index hints en niet in deze gevallen mogen worden ingeschakeld.

Wanneer u de gewenste configuratie hebt geselecteerd, klikt u op **Toep assen**.

### <a name="rest-api"></a>Rest API

Meer informatie over het gebruik van REST API om automatisch afstemmen in te scha kelen voor één data base, vindt u [SQL database automatisch afstemmen bijwerken en HTTP-methoden ophalen](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning).

### <a name="t-sql"></a>T-SQL

Als u automatisch afstemmen wilt inschakelen voor één data base via T-SQL, maakt u verbinding met de data base en voert u de volgende query uit:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Als u automatisch afstemmen instelt op automatisch, worden de standaard instellingen van Azure toegepast. Als u deze instelt op INHERIT, wordt de configuratie voor automatisch afstemmen overgenomen van de bovenliggende server. Als u Aangepast kiest, moet u automatisch afstemmen hand matig configureren.

Als u afzonderlijke opties voor automatisch afstemmen wilt configureren via T-SQL, maakt u verbinding met de data base en voert u de query uit, zoals deze:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Als u de individuele afstemmings optie instelt op aan, wordt elke instelling die door de data base is overgenomen en de afstemmings optie ingeschakeld. Als u deze instelling uitschakelt, wordt ook alle instellingen die door de Data Base zijn overgenomen, overschreven en wordt de afstemmings optie uitgeschakeld. Voor de optie automatisch afstemmen, waarvoor standaard is opgegeven, wordt de configuratie overgenomen van de instelling voor automatisch afstemmen op database niveau.  

> [!IMPORTANT]
> In het geval van [actieve geo-replicatie](sql-database-auto-failover-group.md)moet automatisch afstemmen worden geconfigureerd op de primaire data base. Automatisch toegepaste afstemmings acties, zoals het maken of verwijderen van een index, worden automatisch gerepliceerd naar het secundaire kenmerk alleen-lezen. Als u probeert automatische afstemming via T-SQL in te scha kelen op het alleen-lezen secundair, resulteert dit in een fout omdat een andere afstemmings configuratie op het secundaire kenmerk alleen-lezen niet wordt ondersteund.
>

Meer over T-SQL-opties voor het configureren van automatisch afstemmen vindt u in [ALTER data base set Options (Transact-SQL) voor SQL database server](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Uitgeschakeld door het systeem

Automatisch afstemmen bewaken alle acties die ze in de Data Base uitvoeren. in sommige gevallen kan het bepalen dat automatisch afstemmen niet goed kan worden uitgevoerd op de data base. In deze situatie wordt de afstemmings optie uitgeschakeld door het systeem. In de meeste gevallen gebeurt dit omdat query Store niet is ingeschakeld of de status alleen-lezen heeft voor een specifieke data base.

## <a name="permissions"></a>Machtigingen

Als automatische afstemming is Azure-functie, moet u de ingebouwde RBAC-rollen van Azure gebruiken om het te gebruiken. Het gebruik van alleen SQL-verificatie is niet voldoende om de functie uit Azure Portal te gebruiken.

Als u automatisch afstemmen wilt gebruiken, is de mini maal vereiste machtiging voor het verlenen aan de gebruiker de ingebouwde rol van de [SQL DB-Inzender](../role-based-access-control/built-in-roles.md#sql-db-contributor) voor Azure. U kunt ook overwegen om hogere bevoegdheden te gebruiken, zoals SQL Server Inzender, Inzender en eigenaar.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>E-mail meldingen automatisch afstemmen configureren

Zie de hand leiding voor het [automatisch afstemmen van berichten](sql-database-automatic-tuning-email-notifications.md) .

## <a name="next-steps"></a>Volgende stappen

* Lees het [artikel automatisch afstemmen](sql-database-automatic-tuning.md) voor meer informatie over automatisch afstemmen en hoe u dit kunt helpen bij het verbeteren van de prestaties.
* Bekijk de [aanbevelingen voor prestaties](sql-database-advisor.md) voor een overzicht van Azure SQL database prestatie aanbevelingen.
* Zie [query performance Insights](sql-database-query-performance.md) voor meer informatie over het weer geven van de prestatie-impact van uw meest voorkomende query's.
