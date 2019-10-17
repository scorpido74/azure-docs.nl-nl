---
title: Controle logboeken voor Azure Database for MySQL
description: Beschrijft de audit logboeken die beschikbaar zijn in Azure Database for MySQL en de beschik bare para meters voor het inschakelen van logboek registratie niveaus.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 42881fcb12f29ec14bbdc0ec4942b2eef17c7312
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434397"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Audit Logboeken in Azure Database for MySQL

In Azure Database for MySQL is het controle logboek beschikbaar voor gebruikers. Het controle logboek kan worden gebruikt om activiteit op database niveau bij te houden en wordt vaak gebruikt voor naleving.

> [!IMPORTANT]
> De functionaliteit van het controle logboek is momenteel beschikbaar als preview-versie.

## <a name="configure-audit-logging"></a>Controle logboek registratie configureren

Het controle logboek is standaard uitgeschakeld. Als u deze functie wilt inschakelen, stelt u `audit_log_enabled` in op aan.

Andere para meters die u kunt aanpassen zijn onder andere:

- `audit_log_events`: bepaalt welke gebeurtenissen moeten worden geregistreerd. Zie de onderstaande tabel voor specifieke controle gebeurtenissen.
- `audit_log_include_users`: MySQL-gebruikers die moeten worden opgenomen voor logboek registratie. De standaard waarde voor deze para meter is leeg, die alle gebruikers bevat die moeten worden geregistreerd. Dit heeft een hogere prioriteit dan `audit_log_exclude_users`. De maximale lengte van de para meter is 512 tekens.
> [!Note]
> `audit_log_include_users` heeft een hogere prioriteit dan `audit_log_exclude_users` bijvoorbeeld als audit_log_include_users = `demouser` en audit_log_exclude_users = `demouser`, wordt de logboeken gecontroleerd, omdat `audit_log_include_users` een hogere prioriteit heeft.
- `audit_log_exclude_users`: MySQL-gebruikers die moeten worden uitgesloten van logboek registratie. De maximale lengte van de para meter is 512 tekens.

> [!Note]
> Voor `sql_text` wordt het logboek afgekapt als dit 2048 tekens overschrijdt.

| **Gebeurtenis** | **Beschrijving** |
|---|---|
| `CONNECTION` | -Initiëren van verbinding (geslaagd of mislukt) <br> -Herauthenticatie van gebruiker met andere gebruiker/wacht woord tijdens sessie <br> -Verbinding beëindigen |
| `DML_SELECT`| Query's selecteren |
| `DML_NONSELECT` | Query's invoegen/verwijderen/bijwerken |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Query's als "DROP data base" |
| `DCL` | Query's zoals ' machtiging verlenen ' |
| `ADMIN` | Query's als "STATUS weer geven" |
| `GENERAL` | Alles in DML_SELECT, DML_NONSELECT, DML, DDL, DCL en beheerder |
| `TABLE_ACCESS` | -Alleen beschikbaar voor MySQL 5,7 <br> -Lees instructies voor tabellen, zoals selecteren of invoegen in... UITGESCHAKELD <br> -Instructies voor het verwijderen van tabellen, zoals verwijderen of TRUNCATE TABLE <br> -Instructies voor het invoegen van tabellen, zoals invoegen of vervangen <br> -Update-instructies voor tabellen, zoals UPDATE |

## <a name="access-audit-logs"></a>Auditlogboeken inzien

Audit logboeken zijn geïntegreerd met Azure Monitor Diagnostische logboeken. Zodra u audit logboeken op de MySQL-server hebt ingeschakeld, kunt u ze naar Azure Monitor-logboeken, Event Hubs of Azure Storage verzenden. Zie het artikel over het [controle logboek](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)van de portal voor meer informatie over het inschakelen van Diagnostische logboeken in de Azure Portal.

## <a name="diagnostic-logs-schemas"></a>Schema's voor Diagnostische logboeken

In de volgende secties wordt beschreven wat er wordt uitgevoerd door MySQL-controle logboeken op basis van het gebeurtenis type. Afhankelijk van de uitvoer methode worden de opgenomen velden en de volg orde waarin ze worden weer gegeven, mogelijk verschillend.

### <a name="connection"></a>Verbinding

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw Tenant-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tijds tempel voor het vastleggen van het logboek in UTC |
| `Type` | Het type van het logboek. Altijd `AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de resource groep waartoe de server behoort |
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Naam van de server |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (alleen beschikbaar voor MySQL 5,7) |
| `connection_id_d` | Unieke verbindings-ID die is gegenereerd door MySQL |
| `host_s` | Leeg |
| `ip_s` | IP-adres van de client die verbinding maakt met MySQL |
| `user_s` | Naam van de gebruiker die de query uitvoert |
| `db_s` | Naam van de data base die is verbonden met |
| `\_ResourceId` | Resource-URI |

### <a name="general"></a>Algemeen

Schema hieronder is van toepassing op de gebeurtenis typen algemeen, DML_SELECT, DML_NONSELECT, DML, DDL, DCL en beheerder.

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw Tenant-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tijds tempel voor het vastleggen van het logboek in UTC |
| `Type` | Het type van het logboek. Altijd `AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de resource groep waartoe de server behoort |
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Naam van de server |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (alleen beschikbaar voor MySQL 5,6) |
| `event_time` | Begin tijd van de query in UTC-tijds tempel |
| `error_code_d` | Fout code als de query is mislukt. `0` betekent geen fout |
| `thread_id_d` | ID van de thread die de query heeft uitgevoerd |
| `host_s` | Leeg |
| `ip_s` | IP-adres van de client die verbinding maakt met MySQL |
| `user_s` | Naam van de gebruiker die de query uitvoert |
| `sql_text_s` | Volledige query tekst |
| `\_ResourceId` | Resource-URI |

### <a name="table-access"></a>Tabel toegang

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw Tenant-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tijds tempel voor het vastleggen van het logboek in UTC |
| `Type` | Het type van het logboek. Altijd `AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de resource groep waartoe de server behoort |
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Naam van de server |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` of `DELETE` |
| `connection_id_d` | Unieke verbindings-ID die is gegenereerd door MySQL |
| `db_s` | De naam van de data base die wordt geopend |
| `table_s` | De naam van de tabel die wordt geopend |
| `sql_text_s` | Volledige query tekst |
| `\_ResourceId` | Resource-URI |

## <a name="next-steps"></a>Volgende stappen

- [Audit logboeken configureren in de Azure Portal](howto-configure-audit-logs-portal.md)