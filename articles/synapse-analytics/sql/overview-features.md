---
title: Verschillen in functies voor T-SQL in azure Synapse SQL
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424893"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Transact-SQL-functies die worden ondersteund in azure Synapse SQL

Azure Synapse SQL is een big data analyse service waarmee u uw gegevens kunt opvragen en analyseren met behulp van de T-SQL-taal. U kunt een standaard ANSI-compatibel dialect gebruiken van SQL-taal die wordt gebruikt op SQL Server en Azure SQL Database voor gegevens analyse. 

De Transact-SQL-taal wordt gebruikt in Synapse SQL serverloze en ingerichte model kunnen naar verschillende objecten verwijzen en heeft een aantal verschillen in de set met ondersteunde functies. Op deze pagina vindt u de verschillen tussen de Transact-SQL-taal op het hoogste niveau tussen verbruiks modellen van Synapse SQL.

## <a name="database-objects"></a>Database objecten

Met verbruiks modellen in Synapse SQL kunt u verschillende database objecten gebruiken. De vergelijking van ondersteunde object typen wordt weer gegeven in de volgende tabel:

|   | Ingericht | Serverloos |
| --- | --- | --- |
| Tabellen | [Ja](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee, serverloos model kan alleen query's uitvoeren op externe gegevens die op [Azure Storage](#storage-options) zijn geplaatst |
| Weergaven | [Ja](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Weer gaven kunnen [query taal elementen](#query-language) gebruiken die beschikbaar zijn in het ingerichte model. | [Ja](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Weer gaven kunnen [query taal elementen](#query-language) gebruiken die beschikbaar zijn in serverloze modellen. |
| Schema 's | [Ja](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Tijdelijke tabellen | [Ja](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nee |
| Procedures | [Ja](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| Functions | [Ja](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| Triggers | Nee | Nee |
| Externe tabellen | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Zie ondersteunde [notaties voor gegevens](#data-formats). | [Ja](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Zie ondersteunde [notaties voor gegevens](#data-formats). |
| Cache query's | Ja, meerdere formulieren (op SSD gebaseerde caching, in-Memory, resultaatset cache). Daarnaast worden gerealiseerde weer gaven ondersteund | Nee |
| Tabel variabelen | [Nee](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), tijdelijke tabellen gebruiken | Nee |

## <a name="query-language"></a>Querytaal

Query talen die worden gebruikt in Synapse SQL kunnen verschillende ondersteunde functies hebben, afhankelijk van het verbruiks model. De volgende tabel bevat een overzicht van de belangrijkste verschillen in de query taal in Transact-SQL-dialecten:

|   | Ingericht | Serverloos |
| --- | --- | --- |
| Instructie selecteren | Ja. Transact-SQL-query componenten [voor XML/voor json](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [overeenkomst](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en voor [spel](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) worden niet ondersteund. | Ja. Transact-SQL [-query-componenten voor XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [overeenkomst](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), voors [pellen](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en query hints worden niet ondersteund. [Verschuiving/ophalen](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) en [draai tabel/draai tabel](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) kunnen alleen worden gebruikt om gegevens in tijdelijke tabellen (niet externe gegevens) op te vragen. |
| Instructie invoegen | Ja | Nee |
| UPDATE-instructie | Ja | Nee |
| Instructie verwijderen | Ja | Nee |
| Instructie MERGe | Ja | Nee |
| Gegevens laden | Ja. Het voorkeurs hulpprogramma is een [Kopieer](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) instructie, maar het systeem ondersteunt zowel bulksgewijs laden (BCP) als [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor het laden van gegevens. | Nee |
| Gegevensexport | Ja. [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)gebruiken. | Ja. [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)gebruiken. |
| Typen | Ja, alle Transact-SQL-typen behalve [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, Text en image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ruimtelijke types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [SQL\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alle Transact-SQL-typen behalve [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, Text en image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ruimtelijke types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [SQL\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en tabel type |
| Query’s tussen databases | Nee | Ja, inclusief [use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) -instructie. |
| Ingebouwde functies (analyse) | Ja, alle Transact-SQL- [analyse](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), conversie, [datum en tijd](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logische, [wiskundige](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) functies, behalve [kiezen](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [IIf](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [parseren](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alle Transact-SQL- [analyse](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), conversie, [datum en tijd](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logische, [wiskundige](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) functies. |
| Ingebouwde functies (tekst) | Ja. Alle Transact-SQL- [teken reeks](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-, [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-en sorterings functies, behalve [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en [vertalen](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. Alle Transact-SQL- [teken reeks](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-, [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-en collatie functies. |
| Ingebouwde functies voor tabel waarden | Ja, [Transact-SQL Rowset-functies](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), behalve [OpenXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [open query](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [OpenRowSet](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, [Transact-SQL Rowset-functies](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), behalve [OpenXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OpenDataSource](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [open query](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Aggregaties |  Ingebouwde aggregaties van Transact-SQL, behalve [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ingebouwde aggregaties van Transact-SQL, behalve [String\_agg](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Operators | Ja, alle [Transact-SQL-Opera tors](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , behalve [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, alle [Transact-SQL-Opera tors](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Controle van de stroom | Ja. Alle [Transact-SQL-instructie Control-of-flow](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , behalve [door gaan](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [goto](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [return](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)en [waitfor](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. Alle [Transact-SQL-instructie Control-of-flow](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , behalve [retour](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en Select `WHILE (...)` -query in voor waarde |
| DDL-instructies (maken, wijzigen, NEERZETten) | Ja. Alle Transact-SQL DDL-instructies die van toepassing zijn op de ondersteunde object typen | Ja. Alle Transact-SQL DDL-instructies die van toepassing zijn op de ondersteunde object typen |

## <a name="security"></a>Beveiliging

Met Synapse SQL kunt u ingebouwde beveiligings functies gebruiken om uw gegevens te beveiligen en de toegang te beheren. In de volgende tabel worden de verschillen op hoog niveau vergeleken tussen de Synapse-modellen van het SQL-verbruik.

|   | Ingericht | Serverloos |
| --- | --- | --- |
| Aanmeldingen | N.v.t. (alleen opgenomen gebruikers worden ondersteund in data bases) | Ja |
| Gebruikers |  N.v.t. (alleen opgenomen gebruikers worden ondersteund in data bases) | Ja |
| [Ingesloten gebruikers](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja. **Opmerking:** slechts één Aad-gebruiker kan een onbeperkte beheerder zijn | Ja |
| Azure Active Directory-verificatie (AAD) | Ja, AAD-gebruikers | Ja, AAD-aanmeldingen en gebruikers |
| Verificatie AAD passthrough voor opslag | Ja | Ja |
| Verificatie van SAS-token voor opslag | Nee | Ja, met behulp van [Data Base-bereik referenties](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) in [externe gegevens bron](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) of [referentie](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)op exemplaar niveau. |
| Verificatie van de toegangs sleutel voor opslag | Ja, met [Data Base-scoped referentie](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) in [externe gegevens bron](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| Door opslag beheerde identiteits verificatie | Ja, met [Managed Service Identity referentie](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja, met `Managed Identity` behulp van referenties. |
| Verificatie van de opslag toepassings-id | [Ja](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| Machtigingen-object niveau | Ja, inclusief de mogelijkheid om machtigingen aan gebruikers toe te kennen, te weigeren en in te trekken | Ja, inclusief de mogelijkheid om machtigingen te verlenen, te weigeren en in te trekken voor gebruikers/aanmeldingen op de systeem objecten die worden ondersteund |
| Machtigingen-schema niveau | Ja, inclusief de mogelijkheid om machtigingen voor gebruikers/aanmeldingen in het schema toe te kennen, te weigeren en in te trekken | Ja, inclusief de mogelijkheid om machtigingen voor gebruikers/aanmeldingen in het schema toe te kennen, te weigeren en in te trekken |
| Machtigingen- [database niveau](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Ja | Ja |
| Machtigingen- [server niveau](/sql/relational-databases/security/authentication-access/server-level-roles) | Nee | Ja, sysadmin en andere server rollen worden ondersteund |
| Rollen/groepen | Ja (data base-bereik) | Ja (zowel server-als database bereik) |
| Beveiligings &amp; identiteits functies | Enkele Transact-SQL-beveiligings functies en- `CURRENT_USER`Opera `HAS_DBACCESS`tors `IS_MEMBER`: `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`,,,,`OPEN/CLOSE MASTER KEY` | Enkele Transact-SQL-beveiligings functies en- `CURRENT_USER`Opera `HAS_DBACCESS`tors `HAS_PERMS_BY_NAME`: `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER` `USER_NAME` `EXECUTE AS`,,,, `REVERT`, en. Beveiligings functies kunnen niet worden gebruikt om externe gegevens op te vragen (Sla het resultaat op in een variabele die in de query kan worden gebruikt).  |
| DATA BASE-SCOPED REFERENTIE | Ja | Ja |

SQL-groep en SQL op aanvraag gebruiken de standaard Transact-SQL-taal om gegevens op te vragen. Zie de [Naslag informatie voor de Transact-SQL-taal](/sql/t-sql/language-reference)voor gedetailleerde verschillen.

## <a name="tools"></a>Hulpprogramma's

U kunt verschillende hulpprogram ma's gebruiken om verbinding te maken met Synapse SQL om gegevens op te vragen.

|   | Ingericht | Serverloos |
| --- | --- | --- |
| Synapse Studio | Ja, SQL-scripts | Ja, SQL-scripts |
| Power BI | Ja | [Ja](tutorial-connect-power-bi-desktop.md) |
| Azure Analysis Service | Ja | Ja |
| Azure Data Studio | Ja | Ja, versie 1,14 of hoger. SQL-scripts en SQL-notitie blokken worden ondersteund. |
| SQL Server Management Studio | Ja | Ja, versie 18,4 of hoger |

De meeste toepassingen gebruiken de standaard Transact-SQL-taal om zowel ingerichte als serverloze verbruiks modellen van Synapse SQL op te vragen.

## <a name="storage-options"></a>Opslagopties

Gegevens die worden geanalyseerd, kunnen worden opgeslagen op verschillende opslag typen. De volgende tabel geeft een lijst van alle beschik bare opslag opties:

|   | Ingericht | Serverloos |
| --- | --- | --- |
| Interne opslag | Ja | Nee |
| Azure Data Lake v2 | Ja | Ja |
| Azure Blob Storage | Ja | Ja |

## <a name="data-formats"></a>Gegevens indelingen

Gegevens die worden geanalyseerd, kunnen worden opgeslagen in verschillende opslag indelingen. De volgende tabel geeft een lijst van alle beschik bare gegevens indelingen die kunnen worden geanalyseerd:

|   | Ingericht | Serverloos |
| --- | --- | --- |
| Gescheiden | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja](query-single-csv-file.md) |
| CSV | Ja (scheidings tekens voor meerdere karakters worden niet ondersteund) | [Ja](query-single-csv-file.md) |
| Parquet | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Ja](query-parquet-files.md), inclusief bestanden met [geneste typen](query-parquet-nested-types.md) |
| Hive ORC | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| Hive RC | [Ja](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nee |
| JSON | Ja | [Ja](query-json-files.md) |
| [Delta-Lake](https://delta.io/) | Nee | Nee |
| [CDM](https://docs.microsoft.com/common-data-model/) | Nee | Nee |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over best practices voor SQL-pool en SQL on-demand vindt u in de volgende artikelen:

- [Aanbevolen procedures voor SQL-groep](best-practices-sql-pool.md)
- [Aanbevolen procedures voor SQL op aanvraag](best-practices-sql-on-demand.md)