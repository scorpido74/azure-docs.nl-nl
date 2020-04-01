---
title: 'Zelfstudie: Java-functies gebruiken met Azure Cosmos DB en Event Hubs'
description: In deze zelfstudie ziet u hoe u gebeurtenissen van Gebeurtenishubs gebruiken om updates in Azure Cosmos DB te maken met behulp van een functie die in Java is geschreven.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.openlocfilehash: b6d7b2c60e777266b1cab578b8970c1fa1c6bc50
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77425320"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Zelfstudie: Een functie in Java maken met een Trigger voor eventhub en een Azure Cosmos DB-uitvoerbinding

In deze zelfstudie ziet u hoe u Azure-functies gebruiken om een Java-functie te maken die een continue stroom van temperatuur- en drukgegevens analyseert. Gebeurtenishubgebeurtenissen die sensormetingen vertegenwoordigen, activeren de functie. De functie verwerkt de gebeurtenisgegevens en voegt vervolgens statusvermeldingen toe aan een Azure Cosmos DB.

In deze zelfstudie volgt u het als volgt:

> [!div class="checklist"]
> * Azure-resources maken en configureren met de Azure CLI.
> * Java-functies maken en testen die met deze bronnen communiceren.
> * Implementeer uw functies in Azure en monitor ze met Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, moet u het volgende hebben geïnstalleerd:

* [Java Developer Kit](https://aka.ms/azure-jdks), versie 8
* [Apache Maven](https://maven.apache.org), versie 3.0 of hoger
* [Azure CLI](/cli/azure/install-azure-cli) als u Cloud Shell liever niet gebruikt
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) versie 2.6.666 of hoger

> [!IMPORTANT]
> De `JAVA_HOME` omgevingsvariabele moet worden ingesteld op de installatielocatie van de JDK om deze zelfstudie te voltooien.

Als u de code liever direct voor deze zelfstudie gebruikt, raadpleegt u de [java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) sample repo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Azure-resources maken

In deze zelfstudie heb je de volgende bronnen nodig:

* Een resourcegroep met de andere bronnen
* Een naamruimte voor gebeurtenishubs, gebeurtenishub en autorisatieregel
* Een Cosmos DB-account, -database en -verzameling
* Een functie-app en een opslagaccount om deze te hosten

In de volgende secties ziet u hoe u deze resources maakt met de Azure CLI.

### <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Als u Cloud Shell niet gebruikt, moet u de Azure CLI lokaal gebruiken om toegang te krijgen tot uw account. Gebruik `az login` de opdracht van de Bash-prompt om de browsergebaseerde inlogervaring te starten. Als u toegang hebt tot meer dan één `az account set --subscription` Azure-abonnement, stelt u de standaardinstelling in, gevolgd door de abonnements-ID.

### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

Maak vervolgens enkele omgevingsvariabelen voor de namen en locatie van de resources die u maakt. Gebruik de volgende opdrachten en `<value>` vervang de tijdelijke aanduidingen door waarden van uw keuze. Waarden moeten voldoen aan de [naamgevingsregels en -beperkingen voor Azure-resources.](/azure/architecture/best-practices/resource-naming) Gebruik `LOCATION` voor de variabele een van `az functionapp list-consumption-locations` de waarden die door de opdracht worden geproduceerd.

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

De rest van deze tutorial maakt gebruik van deze variabelen. Houd er rekening mee dat deze variabelen alleen blijven bestaan voor de duur van uw huidige Azure CLI- of Cloud Shell-sessie. U moet deze opdrachten opnieuw uitvoeren als u een ander lokaal terminalvenster gebruikt of als er een time-out is ophet moment van uw Cloud Shell-sessie.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Azure gebruikt brongroepen om alle gerelateerde bronnen in uw account te verzamelen. Op die manier u ze bekijken als een eenheid en ze verwijderen met een enkele opdracht wanneer u klaar bent met hen.

Gebruik de volgende opdracht om een resourcegroep te maken:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Een Event Hub maken

Maak vervolgens een naamruimte voor Azure Event Hubs, gebeurtenishub en autorisatieregel met de volgende opdrachten:

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

De naamruimte gebeurtenishubs bevat de werkelijke gebeurtenishub en de autorisatieregel. Met de autorisatieregel kunnen uw functies berichten naar de hub verzenden en naar de bijbehorende gebeurtenissen luisteren. Eén functie verzendt berichten die telemetriegegevens vertegenwoordigen. Een andere functie luistert naar gebeurtenissen, analyseert de gebeurtenisgegevens en slaat de resultaten op in Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Een Azure Cosmos DB maken

Maak vervolgens een Azure Cosmos DB-account, -database en -verzameling met de volgende opdrachten:

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

De `partition-key-path` waarde partities van `temperatureStatus` uw gegevens op basis van de waarde van elk item. Met de partitiesleutel kan Cosmos DB de prestaties verhogen door uw gegevens te verdelen in afzonderlijke subsets die het onafhankelijk van elkaar kan openen.

### <a name="create-a-storage-account-and-function-app"></a>Een opslagaccount en functie-app maken

Maak vervolgens een Azure Storage-account aan, dat vereist is voor Azure Functions en maak vervolgens de functie-app. Gebruik de volgende opdrachten:

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

Wanneer `az functionapp create` de opdracht uw functie-app maakt, wordt er ook een Application Insights-bron met dezelfde naam. De functie-app wordt automatisch geconfigureerd `APPINSIGHTS_INSTRUMENTATIONKEY` met een instelling met de naam die deze verbindt met Application Insights. U telemetrie van apps bekijken nadat u uw functies hebt geïmplementeerd in Azure, zoals later in deze zelfstudie wordt beschreven.

## <a name="configure-your-function-app"></a>Uw functie-app configureren

Uw functie-app moet toegang krijgen tot de andere bronnen om correct te kunnen werken. In de volgende secties ziet u hoe u uw functie-app configureert, zodat deze op uw lokale machine kan worden uitgevoerd.

### <a name="retrieve-resource-connection-strings"></a>Tekenreeksen voor resourceverbindingen ophalen

Gebruik de volgende opdrachten om de verbindingstekenreeksen opslag, gebeurtenishub en Cosmos DB op te halen en op te slaan in omgevingsvariabelen:

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

Deze variabelen zijn ingesteld op waarden die zijn opgehaald uit Azure CLI-opdrachten. Elke opdracht maakt gebruik van een JMESPath-query om de verbindingstekenreeks uit de teruggekeerde JSON-payload te extraheren. De verbindingstekenreeksen worden `echo` ook weergegeven met behulp van, zodat u bevestigen dat ze zijn opgehaald.

### <a name="update-your-function-app-settings"></a>Instellingen voor de functie-app bijwerken

Gebruik vervolgens de volgende opdracht om de verbindingstekenreekswaarden over te zetten naar app-instellingen in uw Azure-functieaccount:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Uw Azure-bronnen zijn nu gemaakt en geconfigureerd om goed samen te werken.

## <a name="create-and-test-your-functions"></a>Uw functies maken en testen

Vervolgens maakt u een project op uw lokale machine, voegt u Java-code toe en test u deze. U gebruikt opdrachten die werken met de Plug-in Azure-functies voor Maven en de Azure Functions Core-hulpprogramma's. Uw functies worden lokaal uitgevoerd, maar maken gebruik van de cloudbronnen die u hebt gemaakt. Nadat u de functies lokaal hebt laten werken, u Maven gebruiken om ze in de cloud te implementeren en uw gegevens en analyses te zien accumuleren.

Als u Cloud Shell hebt gebruikt om uw resources te maken, wordt u niet lokaal verbonden met Azure. Gebruik in dit `az login` geval de opdracht om het browsergebaseerde aanmeldingsproces te starten. Stel vervolgens indien nodig het `az account set --subscription` standaardabonnement in met de abonnements-ID. Voer ten slotte de volgende opdrachten uit om bepaalde omgevingsvariabelen opnieuw te maken op uw lokale machine. Vervang `<value>` de tijdelijke aanduidingen door dezelfde waarden die u eerder hebt gebruikt.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Een project voor lokale functies maken

Gebruik de volgende opdracht Maven om een functieproject te maken en de vereiste afhankelijkheden toe te voegen.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

Met deze opdracht worden `telemetry-functions` verschillende bestanden in een map gegenereerd:

* Een `pom.xml` bestand voor gebruik met Maven
* Een `local.settings.json` bestand dat app-instellingen voor lokale tests moet vasthouden
* Een `host.json` bestand dat de Azure Functions Extension Bundle inschakelt, vereist voor Cosmos DB-uitvoerbinding in uw gegevensanalysefunctie
* Een `Function.java` bestand met een standaardfunctieimplementatie
* Een paar testbestanden die deze zelfstudie niet nodig heeft

Om compilatiefouten te voorkomen, moet u de testbestanden verwijderen. Voer de volgende opdrachten uit om naar de nieuwe projectmap te navigeren en de testmap te verwijderen:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Uw functie-app-instellingen ophalen voor lokaal gebruik

Voor lokale tests heeft uw functieproject de verbindingstekenreeksen nodig die u eerder in deze zelfstudie aan uw functie-app in Azure hebt toegevoegd. Gebruik de volgende opdracht Azure Functions Core Tools, waarmee alle functie-app-instellingen `local.settings.json` die in de cloud zijn opgeslagen, worden opgehaald en aan uw bestand wordt toegevoegd:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Java-code toevoegen

Open vervolgens `Function.java` het bestand en vervang de inhoud door de volgende code.

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

Zoals u zien, bevat `generateSensorData` dit `processSensorData`bestand twee functies en. De `generateSensorData` functie simuleert een sensor die temperatuur- en drukmetingen naar de gebeurtenishub stuurt. Een timertrigger voert de functie elke 10 seconden uit en een gebeurtenishub-uitvoerbinding verzendt de retourwaarde naar de gebeurtenishub.

Wanneer de gebeurtenishub het bericht ontvangt, genereert deze een gebeurtenis. De `processSensorData` functie wordt uitgevoerd wanneer de gebeurtenis wordt ontvangen. Vervolgens worden de gebeurtenisgegevens verwerkt en wordt een Azure Cosmos DB-uitvoerbinding gebruikt om de resultaten naar Azure Cosmos DB te verzenden.

De gegevens die door deze functies `TelemetryItem`worden gebruikt, worden opgeslagen met behulp van een klasse die wordt genoemd, die u moet implementeren. Maak een nieuw `TelemetryItem.java` bestand dat `Function.java` op dezelfde locatie als en voeg de volgende code:

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

U de functies nu lokaal bouwen en uitvoeren en gegevens zien verschijnen in uw Azure Cosmos DB.

Gebruik de volgende Maven-opdrachten om de functies te bouwen en uit te voeren:

```bash
mvn clean package
mvn azure-functions:run
```

Na sommige build- en opstartberichten ziet u uitvoer die vergelijkbaar is met het volgende voorbeeld voor elke keer dat de functies worden uitgevoerd:

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

U vervolgens naar de [Azure-portal](https://portal.azure.com) gaan en naar uw Azure Cosmos DB-account navigeren. Selecteer **Gegevensverkenner**, vouw **TelemetrieInfo**uit en selecteer **Items** om uw gegevens weer te geven wanneer deze binnenkomen.

![Cosmos DB-gegevensverkenner](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Implementeren in Azure en telemetrie van apps weergeven

Ten slotte u uw app implementeren in Azure en controleren of deze op dezelfde manier werkt als lokaal.

Implementeer uw project naar Azure met de volgende opdracht:

```bash
mvn azure-functions:deploy
```

Uw functies worden nu uitgevoerd in Azure en blijven gegevens verzamelen in uw Azure Cosmos DB. U de geïmplementeerde functie-app bekijken in de Azure-portal en telemetrie van de app bekijken via de verbonden Application Insights-bron, zoals weergegeven in de volgende schermafbeeldingen:

**Live statistieken stream:**

![Livestream van live statistieken van toepassingstatistieken](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Prestaties:**

![Application Insights Performance blade](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de Azure-resources die u in deze zelfstudie hebt gemaakt, kunt u deze verwijderen met de volgende opdracht:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Azure-functie maakt waarmee gebeurtenishubgebeurtenissen worden verwerkt en een Cosmos DB wordt bijgewerkt. Zie de [ontwikkelaarshandleiding Azure Functions Java](/azure/azure-functions/functions-reference-java)voor meer informatie . Zie de [annotatieverwijzing com.microsoft.azure.functions.functions.annotatie](/java/api/com.microsoft.azure.functions.annotation) voor informatie over de gebruikte annotaties.

In deze zelfstudie worden omgevingsvariabelen en toepassingsinstellingen gebruikt om geheimen op te slaan, zoals verbindingstekenreeksen. Zie [Key Vault-referenties voor App-service en Azure-functies gebruiken voor](/azure/app-service/app-service-key-vault-references)informatie over het opslaan van deze geheimen in Azure Key Vault.

Lees vervolgens hoe u Azure Pipelines CI/CD gebruikt voor geautomatiseerde implementatie:

> [!div class="nextstepaction"]
> [Java bouwen en implementeren in Azure-functies](/azure/devops/pipelines/ecosystems/java-function)
