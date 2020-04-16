---
title: Gedeelde azure Synapse Analytics-database
description: Azure Synapse Analytics biedt een gedeeld metadatamodel waarbij het maken van een database in Apache Spark het toegankelijk maakt vanuit de SQL on-demand (preview) en SQL-poolengines.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424144"
---
# <a name="azure-synapse-analytics-shared-database"></a>Gedeelde azure Synapse Analytics-database

Azure Synapse Analytics stelt de verschillende enginen voor computationele werkruimtes in staat om databases en tabellen te delen tussen de Spark-pools (preview), SQL on-demand (preview)-engine en SQL-pools.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Een database die is gemaakt met een Spark-taak wordt zichtbaar met dezelfde naam voor alle huidige en toekomstige Spark-pools (preview) in de werkruimte en de SQL on-demand-engine.

Als er SQL-groepen in de werkruimte zijn die synchronisatie met metagegevens hebben ingeschakeld of als u een nieuwe SQL-groep maakt met de synchronisatie van metagegevens ingeschakeld, worden deze spark-databases automatisch toegewezen aan speciale schema's in de SQL-groepdatabase. 

Elk schema is vernoemd naar de `$` naam spark-database met een extra voorvoegsel. Zowel de externe als de beheerde tabellen in de spark-gegenereerde database worden weergegeven als externe tabellen in het bijbehorende speciale schema.

De Spark-standaarddatabase, die wordt aangeroepen, `default`is ook zichtbaar `default`in de SQL on-demand context als een database genaamd `$default`, en in een van de SQL-pooldatabases met synchronisatie van metagegevens ingeschakeld als het schema .

Aangezien de databases worden gesynchroniseerd met SQL on-demand en de SQL-pools asynchroon, zal er een vertraging totdat ze worden weergegeven.

## <a name="manage-a-spark-created-database"></a>Een spark-database beheren

Gebruik Spark om spark-databases te beheren. Verwijder deze bijvoorbeeld via een Spark-pooltaak en maak er tabellen in vanuit Spark.

Als u objecten maakt in een Spark-database met SQL on-demand of probeert de database te laten vallen, wordt de bewerking uitgevoerd. Maar de oorspronkelijke Spark-database wordt niet gewijzigd.

Als u het gesynchroniseerde schema in een SQL-groep probeert te laten vallen of een tabel probeert te maken, geeft Azure een fout als resultaat.

## <a name="handling-of-name-conflicts"></a>Afhandeling van naamconflicten

Als de naam van een Spark-database in strijd is met de naam van een bestaande SQL on-demand database, wordt een achtervoegsel toegevoegd in SQL on-demand aan de Spark-database. Het achtervoegsel in SQL `_<workspace name>-ondemand-DefaultSparkConnector`on-demand is .

Als er bijvoorbeeld een `mydb` Spark-database wordt aangeroepen die `myws` wordt gemaakt in de Azure Synapse-werkruimte en er al een SQL on-demand `mydb_myws-ondemand-DefaultSparkConnector`database met die naam bestaat, moet de Spark-database in SQL on-demand worden verwezen met de naam .

> [!CAUTION]
> Let op: U moet niet afhankelijk zijn van dit gedrag.

## <a name="security-model"></a>Beveiligingsmodel

De Spark-databases en -tabellen, samen met hun gesynchroniseerde representaties in de SQL-engines, worden beveiligd op het onderliggende opslagniveau.

De beveiligingsprincipal die een database maakt, wordt beschouwd als de eigenaar van die database en heeft alle rechten op de database en zijn objecten.

Als u een beveiligingsprincipal, zoals een gebruiker of een beveiligingsgroep, toegang wilt geven tot een database, moet `warehouse` u de juiste POSIX-map en bestandsmachtigingen geven aan de onderliggende mappen en bestanden in de map. 

Als een beveiligingsprincipal bijvoorbeeld een tabel in een database kan lezen, moeten alle `warehouse` mappen die `X` beginnen `R` met de databasemap in de map, en machtigingen die aan die beveiligingsprincipal zijn toegewezen, worden toegewezen. Bovendien hebben bestanden (zoals de onderliggende gegevensbestanden `R` van de tabel) machtigingen nodig. 

Als een beveiligingsprincipal de mogelijkheid vereist om objecten te `W` maken of objecten in een database `warehouse` neer te zetten, zijn aanvullende machtigingen vereist voor de mappen en bestanden in de map.

## <a name="examples"></a>Voorbeelden

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Maak & verbinding maken met Spark-database - SQL on-demand

Maak eerst een nieuwe `mytestdb` Spark-database met de naam met een Spark-cluster dat u al in uw werkruimte hebt gemaakt. U dat bijvoorbeeld bereiken met behulp van een Spark C#-notitieblok met de volgende .NET voor Spark-instructie:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Na een korte vertraging u de database zien vanuit SQL on-demand. Voer bijvoorbeeld de volgende instructie uit SQL on-demand uit.

```sql
SELECT * FROM sys.databases;
```

Controleer `mytestdb` of dit in de resultaten is opgenomen.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>Een Spark-database blootstellen in een SQL-groep

Met de database die in het vorige voorbeeld is `mysqlpool` gemaakt, maakt u nu een SQL-groep in uw werkruimte met de naam met de naam metagegevenssynchronisatie.

Voer de volgende `mysqlpool` instructie uit tegen de SQL-groep:

```sql
SELECT * FROM sys.schema;
```

Controleer het schema voor de nieuw gemaakte database in de resultaten.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over de gedeelde metadata van Azure Synapse Analytics](overview.md)
- [Meer informatie over de gedeelde metadatatabellen van Azure Synapse Analytics](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->
