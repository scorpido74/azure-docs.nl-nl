---
title: Java-quickstart voor Metrics Advisor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: ff8a09e32a44f51571cca93655f91080e5df9a50
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186910"
---
[Referentiedocumentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src) | [Artefact (Maven)](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulpprogramma Gradle](https://gradle.org/install/) of een andere afhankelijkheidsbeheerder.
* Wanneer u uw Azure-abonnement hebt, kunt u <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Een Metrics Advisor-resource maken"  target="_blank">een Metrics Advisor-resource maken<span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw Metrics Advisor-instantie te implementeren.  
* Uw eigen SQL-database met tijdreeksgegevens.
  
  
> [!TIP]
> * Op [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples) vindt u Java-voorbeelden voor Metrics Advisor.
> * Het kan 10 tot 30 minuten duren voordat uw Metrics Advisor-resource een service-instantie heeft geïmplementeerd die u kunt gebruiken. Klik op **Naar de resource gaan** zodra de implementatie is voltooid. Na de implementatie kunt u uw Metrics Advisor-instantie gebruiken met zowel de webportal als de REST API. 
> * U kunt de URL voor de REST API in Azure Portal vinden in het gedeelte **Overzicht** van uw resource. Dit zal er als volgt uitzien:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Instellen

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

Deze quickstart maakt gebruik van de Gradle-afhankelijkheidsmanager. U vindt meer informatie over clientbibliotheken in de [centrale opslagplaats van Maven](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `gradle init` uit vanuit uw werkmap. Met deze opdracht maakt u essentiële buildbestanden voor Gradle, inclusief *build.gradle.kts* , dat tijdens runtime wordt gebruikt om de toepassing te maken en te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Zoek *build.gradle.kts* op en open het met uw favoriete IDE of teksteditor. Kopieer het vervolgens in deze buildconfiguratie. Zorg ervoor dat u de projectafhankelijkheden mee kopieert.

```kotlin
dependencies {
    compile("com.azure:azure-ai-metricsadvisor:1.0.0-beta.1")
}
```

### <a name="create-a-java-file"></a>Een Java-bestand maken

Maak een map voor de voorbeeld-app. Voer de volgende opdracht uit vanuit uw werkmap:

```console
mkdir -p src/main/java
```

Ga naar de nieuwe map en maak een bestand met de naam *MetricsAdvisorQuickstarts.java*. Open het bestand in uw voorkeurseditor of IDE en voeg de volgende `import`-instructies toe:

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak in de klasse `MetricsAdvisorQuickstarts` van de toepassing variabelen voor de sleutel en het eindpunt van uw resource.


> [!IMPORTANT]
> Ga naar Azure Portal. Als de Metrics Advisor-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt uw abonnementssleutels en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. <br><br>Als u uw API-sleutel wilt ophalen, moet u naar [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) gaan. Selecteer de gewenste optie: **Map** , **Abonnementen** en **Werkruimte** voor uw resource en kies **Aan de slag**. Vervolgens kunt u de API-sleutels ophalen van [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key).   
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) voor meer informatie.

```java
private static String SUBSCRIPTION_KEY = "<replace-with-your-metrics-advisor-subscription-key-here>";
private static String API_KEY = "<replace-with-your-metrics-advisor-api-key-here>"
private static String ENDPOINT = "<replace-with-your-metrics-advisor-endpoint-here>";
```

Voeg in de methode `Main()` van de toepassing aanroepen toe voor de methoden die in deze quickstart worden gebruikt. U gaat deze later maken.

```java
static void Main(string[] args){

    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Objectmodel

De volgende klassen worden gebruikt voor enkele van de belangrijkste functies van de Metrics Advisor Java-SDK.

|Naam|Beschrijving|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/MetricsAdvisorClient.html) | **Gebruikt voor** : <br> - het weergeven van incidenten <br> - het weergeven van hoofdoorzaken van incidenten <br> - het ophalen van de oorspronkelijke tijdreeksgegevens en tijdreeksgegevens die door de service zijn verrijkt <br> - het weergeven van waarschuwingen <br> - het toevoegen van feedback om uw model af te stemmen |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/administration/MetricsAdvisorAdministrationClient.html)| **Hiermee kunt u:** <br> - gegevensfeeds beheren <br> - configuratie voor anomaliedetectie configureren <br> - configuratie voor anomaliewaarschuwingen configureren <br> - hooks beheren  |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/models/DataFeed.html)| **Wat Metrics Advisor van uw gegevensbron opneemt. Een `DataFeed` bevat rijen van:** <br> - tijdstempels <br> - nul of meer dimensies <br> - één of meer metingen  |
| [Meting](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/models/Metric.html) | Een `Metric` is een kwantificeerbaar metrisch gegeven dat wordt gebruikt om de status van een specifiek bedrijfsproces te controleren en te beoordelen. Dit kan een combinatie zijn van meerdere tijdreekswaarden, onderverdeeld in dimensies. Een metrisch gegeven dat de status van het web aangeeft, kan bijvoorbeeld dimensies bevatten voor het aantal gebruikers en voor het en-us-taalgebied. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Metrics Advisor-clientbibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Een gegevensfeed toevoegen](#add-a-data-feed)
* [De opnamestatus controleren](#check-the-ingestion-status)
* [Anomaliedetectie configureren](#configure-anomaly-detection)
* [Een hook maken](#create-a-hook)
* [Een waarschuwingsconfiguratie maken](#create-an-alert-configuration)
* [De waarschuwing opvragen](#query-the-alert)

## <a name="authenticate-the-client"></a>De client verifiëren

### <a name="create-a-metrics-advisor-client-using-metricsadvisorkeycredential"></a>Een Metrics Advisor-client maken met behulp van MetricsAdvisorKeyCredential

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorClient metricsAdvisorClient = new MetricsAdvisorClientBuilder()
    .endpoint(ENDPOINT)
    .credential(credential)
    .buildClient();
```

### <a name="create-a-metrics-administration-client-using-metricsadvisorkeycredential"></a>Een Metrics Administration-client maken met behulp van MetricsAdvisorKeyCredential

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorAdministrationClient metricsAdvisorAdministrationClient =
    new MetricsAdvisorAdministrationClientBuilder()
        .endpoint(ENDPOINT)
        .credential(credential)
        .buildClient();

``` 

## <a name="add-a-data-feed"></a>Een gegevensfeed toevoegen

Vervang `sql_server_connection_string` door uw eigen SQL Server-verbindingsreeks en vervang `query` door een query die uw gegevens voor één enkele tijdstempel retourneert. U moet ook de waarden `metric` en `dimension` aanpassen op basis van uw aangepaste gegevens.

> [!IMPORTANT]
> Met de query wordt maximaal één record per dimensiecombinatie geretourneerd voor elke tijdstempel. En alle records die zijn geretourneerd met de query, moeten dezelfde tijdstempels hebben. In Metrics Advisor wordt deze query uitgevoerd voor elke tijdstempel om uw gegevens op te nemen. Raadpleeg de sectie [Veelgestelde vragen over query's](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) voor meer informatie en voorbeelden. 

```java
final DataFeed createdSqlDataFeed = metricsAdvisorAdministrationClient.createDataFeed(
    "My data feed name",
    new SQLServerDataFeedSource("sql_server_connection_string", "query"),
    new DataFeedGranularity().setGranularityType(DataFeedGranularityType.DAILY),
    new DataFeedSchema(Arrays.asList(
        new Metric().setName("cost"),
        new Metric().setName("revenue")))
        .setDimensions(Arrays.asList(
            new Dimension().setName("category"),
            new Dimension().setName("city"))),
    new DataFeedIngestionSettings(OffsetDateTime.parse("2020-01-01T00:00:00Z")),
    new DataFeedOptions()
        .setDescription("My data feed description")
        .setRollupSettings(
            new DataFeedRollupSettings()
                .setAutoRollup(DataFeedAutoRollUpMethod.SUM, Arrays.asList("cost"), "__CUSTOM_SUM__"))
        .setMissingDataPointFillSettings(
            new DataFeedMissingDataPointFillSettings()
                .setFillType(DataSourceMissingDataPointFillType.SMART_FILLING))
        .setAccessMode(DataFeedAccessMode.PUBLIC));

System.out.printf("Data feed Id : %s%n", createdSqlDataFeed.getId());
System.out.printf("Data feed name : %s%n", createdSqlDataFeed.getName());
System.out.printf("Is the query user is one of data feed administrator : %s%n", createdSqlDataFeed.isAdmin());
System.out.printf("Data feed created time : %s%n", createdSqlDataFeed.getCreatedTime());
System.out.printf("Data feed granularity type : %s%n",
    createdSqlDataFeed.getGranularity().getGranularityType());
System.out.printf("Data feed granularity value : %d%n",
    createdSqlDataFeed.getGranularity().getCustomGranularityValue());
System.out.println("Data feed related metric Ids:");
createdSqlDataFeed.getMetricIds().forEach(metricId -> System.out.println(metricId));
System.out.printf("Data feed source type: %s%n", createdSqlDataFeed.getSourceType());
createdSqlDataFeed.getSchema().getMetrics().forEach(metric -> {
    System.out.printf("metric name: %s metric id:%s", metric.getName(), metric.getId());
});

System.out.printf("Data feed sql server query: %s%n",
    ((SQLServerDataFeedSource) createdSqlDataFeed.getSource()).getQuery());
```

## <a name="check-the-ingestion-status"></a>De opnamestatus controleren

In dit voorbeeld wordt de opnamestatus van een eerder opgegeven bron van een gegevensfeed gecontroleerd.

```java
String dataFeedId = "<use-the-data-feed-id-from-createdSqlDataFeed.getId()"; 

metricsAdvisorAdministrationClient.listDataFeedIngestionStatus(
    dataFeedId,
    new ListDataFeedIngestionOptions(
        OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.parse("2020-09-09T00:00:00Z"))
).forEach(dataFeedIngestionStatus -> {
    System.out.printf("Message : %s%n", dataFeedIngestionStatus.getMessage());
    System.out.printf("Timestamp value : %s%n", dataFeedIngestionStatus.getTimestamp());
    System.out.printf("Status : %s%n", dataFeedIngestionStatus.getStatus());
});
```
## <a name="configure-anomaly-detection"></a>Anomaliedetectie configureren 

In dit voorbeeld ziet u hoe een gebruiker een anomaliedetectieconfiguratie voor zijn gegevens kan configureren.

```java
String metricId = "<metric-id-from-adding-data-feed>";

ChangeThresholdCondition changeThresholdCondition = new ChangeThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.BOTH)
    .setChangePercentage(20)
    .setShiftPoint(10)
    .setWithinRange(true)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

HardThresholdCondition hardThresholdCondition = new HardThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.DOWN)
    .setLowerBound(5.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(1));

SmartDetectionCondition smartDetectionCondition = new SmartDetectionCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.UP)
    .setSensitivity(10.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

final AnomalyDetectionConfiguration anomalyDetectionConfiguration =
    metricsAdvisorAdministrationClient.createMetricAnomalyDetectionConfiguration(
        metricId,
        new AnomalyDetectionConfiguration("My Anomaly detection configuration")
            .setDescription("anomaly detection config description")
            .setWholeSeriesDetectionCondition(
                new MetricWholeSeriesDetectionCondition()
                    .setChangeThresholdCondition(changeThresholdCondition)
                    .setHardThresholdCondition(hardThresholdCondition)
                    .setSmartDetectionCondition(smartDetectionCondition)
                    .setCrossConditionOperator(DetectionConditionsOperator.OR))
    );
```

## <a name="create-a-hook"></a>Een hook maken

In dit voorbeeld wordt een e-mailhook gemaakt die incidentwaarschuwingen bij anomalieën ontvangt.

```java
Hook emailHook = new EmailHook("email hook")
    .setDescription("my email hook")
    .addEmailToAlert("alertme@alertme.com")
    .setExternalLink("https://adwiki.azurewebsites.net/articles/howto/alerts/create-hooks.html");

final Hook hook = metricsAdvisorAdministrationClient.createHook(emailHook);
EmailHook createdEmailHook = (EmailHook) hook;
System.out.printf("Hook Id: %s%n", createdEmailHook.getId());
System.out.printf("Hook Name: %s%n", createdEmailHook.getName());
System.out.printf("Hook Description: %s%n", createdEmailHook.getDescription());
System.out.printf("Hook External Link: %s%n", createdEmailHook.getExternalLink());
System.out.printf("Hook Emails: %s%n", String.join(",", createdEmailHook.getEmailsToAlert()));
```

##  <a name="create-an-alert-configuration"></a>Een waarschuwingsconfiguratie maken

Dit voorbeeld laat zien hoe een gebruiker een waarschuwingsconfiguratie kan configureren voor anomalieën die in zijn gegevens zijn gedetecteerd.

```java
String detectionConfigurationId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String detectionConfigurationId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

final AnomalyAlertConfiguration anomalyAlertConfiguration
    = metricsAdvisorAdministrationClient.createAnomalyAlertConfiguration(
        new AnomalyAlertConfiguration("My Alert config name")
            .setDescription("alert config description")
            .setMetricAlertConfigurations(
                Arrays.asList(
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId1,
                        MetricAnomalyAlertScope.forWholeSeries()),
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId2,
                        MetricAnomalyAlertScope.forWholeSeries())
                        .setAlertConditions(new MetricAnomalyAlertConditions()
                            .setSeverityCondition(new SeverityCondition()
                                .setMaxAlertSeverity(Severity.HIGH)))
                ))
            .setCrossMetricsOperator(MetricAnomalyAlertConfigurationsOperator.AND)
            .setIdOfHooksToAlert(Arrays.asList(hookId1, hookId2)));
```

## <a name="query-the-alert"></a>De waarschuwing opvragen

Dit voorbeeld laat zien hoe een gebruiker waarschuwingen kan opvragen die zijn geactiveerd voor een waarschuwingsdetectieconfiguratie, en hoe hij anomalieën voor die waarschuwing kan ophalen.

```java
String alertConfigurationId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
metricsAdvisorClient.listAlerts(
    alertConfigurationId,
    new ListAlertOptions(OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.now(),
        TimeMode.ANOMALY_TIME))
    .forEach(alert -> {
        System.out.printf("Alert Id: %s%n", alert.getId());
        System.out.printf("Alert created on: %s%n", alert.getCreatedTime());

        // List anomalies for returned alerts
        metricsAdvisorClient.listAnomaliesForAlert(
            alertConfigurationId,
            alert.getId())
            .forEach(anomaly -> {
                System.out.printf("Anomaly was created on: %s%n", anomaly.getCreatedTime());
                System.out.printf("Anomaly severity: %s%n", anomaly.getSeverity().toString());
                System.out.printf("Anomaly status: %s%n", anomaly.getStatus());
                System.out.printf("Anomaly related series key: %s%n", anomaly.getSeriesKey().asMap());
            });
    });
```

U kunt de app maken met:

```console
gradle build
```
### <a name="run-the-application"></a>De toepassing uitvoeren

De toepassing uitvoeren met het doel `run`:

```console
gradle run
```