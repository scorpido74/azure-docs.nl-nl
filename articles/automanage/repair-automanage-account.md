---
title: Een gebroken Azure automanage-account herstellen
description: Meer informatie over het oplossen van een gebroken automanage-account
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: ad54b37da8a4945162b507232f33083890ec1fff
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557673"
---
# <a name="repair-a-broken-automanage-account"></a>Een gebroken account voor automanage herstellen
Het [account](./automanage-virtual-machines.md#automanage-account) voor automatisch beheer is de beveiligings context of de identiteit waaronder de geautomatiseerde bewerkingen plaatsvinden. Als u onlangs een abonnement met een account voor automatisch beheer naar een nieuwe Tenant hebt verplaatst, moet u uw account voor automatisch beheer opnieuw configureren. Als u uw account voor automatisch beheren opnieuw wilt configureren, moet u het identiteits type opnieuw instellen en de juiste rollen voor het account toewijzen.

## <a name="step-1-reset-automanage-account-identity-type"></a>Stap 1: het identiteits type van het account voor automanage opnieuw instellen
Stel het identiteits type van de automanage-account opnieuw in met de Azure Resource Manager ARM-sjabloon hieronder. Sla het bestand lokaal op `armdeploy.json` of op dezelfde manier op. Noteer de naam en locatie van uw account voor automanage. Dit zijn de vereiste para meters in de ARM-sjabloon.

1. Maak een nieuwe ARM-implementatie met de onderstaande sjabloon en gebruik `identityType = None`
    * U kunt dit doen met Azure CLI met `az deployment sub create` . Meer informatie over de `az deployment sub` opdracht [vindt u hier](https://docs.microsoft.com/cli/azure/deployment/sub).
    * U kunt dit ook doen met Power shell met behulp van de `New-AzDeployment` module. Meer informatie over de `New AzDeployment` module [vindt u hier](https://docs.microsoft.com/powershell/module/az.resources/new-azdeployment).

1. Voer dezelfde ARM-sjabloon opnieuw uit met `identityType = SystemAssigned`

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>Stap 2: de juiste rollen toewijzen voor het account voor automatisch beheren
Voor het account voor de functie voor automanaged moet de Inzender-en resource beleid contributor-rollen in het abonnement met de Vm's die door automanage worden beheerd, worden beheerd. U kunt deze rollen toewijzen met behulp van de Azure Portal, ARM-sjablonen of Azure CLI.

Als u een ARM-sjabloon of Azure CLI gebruikt, hebt u de principal-ID (ook wel object-ID genoemd) van uw account voor automatisch beheer nodig (dit is niet nodig als u de Azure Portal gebruikt). U kunt de principal-ID (object-ID) van uw account voor het autobeheren van de volgende methoden vinden:

- [Azure cli](https://docs.microsoft.com/cli/azure/ad/sp): gebruik de opdracht `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: Navigeer naar **Azure Active Directory** en zoek uw account voor automanage op naam. Selecteer onder **bedrijfs toepassingen** de naam van het account voor automanage wanneer het wordt weer gegeven.

### <a name="azure-portal"></a>Azure Portal
1. Ga onder **abonnementen** naar het abonnement met uw door u beheerde vm's.
1. Navigeer naar **toegangs beheer (IAM)**.
1. Klik op Roltoewijzingen **toevoegen**.
1. Selecteer de rol **Inzender** en typ de naam van uw account voor automanage.
1. Klik op **Opslaan**.
1. Herhaal stap 3-5 en deze keer met de rol **Inzender voor resource beleid** .

### <a name="arm-template"></a>ARM-sjabloon
Voer de volgende ARM-sjabloon uit. U hebt de principal-ID van uw account voor automatisch beheren nodig om de principal-ID hierboven weer te geven. Voer de waarde in wanneer u hierom wordt gevraagd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
Voer de volgende opdrachten uit:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Contributor" --scope /subscriptions/<your subscription id>

az role assignment create --assignee-object-id <your Automanage Account's object id> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription id>
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over Azure automanage [vindt u hier](./automanage-virtual-machines.md).
