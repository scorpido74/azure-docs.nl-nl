---
title: Overzicht van Azure Data Factory-connector
description: Meer informatie over de ondersteunde connectors in Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 334d5b5113dba17c5abc2b4f2520bde0d16e4c06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87007437"
---
# <a name="azure-data-factory-connector-overview"></a>Overzicht van Azure Data Factory-connector

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory ondersteunt de volgende gegevens archieven en-indelingen via kopiëren, gegevens stroom, zoeken, meta gegevens ophalen en activiteiten verwijderen. Klik op elk gegevens Archief voor meer informatie over de ondersteunde mogelijkheden en de bijbehorende configuraties.

## <a name="supported-data-stores"></a>Ondersteunde gegevensarchieven

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen

Azure Data Factory ondersteunt de volgende bestands indelingen. Raadpleeg elk artikel voor instellingen op basis van een indeling.

- [Avro-indeling](format-avro.md)
- [Binaire indeling](format-binary.md)
- [Common Data Model-indeling](format-common-data-model.md)
- [Tekstindeling met scheidingstekens](format-delimited-text.md)
- [Delta-indeling](format-delta.md)
- [Excel-indeling](format-excel.md)
- [JSON-indeling](format-json.md)
- [ORC-indeling](format-orc.md)
- [Parquet-indeling](format-parquet.md)
- [XML-indeling](format-xml.md)

## <a name="next-steps"></a>Volgende stappen

- [Kopieeractiviteit](copy-activity-overview.md)
- [Toewijzingsgegevensstroom](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)
