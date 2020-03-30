---
title: Een app automatisch schalen die wordt uitgevoerd in Azure Service Fabric-mesh
description: Meer informatie over het configureren van beleid voor automatische schade waarden voor de services van een Service Fabric Mesh-toepassing.
author: dkkapur
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: fb72806dd7ba838ba7170bda409715bc074e1d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461971"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Autoscale-beleid maken voor een Service Fabric Mesh-toepassing
Een van de belangrijkste voordelen van het implementeren van applicaties naar Service Fabric Mesh is de mogelijkheid voor u om uw services eenvoudig in of uit te schalen. Dit moet worden gebruikt voor het verwerken van verschillende hoeveelheden belasting op uw services of het verbeteren van de beschikbaarheid. U uw services handmatig schalen in of uit- of uitschalen of beleid voor automatisch schalen instellen.

[Met automatisch schalen](service-fabric-mesh-scalability.md#autoscaling-service-instances) u het aantal service-exemplaren dynamisch schalen (horizontaal schalen). Automatisch schalen geeft een grote elasticiteit en maakt het mogelijk om service-exemplaren in te richten of te verwijderen op basis van CPU- of geheugengebruik.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Opties voor het maken van een beleid voor automatisch schalen, trigger en mechanisme
Voor elke service die u wilt schalen, wordt een beleid voor automatisch schalen gedefinieerd. Het beleid wordt gedefinieerd in het YAML-servicebronbestand of de JSON-implementatiesjabloon. Elk schalingsbeleid bestaat uit twee delen: een trigger en een schalingsmechanisme.

De trigger definieert wanneer een beleid voor automatisch schalen wordt aangeroepen.  Geef het soort trigger (gemiddelde belasting) en de statistiek op die moet worden bewaakt (CPU of geheugen).  Bovenste en lagere belastingsdrempels opgegeven als percentage. Het schaalinterval bepaalt hoe vaak het opgegeven gebruik (zoals de gemiddelde CPU-belasting) moet worden gecontroleerd in alle momenteel geïmplementeerde service-exemplaren.  Het mechanisme wordt geactiveerd wanneer de bewaakte statistiek onder de ondergrens zakt of boven de bovengrens stijgt.  

Het schaalmechanisme definieert hoe u de schaalbewerking uitvoert wanneer het beleid wordt geactiveerd.  Geef het soort mechanisme op (replica toevoegen/verwijderen), het minimum- en maximale aantal replica's (als gehele getallen).  Het aantal servicereplica's wordt nooit geschaald onder het minimumaantal of boven het maximumaantal.  Geef ook de schaaltoename op als een geheel getal, het aantal replica's dat wordt toegevoegd of verwijderd in een schaalbewerking.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Een beleid voor automatisch schalen definiëren in een JSON-sjabloon

In het volgende voorbeeld wordt een beleid voor automatisch schalen weergegeven in een JSON-implementatiesjabloon.  Het beleid voor automatisch schalen wordt gedeclareerd in een eigenschap van de service die moet worden geschaald.  In dit voorbeeld wordt een CPU-trigger voor gemiddelde belasting gedefinieerd.  Het mechanisme wordt geactiveerd als de gemiddelde CPU-belasting van alle geïmplementeerde exemplaren onder 0,2 (20%) daalt of hoger gaat dan 0,8 (80%).  De CPU-belasting wordt elke 60 seconden gecontroleerd.  Het schaalmechanisme wordt gedefinieerd om instanties toe te voegen of te verwijderen als het beleid wordt geactiveerd.  Service-exemplaren worden in stappen van één toegevoegd of verwijderd.  Er wordt ook een minimum aantal instanties van één en een maximumaantal instanties van 40 gedefinieerd.

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

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Een autoscale-beleid definiëren in een resourcebestand van service.yaml
In het volgende voorbeeld wordt een beleid voor automatisch schalen weergegeven in een YAML-bestand (Service Resource).  Het beleid voor automatisch schalen wordt gedeclareerd als een eigenschap van de te schalen service.  In dit voorbeeld wordt een CPU-trigger voor gemiddelde belasting gedefinieerd.  Het mechanisme wordt geactiveerd als de gemiddelde CPU-belasting van alle geïmplementeerde exemplaren onder 0,2 (20%) daalt of hoger gaat dan 0,8 (80%).  De CPU-belasting wordt elke 60 seconden gecontroleerd.  Het schaalmechanisme wordt gedefinieerd om instanties toe te voegen of te verwijderen als het beleid wordt geactiveerd.  Service-exemplaren worden in stappen van één toegevoegd of verwijderd.  Er wordt ook een minimum aantal instanties van één en een maximumaantal instanties van 40 gedefinieerd.

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
Meer informatie over het [handmatig schalen van een service](service-fabric-mesh-tutorial-template-scale-services.md)
