---
title: Gedeelde Azure Synapse Analytics-databases
description: Azure Synapse Analytics biedt een gedeeld metagegevensmodel. Wanneer u een database in Apache Spark maakt, is deze toegankelijk vanuit de SQL on-demand (preview-versie) en de SQL-poolengines.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: da1bd9c812c20f60264d1a5ee1f8821128900618
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698847"
---
# <a name="azure-synapse-analytics-shared-database"></a>Gedeelde Azure Synapse Analytics-databases

Met Azure Synapse Analytics kunnen de verschillende rekenengines voor de werkruimte databases en tabellen delen tussen de Spark-pools (preview-versie) en de SQL-on-demand engine (preview-versie).

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Een database die is gemaakt met een Spark-taak wordt weergegeven met dezelfde naam in alle huidige en toekomstige Spark-pools (preview-versie) in de werkruimte en in de SQL on-demand engine.

De standaard Spark-database met de naam `default` wordt ook weergegeven in de context van SQL on-demand met de naam `default`.

Omdat de databases asynchroon worden gesynchroniseerd naar SQL on-demand, is er een kleine vertraging voordat ze worden weergegeven.

## <a name="manage-a-spark-created-database"></a>Een met Spark gemaakte database beheren

Gebruik Spark voor het beheren van met Spark gemaakte databases. U kunt een database bijvoorbeeld verwijderen via een Spark-pooltaak en er tabellen in maken vanuit Spark.

Als u objecten in een met Spark gemaakte database maakt met SQL on-demand, of als u de database probeert te verwijderen, wordt de bewerking uitgevoerd. De oorspronkelijke Spark-database wordt echter niet gewijzigd.

## <a name="handling-of-name-conflicts"></a>Verwerking van naamconflicten

Als de naam van een Spark-database een conflict veroorzaakt met de naam van een bestaande SQL on-demand-database, wordt een achtervoegsel toegevoegd aan SQL on-demand in de Spark-database. Het achtervoegsel in SQL on-demand is `_<workspace name>-ondemand-DefaultSparkConnector`.

Als bijvoorbeeld een Spark-database met de naam `mydb` wordt gemaakt in de Azure Synapse-werk ruimte `myws` en er al een SQL on-demand-database met die naam bestaat, moet er naar de Spark-database in SQL on-demand worden verwezen met de naam `mydb_myws-ondemand-DefaultSparkConnector`.

> [!CAUTION]
> Let op: u mag geen afhankelijkheid opnemen van dit gedrag.

## <a name="security-model"></a>Beveiligingsmodel

De Spark-databases en -tabellen, gecombineerd met hun gesynchroniseerde weergaven in de SQL-engine, worden beveiligd op het onderliggende opslagniveau.

De beveiligingsprincipal die een database maakt, wordt als de eigenaar van die database beschouwd en beschikt over alle rechten voor de database en de bijbehorende objecten.

Als u een beveiligingsprincipal, zoals een gebruiker of een beveiligingsgroep, toegang tot een database wilt geven, geeft u de juiste POSIX-map en bestandsmachtigingen op voor de onderliggende mappen en bestanden in de map `warehouse`. 

Als u bijvoorbeeld wilt dat een beveiligingsprincipal een tabel in een database leest, moeten alle mappen die beginnen bij de databasemap in de map `warehouse` de machtigingen `X` en `R` hebben die aan die beveiligingsprincipal zijn toegewezen. Daarnaast zijn voor bestanden (zoals de onderliggende gegevensbestanden van de tabel) `R` machtigingen vereist. 

Als voor een beveiligingsprincipal is vereist dat objecten moeten kunnen worden gemaakt of verwijderd in een database, zijn aanvullende `W`-machtigingen vereist voor de mappen en bestanden in de map `warehouse`.

## <a name="examples"></a>Voorbeelden

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Een database maken en verbinden met Spark - SQL on-demand

Maak eerst een nieuwe Spark-database met de naam `mytestdb` met behulp van een Spark-cluster dat u al hebt gemaakt in uw werkruimte. U kunt bijvoorbeeld een Spark C#-notebook gebruiken met de volgende .NET for Spark-instructie:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Na een korte vertraging wordt de database in SQL on-demand weergegeven. Voer bijvoorbeeld de volgende instructie uit vanuit SQL on-demand.

```sql
SELECT * FROM sys.databases;
```

Controleer of `mytestdb` is opgenomen in de resultaten.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over gedeelde Azure Synapse Analytics-metagegevens](overview.md)
- [Meer informatie over gedeelde Azure Synapse Analytics-metagegevenstabellen](table.md)
