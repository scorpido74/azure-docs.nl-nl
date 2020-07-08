---
title: Inleiding tot de Azure Cosmos DB etcd-API
description: In dit artikel vindt u een overzicht van de belangrijkste voor delen van etcd-API in Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 208f0d73b4c8bb11adc9c73796ebc6a622f5ed50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85118165"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Inleiding tot de Azure Cosmos DB etcd-API (preview)

Azure Cosmos DB is de wereldwijd gedistribueerde databaseservice met meerdere modellen van Microsoft voor essentiële toepassingen. Het biedt kant-en-klare wereld wijde distributie, elastische schaal baarheid van door Voer en opslag, latenties in milliseconden van één cijfer op het 99e percentiel en gegarandeerd hoge Beschik baarheid, die allemaal worden ondersteund door toonaangevende SLA.

[Etcd](https://github.com/etcd-io/etcd) is een gedistribueerd sleutel/waarde-archief. In [Kubernetes](https://kubernetes.io/)wordt etcd gebruikt om de status en de configuratie van de Kubernetes-clusters op te slaan. Het garanderen van de beschik baarheid, betrouw baarheid en prestaties van etcd is van cruciaal belang voor de algehele cluster status, schaal baarheid, beschik bare elasticiteit en prestaties van een Kubernetes-cluster.

Met de etcd-API in Azure Cosmos DB kunt u Azure Cosmos DB gebruiken als back-end-Archief voor Azure Kubernetes. etcd-API in Azure Cosmos DB is momenteel beschikbaar als preview-versie. Azure Cosmos DB implementeert het etcd wire-protocol. Met etcd-API in Azure Cosmos DB krijgen ontwikkel aars automatisch een zeer betrouw [bare](high-availability.md), [wereld wijd gedistribueerde](distribute-data-globally.md) Kubernetes. Met deze API kunnen ontwikkel aars Kubernetes-status beheer schalen op een volledig beheerde PaaS-service in de Cloud. 

> [!NOTE]
> In tegens telling tot andere Api's in Azure Cosmos DB kunt u geen etcd-API-account inrichten via de Azure Portal, CLI of Sdk's. U kunt een etcd-API-account inrichten door alleen de Resource Manager-sjabloon te implementeren. Zie voor gedetailleerde stappen [Azure Kubernetes inrichten met Azure Cosmos DB](bootstrap-kubernetes-cluster.md) -artikel. Azure Cosmos DB etcd-API is momenteel een beperkte preview-versie. U kunt [zich registreren voor de preview-versie](https://aka.ms/cosmosetcdapi-signup)door het aanmeldings formulier in te vullen.

## <a name="wire-level-compatibility"></a>Compatibiliteit met Wire-level

Azure Cosmos DB implementeert het wire-protocol van etcd versie 3 en maakt de API-servers [van het hoofd knooppunt](https://kubernetes.io/docs/concepts/overview/components/) Azure Cosmos DB net als in een lokaal geïnstalleerde etcd-omgeving. De etcd-API ondersteunt wederzijdse TLS-verificatie. 

In het volgende diagram ziet u de onderdelen van een Kubernetes-cluster. In de cluster Master gebruikt de API-server Azure Cosmos DB etcd API, in plaats van lokaal geïnstalleerde etcd. 

:::image type="content" source="./media/etcd-api-introduction/etcd-api-wire-protocol.png" alt-text="Azure Cosmos DB implementeren van het etcd wire-protocol" border="false":::

## <a name="key-benefits"></a>Belangrijkste voordelen

### <a name="no-etcd-operations-management"></a>Geen etcd-beheer van bewerkingen

Als volledig beheerde Cloud service biedt Azure Cosmos DB de nood zaak van Kubernetes-ontwikkel aars om etcd in te stellen en te beheren. De etcd-API in Azure Cosmos DB is schaalbaar, Maxi maal beschikbaar, fout tolerant en biedt hoge prestaties. De overhead voor het instellen van replicatie op meerdere knoop punten, het uitvoeren van Rolling updates, beveiligings patches en het controleren van de status van de etcd wordt afgehandeld door Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Globale distributie & hoge Beschik baarheid 

Met behulp van de etcd-API biedt Azure Cosmos DB gegarandeerd 99,99% Beschik baarheid voor het lezen en schrijven van gegevens in één regio, en beschik baarheid van 99,999% voor meerdere regio's. 

### <a name="elastic-scalability"></a>Elastische schaalbaarheid

Azure Cosmos DB biedt elastische schaal baarheid voor lees-en schrijf aanvragen in verschillende regio's.
Naarmate het Kubernetes-cluster groeit, wordt het etcd-API-account in Azure Cosmos DB elastisch geschaald zonder uitval tijd. Het opslaan van etcd-gegevens in Azure Cosmos DB, in plaats van de Kubernetes-hoofd knooppunten, biedt ook meer flexibiliteit voor het schalen van hoofd knooppunten. 

### <a name="security--enterprise-readiness"></a>Beveiliging & voor bereiding op bedrijfs niveau

Wanneer etcd-gegevens worden opgeslagen in Azure Cosmos DB, krijgen Kubernetes-ontwikkel aars automatisch de [ingebouwde versleuteling op rest](database-encryption-at-rest.md), [certificeringen en naleving](compliance.md), en [back-up-en herstel functies](online-backup-and-restore.md) die door Azure Cosmos DB worden ondersteund. 

## <a name="next-steps"></a>Volgende stappen

* [Azure Kubernetes gebruiken met Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Belangrijkste voor delen van Azure Cosmos DB](introduction.md)
* [Snelstartgids voor AKS-engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)