---
title: Overzicht van Azure Data Factory-connector
description: Meer informatie over de ondersteunde connectors in Data Factory.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/08/2020
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: d7a872121ca6560b8ede86abc35294ab8c9b0c1b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142020"
---
# <a name="azure-data-factory-connector-overview"></a>Overzicht van Azure Data Factory-connector

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory ondersteunen de volgende gegevens archieven en-indelingen via kopiëren, gegevens stroom, opzoeken, meta gegevens ophalen en activiteiten verwijderen. Klik op elk gegevens Archief voor meer informatie over de ondersteunde mogelijkheden en de bijbehorende configuraties.

## <a name="supported-data-stores"></a>Ondersteunde gegevensarchieven

[!INCLUDE [Connector overview](../../includes/data-factory-v2-connector-overview.md)]

## <a name="supported-file-formats"></a>Ondersteunde bestandsindelingen

Azure Data Factory ondersteunt de volgende bestands indelingen. Raadpleeg elk artikel voor instellingen op basis van een indeling.

- [Avro-indeling](format-avro.md)
- [Binaire indeling](format-binary.md)
- [Common Data Model-indeling](format-common-data-model.md)
- [Tekstindeling met scheidingstekens](format-delimited-text.md)
- [Excel-indeling](format-excel.md)
- [JSON-indeling](format-json.md)
- [ORC-indeling](format-orc.md)
- [Parquet-indeling](format-parquet.md)

## <a name="next-steps"></a>Volgende stappen

- [Kopieeractiviteit](copy-activity-overview.md)
- [Toewijzingsgegevensstroom](concepts-data-flow-overview.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Activiteit verwijderen](delete-activity.md)
