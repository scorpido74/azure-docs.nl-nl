---
title: Een blauw druk maken met Power shell
description: Gebruik Azure-blauw drukken om artefacten te maken, te definiëren en te implementeren met behulp van de Power shell.
ms.date: 08/21/2019
ms.topic: quickstart
ms.openlocfilehash: 4cc181a7e0b07448d6dd538cfb1621d1efa43a80
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960429"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Snelstartgids: een Azure Blueprint definiëren en toewijzen met Power shell

Als u leert hoe u blauwdrukken in Azure maakt en toewijst, kunnen er algemene patronen worden gedefinieerd voor de ontwikkeling van herbruikbare en snel implementeerbare configuraties op basis van Resource Manager-sjablonen, beleid, beveiliging en meer. In deze zelfstudie leert u hoe u Azure Blueprints gebruikt om algemene taken uit te voeren met betrekking tot het maken, publiceren en toewijzen van een blauwdruk binnen uw organisatie, zoals:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken en verschillende ondersteunde artefacten toevoegen
> - Wijzigingen aanbrengen aan een bestaande blauwdruk die nog een **Concept** is
> - Blauwdrukken markeren als gereed om toe te wijzen met **Gepubliceerd**
> - Een blauwdruk toewijzen aan een bestaand abonnement
> - De status en voortgang van een toegewezen blauwdruk controleren
> - Een blauwdruk verwijderen die aan een abonnement is toegewezen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als deze nog niet is geïnstalleerd, volgt u de instructies in [add de module AZ. Blue](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) voor het installeren en valideren van de module **AZ. blauw druk** van de PowerShell Gallery.

## <a name="create-a-blueprint"></a>Een blauwdruk maken

De eerste stap bij het definiëren van een standaardpatroon voor naleving is om een blauwdruk samen te stellen uit de beschikbare resources. U maakt een blauwdruk met de naam MyBlueprint om de rol en de beleidstoewijzingen voor het abonnement te configureren. Vervolgens voegt u een resourcegroep en een Resource Manager-sjabloon toe en voegt u een roltoewijzing aan de resourcegroep toe.

> [!NOTE]
> Wanneer u Power shell gebruikt, wordt het object _Blue Print_ eerst gemaakt. Voor elk _artefact_ dat wordt toegevoegd en parameters bevat, moeten de parameters vooraf worden gedefinieerd in de eerste _blauwdruk_.

1. Maak het eerste _blauwdruk_object. De para meter **BlueprintFile** maakt deel uit van een JSON-bestand dat eigenschappen bevat over de blauw druk, alle resource groepen die moeten worden gemaakt en alle para meters op het niveau van de blauw druk. De parameters worden tijdens het toewijzen ingesteld en gebruikt door de artefacten die in latere stappen worden toegevoegd.

   - JSON-bestand-blauw druk. json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
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
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - Power shell-opdracht

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Gebruik de bestands naam _blauw druk. json_ bij het programmatisch maken van uw blauw druk definities.
     > Deze bestands naam wordt gebruikt bij het aanroepen van [import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     Het object Blue Print wordt standaard gemaakt in het standaard abonnement. Gebruik para meter **ManagementGroupId**om de beheer groep op te geven. Gebruik para meter **SubscriptionId**om het abonnement op te geven.

1. Voeg de roltoewijzing toe aan het abonnement. De **ArtifactFile** definieert het _type_ artefact, de eigenschappen worden uitgelijnd op de roldefinitie-id en de principal-identiteiten worden door gegeven als een matrix met waarden. In het onderstaande voorbeeld worden de principal-identiteiten en de opgegeven rol geconfigureerd in een parameter die wordt ingesteld tijdens de toewijzing van de blauwdruk. In dit voorbeeld wordt de ingebouwde rol van _Inzender_ met een GUID van `b24988ac-6180-42a0-ab88-20f7382dd24c` gebruikt.

   - JSON-bestand-\artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - Power shell-opdracht

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Voeg de beleidstoewijzing toe aan het abonnement. De **ArtifactFile** definieert het _type_ artefact, de eigenschappen die worden uitgelijnd op een beleid of initiatief definitie en configureert de beleids toewijzing voor het gebruik van de gedefinieerde blauw drukken para meters voor configuratie tijdens de toewijzing van blauw druk. In dit voorbeeld wordt het ingebouwde beleid _Tag met standaardwaarde op resourcegroepen toepassen_ met een GUID van `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` gebruikt.

   - JSON-bestand-\artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - Power shell-opdracht

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Voeg nog een beleidstoewijzing toe voor de Storage-tag (gebruik hierbij de parameter _storageAccountType_ opnieuw) aan het abonnement. Deze aanvullende beleidstoewijzingsartefact laat zien dat een in de blauwdruk gedefinieerde parameter door meer dan één artefact kan worden gebruikt. In dit voorbeeld wordt **storageAccountType** gebruikt voor het instellen van een tag op de resourcegroep. Deze waarde geeft informatie over het opslagaccount dat in de volgende stap wordt gemaakt. In dit voorbeeld wordt het ingebouwde beleid _Tag met standaardwaarde op resourcegroepen toepassen_ met een GUID van `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` gebruikt.

   - JSON-bestand-\artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - Power shell-opdracht

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Voeg een sjabloon toe onder resourcegroep. De **TemplateFile** voor een resource manager-sjabloon bevat het normale JSON-onderdeel van de sjabloon. De sjabloon maakt ook opnieuw gebruik van de blauwdrukparameters **storageAccountType**, **tagName** en **tagValue** door ze allemaal door te geven aan de sjabloon. De blauw druk-para meters zijn beschikbaar voor de sjabloon met behulp van para meter **TemplateParameterFile** en in de JSON van de sjabloon die sleutel/waarde-paar wordt gebruikt om de waarde te injecteren. De blauw druk en sjabloon parameter namen kunnen hetzelfde zijn.

   - Bestand met JSON-Azure Resource Manager-sjabloon-\artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - Parameter bestand voor JSON-Azure Resource Manager sjabloon-\artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - Power shell-opdracht

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Voeg een roltoewijzing toe onder resourcegroep. Net als bij de vorige vermelding van een roltoewijzing wordt in het onderstaande voorbeeld de definitie-id van de rol **Eigenaar** gebruikt en krijgt deze een andere parameter van de blauwdruk. In dit voorbeeld wordt de ingebouwde rol van _Eigenaar_ met een GUID van `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` gebruikt.

   - JSON-bestand-\artifacts\roleOwner.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - Power shell-opdracht

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Een blauwdruk publiceren

Nu de artefacten zijn toegevoegd aan de blauwdruk, is het tijd om deze te publiceren. Als de blauwdruk wordt gepubliceerd, kan deze worden toegewezen aan een abonnement.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

De waarde voor `{BlueprintVersion}` is een reeks letters, cijfers en afbreekstreepjes (geen spaties of andere speciale tekens) met een maximale lengte van twintig tekens. Gebruik iets unieks en informatiefs als **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Een blauwdruk toewijzen

Zodra een blauw druk is gepubliceerd met Power shell, kan deze worden toegewezen aan een abonnement. Wijs de blauwdruk die u hebt gemaakt toe aan een van de abonnementen in uw beheergroephiërarchie. Als de blauwdruk is opgeslagen in een abonnement, kan deze alleen aan dat abonnement worden toegewezen. De para meter **blauw** drukken geeft aan welke blauw druk moet worden toegewezen. Als u de para meters naam, locatie, identiteit, vergren deling en blauw druk wilt opgeven, gebruikt u de overeenkomende Power shell-para meters op de `New-AzBlueprintAssignment` cmdlet of geeft u deze op in het JSON-bestand **AssignmentFile** .

1. Voer de blauwdrukimplementatie uit door deze toe te wijzen aan een abonnement. Als de para meters voor **inzenders** en **eigen aren** een matrix moeten hebben met objectid's van de principals waaraan de roltoewijzing moet worden toegekend, gebruikt u [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) voor het verzamelen van de objectid's voor gebruik in de **AssignmentFile** voor uw eigen gebruikers, groepen of service-principals.

   - JSON-bestand-blueprintAssignment. json

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - Power shell-opdracht

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Door een gebruiker toegewezen beheerde identiteit

     Een blauwdruktoewijzing kan ook gebruikmaken van een [door een gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md).
     In dit geval wordt het **identiteits** gedeelte van het JSON-toewijzings bestand als volgt gewijzigd. Vervang `{tenantId}`, `{subscriptionId}`, `{yourRG}`en `{userIdentity}` met uw tenantId, subscriptionId, naam van de resource groep en de naam van de door de gebruiker toegewezen beheerde identiteit.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     De **door een gebruiker toegewezen beheerde identiteit** kan in elk abonnement en in elke resourcegroep aanwezig zijn waarvoor de gebruiker die de blauwdruk toewijst, is gemachtigd.

     > [!IMPORTANT]
     > De door een gebruiker toegewezen beheerde identiteit wordt niet beheerd door blauwdrukken. Gebruikers zijn verantwoordelijk voor het toewijzen van voldoende rollen en machtigingen, anders mislukken de blauwdruktoewijzingen.

## <a name="unassign-a-blueprint"></a>De toewijzing van een blauwdruk ongedaan maken

U kunt een blauwdruk uit een abonnement verwijderen. Het verwijderen wordt vaak uitgevoerd als de artefactresources niet langer nodig zijn. Wanneer een blauwdruk wordt verwijderd, worden de artefacten die als onderdeel van die blauwdruk zijn toegewezen, achtergelaten. Als u een blauw druk toewijzing wilt verwijderen, gebruikt u de cmdlet `Remove-AzBlueprintAssignment`:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van een blauwdruk](./concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](./concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](./concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](./concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](./how-to/update-existing-assignments.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](./troubleshoot/general.md).