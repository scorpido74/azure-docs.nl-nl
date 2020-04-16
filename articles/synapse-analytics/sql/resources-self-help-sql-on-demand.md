---
title: SQL on-demand Preview) zelfhulp
description: Deze sectie bevat informatie waarmee u problemen met SQL on-demand (preview) oplossen.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424830"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Zelfhulp voor SQL on-demand (preview)

Dit artikel bevat informatie over het oplossen van de meest voorkomende problemen met SQL on-demand (preview) in Azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>SQL on-demand wordt grijs weergegeven in Synapse Studio

Als Synapse Studio geen verbinding kan maken met SQL on-demand, zult u merken dat SQL on-demand grijs wordt weergegeven of de status 'Offline' wordt weergegeven. Meestal treedt dit probleem op wanneer een van de volgende gevallen zich voordoet:

1) Uw netwerk voorkomt communicatie met Azure Synapse backend. Het meest voorkomende geval is dat poort 1443 is geblokkeerd. Om de SQL on-demand te laten werken, deblokkeer deze poort. Andere problemen kunnen sql on-demand voorkomen om ook te werken, [bezoek de volledige handleiding voor probleemoplossing voor meer informatie.](../troubleshoot/troubleshoot-synapse-studio.md)
2) U hebt geen machtigingen om u aan te melden bij SQL on-demand. Om toegang te krijgen, moet een van de beheerders van Azure Synapse-werkruimtes u toevoegen aan de beheerder van de werkruimte of de SQL-beheerdersrol. [Bezoek de volledige gids over toegangscontrole voor meer informatie.](access-control.md)

## <a name="query-fails-because-file-cannot-be-opened"></a>Query mislukt omdat bestand niet kan worden geopend

Als uw query mislukt met de fout met de tekst 'Bestand kan niet worden geopend omdat het niet bestaat of het wordt gebruikt door een ander proces' en u weet zeker dat beide bestanden bestaan en het wordt niet gebruikt door een ander proces betekent sql on-demand geen toegang tot het bestand. Dit probleem treedt meestal op omdat uw Azure Active Directory-identiteit geen rechten heeft om toegang te krijgen tot het bestand. SQL on-demand probeert standaard toegang te krijgen tot het bestand met uw Azure Active Directory-identiteit. Om dit probleem op te lossen, moet u de juiste rechten hebben om toegang te krijgen tot het bestand. De eenvoudigste manier is om jezelf de rol 'Storage Blob Data Contributor' te geven op het opslagaccount dat u probeert op te vragen. [Ga naar de volledige handleiding voor Azure Active Directory-toegangsbeheer voor opslag voor meer informatie.](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Query mislukt omdat deze niet kan worden uitgevoerd vanwege de huidige resourcebeperkingen 

Als uw query mislukt met het foutbericht 'Deze query kan niet worden uitgevoerd vanwege de huidige resourcebeperkingen', betekent dit dat SQL OD deze op dit moment niet kan uitvoeren vanwege resourcebeperkingen: 

- Zorg ervoor dat gegevenssoorten van redelijke grootte worden gebruikt. Geef ook schema op voor parketbestanden voor tekenreekskolommen, omdat ze standaard VARCHAR(8000) zijn. 

- Als uw query is gericht op CSV-bestanden, u overwegen [statistieken te maken.](develop-tables-statistics.md#statistics-in-sql-on-demand-preview) 

- Bezoek [best practices voor prestaties voor SQL on-demand](best-practices-sql-on-demand.md) om query's te optimaliseren.  

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende artikelen voor meer informatie over het gebruik van SQL on-demand:

- [Query enkel CSV-bestand](query-single-csv-file.md)

- [Querymappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)

- [Specifieke bestanden opvragen](query-specific-files.md)

- [Parketbestanden voor query's](query-parquet-files.md)

- [Geneste querygenese](query-parquet-nested-types.md)

- [JSON-bestanden opvragen](query-json-files.md)

- [Weergaven maken en gebruiken](create-use-views.md)

- [Externe tabellen maken en gebruiken](create-use-external-tables.md)

- [Queryresultaten opslaan in opslag](create-external-table-as-select.md)
