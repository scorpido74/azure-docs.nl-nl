---
title: Gedeelde Azure Synapse Analytics-Data Base
description: Azure Synapse Analytics biedt een gemeen schappelijk meta gegevens model waarmee u een data base in Apache Spark maakt, toegankelijk is vanuit de SQL-service on-demand (preview) en de SQL-groeps engines.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424144"
---
# <a name="azure-synapse-analytics-shared-database"></a>Gedeelde Azure Synapse Analytics-Data Base

Met Azure Synapse Analytics kunt u de verschillende reken kundige werk ruimte-engines gebruiken om data bases en tabellen te delen tussen hun Spark-Pools (preview), de SQL on-demand-Engine (preview) en SQL-groepen.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

Een Data Base die is gemaakt met een Spark-taak wordt weer gegeven met dezelfde naam voor alle huidige en toekomstige Spark-Pools (preview) in de werk ruimte en de SQL on-demand-engine.

Als er SQL-groepen in de werk ruimte waarvoor synchronisatie van meta gegevens is ingeschakeld, of als u een nieuwe SQL-groep maakt terwijl de synchronisatie van meta gegevens is ingeschakeld, worden deze Spark-data bases automatisch toegewezen in speciale schema's in de SQL-groeps database. 

Elk schema krijgt de naam van de Spark-data base met `$` een extra voor voegsel. De externe en beheerde tabellen in de door Spark gegenereerde data base worden weer gegeven als externe tabellen in het bijbehorende speciale schema.

De Spark-standaard database, `default`die wordt aangeroepen, wordt ook weer gegeven in de SQL on-demand-context `default`als een Data Base met de naam en in de data BASEs van de SQL- `$default`groep met synchronisatie van meta gegevens ingeschakeld als het schema.

Omdat de data bases asynchroon worden gesynchroniseerd met SQL on-demand en de SQL-groepen, wordt er een vertraging weer gegeven totdat deze zich voordoen.

## <a name="manage-a-spark-created-database"></a>Een door Spark gemaakte data base beheren

Gebruik Spark voor het beheren van Spark gemaakte data bases. U kunt dit bijvoorbeeld verwijderen via een Spark-pool taak en er tabellen in maken vanuit Spark.

Als u objecten in een met Spark gemaakte data base maakt met behulp van SQL op aanvraag, of als u probeert de data base te verwijderen, wordt de bewerking uitgevoerd. Maar de oorspronkelijke Spark-data base wordt niet gewijzigd.

Als u probeert het gesynchroniseerde schema in een SQL-groep te verwijderen of een tabel erin te maken, wordt een fout geretourneerd door Azure.

## <a name="handling-of-name-conflicts"></a>Verwerking van naam conflicten

Als de naam van een Spark-Data Base een conflict veroorzaakt met de naam van een bestaande SQL on-demand-data base, wordt een achtervoegsel toegevoegd aan SQL op aanvraag in de Spark-data base. Het achtervoegsel in SQL op aanvraag is `_<workspace name>-ondemand-DefaultSparkConnector`.

Als bijvoorbeeld een Spark-data base met `mydb` de naam wordt gemaakt in de Azure `myws` Synapse-werk ruimte en een SQL on-demand-data base met die naam al bestaat, moet er naar de Spark-data base in SQL op aanvraag `mydb_myws-ondemand-DefaultSparkConnector`worden verwezen met behulp van de naam.

> [!CAUTION]
> Let op: u mag geen afhankelijkheid nemen van dit gedrag.

## <a name="security-model"></a>Beveiligingsmodel

De Spark-data bases en tabellen, samen met hun gesynchroniseerde representaties in de SQL-engines, worden beveiligd op het onderliggende opslag niveau.

De beveiligingsprincipal die een Data Base maakt, wordt beschouwd als de eigenaar van de data base en heeft alle rechten voor de data base en de bijbehorende objecten.

Als u een beveiligingsprincipal, zoals een gebruiker of een beveiligings groep, toegang tot een Data Base wilt geven, geeft u de juiste POSIX-map en bestands machtigingen op voor de onderliggende mappen `warehouse` en bestanden in de map. 

Als er bijvoorbeeld een beveiligingsprincipal is om een tabel in een Data Base te lezen, moeten alle mappen die beginnen bij de databasemap in de `warehouse` Directory, beschikken over `X` de `R` machtigingen die zijn toegewezen aan die beveiligingsprincipal. Daarnaast zijn er machtigingen vereist `R` voor bestanden (zoals de onderliggende gegevens bestanden van de tabel). 

Als een beveiligings-principal de mogelijkheid heeft om objecten te maken of objecten in een Data Base `W` te verwijderen, zijn er aanvullende machtigingen vereist voor de `warehouse` mappen en bestanden in de map.

## <a name="examples"></a>Voorbeelden

### <a name="create--connect-to-spark-database---sql-on-demand"></a>& verbinding maken met Spark-data base-SQL op aanvraag

Maak eerst een nieuwe Spark-data `mytestdb` Base met de naam met behulp van een Spark-cluster dat u al hebt gemaakt in uw werk ruimte. U kunt bijvoorbeeld een Spark C#-notebook met de volgende .NET for Spark-instructie gebruiken:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Na een korte vertraging kunt u de data base van SQL op aanvraag bekijken. Voer bijvoorbeeld de volgende instructie uit op SQL op aanvraag.

```sql
SELECT * FROM sys.databases;
```

Controleer of `mytestdb` het is opgenomen in de resultaten.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>Een Spark-data base in een SQL-groep weer geven

Met de data base die in het vorige voor beeld is gemaakt, maakt u nu een SQL `mysqlpool` -groep in uw werk ruimte met de naam waarmee synchronisatie van meta gegevens mogelijk is.

Voer de volgende instructie uit op `mysqlpool` de SQL-groep:

```sql
SELECT * FROM sys.schema;
```

Controleer het schema voor de zojuist gemaakte data base in de resultaten.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over gedeelde meta gegevens van Azure Synapse Analytics](overview.md)
- [Meer informatie over gedeelde meta gegevens tabellen van Azure Synapse Analytics](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->
