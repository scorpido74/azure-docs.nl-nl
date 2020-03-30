---
title: Micrometer gebruiken met Azure Application Insights Java SDK
description: Een stapsgewijze handleiding over het gebruik van Micrometer met uw Application Insights Spring Boot en niet-Spring Boot-toepassingen.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: dd04087db32f0bbfa75dafa7e12c355e5ab7b515
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670063"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk"></a>Micrometer gebruiken met Azure Application Insights Java SDK
Micrometer applicatie monitoring meet metrics voor JVM-gebaseerde applicatiecode en u de gegevens exporteren naar uw favoriete monitoring systemen. In dit artikel leert u hoe u Micrometer gebruiken met Application Insights voor zowel Spring Boot als niet-Spring Boot-toepassingen.

## <a name="using-spring-boot-15x"></a>Spring Boot 1,5x gebruiken
Voeg de volgende afhankelijkheden toe aan uw pom.xml- of build.gradle-bestand: 
* [Application Insights spring-boot-starter](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/azure-application-insights-spring-boot-starter) 2.5.0 of hoger
* Micrometer Azure Registry 1.1.0 of hoger
* [Micrometer Spring Legacy](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 of hoger (dit backports de autoconfig code in de lente kader).
* [ApplicationInsights-bron](../../azure-monitor/app/create-new-resource.md )

Stappen

1. Werk het pom.xml-bestand van uw springboot-toepassing bij en voeg daardede volgende afhankelijkheden toe:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Werk de application.properties of het yml-bestand bij met de toets Application Insights Instrumentation met de volgende eigenschap:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Uw toepassing bouwen en uitvoeren
2. Het bovenstaande moet u aan de slag brengen met vooraf geaggregeerde statistieken die automatisch zijn verzameld in Azure Monitor. Voor meer informatie over het verfijnen van Application Insights Spring Boot starter verwijzen naar de [readme op GitHub](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/azure-application-insights-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Voorjaar 2.x gebruiken

Voeg de volgende afhankelijkheden toe aan uw pom.xml- of build.gradle-bestand:

* Application Insights Spring-boot-starter 2.1.2 of hoger
* Azure-spring-boot-metrics-starters 2.0.7 of hoger
* [Resource voor toepassingsinzichten](../../azure-monitor/app/create-new-resource.md )

Stappen:

1. Werk het pom.xml-bestand van uw springboot-toepassing bij en voeg daardevolgende afhankelijkheid toe:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Werk de application.properties of het yml-bestand bij met de toets Application Insights Instrumentation met de volgende eigenschap:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Uw toepassing bouwen en uitvoeren
4. In het bovenstaande u werken met vooraf geaggregeerde statistieken die automatisch zijn verzameld naar Azure Monitor. Voor meer informatie over het verfijnen van Application Insights Spring Boot starter verwijzen naar de [readme op GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Standaardstatistieken:

*    Automatisch geconfigureerde statistieken voor Tomcat, JVM, Logback Metrics, Log4J metrics, Uptime Metrics, Processor Metrics, FileDescriptorMetrics.
*    Als Netflix Hystrix bijvoorbeeld aanwezig is op het klassenpad, krijgen we die statistieken ook. 
*    De volgende statistieken kunnen beschikbaar zijn door het toevoegen van respectievelijke bonen. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - Gegevensbasistabelmetriek 
        - Winterslaapmetrics 
        - JettyMetrics JettyMetrics JettyMetrics JettyMetrics 
        - OkHttp3-statistieken 
        - Kafka-statistieken 

 

Automatische verzameling metrische gegevens uitschakelen: 
 
- JVM-statistieken: 
    - management.metrics.binders.jvm.enabled=false 
- Logboekstatistieken: 
    - management.metrics.binders.logback.enabled=false
- Uptime-statistieken: 
    - management.metrics.binders.uptime.enabled=false 
- Processorstatistieken:
    -  management.metrics.binders.processor.enabled=false 
- FileDescriptorMetrics:
    - management.metrics.binders.files.enabled=false 
- Hystrix Metrics if library on classpath: Hystrix Metrics if library on classpath: Hystrix Metrics if library on classpath: Hystrix 
    - management.metrics.binders.hystrix.enabled=false 
- AspectJ Metrics if library on classpath: 
    - spring.aop.enabled=false 

> [!NOTE]
> Geef de bovenstaande eigenschappen op in het bestand application.properties of application.yml van uw springboot-toepassing

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Micrometer gebruiken met webtoepassingen voor niet-springboot

Voeg de volgende afhankelijkheden toe aan uw pom.xml- of build.gradle-bestand:

* Application Insights Web Auto 2.5.0 of hoger
* Micrometer Azure Registry 1.1.0 of hoger
* [Resource voor toepassingsinzichten](../../azure-monitor/app/create-new-resource.md )

Stappen:

1. Voeg de volgende afhankelijkheden toe aan uw pom.xml- of build.gradle-bestand:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. Bestand `ApplicationInsights.xml` in de map resources plaatsen:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
    
       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
    
       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>
    
       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>
    
    </ApplicationInsights>
    ```

3. Voorbeeld klasse Servlet (zendt een timerstatistiek uit):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

4. Voorbeeldconfiguratieklasse:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

Zie de [micrometerdocumentatie](https://micrometer.io/docs/)voor meer informatie over statistieken.

Andere voorbeeldcode voor het maken van verschillende soorten statistieken is te vinden in[de officiële Micrometer GitHub repo.](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples)

## <a name="how-to-bind-additional-metrics-collection"></a>Extra verzameling met statistieken binden

### <a name="springbootspring"></a>Springboot/Lente

Maak een boon van de betreffende metrische categorie. Stel dat we Guava-cachestatistieken nodig hebben:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Er zijn verschillende statistieken die niet standaard zijn ingeschakeld, maar op de bovenstaande manier kunnen worden gebonden. Voor een volledige lijst, verwijzen wij u naar [de officiële Micrometer GitHub repo](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Niet-lente-apps
Voeg de volgende bindingscode toe aan het configuratiebestand:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Volgende stappen

* Zie de officiële [Micrometer-documentatie](https://micrometer.io/docs)voor meer informatie over Micrometer.
* Zie de officiële [voorjaarsbron op Azure-documentatie](https://docs.microsoft.com/java/azure/spring-framework/?view=azure-java-stable)voor meer informatie over De lente op Azure.
