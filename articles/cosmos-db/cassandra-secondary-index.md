---
title: Indexering in Azure Cosmos DB Cassandra API-account
description: Meer informatie over hoe secundaire indexering werkt in azure Azure Cosmos DB Cassandra API-account.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758025"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Secundaire indexering in Azure Cosmos DB Cassandra API

De Cassandra API in Azure Cosmos DB maakt gebruik van de onderliggende indexeringsinfrastructuur om de indexeringssterkte bloot te leggen die inherent is aan het platform. In tegenstelling tot de core SQL API indexeert Cassandra API in Azure Cosmos DB echter niet standaard alle kenmerken. In plaats daarvan ondersteunt het secundairindexeren om een index te maken voor bepaalde kenmerken, die zich op dezelfde manier gedraagt als Apache Cassandra.  

Over het algemeen wordt het afgeraden om filterquery's uit te voeren op de kolommen die niet zijn verdeeld. U moet de syntaxis VAN FILTERING TOESTAAN expliciet gebruiken, wat resulteert in een bewerking die mogelijk niet goed presteert. In Azure Cosmos DB u dergelijke query's uitvoeren op kenmerken met lage kardinaliteit, omdat ze uitwaaieren over partities om de resultaten op te halen.

Het wordt afgeraden om een index te maken op een veelgebruikte kolom. Het is verstandig om een index te maken wanneer u de tabel definieert. Dit zorgt ervoor dat gegevens en indexen consistent zijn. Als u momenteel een nieuwe index maakt voor de bestaande gegevens, u de wijziging van de indexvoortgang voor de tabel niet bijhouden. Als u de voortgang van deze bewerking moet bijhouden, moet u de voortgangswijziging aanvragen via een [ondersteuningsticket.]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)


> [!NOTE]
> Secundaire index wordt niet ondersteund op de volgende objecten:
> - gegevenstypen zoals bevroren verzamelingstypen, decimale en varianttypen.
> - Statische kolommen
> - Clustertoetsen

## <a name="indexing-example"></a>Voorbeeld van indexering

Maak eerst een voorbeeldsleutelruimte en -tabel door de volgende opdrachten uit te voeren op de CQL-shellprompt:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Voeg vervolgens voorbeeldgebruikersgegevens in met de volgende opdrachten:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Als u probeert de volgende instructie uit te voeren, loopt `ALLOW FILTERING`u tegen een fout aan die u vraagt om : 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Hoewel de Cassandra API FILTERING ondersteunt, zoals in de vorige sectie werd vermeld, wordt dit niet aanbevolen. In plaats daarvan moet u een index maken in de zoals weergegeven in het volgende voorbeeld:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
Nadat u een index hebt gemaakt op het veld Achternaam, u de vorige query nu met succes uitvoeren. Met De API van Cassandra in Azure Cosmos DB hoeft u geen indexnaam op te geven. Er wordt een `tablename_columnname_idx` standaardindex met indeling gebruikt. Is bijvoorbeeld ` t1_lastname_idx` de indexnaam voor de vorige tabel.

## <a name="dropping-the-index"></a>De index laten vallen 
U moet weten wat de indexnaam is om de index te laten vallen. Voer `desc schema` de opdracht uit om de beschrijving van uw tabel te krijgen. De uitvoer van deze opdracht bevat `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`de indexnaam in de notatie . U vervolgens de indexnaam gebruiken om de index te laten vallen zoals in het volgende voorbeeld wordt weergegeven:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over hoe [automatisch indexeren](index-overview.md) werkt in Azure Cosmos DB
* [Apache Cassandra-functies ondersteund door Azure Cosmos DB Cassandra API](cassandra-support.md)
