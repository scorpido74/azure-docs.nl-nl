---
title: Zelfstudie - aangepaste acties & resources
description: In deze zelfstudie wordt beschreven hoe u een Azure Managed Application maakt met een Azure Custom Provider.
ms.topic: tutorial
ms.author: lazinnat
author: lazinnat
ms.date: 06/20/2019
ms.openlocfilehash: c3750da6bd76c8cb3908fbdc71ba676f09d77def
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650077"
---
# <a name="tutorial-create-managed-application-with-custom-actions-and-resources"></a>Zelfstudie: Beheerde toepassing maken met aangepaste acties en resources

In deze zelfstudie maakt u uw eigen beheerde toepassing met aangepaste acties en resources. De beheerde toepassing bevat een `Overview` aangepaste actie op de pagina, een `Table of Content` aangepast brontype dat wordt weergegeven als een afzonderlijk menu-item in en een aangepaste contextactie op de aangepaste resourcepagina.

Deze zelfstudie bevat de volgende stappen:

> [!div class="checklist"]
> * Definitiebestand voor gebruikersinterface voor het maken van een beheerde toepassingsinstantie
> * Ontwerpimplementatiesjabloon met [Azure Custom Provider,](../custom-providers/overview.md)Azure Storage Account en Azure-functie
> * Auteur weergave definitie artefact met aangepaste acties en resources
> * Een beheerde toepassingsdefinitie implementeren
> * Een instantie van beheerde toepassing implementeren
> * Aangepaste acties uitvoeren en aangepaste resources maken

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, moet je het weten:

* Een [beheerde toepassingsdefinitie maken en publiceren](publish-service-catalog-app.md).
* [Servicecatalogus-app implementeren via Azure-portal](deploy-service-catalog-quickstart.md).
* De [gebruikersinterface van Azure-portalen maken voor uw beheerde toepassing.](create-uidefinition-overview.md)
* Bekijk de mogelijkheden [voor definitieartefacten.](concepts-view-definition.md)
* [Azure Custom](../custom-providers/overview.md) Provider-mogelijkheden.

## <a name="user-interface-definition"></a>Definitie van gebruikersinterface

In deze zelfstudie maakt u een beheerde toepassing en de beheerde brongroep bevat aangepaste providerinstantie, opslagaccount en functie. De Azure-functie die in dit voorbeeld wordt gebruikt, implementeert een API die aangepaste providerbewerkingen voor acties en resources verwerkt. Azure Storage Account wordt gebruikt als basisopslag voor uw aangepaste providerresources.

De definitie van de gebruikersinterface `funcname` voor `storagename` het maken van een beheerde toepassingsinstantie bevat en invoerelementen. De naam en de functienaam van het opslagaccount moeten wereldwijd uniek zijn. Standaard worden functiebestanden geïmplementeerd vanuit het [voorbeeldfunctiepakket,](https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip)maar u deze wijzigen door een invoerelement toe te voegen voor een pakketkoppeling in *createUIDefinition.json:*

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

en uitvoer in *createUIDefinition.json:*

```json
  "funcname": "[steps('applicationSettings').funcname]",
  "storageName": "[steps('applicationSettings').storagename]",
  "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
```

Het volledige *voorbeeld van createUIDefinition.json* is te vinden op [Reference: Artefacten voor gebruikersinterface-elementen.](reference-createuidefinition-artifact.md)

## <a name="template-with-custom-provider"></a>Sjabloon met aangepaste provider

Als u een beheerde toepassingsinstantie wilt maken met aangepaste provider, moet u aangepaste providerbronnen definiëren met een naam **openbaar** en **Microsoft.CustomProviders/resourceProviders** typen in uw **mainTemplate.json**. In die resource definieert u de resourcetypen en -acties voor uw service. Als u Azure Function- en Azure `Microsoft.Web/sites` Storage `Microsoft.Storage/storageAccounts` Account-exemplaren wilt implementeren, worden resources van type en respectievelijk gedefinieerd.

In deze zelfstudie maakt `users` u `ping` één resourcetype, aangepaste actie en `users/contextAction` aangepaste actie `users` die wordt uitgevoerd in een context van een aangepaste bron. Voor elk resourcetype en elke actie wordt een eindpunt opgegeven dat wijst op de functie met de naam die is opgegeven in [createUIDefinition.json](#user-interface-definition). Geef de **routingType** op `Proxy,Cache` `Proxy` als voor resourcetypen en voor acties:

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

Het volledige *voorbeeld van mainTemplate.json* is te vinden op [Referentie: Artefact van de sjabloon implementatie](reference-main-template-artifact.md).

## <a name="view-definition-artifact"></a>Definitie-artefact weergeven

Als u de gebruikersinterface wilt definiëren die aangepaste acties en aangepaste bronnen in uw beheerde toepassing bevat, moet u **viewDefinition.json-artefact maken.** Zie [Artefact definitie van weergave weergeven in Azure Managed Applications](concepts-view-definition.md)voor meer informatie over artefacten met de weergavedefinitie.

In deze zelfstudie definieert u:
* Een *overzichtspagina* met werkbalkknop `TestAction` die een aangepaste actie met basistekstinvoer vertegenwoordigt.
* Een *pagina Gebruikers* die een `users`aangepast resourcetype vertegenwoordigt.
* Een aangepaste `users/contextAction` bronactie in de pagina *Gebruikers* die wordt `users`uitgevoerd in een context van aangepaste bron van het type .

In het volgende voorbeeld ziet u de weergaveconfiguratie voor een pagina 'Overzicht':

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

Het onderstaande voorbeeld bevat de configuratie van de pagina 'Gebruikers' met aangepaste bronactie:

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

Het voorbeeld van de volledige *viewDefinition.json* is te vinden op [Referentie: Artefact voor definitie weergeven](reference-view-definition-artifact.md).

## <a name="managed-application-definition"></a>Beheerde toepassingsdefinitie

Verpak de volgende beheerde toepassingsartefacten om het archief te zippen en upload het naar opslag:

* createUiDefinition.json
* mainTemplate.json
* viewDefinition.json

Alle bestanden moeten op basisniveau zijn. Het pakket met artefacten kan worden opgeslagen in elke opslag, bijvoorbeeld GitHub blob of Azure Storage Account blob. Hier is een script om het toepassingspakket te uploaden naar een opslagaccount: 

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

Voer het Azure CLI-script hieronder uit of volg de stappen in de Azure-portal om een door de servicecatalogus beheerde toepassingsdefinitie te implementeren:

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

1. Selecteer **alle services**in de Azure-portal . Typ en selecteer **Managed Applications Center**in de lijst met resources .
2. Kies in het **Centrum beheerde toepassingen**de **optie Servicecatalogustoepassingsdefinitie** en klik op **Toevoegen**. 
    
    ![Servicecatalogus toevoegen](./media/tutorial-create-managed-app-with-custom-provider/service-catalog-managed-application.png)

3. Geef waarden op voor het maken van een definitie van servicecatalogus:

    * Geef een unieke **naam op** voor de definitie van de servicecatalogus, **weergavenaam** en *beschrijving*(optioneel).
    * Selecteer de **groep Abonnement**, **Resource**en **Locatie** waar de toepassingsdefinitie wordt gemaakt. U dezelfde resourcegroep gebruiken die wordt gebruikt voor een zip-pakket of een nieuwe resourcegroep maken.
    * Geef voor een **Pakketbestand Uri**het pad op naar het zip-bestand dat u in de vorige stap hebt gemaakt.

    ![Waarden opgeven](./media/tutorial-create-managed-app-with-custom-provider/add-service-catalog-managed-application.png)

4. Wanneer u de sectie Verificatie- en vergrendelingsniveau hebt bereikt, selecteert u **Autorisatie toevoegen**.

    ![Autorisatie toevoegen](./media/tutorial-create-managed-app-with-custom-provider/add-authorization.png)

5. Selecteer een Azure Active Directory-groep om de resources te beheren en selecteer **OK**.

   ![Autorisatiegroep toevoegen](./media/tutorial-create-managed-app-with-custom-provider/add-auth-group.png)

6. Wanneer u alle waarden hebt opgegeven, selecteert u **Maken**.

   ![Beheerde toepassingsdefinitie maken](./media/tutorial-create-managed-app-with-custom-provider/create-service-catalog-definition.png)

---

## <a name="managed-application-instance"></a>Exemplaar beheerde toepassing

Wanneer beheerde toepassingsdefinitie wordt geïmplementeerd, voert u het onderstaande script uit of volgt u de stappen in de Azure-portal om uw beheerde toepassingsinstantie te implementeren bij aangepaste provider:

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

1. Selecteer **alle services**in de Azure-portal . Typ en selecteer **Managed Applications Center**in de lijst met resources .
2. Kies **servicecatalogustoepassingen** in het **Beheerde toepassingencentrum**en klik op **Toevoegen**. 

    ![Beheerde toepassing toevoegen](./media/tutorial-create-managed-app-with-custom-provider/add-managed-application.png)

3. In het paginatype **Servicecatalogus-toepassingen** zet u de weergavenaam van de servicecataloguscatalogus in het zoekvak. Selecteer de definitie die in vorige stap is gemaakt en klik op **Maken**.

    ![Servicecatalogus selecteren](./media/tutorial-create-managed-app-with-custom-provider/select-service-catalog-definition.png)

4. Geef waarden op voor het maken van een beheerde toepassingsinstantie vanuit de definitie van servicecatalogus:

    * Selecteer de **groep Abonnement**, **Resource**en **Locatie** waar de toepassingsinstantie wordt gemaakt.
    * Geef een unieke Azure-functienaam en azure-opslagaccountnaam op.

    ![Toepassingsinstellingen](./media/tutorial-create-managed-app-with-custom-provider/application-settings.png)

5. Wanneer de validatie is geslaagd, klikt u op **OK** om een instantie van een beheerde toepassing te implementeren. 
    
    ![Beheerde toepassing implementeren](./media/tutorial-create-managed-app-with-custom-provider/deploy-managed-application.png)

---

## <a name="custom-actions-and-resources"></a>Aangepaste acties en resources

Nadat de instantie van de servicecatalogustoepassing is geïmplementeerd, hebt u twee nieuwe brongroepen. Eerste resourcegroep `applicationGroup` bevat een instantie van de `managedResourceGroup` beheerde toepassing, de tweede resourcegroep bevat de resources voor de beheerde toepassing, inclusief **aangepaste provider.**

![Groepen toepassingsbronnen](./media/tutorial-create-managed-app-with-custom-provider/application-resource-groups.png)

U naar beheerde toepassingsinstantie gaan en **aangepaste actie** uitvoeren op de pagina 'Overzicht', **aangepaste** bronnen voor gebruikers maken op de pagina 'Gebruikers' en **aangepaste contextactie** uitvoeren op aangepaste resource.

* Ga naar de pagina 'Overzicht' en klik op de knop 'Actie pingen':

![Aangepaste actie uitvoeren](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-action.png)

* Ga naar de pagina 'Gebruikers' en klik op de knop Toevoegen. Geef ingangen op voor het maken van een resource en dien het formulier in:

![Aangepaste resource maken](./media/tutorial-create-managed-app-with-custom-provider/create-custom-resource.png)

* Ga naar de pagina 'Gebruikers', selecteer een bron voor 'gebruikers' en klik op Actie voor aangepaste context:

![Aangepaste resource maken](./media/tutorial-create-managed-app-with-custom-provider/perform-custom-resource-action.png)

[!INCLUDE [clean-up-section-portal](../../../includes/clean-up-section-portal.md)]

## <a name="looking-for-help"></a>Op zoek naar hulp

Als u vragen hebt over Azure Managed Applications, probeert u vragen te stellen op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Een soortgelijke vraag kan al zijn gesteld en beantwoord, dus controleer eerst voor het posten. Voeg de `azure-managedapps` tag toe om snel te reageren!

## <a name="next-steps"></a>Volgende stappen

Zie [Door Azure beheerde toepassingen in Marketplace](publish-marketplace-app.md) voor het in Azure Marketplace publiceren van uw beheerde toepassing.

Meer informatie over [Azure Custom Providers](../custom-providers/overview.md).
