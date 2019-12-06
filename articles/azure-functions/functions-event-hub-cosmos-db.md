---
title: 'Zelf studie: Java-functies gebruiken met Azure Cosmos DB en Event Hubs'
description: In deze zelf studie wordt uitgelegd hoe u gebeurtenissen van Event Hubs kunt gebruiken om updates in Azure Cosmos DB te maken met behulp van een functie die is geschreven in Java.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.openlocfilehash: cef1d09f3365350240cb2ed879e4d41edec74aef
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849833"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Zelf studie: een functie maken in Java met een event hub-trigger en een Azure Cosmos DB uitvoer binding

In deze zelf studie ziet u hoe u Azure Functions kunt gebruiken om een Java-functie te maken waarmee een continue stroom van de Tempe ratuur en druk gegevens wordt geanalyseerd. Event hub-gebeurtenissen die de functie voor leesingen van de sensor vertegenwoordigen, activeren. De functie verwerkt de gebeurtenis gegevens en voegt vervolgens status vermeldingen toe aan een Azure Cosmos DB.

In deze zelf studie doet u het volgende:

> [!div class="checklist"]
> * Azure-resources maken en configureren met behulp van de Azure CLI.
> * Maak en test java-functies die communiceren met deze resources.
> * Implementeer uw functies in Azure en controleer ze met Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van deze zelf studie moet het volgende zijn geïnstalleerd:

* [Java Developer Kit](https://aka.ms/azure-jdks), versie 8
* [Apache Maven](https://maven.apache.org), versie 3,0 of hoger
* [Azure cli](/cli/azure/install-azure-cli) als u geen Cloud shell wilt gebruiken
* [Azure functions core tools](https://www.npmjs.com/package/azure-functions-core-tools) versie 2.6.666 of hoger

> [!IMPORTANT]
> De omgevings variabele `JAVA_HOME` moet worden ingesteld op de installatie locatie van de JDK om deze zelf studie te volt ooien.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Azure-resources maken

In deze zelf studie hebt u de volgende resources nodig:

* Een resource groep die de andere resources bevat
* Een Event Hubs naam ruimte, Event Hub en autorisatie regel
* Een Cosmos DB-account,-data base en-verzameling
* Een functie-app en een opslag account om deze te hosten

In de volgende secties ziet u hoe u deze resources maakt met behulp van de Azure CLI.

### <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Als u geen gebruik maakt van Cloud Shell, moet u de Azure CLI lokaal gebruiken om toegang te krijgen tot uw account. Gebruik de `az login` opdracht uit de bash-prompt om de aanmeldings ervaring op basis van de browser te starten. Als u toegang hebt tot meer dan één Azure-abonnement, stelt u de standaard waarde in op `az account set --subscription` gevolgd door de abonnements-ID.

### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

Maak vervolgens enkele omgevings variabelen voor de namen en de locatie van de resources die u maakt. Gebruik de volgende opdrachten om de tijdelijke aanduidingen `<value>` te vervangen door waarden van uw keuze. De waarden moeten voldoen aan de [naamgevings regels en beperkingen voor Azure-resources](/azure/architecture/best-practices/resource-naming). Gebruik voor de variabele `LOCATION` een van de waarden die door de `az functionapp list-consumption-locations` opdracht worden geproduceerd.

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

De rest van deze zelf studie maakt gebruik van deze variabelen. Houd er rekening mee dat deze variabelen alleen behouden blijven voor de duur van uw huidige Azure CLI-of Cloud Shell-sessie. U moet deze opdrachten opnieuw uitvoeren als u een ander lokaal Terminal venster gebruikt, of als er een time-out optreedt voor de Cloud Shell sessie.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Azure gebruikt resource groepen om alle gerelateerde resources in uw account te verzamelen. Op die manier kunt u ze als een eenheid weer geven en deze verwijderen met één opdracht wanneer u ermee klaar bent.

Gebruik de volgende opdracht om een resource groep te maken:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Een Event Hub maken

Maak vervolgens een Azure Event Hubs-naam ruimte, Event Hub en autorisatie regel met behulp van de volgende opdrachten:

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

De Event Hubs naam ruimte bevat de daad werkelijke Event Hub en de autorisatie regel. Met de autorisatie regel kunnen uw functies berichten verzenden naar de hub en Luis teren naar de bijbehorende gebeurtenissen. Met de ene functie worden berichten verzonden die telemetriegegevens vertegenwoordigen. Een andere functie luistert naar gebeurtenissen, analyseert de gebeurtenis gegevens en slaat de resultaten op in Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Een Azure Cosmos DB maken

Maak vervolgens een Azure Cosmos DB-account,-data base en-verzameling met behulp van de volgende opdrachten:

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

Met de `partition-key-path` waarde worden uw gegevens gepartitioneerd op basis van de `temperatureStatus` waarde van elk item. De partitie sleutel maakt het Cosmos DB mogelijk om de prestaties te verbeteren door uw gegevens te delen in afzonderlijke subsets waartoe deze onafhankelijk kan toegang hebben.

### <a name="create-a-storage-account-and-function-app"></a>Een opslag account en functie-app maken

Maak vervolgens een Azure Storage account dat wordt vereist door Azure Functions en maak vervolgens de functie-app. Gebruik de volgende opdrachten:

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

Wanneer de `az functionapp create` opdracht uw functie-app maakt, wordt er ook een Application Insights resource met dezelfde naam gemaakt. De functie-app wordt automatisch geconfigureerd met een instelling met de naam `APPINSIGHTS_INSTRUMENTATIONKEY` die deze verbindt met Application Insights. U kunt de app-telemetrie bekijken nadat u uw functies hebt geïmplementeerd in azure, zoals verderop in deze zelf studie wordt beschreven.

## <a name="configure-your-function-app"></a>Uw functie-app configureren

De functie-app moet toegang hebben tot de andere resources om goed te kunnen werken. In de volgende secties ziet u hoe u uw functie-app zodanig configureert dat deze op uw lokale machine kan worden uitgevoerd.

### <a name="retrieve-resource-connection-strings"></a>Bron verbindings reeksen ophalen

Gebruik de volgende opdrachten om de opslag-, Event Hub-en Cosmos DB verbindings reeksen op te halen en op te slaan in omgevings variabelen:

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query connectionStrings[0].connectionString \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

Deze variabelen worden ingesteld op waarden die worden opgehaald uit Azure CLI-opdrachten. Elke opdracht maakt gebruik van een JMESPath-query voor het uitpakken van de connection string van de JSON-nettolading die wordt geretourneerd. De verbindings reeksen worden ook weer gegeven met `echo` zodat u kunt bevestigen dat ze zijn opgehaald.

### <a name="update-your-function-app-settings"></a>De instellingen van de functie-app bijwerken

Gebruik vervolgens de volgende opdracht om de connection string waarden over te brengen naar app-instellingen in uw Azure Functions-account:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Uw Azure-resources zijn nu gemaakt en geconfigureerd om goed te kunnen samen werken.

## <a name="create-and-test-your-functions"></a>Uw functies maken en testen

Vervolgens maakt u een project op uw lokale machine, voegt u Java-code toe en test u deze. U gebruikt opdrachten die samen werken met de Maven-invoeg toepassing voor Azure Functions en de Azure Functions Core Tools. Uw functies worden lokaal uitgevoerd, maar de cloud resources die u hebt gemaakt, worden gebruikt. Nadat u de functies hebt die lokaal werken, kunt u maven gebruiken om ze te implementeren in de Cloud en uw gegevens en analyses te bekijken.

Als u Cloud Shell hebt gebruikt om uw resources te maken, wordt u niet lokaal verbonden met Azure. In dit geval gebruikt u de opdracht `az login` om het aanmeldings proces op de browser te starten. Stel vervolgens, indien nodig, het standaard abonnement in met `az account set --subscription` gevolgd door de abonnements-ID. Ten slotte voert u de volgende opdrachten uit om bepaalde omgevings variabelen op uw lokale computer opnieuw te maken. Vervang de tijdelijke aanduidingen `<value>` door de waarden die u eerder hebt gebruikt.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Een project met lokale functies maken

Gebruik de volgende maven-opdracht om een functions-project te maken en de vereiste afhankelijkheden toe te voegen.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

Met deze opdracht worden verschillende bestanden in een `telemetry-functions` map gegenereerd:

* Een `pom.xml`-bestand dat kan worden gebruikt met maven
* Een `local.settings.json` bestand voor het bewaren van app-instellingen voor lokale tests
* Een `host.json` bestand waarmee de Azure Functions extensie bundel, vereist voor Cosmos DB uitvoer binding in uw functie voor gegevens analyse, wordt ingeschakeld
* Een `Function.java`-bestand dat een standaard functie-implementatie bevat
* Een aantal test bestanden die niet nodig zijn voor deze zelf studie

Als u compilatie fouten wilt voor komen, moet u de test bestanden verwijderen. Voer de volgende opdrachten uit om naar de nieuwe projectmap te gaan en de map test te verwijderen:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>De instellingen van de functie-app ophalen voor lokaal gebruik

Voor lokale tests heeft uw functie project de verbindings reeksen nodig die u hebt toegevoegd aan uw functie-app in azure eerder in deze zelf studie. Gebruik de volgende Azure Functions Core Tools opdracht, waarmee alle in de cloud opgeslagen functie-app-instellingen worden opgehaald en toegevoegd aan uw `local.settings.json`-bestand:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Java-code toevoegen

Open vervolgens het `Function.java`-bestand en vervang de inhoud door de volgende code.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Zoals u kunt zien, bevat dit bestand twee functies, `generateSensorData` en `processSensorData`. De functie `generateSensorData` simuleert een sensor die de Tempe ratuur en druk aflezingen naar de Event Hub verzendt. Een timer trigger voert de functie elke 10 seconden uit en een Event Hub uitvoer binding verzendt de retour waarde naar de Event Hub.

Wanneer het Event Hub het bericht ontvangt, wordt een gebeurtenis gegenereerd. De functie `processSensorData` wordt uitgevoerd wanneer de gebeurtenis wordt ontvangen. Vervolgens worden de gebeurtenis gegevens verwerkt en wordt een Azure Cosmos DB uitvoer binding gebruikt om de resultaten naar Azure Cosmos DB te verzenden.

De gegevens die door deze functies worden gebruikt, worden opgeslagen met behulp van een klasse met de naam `TelemetryItem`, die u moet implementeren. Maak een nieuw bestand met de naam `TelemetryItem.java` op dezelfde locatie als `Function.java` en voeg de volgende code toe:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Lokaal uitvoeren

U kunt nu de functies lokaal bouwen en uitvoeren en gegevens weer geven in uw Azure Cosmos DB.

Gebruik de volgende maven-opdrachten om de functies te bouwen en uit te voeren:

```bash
mvn clean package
mvn azure-functions:run
```

Na sommige build-en opstart berichten ziet u uitvoer die lijkt op het volgende voor beeld, voor elke keer dat de functies worden uitgevoerd:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

U kunt vervolgens naar de [Azure Portal](https://portal.azure.com) gaan en naar uw Azure Cosmos DB-account navigeren. Selecteer **Data Explorer**, vouw **TelemetryInfo**uit en selecteer vervolgens **items** om uw gegevens weer te geven wanneer deze arriveren.

![Cosmos DB Data Explorer](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Implementeren naar Azure en app-telemetrie weer geven

Ten slotte kunt u uw app implementeren in Azure en controleren of het werkt op dezelfde manier als de lokale.

Implementeer uw project in azure met behulp van de volgende opdracht:

```bash
mvn azure-functions:deploy
```

Uw functies worden nu uitgevoerd in Azure en blijven gegevens in uw Azure Cosmos DB verzamelen. U kunt uw geïmplementeerde functie-app weer geven in de Azure Portal en de telemetrie van de app weer geven via de verbonden Application Insights resource, zoals wordt weer gegeven in de volgende scherm afbeeldingen:

**Live Metrics Stream:**

![Application Insights Live Metrics Stream](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Prestaties:**

![Blade Application Insights prestaties](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de Azure-resources die u in deze zelf studie hebt gemaakt, kunt u deze verwijderen met de volgende opdracht:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een Azure-functie maakt waarmee Event hub-gebeurtenissen worden verwerkt en een Cosmos DB wordt bijgewerkt. Zie de [Azure functions Java-ontwikkelaars handleiding](/azure/azure-functions/functions-reference-java)voor meer informatie. Voor informatie over de gebruikte aantekeningen raadpleegt u de verwijzing [com. Microsoft. Azure. functions. annotaties](/java/api/com.microsoft.azure.functions.annotation) .

In deze zelf studie hebt u omgevings variabelen en toepassings instellingen gebruikt voor het opslaan van geheimen zoals verbindings reeksen. Zie [Key Vault verwijzingen gebruiken voor app service en Azure functions](/azure/app-service/app-service-key-vault-references)voor meer informatie over het opslaan van deze geheimen in azure Key Vault.

Vervolgens leert u hoe u Azure pipelines CI/CD gebruikt voor automatische implementatie:

> [!div class="nextstepaction"]
> [Java maken en implementeren in Azure Functions](/azure/devops/pipelines/ecosystems/java-function)
