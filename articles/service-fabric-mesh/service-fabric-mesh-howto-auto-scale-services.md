---
title: Een app die wordt uitgevoerd in azure Service Fabric net automatisch schalen
description: Meer informatie over het configureren van beleid voor automatisch schalen voor de services van een Service Fabric mesh-toepassing.
author: dkkapur
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: fb72806dd7ba838ba7170bda409715bc074e1d99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75461971"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Beleid voor automatisch schalen maken voor een Service Fabric mesh-toepassing
Een van de belangrijkste voor delen van het implementeren van toepassingen naar Service Fabric net is de mogelijkheid om uw services eenvoudig in of uit te schalen. Dit moet worden gebruikt voor het verwerken van verschillende belasting hoeveelheden van uw services of het verbeteren van de beschik baarheid. U kunt uw services hand matig in-of uitschalen of het beleid voor automatisch schalen instellen.

Met [automatisch schalen](service-fabric-mesh-scalability.md#autoscaling-service-instances) kunt u het aantal service-exemplaren dynamisch schalen (horizon taal schalen). Automatisch schalen biedt een fantastische elasticiteit en maakt het inrichten of verwijderen van service-exemplaren mogelijk op basis van CPU-of geheugen gebruik.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Opties voor het maken van een beleid voor automatisch schalen, activeren en mechanisme
Er wordt een beleid voor automatisch schalen gedefinieerd voor elke service die u wilt schalen. Het beleid wordt gedefinieerd in het bron bestand van de YAML-service of de JSON-implementatie sjabloon. Elk schaal beleid bestaat uit twee delen: een trigger en een schaal mechanisme.

De trigger definieert wanneer een beleid voor automatisch schalen wordt geactiveerd.  Geef het soort trigger op (gemiddelde belasting) en de metrische gegevens die moeten worden bewaakt (CPU of geheugen).  Drempel waarden voor bovenste en onderste belasting worden opgegeven als een percentage. Het schaal interval bepaalt hoe vaak (in seconden) het opgegeven gebruik (zoals gemiddelde CPU-belasting) in alle momenteel geïmplementeerde service-exemplaren moet worden gecontroleerd.  Het mechanisme wordt geactiveerd wanneer de bewaakte meet waarde onder de laagste drempel waarde daalt of hoger is dan de bovenste drempel waarde.  

Het mechanisme voor schalen bepaalt hoe de schaal bewerking moet worden uitgevoerd wanneer het beleid wordt geactiveerd.  Geef het type mechanisme op (replica toevoegen/verwijderen), het minimale en maximale aantal replica's (als gehele getallen).  Het aantal service replica's wordt nooit onder het minimum aantal of boven het maximum aantal geschaald.  U kunt ook de schaal verhoging opgeven als een geheel getal. Dit is het aantal replica's dat wordt toegevoegd of verwijderd in een schaal bewerking.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Een beleid voor automatisch schalen definiëren in een JSON-sjabloon

In het volgende voor beeld ziet u een beleid voor automatisch schalen in een JSON-implementatie sjabloon.  Het beleid voor automatisch schalen wordt gedeclareerd in een eigenschap van de service die moet worden geschaald.  In dit voor beeld wordt een gemiddelde belasting trigger voor CPU gedefinieerd.  Het mechanisme wordt geactiveerd als de gemiddelde CPU-belasting van alle geïmplementeerde instanties onder 0,2 (20%) daalt of komt meer dan 0,8 (80%).  De CPU-belasting wordt elke 60 seconden gecontroleerd.  Het mechanisme voor schalen wordt gedefinieerd om instanties toe te voegen of te verwijderen als het beleid wordt geactiveerd.  Service-exemplaren worden toegevoegd of verwijderd in stappen van één.  Er wordt ook een minimum aantal exemplaren van één en een maximum aantal exemplaren van 40 gedefinieerd.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Een beleid voor automatisch schalen definiëren in een service. yaml-resource bestand
In het volgende voor beeld wordt een beleid voor automatisch schalen in een service resource-bestand (YAML) weer gegeven.  Het beleid voor automatisch schalen wordt gedeclareerd als een eigenschap van de service die moet worden geschaald.  In dit voor beeld wordt een gemiddelde belasting trigger voor CPU gedefinieerd.  Het mechanisme wordt geactiveerd als de gemiddelde CPU-belasting van alle geïmplementeerde instanties onder 0,2 (20%) daalt of komt meer dan 0,8 (80%).  De CPU-belasting wordt elke 60 seconden gecontroleerd.  Het mechanisme voor schalen wordt gedefinieerd om instanties toe te voegen of te verwijderen als het beleid wordt geactiveerd.  Service-exemplaren worden toegevoegd of verwijderd in stappen van één.  Er wordt ook een minimum aantal exemplaren van één en een maximum aantal exemplaren van 40 gedefinieerd.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [hand matig schalen van een service](service-fabric-mesh-tutorial-template-scale-services.md)
