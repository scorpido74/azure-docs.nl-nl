---
title: Auditlogboek controleren
description: Begrijpen hoe SQL Database-controlelogboeken zijn gestructureerd.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 13746b86eed75055ceb5203afafb2d27a78ce1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722081"
---
# <a name="sql-database-audit-log-format"></a>SQL-databasecontrolelogboekindeling

[Azure SQL Database auditing](sql-database-auditing.md) houdt databasegebeurtenissen bij en schrijft deze naar een controlelogboek in uw Azure-opslagaccount of stuurt ze naar Event Hub of Log Analytics voor downstreamverwerking en -analyse.

## <a name="naming-conventions"></a>Naamgevingsconventies

### <a name="blob-audit"></a>Blob-controle

Controlelogboeken die zijn opgeslagen in `sqldbauditlogs` Blob-opslag, worden opgeslagen in een container met de naam In het Azure Storage-account. De maphiërarchie in de container `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`is van het formulier . De blobbestandsnaamindeling `<CreationTime>_<FileNumberInSession>.xel`is `CreationTime` , `hh_mm_ss_ms` waar is `FileNumberInSession` in UTC-indeling, en is een lopende index voor het geval sessielogboeken zich over meerdere Blob-bestanden uitstrekken.

Voor database `Database1` op `Server1` het volgende is bijvoorbeeld een mogelijk geldig pad:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[Alleen-lezen replica's](sql-database-read-scale-out.md) Controlelogboeken worden opgeslagen in dezelfde container. De maphiërarchie in de container `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`is van het formulier . De Blob-bestandsnaam deelt dezelfde indeling. De controlelogboeken van alleen-lezen replica's worden opgeslagen in dezelfde container.


### <a name="event-hub"></a>Event Hub

Controlegebeurtenissen worden geschreven naar de naamruimte- en gebeurtenishub die is gedefinieerd tijdens de controleconfiguratie en worden vastgelegd in het lichaam van [Apache Avro-gebeurtenissen](https://avro.apache.org/) en opgeslagen met JSON-opmaak met UTF-8-codering. Als u de controlelogboeken wilt lezen, u [Avro Tools](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) of vergelijkbare hulpprogramma's gebruiken die dit formaat verwerken.

### <a name="log-analytics"></a>Log Analytics

Controlegebeurtenissen worden geschreven naar de Log Analytics-werkruimte `AzureDiagnostics` die tijdens `SQLSecurityAuditEvents`de controleconfiguratie is gedefinieerd, naar de tabel met de categorie . Zie [Zoekverwijzing Log Analytics](../log-analytics/log-analytics-log-search.md)voor meer nuttige informatie over de zoektaal en -opdrachten van Log Analytics.

## <a name="audit-log-fields"></a><a id="subheading-1"></a>Controlelogboekvelden

| Naam (Blob) | Naam (gebeurtenishubs/logboekanalyse) | Beschrijving | Blobtype | Type gebeurtenishubs/logboekanalyse |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID van de actie | varchar(4) | tekenreeks |
| action_name | action_name_s | Naam van de actie | N.v.t. | tekenreeks |
| additional_information | additional_information_s | Aanvullende informatie over de gebeurtenis, opgeslagen als XML | nvarchar(4000) | tekenreeks |
| affected_rows | affected_rows_d | Aantal rijen dat door de query wordt beïnvloed | bigint | int |
| application_name | application_name_s| Naam van clienttoepassing | nvarchar(128) | tekenreeks |
| audit_schema_version | audit_schema_version_d | Altijd 1 | int | int |
| class_type | class_type_s | Type controleerbare entiteit waarop de controle plaatsvindt | varchar(2) | tekenreeks |
| class_type_desc | class_type_description_s | Beschrijving van de controleerbare entiteit waarop de controle plaatsvindt | N.v.t. | tekenreeks |
| client_ip | client_ip_s | Bron-IP van de clienttoepassing | nvarchar(128) | tekenreeks |
| connection_id | N.v.t. | ID van de verbinding in de server | GUID | N.v.t. |
| data_sensitivity_information | data_sensitivity_information_s | Informatietypen en gevoeligheidslabels die worden geretourneerd door de gecontroleerde query, op basis van de geclassificeerde kolommen in de database. Meer informatie over [het ontdekken en classificeren van Azure SQL Database-gegevens](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | tekenreeks |
| Database_name | database_name_s | De databasecontext waarin de actie heeft plaatsgevonden | sysname | tekenreeks |
| database_principal_id | database_principal_id_d | ID van de databasegebruikerscontext waarin de actie wordt uitgevoerd | int | int |
| database_principal_name | database_principal_name_s | Naam van de context van de databasegebruiker waarin de actie wordt uitgevoerd | sysname | tekenreeks |
| duration_milliseconds | duration_milliseconds_d | Duur van queryuitvoering in milliseconden | bigint | int |
| event_time | event_time_t | Datum en tijd waarop de controleerbare actie wordt geactiveerd | datetime2 | datum/tijd |
| host_name | N.v.t. | Naam clienthost | tekenreeks | N.v.t. |
| is_column_permission | is_column_permission_s | Vlag die aangeeft of dit een machtiging op kolomniveau is. 1 = waar, 0 = vals | bit | tekenreeks |
| N.v.t. | is_server_level_audit_s | Vlag die aangeeft of deze controle zich op serverniveau bevindt | N.v.t. | tekenreeks |
| object_ id | object_id_d | De id van de entiteit waarop de controle heeft plaatsgevonden. Dit omvat de : serverobjecten, databases, databaseobjecten en schemaobjecten. 0 als de entiteit de server zelf is of als de audit niet op objectniveau wordt uitgevoerd | int | int |
| object_name | object_name_s | De naam van de entiteit waarop de controle heeft plaatsgevonden. Dit omvat de : serverobjecten, databases, databaseobjecten en schemaobjecten. 0 als de entiteit de server zelf is of als de audit niet op objectniveau wordt uitgevoerd | sysname | tekenreeks |
| permission_bitmask | permission_bitmask_s | Wanneer dit van toepassing is, worden de machtigingen weergegeven die zijn verleend, geweigerd of ingetrokken | varbinary(16) | tekenreeks |
| response_rows | response_rows_d | Aantal rijen dat is geretourneerd in de resultaatset | bigint | int |
| schema_name | schema_name_s | De schemacontext waarin de actie heeft plaatsgevonden. NULL voor audits die buiten een schema plaatsvinden | sysname | tekenreeks |
| N.v.t. | securable_class_type_s | Securable object dat kaarten aan de class_type wordt gecontroleerd | N.v.t. | tekenreeks |
| sequence_group_id | sequence_group_id_g | Unieke id | varbinary varbinary | GUID |
| sequence_number | sequence_number_d | Houdt de volgorde bij van records binnen één auditrecord die te groot was om in de schrijfbuffer voor audits te passen | int | int |
| server_instance_name | server_instance_name_s | Naam van de serverinstantie waar de audit heeft plaatsgevonden | sysname | tekenreeks |
| server_principal_id | server_principal_id_d | ID van de login context waarin de actie wordt uitgevoerd | int | int |
| server_principal_name | server_principal_name_s | Huidige aanmelding | sysname | tekenreeks |
| server_principal_sid | server_principal_sid_s | Huidige login SID | varbinary varbinary | tekenreeks |
| session_id | session_id_d | ID van de sessie waarop de gebeurtenis heeft plaatsgevonden | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Serverprincipal voor sessie | sysname | tekenreeks |
| Verklaring | statement_s | T-SQL-instructie die is uitgevoerd (indien aanwezig) | nvarchar(4000) | tekenreeks |
| geslaagd | succeeded_s | Geeft aan of de actie die de gebeurtenis heeft geactiveerd, is geslaagd. Voor andere gebeurtenissen dan login en batch wordt alleen gemeld of de machtigingscontrole is geslaagd of mislukt, niet de bewerking. 1 = succes, 0 = fail | bit | tekenreeks |
| target_database_principal_id | target_database_principal_id_d | De databaseprincipal van de grant/deny/revoke-bewerking wordt uitgevoerd. 0 indien niet van toepassing | int | int |
| target_database_principal_name | target_database_principal_name_s | Doelgebruiker van actie. NULL indien niet van toepassing | tekenreeks | tekenreeks |
| target_server_principal_id | target_server_principal_id_d | Serverprincipal waarop de bewerking GRANT/DENY/INTREKKEN wordt uitgevoerd. Retourneert 0 indien niet van toepassing | int | int |
| target_server_principal_name | target_server_principal_name_s | Doel login van actie. NULL indien niet van toepassing | sysname | tekenreeks |
| target_server_principal_sid | target_server_principal_sid_s | SID van doel login. NULL indien niet van toepassing | varbinary varbinary | tekenreeks |
| transaction_id | transaction_id_d | ALLEEN SQL Server (vanaf 2016) - 0 voor Azure SQL DB | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Door de gebruiker gedefinieerde gebeurtenis-id die als argument wordt doorgegeven aan sp_audit_write. NULL voor systeemgebeurtenissen (standaard) en niet-nul voor door de gebruiker gedefinieerde gebeurtenis. Zie [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) voor meer informatie | smallint | int |
| user_defined_information | user_defined_information_s | Gebruiker gedefinieerde informatie doorgegeven als een argument om te sp_audit_write. NULL voor systeemgebeurtenissen (standaard) en niet-nul voor door de gebruiker gedefinieerde gebeurtenis. Zie [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) voor meer informatie | nvarchar(4000) | tekenreeks |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure SQL Database auditing](sql-database-auditing.md).
