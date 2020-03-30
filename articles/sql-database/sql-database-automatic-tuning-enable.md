---
title: Automatisch instellen inschakelen
description: U automatisch afstemmen op uw Azure SQL Database eenvoudig inschakelen.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/03/2019
ms.openlocfilehash: eed839c277156046ff9b7d97c6e87636a0822889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299325"
---
# <a name="enable-automatic-tuning-to-monitor-queries-and-improve-workload-performance"></a>Automatische afstemming inschakelen om query's te controleren en de prestaties van de werkbelasting te verbeteren

Azure SQL Database is een automatisch beheerde gegevensservice die uw query's voortdurend controleert en de actie identificeert die u uitvoeren om de prestaties van uw werkbelasting te verbeteren. U aanbevelingen controleren en handmatig toepassen, of Azure SQL Database automatisch corrigerende acties laten toepassen - dit staat bekend als **automatische tuningmodus**.

Automatische afstemming kan worden ingeschakeld op server- of databaseniveau via de [Azure-portal,](sql-database-automatic-tuning-enable.md#azure-portal) [REST API-aanroepen](sql-database-automatic-tuning-enable.md#rest-api) en [T-SQL-opdrachten.](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current)

> [!NOTE]
> Voor Beheerde instantie kan de ondersteunde optie FORCE_LAST_GOOD_PLAN alleen via [T-SQL](https://azure.microsoft.com/blog/automatic-tuning-introduces-automatic-plan-correction-and-t-sql-management) worden geconfigureerd. Portal gebaseerde configuratie en automatische index tuning opties beschreven in dit artikel niet van toepassing op Managed Instance.

> [!NOTE]
> Het configureren van automatische tuningopties via de sjabloon ARM (Azure Resource Manager) wordt op dit moment niet ondersteund.

## <a name="enable-automatic-tuning-on-server"></a>Automatische afstemming op de server inschakelen

Op serverniveau u ervoor kiezen om automatische tuningconfiguratie over te nemen van 'Azure Defaults' of de configuratie niet over te nemen. Azure-standaardinstellingen zijn FORCE_LAST_GOOD_PLAN is ingeschakeld, CREATE_INDEX is ingeschakeld en DROP_INDEX is uitgeschakeld.

> [!IMPORTANT]
> Vanaf maart worden wijzigingen in Azure-standaardinstellingen voor automatische afstemming als volgt van kracht:
>
> - Nieuwe Azure-standaardinstellingen worden FORCE_LAST_GOOD_PLAN = ingeschakeld, CREATE_INDEX = uitgeschakeld en DROP_INDEX = uitgeschakeld.
> - Bestaande servers zonder geconfigureerde automatische stemvoorkeuren worden automatisch geconfigureerd om de nieuwe Azure-standaardinstellingen te erven. Dit geldt voor alle klanten die momenteel serverinstellingen hebben voor automatische afstemming in een ongedefinieerde status.
> - Nieuwe servers die zijn gemaakt, worden automatisch geconfigureerd om de nieuwe Azure-standaardinstellingen te erven (in tegenstelling tot eerder toen de automatische tuningconfiguratie in een niet-gedefinieerde status was bij het maken van nieuwe servers).

### <a name="azure-portal"></a>Azure Portal

Als u automatische afstemming op de logische **server**van Azure SQL Database wilt inschakelen, navigeert u naar de server in azure-portal en selecteert u **Automatisch afstemmen** in het menu.

![server](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Houd er rekening mee dat **DROP_INDEX** optie op dit moment niet compatibel is met toepassingen met behulp van partitieschakelen en indexhints en in deze gevallen niet moet worden ingeschakeld. Het laten vallen van ongebruikte indexen wordt niet ondersteund voor Premium- en Business Critical-servicelagen.
>

Selecteer de automatische afstemmingsopties die u wilt inschakelen en selecteer **Toepassen**.

Automatische afstemmingsopties op een server worden toegepast op alle databases op deze server. Standaard nemen alle databases configuratie over van de bovenliggende server, maar deze kan voor elke database afzonderlijk worden overschreven en opgegeven.

### <a name="rest-api"></a>REST API

Meer informatie over het gebruik van REST API om automatische afstemming op een server in te schakelen, zie [SQL Server Automatic tuning UPDATE en GET HTTP-methoden.](https://docs.microsoft.com/rest/api/sql/serverautomatictuning)

## <a name="enable-automatic-tuning-on-an-individual-database"></a>Automatische afstemming inschakelen in een afzonderlijke database

Met de Azure SQL Database u de automatische afstemmingsconfiguratie voor elke database afzonderlijk opgeven. Op databaseniveau u ervoor kiezen om automatische tuningconfiguratie over te nemen van de bovenliggende server, 'Azure Defaults' of de configuratie niet over te nemen. Azure Defaults zijn ingesteld op FORCE_LAST_GOOD_PLAN is ingeschakeld, CREATE_INDEX is ingeschakeld en DROP_INDEX is uitgeschakeld.

> [!TIP]
> De algemene aanbeveling is om de automatische tuningconfiguratie op **serverniveau** te beheren, zodat dezelfde configuratie-instellingen automatisch op elke database kunnen worden toegepast. Configureer automatische afstemming op een afzonderlijke database alleen als u die database nodig hebt om andere instellingen te hebben dan anderen die instellingen van dezelfde server overnemen.
>

### <a name="azure-portal"></a>Azure Portal

Als u automatische afstemming in **één database**wilt inschakelen, navigeert u naar de database in azure-portal en selecteert u **Automatisch afstemmen**.

Individuele automatische afstemmingsinstellingen kunnen afzonderlijk voor elke database worden geconfigureerd. U handmatig een afzonderlijke automatische afstemmingsoptie configureren of opgeven dat een optie de instellingen van de server overneemt.

![Database](./media/sql-database-automatic-tuning-enable/database.png)

Houd er rekening mee dat DROP_INDEX optie op dit moment niet compatibel is met toepassingen die partitieschakel- en indexhints gebruiken en in deze gevallen niet mogen worden ingeschakeld.

Nadat u de gewenste configuratie hebt geselecteerd, klikt u op **Toepassen.**

### <a name="rest-api"></a>Rest-API

Lees meer over het gebruik van REST API om automatische afstemming in één database in te schakelen, zie [SQL Database Automatic tuning UPDATE en GET HTTP-methoden.](https://docs.microsoft.com/rest/api/sql/databaseautomatictuning)

### <a name="t-sql"></a>T-SQL

Als u automatische afstemming op één database via T-SQL wilt inschakelen, maakt u verbinding met de database en voert u de volgende query uit:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING = AUTO | INHERIT | CUSTOM
```

Als u automatische afstemming instelt op AUTO, worden Azure Defaults toegepast. Als u deze instelt op INHERIT, wordt de automatische tuningconfiguratie overgenomen van de bovenliggende server. Als u AANGEPAST kiest, moet u automatisch stemmen handmatig configureren.

Als u afzonderlijke automatische afstemmingsopties wilt configureren via T-SQL, maakt u verbinding met de database en voert u de query uit zoals deze:

```SQL
ALTER DATABASE current SET AUTOMATIC_TUNING (FORCE_LAST_GOOD_PLAN = ON, CREATE_INDEX = DEFAULT, DROP_INDEX = OFF)
```

Als u de afzonderlijke tuningoptie instelt op AAN, wordt elke instelling die de database heeft overgenomen, overschreven en wordt de tuningoptie ingeschakeld. Als u deze instelt op UIT, wordt ook elke instelling die de database heeft overgenomen, overschreven en wordt de tuningoptie uitgeschakeld. De optie automatische afstemming, waarvoor STANDAARD is opgegeven, neemt de automatische afstemmingsconfiguratie over van de instellingen op serverniveau.  

> [!IMPORTANT]
> In het geval van [actieve georeplicatie](sql-database-auto-failover-group.md)hoeft automatische afstemming alleen in de primaire database te worden geconfigureerd. Automatisch toegepaste tuningacties, zoals bijvoorbeeld indexmaken of verwijderen, worden automatisch gerepliceerd naar de alleen-lezen secundaire. Een poging om automatisch afstemmen via T-SQL in te schakelen op de alleen-lezen secundaire zal resulteren in een storing als het hebben van een andere tuning configuratie op de alleen-lezen secundaire wordt niet ondersteund.
>

Vind onze meer abut T-SQL opties om automatische tuning te configureren, zie [ALTER DATABASE SET Options (Transact-SQL) voor SQL Database server](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azuresqldb-current).

## <a name="disabled-by-the-system"></a>Uitgeschakeld door het systeem

Automatische tuning is het toezicht op alle acties die zij neemt op de database en in sommige gevallen kan bepalen dat automatische tuning niet goed kan werken op de database. In deze situatie wordt de tuning-optie uitgeschakeld door het systeem. In de meeste gevallen gebeurt dit omdat Query Store niet is ingeschakeld of omdat het alleen-lezen staat in een specifieke database.

## <a name="permissions"></a>Machtigingen

Aangezien automatische afstemming azure-functie is, moet u voor het gebruik ervan de ingebouwde RBAC-rollen van Azure gebruiken. Alleen SQL-verificatie gebruiken is niet voldoende om de functie van Azure-portal te gebruiken.

Om automatische afstemming te gebruiken, is de minimaal vereiste toestemming om aan de gebruiker te verlenen de ingebouwde [SQL DB-bijdragerrol van](../role-based-access-control/built-in-roles.md#sql-db-contributor) Azure. U ook overwegen om rollen met hogere bevoegdheden te gebruiken, zoals SQL Server-inzender, inzender en eigenaar.

## <a name="configure-automatic-tuning-e-mail-notifications"></a>Automatische tuning-e-mailmeldingen configureren

Zie [handleiding voor automatische afstemming van e-mailmeldingen.](sql-database-automatic-tuning-email-notifications.md)

## <a name="next-steps"></a>Volgende stappen

* Lees het [artikel Automatisch afstemmen](sql-database-automatic-tuning.md) voor meer informatie over automatisch stemmen en hoe het u kan helpen uw prestaties te verbeteren.
* Zie [Prestatieaanbevelingen](sql-database-advisor.md) voor een overzicht van prestatieaanbevelingen voor Azure SQL Database.
* Zie [Queryprestatiestatistieken](sql-database-query-performance.md) voor meer informatie over het bekijken van de prestatie-impact van uw topquery's.
