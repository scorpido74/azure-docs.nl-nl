---
title: Implementatie van statusintegratie - Azure Deployment Manager
description: Beschrijft hoe u een service voor veel regio's implementeert met Azure Deployment Manager. Het toont veilige implementatiepraktijken om de stabiliteit van uw implementatie te controleren voordat u naar alle regio's wordt uitgerold.
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273798"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Implementatie van statusintegratie introduceren in Azure Deployment Manager (openbare voorbeeld)

[Met Azure Deployment Manager](./deployment-manager-overview.md) u gefaseerde implementaties van Azure Resource Manager-resources uitvoeren. De middelen worden per regio op geordende wijze ingezet. De geïntegreerde statuscontrole van Azure Deployment Manager kan implementaties controleren en problematische implementaties automatisch stoppen, zodat u problemen oplossen en de schaal van de impact verkleinen. Deze functie kan de beschikbaarheid van service-problemen verminderen als gevolg van regressies in updates.

## <a name="health-monitoring-providers"></a>Aanbieders van gezondheidsmonitoring

Om de integratie van de gezondheid zo eenvoudig mogelijk te maken, heeft Microsoft samengewerkt met enkele van de beste servicehealthmonitoringbedrijven om u een eenvoudige kopieer-/plakoplossing te bieden om statuscontroles te integreren met uw implementaties. Als u nog geen gezondheidsmonitor gebruikt, zijn dit geweldige oplossingen om mee te beginnen:

| ![gegevenshond azure deployment manager health monitor provider](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure deployment manager health monitor provider site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure deployment manager health monitor provider wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, het toonaangevende monitoring- en analyseplatform voor moderne cloudomgevingen. Bekijk [hoe Datadog integreert met Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, de alles-in-één private en public cloud services monitoring oplossing. Bekijk [hoe Site24x7 integreert met Azure Deployment Manager.](https://www.site24x7.com/azure/adm.html)| Wavefront, het monitoring- en analyseplatform voor multi-cloud applicatieomgevingen. Bekijk [hoe Wavefront integreert met Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Hoe de gezondheid van de dienstverlening wordt bepaald

[Health monitoring providers](#health-monitoring-providers) bieden verschillende mechanismen voor het toezicht op diensten en u te waarschuwen voor eventuele service gezondheidsproblemen. [Azure Monitor](../../azure-monitor/overview.md) is een voorbeeld van een dergelijk aanbod. Azure Monitor kan worden gebruikt om waarschuwingen te maken wanneer bepaalde drempelwaarden worden overschreden. Uw geheugen- en CPU-gebruik stijgen bijvoorbeeld boven de verwachte niveaus wanneer u een nieuwe update voor uw service implementeert. Wanneer u een melding krijgt, u corrigerende maatregelen nemen.

Deze zorgverleners bieden doorgaans REST API's, zodat de status van de monitoren van uw service programmatisch kan worden onderzocht. De REST API's kunnen terugkomen met een eenvoudig gezond/ongezond signaal (bepaald door de HTTP-responscode) en/of met gedetailleerde informatie over de signalen die het ontvangt.

Met de nieuwe *healthCheck-stap* in Azure Deployment Manager u HTTP-codes declareren die duiden op een gezonde service, of, voor complexere REST-resultaten, u zelfs reguliere expressies opgeven die, als ze overeenkomen, een gezonde respons aangeven.

De stroom naar het instellen van de statuscontrole van Azure Deployment Manager:

1. Maak uw gezondheidsmonitoren via een zorgverlener van uw keuze.
1. Maak een of meer healthCheck-stappen als onderdeel van de implementatie van Azure Deployment Manager. Vul de stappen healthCheck in met de volgende informatie:

    1. De URI voor de REST API voor uw statusmonitors (zoals gedefinieerd door uw zorgverlener).
    1. Verificatiegegevens. Momenteel wordt alleen API-key-stijlverificatie ondersteund.
    1. [HTTP-statuscodes](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) of reguliere expressies die een gezonde respons definiëren. Houd er rekening mee dat u reguliere expressies verstrekken, die ALL moet overeenkomen om het antwoord als gezond te beschouwen, of u uitdrukkingen geven waarvan ELKE moet overeenkomen om het antwoord als gezond te beschouwen. Beide methoden worden ondersteund.

    De volgende Json is een voorbeeld:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Roep de healthCheck-stappen op het juiste moment in uw Azure Deployment Manager-implementatie. In het volgende voorbeeld wordt een statuscontrolestap ingeroepen in **postDeploymentSteps** van **stepGroup2**.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Zie [Zelfstudie: Statuscontrole gebruiken in Azure Deployment Manager](./deployment-manager-health-check.md)als u een voorbeeld wilt doorlopen.

## <a name="phases-of-a-health-check"></a>Fasen van een gezondheidscontrole

Op dit moment weet Azure Deployment Manager hoe u de status van uw service moet opvragen en in welke fasen uw implementatie dit moet doen. Azure Deployment Manager maakt echter ook een diepe configuratie van de timing van deze controles mogelijk. Een healthCheck-stap wordt uitgevoerd in 3 opeenvolgende fasen, die allemaal configureerbare duur hebben: 

1. Wait

    1. Nadat een implementatiebewerking is voltooid, kunnen VM's opnieuw worden opgestart, opnieuw configureren op basis van nieuwe gegevens of zelfs voor de eerste keer worden gestart. Het kost ook tijd voor diensten om te beginnen met het uitzenden van gezondheidssignalen worden samengevoegd door de leverancier van gezondheidsmonitoring in iets nuttigs. Tijdens dit tumultueuze proces, kan het niet zinvol om te controleren op de gezondheid van de dienst, omdat de update nog niet heeft bereikt een steady state. Inderdaad, de dienst kan oscilleren tussen gezonde en ongezonde staten als de middelen te vestigen. 
    1. Tijdens de wachtfase wordt de servicestatus niet gecontroleerd. Dit wordt gebruikt om de geïmplementeerde resources de tijd om te bakken voordat u begint met de health check proces. 
1. Elastische

    1. Aangezien het onmogelijk is om in alle gevallen te weten hoe lang de middelen zullen duren om te bakken voordat ze stabiel worden, de Elastische fase zorgt voor een flexibele periode tussen wanneer de middelen zijn potentieel instabiel en wanneer ze nodig zijn om een gezonde gestage te handhaven Staat.
    1. Wanneer de elasticfase begint, begint Azure Deployment Manager met het periodiek peilen van het meegeleverde REST-eindpunt voor de servicestatus. Het polling-interval is configureerbaar. 
    1. Als de statusmonitor terugkomt met signalen die aangeven dat de service ongezond is, worden deze signalen genegeerd, wordt de elastische fase voortgezet en wordt de polling voortgezet. 
    1. Zodra de gezondheidsmonitor terugkomt met signalen die aangeven dat de service gezond is, eindigt de elasticfase en begint de HealthyState-fase. 
    1. De duur die is opgegeven voor de elastische fase is dus de maximale hoeveelheid tijd die kan worden besteed aan polling voor de gezondheid van de dienst voordat een gezonde reactie als verplicht wordt beschouwd. 
1. HealthyState HealthyState

    1. Tijdens de HealthyState-fase wordt de servicestatus voortdurend gepeild met hetzelfde interval als de elasticfase. 
    1. De service zal naar verwachting gezonde signalen van de leverancier van statusbewaking gedurende de gehele opgegeven duur behouden. 
    1. Als op enig moment een ongezonde reactie wordt gedetecteerd, stopt Azure Deployment Manager de hele implementatie en retourneert het REST-antwoord met de ongezonde servicesignalen.
    1. Zodra de duur van de HealthyState is beëindigd, is de healthCheck voltooid en gaat de implementatie door naar de volgende stap.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u hoe u statuscontrole integreren in Azure Deployment Manager. Ga naar het volgende artikel om te leren hoe u implementeren met Deployment Manager.

> [!div class="nextstepaction"]
> [Zelfstudie: statuscontrole integreren in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)