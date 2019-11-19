---
title: Azure Functions op Kubernetes met KEDA
description: Meer informatie over het uitvoeren van Azure Functions in Kubernetes in de Cloud of on-premises met behulp van KEDA, op Kubernetes gebaseerde automatische schaling van gebeurtenissen.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure functions, functies, gebeurtenis verwerking, dynamische compute, serverloze architectuur, kubernetes
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 0b77946b24bcc2e329a5c4480e9bd5ef055ef82b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173694"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions op Kubernetes met KEDA

De Azure Functions runtime biedt flexibiliteit bij het hosten van waar en hoe u dat wilt.  [KEDA](https://keda.sh) (op Kubernetes gebaseerde automatisch schalen) paren naadloos met de Azure functions runtime en hulpprogram ma's voor het leveren van op gebeurtenissen gebaseerde schaal in Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>De werking van Kubernetes-functies

De Azure Functions-service bestaat uit twee belang rijke onderdelen: een runtime en een scale-controller.  De runtime van functions wordt uitgevoerd en de code wordt uitgevoerd.  De runtime bevat logica over het activeren, registreren en beheren van functie-uitvoeringen.  De runtime van Azure Functions kan *overal*worden uitgevoerd.  Het andere onderdeel is een scale-controller.  De schaal controller bewaakt de frequentie van gebeurtenissen die zijn gericht op uw functie en schaalt proactief het aantal exemplaren dat uw app uitvoert.  Zie [Azure functions schalen en hosten](functions-scale.md)voor meer informatie.

Kubernetes-functies biedt de functions-runtime in een [docker-container](functions-create-function-linux-custom-image.md) met op gebeurtenissen gebaseerd SCHALEN via KEDA.  KEDA kan worden geschaald naar 0 instanties (wanneer er geen gebeurtenissen plaatsvinden) en Maxi maal *n* exemplaren. Dit wordt gedaan door aangepaste metrische gegevens weer te geven voor de Kubernetes-automatisch schalen (horizontale pod autoschaalr).  Met behulp van functions-containers met KEDA kunt u functie mogelijkheden zonder server repliceren in een Kubernetes-cluster.  Deze functies kunnen ook worden geïmplementeerd met de functie [virtuele knoop punten van Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) voor serverloze infra structuur.

## <a name="managing-keda-and-functions-in-kubernetes"></a>KEDA en functions beheren in Kubernetes

Als u functies wilt uitvoeren op uw Kubernetes-cluster, moet u het onderdeel KEDA installeren. U kunt dit onderdeel installeren met behulp van [Azure functions core tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Installeren met de Azure Functions Core Tools

Standaard installeert kern Hulpprogramma's zowel de KEDA-als Osiris-onderdelen, die respectievelijk gebeurtenis-en HTTP-schaling ondersteunen.  De installatie maakt gebruik van `kubectl` uitgevoerd in de huidige context.

Installeer KEDA in uw cluster door de volgende installatie opdracht uit te voeren:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Een functie-app implementeren op Kubernetes

U kunt elke functie-app implementeren in een Kubernetes-cluster met KEDA.  Omdat uw functies worden uitgevoerd in een docker-container, heeft het project een `Dockerfile`nodig.  Als dat nog niet het geval is, kunt u een Dockerfile toevoegen door de volgende opdracht uit te voeren in de hoofdmap van uw functions-project:

```cli
func init --docker-only
```

Voer de volgende opdracht uit om een installatie kopie te maken en uw functies te implementeren op Kubernetes:

> [!NOTE]
> De kern hulpprogramma's maken gebruik van de docker-CLI voor het bouwen en publiceren van de installatie kopie. Zorg ervoor dat docker al is geïnstalleerd en is verbonden met uw account met `docker login`.

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Vervang `<name-of-function-deployment>` door de naam van uw functie-app.

Hiermee maakt u een Kubernetes `Deployment` resource, een `ScaledObject` resource en `Secrets`, die omgevings variabelen bevat die uit uw `local.settings.json`-bestand zijn geïmporteerd.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Een functie-app implementeren vanuit een persoonlijk REGI ster

De bovenstaande stroom werkt ook voor persoonlijke registers.  Als u de container installatie kopie uit een persoonlijk REGI ster haalt, neemt u de `--pull-secret` vlag op die verwijst naar het Kubernetes-geheim met de referenties van het persoonlijke REGI ster wanneer u `func kubernetes deploy`uitvoert.

## <a name="removing-a-function-app-from-kubernetes"></a>Een functie-app uit Kubernetes verwijderen

Na de implementatie kunt u een functie verwijderen door de bijbehorende `Deployment`, `ScaledObject`, een `Secrets` gemaakt te verwijderen.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>KEDA verwijderen uit Kubernetes

U kunt de volgende kern hulpprogramma's opdracht uitvoeren om KEDA te verwijderen uit een Kubernetes-cluster:

```cli
func kubernetes remove --namespace keda
```

## <a name="supported-triggers-in-keda"></a>Ondersteunde triggers in KEDA

KEDA biedt ondersteuning voor de volgende Azure-functie triggers:

* [Azure Storage wachtrijen](functions-bindings-storage-queue.md)
* [Azure Service Bus wachtrijen](functions-bindings-service-bus.md)
* [Azure Event/IoT hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ-wachtrij](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>Ondersteuning voor HTTP-triggers

U kunt Azure Functions gebruiken dat HTTP-triggers beschikbaar maakt, maar KEDA beheert ze niet rechtstreeks.  De Azure Functions Core Tools installeert een verwant project, Osiris, waarmee HTTP-eind punten kunnen worden geschaald van 0 tot 1.  Het schalen van 1 naar *n* vertrouwt op het traditionele Kubernetes-schaal beleid.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie:

* [Een functie maken met behulp van een aangepaste installatie kopie](functions-create-function-linux-custom-image.md)
* [Azure-functies lokaal programmeren en testen](functions-develop-local.md)
* [De werking van het verbruiks abonnement voor Azure functions](functions-scale.md)