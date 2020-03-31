---
title: Implementeren en upgraden met Azure Resource Manager
description: Meer informatie over het implementeren van toepassingen en services naar een Cluster Servicefabric met behulp van een Azure Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: a2dfe54bf2c6b4fa8814f10c10576a73727a7417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610247"
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>Toepassingen en services beheren als Azure Resource Manager-resources

U toepassingen en services implementeren in uw Service Fabric-cluster via Azure Resource Manager. Dit betekent dat in plaats van het implementeren en beheren van toepassingen via PowerShell of CLI nadat u hebt hoeven te wachten tot het cluster klaar is, u nu toepassingen en services in JSON uitdrukken en deze implementeren in dezelfde Resource Manager-sjabloon als uw cluster. Het proces van registratie, inrichting en implementatie van toepassingen gebeurt allemaal in één stap.

Dit is de aanbevolen manier om alle installatie-, governance- of clusterbeheertoepassingen te implementeren die u in uw cluster nodig hebt. Dit omvat de [Patch Orchestration Application](service-fabric-patch-orchestration-application.md), Watchdogs of toepassingen die in uw cluster moeten worden uitgevoerd voordat andere toepassingen of services worden geïmplementeerd. 

Indien van toepassing, beheert u uw toepassingen als Resource Manager-resources om te verbeteren:
* Controlespoor: Resource Manager controleert elke bewerking en houdt een gedetailleerd *activiteitenlogboek bij* waarmee u eventuele wijzigingen in deze toepassingen en uw cluster traceren.
* Op rollen gebaseerd toegangscontrole (RBAC): Het beheren van toegang tot clusters en toepassingen die op het cluster worden geïmplementeerd, kan via dezelfde Resource Manager-sjabloon worden uitgevoerd.
* Azure Resource Manager (via Azure portal) wordt een one-stop-shop voor het beheren van uw cluster en kritieke toepassingsimplementaties.

In het volgende fragment worden de verschillende soorten resources weergegeven die via een sjabloon kunnen worden beheerd:

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>Een nieuwe toepassing toevoegen aan de sjabloon Resourcemanager

1. Bereid de resourcemanagersjabloon van uw cluster voor op implementatie. Zie [Een cluster voor servicestructuur maken met Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) voor meer informatie hierover.
2. Denk na over een aantal toepassingen die u van plan bent te implementeren in het cluster. Zijn er die altijd zal worden uitgevoerd dat andere toepassingen kunnen afhankelijkheden op? Bent u van plan clusterbeheer- of installatietoepassingen te implementeren? Dit soort toepassingen kunnen het best worden beheerd via een Resource Manager-sjabloon, zoals hierboven besproken. 
3. Zodra u hebt bedacht welke toepassingen u op deze manier wilt worden geïmplementeerd, moeten de toepassingen worden verpakt, geritst en een bestandsshare worden geplaatst. Het aandeel moet toegankelijk zijn via een REST-eindpunt dat Azure Resource Manager tijdens de implementatie kan gebruiken.
4. Beschrijf in de sjabloon Resourcemanager onder uw clusterdeclaratie de eigenschappen van elke toepassing. Deze eigenschappen omvatten het aantal replica's of instance en eventuele afhankelijkheidsketens tussen resources (andere toepassingen of services). Zie de [REST API Swagger Spec](https://aka.ms/sfrpswaggerspec)voor een lijst met uitgebreide eigenschappen. Houd er rekening mee dat dit de manifesten van toepassing of service niet vervangt, maar een deel van wat erin staat als onderdeel van de resourcemanagersjabloon van het cluster. Hier is een voorbeeldsjabloon met het implementeren van een stateless service *Service1* en een stateful *serviceService2* als onderdeel van *Application1:*

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only."
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name."
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version."
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file."
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The name of the application resource."
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Application1~Service1",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The name of the service type."
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Application1~Service2",
        "metadata": {
          "description": "The name of the service resource in the format of {applicationName}~{serviceName}."
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The name of the service type."
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2019-03-01",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
   }
   ```

   > [!NOTE] 
   > De *apiVersion* moet `"2019-03-01"`worden ingesteld op . Deze sjabloon kan ook onafhankelijk van het cluster worden geïmplementeerd, zolang het cluster al is geïmplementeerd.

5. Implementeren! 

## <a name="remove-service-fabric-resource-provider-application-resource"></a>Bronbron van servicefabricresourceprovider verwijderen
Als volgt wordt het app-pakket geactiveerd om niet-ingericht te zijn uit het cluster en wordt de gebruikte schijfruimte opgeschoond:
```powershell
Get-AzureRmResource -ResourceId /subscriptions/{sid}/resourceGroups/{rg}/providers/Microsoft.ServiceFabric/clusters/{cluster}/applicationTypes/{apptType}/versions/{version} -ApiVersion "2019-03-01" | Remove-AzureRmResource -Force -ApiVersion "2017-07-01-preview"
```
Door microsoft.servicefabric/clusters/toepassing van uw ARM-sjabloon te verwijderen, wordt de toepassing niet ontrusten

>[!NOTE]
> Zodra de verwijdering is voltooid, mag u de pakketversie niet meer in SFX of ARM zien. U de bron van de toepassingstypeversie waarmee de toepassing wordt uitgevoerd, niet verwijderen. ARM/SFRP voorkomt dit. Als u het lopende pakket probeert te ontleden, voorkomt SF-runtime dit.


## <a name="manage-an-existing-application-via-resource-manager"></a>Een bestaande toepassing beheren via Resource Manager

Als uw cluster al is ingeschakeld en sommige toepassingen die u wilt beheren omdat resourcebeheerresources al zijn geïmplementeerd, in plaats van de toepassingen te verwijderen en opnieuw te implementeren, u een PUT-oproep gebruiken met dezelfde API's om de toepassingen te laten ophalen erkend als Resource Manager resources. Raadpleeg voor meer informatie [Wat is het resourcemodel van de Service Fabric-toepassing?](https://docs.microsoft.com/azure/service-fabric/service-fabric-concept-resource-model)

> [!NOTE]
> Als u wilt toestaan dat een clusterupgrade ongezonde apps negeert, kan de klant "maxPercentUnhealthyApplications: 100" opgeven in de sectie 'upgradeBeschrijving/healthPolicy'. gedetailleerde beschrijvingen voor alle instellingen staan in [de documentatie van servicestoffen REST API-clusterupgradebeleid](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-clusterupgradepolicy).

## <a name="next-steps"></a>Volgende stappen

* Gebruik de [Service Fabric CLI](service-fabric-cli.md) of [PowerShell](service-fabric-deploy-remove-applications.md) om andere toepassingen in uw cluster te implementeren. 
* [Uw cluster servicestructuur upgraden](service-fabric-cluster-upgrade.md)

