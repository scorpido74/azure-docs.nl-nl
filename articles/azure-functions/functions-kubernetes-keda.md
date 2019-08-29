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
ms.date: 05/06/2019
ms.author: jehollan
ms.openlocfilehash: b581d7c9b5876813e36ebbf41be713b44dd97735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096100"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure Functions op Kubernetes met KEDA

De Azure Functions runtime biedt flexibiliteit bij het hosten van waar en hoe u dat wilt.  [KEDA](https://github.com/kedacore/kore) (Op Kubernetes gebaseerde automatische schaal aanpassing) paren naadloos met de runtime van Azure Functions en hulp middelen voor het leveren van op gebeurtenissen gebaseerde schaal in Kubernetes.

## <a name="how-kubernetes-based-functions-work"></a>De werking van Kubernetes-functies

De Azure Functions-service bestaat uit twee belang rijke onderdelen: een runtime en een scale-controller.  De runtime van functions wordt uitgevoerd en de code wordt uitgevoerd.  De runtime bevat logica over het activeren, registreren en beheren van functie-uitvoeringen.  Het andere onderdeel is een scale-controller.  De schaal controller bewaakt de frequentie van gebeurtenissen die zijn gericht op uw functie en schaalt proactief het aantal exemplaren dat uw app uitvoert.  Zie [Azure functions schalen en hosten](functions-scale.md)voor meer informatie.

Kubernetes-functies biedt de functions-runtime in een docker- [container](functions-create-function-linux-custom-image.md) met op gebeurtenissen gebaseerd SCHALEN via KEDA.  KEDA kan worden geschaald naar 0 instanties (wanneer er geen gebeurtenissen plaatsvinden) en Maxi maal *n* exemplaren. Dit wordt gedaan door aangepaste metrische gegevens weer te geven voor de Kubernetes-automatisch schalen (horizontale pod autoschaalr).  Met behulp van functions-containers met KEDA kunt u functie mogelijkheden zonder server repliceren in een Kubernetes-cluster.  Deze functies kunnen ook worden geïmplementeerd met de functie [virtuele knoop punten van Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) voor serverloze infra structuur.

## <a name="managing-keda-and-functions-in-kubernetes"></a>KEDA en functions beheren in Kubernetes

Als u functies wilt uitvoeren op uw Kubernetes-cluster, moet u het onderdeel KEDA installeren. U kunt dit onderdeel installeren met behulp van [Azure functions core tools](functions-run-local.md).

### <a name="installing-with-the-azure-functions-core-tools"></a>Installeren met de Azure Functions Core Tools

Standaard installeert kern Hulpprogramma's zowel de KEDA-als Osiris-onderdelen, die respectievelijk gebeurtenis-en HTTP-schaling ondersteunen.  De installatie wordt `kubectl` uitgevoerd in de huidige context.

Installeer KEDA in uw cluster door de volgende installatie opdracht uit te voeren:

```cli
func kubernetes install --namespace keda
```

## <a name="deploying-a-function-app-to-kubernetes"></a>Een functie-app implementeren op Kubernetes

U kunt elke functie-app implementeren in een Kubernetes-cluster met KEDA.  Omdat uw functies worden uitgevoerd in een docker-container, heeft het `Dockerfile`project een nodig.  Als dat nog niet het geval is, kunt u een Dockerfile toevoegen door de volgende opdracht uit te voeren in de hoofdmap van uw functions-project:

```cli
func init --docker-only
```

Voer de volgende opdracht uit om een installatie kopie te maken en uw functies te implementeren op Kubernetes:

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Vervang `<name-of-function-deployment>` door de naam van uw functie-app.

Hiermee maakt u een `Deployment` Kubernetes-resource `ScaledObject` , een resource `Secrets`en, die omgevings variabelen bevat die `local.settings.json` uit het bestand zijn geïmporteerd.

## <a name="removing-a-function-app-from-kubernetes"></a>Een functie-app uit Kubernetes verwijderen

Na de implementatie kunt u een functie verwijderen door de bijbehorende `Deployment`, `ScaledObject`, een `Secrets` gemaakte, te verwijderen.

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

KEDA is momenteel beschikbaar in een bèta versie met ondersteuning voor de volgende Azure-functie triggers:

* [Azure Storage wachtrijen](functions-bindings-storage-queue.md)
* [Azure Service Bus wachtrijen](functions-bindings-service-bus.md)
* [HTTP](functions-bindings-http-webhook.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie:

* [Een functie maken met behulp van een aangepaste installatie kopie](functions-create-function-linux-custom-image.md)
* [Azure-functies lokaal programmeren en testen](functions-develop-local.md)
* [De werking van het verbruiks abonnement voor Azure functions](functions-scale.md)