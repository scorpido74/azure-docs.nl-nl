---
title: Azure-functies op Kubernetes met KEDA
description: Begrijp hoe u Azure-functies in Kubernetes in de cloud of on-premises uitvoert met KEDA, op Kubernetes gebaseerde gebeurtenisgestuurde automatischscaling.
author: jeffhollan
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: jehollan
ms.openlocfilehash: 2c06fdba8f60243acf4e0fabd23df8b832c210db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301672"
---
# <a name="azure-functions-on-kubernetes-with-keda"></a>Azure-functies op Kubernetes met KEDA

De runtime van Azure Functions biedt flexibiliteit bij het hosten waar en hoe u wilt.  [KEDA](https://keda.sh) (Kubernetes-based Event Driven Autoscaling) sluit naadloos aan op de runtime en tooling van Azure Functions om event driven scale in Kubernetes te bieden.

## <a name="how-kubernetes-based-functions-work"></a>Hoe Kubernetes-gebaseerde functies werken

De Azure Functions-service bestaat uit twee belangrijke onderdelen: een runtime en een schaalcontroller.  De runtime van Functies wordt uitgevoerd en uitgevoerd uw code.  De runtime bevat logica voor het activeren, registreren en beheren van functie-uitvoeringen.  De runtime van Azure-functies kan *overal*worden uitgevoerd.  Het andere onderdeel is een schaalcontroller.  De schaalcontroller controleert de snelheid van gebeurtenissen die op uw functie zijn gericht en schaalt proactief het aantal exemplaren waarop uw app wordt uitgevoerd.  Zie [Azure Functions schalen en hosten](functions-scale.md)voor meer informatie.

Kubernetes-gebaseerde functies bieden de runtime van de functies in een [Docker-container](functions-create-function-linux-custom-image.md) met gebeurtenisgestuurde schaling via KEDA.  KEDA kan schalen naar 0 instanties (wanneer er *n* geen gebeurtenissen plaatsvinden) en naar n-exemplaren. Het doet dit door het blootstellen van aangepaste statistieken voor de Kubernetes autoscaler (Horizontal Pod Autoscaler).  Het gebruik van Functions-containers met KEDA maakt het mogelijk om serverloze functiemogelijkheden in elk Kubernetes-cluster te repliceren.  Deze functies kunnen ook worden geïmplementeerd met behulp van [de virtuele knooppuntenfunctie voor virtuele knooppunten van Azure Kubernetes Services (AKS)](../aks/virtual-nodes-cli.md) voor serverloze infrastructuur.

## <a name="managing-keda-and-functions-in-kubernetes"></a>KEDA en functies beheren in Kubernetes

Als u functies wilt uitvoeren op uw Kubernetes-cluster, moet u de KEDA-component installeren. U deze component installeren met Behulp van [Azure Functions Core Tools](functions-run-local.md).

### <a name="installing-with-helm"></a>Installeren met Helm

Er zijn verschillende manieren om KEDA te installeren in elk Kubernetes-cluster, inclusief Helm.  Implementatieopties worden gedocumenteerd op de [KEDA-site.](https://keda.sh/deploy/)

## <a name="deploying-a-function-app-to-kubernetes"></a>Een functie-app implementeren voor Kubernetes

U elke functie-app implementeren in een Kubernetes-cluster met KEDA.  Aangezien uw functies in een Docker-container `Dockerfile`worden uitgevoerd, heeft uw project een .  Als deze nog niet is, u een Dockerfile toevoegen door de volgende opdracht uit te voeren aan de hoofdmap van het project Functies:

```cli
func init --docker-only
```

Voer de volgende opdracht uit om een afbeelding te maken en uw functies te implementeren in Kubernetes:

> [!NOTE]
> De Core Tools maken gebruik van de docker CLI om de afbeelding te bouwen en te publiceren. Zorg ervoor dat docker al is geïnstalleerd `docker login`en verbonden bent met uw account met .

```cli
func kubernetes deploy --name <name-of-function-deployment> --registry <container-registry-username>
```

> Vervang `<name-of-function-deployment>` door de naam van uw functie-app.

Hiermee wordt `Deployment` een Kubernetes-bron, een `ScaledObject` resource en `Secrets`- omgevingsvariabelen die uit uw `local.settings.json` bestand zijn geïmporteerd, gebruikt.

### <a name="deploying-a-function-app-from-a-private-registry"></a>Een functie-app implementeren vanuit een privéregister

De bovenstaande stroom werkt ook voor particuliere registers.  Als u uw containerafbeelding uit een privéregister haalt, neemt u de `--pull-secret` vlag op `func kubernetes deploy`die verwijst naar het geheim kubernetes dat de persoonlijke registerreferenties bevat wanneer u deze uitvoert.

## <a name="removing-a-function-app-from-kubernetes"></a>Een functie-app verwijderen uit Kubernetes

Na het implementeren u een `Deployment`functie `ScaledObject`verwijderen `Secrets` door de bijbehorende, een gemaakte functie te verwijderen.

```cli
kubectl delete deploy <name-of-function-deployment>
kubectl delete ScaledObject <name-of-function-deployment>
kubectl delete secret <name-of-function-deployment>
```

## <a name="uninstalling-keda-from-kubernetes"></a>KEDA verwijderen van Kubernetes

Stappen om KEDA te verwijderen zijn gedocumenteerd [op de KEDA-site.](https://keda.sh/deploy/)

## <a name="supported-triggers-in-keda"></a>Ondersteunde triggers in KEDA

KEDA heeft ondersteuning voor de volgende Azure-functietriggers:

* [Azure Storage-wachtrijen](functions-bindings-storage-queue.md)
* [Azure-servicebuswachtrijen](functions-bindings-service-bus.md)
* [Azure-gebeurtenis / IoT-hubs](functions-bindings-event-hubs.md)
* [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
* [RabbitMQ-wachtrij](https://github.com/azure/azure-functions-rabbitmq-extension)

### <a name="http-trigger-support"></a>ONDERSTEUNING voor HTTP Trigger

U Azure-functies gebruiken die HTTP-triggers blootleggen, maar KEDA beheert deze niet rechtstreeks.  U de KEDA prometheus-trigger gebruiken om [HTTP Azure-functies te schalen van 1 naar *n-exemplaren.* ](https://dev.to/anirudhgarg_99/scale-up-and-down-a-http-triggered-function-app-in-kubernetes-using-keda-4m42)

## <a name="next-steps"></a>Volgende stappen
Zie de volgende bronnen voor meer informatie:

* [Een functie maken met een aangepaste afbeelding](functions-create-function-linux-custom-image.md)
* [Azure-functies lokaal programmeren en testen](functions-develop-local.md)
* [Hoe het Azure-functieverbruiksplan werkt](functions-scale.md)