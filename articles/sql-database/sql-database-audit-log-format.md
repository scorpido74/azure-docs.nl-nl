---
title: Auditlogboek controleren
description: Begrijpen hoe SQL Database controle logboeken worden gestructureerd.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 01/03/2019
ms.openlocfilehash: 3b7a3c295d2edd60c70f47ea155a5d747a3bfb03
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873757"
---
# <a name="sql-database-audit-log-format"></a>Indeling van SQL Database audit logboek

Met [Azure SQL database controle](sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in uw Azure Storage-account geschreven, of verzonden naar Event Hub of log Analytics voor downstream-verwerking en-analyse.

## <a name="naming-conventions"></a>Naamgevings regels

### <a name="blob-audit"></a>BLOB-controle

Audit logboeken die zijn opgeslagen in Blob Storage worden opgeslagen in een container met de naam `sqldbauditlogs` in het Azure Storage-account. De Directory-hiërarchie in de container is van het formulier `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`. De indeling van de BLOB-bestands naam is `<CreationTime>_<FileNumberInSession>.xel`, waarbij `CreationTime` in UTC `hh_mm_ss_ms` notatie en `FileNumberInSession` een lopende index is in de sessie logboeken over meerdere BLOB-bestanden.

Bijvoorbeeld: voor data base-`Database1` op `Server1` is het volgende een geldig pad.

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

[Alleen-lezen replica's](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-read-scale-out) Audit logboeken worden in dezelfde container opgeslagen. De Directory-hiërarchie in de container is van het formulier `<ServerName>/<DatabaseName>/<AuditName>/<Date>/RO/`. De BLOB-bestands naam heeft dezelfde indeling. De audit logboeken van alleen-lezen Replica's worden in dezelfde container opgeslagen.


### <a name="event-hub"></a>Event Hub

Controle gebeurtenissen worden geschreven naar de naam ruimte en Event Hub die tijdens de controle configuratie zijn gedefinieerd, en worden vastgelegd in de hoofd tekst van [Apache Avro](https://avro.apache.org/) -gebeurtenissen en opgeslagen met JSON-indeling met UTF-8-code ring. Als u de audit logboeken wilt lezen, kunt u [Avro-Hulpprogram ma's](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) of gelijksoortige hulp middelen gebruiken waarmee deze indeling wordt verwerkt.

### <a name="log-analytics"></a>Log Analytics

Controle gebeurtenissen worden geschreven naar Log Analytics werk ruimte die tijdens de controle configuratie is gedefinieerd, naar de `AzureDiagnostics` tabel met de categorie `SQLSecurityAuditEvents`. Zie [log Analytics Search Reference](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search)(Engelstalig) voor meer nuttige informatie over log Analytics Zoek taal en-opdrachten.

## <a id="subheading-1"></a>Controle logboek velden

| Naam (BLOB) | Naam (Event Hubs/Log Analytics) | Beschrijving | Blobtype | Type Event Hubs/Log Analytics |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | ID van de actie | varchar (4) | string |
| action_name | action_name_s | De naam van de actie | N/A | string |
| additional_information | additional_information_s | Eventuele aanvullende informatie over de gebeurtenis, opgeslagen als XML | nvarchar (4000) | string |
| affected_rows | affected_rows_d | Het aantal rijen dat wordt beïnvloed door de query | bigint | int |
| application_name | application_name_s| Naam van client toepassing | nvarchar (128) | string |
| audit_schema_version | audit_schema_version_d | Altijd 1 | int | int |
| class_type | class_type_s | Type controle bare entiteit waarop de audit plaatsvindt | varchar (2) | string |
| class_type_desc | class_type_description_s | Beschrijving van de controle bare entiteit waarvoor de audit plaatsvindt | N/A | string |
| client_ip | client_ip_s | Bron-IP van de client toepassing | nvarchar (128) | string |
| connection_id | N/A | ID van de verbinding in de server | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | Gegevens typen en gevoeligheids labels die worden geretourneerd door de gecontroleerde query, op basis van de geclassificeerde kolommen in de data base. Meer informatie over het [detecteren en classificeren van Azure SQL database gegevens](sql-database-data-discovery-and-classification.md) | nvarchar (4000) | string |
| database_name | database_name_s | De database context waarin de actie is uitgevoerd | sysname | string |
| database_principal_id | database_principal_id_d | ID van de database gebruikers context waarin de actie wordt uitgevoerd | int | int |
| database_principal_name | database_principal_name_s | De naam van de database gebruikers context waarin de actie wordt uitgevoerd | sysname | string |
| duration_milliseconds | duration_milliseconds_d | Uitvoerings duur van de query in milliseconden | bigint | int |
| event_time | event_time_t | De datum en tijd waarop de Controleer bare actie wordt geactiveerd | datetime2 | datum/tijd |
| host_name | N/A | Hostnaam van client | string | N/A |
| is_column_permission | is_column_permission_s | Vlag waarmee wordt aangegeven of dit een machtiging op kolom niveau is. 1 = waar, 0 = ONWAAR | bit | string |
| N/A | is_server_level_audit_s | Vlag waarmee wordt aangegeven of deze controle zich op server niveau bevindt | N/A | string |
| object_-id | object_id_d | De ID van de entiteit waarop de audit heeft plaatsgevonden. Dit omvat de volgende elementen: Server objecten, data bases, database objecten en schema-objecten. 0 als de entiteit de server zelf is of als de audit niet wordt uitgevoerd op het niveau van een object | int | int |
| object_name | object_name_s | De naam van de entiteit waarop de audit heeft plaatsgevonden. Dit omvat de volgende elementen: Server objecten, data bases, database objecten en schema-objecten. 0 als de entiteit de server zelf is of als de audit niet wordt uitgevoerd op het niveau van een object | sysname | string |
| permission_bitmask | permission_bitmask_s | In voorkomend geval worden de machtigingen weer gegeven die zijn verleend, geweigerd of ingetrokken | varbinary(16) | string |
| response_rows | response_rows_d | Aantal rijen dat wordt geretourneerd in de resultatenset | bigint | int |
| schema_name | schema_name_s | De schema context waarin de actie is uitgevoerd. NULL voor controles buiten een schema | sysname | string |
| N/A | securable_class_type_s | Beveilig bare object dat is gekoppeld aan de class_type wordt gecontroleerd | N/A | string |
| sequence_group_id | sequence_group_id_g | Unieke id | varbinary | GUID |
| sequence_number | sequence_number_d | Houdt de volg orde bij van records binnen één controle record die te groot is om te passen in de schrijf buffer voor audits | int | int |
| server_instance_name | server_instance_name_s | Naam van het Server exemplaar waarop de audit heeft plaatsgevonden | sysname | string |
| server_principal_id | server_principal_id_d | ID van de aanmeldings context waarin de actie wordt uitgevoerd | int | int |
| server_principal_name | server_principal_name_s | Huidige aanmelding | sysname | string |
| server_principal_sid | server_principal_sid_s | Huidige aanmeldings-SID | varbinary | string |
| session_id | session_id_d | De ID van de sessie waarop de gebeurtenis heeft plaatsgevonden | smallint | int |
| session_server_principal_name | session_server_principal_name_s | Server-Principal voor sessie | sysname | string |
| instructie | statement_s | T-SQL-instructie die is uitgevoerd (indien van toepassing) | nvarchar (4000) | string |
| geslaagd | succeeded_s | Hiermee wordt aangegeven of de actie die de gebeurtenis heeft geactiveerd is geslaagd. Voor andere gebeurtenissen dan aanmelden en batch wordt hiermee alleen gerapporteerd of de machtiging is geslaagd of mislukt, niet voor de bewerking. 1 = geslaagd, 0 = mislukt | bit | string |
| target_database_principal_id | target_database_principal_id_d | De databaseprincipal waarmee de bewerking GRANT/DENY/REVOKE wordt uitgevoerd. 0 indien niet van toepassing | int | int |
| target_database_principal_name | target_database_principal_name_s | Doel gebruiker van actie. NULL indien niet van toepassing | string | string |
| target_server_principal_id | target_server_principal_id_d | Server-Principal waarop de bewerking GRANT/DENY/REVOKE wordt uitgevoerd. Retourneert 0 indien niet van toepassing | int | int |
| target_server_principal_name | target_server_principal_name_s | Aanmeldingen bij doel van actie. NULL indien niet van toepassing | sysname | string |
| target_server_principal_sid | target_server_principal_sid_s | SID van de doel aanmelding. NULL indien niet van toepassing | varbinary | string |
| transaction_id | transaction_id_d | Alleen SQL Server (vanaf 2016)-0 voor Azure SQL DB | bigint | int |
| user_defined_event_id | user_defined_event_id_d | Door de gebruiker gedefinieerde gebeurtenis-id door gegeven als argument voor sp_audit_write. NULL voor systeem gebeurtenissen (standaard) en niet-nul voor door de gebruiker gedefinieerde gebeurtenis. Zie [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) voor meer informatie. | smallint | int |
| user_defined_information | user_defined_information_s | Door de gebruiker gedefinieerde gegevens die als argument aan sp_audit_write worden door gegeven. NULL voor systeem gebeurtenissen (standaard) en niet-nul voor door de gebruiker gedefinieerde gebeurtenis. Zie [sp_audit_write (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) voor meer informatie. | nvarchar (4000) | string |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [controleren van Azure SQL database](sql-database-auditing.md).
