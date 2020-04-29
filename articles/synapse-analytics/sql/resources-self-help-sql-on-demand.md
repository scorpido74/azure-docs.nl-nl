---
title: Zelf ondersteuning voor SQL op aanvraag
description: Deze sectie bevat informatie die u kan helpen bij het oplossen van problemen met SQL op aanvraag (preview).
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e2c262915c928cf487cb84aeb3423d67e7a96e97
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424830"
---
# <a name="self-help-for-sql-on-demand-preview"></a>Zelf ondersteuning voor SQL op aanvraag (preview)

Dit artikel bevat informatie over het oplossen van de meest voorkomende problemen met SQL op aanvraag (preview) in azure Synapse Analytics.

## <a name="sql-on-demand-is-grayed-out-in-synapse-studio"></a>SQL on-demand is niet beschikbaar in Synapse Studio

Als Synapse Studio geen verbinding kan maken met SQL op aanvraag, zult u merken dat SQL on-demand grijs wordt weer gegeven of dat de status ' offline ' is. Dit probleem treedt meestal op in een van de volgende situaties:

1) Uw netwerk voor komt communicatie met Azure Synapse back-end. Het meest voorkomende geval is dat poort 1443 wordt geblokkeerd. Als u de SQL on-demand wilt gebruiken voor het deblokkeren van deze poort. Andere problemen kunnen ertoe leiden dat SQL op aanvraag ook kan worden gebruikt. [Zie de volledige probleemoplossings gids voor meer informatie](../troubleshoot/troubleshoot-synapse-studio.md).
2) U bent niet gemachtigd om u aan te melden bij SQL op aanvraag. Om toegang te krijgen, moet een van de beheerders van de Azure Synapse-werk ruimte u toevoegen aan de rol werkruimte beheerder of SQL-beheerder. [Ga naar de volledige gids over toegangs beheer voor meer informatie](access-control.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>De query is mislukt omdat het bestand niet kan worden geopend

Als uw query mislukt met de fout melding dat het bestand niet kan worden geopend omdat het niet bestaat of door een ander proces wordt gebruikt, en u zeker weet dat beide bestanden bestaan en niet worden gebruikt door een ander proces, betekent SQL on-demand geen toegang tot het bestand. Dit probleem treedt meestal op omdat uw Azure Active Directory identiteit geen rechten heeft om het bestand te openen. SQL on demand probeert standaard toegang tot het bestand met uw Azure Active Directory-identiteit. Om dit probleem op te lossen, moet u de juiste rechten hebben voor toegang tot het bestand. De eenvoudigste manier is om uzelf de rol ' Storage BLOB data contributor ' toe te kennen voor het opslag account dat u wilt opvragen. [Ga naar de volledige hand leiding over Azure Active Directory toegangs beheer voor opslag voor meer informatie](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>De query is mislukt omdat deze niet kan worden uitgevoerd vanwege huidige resource beperkingen 

Als uw query mislukt met het fout bericht ' deze query kan niet worden uitgevoerd vanwege huidige resource beperkingen ', betekent dit dat SQL OD deze niet kan uitvoeren vanwege resource beperkingen: 

- Zorg ervoor dat de gegevens typen van redelijke grootten worden gebruikt. Geef ook schema op voor Parquet-bestanden voor teken reeks kolommen, aangezien deze standaard VARCHAR (8000) zijn. 

- Als uw query CSV-bestanden bedoelt, kunt u overwegen om [statistieken te maken](develop-tables-statistics.md#statistics-in-sql-on-demand-preview). 

- Ga naar [Aanbevolen prestatie procedures voor SQL op aanvraag](best-practices-sql-on-demand.md) om de query te optimaliseren.  

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van SQL op aanvraag:

- [Query uitvoeren op één CSV-bestand](query-single-csv-file.md)

- [Query mappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)

- [Query's uitvoeren op specifieke bestanden](query-specific-files.md)

- [Query uitvoeren op Parquet-bestanden](query-parquet-files.md)

- [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md)

- [Query uitvoeren op JSON-bestanden](query-json-files.md)

- [Weer gaven maken en gebruiken](create-use-views.md)

- [Externe tabellen maken en gebruiken](create-use-external-tables.md)

- [Queryresultaten opslaan in opslag](create-external-table-as-select.md)
