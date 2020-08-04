---
title: Automatisch een back-up maken van sleutel waarden uit Azure-app configuratie archief
description: Meer informatie over het instellen van een automatische back-up van sleutel waarden tussen app-configuratie archieven
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: ef777f235c85c423ea126fb7249f8e6a11492d3a
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528545"
---
# <a name="backup-app-configuration-stores-automatically"></a>Back-ups van app-configuratie archieven automatisch

In dit artikel leert u hoe u een automatische back-up van sleutel waarden vanuit een configuratie van een primaire app-archief naar een secundair archief kunt instellen. De oplossing maakt gebruik van de integratie van Azure Event Grid met app-configuratie. Wanneer de app-configuratie eenmaal is ingesteld, worden gebeurtenissen naar Event Grid gepubliceerd voor wijzigingen die zijn aangebracht in sleutel waarden in een configuratie opslag. Event Grid ondersteunt een groot aantal Azure-Services waarvan gebruikers zich kunnen abonneren op de gebeurtenissen die worden verzonden wanneer sleutel waarden worden gemaakt, bijgewerkt of verwijderd.

## <a name="overview"></a>Overzicht

In deze zelf studie gaat u een Azure Storage wachtrij gebruiken om gebeurtenissen van Event Grid te ontvangen en een timer trigger van Azure Functions te gebruiken voor het verwerken van gebeurtenissen in de opslag wachtrij in batches. Wanneer de functie wordt geactiveerd, worden de meest recente waarden opgehaald van de sleutels die zijn gewijzigd in het configuratie archief van de primaire app en wordt de secundaire opslag dienovereenkomstig bijgewerkt. Deze installatie helpt bij het combi neren van meerdere wijzigingen in een korte periode in één back-upbewerking en Vermijd buitensporige aanvragen voor uw app-configuratie archieven.

![Back-uparchitectuur voor app-configuratie opslag](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Inrichten van resources

De motivatie achter back-ups van app-configuratie archieven is het gebruik van meerdere configuratie archieven in verschillende Azure-regio's om de geo-tolerantie van uw toepassing te verg Roten. Om dit te kunnen doen, moeten uw primaire en secundaire winkels zich in verschillende Azure-regio's bevinden. Alle andere resources die u in deze zelf studie hebt gemaakt, kunnen in elke gewenste regio worden ingericht. Dit komt doordat als de primaire regio niet beschikbaar is, er geen nieuwe back-up meer kan worden gemaakt totdat de primaire regio weer toegankelijk is.

In deze zelf studie maakt u een secundair archief in de `centralus` regio en alle andere resources in de `westus` regio.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak een gratis versie](https://azure.microsoft.com/free/). U kunt eventueel de Azure Cloud Shell gebruiken.
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) met de werk belasting Azure Development.
- Down load en installeer de [.net core SDK](https://dotnet.microsoft.com/download).
- Nieuwste versie van Azure CLI (2.3.1 of hoger). Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u Azure CLI gebruikt, moet u zich eerst aanmelden met `az login` . U kunt eventueel de Azure Cloud Shell gebruiken.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Een resourcegroep maken

De resourcegroep is een logische verzameling waarin Azure-resources worden geïmplementeerd en beheerd.

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group).

In het volgende voorbeeld wordt een resourcegroep met de naam `<resource_group_name>` gemaakt op de locatie `westus`.  Vervang `<resource_group_name>` door een unieke naam voor uw resourcegroep.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>App-configuratie archieven maken

Maak uw primaire en secundaire app-configuratie archieven in verschillende regio's.
Vervang  `<primary_appconfig_name>` en `<secondary_appconfig_name>` door unieke namen voor uw configuratie archieven. De archief naam moet uniek zijn omdat deze wordt gebruikt als een DNS-naam.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-azure-storage-queue"></a>Azure Storage wachtrij maken

Maak een opslag account en een opslag wachtrij voor het ontvangen van de gebeurtenissen die worden gepubliceerd door Event Grid.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Abonneren op gebeurtenissen in het configuratie archief van uw app

U abonneert u op deze twee gebeurtenissen vanuit het configuratie archief van de primaire app:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

Met de volgende opdracht maakt u een Event Grid-abonnement voor de twee gebeurtenissen die naar uw opslag wachtrij worden verzonden, waarbij het eindpunt type is ingesteld op `storagequeue` en het eind punt wordt ingesteld op de wachtrij-id. Vervang door `<event_subscription_name>` de naam van uw keuze voor het gebeurtenis abonnement.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>Azure Functions maken voor het afhandelen van gebeurtenissen uit de opslag wachtrij

### <a name="setup-with-ready-to-use-azure-functions"></a>Instellen met kant-en-klare Azure Functions

In deze zelf studie werkt u met C# Azure Functions met de volgende eigenschappen:
- Runtime stack .NET Core 3,1
- Azure Functions runtime versie 3. x
- De functie wordt elke 10 minuten geactiveerd door de timer

Om het gemakkelijker te maken om een back-up van uw gegevens te beginnen, hebben we [Azure functions](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) getest en gepubliceerd, die u kunt gebruiken zonder wijzigingen in de code aan te brengen. Down load de project bestanden en [Publiceer deze naar uw eigen Azure-functie-app vanuit Visual Studio.](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> Breng geen wijzigingen aan in de omgevings variabelen in de code die u hebt gedownload. In de volgende sectie gaat u de vereiste app-instellingen maken.
>

### <a name="build-your-own-azure-functions"></a>Bouw uw eigen Azure Functions

Als de hierboven vermelde voorbeeld code niet aan uw vereisten voldoet, kunt u ook uw eigen Azure Functions maken. De functie moet in staat zijn om de volgende taken uit te voeren om de back-up te volt ooien:
- Lees de inhoud van uw opslag wachtrij regel matig om te zien of deze meldingen van Event Grid bevat. Raadpleeg de [opslag wachtrij-SDK](/azure/storage/queues/storage-quickstart-queues-dotnet) voor meer informatie over de implementatie.
- Als uw opslag wachtrij [gebeurtenis meldingen bevat van Event grid](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema), extraheert u alle unieke <sleutel, label> van gebeurtenis berichten. De combi natie van sleutel en label is de unieke id voor het wijzigen van de sleutel waarde in het primaire archief.
- Alle instellingen van het primaire archief lezen. Werk alleen de instellingen in het secundaire archief bij die een corresponderende gebeurtenis in de opslag wachtrij hebben. Verwijder alle instellingen van het secundaire archief die aanwezig waren in de opslag wachtrij, maar niet in het primaire archief. U kunt gebruikmaken van de [app-configuratie-SDK](https://github.com/Azure/AppConfiguration#sdks) om via een programma toegang te krijgen tot uw configuratie archieven.
- Berichten uit de opslag wachtrij verwijderen als er geen uitzonde ringen zijn tijdens de verwerking.
- Zorg ervoor dat u de fout afhandeling implementeert volgens uw behoeften. Raadpleeg de bovenstaande voorbeeld code voor een aantal veelvoorkomende uitzonde ringen die u kunt afhandelen.

Zie voor meer informatie over het maken van Azure Functions: [een functie in azure maken die wordt geactiveerd door een timer](/azure/azure-functions/functions-create-scheduled-function) en [Azure functions ontwikkelen met behulp van Visual Studio](/azure/azure-functions/functions-develop-vs).


> [!IMPORTANT]
> Gebruik uw beste beoordeling om het timer schema te kiezen op basis van hoe vaak u wijzigingen aanbrengt in uw primaire configuratie opslag. Houd er rekening mee dat het uitvoeren van de functie te vaak kan leiden tot het beëindigen van het beperken van aanvragen voor uw Store.
>


## <a name="create-azure-function-app-settings"></a>Azure functie-app-instellingen maken

Als u gebruikmaakt van de Azure Functions die u hebt gegeven, hebt u de volgende app-instellingen nodig in uw Azure-functie-app:
- `PrimaryStoreEndpoint`: Het eind punt voor het configuratie archief van de primaire app. Bijvoorbeeld: `https://{primary_appconfig_name}.azconfig.io`
- `SecondaryStoreEndpoint`: Het eind punt voor de configuratie opslag van de secundaire app. Bijvoorbeeld: `https://{secondary_appconfig_name}.azconfig.io`
- `StorageQueueUri`: URI van opslag wachtrij. Bijvoorbeeld: `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`

Met de volgende opdracht worden de vereiste app-instellingen gemaakt in uw Azure functie-app. Vervang door `<function_app_name>` de naam van uw Azure-functie-app.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>Toegang verlenen tot de beheerde identiteit van Azure functie-app

Gebruik de volgende opdracht of [Azure Portal](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) een door het systeem toegewezen beheerde identiteit voor uw Azure functie-app toe te voegen.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Voor het uitvoeren van de vereiste functie voor het maken van resources en het beheer van rollen moet uw account `'Owner'` machtigingen hebben op het juiste bereik (uw abonnement of resource groep). Als u hulp nodig hebt bij roltoewijzing, leert u [hoe u Azure-roltoewijzingen toevoegt of verwijdert met behulp van de Azure Portal](/azure/role-based-access-control/role-assignments-portal).

Gebruik de volgende opdrachten of [Azure Portal](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) om de beheerde identiteit van uw Azure-functie-app toegang te verlenen tot uw app-configuratie archieven.
- Wijs `App Configuration Data Reader` de rol toe in het configuratie archief van de primaire app.
- Wijs `App Configuration Data Owner` de rol toe in het configuratie archief van de secundaire app.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Gebruik de volgende opdracht of [Azure Portal](/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal) om de beheerde identiteit van uw Azure functie-app toegang tot uw opslag wachtrij te verlenen. 
- Wijs `Storage Queue Data Contributor` de rol toe aan de opslag wachtrij.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Een configuratie gebeurtenis voor een app activeren

Als u wilt testen of alles werkt, kunt u een sleutel waarde in het primaire archief maken/bijwerken/verwijderen. U moet deze wijziging in het secundaire archief een paar seconden weer geven nadat Azure Functions door de timer wordt geactiveerd.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

U hebt de gebeurtenis geactiveerd, en in een paar seconden Event Grid wordt de gebeurtenis melding naar uw Azure Storage wachtrij verzonden. Bekijk ***na de volgende geplande uitvoering van uw Azure functions***de configuratie-instellingen in het secundaire archief om te zien of deze de bijgewerkte sleutel waarde uit het primaire archief bevat.

> [!NOTE]
> U kunt [uw Azure functions hand matig activeren](/azure/azure-functions/functions-manually-run-non-http) tijdens het testen en oplossen van problemen, zonder dat u hoeft te wachten op de geplande timer trigger.

Nadat u hebt gecontroleerd of de back-upfunctie is uitgevoerd, kunt u zien dat de sleutel nu aanwezig is in uw secundaire opslag.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Problemen oplossen

Als u de nieuwe instelling niet ziet in uw secundaire archief:

- Zorg ervoor dat de back-upfunctie is geactiveerd ***nadat*** u de instelling in uw primaire archief hebt gemaakt.
- Het is mogelijk dat Event Grid de gebeurtenis melding niet op tijd kon verzenden naar de opslag wachtrij. Controleer of uw opslag wachtrij nog steeds de gebeurtenis melding van uw primaire archief bevat. als dit het geval is, moet u de back-upfunctie opnieuw activeren.
- Controleer [Azure functions logboeken](/azure/azure-functions/functions-create-scheduled-function#test-the-function) op fouten of waarschuwingen.
- Gebruik [Azure Portal](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) om ervoor te zorgen dat de Azure-functie-app de juiste waarden bevat voor de toepassings instellingen die uw Azure functions probeert te lezen.
- U kunt ook bewaking en waarschuwingen voor uw Azure Functions instellen met behulp van [Azure-toepassing Insights.](/azure/azure-functions/functions-monitoring?tabs=cmd) 


## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent om verder te gaan met deze app-configuratie en het gebeurtenis abonnement, moet u de resources die in dit artikel zijn gemaakt, niet opschonen. Als u niet verder wilt werken, gebruikt u de volgende opdracht om de resources te verwijderen die u in dit artikel hebt gemaakt.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u een automatische back-up van uw sleutel waarden instelt, vindt u meer informatie over hoe u de geo-tolerantie van uw toepassing kunt verhogen:

- [Tolerantie en herstel na noodgevallen](concept-disaster-recovery.md)
