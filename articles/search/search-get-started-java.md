---
title: "Snelstart: Een zoekindex maken in Java met REST API's"
titleSuffix: Azure Cognitive Search
description: In deze Quickstart java leert u hoe u een index maakt, gegevens laadt en query's uitvoert met de AZURE Cognitive Search REST API's.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: java
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/25/2020
ms.openlocfilehash: 0b07e934625b09e7f6249dc00865465147f6f0ba
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77624024"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-java-using-rest-apis"></a>Snelstart: een Azure Cognitive Search-index maken in Java met REST API's
> [!div class="op_single_selector"]
> * [Javascript](search-get-started-nodejs.md)
> * [C #](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Portal](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Maak een Java-consoletoepassing die een zoekindex maakt, laadt en opvraagt met [IntelliJ,](https://www.jetbrains.com/idea/) [Java 11 SDK](/java/azure/jdk/?view=azure-java-stable)en de [Azure Cognitive Search REST API](/rest/api/searchservice/). Dit artikel bevat stapsgewijze instructies voor het maken van de toepassing. U ook [de volledige toepassing downloaden en uitvoeren.](/samples/azure-samples/azure-search-java-samples/java-sample-quickstart/)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

We hebben de volgende software en services gebruikt om deze quickstart te bouwen en te testen:

+ [IntelliJ IDEA](https://www.jetbrains.com/idea/)

+ [Java 11 SDK](/java/azure/jdk/?view=azure-java-stable)

+ [Maak een Azure Cognitive Search-service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) onder uw huidige abonnement. U gebruik maken van een gratis service voor deze quickstart.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Een sleutel en URL ophalen

Voor oproepen naar de service is een URL-eindpunt en een toegangssleutel vereist voor elk verzoek. Er wordt een zoekservice gemaakt met beide, dus als u Azure Cognitive Search aan uw abonnement hebt toegevoegd, voert u de volgende stappen uit om de benodigde informatie te krijgen:

1. [Meld u aan bij de Azure-portal](https://portal.azure.com/)en ontvang de URL op de pagina **Overzicht** van uw zoekservice. Een eindpunt ziet er bijvoorbeeld uit als `https://mydemo.search.windows.net`.

2. Ontvang **in Instellingentoetsen** > **Keys**een beheersleutel voor volledige rechten op de service. Er zijn twee verwisselbare beheerderssleutels, voorzien voor bedrijfscontinuïteit voor het geval u er een moet omdraaien. U de primaire of secundaire sleutel gebruiken voor aanvragen voor het toevoegen, wijzigen en verwijderen van objecten.

   Maak ook een querytoets. Het is een aanbevolen manier om queryaanvragen uit te geven met alleen-lezen toegang.

![De servicenaam en -beheerder en querysleutels ophalen](media/search-get-started-nodejs/service-name-and-keys.png)

Elk verzoek dat naar uw service wordt verzonden, vereist een api-sleutel. Met een geldige sleutel stelt u per aanvraag een vertrouwensrelatie in tussen de toepassing die de aanvraag verzendt en de service die de aanvraag afhandelt.

## <a name="set-up-your-environment"></a>Uw omgeving instellen

Begin met het openen van IntelliJ IDEA en het opzetten van een nieuw project.

### <a name="create-the-project"></a>Het project maken

1. Open IntelliJ IDEA en selecteer **Nieuw project maken**.
1. Selecteer **Maven**.
1. Selecteer in de **lijst Project SDK** de Java 11 SDK.

    ![Een maven-project maken](media/search-get-started-java/java-quickstart-create-new-maven-project.png) 

1. Voer **voor GroupId** en `AzureSearchQuickstart` **ArtifactId**.
1. Accepteer de resterende standaardinstellingen om het project te openen.

### <a name="specify-maven-dependencies"></a>Maven-afhankelijkheden opgeven

1. Selecteer **Bestandsinstellingen** > **Settings**.
1. Selecteer **in** het venster Instellingen **Build, Execution, Deployment** > **Build Tools** > **Maven** > **Importeren**.
1. Schakel het selectievakje **Maven-projecten importeren automatisch** in en klik op **OK** om het venster te sluiten. Maven-plug-ins en andere afhankelijkheden worden nu automatisch gesynchroniseerd wanneer u het pom.xml-bestand in de volgende stap bijwerkt.

    ![Maven importeeropties in IntelliJ-instellingen](media/search-get-started-java/java-quickstart-settings-import-maven-auto.png)

1. Open het pom.xml-bestand en vervang de inhoud door de volgende Maven-configuratiegegevens. Deze omvatten verwijzingen naar de [Exec Maven Plugin](https://www.mojohaus.org/exec-maven-plugin/) en een [JSON interface API](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2)

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

### <a name="set-up-the-project-structure"></a>De projectstructuur instellen

1. Selecteer > **Bestandsprojectstructuur**. **File**
1. Selecteer **Modules**en vouw de bronstructuur uit `src`  >   `main` om toegang te krijgen tot de inhoud van de map.
1. Voeg `src`  >   `main`  >  `java` in de `app` `service` map toe en mappen toe. Selecteer hiervoor de `java` map, druk op Alt + Invoegen en voer de naam van de map in.
1. Voeg `src`  >   `main`  > `resources` in de `app` `service` map toe en mappen toe.

    Wanneer u klaar bent, moet de projectstructuur er als volgt uitzien.

    ![Projectmapstructuur](media/search-get-started-java/java-quickstart-basic-code-tree.png)

1. Klik op **OK** om het venster te sluiten.

### <a name="add-azure-cognitive-search-service-information"></a>Azure Cognitive Search-servicegegevens toevoegen

1. Vouw in het venster **Project** de `src`  >   `main`  > bronstructuur uit om toegang te krijgen tot de `resources`  >  `app` map en voeg een `config.properties` bestand toe. Selecteer hiervoor de `app` map, druk op Alt + Invoegen, selecteer **Bestand**en voer de bestandsnaam in.

1. Kopieer de volgende instellingen naar `<YOUR-SEARCH-SERVICE-NAME>`het `<YOUR-ADMIN-KEY>`nieuwe `<YOUR-QUERY-KEY>` bestand en vervang , en met uw servicenaam en toetsen. Als uw service `https://mydemo.search.windows.net`eindpunt is, zou de service naam "mydemo".

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2019-05-06
    ```

### <a name="add-the-main-method"></a>De hoofdmethode toevoegen

1. Voeg `src`  >   `main`  >  `java`  >  in de `app` `App` map een klasse toe. Selecteer hiervoor de `app` map, druk op Alt + Invoegen, selecteer **Javaklasse**en voer de klassennaam in.
1. Open `App` de klasse en vervang de inhoud door de volgende code. Deze code `main` bevat de methode. 

    De niet-gereageerde code leest de parameters van de zoekservice en gebruikt deze om een instantie van de zoekserviceclient te maken. De clientcode van de zoekservice wordt in de volgende sectie toegevoegd.

    De commentaarcode in deze klasse wordt in een later gedeelte van deze quickstart niet gereageerd.

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

1. Voeg `src`  >   `main`  >  `java`  >  in de `service` `SearchServiceClient` map een klasse toe. Selecteer hiervoor de `service` map, druk op Alt + Invoegen, selecteer **Javaklasse**en voer de klassennaam in.
1. Open `SearchServiceClient` de klasse en vervang de inhoud door de volgende code. Deze code biedt de HTTP-bewerkingen die nodig zijn om de Azure Cognitive Search REST API te gebruiken. In een latere sectie worden aanvullende methoden toegevoegd voor het maken van een index, het uploaden van documenten en het opvragen van de index.

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

    ![Projectmapstructuur](media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png)

1. Open het **maven-gereedschapsvenster** en voer `verify exec:java` 
 ![dit maven-doel uit: Maven-doel uitvoeren: exec:java verifiëren](media/search-get-started-java/java-quickstart-execute-maven-goal.png)

Wanneer de verwerking is voltooid, zoekt u naar een BUILD SUCCESS-bericht, gevolgd door een nulcode (0).

## <a name="1---create-index"></a>1 - Index maken

De definitie van de hotelindex bevat eenvoudige velden en één complex veld. Voorbeelden van een eenvoudig veld zijn 'HotelName' of 'Beschrijving'. Het veld 'Adres' is een complex veld omdat het subvelden heeft, zoals 'Adres' en 'Stad'. In deze quickstart wordt de indexdefinitie opgegeven met JSON.

1. Vouw in het venster **Project** de `src`  >   `main`  > bronstructuur uit om toegang te krijgen tot de `resources`  >  `service` map en voeg een `index.json` bestand toe. Selecteer hiervoor de `app` map, druk op Alt + Invoegen, selecteer **Bestand**en voer de bestandsnaam in.

1. Open `index.json` het bestand en voeg de volgende indexdefinitie in.

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

    De indexnaam is "hotels-quickstart". Kenmerken in de indexvelden bepalen hoe de geïndexeerde gegevens in een toepassing kunnen worden doorzocht. Het `IsSearchable` kenmerk moet bijvoorbeeld worden toegewezen aan elk veld dat moet worden opgenomen in een zoekopdracht met volledige tekst. Zie Verzameling en [veldkenmerken velden](search-what-is-an-index.md#fields-collection)voor meer informatie over kenmerken .
    
    Het `Description` veld in deze `analyzer` index gebruikt de optionele eigenschap om de standaard Lucene-taalanalyzer te overschrijven. Het `Description_fr` veld maakt gebruik van de `fr.lucene` Franse Lucene analyzer omdat het Franse tekst opslaat. Het `Description` is met behulp van de optionele Microsoft taal analyzer en.lucene. Zie [Analysevan gegevens voor tekstverwerking in Azure Cognitive Search](search-analyzers.md)voor meer informatie over analysers.

1. Voeg de volgende `SearchServiceClient` code toe aan de klasse. Deze methoden bouwen AZURE Cognitive Search REST-service-URL's die een index maken en verwijderen en die bepalen of er een index bestaat. De methoden maken ook de HTTP-aanvraag.

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

1. Geef de volgende code `App` in de klasse ongedaan. Met deze code wordt de index 'hotels-quickstart' verwijderd als deze bestaat, en wordt een nieuwe index gemaakt op basis van de indexdefinitie in het bestand "index.json". 

    Er wordt een pauze van één seconde ingevoegd na de aanvraag voor het maken van de index. Deze pauze zorgt ervoor dat de index wordt gemaakt voordat u documenten uploadt.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. Open het **maven-gereedschapsvenster** en voer dit maven-doel uit:`verify exec:java`

    Zoek naar een bericht 'Index maken' op basis van de code, gevolgd door een reactiecode van 201. Deze reactiecode bevestigt dat de index is gemaakt. De run moet eindigen met een BUILD SUCCESS-bericht en een nul (0) exitcode.
    
## <a name="2---load-documents"></a>2 - Documenten laden

1. Vouw in het venster **Project** de `src`  >   `main`  > bronstructuur uit om toegang te krijgen tot de `resources`  >  `service` map en voeg een `hotels.json` bestand toe. Selecteer hiervoor de `app` map, druk op Alt + Invoegen, selecteer **Bestand**en voer de bestandsnaam in.
1. Voeg de volgende hoteldocumenten in het bestand in.

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

1. Voeg de volgende `SearchServiceClient` code in de klasse in. Deze code bouwt de URL van de REST-service om de hoteldocumenten naar de index te uploaden en maakt vervolgens het HTTP POST-verzoek.

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

1. Geef de volgende code `App` in de klasse ongedaan. Deze code uploadt de documenten in "hotels.json" naar de index.

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    Er wordt een pauze van twee seconden ingevoegd na het uploadverzoek om ervoor te zorgen dat het laadproces van het document is voltooid voordat u de index opvraagt.

1. Open het **maven-gereedschapsvenster** en voer dit maven-doel uit:`verify exec:java`

    Omdat u in de vorige stap een index 'hotels-quickstart' hebt gemaakt, wordt deze nu verwijderd en opnieuw gemaakt voordat u de hoteldocumenten laadt.

    Zoek naar een bericht 'Documenten uploaden' waarna de code wordt uitgevoerd, gevolgd door een 200-antwoordcode. Deze reactiecode bevestigt dat de documenten zijn geüpload naar de index. De run moet eindigen met een BUILD SUCCESS-bericht en een nul (0) exitcode.

## <a name="3---search-an-index"></a>3 - Een index doorzoeken

Nu u de hotelsdocumenten hebt geladen, u zoekopdrachten maken om toegang te krijgen tot de hotelgegevens.

1. Voeg de volgende `SearchServiceClient` code toe aan de klasse. Met deze code worden URL's van azure cognitive search REST-service gebouwd om de geïndexeerde gegevens te doorzoeken en de zoekresultaten af te drukken.

    Met `SearchOptions` de `createSearchOptions` klasse en methode u een subset opgeven van de beschikbare Azure Cognitive Search REST-queryopties. Zie [Zoekdocumenten (Azure Cognitive Search REST API)](/rest/api/searchservice/search-documents)voor meer informatie over de opties voor de REST API-query.

    De `SearchPlus` methode maakt de URL van de zoekopdracht, maakt de zoekaanvraag en drukt de resultaten vervolgens af op de console. 

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

1. Geef `App` in de klasse geen commentaar op de volgende code. Met deze code worden vijf verschillende query's ingesteld, waaronder de zoektekst, queryparameters en gegevensvelden om terug te keren. 

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



    Er zijn twee [manieren om termen in een query te matchen:](search-query-overview.md#types-of-queries)full-text search en filters. Een zoekopdracht met volledige tekst zoekt naar `IsSearchable` een of meer termen in velden in uw index. Een filter is een booleaanse `IsFilterable` expressie die wordt geëvalueerd over velden in een index. U zoeken en filteren op volledige tekst samen of afzonderlijk gebruiken.

1. Open het **maven-gereedschapsvenster** en voer dit maven-doel uit:`verify exec:java`

    Zoek naar een overzicht van elke query en de resultaten ervan. De run moet worden voltooid met BUILD SUCCESS-bericht en een nul (0) exitcode.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u aan het einde van een project in uw eigen abonnement werkt, is het een goed idee om de resources te verwijderen die u niet meer nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U resources in de portal vinden en beheren met de koppeling **Alle resources** of **Resourcegroepen** in het linkernavigatiedeelvenster.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u beperkt bent tot drie indexen, indexeerders en gegevensbronnen. U afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

In deze Java-quickstart hebt u een reeks taken doorlopen om een index te maken, deze te laden met documenten en query's uit te voeren. Als u vertrouwd bent met de basisconcepten, raden we het volgende artikel aan waarin indexerbewerkingen in REST worden weergegeven.

> [!div class="nextstepaction"]
> [Indexerbewerkingen](/rest/api/searchservice/indexer-operations)
