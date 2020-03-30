---
title: Controlelogboeken - Azure Database voor MySQL
description: Beschrijft de controlelogboeken die beschikbaar zijn in Azure Database voor MySQL en de beschikbare parameters voor het inschakelen van logboeken.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/19/2020
ms.openlocfilehash: b42f0d7a8146f7f2b313959273abd22303c89a60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062548"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Controlelogboeken in Azure-database voor MySQL

In Azure Database voor MySQL is het controlelogboek beschikbaar voor gebruikers. Het controlelogboek kan worden gebruikt om activiteiten op databaseniveau bij te houden en wordt vaak gebruikt voor naleving.

> [!IMPORTANT]
> De functionaliteit van het controlelogboek is momenteel in preview.

## <a name="configure-audit-logging"></a>Controlelogboekregistratie configureren

Standaard is het controlelogboek uitgeschakeld. Als u deze `audit_log_enabled` wilt inschakelen, stelt u in op AAN.

Andere parameters die u aanpassen zijn:

- `audit_log_events`: bepaalt de gebeurtenissen die moeten worden geregistreerd. Zie onderstaande tabel voor specifieke auditgebeurtenissen.
- `audit_log_include_users`: MySQL-gebruikers worden opgenomen voor logboekregistratie. De standaardwaarde voor deze parameter is leeg, waaronder alle gebruikers die moeten worden logboekregistratie. Dit heeft een `audit_log_exclude_users`hogere prioriteit boven . De maximale lengte van de parameter is 512 tekens.
> [!Note]
> `audit_log_include_users`heeft een `audit_log_exclude_users`hogere prioriteit boven . Bijvoorbeeld, als `audit_log_include_users`  =  `demouser` `audit_log_exclude_users`  =  `demouser`en , de gebruiker zal worden `audit_log_include_users` opgenomen in de controle logs omdat heeft een hogere prioriteit.
- `audit_log_exclude_users`: MySQL-gebruikers worden uitgesloten van logboekregistratie. De maximale lengte van de parameter is 512 tekens.

> [!Note]
> Voor `sql_text`, log zal worden afgekapt als het meer dan 2048 tekens.

| **Gebeurtenis** | **Beschrijving** |
|---|---|
| `CONNECTION` | - Initiatie van de verbinding (geslaagd of mislukt) <br> - Herverificatie door gebruiker met ander gebruikers-/wachtwoord tijdens sessie <br> - Beëindiging van de verbinding |
| `DML_SELECT`| SELECT-query's |
| `DML_NONSELECT` | Query's INVOEGEN/VERWIJDEREN/BIJWERKEN |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Query's zoals "DROP DATABASE" |
| `DCL` | Query's zoals 'TOESTEMMING VERLENEN' |
| `ADMIN` | Query's zoals 'STATUS WEERGEVEN' |
| `GENERAL` | Alles in DML_SELECT, DML_NONSELECT, DML, DDL, DCL en ADMIN |
| `TABLE_ACCESS` | - Alleen beschikbaar voor MySQL 5.7 <br> - Tabel leesverklaringen, zoals SELECT of INSERT IN ... Selecteer <br> - Tabelverwijderinstructies, zoals DELETE- of TRUNCATE-tabel <br> - Tabelinvoegingsinstructies, zoals INVOEGEN of VERVANGEN <br> - Tabelupdate-instructies, zoals BIJWERKEN |

## <a name="access-audit-logs"></a>Auditlogboeken inzien

Controlelogboeken zijn geïntegreerd met Azure Monitor Diagnostic Logs. Zodra u controlelogboeken op uw MySQL-server hebt ingeschakeld, u deze verzenden naar Azure Monitor-logboeken, gebeurtenishubs of Azure Storage. Zie het artikel over de [controlelogboekportal](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)voor meer informatie over het inschakelen van diagnostische logboeken in de Azure-portal.

## <a name="diagnostic-logs-schemas"></a>Schema's voor diagnostische logboeken

In de volgende secties wordt beschreven wat de uitvoer is uitgevoerd door MySQL-controlelogboeken op basis van het gebeurtenistype. Afhankelijk van de uitvoermethode kunnen de opgenomen velden en de volgorde waarin ze worden weergegeven, variëren.

### <a name="connection"></a>Verbinding

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw tenant-id |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tijdstempel toen het logboek werd opgenomen in UTC |
| `Type` | Type van het logboek. Altijd`AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de brongroep waartoe de server behoort |
| `ResourceProvider` | Naam van de resourceprovider. Altijd`MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Naam van de server |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` `CHANGE USER` (alleen beschikbaar voor MySQL 5.7) |
| `connection_id_d` | Unieke verbindings-ID gegenereerd door MySQL |
| `host_s` | Blank |
| `ip_s` | IP-adres van client die verbinding maakt met MySQL |
| `user_s` | Naam van de gebruiker die de query uitvoert |
| `db_s` | Naam van de database die is verbonden met |
| `\_ResourceId` | Resource URI |

### <a name="general"></a>Algemeen

Onderstaand schema is van toepassing op gebeurtenistypen ALGEMEEN, DML_SELECT, DML_NONSELECT, DML, DDL, DCL en ADMIN.

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw tenant-id |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tijdstempel toen het logboek werd opgenomen in UTC |
| `Type` | Type van het logboek. Altijd`AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de brongroep waartoe de server behoort |
| `ResourceProvider` | Naam van de resourceprovider. Altijd`MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Naam van de server |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` `RESULT` (alleen beschikbaar voor MySQL 5.6) |
| `event_time` | Begintijd van query in UTC-tijdstempel |
| `error_code_d` | Foutcode als query is mislukt. `0`betekent geen fout |
| `thread_id_d` | ID van thread die de query heeft uitgevoerd |
| `host_s` | Blank |
| `ip_s` | IP-adres van client die verbinding maakt met MySQL |
| `user_s` | Naam van de gebruiker die de query uitvoert |
| `sql_text_s` | Volledige querytekst |
| `\_ResourceId` | Resource URI |

### <a name="table-access"></a>Tabeltoegang

> [!NOTE]
> Tabeltoegangslogboeken worden alleen uitgevoerd voor MySQL 5.7.

| **Eigenschap** | **Beschrijving** |
|---|---|
| `TenantId` | Uw tenant-id |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tijdstempel toen het logboek werd opgenomen in UTC |
| `Type` | Type van het logboek. Altijd`AzureDiagnostics` |
| `SubscriptionId` | GUID voor het abonnement waartoe de server behoort |
| `ResourceGroup` | Naam van de brongroep waartoe de server behoort |
| `ResourceProvider` | Naam van de resourceprovider. Altijd`MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resource URI |
| `Resource` | Naam van de server |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Naam van de server |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT` `UPDATE`, , of`DELETE` |
| `connection_id_d` | Unieke verbindings-ID gegenereerd door MySQL |
| `db_s` | Naam van de geopende database |
| `table_s` | Naam van geopende tabel |
| `sql_text_s` | Volledige querytekst |
| `\_ResourceId` | Resource URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Logboeken in Azure-monitorlogboeken analyseren

Zodra uw controlelogboeken via diagnostische logboeken naar Azure Monitor Logs zijn gepipeteerd, u uw gecontroleerde gebeurtenissen verder analyseren. Hieronder vindt u enkele voorbeeldquery's om u op weg te helpen. Zorg ervoor dat u de onderstaande update met uw server naam.

- Algemene gebeurtenissen op een bepaalde server weergeven

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- VERBINDINGSGEBEURTENISSEN op een bepaalde server weergeven

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Gecontroleerde gebeurtenissen op een bepaalde server samenvatten

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- De distributie van het type controlegebeurtenis op een bepaalde server in kaart te plaatsen

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Gecontroleerde gebeurtenissen weergeven op alle MySQL-servers met diagnostische logboeken ingeschakeld voor controlelogboeken

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Volgende stappen

- [Controlelogboeken configureren in de Azure-portal](howto-configure-audit-logs-portal.md)