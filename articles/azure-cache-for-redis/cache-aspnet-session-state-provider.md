---
title: Cache ASP.NET Sessiestatusprovider
description: Meer informatie over het opslaan van ASP.NET sessiestatus in het geheugen met Azure Cache voor Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 25cef95e2d01012506148f03be45104e455e1fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530271"
---
# <a name="aspnet-session-state-provider-for-azure-cache-for-redis"></a>ASP.NET-sessiestatusprovider voor Azure Cache voor Redis

Azure Cache for Redis biedt een sessiestatusprovider die u gebruiken om uw sessiestatus in het geheugen op te slaan met Azure Cache voor Redis in plaats van een SQL Server-database. Als u de caching-sessiestatusprovider wilt gebruiken, configureert u eerst uw cache en configureert u vervolgens uw ASP.NET-toepassing voor cache met behulp van het NuGet-pakket Azure Cache for Redis Session State.

Het is vaak niet praktisch in een echte cloud-app om te voorkomen dat een bepaalde vorm van status voor een gebruikerssessie wordt opgeslagen, maar sommige benaderingen hebben meer invloed op prestaties en schaalbaarheid dan andere. Als u staat moet opslaan, is de beste oplossing om de hoeveelheid staat klein te houden en het in koekjes op te slaan. Als dat niet haalbaar is, is de volgende beste oplossing het gebruik van ASP.NET sessiestatus met een provider voor gedistribueerde cache in het geheugen. De slechtste oplossing vanuit het oogpunt van prestaties en schaalbaarheid is het gebruik van een database backed session state provider. In dit onderwerp vindt u richtlijnen voor het gebruik van de ASP.NET Session State Provider voor Azure Cache voor Redis. Zie [ASP.NET opties voor sessiestatus](#aspnet-session-state-options)voor meer informatie over andere opties voor sessiestatus.

## <a name="store-aspnet-session-state-in-the-cache"></a>De ASP.NET-sessiestatus in de cache opslaan

Als u een clienttoepassing in Visual Studio wilt configureren met het NuGet-pakket Azure Cache for Redis Session State, klikt u op **NuGet Package Manager**, **Package Manager Console** in het menu **Extra.**

Voer de volgende opdracht uit vanuit het venster `Package Manager Console`.
    

```powershell
Install-Package Microsoft.Web.RedisSessionStateProvider
```

> [!IMPORTANT]
> Als u de clusterfunctie van de premiumlaag gebruikt, moet u [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 of hoger gebruiken of wordt er een uitzondering gegenereerd. Naar 2.0.1 of hoger gaan is een brekende verandering; Zie [v2.0.0 Breaking Change Details voor](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)meer informatie. Op het moment van deze artikelupdate is de huidige versie van dit pakket 2.2.3.
> 
> 

Het NuGet-pakket van de Programmastatus van Redis Is is afhankelijk van het StackExchange.Redis.StrongName-pakket. Als het StackExchange.Redis.StrongName-pakket niet aanwezig is in uw project, wordt het geïnstalleerd.

>[!NOTE]
>Naast het sterk genaamde StackExchange.Redis.StrongName-pakket is er ook de StackExchange.Redis-versie met niet-sterke naam. Als uw project de stackexchange.Redis-versie met niet-sterke naam gebruikt, moet u deze verwijderen, anders krijgt u naamgevingsconflicten in uw project. Zie Clients met de [.NET-cache configureren](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)voor meer informatie over deze pakketten.
>
>

Het NuGet-pakket downloadt en voegt de vereiste assemblagereferenties toe en voegt de volgende sectie toe aan uw web.config-bestand. Deze sectie bevat de vereiste configuratie voor uw ASP.NET-toepassing om de Azure-cache voor De Session State Provider van Redis te gebruiken.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
  <providers>
    <!-- Either use 'connectionString' OR 'settingsClassName' and 'settingsMethodName' OR use 'host','port','accessKey','ssl','connectionTimeoutInMilliseconds' and 'operationTimeoutInMilliseconds'. -->
    <!-- 'throwOnError','retryTimeoutInMilliseconds','databaseId' and 'applicationName' can be used with both options. -->
    <!--
      <add name="MySessionStateStore" 
        host = "127.0.0.1" [String]
        port = "" [number]
        accessKey = "" [String]
        ssl = "false" [true|false]
        throwOnError = "true" [true|false]
        retryTimeoutInMilliseconds = "5000" [number]
        databaseId = "0" [number]
        applicationName = "" [String]
        connectionTimeoutInMilliseconds = "5000" [number]
        operationTimeoutInMilliseconds = "1000" [number]
        connectionString = "<Valid StackExchange.Redis connection string>" [String]
        settingsClassName = "<Assembly qualified class name that contains settings method specified below. Which basically return 'connectionString' value>" [String]
        settingsMethodName = "<Settings method should be defined in settingsClass. It should be public, static, does not take any parameters and should have a return type of 'String', which is basically 'connectionString' value.>" [String]
        loggingClassName = "<Assembly qualified class name that contains logging method specified below>" [String]
        loggingMethodName = "<Logging method should be defined in loggingClass. It should be public, static, does not take any parameters and should have a return type of System.IO.TextWriter.>" [String]
        redisSerializerType = "<Assembly qualified class name that implements Microsoft.Web.Redis.ISerializer>" [String]
      />
    -->
    <add name="MySessionStateStore" type="Microsoft.Web.Redis.RedisSessionStateProvider"
         host=""
         accessKey=""
         ssl="true" />
  </providers>
</sessionState>
```

De commentaarsectie geeft een voorbeeld van de kenmerken en voorbeeldinstellingen voor elk kenmerk.

Configureer de kenmerken met de waarden uit uw cacheblad in de Microsoft Azure-portal en configureer de andere waarden naar wens. Zie Azure Cache configureren voor [Redis-instellingen voor](cache-configure.md#configure-azure-cache-for-redis-settings)instructies voor instructies voor toegang tot uw cache.

* **host** – geef het eindpunt van de cache op.
* **poort** : gebruik uw niet-SSL-poort of uw SSL-poort, afhankelijk van de ssl-instellingen.
* **accessKey** – gebruik de primaire of secundaire sleutel voor uw cache.
* **ssl** - waar als u cache/clientcommunicatie met ssl wilt beveiligen; anders vals. Zorg ervoor dat u de juiste poort opgeeft.
  * De poort zonder SSL-beveiliging is standaard uitgeschakeld voor nieuwe caches. Geef op dat deze instelling de SSL-poort wilt gebruiken. Zie de sectie [Access-poorten](cache-configure.md#access-ports) in het onderwerp Een [cache](cache-configure.md) configureren voor meer informatie over het inschakelen van de niet-SSL-poort.
* **throwOnError** - waar als u wilt dat een uitzondering wordt gegooid als er een fout is, of als u wilt dat de bewerking in stilte mislukt. U controleren op een fout door de statische eigenschap Microsoft.Web.Redis.RedisSessionStateProvider.LastException te controleren. De standaardinstelling is waar.
* **retryTimeoutInMilliseconds** – Bewerkingen die mislukken worden opnieuw geprobeerd tijdens dit interval, opgegeven in milliseconden. De eerste poging vindt plaats na 20 milliseconden en vervolgens treden elke seconde opnieuw pogingen op totdat het interval retryTimeoutInMilliseconds verloopt. Onmiddellijk na dit interval wordt de bewerking nog een laatste keer opnieuw geprobeerd. Als de bewerking nog steeds mislukt, wordt de uitzondering teruggeworpen naar de beller, afhankelijk van de instelling throwOnError. De standaardwaarde is 0, wat betekent dat er geen nieuwe pogingen worden ingesteld.
* **databaseId** – Geeft op welke database moet worden gebruikt voor cacheuitvoergegevens. Als dit niet is opgegeven, wordt de standaardwaarde van 0 gebruikt.
* **applicationName** – Sleutels worden opgeslagen `{<Application Name>_<Session ID>}_Data`in redis als . Met dit naamgevingsschema kunnen meerdere toepassingen dezelfde Redis-instantie delen. Deze parameter is optioneel en als u deze niet opgeeft, wordt een standaardwaarde gebruikt.
* **connectionTimeoutInMilliseconds** – Met deze instelling u de instelling connectTimeout overschrijven in de StackExchange.Redis-client. Als dit niet is opgegeven, wordt de standaardinstelling connectTimeout van 5000 gebruikt. Zie [StackExchange.Redis-configuratiemodel voor](https://go.microsoft.com/fwlink/?LinkId=398705)meer informatie .
* **operationTimeoutInMilliseconds** – Met deze instelling u de synchronisatie-instelling overschrijven in de StackExchange.Redis-client. Als dit niet is opgegeven, wordt de standaardsynchronisatieinstelling van 1000 gebruikt. Zie [StackExchange.Redis-configuratiemodel voor](https://go.microsoft.com/fwlink/?LinkId=398705)meer informatie .
* **redisSerializerType** - Met deze instelling u aangepaste serialisatie opgeven van sessie-inhoud die naar Redis wordt verzonden. Het opgegeven type `Microsoft.Web.Redis.ISerializer` moet de openbare parameterloze constructor implementeren en aangeven. Standaard `System.Runtime.Serialization.Formatters.Binary.BinaryFormatter` wordt gebruikt.

Voor meer informatie over deze eigenschappen, zie de oorspronkelijke blog post aankondiging op [aankondiging van ASP.NET Session State Provider voor Redis](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Vergeet niet om commentaar te leveren op de standaard InProc sessie state provider sectie in uw web.config.

```xml
<!-- <sessionState mode="InProc"
     customProvider="DefaultSessionProvider">
     <providers>
        <add name="DefaultSessionProvider"
              type="System.Web.Providers.DefaultSessionStateProvider,
                    System.Web.Providers, Version=1.0.0.0, Culture=neutral,
                    PublicKeyToken=31bf3856ad364e35"
              connectionStringName="DefaultConnection" />
      </providers>
</sessionState> -->
```

Zodra deze stappen zijn uitgevoerd, is uw toepassing geconfigureerd om de Azure-cache te gebruiken voor de Timeis Session State Provider. Wanneer u sessiestatus in uw toepassing gebruikt, wordt deze opgeslagen in een Azure-cache voor bijvoorbeeld Redis.

> [!IMPORTANT]
> Gegevens die in de cache zijn opgeslagen, moeten serialiseerbaar zijn, in tegenstelling tot de gegevens die kunnen worden opgeslagen in de standaard-in-memory ASP.NET Session State Provider. Wanneer de sessiestatusprovider voor Redis wordt gebruikt, moet u ervoor zorgen dat de gegevenstypen die in sessiestatus worden opgeslagen, serialiste zijn.
> 
> 

## <a name="aspnet-session-state-options"></a>opties ASP.NET sessiestatus

* In Memory Session State Provider - Deze provider slaat de sessiestatus op in het geheugen. Het voordeel van het gebruik van deze provider is het is eenvoudig en snel. U uw web-apps echter niet schalen als u deze gebruikt in de geheugenprovider omdat deze niet is gedistribueerd.
* Sql Server Session State Provider - Deze provider slaat de sessiestatus op in Sql Server. Gebruik deze provider als u de sessiestatus wilt opslaan in permanente opslag. U uw web-app schalen, maar het gebruik van Sql Server voor sessie heeft een impact op de prestaties van uw web-app. U deze provider ook gebruiken met een [OLTP-configuratie in het geheugen](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/11/28/asp-net-session-state-with-sql-server-in-memory-oltp/) om de prestaties te verbeteren.
* Distributed In Memory Session State Provider, zoals Azure Cache for Redis Session State Provider - Deze provider biedt u het beste van beide werelden. Uw web-app kan beschikken over een eenvoudige, snelle en schaalbare sessieserviceprovider. Omdat deze provider de sessiestatus opslaat in een cache, moet uw app rekening houden met alle kenmerken die zijn gekoppeld aan het praten met een Gedistribueerde geheugencache, zoals tijdelijke netwerkfouten. Zie [Richtlijnen voor cache & Cache](../best-practices-caching.md) voor aanbevolen procedures voor het gebruik van cache. [Azure Cloud Application Design and Implementation Guidance](https://github.com/mspnp/azure-guidance)

Zie [Best Practices voor webontwikkeling (Real-World Cloud Apps bouwen met Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices)voor meer informatie over sessiestatus en andere aanbevolen procedures.

## <a name="third-party-session-state-providers"></a>Externe sessie-overheidsproviders

* [NCache](https://www.alachisoft.com/ncache/session-index.html)
* [Apache ontbranden](https://apacheignite-net.readme.io/docs/aspnet-session-state-caching)

## <a name="next-steps"></a>Volgende stappen

Bekijk de [ASP.NET Output Cache Provider voor Azure Cache voor Redis.](cache-aspnet-output-cache-provider.md)
