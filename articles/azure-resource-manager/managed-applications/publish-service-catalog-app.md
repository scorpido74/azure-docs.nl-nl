---
title: Beheerde app servicecatalogus publiceren
description: In dit artikel leest u hoe u een beheerde Azure-toepassing maakt die is bedoeld voor leden van uw organisatie.
author: tfitzmac
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: 48aaca64949aafecff27c76ad7572b3c2fa44732
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391512"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Snelstart: een beheerde toepassingsdefinitie maken en publiceren

Deze quickstart biedt een inleiding tot het werken met [Azure Managed Applications.](overview.md) U een beheerde toepassing maken en publiceren die is bedoeld voor leden van uw organisatie.

Als u een beheerde toepassing wilt publiceren in uw servicecatalogus, moet u het als:

* Een sjabloon maken die de resources definieert die met de beheerde toepassing moeten worden geïmplementeerd.
* De elementen van de gebruikersinterface voor de portal definiëren bij het implementeren van de beheerde toepassing.
* Een ZIP-pakket met de vereiste sjabloonbestanden maken.
* Bepalen welke gebruiker, groep of toepassing toegang moet hebben tot de resourcegroep in het abonnement van de gebruiker.
* De definitie van de beheerde toepassing maken die verwijst naar het ZIP-pakket en toegang voor de identiteit aanvraagt.

## <a name="create-the-arm-template"></a>De ARM-sjabloon maken

De definitie van een beheerde toepassing bevat altijd een bestand met de naam **mainTemplate.json**. Hierin definieert u de Azure-resources die u wilt implementeren. De sjabloon is niet anders dan een gewone ARM-sjabloon (Azure Resource Manager).

Maak een bestand met de naam **mainTemplate.json**. De naam is hoofdlettergevoelig.

Voeg de volgende JSON-code toe aan uw bestand. Hiermee definieert u de parameters voor het maken van een opslagaccount, en geeft u de eigenschappen voor het opslagaccount op.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
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
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
        }
    }
}
```

Sla het bestand mainTemplate.json op.

## <a name="define-your-create-experience"></a>Definieer uw maakervaring

Als uitgever definieert u de portalervaring voor het maken van de beheerde toepassing. Het **bestand createUiDefinition.json** genereert de portalinterface. U definieert hoe gebruikers invoer voor elke parameter leveren met behulp van [besturingselementelementen,](create-uidefinition-elements.md) waaronder vervolgkeuzelijsten, tekstvakken en wachtwoordvakken.

Een bestand met de naam **createUiDefinition.json** maken (deze naam is hoofdlettergevoelig)

Voeg de volgende starter JSON toe aan het bestand en sla het op.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

Zie [Aan de slag met CreateUiDefinition voor](create-uidefinition-overview.md)meer informatie.

## <a name="package-the-files"></a>De bestanden verpakken

Voeg de twee bestanden toe aan een ZIP-bestand met de naam app.zip. De twee bestanden moeten zich in de hoofdmap van het ZIP-bestand bevinden. Als u ze in een map opslaat, treedt er een fout op bij het maken van de definitie van de beheerde toepassing met de mededeling dat de vereiste bestanden niet zijn gevonden.

Upload het pakket naar een toegankelijke locatie vanaf waar het pakket kan worden gebruikt. Je moet een unieke naam opgeven voor het opslagaccount.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>De definitie van de beheerde toepassing maken

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Een Azure Active Directory-gebruikersgroep of -toepassing maken

De volgende stap is het selecteren van een gebruikersgroep, gebruiker of toepassing voor het beheren van de resources voor de klant. Deze identiteit heeft machtigingen voor de beheerde resourcegroep die overeenkomen met de toegewezen rol. De rol kan elke ingebouwde rol van op rollen gebaseerd toegangsbeheer (RBAC) zijn, zoals Eigenaar of Inzender. Zie [Een groep maken en leden toevoegen in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) voor instructies voor het maken van een nieuwe Active Directory-gebruikersgroep.

U hebt de object-id van de gebruikersgroep nodig om de resources te beheren. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>De roldefinitie-id opvragen

Vervolgens hebt u de roldefinitie-id nodig van de ingebouwde RBAC-rol waarmee u toegang wilt verlenen aan de gebruiker, gebruikersgroep of toepassing. Meestal gebruikt u de rol van eigenaar, inzender of lezer. In de volgende opdracht ziet u hoe u de roldefinitie-id ophaalt voor de rol van eigenaar:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>De definitie van de beheerde toepassing maken

Als u nog geen resourcegroep hebt voor het opslaan van de definitie van de beheerde toepassing, maakt u er nu een:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Maak nu de definitieresource van de beheerde toepassing.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

Wanneer de opdracht is voltooid, hebt u de definitie van een beheerde toepassing in de resourcegroep.

Enkele parameters die in het voorgaande voorbeeld worden gebruikt, zijn:

* **resourcegroep:** de naam van de resourcegroep waarin de beheerde toepassingsdefinitie wordt gemaakt.
* **vergrendelingsniveau**: het type vergrendeling dat op de beheerde resourcegroep wordt geplaatst. Hiermee voorkomt u dat de klant ongewenste bewerkingen uitvoert op deze resourcegroep. ReadOnly is momenteel het enige ondersteunde vergrendelingsniveau. Als ReadOnly is opgegeven, kan de klant alleen de resources die aanwezig zijn in de beheerde resourcegroep lezen. De uitgeversidentiteiten die toegang tot de beheerde resourcegroep krijgen, zijn uitgesloten van de vergrendeling.
* **authorizations**: beschrijft de principal-id en de roldefinitie-id die worden gebruikt om machtiging te verlenen aan de beheerde resourcegroep. Wordt opgegeven in de `<principalId>:<roleDefinitionId>`-indeling. Als meer dan één waarde is vereist, geeft u deze op in de vorm `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. De waarden worden gescheiden door een spatie.
* **pakketbestand URI:** De locatie van een .zip-pakket dat de vereiste bestanden bevat.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Breng uw eigen opslag mee voor de beheerde toepassingsdefinitie

U ervoor kiezen om uw beheerde toepassingsdefinitie op te slaan in een opslagaccount dat door u wordt verstrekt tijdens het maken, zodat de locatie en toegang volledig door u kunnen worden beheerd voor uw wettelijke behoeften.

> [!NOTE]
> Breng uw eigen opslag wordt alleen ondersteund met ARM Template of REST API implementaties van de beheerde toepassingsdefinitie.

### <a name="select-your-storage-account"></a>Selecteer uw opslagaccount

U moet [een opslagaccount maken](../../storage/common/storage-account-create.md) om de beheerde toepassingsdefinitie te bevatten voor gebruik met Service Catalog.

Kopieer de bron-id van het opslagaccount. Het zal later worden gebruikt bij het implementeren van de definitie.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>De roltoewijzing voor 'Toestelserviceprovider' instellen in uw opslagaccount

Voordat uw beheerde toepassingsdefinitie kan worden geïmplementeerd in uw opslagaccount, moet u inzendermachtigingen geven aan de rol **van toestelbronprovider,** zodat de definitiebestanden naar de container van uw opslagaccount kunnen worden geschreven.

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw opslagaccount.
1. Selecteer **Toegangsbeheer (IAM)** om de toegangscontrole-instellingen voor het opslagaccount weer te geven. Selecteer het tabblad **Toewijzingen van rollen** om de lijst met roltoewijzingen weer te geven.
1. Selecteer in het venster **Roltoewijzing toevoegen** de rol **Inzender.** 
1. Selecteer **azure** **AD-gebruiker, -groep of serviceprincipal**in het veld Toegang toewijzen tot veld .
1. Zoek **onder Selecteren**naar de rol **Toestelresourceprovider** en selecteer deze.
1. Sla de roltoewijzing op.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>De beheerde toepassingsdefinitie implementeren met een ARM-sjabloon 

Gebruik de volgende ARM-sjabloon om uw verpakte beheerde toepassing te implementeren als een nieuwe beheerde toepassingsdefinitie in servicecatalogus waarvan de definitiebestanden worden opgeslagen en onderhouden in uw eigen opslagaccount:
   
```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "applicationName": {
            "type": "string",
            "metadata": {
                "description": "Managed Application name"
            }
        },
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
        "definitionStorageResourceID": {
            "type": "string",
            "metadata": {
                "description": "Storage account resource ID for where you're storing your definition"
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located."
            }
        }
    },
    "variables": {
        "lockLevel": "None",
        "description": "Sample Managed application definition",
        "displayName": "Sample Managed application definition",
        "managedApplicationDefinitionName": "[parameters('applicationName')]",
        "packageFileUri": "[parameters('_artifactsLocation')]",
        "defLocation": "[parameters('definitionStorageResourceID')]",
        "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
        "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/applicationDefinitions",
            "apiVersion": "2019-07-01",
            "name": "[variables('managedApplicationDefinitionName')]",
            "location": "[parameters('location')]",
            "properties": {
                "lockLevel": "[variables('lockLevel')]",
                "description": "[variables('description')]",
                "displayName": "[variables('displayName')]",
                "packageFileUri": "[variables('packageFileUri')]",
                "storageAccountId": "[variables('defLocation')]"
            }
        }
    ],
    "outputs": {}
}
```

We hebben een nieuwe eigenschap met de naam **storageAccountId** toegevoegd aan de eigenschappen van uw applicatieDefintion en bieden opslagaccount-ID waar u uw definitie in wilt opslaan als de waarde:

U controleren of de toepassingsdefinitiebestanden zijn opgeslagen in uw opgegeven opslagaccount in een container met de **titel toepassingsdefinities**.

> [!NOTE]
> Voor extra beveiliging u een definitie van beheerde toepassingen maken, deze opslaan in een [blob met Azure-opslagaccount waar versleuteling is ingeschakeld.](../../storage/common/storage-service-encryption.md) De definitie-inhoud wordt versleuteld via de versleutelingsopties van het opslagaccount. Alleen gebruikers met machtigingen voor het bestand kunnen de definitie in de servicecatalogus zien.

## <a name="make-sure-users-can-see-your-definition"></a>Ervoor zorgen dat gebruikers de definitie kunnen zien

U hebt toegang tot de definitie van de beheerde toepassing, maar u wilt controleren of andere gebruikers in uw organisatie hiertoe toegang hebben. Verleen hun minimaal de rol van Lezer voor de definitie. Mogelijk hebben ze dit toegangsniveau overgenomen van het abonnement of de resourcegroep. Als u wilt controleren wie toegang heeft tot de definitie en gebruikers of groepen wilt toevoegen, raadpleegt u [Op rollen gebaseerd toegangsbeheer gebruiken om de toegang tot de resources van uw Azure-abonnement te beheren](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Volgende stappen

U hebt de definitie van de beheerde toepassing gepubliceerd. Nu krijgt u informatie over het implementeren van een exemplaar van deze definitie.

> [!div class="nextstepaction"]
> [Snelstart: Servicecatalogus-app implementeren](deploy-service-catalog-quickstart.md)
