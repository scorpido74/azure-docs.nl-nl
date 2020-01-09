---
title: Een app die wordt uitgevoerd in azure Service Fabric net automatisch schalen
description: Informatie over het configureren van beleid voor automatisch schalen voor de services van een Service Fabric-NET-toepassing.
author: dkkapur
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: fb72806dd7ba838ba7170bda409715bc074e1d99
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461971"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Beleid voor automatisch schalen voor een Service Fabric-NET-toepassing maken
Een van de belangrijkste voor delen van het implementeren van toepassingen naar Service Fabric net is de mogelijkheid om uw services eenvoudig in of uit te schalen. Dit moet worden gebruikt voor het verwerken van verschillende belasting hoeveelheden van uw services of het verbeteren van de beschik baarheid. U kunt handmatig schalen van uw services in- of uitschalen of instellen van beleid voor automatisch schalen.

[Automatisch schalen](service-fabric-mesh-scalability.md#autoscaling-service-instances) kunt u het nummer van uw service-instanties (horizontaal schalen) dynamisch te schalen. Automatisch schalen biedt grote flexibiliteit en kunt inrichten of verwijdering van service-exemplaren op basis van gebruik van CPU of geheugen.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Opties voor het maken van een functie voor automatisch schalen van beleid, trigger en een mechanisme
Een functie voor automatisch schalen van beleid is gedefinieerd voor elke service die u wilt schalen. Het beleid is gedefinieerd in het bronbestand voor YAML-service of de JSON-sjabloon voor implementatie. Elk beleid voor vergroten/verkleinen bestaat uit twee delen: een trigger en een mechanisme voor vergroten/verkleinen.

De trigger wordt gedefinieerd wanneer een beleid voor automatisch schalen wordt aangeroepen.  Geef het type van de trigger (gemiddelde belasting) en de metrische gegevens moeten worden bewaakt (CPU of geheugen).  Boven en ondergrenzen load-drempelwaarden die zijn opgegeven als een percentage. Het schaalinterval bepaalt hoe vaak u wilt controleren (in seconden) het gebruik van de opgegeven (zoals gemiddelde CPU-belasting) voor alle instanties van de huidige geïmplementeerde service.  Het mechanisme wordt geactiveerd wanneer de bewaakte metrische gegevens onder de laagste drempel komt of boven de hoogste drempelwaarde toeneemt.  

Het mechanisme voor vergroten/verkleinen definieert hoe u de schaal bewerking niet uitvoeren wanneer het beleid wordt geactiveerd.  Geef het soort mechanisme (replica toevoegen/verwijderen), de minimale en maximale replica telt (als gehele getallen).  Het aantal service-replica's wordt nooit worden geschaald, onder het minimale aantal of boven het maximale aantal.  De schaal verhoging ook opgeven als een geheel getal, dit is het aantal replica's die worden toegevoegd of verwijderd in een bewerking voor vergroten/verkleinen.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Een functie voor automatisch schalen van beleid in een JSON-sjabloon definiëren

Het volgende voorbeeld ziet een beleid voor automatisch schalen in een JSON-sjabloon voor implementatie.  Het beleid voor automatisch schalen is gedeclareerd in een eigenschap van de service kunnen worden geschaald.  In dit voorbeeld wordt een gemiddelde belasting CPU trigger gedefinieerd.  Het mechanisme wordt geactiveerd als de gemiddelde CPU-belasting van alle geïmplementeerde exemplaren vallen onder 0.2 (20%) of uitvalt hierboven 0,8 (80%).  Elke 60 seconden, de CPU-belasting wordt gecontroleerd.  Het mechanisme voor vergroten/verkleinen is gedefinieerd als u wilt toevoegen of verwijderen van instanties als het beleid wordt geactiveerd.  Service-exemplaren wordt toegevoegd of verwijderd in stappen van één.  Een minimum aantal exemplaren van een en een maximum aantal exemplaren van 40 wordt ook gedefinieerd.

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

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Een beleid voor automatisch schalen definiëren in een bronbestand service.yaml
Het volgende voorbeeld ziet een beleid voor automatisch schalen in een service-bronbestand (YAML).  Het beleid voor automatisch schalen is gedeclareerd als een eigenschap van de service kunnen worden geschaald.  In dit voorbeeld wordt een gemiddelde belasting CPU trigger gedefinieerd.  Het mechanisme wordt geactiveerd als de gemiddelde CPU-belasting van alle geïmplementeerde exemplaren vallen onder 0.2 (20%) of uitvalt hierboven 0,8 (80%).  Elke 60 seconden, de CPU-belasting wordt gecontroleerd.  Het mechanisme voor vergroten/verkleinen is gedefinieerd als u wilt toevoegen of verwijderen van instanties als het beleid wordt geactiveerd.  Service-exemplaren wordt toegevoegd of verwijderd in stappen van één.  Een minimum aantal exemplaren van een en een maximum aantal exemplaren van 40 wordt ook gedefinieerd.

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
Meer informatie over het [handmatig een service schaalt](service-fabric-mesh-tutorial-template-scale-services.md)
