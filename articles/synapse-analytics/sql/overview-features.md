---
title: Verschillen in T-SQL-functie in Azure Synapse SQL
description: Lijst met Transact-SQL-functies die beschikbaar zijn in Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: b3e4d705bea7243966959038cf15373097c73ebc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424893"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Transact-SQL-functies die worden ondersteund in Azure Synapse SQL

Azure Synapse SQL is een big data analytische service waarmee u uw gegevens opvragen en analyseren met behulp van de T-SQL-taal. U standaard ANSI-compatibel dialect van SQL-taal gebruiken dat wordt gebruikt op SQL Server en Azure SQL Database voor gegevensanalyse. 

Transact-SQL-taal wordt gebruikt in Synapse SQL-serverless en het ingerichte model kan verwijzen naar verschillende objecten en heeft enkele verschillen in de set ondersteunde functies. Op deze pagina vindt u taalverschillen op hoog niveau tussen verbruiksmodellen van Synapse SQL.

## <a name="database-objects"></a>Databaseobjecten

Met verbruiksmodellen in Synapse SQL u verschillende databaseobjecten gebruiken. De vergelijking van ondersteunde objecttypen wordt weergegeven in de volgende tabel:

|   | Ingericht | Serverloos |
| --- | --- | --- |
| Tabellen | [Ja](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee, serverloos model kan alleen externe gegevens opvragen die op [Azure Storage](#storage-options) zijn geplaatst |
| Weergaven | [Ja.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Weergaven kunnen [querytaalelementen](#query-language) gebruiken die beschikbaar zijn in het ingerichte model. | [Ja.](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Weergaven kunnen [querytaalelementen](#query-language) gebruiken die beschikbaar zijn in het serverloze model. |
| Schema 's | [Ja](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Tijdelijke tabellen | [Ja](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nee |
| Procedures | [Ja](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| Functions | [Ja](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| Triggers | Nee | Nee |
| Externe tabellen | [Ja.](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Zie ondersteunde [gegevensindelingen](#data-formats). | [Ja.](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) Zie ondersteunde [gegevensindelingen](#data-formats). |
| Caching-query's | Ja, meerdere formulieren (SSD-gebaseerde caching, in-memory, resultset caching). Bovendien worden gematerialiseerde weergave ondersteund | Nee |
| Tabelvariabelen | [Nee,](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)gebruik tijdelijke tabellen | Nee |

## <a name="query-language"></a>Querytaal

Querytalen die in Synapse SQL worden gebruikt, kunnen verschillende ondersteunde functies hebben, afhankelijk van het verbruiksmodel. In de volgende tabel worden de belangrijkste verschillen in querytaal in Transact-SQL-dialecten beschreven:

|   | Ingericht | Serverloos |
| --- | --- | --- |
| SELECT-instructie | Ja. Transact-SQL queryclausules [VOOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) worden niet ondersteund. | Ja. Transact-SQL-queryclausules [VOOR XML,](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [MATCH,](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en queryhints worden niet ondersteund. [OFFSET/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) en [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kunnen alleen worden gebruikt om gegevens op te vragen in tijdelijke tabellen (geen externe gegevens). |
| Instructie INVOEGEN | Ja | Nee |
| Update-instructie | Ja | Nee |
| Verwijderinstructie | Ja | Nee |
| Samenvoegen-instructie | Ja | Nee |
| Gegevensbelasting | Ja. Preferred utility is [COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) statement, maar het systeem ondersteunt zowel BULK load (BCP) als [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor het laden van gegevens. | Nee |
| Gegevensexport | Ja. Cetas [gebruiken](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Ja. Cetas [gebruiken](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| Typen | Ja, alle Transact-SQL-typen behalve [cursor,](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [hierarchyid,](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ntext, tekst en afbeelding,](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [rijversie](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Ruimtelijke typen,](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [sql-variant\_](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alle Transact-SQL-typen behalve [cursor,](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [hierarchyid,](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [ntext, tekst en afbeelding,](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [rijversie](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Ruimtelijke typen,](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [sql-variant,\_](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en tabeltype |
| Query’s tussen databases | Nee | Ja, [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) inclusief USE-instructie. |
| Ingebouwde functies (analyse) | Ja, alle Transact-SQL [Analytische](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), Conversie, [Datum en Tijd,](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Logische, [Wiskundige](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) functies, behalve [KIEZEN,](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alle Transact-SQL [Analytische,](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Conversie, [Datum en Tijd,](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Logische, [Wiskundige](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) functies. |
| Ingebouwde functies (tekst) | Ja. Alle functies Transact-SQL [String,](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en Collation, behalve [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. Alle functies Transact-SQL [String,](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en Collation. |
| Ingebouwde tabelwaardefuncties | Ja, [transact-SQL Rowset-functies](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), met uitzondering van [OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENDATASOURCE,](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, [transact-SQL Rowset-functies](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), met uitzondering van [OPENXML,](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Aggregaties |  Transact-SQL ingebouwde aggregaten, behalve, behalve [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Transact-SQL ingebouwde aggregaten, behalve [\_STRING AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Operators | Ja, alle [Transact-SQL operators](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) behalve [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alle [Transact-SQL-operatoren](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Controle van de stroom | Ja. Alle [transact-SQL Control-of-flow-instructie,](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) behalve [CONTINUE,](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [GOTO,](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [RETURN,](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. Alle [transact-SQL-controle-van-stroominstructie,](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) behalve `WHILE (...)` [RETURN-](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en SELECT-query in voorwaarde |
| DDL-instructies (CREATE, ALTER, DROP) | Ja. Alle Transact-SQL DDL-instructie die van toepassing is op de ondersteunde objecttypen | Ja. Alle Transact-SQL DDL-instructie die van toepassing is op de ondersteunde objecttypen |

## <a name="security"></a>Beveiliging

Synapse SQL stelt u in staat om ingebouwde beveiligingsfuncties te gebruiken om uw gegevens te beveiligen en de toegang te beheren. In de volgende tabel worden verschillen op hoog niveau tussen Synapse SQL-verbruiksmodellen vergeleken.

|   | Ingericht | Serverloos |
| --- | --- | --- |
| Aanmeldingen | N/A (alleen opgenomen gebruikers worden ondersteund in databases) | Ja |
| Gebruikers |  N/A (alleen opgenomen gebruikers worden ondersteund in databases) | Ja |
| [Ingesloten gebruikers](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. **Opmerking:** slechts één AAD-gebruiker kan onbeperkt worden beheerder | Ja |
| Azure Active Directory (AAD)-verificatie | Ja, AAD-gebruikers | Ja, AAD-aanmeldingen en gebruikers |
| Verificatie van OPSLAG AAD-passthrough | Ja | Ja |
| SAS-tokenverificatie voor opslag | Nee | Ja, met [behulp van DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) in EXTERNE [GEGEVENSBRON](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) of referentie [op](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)instantieniveau . |
| Verificatie van opslagtoegangssleutel | Ja, [databasescopedreferentie gebruiken](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) in [externe gegevensbron](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| Verificatie van beheerde identiteit voor opslag | Ja, met behulp van [beheerde service-identiteitsreferenties](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, `Managed Identity` met behulp van referenties. |
| Verificatie van de identiteit van opslagtoepassingen | [Ja](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| Machtigingen - Objectniveau | Ja, inclusief de mogelijkheid om machtigingen voor gebruikers te verlenen, TE WEIGEREN en in te trekken | Ja, inclusief de mogelijkheid om machtigingen te verlenen, TE WEIGEREN en IN TE TREKKEN voor gebruikers/aanmeldingen op de systeemobjecten die worden ondersteund |
| Machtigingen - Schemaniveau | Ja, inclusief de mogelijkheid om machtigingen te verlenen, TE WEIGEREN en IN te TREKKEN voor gebruikers/aanmeldingen op het schema | Ja, inclusief de mogelijkheid om machtigingen te verlenen, TE WEIGEREN en IN te TREKKEN voor gebruikers/aanmeldingen op het schema |
| Machtigingen - [Databaseniveau](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja | Ja |
| Machtigingen - [Serverniveau](/sql/relational-databases/security/authentication-access/server-level-roles) | Nee | Ja, sysadmin en andere serverrollen worden ondersteund |
| Rollen/groepen | Ja (database scoped) | Ja (zowel server als database scoped) |
| Beveiligingsidentiteitsfuncties &amp; | Sommige transact-SQL-beveiligingsfuncties `CURRENT_USER`en `HAS_DBACCESS` `IS_MEMBER`-operatoren: `SYSTEM_USER`, `USER` `USER_NAME`, `EXECUTE AS`, `IS_ROLEMEMBER` `SESSION_USER`, `SUSER_NAME` `SUSER_SNAME`, , ,`OPEN/CLOSE MASTER KEY` | Sommige transact-SQL-beveiligingsfuncties `CURRENT_USER`en -operatoren: `SUSER_NAME` `SUSER_SNAME`, `SYSTEM_USER` `USER`, `USER_NAME` `EXECUTE AS` `REVERT` `HAS_DBACCESS` `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER` `SESSION_USER`, , , , , , , en . Beveiligingsfuncties kunnen niet worden gebruikt om externe gegevens op te vragen (sla het resultaat op in variabele die in de query kan worden gebruikt).  |
| DATABASESCOPED-REFERENTIE | Ja | Ja |

SQL-groep en SQL on-demand gebruiken standaard Transact-SQL-taal om gegevens op te vragen. Voor gedetailleerde verschillen raadpleegt u de [transact-SQL-taalverwijzing.](/sql/t-sql/language-reference)

## <a name="tools"></a>Hulpprogramma's

U verschillende hulpprogramma's gebruiken om verbinding te maken met Synapse SQL om gegevens op te vragen.

|   | Ingericht | Serverloos |
| --- | --- | --- |
| Synapse Studio | Ja, SQL-scripts | Ja, SQL-scripts |
| Power BI | Ja | [Ja](tutorial-connect-power-bi-desktop.md) |
| Azure-analyseservice | Ja | Ja |
| Azure Data Studio | Ja | Ja, versie 1.14 of hoger. SQL-scripts en SQL-laptops worden ondersteund. |
| SQL Server Management Studio | Ja | Ja, versie 18.4 of hoger |

De meeste toepassingen gebruiken standaard Transact-SQL-taal kunnen zowel ingerichte als serverloze verbruiksmodellen van Synapse SQL opvragen.

## <a name="storage-options"></a>Opslagopties

Gegevens die worden geanalyseerd, kunnen worden opgeslagen op verschillende opslagtypen. In de volgende tabel worden alle beschikbare opslagopties weergegeven:

|   | Ingericht | Serverloos |
| --- | --- | --- |
| Interne opslag | Ja | Nee |
| Azure Data Lake v2 | Ja | Ja |
| Azure Blob Storage | Ja | Ja |

## <a name="data-formats"></a>Gegevensindelingen

Gegevens die worden geanalyseerd, kunnen in verschillende opslagformaten worden opgeslagen. In de volgende tabel worden alle beschikbare gegevensindelingen weergegeven die kunnen worden geanalyseerd:

|   | Ingericht | Serverloos |
| --- | --- | --- |
| Gescheiden | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja](query-single-csv-file.md) |
| CSV | Ja (scheidingstekens met meerdere tekens worden niet ondersteund) | [Ja](query-single-csv-file.md) |
| Parket | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja,](query-parquet-files.md)inclusief bestanden met [geneste typen](query-parquet-nested-types.md) |
| Bijenkorf ORC | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| Bijenkorf RC | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| JSON | Ja | [Ja](query-json-files.md) |
| [Delta-meer](https://delta.io/) | Nee | Nee |
| [Cdm](https://docs.microsoft.com/common-data-model/) | Nee | Nee |

## <a name="next-steps"></a>Volgende stappen
Aanvullende informatie over best practices voor SQL-pool en SQL on-demand vindt u in de volgende artikelen:

- [Aanbevolen procedures voor SQL-pool](best-practices-sql-pool.md)
- [Aanbevolen procedures voor SQL on-demand](best-practices-sql-on-demand.md)