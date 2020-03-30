---
title: Problemen oplossen met intermitterende uitgaande verbindingsfouten in Azure App Service
description: Problemen met intermitterende verbindingsfouten en gerelateerde prestatieproblemen in Azure App Service oplossen
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367549"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Problemen oplossen met intermitterende uitgaande verbindingsfouten in Azure App Service

Met dit artikel u intermitterende verbindingsfouten en gerelateerde prestatieproblemen oplossen in [Azure App Service.](https://docs.microsoft.com/azure/app-service/overview) Dit onderwerp geeft meer informatie over en probleemoplossingsmethoden voor, uitputting van SNAT-poorten (Source Address Network Translation). Als u op enig moment in dit artikel meer hulp nodig hebt, neemt u contact op met de Azure-experts van de [MSDN Azure- en de Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure Support-site](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.

## <a name="symptoms"></a>Symptomen

Toepassingen en functies die worden gehost op de Azure App-service kunnen een of meer van de volgende symptomen vertonen:

* Trage responstijden op alle of sommige exemplaren in een serviceplan.
* Intermitterende 5xx- of **Bad Gateway-fouten**
* Foutberichten time-out
* Kon geen verbinding maken met externe eindpunten (zoals SQLDB, Service Fabric, andere App-services enz.)

## <a name="cause"></a>Oorzaak

Een belangrijke oorzaak van deze symptomen is dat de toepassingsinstantie niet in staat is om een nieuwe verbinding met het externe eindpunt te openen omdat het een van de volgende limieten heeft bereikt:

* TCP-verbindingen: er is een limiet aan het aantal uitgaande verbindingen dat kan worden gemaakt. Dit is gekoppeld aan de grootte van de werknemer die wordt gebruikt.
* SNAT-poorten: Zoals besproken in [uitgaande verbindingen in Azure,](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)gebruikt Azure de vertaling van het bronnetwerkadres (SNAT) en een Load Balancer (niet blootgesteld aan klanten) om te communiceren met eindpunten buiten Azure in de openbare IP-adresruimte. Elke instantie op Azure App-service krijgt in eerste instantie een vooraf toegewezen aantal van **128** SNAT-poorten. Deze limiet is van invloed op het openen van verbindingen met dezelfde host- en poortcombinatie. Als uw app verbindingen maakt met een mix van adres- en poortcombinaties, gebruikt u uw SNAT-poorten niet. De SNAT-poorten zijn opgebruikt wanneer u herhaaldelijk naar hetzelfde adres en dezelfde poortcombinatie bent gebeld. Zodra een poort is vrijgegeven, is de poort beschikbaar voor hergebruik als dat nodig is. De Azure Network load balancer herwint de SNAT-poort alleen van gesloten verbindingen na 4 minuten wachten.

Wanneer toepassingen of functies snel een nieuwe verbinding openen, kunnen ze hun vooraf toegewezen quotum van de 128 poorten snel uitputten. Ze worden vervolgens geblokkeerd totdat er een nieuwe SNAT-poort beschikbaar komt, hetzij door het dynamisch toewijzen van extra SNAT-poorten, hetzij door hergebruik van een teruggewonnen SNAT-poort. Toepassingen of functies die zijn geblokkeerd vanwege dit onvermogen om nieuwe verbindingen te maken, zullen een of meer van de problemen ondervinden die in het gedeelte **Symptomen** van dit artikel worden beschreven.

## <a name="avoiding-the-problem"></a>Het probleem vermijden

Het vermijden van het SNAT-poortprobleem betekent het vermijden van het herhaaldelijk maken van nieuwe verbindingen met dezelfde host en poort.

Algemene strategieën voor het beperken van snat-poortuitputting worden besproken in het gedeelte Probleemoplossen van de uitgaande verbindingen van [Azure-documentatie.](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) **Outbound connections of Azure** Van deze strategieën zijn de volgende strategieën van toepassing op apps en functies die worden gehost op azure-app-service.

### <a name="modify-the-application-to-use-connection-pooling"></a>De toepassing wijzigen om verbindingspooling te gebruiken

* Voor het bundelen van HTTP-verbindingen controleert u [HTTP-verbindingen groep met HttpClientFactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Voor informatie over SQL Server-verbindingspooling controleert [u SQL Server Connection Pooling (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Voor het implementeren van pooling met entiteitskadertoepassingen, controleert u [DbContext-pooling](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Hier volgt een verzameling koppelingen voor het implementeren van Connection pooling by different solution stack.

#### <a name="node"></a>Knooppunt

Standaard worden verbindingen voor NodeJS niet in leven gehouden. Hieronder vindt u de populaire databases en pakketten voor het bundelen van verbindingen die voorbeelden bevatten voor hoe ze te implementeren.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP In leven

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Node.js v13.9.0 Documentatie](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Hieronder vindt u de populaire bibliotheken die worden gebruikt voor JDBC-koppelingspooling die voorbeelden bevatten voor de implementatie ervan: JDBC Connection Pooling.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

HTTP-verbinding pooling

* [Apache-verbindingsbeheer](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [KlassepoolinghttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Hoewel PHP geen ondersteuning biedt voor het poolen van verbindingen, u proberen permanente databaseverbindingen met uw back-endserver te gebruiken.
 
* MySQL-server

   * [MySQLi-aansluitingen](https://www.php.net/manual/mysqli.quickstart.connections.php) voor nieuwere versies
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) voor oudere versies van PHP

* Andere gegevensbronnen

   * [PHP-verbindingsbeheer](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (LET OP: SQLAlchemy kan worden gebruikt met andere databases naast MicrosoftSQL Server)
* [HTTP Keep-alive](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(Keep-Alive is automatisch bij het gebruik van [sessies sessie-objecten](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)).

Voor andere omgevingen, review provider of driver-specifieke documenten voor het implementeren van verbinding pooling in uw toepassingen.

### <a name="modify-the-application-to-reuse-connections"></a>De toepassing wijzigen om verbindingen opnieuw te gebruiken

*  Controleer [Verbindingen beheren in Azure-functies](https://docs.microsoft.com/azure/azure-functions/manage-connections)voor extra aanwijzers en voorbeelden over het beheren van verbindingen in Azure-functies.

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>De toepassing wijzigen om minder agressieve logica voor nieuwe try's te gebruiken

* Voor aanvullende richtlijnen en voorbeelden, bekijk [opnieuw proberen patroon](https://docs.microsoft.com/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Keepalives gebruiken om de uitgaande idle time-out opnieuw in te stellen

* Voor het implementeren van keepalives voor Node.js apps, review [My node application is making excessive outbound calls](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Aanvullende richtlijnen die specifiek zijn voor app-service:

* Een [load test](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) moet real world data simuleren in een constante voedingssnelheid. Het testen van apps en functies onder echte wereldstress kan snat-poortuitputtingsproblemen van tevoren identificeren en oplossen.
* Zorg ervoor dat de back-endservices snel reacties kunnen retourneren. Voor het oplossen van prestatieproblemen met Azure SQL-database, controleert [u problemen met azure SQL-database-prestatieproblemen met Intelligente Inzichten.](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow)
* Schaal het App-serviceplan op naar meer exemplaren. Zie [Een app schalen in Azure App Service](https://docs.microsoft.com/azure/app-service/manage-scale-up)voor meer informatie over schalen. Aan elke werknemerinstantie in een app-serviceplan wordt een aantal SNAT-poorten toegewezen. Als u uw gebruik over meer exemplaren verspreidt, u het SNAT-poortgebruik per instantie onder de aanbevolen limiet van 100 uitgaande verbindingen krijgen, per uniek extern eindpunt.
* Overweeg om over te stappen op [App Service Environment (ASE),](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)waar u één uitgaand IP-adres toegewezen hebt en de limieten voor verbindingen en SNAT-poorten veel hoger zijn.

Het vermijden van de uitgaande TCP-limieten is gemakkelijker op te lossen, omdat de limieten worden ingesteld op de grootte van uw werknemer. U de limieten zien in [Sandbox Cross VM Numerieke limieten - TCP-verbindingen](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Naam beperken|Beschrijving|Klein (A1)|Medium (A2)|Groot (A3)|Geïsoleerde laag (ASE)|
|---|---|---|---|---|---|
|Verbindingen|Aantal verbindingen over hele VM|1920|3968|8064|16.000|

Om uitgaande TCP-limieten te voorkomen, u de grootte van uw werknemers vergroten of horizontaal schalen.

## <a name="troubleshooting"></a>Problemen oplossen

Als u de twee soorten uitgaande verbindingslimieten kent en wat uw app doet, moet het gemakkelijker zijn om problemen op te lossen. Als u weet dat uw app veel gesprekken voert naar hetzelfde opslagaccount, vermoedt u mogelijk een SNAT-limiet. Als uw app een groot aantal oproepen naar eindpunten over het hele internet maakt, zou u vermoeden dat u de VM-limiet bereikt.

Als u het toepassingsgedrag niet voldoende kent om de oorzaak snel te bepalen, zijn er enkele tools en technieken beschikbaar in App Service om te helpen met die bepaling.

### <a name="find-snat-port-allocation-information"></a>SNAT-poorttoewijzingsgegevens zoeken

U [App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) gebruiken om SNAT-poorttoewijzingsgegevens te zoeken en de toewijzingsstatistiek van SNAT-poorten van een App Service-site te observeren. Ga als volgt te werk om snat-poorttoewijzingsgegevens te vinden:

1. Als u toegang wilt krijgen tot diagnostische gegevens van App Service, navigeert u naar uw web-app of App Service-omgeving in de [Azure-portal.](https://portal.azure.com/) Selecteer in de linkernavigatie **De optie Diagnose stellen en problemen oplossen**.
2. Beschikbaarheids- en prestatiecategorie selecteren
3. Selecteer de tegel SNAT-poortuitputting in de lijst met beschikbare tegels onder de categorie. De praktijk is om het onder de 128 te houden.
Als je het nodig hebt, kun je nog steeds een ondersteuningsticket openen en de support engineer krijgt de statistiek van back-end voor je.

Aangezien het gebruik van de SNAT-poort niet beschikbaar is als statistiek, is het niet mogelijk om automatisch te schalen op basis van het gebruik van de SNAT-poort of om automatische schaal te configureren op basis van de toewijzingsstatistiek van SNAT-poorten.

### <a name="tcp-connections-and-snat-ports"></a>TCP-verbindingen en SNAT-poorten

TCP-verbindingen en SNAT-poorten zijn niet direct gerelateerd. Een TCP-verbindingsgebruiksdetector is opgenomen in het blade diagnose- en problemen oplossen van een App Service-site. Zoek naar de term "TCP-verbindingen" om het te vinden.

* De SNAT-poorten worden alleen gebruikt voor externe netwerkstromen, terwijl de totale TCP-verbindingen lokale netbackverbindingen bevatten.
* Een SNAT-poort kan worden gedeeld door verschillende stromen, als de stromen verschillen in protocol, IP-adres of poort. De statistiek TCP Connections telt elke TCP-verbinding.
* De limiet voor TCP-verbindingen gebeurt op het niveau van de werknemerinstantie. De uitgaande taakverdeling voor Azure Network maakt geen gebruik van de statistiek TCP-verbindingen voor SNAT-poortbeperking.
* De limieten voor TCP-verbindingen worden beschreven in [Sandbox Cross VM Numerieke limieten - TCP-verbindingen](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Naam beperken|Beschrijving|Klein (A1)|Medium (A2)|Groot (A3)|Geïsoleerde laag (ASE)|
|---|---|---|---|---|---|
|Verbindingen|Aantal verbindingen over hele VM|1920|3968|8064|16.000|

### <a name="webjobs-and-database-connections"></a>WebJobs- en databaseverbindingen
 
Als SNAT-poorten zijn uitgeput, wanneer WebJobs geen verbinding kan maken met de Azure SQL-database, is er geen statistiek om aan te geven hoeveel verbindingen worden geopend door elk afzonderlijk webtoepassingsproces. Als u de problematische WebJob wilt vinden, verplaatst u verschillende WebJobs naar een ander App Service-abonnement om te zien of de situatie verbetert of dat er een probleem in een van de plannen blijft. Herhaal het proces totdat u de problematische WebJob vindt.

### <a name="using-snat-ports-sooner"></a>SNAT-poorten sneller gebruiken

U geen Azure-instellingen wijzigen om de gebruikte SNAT-poorten eerder vrij te geven, omdat alle SNAT-poorten worden vrijgegeven volgens de onderstaande voorwaarden en het gedrag is door het ontwerp.
 
* Als een server of client FINACK verzendt, wordt de [SNAT-poort](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) na 240 seconden vrijgegeven.
* Als een RST wordt gezien, wordt de SNAT-poort na 15 seconden vrijgegeven.
* Als er een idle time-out is bereikt, wordt de poort vrijgegeven.
 
## <a name="additional-information"></a>Aanvullende informatie

* [SNAT met App-service](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Problemen met trage app-prestaties in Azure App-service oplossen](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
