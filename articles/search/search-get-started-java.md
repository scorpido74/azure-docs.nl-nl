---
title: "Quick Start: een zoek index maken in Java met REST-Api's-Azure Search"
description: Hierin wordt uitgelegd hoe u een index maakt, gegevens laadt en query's uitvoert met behulp van Java en de Azure Search REST-Api's.
manager: nitinme
author: lisaleib
ms.author: v-lilei
ms.service: search
ms.custom: seodec2018, seo-java-july2019, seo-java-august2019
ms.devlang: java
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: 3f424f03f72e288994b05c4559bd42e6429760a8
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166250"
---
# <a name="quickstart-create-an-azure-search-index-in-java-using-rest-apis"></a>Snelstartgids: een Azure Search-index maken in Java met behulp van REST-Api's
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Maak een Java-Console toepassing die een Azure search-index maakt, laadt en opvraagt met behulp van [IntelliJ](https://www.jetbrains.com/idea/), [Java 11 SDK](/java/azure/jdk/?view=azure-java-stable)en de rest API van de [Azure Search-service](/rest/api/searchservice/). In dit artikel vindt u stapsgewijze instructies voor het maken van de toepassing. U kunt ook [de volledige toepassing downloaden en uitvoeren](/samples/azure-samples/azure-search-java-samples/java-sample-quickstart/).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

We hebben de volgende software en services gebruikt om dit voor beeld te bouwen en te testen:

+ [IntelliJ-idee](https://www.jetbrains.com/idea/)

+ [Java 11 SDK](/java/azure/jdk/?view=azure-java-stable)

+ [Een Azure Search-service maken](search-create-service-portal.md) of [een bestaande service vinden](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U kunt een gratis service voor deze Quick Start gebruiken.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

Aanroepen naar de service vereisen een URL-eind punt en een toegangs sleutel voor elke aanvraag. Een zoekservice wordt gemaakt met beide, dus als u Azure Search hebt toegevoegd aan uw abonnement, volgt u deze stappen om de benodigde gegevens op te halen:

1. [Meld u aan bij de Azure Portal](https://portal.azure.com/)en down load de URL op de pagina **overzicht** van de zoek service. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. In **instellingen** > **sleutels**, een beheerders sleutel ophalen voor volledige rechten op de service. Er zijn twee uitwissel bare beheer sleutels die voor bedrijfs continuïteit worden verschaft, voor het geval dat u een voor beeld moet doen. U kunt de primaire of secundaire sleutel gebruiken op aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Maak ook een query sleutel. Het is een best practice voor het uitgeven van query aanvragen met alleen-lezen toegang.

![De service naam en de beheer-en query sleutels ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Voor elke aanvraag die naar uw service wordt verzonden, is een API-sleutel vereist. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Begin met het openen van het IntelliJ-idee en het instellen van een nieuw project.

### <a name="create-the-project"></a>Het project maken

1. Open IntelliJ-idee en selecteer **Nieuw project maken**.
1. Selecteer **maven**.
1. Selecteer in de lijst **Project-SDK** de Java 11-SDK.

    ![Een Maven-project maken](media/search-get-started-java/java-quickstart-create-new-maven-project.png) 

1. Voer `AzureSearchQuickstart` in voor **GroupId** en **ArtifactId**.
1. Accepteer de resterende standaard waarden om het project te openen.

### <a name="specify-maven-dependencies"></a>Maven-afhankelijkheden opgeven

1. Selecteer de**instellingen**voor **bestand** > .
1. Selecteer in het venster **instellingen** de optie **Build, Execution, implementatie** > **Build Tools** > **maven** > **import**.
1. Schakel het selectie vakje **Maven projecten automatisch importeren** in en klik op **OK** om het venster te sluiten. Maven-invoeg toepassingen en andere afhankelijkheden worden nu automatisch gesynchroniseerd wanneer u het bestand pom. XML in de volgende stap bijwerkt.

    ![Opties voor het importeren van Maven in IntelliJ-instellingen](media/search-get-started-java/java-quickstart-settings-import-maven-auto.png)

1. Open het bestand pom. XML en vervang de inhoud door de volgende maven configuratie details. Deze bevatten verwijzingen naar de [exec maven-invoeg toepassing](https://www.mojohaus.org/exec-maven-plugin/) en een [JSON-interface-API](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2)

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>AzureSearchQuickstart</groupId>
        <artifactId>AzureSearchQuickstart</artifactId>
        <version>1.0-SNAPSHOT</version>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.1</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.6.0</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>exec</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <mainClass>main.java.app.App</mainClass>
                        <cleanupDaemonThreads>false</cleanupDaemonThreads>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        <dependencies>
            <dependency>
                <groupId>org.glassfish</groupId>
                <artifactId>javax.json</artifactId>
                <version>1.0.2</version>
            </dependency>
        </dependencies>   
    </project>
    ```

### <a name="set-up-the-project-structure"></a>De project structuur instellen

1. Selecteer **bestand** > **project structuur**.
1. Selecteer **modules**en vouw de bron structuur uit om toegang te krijgen tot de inhoud van de map `src` @ no__t-2 @ no__t-3.
1. Voeg in de `src` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4-map `app` en `service` mappen toe. Als u dit wilt doen, selecteert u de map `java`, drukt u op ALT + INSERT en voert u de naam van de map in.
1. Voeg in de `src` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4-map `app` en `service` mappen toe.

    Wanneer u klaar bent, moet de project structuur eruitzien zoals in de volgende afbeelding.

    ![Structuur van projectmap](media/search-get-started-java/java-quickstart-basic-code-tree.png)

1. Klik op **OK** om het venster te sluiten.

### <a name="add-azure-search-service-information"></a>Informatie over Azure Search-service toevoegen

1. Vouw in het **project** venster de bron structuur uit om toegang te krijgen tot de `src` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7-map en voeg een `config.properties`-bestand toe. Als u dit wilt doen, selecteert u de map `app`, drukt u op ALT + INSERT, selecteert u **bestand**en voert u de bestands naam in.

1. Kopieer de volgende instellingen naar het nieuwe bestand en vervang `<YOUR-SEARCH-SERVICE-NAME>`, `<YOUR-ADMIN-KEY>` en `<YOUR-QUERY-KEY>` door de naam en sleutels van uw service. Als uw service-eind punt `https://mydemo.search.windows.net` is, is de naam van de service ' mydemo '.

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2019-05-06
    ```

### <a name="add-the-main-method"></a>De methode Main toevoegen

1. In de `src` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 map, voegt u een `App`-klasse toe. Als u dit wilt doen, selecteert u de map `app`, drukt u op ALT + INSERT, selecteert u **Java-klasse**en voert u vervolgens de naam van de klasse in.
1. Open de klasse `App` en vervang de inhoud door de volgende code. Deze code bevat de `main`-methode. 

    De niet-genoteerde code leest de para meters van de zoek service en gebruikt deze om een exemplaar van de Search-serviceclient te maken. De client code van de zoek service wordt toegevoegd aan de volgende sectie.

    De code van de tekst in deze klasse wordt in een latere sectie van deze Quick Start weer in een opmerking opgenomen.

    ```java
    package main.java.app;
    
    import main.java.service.SearchServiceClient;
    import java.io.IOException;
    import java.util.Properties;
    
    public class App {
    
        private static Properties loadPropertiesFromResource(String resourcePath) throws IOException {
            var inputStream = App.class.getResourceAsStream(resourcePath);
            var configProperties = new Properties();
            configProperties.load(inputStream);
            return configProperties;
        }
    
        public static void main(String[] args) {
            try {
                var config = loadPropertiesFromResource("/app/config.properties");
                var client = new SearchServiceClient(
                        config.getProperty("SearchServiceName"),
                        config.getProperty("SearchServiceAdminKey"),
                        config.getProperty("SearchServiceQueryKey"),
                        config.getProperty("ApiVersion"),
                        config.getProperty("IndexName")
                );
    
    
    //Uncomment the next 3 lines in the 1 - Create Index section of the quickstart
    //            if(client.indexExists()){ client.deleteIndex();}
    //            client.createIndex("/service/index.json");
    //            Thread.sleep(1000L); // wait a second to create the index
    
    //Uncomment the next 2 lines in the 2 - Load Documents section of the quickstart
    //            client.uploadDocuments("/service/hotels.json");
    //            Thread.sleep(2000L); // wait 2 seconds for data to upload
    
    //Uncomment the following 5 search queries in the 3 - Search an index section of the quickstart
    //            // Query 1
    //            client.logMessage("\n*QUERY 1****************************************************************");
    //            client.logMessage("Search for: Atlanta'");
    //            client.logMessage("Return: All fields'");
    //            client.searchPlus("Atlanta");
    //
    //            // Query 2
    //            client.logMessage("\n*QUERY 2****************************************************************");
    //            client.logMessage("Search for: Atlanta");
    //            client.logMessage("Return: HotelName, Tags, Address");
    //            SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    //            options2.select = "HotelName,Tags,Address";
    //            client.searchPlus("Atlanta", options2);
    //
    //            //Query 3
    //            client.logMessage("\n*QUERY 3****************************************************************");
    //            client.logMessage("Search for: wifi & restaurant");
    //            client.logMessage("Return: HotelName, Description, Tags");
    //            SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    //            options3.select = "HotelName,Description,Tags";
    //            client.searchPlus("wifi,restaurant", options3);
    //
    //            // Query 4 -filtered query
    //            client.logMessage("\n*QUERY 4****************************************************************");
    //            client.logMessage("Search for: all");
    //            client.logMessage("Filter: Ratings greater than 4");
    //            client.logMessage("Return: HotelName, Rating");
    //            SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    //            options4.filter="Rating%20gt%204";
    //            options4.select = "HotelName,Rating";
    //            client.searchPlus("*",options4);
    //
    //            // Query 5 - top 2 results, ordered by
    //            client.logMessage("\n*QUERY 5****************************************************************");
    //            client.logMessage("Search for: boutique");
    //            client.logMessage("Get: Top 2 results");
    //            client.logMessage("Order by: Rating in descending order");
    //            client.logMessage("Return: HotelId, HotelName, Category, Rating");
    //            SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    //            options5.top=2;
    //            options5.orderby = "Rating%20desc";
    //            options5.select = "HotelId,HotelName,Category,Rating";
    //            client.searchPlus("boutique", options5);
    
            } catch (Exception e) {
                System.err.println("Exception:" + e.getMessage());
                e.printStackTrace();
            }
        }
    }
    ```

### <a name="add-the-http-operations"></a>De HTTP-bewerkingen toevoegen

1. In de `src` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 map, voegt u een @ no__t-7-klasse toe. Als u dit wilt doen, selecteert u de map `service`, drukt u op ALT + INSERT, selecteert u **Java-klasse**en voert u vervolgens de naam van de klasse in.
1. Open de klasse `SearchServiceClient` en vervang de inhoud door de volgende code. Deze code geeft de HTTP-bewerkingen die nodig zijn om de Azure Search REST API te gebruiken. Aanvullende methoden voor het maken van een index, het uploaden van documenten en het uitvoeren van query's op de index worden toegevoegd in een latere sectie.

    ```java
    package main.java.service;

    import javax.json.Json;
    import javax.net.ssl.HttpsURLConnection;
    import java.io.IOException;
    import java.io.StringReader;
    import java.net.HttpURLConnection;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.nio.charset.StandardCharsets;
    import java.util.Formatter;
    import java.util.function.Consumer;
    
        /* This class is responsible for implementing HTTP operations for creating the index, uploading documents and searching the data*/
        public class SearchServiceClient {
            private final String _adminKey;
            private final String _queryKey;
            private final String _apiVersion;
            private final String _serviceName;
            private final String _indexName;
            private final static HttpClient client = HttpClient.newHttpClient();
    
        public SearchServiceClient(String serviceName, String adminKey, String queryKey, String apiVersion, String indexName) {
            this._serviceName = serviceName;
            this._adminKey = adminKey;
            this._queryKey = queryKey;
            this._apiVersion = apiVersion;
            this._indexName = indexName;
        }

        private static HttpResponse<String> sendRequest(HttpRequest request) throws IOException, InterruptedException {
            logMessage(String.format("%s: %s", request.method(), request.uri()));
            return client.send(request, HttpResponse.BodyHandlers.ofString());
        }

        private static URI buildURI(Consumer<Formatter> fmtFn)
                {
                    Formatter strFormatter = new Formatter();
                    fmtFn.accept(strFormatter);
                    String url = strFormatter.out().toString();
                    strFormatter.close();
                    return URI.create(url);
        }
    
        public static void logMessage(String message) {
            System.out.println(message);
        }
    
        public static boolean isSuccessResponse(HttpResponse<String> response) {
            try {
                int responseCode = response.statusCode();
    
                logMessage("\n Response code = " + responseCode);
    
                if (responseCode == HttpURLConnection.HTTP_OK || responseCode == HttpURLConnection.HTTP_ACCEPTED
                        || responseCode == HttpURLConnection.HTTP_NO_CONTENT || responseCode == HttpsURLConnection.HTTP_CREATED) {
                    return true;
                }
    
                // We got an error
                var msg = response.body();
                if (msg != null) {
                    logMessage(String.format("\n MESSAGE: %s", msg));
                }
    
            } catch (Exception e) {
                e.printStackTrace();
            }
    
            return false;
        }
    
        public static HttpRequest httpRequest(URI uri, String key, String method, String contents) {
            contents = contents == null ? "" : contents;
            var builder = HttpRequest.newBuilder();
            builder.uri(uri);
            builder.setHeader("content-type", "application/json");
            builder.setHeader("api-key", key);
    
            switch (method) {
                case "GET":
                    builder = builder.GET();
                    break;
                case "HEAD":
                    builder = builder.GET();
                    break;
                case "DELETE":
                    builder = builder.DELETE();
                    break;
                case "PUT":
                    builder = builder.PUT(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                case "POST":
                    builder = builder.POST(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                default:
                    throw new IllegalArgumentException(String.format("Can't create request for method '%s'", method));
            }
            return builder.build();
        }
    }
    
    ```

### <a name="build-the-project"></a>Het project bouwen

1. Controleer of uw project de volgende structuur heeft.

    ![Structuur van projectmap](media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png)

1. Open het **maven** -programma venster en voer dit maven doel uit: `verify exec:java` @ no__t-2 @ No__t-3Execute maven doel: Ga naar exec: Java @ no__t-4

Wanneer de verwerking is voltooid, zoekt u een bericht over een GESLAAGDe BUILD, gevolgd door een afsluit code van nul (0).

## <a name="1---create-index"></a>1-index maken

De index voor de hotels bevat eenvoudige velden en één complex veld. Voor beelden van een eenvoudig veld zijn "naam Hotel" of "Beschrijving". Het veld ' adres ' is een complex veld omdat het subvelden bevat, zoals "straat" en "City". In deze Quick Start wordt de index definitie opgegeven met JSON.

1. Vouw in het **project** venster de bron structuur uit om toegang te krijgen tot de `src` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7-map en voeg een `index.json`-bestand toe. Als u dit wilt doen, selecteert u de map `app`, drukt u op ALT + INSERT, selecteert u **bestand**en voert u de bestands naam in.

1. Open het bestand `index.json` en voeg de volgende index definitie in.

    ```json
    {
      "name": "hotels-quickstart",
      "fields": [
        {
          "name": "HotelId",
          "type": "Edm.String",
          "key": true,
          "filterable": true
        },
        {
          "name": "HotelName",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": true,
          "facetable": false
        },
        {
          "name": "Description",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "en.lucene"
        },
        {
          "name": "Description_fr",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "fr.lucene"
        },
        {
          "name": "Category",
          "type": "Edm.String",
          "searchable": true,
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Tags",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": true,
          "sortable": false,
          "facetable": true
        },
        {
          "name": "ParkingIncluded",
          "type": "Edm.Boolean",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "LastRenovationDate",
          "type": "Edm.DateTimeOffset",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Rating",
          "type": "Edm.Double",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Address",
          "type": "Edm.ComplexType",
          "fields": [
            {
              "name": "StreetAddress",
              "type": "Edm.String",
              "filterable": false,
              "sortable": false,
              "facetable": false,
              "searchable": true
            },
            {
              "name": "City",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "StateProvince",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "PostalCode",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "Country",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            }
          ]
        }
      ]
    }
    ```

    De naam van de index is "Hotels-Quick Start". Kenmerken voor de index velden bepalen hoe de geïndexeerde gegevens kunnen worden doorzocht in een toepassing. Het kenmerk `IsSearchable` moet bijvoorbeeld worden toegewezen aan elk veld dat moet worden opgenomen in een zoek opdracht in volledige tekst. Zie [velden verzamelings-en veld kenmerken](search-what-is-an-index.md#fields-collection)voor meer informatie over kenmerken.
    
    In het veld `Description` in deze index wordt de optionele eigenschap `analyzer` gebruikt om de standaard taal analyse voor lucene te vervangen. Het veld `Description_fr` maakt gebruik van de Franse lucene Analyzer `fr.lucene` omdat de Franse tekst wordt opgeslagen. De `Description` maakt gebruik van de optionele taal analyse en. lucene van micro soft. Zie voor meer informatie over analyse functies [analyseren voor tekst verwerking in azure Search](search-analyzers.md).

1. Voeg de volgende code toe aan de klasse `SearchServiceClient`. Deze methoden bouwen Azure Search-Url's voor REST-services die een index maken en verwijderen en die bepalen of een index bestaat. De methoden maken ook de HTTP-aanvraag.

    ```java
    public boolean indexExists() throws IOException, InterruptedException {
        logMessage("\n Checking if index exists...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=*",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "HEAD", "");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    public boolean deleteIndex() throws IOException, InterruptedException {
        logMessage("\n Deleting index...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "DELETE", "*");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    
    public boolean createIndex(String indexDefinitionFile) throws IOException, InterruptedException {
        logMessage("\n Creating index...");
        //Build the search service URL
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in index definition file
        var inputStream = SearchServiceClient.class.getResourceAsStream(indexDefinitionFile);
        var indexDef = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP PUT request to create the index in the search service
        var request = httpRequest(uri, _adminKey, "PUT", indexDef);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Verwijder de opmerking de volgende code in de klasse `App`. Deze code verwijdert de index "Hotels-Quick Start" als deze bestaat en maakt een nieuwe index op basis van de index definitie in het bestand index. json. 

    Een pauze van één seconde wordt ingevoegd na de aanvraag voor het maken van de index. Met deze onderbreking zorgt u ervoor dat de index wordt gemaakt voordat u documenten uploadt.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. Open het **maven** -programma venster en voer dit maven doel uit: `verify exec:java`

    Als de code wordt uitgevoerd, zoekt u naar het bericht ' Creating index ', gevolgd door een 201-respons code. Met deze antwoord code wordt bevestigd dat de index is gemaakt. De uitvoering moet eindigen met een succes bericht van de BUILD en een afsluit code van nul (0).
    
## <a name="2---load-documents"></a>2-documenten laden

1. Vouw in het **project** venster de bron structuur uit om toegang te krijgen tot de `src` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7-map en voeg een `hotels.json`-bestand toe. Als u dit wilt doen, selecteert u de map `app`, drukt u op ALT + INSERT, selecteert u **bestand**en voert u de bestands naam in.
1. Voeg de volgende Hotel documenten in het bestand in.

    ```json
    {
      "value": [
        {
          "@search.action": "upload",
          "HotelId": "1",
          "HotelName": "Secret Point Motel",
          "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
          "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
          "Category": "Boutique",
          "Tags": [ "pool", "air conditioning", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1970-01-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "2",
          "HotelName": "Twin Dome Motel",
          "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Boutique",
          "Tags": [ "pool", "free wifi", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1979-02-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "3",
          "HotelName": "Triple Landscape Hotel",
          "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Resort and Spa",
          "Tags": [ "air conditioning", "bar", "continental breakfast" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "2015-09-20T00:00:00Z",
          "Rating": 4.80,
          "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "4",
          "HotelName": "Sublime Cliff Hotel",
          "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
          "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
          "Category": "Boutique",
          "Tags": [ "concierge", "view", "24-hour front desk service" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "1960-02-06T00:00:00Z",
          "Rating": 4.60,
          "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
          }
        }
      ]
    }
    ```

1. Voeg de volgende code toe aan de klasse `SearchServiceClient`. Met deze code wordt de URL van de REST-service gebaseerd op het uploaden van de Hotel documenten naar de index en wordt vervolgens de HTTP POST-aanvraag gemaakt.

    ```java
    public boolean uploadDocuments(String documentsFile) throws IOException, InterruptedException {
        logMessage("\n Uploading documents...");
        //Build the search service URL
        var endpoint = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs/index?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in the data to index
        var inputStream = SearchServiceClient.class.getResourceAsStream(documentsFile);
        var documents = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP POST request to upload and index the data
        var request = httpRequest(endpoint, _adminKey, "POST", documents);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Verwijder de opmerking de volgende code in de klasse `App`. Deze code uploadt de documenten in "Hotels. json" naar de index.

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    Na de upload aanvraag wordt een pauze van twee seconden ingevoegd om ervoor te zorgen dat het proces voor het laden van documenten is voltooid voordat u een query op de index uitvoert.

1. Open het **maven** -programma venster en voer dit maven doel uit: `verify exec:java`

    Omdat u in de vorige stap een "Hotels-Quick Start"-index hebt gemaakt, wordt de code nu verwijderd en opnieuw gemaakt voordat de documenten in het hotel worden geladen.

    Wanneer de code wordt uitgevoerd, zoekt u naar het bericht ' documenten uploaden ' gevolgd door een 200-respons code. Met deze antwoord code wordt bevestigd dat de documenten zijn geüpload naar de index. De uitvoering moet eindigen met een succes bericht van de BUILD en een afsluit code van nul (0).

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

Nu u de documenten in de hotels hebt geladen, kunt u zoek query's maken voor toegang tot de hotels-gegevens.

1. Voeg de volgende code toe aan de klasse `SearchServiceClient`. Deze code bouwt Azure Search-Url's voor REST-services om de geïndexeerde gegevens te doorzoeken en de zoek resultaten af te afdrukken.

    Met de klasse `SearchOptions` en de `createSearchOptions` kunt u een subset opgeven van de beschik bare Azure Search REST API Query opties. Zie [zoeken naar documenten (Azure Search Service rest API)](/rest/api/searchservice/search-documents)voor meer informatie over de rest API Query opties.

    De `SearchPlus`-methode maakt de zoek query-URL, maakt de zoek opdracht en drukt de resultaten vervolgens naar de-console. 

    ```java
    public SearchOptions createSearchOptions() { return new SearchOptions();}

    //Defines available search parameters that can be set
    public static class SearchOptions {

        public String select = "";
        public String filter = "";
        public int top = 0;
        public String orderby= "";
    }

    //Concatenates search parameters to append to the search request
    private String createOptionsString(SearchOptions options)
    {
        String optionsString = "";
        if (options != null) {
            if (options.select != "")
                optionsString = optionsString + "&$select=" + options.select;
            if (options.filter != "")
                optionsString = optionsString + "&$filter=" + options.filter;
            if (options.top != 0)
                optionsString = optionsString + "&$top=" + options.top;
            if (options.orderby != "")
                optionsString = optionsString + "&$orderby=" +options.orderby;
        }
        return optionsString;
    }
    
    public void searchPlus(String queryString)
    {
        searchPlus( queryString, null);
    }
    
    public void searchPlus(String queryString, SearchOptions options) {
    
        try {
            String optionsString = createOptionsString(options);
            var uri = buildURI(strFormatter -> strFormatter.format(
                    "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=%s%s",
                    _serviceName, _indexName, _apiVersion, queryString, optionsString));
            var request = httpRequest(uri, _queryKey, "GET", null);
            var response = sendRequest(request);
            var jsonReader = Json.createReader(new StringReader(response.body()));
            var jsonArray = jsonReader.readObject().getJsonArray("value");
            var resultsCount = jsonArray.size();
            logMessage("Results:\nCount: " + resultsCount);
            for (int i = 0; i <= resultsCount - 1; i++) {
                logMessage(jsonArray.get(i).toString());
            }
    
            jsonReader.close();
    
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    
    }
    ```

1. Verwijder de volgende code in de klasse `App`. Deze code stelt vijf verschillende query's in, met inbegrip van de Zoek tekst, query parameters en gegevens velden die moeten worden geretourneerd. 

    ```java
    // Query 1
    client.logMessage("\n*QUERY 1****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: All fields'");
    client.searchPlus("Atlanta");

    // Query 2
    client.logMessage("\n*QUERY 2****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: HotelName, Tags, Address");
    SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    options2.select = "HotelName,Tags,Address";
    client.searchPlus("Atlanta", options2);

    //Query 3
    client.logMessage("\n*QUERY 3****************************************************************");
    client.logMessage("Search for: wifi & restaurant");
    client.logMessage("Return: HotelName, Description, Tags");
    SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    options3.select = "HotelName,Description,Tags";
    client.searchPlus("wifi,restaurant", options3);

    // Query 4 -filtered query
    client.logMessage("\n*QUERY 4****************************************************************");
    client.logMessage("Search for: all");
    client.logMessage("Filter: Ratings greater than 4");
    client.logMessage("Return: HotelName, Rating");
    SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    options4.filter="Rating%20gt%204";
    options4.select = "HotelName,Rating";
    client.searchPlus("*",options4);

    // Query 5 - top 2 results, ordered by
    client.logMessage("\n*QUERY 5****************************************************************");
    client.logMessage("Search for: boutique");
    client.logMessage("Get: Top 2 results");
    client.logMessage("Order by: Rating in descending order");
    client.logMessage("Return: HotelId, HotelName, Category, Rating");
    SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    options5.top=2;
    options5.orderby = "Rating%20desc";
    options5.select = "HotelId,HotelName,Category,Rating";
    client.searchPlus("boutique", options5);
    ```



    Er zijn twee [manieren om te voldoen aan de voor waarden in een query](search-query-overview.md#types-of-queries): zoeken in volledige tekst en filters. Een zoek opdracht in volledige tekst zoekt naar een of meer voor waarden in @no__t velden in uw index. Een filter is een booleaanse expressie die wordt geëvalueerd ten opzichte van `IsFilterable` velden in een index. U kunt zoeken in volledige tekst en filters samen of afzonderlijk gebruiken.

1. Open het **maven** -programma venster en voer dit maven doel uit: `verify exec:java`

    Zoek naar een samen vatting van elke query en de bijbehorende resultaten. De uitvoering moet zijn voltooid met het bericht BUILD slagen en een afsluit code van nul (0).

## <a name="clean-up"></a>Opruimen

Wanneer u aan het eind van een project aan het werk bent, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief zijn, kunnen kosten in rekening worden. U kunt resources afzonderlijk verwijderen of de resource groep verwijderen om de volledige set resources te verwijderen.

U kunt resources vinden en beheren in de portal met behulp van de koppeling **alle resources** of **resource groepen** in het navigatie deel venster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, Indexeer functies en gegevens bronnen. U kunt afzonderlijke items in de Portal verwijderen om de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze Java-Snelstartgids hebt u een reeks taken uitgevoerd voor het maken van een index, het laden van documenten en het uitvoeren van query's. Als u vertrouwd bent met de basis concepten, raden we de volgende artikelen aan voor meer informatie.

+ [Index bewerkingen](/rest/api/searchservice/index-operations)

+ [Document bewerkingen](/rest/api/searchservice/document-operations)

+ [Indexeer bewerkingen](/rest/api/searchservice/indexer-operations)
