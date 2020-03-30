---
title: Inleiding tot de Azure Cosmos DB etcd API
description: Dit artikel bevat een overzicht en de belangrijkste voordelen van de ETCD API in Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: acd87fac5ec2edc40d27d98f073e13c0acae8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498591"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Inleiding tot de Azure Cosmos DB etcd API (preview)

Azure Cosmos DB is de wereldwijd gedistribueerde databaseservice met meerdere modellen van Microsoft voor essentiële toepassingen. Het biedt kant-en-klare wereldwijde distributie, elastische schaling van doorvoer en opslag, enkelcijferige milliseconde latencies op het 99e percentiel en gegarandeerde hoge beschikbaarheid, allemaal ondersteund door toonaangevende SLA's.

[Etcd](https://github.com/etcd-io/etcd) is een gedistribueerde sleutel /waarde winkel. In [Kubernetes](https://kubernetes.io/)wordt etcd gebruikt om de status en de configuratie van de Kubernetes-clusters op te slaan. Het waarborgen van beschikbaarheid, betrouwbaarheid en prestaties van etcd is cruciaal voor de algehele clusterstatus, schaalbaarheid, elasticiteitsbeschikbaarheid en prestaties van een Kubernetes-cluster. 

Met de geëetcd-API in Azure Cosmos DB u Azure Cosmos DB gebruiken als backend-store voor [Azure Kubernetes.](../aks/index.yml) etcd API in Azure Cosmos DB is momenteel in preview. Azure Cosmos DB implementeert het etcd-draadprotocol. Met de ETCD API in Azure Cosmos DB krijgen ontwikkelaars automatisch zeer betrouwbare, [beschikbare,](high-availability.md) [wereldwijd gedistribueerde](distribute-data-globally.md) Kubernetes. Met deze API kunnen ontwikkelaars Kubernetes state management schalen op een volledig beheerde cloud native PaaS-service. 

> [!NOTE]
> In tegenstelling tot andere API's in Azure Cosmos DB u geen geëetcd API-account inrichten via de Azure-portal, CLI of SDKs. U een geëetcd API-account inrichten door alleen de resourcemanagersjabloon te implementeren. zie [Azure Kubernetes inrichten met](bootstrap-kubernetes-cluster.md) het azure cosmos DB-artikel voor gedetailleerde stappen. Azure Cosmos DB etcd API is momenteel in beperkte preview. U [zich aanmelden voor de preview](https://aka.ms/cosmosetcdapi-signup)door het inschrijfformulier in te vullen.

## <a name="wire-level-compatibility"></a>Compatibiliteit op draadniveau

Azure Cosmos DB implementeert het wire-protocol van versie 3 en stelt de API-servers van het [hoofdknooppunt](https://kubernetes.io/docs/concepts/overview/components/) in staat azure Cosmos DB te gebruiken, net zoals het zou doen in een lokaal geïnstalleerde etcd-omgeving. De etcd API ondersteunt TLS wederzijdse authenticatie. 

In het volgende diagram worden de componenten van een Kubernetes-cluster weergegeven. In de clustermaster gebruikt de API Server Azure Cosmos DB etcd API, in plaats van lokaal geïnstalleerde etcd. 

![Azure Cosmos DB implementeert het geëetcd-draadprotocol](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Belangrijkste voordelen

### <a name="no-etcd-operations-management"></a>Geen etcd operations management

Als volledig beheerde native cloudservice verwijdert Azure Cosmos DB de noodzaak voor Kubernetes-ontwikkelaars om etcd in te stellen en te beheren. De geëetcd-API in Azure Cosmos DB is schaalbaar, zeer beschikbaar, fouttolerant en biedt hoge prestaties. De overhead van het instellen van replicatie op meerdere knooppunten, het uitvoeren van rolling updates, beveiligingspatches en het bewaken van de geëetcd-status worden verwerkt door Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Wereldwijde distributie & hoge beschikbaarheid 

Door de GEËD API te gebruiken, garandeert Azure Cosmos DB 99,99% beschikbaarheid voor gegevenslezen en schrijven in één regio en 99,999% beschikbaarheid in meerdere regio's. 

### <a name="elastic-scalability"></a>Elastische schaalbaarheid

Azure Cosmos DB biedt elastische schaalbaarheid voor lees- en schrijfverzoeken in verschillende regio's.
Naarmate het Kubernetes-cluster groeit, schaalt het geëetcd API-account in Azure Cosmos DB elastisch zonder downtime. Het opslaan van etcd-gegevens in Azure Cosmos DB, in plaats van de Hoofdknooppunten van Kubernetes, maakt ook flexibelere hoofdknooppuntschaling mogelijk. 

### <a name="security--enterprise-readiness"></a>Gereedheid voor beveiliging & onderneming

Wanneer etcd-gegevens worden opgeslagen in Azure Cosmos DB, krijgen Kubernetes-ontwikkelaars automatisch de [ingebouwde versleuteling in rust,](database-encryption-at-rest.md) [certificeringen en naleving](compliance.md)en [back-up- en herstelmogelijkheden](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) die worden ondersteund door Azure Cosmos DB. 

## <a name="next-steps"></a>Volgende stappen

* [Azure Kubernetes gebruiken met Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Belangrijkste voordelen van Azure Cosmos DB](introduction.md)
* [AKS-engine Quickstart-handleiding](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)