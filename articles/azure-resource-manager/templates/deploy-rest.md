---
title: Resources implementeren met REST API en sjabloon
description: Gebruik Azure Resource Manager en Resource Manager REST API om resources te implementeren in Azure. De resources zijn gedefinieerd in een Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 9cdb7b668e5170917b41ef49639bd9a17e538766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153230"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>Resources implementeren met ARM-sjablonen en REST API voor Resource Manager

In dit artikel wordt uitgelegd hoe u de RestAPI voor ResourceBeheer gebruiken met ARM-sjablonen (Azure Resource Manager) om uw resources te implementeren in Azure.

U uw sjabloon opnemen in de aanvraaginstantie of een koppeling maken naar een bestand. Bij het gebruik van een bestand kan het een lokaal bestand zijn of een extern bestand dat beschikbaar is via een URI. Wanneer uw sjabloon zich in een opslagaccount bevindt, u de toegang tot de sjabloon beperken en tijdens de implementatie een SAS-token (Shared Access Signature) geven.

## <a name="deployment-scope"></a>Implementatiebereik

U uw implementatie targeten op een beheergroep, een Azure-abonnement of een brongroep. In de meeste gevallen targetu's implementaties op een resourcegroep. Gebruik beheergroep- of abonnementsimplementaties om beleids- en roltoewijzingen toe te passen in het opgegeven bereik. U gebruikt ook abonnementsimplementaties om een resourcegroep te maken en er resources op te implementeren. Afhankelijk van het bereik van de implementatie gebruikt u verschillende opdrachten.

Als u wilt implementeren in een **resourcegroep,** gebruikt u [Implementatieen - Maken](/rest/api/resources/deployments/createorupdate). Het verzoek wordt verzonden naar:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Als u wilt implementeren op een **abonnement,** gebruikt u [Implementaties - Maken bij abonnementsbereik](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Het verzoek wordt verzonden naar:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Zie [Resourcegroepen en resources maken op abonnementsniveau](deploy-to-subscription.md)voor meer informatie over implementaties op abonnementsniveau.

Als u wilt implementeren in een **beheergroep,** gebruikt u [Implementaties - Maken bij het bereik van de beheergroep](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Het verzoek wordt verzonden naar:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Zie [Resources maken op het niveau van de beheergroep voor](deploy-to-management-group.md)meer informatie over implementaties op managementgroepenniveau.

De voorbeelden in dit artikel gebruiken implementaties van resourcegroepen.

## <a name="deploy-with-the-rest-api"></a>Implementeren met de REST-API

1. Stel [algemene parameters en kopteksten](/rest/api/azure/)in, inclusief verificatietokens.

1. Als u geen bestaande resourcegroep hebt, maakt u een resourcegroep. Geef uw abonnements-ID, de naam van de nieuwe resourcegroep en de locatie op die u nodig hebt voor uw oplossing. Zie [Een resourcegroep maken](/rest/api/resources/resourcegroups/createorupdate)voor meer informatie .

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Met een aanvraagorgaan als:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Valideer uw implementatie voordat u deze uitvoert door de bewerking [Een sjabloonimplementatie](/rest/api/resources/deployments/validate) valideren uit te voeren. Geef bij het testen van de implementatie parameters op die precies zo zijn als bij het uitvoeren van de implementatie (weergegeven in de volgende stap).

1. Als u een sjabloon wilt implementeren, geeft u uw abonnements-id op, de naam van de resourcegroep, de naam van de implementatie in de aanvraag URI.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   Geef in de aanvraaginstantie een koppeling naar uw sjabloon en parameterbestand. Zie [Resourcebeheer-parameterbestand maken](parameter-files.md)voor meer informatie over het parameterbestand .

   Let op: de **modus** is ingesteld op **Incrementeel**. Als u een volledige implementatie wilt uitvoeren, stelt u **de modus** in op **Voltooien**. Wees voorzichtig bij het gebruik van de volledige modus, omdat u per ongeluk bronnen verwijderen die niet in uw sjabloon staan.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Als u antwoordinhoud wilt registreren, inhoud wilt aanvragen of beide, **u foutopsporing instellen** in de aanvraag opnemen.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    U uw opslagaccount zo instellen dat u een SAS-token (Shared Access Signature) gebruikt. Zie [Toegang delegeren met een gedeelde toegangshandtekening](/rest/api/storageservices/delegating-access-with-a-shared-access-signature)voor meer informatie.

    Als u een gevoelige waarde voor een parameter (zoals een wachtwoord) moet opgeven, voegt u die waarde toe aan een sleutelkluis. Haal het sleutelkluis op tijdens de implementatie, zoals in het vorige voorbeeld wordt weergegeven. Zie [Veilige waarden doorgeven tijdens de implementatie voor](key-vault-parameter.md)meer informatie.

1. In plaats van te koppelen aan bestanden voor de sjabloon en parameters, u deze opnemen in de aanvraaginstantie. In het volgende voorbeeld wordt de aanvraagtekst met de sjabloon en parameterinline weergegeven:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Als u de status van de sjabloonimplementatie wilt krijgen, gebruikt u [Implementatieen - Get](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>Volgende stappen

- Als u wilt terugdraaien naar een geslaagde implementatie wanneer er een fout optreedt, raadpleegt u [Rollback-on error naar succesvolle implementatie](rollback-on-error.md).
- Zie [Azure Resource Manager-implementatiemodi](deployment-modes.md)als u wilt opgeven hoe u resources in de resourcegroep verwerken, maar niet in de sjabloon bent gedefinieerd.
- Zie [Asynchrone Azure-bewerkingen bijhouden](../management/async-operations.md)voor meer informatie over het afhandelen van asynchrone REST-bewerkingen.
- Zie [De structuur en syntaxis van ARM-sjablonen begrijpen](template-syntax.md)voor meer informatie over sjablonen.

