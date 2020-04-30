---
title: Gedeeld meta gegevens model voor Azure Synapse Analytics
description: Met Azure Synapse Analytics kunnen de verschillende reken kundige engines voor werk ruimten data bases en tabellen uitwisselen tussen de Spark-Pools (preview), de SQL-engine op aanvraag (preview) en SQL-groepen.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: 3b26d516080961a482a3ba67f314e98ece4c9f24
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424116"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Gedeelde Azure Synapse Analytics-meta gegevens

Met Azure Synapse Analytics kunnen de verschillende reken kundige engines voor werk ruimten data bases en tabellen uitwisselen tussen de Spark-Pools (preview), de SQL-engine op aanvraag (preview) en SQL-groepen.

[!INCLUDE [preview](../includes/note-preview.md)]



Het delen ondersteunt het zogenaamde moderne Data Warehouse-patroon en geeft de werk ruimte-SQL-engines toegang tot data bases en tabellen die zijn gemaakt met Spark. Ook kunnen de SQL-engines hun eigen objecten maken die niet worden gedeeld met de andere engines.

## <a name="support-the-modern-data-warehouse"></a>Ondersteuning voor het moderne Data Warehouse

Het gedeelde meta gegevens model ondersteunt het moderne Data Warehouse patroon op de volgende manier:

1. Gegevens van de data Lake worden op efficiënte wijze voor bereid en structureeerd met Spark door de voor bereide gegevens op te slaan in (mogelijk gepartitioneerde) Parquet-back tabellen die zijn opgenomen in mogelijk verschillende data bases.

2. De Spark-data bases en alle bijbehorende tabellen worden zichtbaar in een van de opslag ruimten van de Azure Synapse-werk ruimte en kunnen worden gebruikt vanuit een van de Spark-taken. Deze functionaliteit is onderhevig aan de [machtigingen](#security-model-at-a-glance) omdat alle Spark-Pools in een werk ruimte dezelfde onderliggende meta gegevens voor de catalogus hebben.

3. De door Spark gemaakte data bases en de tabellen met Parquet-back-ups worden zichtbaar in de werk ruimte SQL on-demand engine. [Data bases](database.md) worden automatisch gemaakt in de SQL on-demand-meta gegevens en zowel de [externe als de beheerde tabellen](table.md) die zijn gemaakt door een Spark-taak, worden toegankelijk gemaakt als externe tabellen in `dbo` de SQL on-demand meta gegevens in het schema van de bijbehorende data base. <!--For more details, see [ADD LINK].-->

4. Als er exemplaren van een SQL-groep aanwezig zijn in de werk ruimte waarvoor de synchronisatie van meta gegevens is ingeschakeld <!--[ADD LINK]--> of als er een nieuw exemplaar van de SQL-groep is gemaakt met de synchronisatie van meta gegevens, worden de Spark gemaakte data bases en de tabellen met Parquet-back-ups automatisch toegewezen in de SQL-groeps database, zoals beschreven in de [gedeelde Azure-Data Base voor Synapse Analytics](database.md).

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Object synchronisatie vindt asynchroon plaats. Objecten hebben een lichte vertraging van enkele seconden tot ze worden weer gegeven in de SQL-context. Zodra ze worden weer gegeven, kunnen ze worden opgevraagd, maar niet worden bijgewerkt of gewijzigd door de SQL-engines die toegang hebben.

## <a name="which-metadata-objects-are-shared"></a>Welke meta gegevens objecten worden gedeeld

Met Spark kunt u data bases, externe tabellen, beheerde tabellen en weer gaven maken. Aangezien Spark-weer gaven een Spark-Engine nodig hebben voor het verwerken van de definiërende Spark SQL-instructie en deze niet kan worden verwerkt door een SQL-engine, worden alleen data bases en de bijbehorende externe en beheerde tabellen die gebruikmaken van de Parquet-opslag indeling gedeeld met de werk ruimte-SQL-engines. Spark-weer gaven worden alleen gedeeld tussen instanties van de Spark-groep.

## <a name="security-model-at-a-glance"></a>Beveiligings model in één oogopslag

De Spark-data bases en tabellen, samen met hun gesynchroniseerde representaties in de SQL-engines, worden beveiligd op het onderliggende opslag niveau. Wanneer de tabel wordt doorzocht op een van de engines die de query indiener het recht heeft om te gebruiken, wordt de beveiligingsprincipal van de query indiener door gegeven aan de onderliggende bestanden. De machtigingen worden op bestandssysteem niveau gecontroleerd.

Zie voor meer informatie [Azure Synapse Analytics gedeelde data base](database.md).

## <a name="change-maintenance"></a>Onderhoud wijzigen

Als een meta gegevens object wordt verwijderd of gewijzigd met Spark, worden de wijzigingen opgehaald en door gegeven aan de SQL on-demand-engine en de SQL-groepen waarvan de objecten zijn gesynchroniseerd. Synchronisatie is asynchroon en wijzigingen worden weer gegeven in de SQL-engines na een korte vertraging.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over gedeelde meta gegevens databases van Azure Synapse Analytics](database.md)
- [Meer informatie over gedeelde meta gegevens tabellen van Azure Synapse Analytics](table.md)

