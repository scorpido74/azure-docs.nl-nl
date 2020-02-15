---
title: Elastisch schalen met Cassandra-API in Azure Cosmos DB
description: Meer informatie over de beschik bare opties voor het schalen van een Azure Cosmos DB Cassandra-API-account en hun voor delen/nadelen
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 668e9ddadf151a86be0d8c09fc91b4c70db12f3a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210786"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Een Azure Cosmos DB Cassandra-API account elastisch schalen

Er zijn diverse opties voor het verkennen van de elastische aard van de API van de Azure Cosmos DB voor Cassandra. Om te begrijpen hoe effectief kan worden geschaald in Azure Cosmos DB, is het belang rijk om te begrijpen hoe u de juiste hoeveelheid aanvraag eenheden (RU/s) inricht om rekening te houden met de prestatie vereisten in uw systeem. Zie het artikel [aanvraag eenheden](request-units.md) voor meer informatie over aanvraag eenheden. 

![Database bewerkingen gebruiken aanvraag eenheden](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Beperking van de verwerkings frequentie (429 fouten)

Azure Cosmos DB retourneert een frequentie-Limited (429) fouten als clients meer bronnen (RU/s) verbruiken dan de hoeveelheid die u hebt ingericht. Met de Cassandra-API in Azure Cosmos DB worden deze uitzonde ringen omgezet in overbelaste fouten op het Cassandra systeem eigen protocol. 

Als uw systeem niet gevoelig is voor latentie, kan het voldoende zijn om de doorvoer snelheid te verwerken door nieuwe pogingen te gebruiken. Zie het voor [beeld van Java-code](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) voor het transparant afhandelen met behulp van de [Azure Cosmos DB extensie](https://github.com/Azure/azure-cosmos-cassandra-extensions) voor Cassandra-beleid voor [opnieuw proberen](https://docs.datastax.com/drivers/java/2.0/com/datastax/driver/core/policies/RetryPolicy.html) in Java. U kunt ook de [Spark-extensie](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) gebruiken om de frequentie limiet te verwerken.

## <a name="manage-scaling"></a>Schalen beheren

Als u de latentie wilt minimaliseren, is er een breed scala aan opties voor het beheren van de door Voer van schalen en inrichten (RUs) in de Cassandra-API:

* [Hand matig met behulp van de Azure Portal](#use-azure-portal)
* [Programmatisch met behulp van de functies van het besturings vlak](#use-control-plane)
* [Programmatisch met behulp van CQL-opdrachten met een specifieke SDK](#use-cql-queries)
* [Dynamisch door gebruik te maken van auto pilot](#use-autopilot)

In de volgende secties worden de voor-en nadelen van elke benadering uitgelegd. U kunt vervolgens besluiten over de beste strategie om de schaal behoeften van uw systeem, de totale kosten en efficiëntie behoeften voor uw oplossing te verdelen.

## <a id="use-azure-portal"></a>De Azure Portal gebruiken

U kunt de resources in Azure Cosmos DB Cassandra-API-account schalen met behulp van Azure Portal. Zie het artikel over het inrichten van de [door Voer voor containers en data bases](set-throughput.md)voor meer informatie. In dit artikel worden de relatieve voor delen beschreven van het instellen van door Voer op [Data Base](set-throughput.md#set-throughput-on-a-database) -of [container](set-throughput.md#set-throughput-on-a-container) niveau in de Azure Portal. De termen "data base" en "container" die in deze artikelen worden vermeld, worden respectievelijk toegewezen aan "sleutel ruimte" en "tabel" voor de Cassandra-API.

Het voor deel van deze methode is dat het een eenvoudig kant-en-klare manier is om de doorvoer capaciteit van de data base te beheren. Het nadeel is dat in veel gevallen het mogelijk is dat bepaalde niveaus van automatisering worden vereist voor de schaal baarheid en een hoge prestaties. In de volgende secties worden de relevante scenario's en methoden uitgelegd.

## <a id="use-control-plane"></a>Het besturings vlak gebruiken

De API van Azure Cosmos DB voor Cassandra biedt de mogelijkheid om de door Voer programmatisch aan te passen met behulp van de verschillende functies van het controle vlak. Zie de artikelen [Azure Resource Manager](manage-cassandra-with-resource-manager.md), [Power shell](powershell-samples-cassandra.md)en [Azure cli](cli-samples-cassandra.md) voor hulp en voor beelden.

Het voor deel van deze methode is dat u de schaal aanpassing van resources naar boven of beneden kunt automatiseren op basis van een timer voor piek activiteit of peri Oden met een lage activiteit. Bekijk het voor beeld [hier](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) om te zien hoe u dit kunt doen met Azure functions en Power shell.

Een nadeel van deze benadering is dat u in realtime niet kunt reageren op veranderende schaal behoeften. In plaats daarvan moet u mogelijk gebruikmaken van de toepassings context in uw systeem, op het niveau van de client/SDK, of met behulp van auto [pilot](provision-throughput-autopilot.md).

## <a id="use-cql-queries"></a>CQL-query's gebruiken met een specifieke SDK

U kunt het systeem dynamisch schalen met code door de [CQL Alter-opdrachten](cassandra-support.md#keyspace-and-table-options) voor de opgegeven Data Base of container uit te voeren.

Het voor deel van deze benadering is dat u op dynamische wijze op schaal behoeften kunt reageren op een aangepaste manier die aansluit bij uw toepassing. Met deze methode kunt u nog steeds gebruikmaken van de standaard kosten en-tarieven van de RU. Als de schaal behoefte van uw systeem voornamelijk voorspelbaar is (ongeveer 70% of meer), is het gebruik van SDK met CQL mogelijk een voordeligere methode voor automatisch schalen dan het gebruik van auto pilot. Het nadeel van deze benadering is dat het lastig kan zijn om nieuwe pogingen te implementeren terwijl de frequentie beperking de latentie kan verg Roten.

## <a id="use-autopilot"></a>Auto Pilot gebruiken

Naast de hand matige of programmatische manier om door Voer in te richten, kunt u ook Azure Cosmos-containers configureren in de modus voor automatisch testen. De automatische test modus wordt automatisch en direct op de behoeften van het verbruik binnen opgegeven RU-bereiken geschaald zonder in te boeten voor Sla's. Zie het artikel [Azure Cosmos-containers en-data bases maken in de automatische test modus](provision-throughput-autopilot.md) voor meer informatie.

Het voor deel van deze benadering is dat het de eenvoudigste manier is om de schaal vereisten in uw systeem te beheren. Hiermee wordt gegarandeerd dat er geen beperking wordt toegepast **binnen de geconfigureerde ru-bereiken**. Het nadeel is dat, als de schaal behoefte in uw systeem voorspelbaar is, de auto pilot mogelijk een minder rendabele manier is voor het verwerken van uw schaal behoeften dan met behulp van het Bespoke-besturings vlak of de hierboven beschreven methoden van het SDK-niveau.

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het [maken van een Cassandra-API-account, -database en een tabel](create-cassandra-api-account-java.md) met behulp van een Java toepassing
