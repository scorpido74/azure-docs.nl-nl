---
title: Audit logboeken-Azure Database for MariaDB
description: Beschrijft de audit logboeken die beschikbaar zijn in Azure Database for MariaDB en de beschik bare para meters voor het inschakelen van logboek registratie niveaus.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 64662499b4ee782bbf04e9e706cd659e84c90eec
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773070"
---
# <a name="audit-logs-in-azure-database-for-mariadb"></a>Audit Logboeken in Azure Database for MariaDB

In Azure Database for MariaDB is het controle logboek beschikbaar voor gebruikers. Het controle logboek kan worden gebruikt om activiteit op database niveau bij te houden en wordt vaak gebruikt voor naleving.

> [!IMPORTANT]
> De functionaliteit van het controle logboek is momenteel beschikbaar als preview-versie.

## <a name="configure-audit-logging"></a>Controle logboek registratie configureren

Het controle logboek is standaard uitgeschakeld. Als u deze functie wilt inschakelen, stelt u `audit_log_enabled` in op aan.

Andere para meters die u kunt aanpassen zijn onder andere:

- `audit_log_events`: Hiermee beheert u de gebeurtenissen die moeten worden geregistreerd. Zie de onderstaande tabel voor specifieke controle gebeurtenissen.
- `audit_log_exclude_users`: MariaDB gebruikers moeten worden uitgesloten van logboek registratie. Hiermee worden Maxi maal vier gebruikers toegestaan. De maximale lengte van de para meter is 256 tekens.

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

## <a name="access-audit-logs"></a>Auditlogboeken inzien

Audit logboeken zijn geïntegreerd met Azure Monitor Diagnostische logboeken. Zodra u audit logboeken op uw MariaDB-server hebt ingeschakeld, kunt u ze naar Azure Monitor-logboeken, Event Hubs of Azure Storage verzenden. Zie het artikel over het [controle logboek](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)van de portal voor meer informatie over het inschakelen van Diagnostische logboeken in de Azure Portal.

## <a name="diagnostic-logs-schemas"></a>Schema's voor Diagnostische logboeken

In de volgende secties wordt beschreven wat er wordt uitgevoerd door MariaDB audit logs op basis van het gebeurtenis type. Afhankelijk van de uitvoer methode worden de opgenomen velden en de volg orde waarin ze worden weer gegeven, mogelijk verschillend.

### <a name="connection"></a>Verbinding

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw Tenant-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tijds tempel voor het vastleggen van het logboek in UTC |
| `Type` | Het type van het logboek. Altijd `AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de resource groep waartoe de server behoort |
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` |
| `connection_id_d` | Unieke verbindings-ID die wordt gegenereerd door MariaDB |
| `host_s` | Leeg |
| `ip_s` | IP-adres van de client die verbinding maakt met MariaDB |
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
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Naam van de server |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` |
| `event_time` | Begin seconden query in UNIX-tijds tempel |
| `error_code_d` | Fout code als de query is mislukt. `0` betekent geen fout |
| `thread_id_d` | ID van de thread die de query heeft uitgevoerd |
| `host_s` | Leeg |
| `ip_s` | IP-adres van de client die verbinding maakt met MariaDB |
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
| `ResourceProvider` | De naam van de resource provider. Altijd `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource-URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Naam van de server |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE`of `DELETE` |
| `connection_id_d` | Unieke verbindings-ID die wordt gegenereerd door MariaDB |
| `db_s` | De naam van de data base die wordt geopend |
| `table_s` | De naam van de tabel die wordt geopend |
| `sql_text_s` | Volledige query tekst |
| `\_ResourceId` | Resource-URI |

## <a name="next-steps"></a>Volgende stappen

- [Audit logboeken configureren in de Azure Portal](howto-configure-audit-logs-portal.md)