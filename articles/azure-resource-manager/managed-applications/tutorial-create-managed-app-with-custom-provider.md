---
title: Zelf studie-aangepaste acties & resources
description: In deze zelf studie wordt beschreven hoe u een door Azure beheerde toepassing maakt met een aangepaste Azure-provider.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: c3750da6bd76c8cb3908fbdc71ba676f09d77def
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75650077"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Zelf studie: een beheerde toepassing maken met aangepaste acties en resources

In deze zelf studie maakt u uw eigen beheerde toepassing met aangepaste acties en resources. De beheerde toepassing bevat een aangepaste actie op de `Overview` pagina, een aangepast resource type dat wordt weer gegeven als een afzonderlijke menu `Table of Content` opdracht in en een aangepaste context actie op de aangepaste resource pagina.

Deze zelf studie bevat de volgende stappen:

> [!div class="checklist"]
> * Het definitie bestand van de gebruikers interface voor het maken van een exemplaar van een beheerde toepassing
> * Implementatie sjabloon voor ontwerpen met [aangepaste Azure-provider](../custom-providers/overview.md), Azure Storage-account en Azure-functie
> * Ontwerp weergave definitie artefact met aangepaste acties en resources
> * Een definitie van een beheerde toepassing implementeren
> * Een exemplaar van een beheerde toepassing implementeren
> * Aangepaste acties uitvoeren en aangepaste resources maken

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie moet u het volgende weten:

* [Een definitie van een beheerde toepassing maken en publiceren](publish-service-catalog-app.md).
* De [Service Catalog-app implementeren via Azure Portal](deploy-service-catalog-quickstart.md).
* [Azure Portal gebruikers interface maken voor uw beheerde toepassing](create-uidefinition-overview.md).
* Opties [voor definitie artefacten weer geven](concepts-view-definition.md) .
* Mogelijkheden van de [aangepaste Azure-provider](../custom-providers/overview.md) .

## <a name="user-interface-definition"></a>Definitie van de gebruikers interface

In deze zelf studie maakt u een beheerde toepassing en de bijbehorende beheerde resource groep bevat aangepast provider exemplaar, opslag account en functie. De Azure-functie die in dit voor beeld wordt gebruikt, implementeert een API die aangepaste provider bewerkingen verwerkt voor acties en resources. Azure Storage-account wordt gebruikt als basis opslag voor de resources van uw aangepaste provider.

De definitie van de gebruikers interface voor het maken van een `funcname` exemplaar `storagename` van een beheerde toepassing bevat en de invoer elementen. De naam van het opslag account en de functie naam moeten wereld wijd uniek zijn. Standaard functie bestanden worden geïmplementeerd vanuit een [voorbeeld functie pakket](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip), maar u kunt dit wijzigen door een INPUT-element toe te voegen voor een pakket koppeling in *createUIDefinition. json*:

```json
{
  "name": "funcname",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the function to be created",
  "toolTip": "Name of the function to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "storagename",
  "type": "Microsoft.Common.TextBox",
  "label": "Name of the storage to be created",
  "toolTip": "Name of the storage to be created",
  "visible": true,
  "constraints": {
    "required": true
  }
},
{
  "name": "zipFileBlobUri",
  "type": "Microsoft.Common.TextBox",
  "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
  "label": "The Uri to the uploaded function zip file",
  "toolTip": "The Uri to the uploaded function zip file",
  "visible": true
}
```

en uitvoer in *createUIDefinition. json*:

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

U kunt het volledige *createUIDefinition. json* -voor beeld vinden in de [verwijzing: elementen artefacten van de gebruikers interface](reference-createuidefinition-artifact.md).

## <a name="template-with-custom-provider"></a>Sjabloon met aangepaste provider

Als u een exemplaar van een beheerde toepassing met een aangepaste provider wilt maken, moet u een aangepaste provider resource met de naam **openbaar** en type **micro soft. CustomProviders/ResourceProviders** in uw **mainTemplate. json**definiëren. In die resource definieert u de resource typen en acties voor uw service. Als u Azure-functie-en Azure Storage-account exemplaren wilt `Microsoft.Web/sites` implementeren `Microsoft.Storage/storageAccounts` , definieert u de resources van het type en respectievelijk.

In deze zelf studie `users` maakt u één resource type, `ping` aangepaste actie en `users/contextAction` aangepaste actie die worden uitgevoerd in een context van een `users` aangepaste resource. Geef voor elk resource type en elke actie een eind punt op dat verwijst naar de functie met de naam die is geleverd in [createUIDefinition. json](#user-interface-definition). Geef de **routingType** `Proxy,Cache` op voor de resource typen `Proxy` en voor acties:

```json
{
  "apiVersion": "[variables('customrpApiversion')]",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[variables('customProviderName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      },
      {
        "name": "users/contextAction",
        "routingType": "Proxy",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[listSecrets(resourceId('Microsoft.Web/sites/functions', parameters('funcname'), 'HttpTrigger1'), '2018-02-01').trigger_url]"
      }
    ]
  },
  "dependsOn": [
    "[concat('Microsoft.Web/sites/',parameters('funcname'))]"
  ]
}
```

U kunt het volledige *mainTemplate. json* -voor beeld vinden op [Referentie: implementatie sjabloon artefact](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Definitie-artefact weergeven

Als u gebruikers interface wilt definiëren die aangepaste acties en aangepaste resources in uw beheerde toepassing bevat, moet u **viewDefinition. json** -artefact maken. Zie [definitie artefact in azure Managed Applications weer geven](concepts-view-definition.md)voor meer informatie over het weer geven van definitie-artefacten.

In deze zelf studie definieert u het volgende:
* Een *overzichts* pagina met een werkbalk knop die een aangepaste `TestAction` actie vertegenwoordigt met de basis tekst invoer.
* Een pagina met *gebruikers* die een aangepast resource type `users`vertegenwoordigt.
* Een aangepaste resource actie `users/contextAction` op de pagina *gebruikers* die wordt uitgevoerd in een context van een aangepaste bron van `users`het type.

In het volgende voor beeld ziet u de weergave configuratie voor een pagina overzicht:

```json
{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  }
```

Het onderstaande voor beeld bevat de pagina configuratie van resources van ' gebruikers ' met aangepaste resource actie:

```json
{
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }
```

Het volledige *viewDefinition. json* -voor beeld vindt u in de [verwijzing: View definition artefact](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Definitie van beheerde toepassing

Verpakken de volgende beheerde toepassings artefacten naar een zip-archief en upload deze naar opslag:

* createUiDefinition. json
* mainTemplate. json
* viewDefinition. json

Alle bestanden moeten op hoofd niveau zijn. Het pakket met artefacten kan in elke opslag ruimte worden opgeslagen, bijvoorbeeld GitHub BLOB of Azure Storage-account-blob. Hier volgt een script voor het uploaden van het toepassings pakket naar het opslag account: 

```powershell
$resourceGroup="appResourcesGroup"
$storageName="mystorageaccount$RANDOM"

# Sign in to your Azure subscription
Connect-AzAccount
# Create resource group for managed application definition and application package
New-AzResourceGroup -Name $resourceGroup -Location eastus

# Create storage account for a package with application artifacts
$storageAccount=New-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -SkuName Standard_LRS `
  -Location eastus `
$ctx=$storageAccount.Context

# Create storage container and upload zip to blob
New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob
Set-AzStorageBlobContent `
  -File "path_to_your_zip_package" `
  -Container appcontainer `
  -Blob app.zip `
  -Context $ctx 

# Get blob absolute uri
$blobUri=(Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx).ICloudBlob.uri.AbsoluteUri
```

Voer hieronder het Azure CLI-script uit of volg de stappen in Azure Portal een definitie van een door een service catalogus beheerde toepassing te implementeren:

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli-interactive)

```azurecli-interactive
resourceGroup="appResourcesGroup"
# Select subscription and create resource group (if you have not created yet)
az account set --subscription <subscriptionID>
az group create --name $resourceGroup --location eastus

# Get object ID of your identity
userid=$(az ad user show --upn-or-object-id example@contoso.org --query objectId --output tsv)
# Get role definition ID for the Owner role
roleid=$(az role definition list --name Owner --query [].name --output tsv)

# Create managed application definition resource
az managedapp definition create \
  --name "ManagedUsersAppDefinition" \
  --location "eastus" \
  --resource-group $resourceGroup \
  --lock-level ReadOnly \
  --display-name "Managed users app definition" \
  --description "Managed application with Azure Custom Provider" \
  --authorizations "$userid:$roleid" \
  --package-file-uri "path to your app.zip package"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in het Azure Portal **alle services**. In de lijst met resources typt en selecteert u **beheerd toepassingen centrum**.
2. Kies **Service Catalog Application Definition** in het **beheerde toepassingen centrum**en klik op **toevoegen**. 
    
    ![Service catalogus toevoegen](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Geef waarden op voor het maken van een service catalogus definitie:

    * Geef een unieke **naam** op voor de Service catalogus definitie, **weergave naam** en *Beschrijving*(optioneel).
    * Selecteer het **abonnement**, de **resource groep**en de **locatie** waar de toepassings definitie wordt gemaakt. U kunt dezelfde resource groep gebruiken die wordt gebruikt voor een zip-pakket of voor het maken van een nieuwe resource groep.
    * Geef voor de URI van een **pakket bestand**het pad op naar het zip-bestand dat u in de vorige stap hebt gemaakt.

    ![Waarden opgeven](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Wanneer u de sectie verificatie en vergrendelings niveau krijgt, selecteert u **autorisatie toevoegen**.

    ![Autorisatie toevoegen](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Selecteer een Azure Active Directory groep om de resources te beheren en selecteer **OK**.

   ![Autorisatie groep toevoegen](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Wanneer u alle waarden hebt ingevoerd, selecteert u **maken**.

   ![Definitie van beheerde toepassing maken](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Exemplaar van beheerde toepassing

Wanneer de definitie van de beheerde toepassing is geïmplementeerd, voert u het onderstaande script uit of volgt u de stappen in Azure Portal om uw exemplaar van de beheerde toepassing te implementeren met een aangepaste provider:

# <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli-interactive)

```azurecli-interactive
appResourcesGroup="appResourcesGroup"
applicationGroup="usersApplicationGroup"

# Create resource group for managed application instance
az group create --name $applicationGroup --location eastus

# Get ID of managed application definition
appid=$(az managedapp definition show --name ManagedUsersAppDefinition --resource-group $appResourcesGroup --query id --output tsv)

# Create the managed application
az managedapp create \
  --name ManagedUsersApp \
  --location "eastus" \
  --kind "Servicecatalog" \
  --resource-group $applicationGroup \
  --managedapp-definition-id $appid \
  --managed-rg-id "managedResourcesGroup" \
  --parameters "{\"funcname\": {\"value\": \"managedusersappfunction\"}, \"storageName\": {\"value\": \"managedusersappstorage\"}}"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer in het Azure Portal **alle services**. In de lijst met resources typt en selecteert u **beheerd toepassingen centrum**.
2. Kies **Service Catalog-toepassingen** in het **beheerde toepassingen centrum**en klik op **toevoegen**. 

    ![Beheerde toepassing toevoegen](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. Op het pagina type **Service catalogus-toepassingen** geeft u de naam van de Service catalogus definitie weer in het zoekvak. Selecteer de definitie die u in de vorige stap hebt gemaakt en klik op **maken**.

    ![Servicecatalogus selecteren](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Geef waarden op voor het maken van een exemplaar van een beheerde toepassing vanuit de Service catalogus definitie:

    * Selecteer het **abonnement**, de **resource groep**en de **locatie** waar het toepassings exemplaar wordt gemaakt.
    * Geef een unieke naam op voor de Azure-functie en de Azure Storage account.

    ![Toepassingsinstellingen](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Wanneer de validatie is voltooid, klikt u op **OK** om een exemplaar van een beheerde toepassing te implementeren. 
    
    ![Beheerde toepassing implementeren](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Aangepaste acties en resources

Nadat het exemplaar van de Service catalogus toepassing is geïmplementeerd, hebt u twee nieuwe resource groepen. De eerste resource `applicationGroup` groep bevat een exemplaar van de beheerde toepassing, de tweede `managedResourceGroup` resource groep bevat de resources voor de beheerde toepassing, met inbegrip van een **aangepaste provider**.

![Toepassings resource groepen](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

U kunt naar het exemplaar van de beheerde toepassing gaan en **aangepaste actie** uitvoeren op de pagina overzicht, **gebruikers** aangepaste resource maken op de pagina gebruikers en **aangepaste context actie** uitvoeren op aangepaste resource.

* Ga naar de pagina overzicht en klik op de knop ping actie:

![Aangepaste actie uitvoeren](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Ga naar de pagina gebruikers en klik op de knop toevoegen. Geef invoer voor het maken van een resource op en verzend het formulier:

![Aangepaste resource maken](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Ga naar de pagina gebruikers, selecteer een resource voor gebruikers en klik op aangepaste context actie:

![Aangepaste resource maken](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Zoeken naar Help

Als u vragen hebt over Azure Managed Applications, kunt u vragen om [stack overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Er is mogelijk al een vergelijk bare vraag gesteld en beantwoord, dus controleer eerst vóór het boeken. Voeg de tag `azure-managedapps` toe om een snel antwoord te krijgen.

## <a name="next-steps"></a>Volgende stappen

Zie [Door Azure beheerde toepassingen in Marketplace](publish-marketplace-app.md) voor het in Azure Marketplace publiceren van uw beheerde toepassing.

Meer informatie over [aangepaste Azure-providers](../custom-providers/overview.md).
