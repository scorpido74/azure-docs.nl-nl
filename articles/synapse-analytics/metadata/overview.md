---
title: Azure Synapse Analytics gedeeld metadatamodel
description: Azure Synapse Analytics stelt de verschillende computerengines voor werkruimtes in staat om databases en tabellen te delen tussen de Spark-pools (preview), SQL on-demand engine (preview) en SQL-pools.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 3b26d516080961a482a3ba67f314e98ece4c9f24
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424116"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Azure Synapse Analytics gedeelde metagegevens

Azure Synapse Analytics stelt de verschillende computerengines voor werkruimtes in staat om databases en tabellen te delen tussen de Spark-pools (preview), SQL on-demand engine (preview) en SQL-pools.

[!INCLUDE [preview](../includes/note-preview.md)]



Het delen ondersteunt het zogenaamde moderne datawarehousepatroon en geeft de workspace SQL-engines toegang tot databases en tabellen die met Spark zijn gemaakt. Het maakt het ook mogelijk de SQL-engines om hun eigen objecten die niet worden gedeeld met de andere motoren te maken.

## <a name="support-the-modern-data-warehouse"></a>Ondersteuning voor het moderne datawarehouse

Het gedeelde metadatamodel ondersteunt het moderne datawarehousepatroon op de volgende manier:

1. Gegevens uit het datalake worden efficiënt voorbereid en gestructureerd met Spark door de voorbereide gegevens op te slaan in (eventueel verdeelde) parquet-backed tabellen in mogelijk meerdere databases.

2. De spark-databases en al hun tabellen worden zichtbaar in een van de Spark-poolinstanties voor Azure Synapse-werkruimte en kunnen worden gebruikt vanuit een van de Spark-taken. Deze mogelijkheid is afhankelijk van de [machtigingen](#security-model-at-a-glance) omdat alle Spark-pools in een werkruimte dezelfde onderliggende catalogusmetawinkel delen.

3. De spark-databases en hun parquet-backed tabellen worden zichtbaar in de workspace SQL on-demand engine. [Databases](database.md) worden automatisch gemaakt in de SQL on-demand metadata en zowel de [externe als beheerde tabellen](table.md) die door een `dbo` Spark-taak zijn gemaakt, worden toegankelijk gemaakt als externe tabellen in de SQL on-demand metadata in het schema van de bijbehorende database. <!--For more details, see [ADD LINK].-->

4. Als er SQL-poolexemplaren in de werkruimte zijn die de synchronisatie van metagegevens hebben ingeschakeld <!--[ADD LINK]--> of als een nieuwe SQL-poolinstantie wordt gemaakt met de synchronisatie van metagegevens ingeschakeld, worden de spark-databases en hun door een parket gesteunde tabellen automatisch toegewezen in de SQL-pooldatabase zoals beschreven in de [gedeelde database van Azure Synapse Analytics.](database.md)

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Objectsynchronisatie vindt asynchroon plaats. Objecten hebben een lichte vertraging van enkele seconden totdat ze in de SQL-context worden weergegeven. Zodra ze worden weergegeven, kunnen ze worden opgevraagd, maar niet bijgewerkt of gewijzigd door de SQL-engines die toegang hebben tot hen.

## <a name="which-metadata-objects-are-shared"></a>Welke metagegevensobjecten worden gedeeld

Met Spark u databases, externe tabellen, beheerde tabellen en weergaven maken. Aangezien Spark-weergaven een Spark-engine vereisen om de definiërende Spark SQL-instructie te verwerken en niet kunnen worden verwerkt door een SQL-engine, worden alleen databases en hun opgenomen externe en beheerde tabellen die de parquet-opslagindeling gebruiken, gedeeld met de SQL-engine van de werkruimte. Spark-weergaven worden alleen gedeeld tussen de spark-poolexemplaren.

## <a name="security-model-at-a-glance"></a>Beveiligingsmodel in één oogopslag

De Spark-databases en -tabellen, samen met hun gesynchroniseerde representaties in de SQL-engines, zijn beveiligd op het onderliggende opslagniveau. Wanneer de tabel wordt opgevraagd door een van de engines die de queryindiener het recht heeft te gebruiken, wordt de beveiligingsprincipal van de queryindiener doorgegeven aan de onderliggende bestanden. Machtigingen worden gecontroleerd op bestandssysteemniveau.

Zie [Azure Synapse Analytics shared database](database.md)voor meer informatie.

## <a name="change-maintenance"></a>Onderhoud wijzigen

Als een metagegevensobject wordt verwijderd of gewijzigd met Spark, worden de wijzigingen opgepikt en gepropageerd naar de SQL on-demand-engine en de SQL-pools waarop de objecten zijn gesynchroniseerd. Synchronisatie is asynchroon en wijzigingen worden na een korte vertraging weerspiegeld in de SQL-engines.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over de gedeelde metadatadatabases van Azure Synapse Analytics](database.md)
- [Meer informatie over de gedeelde metadatatabellen van Azure Synapse Analytics](table.md)

