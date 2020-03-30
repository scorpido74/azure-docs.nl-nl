---
title: ASP.NET Output Cache Provider voor Azure Cache voor Redis
description: Meer informatie over het cacheASP.NET Pagina-uitvoer met Azure Cache voor Redis. De Redis Output Cache Provider is een out-of-process opslagmechanisme voor output cache gegevens.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: af003f1f0422c2351bcdf9b0c0010e38785c0344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530322"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>ASP.NET Output Cache Provider voor Azure Cache voor Redis

De Redis Output Cache Provider is een out-of-process opslagmechanisme voor output cache gegevens. Deze gegevens zijn specifiek bedoeld voor volledige HTTP-antwoorden (pagina-uitvoercaching). De provider sluit zich aan op het nieuwe uitvoercachepunt dat in ASP.NET 4 is geïntroduceerd.

Als u de Redis Output Cache Provider wilt gebruiken, configureert u eerst uw cache en configureert u vervolgens uw ASP.NET-toepassing met het NuGet-pakket van de Redis Output Cache Provider. In dit onderwerp vindt u richtlijnen voor het configureren van uw toepassing om de Redis Output Cache Provider te gebruiken. Zie [Een cache maken](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)voor meer informatie over het maken en configureren van een Azure-cache voor bijvoorbeeld Redis.

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET pagina-uitvoer opslaan in de cache

Als u een clienttoepassing in Visual Studio wilt configureren met het NuGet-pakket Azure Cache for Redis Session State, klikt u op **NuGet Package Manager**, **Package Manager Console** in het menu **Extra.**

Voer de volgende opdracht uit vanuit het venster `Package Manager Console`.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Het NuGet-pakket van de Redis Output Cache Provider is afhankelijk van het StackExchange.Redis.StrongName-pakket. Als het StackExchange.Redis.StrongName-pakket niet aanwezig is in uw project, wordt het geïnstalleerd. Zie de pagina RedisOutputCacheProvider NuGet voor meer informatie over het NuGet-pakket van de [RedisOutputCacheProvider.](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/)

>[!NOTE]
>Naast het sterk genaamde StackExchange.Redis.StrongName-pakket is er ook de StackExchange.Redis-versie met niet-sterke naam. Als uw project de stackexchange.Redis-versie met niet-sterke naam gebruikt, moet u deze verwijderen. anders zult u problemen met het benoemen van conflicten in uw project ervaren. Zie Clients met de [.NET-cache configureren](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)voor meer informatie over deze pakketten.

Het NuGet-pakket downloadt en voegt de vereiste assemblagereferenties toe en voegt de volgende sectie toe aan uw web.config-bestand. Deze sectie bevat de vereiste configuratie voor uw ASP.NET toepassing om de Redis Output Cache Provider te gebruiken.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Configureer de kenmerken met de waarden uit uw cacheblad in de Microsoft Azure-portal en configureer de andere waarden naar wens. Zie Azure Cache configureren voor [Redis-instellingen voor](cache-configure.md#configure-azure-cache-for-redis-settings)instructies voor instructies voor toegang tot uw cache.

| Kenmerk | Type | Standaard | Beschrijving |
| --------- | ---- | ------- | ----------- |
| *Host* | tekenreeks | "localhost" | Het IP-adres of de hostnaam van de Redis-server |
| *Poort* | positief geheel getal | 6379 (niet-SSL)<br/>6380 (SSL) | Redis-serverpoort |
| *Accesskey* | tekenreeks | "" | Redis-serverwachtwoord wanneer de Autorisatie van Redis is ingeschakeld. De waarde is standaard leegtekenreeks, wat betekent dat de sessiestatusprovider geen wachtwoord gebruikt wanneer deze verbinding maakt met de Redis-server. **Als uw Redis-server zich in een openbaar toegankelijk netwerk zoals Azure Redis Cache bevindt, moet u de autorisatie van Redis inschakelen om de beveiliging te verbeteren en een veilig wachtwoord opgeven.** |
| *Ssl* | booleaans | **Valse** | Of u verbinding wilt maken met de Redis-server via SSL. Deze waarde is standaard **onjuist** omdat Redis SSL niet out of the box ondersteunt. **Als u Azure Redis-cache gebruikt die SSL out-of-the-box ondersteunt, moet u dit instellen op true om de beveiliging te verbeteren.**<br/><br/>De poort zonder SSL-beveiliging is standaard uitgeschakeld voor nieuwe caches. Geef **op dat** deze instelling de SSL-poort wilt gebruiken. Zie de sectie [Access-poorten](cache-configure.md#access-ports) in het onderwerp Een [cache](cache-configure.md) configureren voor meer informatie over het inschakelen van de niet-SSL-poort. |
| *databaseIdNumber* | positief geheel getal | 0 | *Dit kenmerk kan alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Geef op welke Redis-database u wilt gebruiken. |
| *connectionTimeoutInMilliseconds* | positief geheel getal | Geleverd door StackExchange.Redis | Wordt gebruikt om *ConnectTimeout* in te stellen bij het maken van StackExchange.Redis.ConnectionMultiplexer. |
| *operationTimeoutInMilliseconds* | positief geheel getal | Geleverd door StackExchange.Redis | Wordt gebruikt om *SyncTimeout* in te stellen bij het maken van StackExchange.Redis.ConnectionMultiplexer. |
| *connectionString* (Valid StackExchange.Redis-verbindingstekenreeks) | tekenreeks | *N/a* | Ofwel een parameterverwijzing naar AppSettings of web.config, of anders een geldige StackExchange.Redis-verbindingstekenreeks. Dit kenmerk kan waarden opgeven voor *host,* *poort,* *accessKey,* *ssl*en andere StackExchange.Redis-kenmerken. Zie Verbinding verbinden instellen [String instellen](#setting-connectionstring) in de sectie [Kenmerknotities](#attribute-notes) voor een nadere blik op *de verbinding.* |
| *instellingenClassName*<br/>*instellingenMethodName* | tekenreeks<br/>tekenreeks | *N/a* | *Deze kenmerken kunnen alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Gebruik deze kenmerken om een verbindingstekenreeks op te geven. *settingsClassName* moet een klassenaam voor een verzameling zijn die de methode bevat die is opgegeven door *instellingenMethodName*.<br/><br/>De methode die is opgegeven door *settingsMethodName* moet openbaar, statisch en ongeldig zijn (neem geen parameters), met een retourtype **tekenreeks**. Met deze methode wordt de werkelijke verbindingstekenreeks geretourneerd. |
| *loggingClassName*<br/>*loggingMethodName* | tekenreeks<br/>tekenreeks | *N/a* | *Deze kenmerken kunnen alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Gebruik deze kenmerken om uw toepassing te debuggen door logboeken uit sessiestatus/uitvoercache en logboeken van StackExchange.Redis op te geven. *loggingClassName* moet een klassenaam voor een verzameling zijn die de methode bevat die is opgegeven door *loggingMethodName*.<br/><br/>De methode die is opgegeven door *loggingMethodName* moet openbaar, statisch en ongeldig zijn (neem geen parameters), met een retourtype **System.IO.TextWriter**. |
| *applicationName* | tekenreeks | De modulenaam van het huidige proces of "/" | *Alleen SessionStateProvider*<br/>*Dit kenmerk kan alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Het app-naamvoorvoegsel dat u wilt gebruiken in de Redis-cache. De klant kan dezelfde Redis-cache voor verschillende doeleinden gebruiken. Om ervoor te zorgen dat de sessietoetsen niet botsen, kan deze vooraf worden bevestigd met de toepassingsnaam. |
| *throwOnError* | booleaans | waar | *Alleen SessionStateProvider*<br/>*Dit kenmerk kan alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Of u een uitzondering wilt maken wanneer er een fout optreedt.<br/><br/>Zie [Notities over *throwOnError* ](#notes-on-throwonerror) voor meer informatie over *throwOnError*in de sectie [Notities van kenmerk.](#attribute-notes) |>*Microsoft.Web.Redis.RedisSessionStateProvider.LastException*. |
| *retryTimeoutInMilliseconds* | positief geheel getal | 5000 | *Alleen SessionStateProvider*<br/>*Dit kenmerk kan alleen worden opgegeven via web.config of AppSettings.*<br/><br/>Hoe lang duurt het opnieuw te proberen wanneer een bewerking mislukt. Als deze waarde lager is dan *operationTimeoutInMilliseconds,* zal de provider het niet opnieuw proberen.<br/><br/>Zie [Notities over *retryTimeoutInMilliseconds* voor](#notes-on-retrytimeoutinmilliseconds) meer informatie over *tryTimeoutInMilliseconds*in de sectie [Notities voor kenmerken.](#attribute-notes) |
| *redisSerializerType* | tekenreeks | *N/a* | Hiermee geeft u de bevoegde typenaam van een klasse op die Microsoft.Web.Redis implementeert. ISerializer en dat bevat de aangepaste logica om de waarden te serialiseren en te deserialiseren. Zie [Over *redisSerializerType* ](#about-redisserializertype) in de sectie [Kenmerkennotities](#attribute-notes) voor meer informatie. |

## <a name="attribute-notes"></a>Kenmerkennotities

### <a name="setting-connectionstring"></a>*Verbindingstekenreeks* instellen

De waarde van *verbindingString* wordt gebruikt als sleutel om de werkelijke verbindingstekenreeks uit AppSettings te halen, als een dergelijke tekenreeks bestaat in AppSettings. Als deze niet wordt gevonden in AppSettings, wordt de waarde van *connectionString* gebruikt als sleutel om de werkelijke verbindingstekenreeks op te halen uit de sectie web.config **ConnectionString,** als die sectie bestaat. Als de verbindingstekenreeks niet bestaat in AppSettings of de sectie web.config **ConnectionString,** wordt de letterlijke waarde van *connectionString* gebruikt als verbindingstekenreeks bij het maken van StackExchange.Redis.ConnectionMultiplexer.

In de volgende voorbeelden wordt uitgelegd hoe *connectionString* wordt gebruikt.

#### <a name="example-1"></a>Voorbeeld 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

Gebruik `web.config`bovendetoets als parameterwaarde in plaats van werkelijke waarde.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>Voorbeeld 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

Gebruik `web.config`bovendetoets als parameterwaarde in plaats van werkelijke waarde.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>Voorbeeld 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>Opmerkingen over *throwOnError*

Als er momenteel een fout optreedt tijdens een sessiebewerking, gooit de sessiestatusprovider een uitzondering. Hiermee wordt de toepassing afgesloten.

Dit gedrag is gewijzigd op een manier die de verwachtingen van bestaande gebruikers van de ASP.NET sessiestateprovider ondersteunt, terwijl het ook de mogelijkheid biedt om te reageren op uitzonderingen, indien gewenst. Het standaardgedrag geeft nog steeds een uitzondering wanneer er een fout optreedt, in overeenstemming met andere ASP.NET sessiestatusproviders; bestaande code moet hetzelfde werken als voorheen.

Als u *throwOnError* op **false**instelt, dan zal het in plaats van een uitzondering te maken wanneer er een fout optreedt, in stilte mislukken. Als u wilt zien of er een fout is opgetreden en zo ja, wilt ontdekken wat de uitzondering was, raadpleegt u de statische eigenschap *Microsoft.Web.Redis.RedisSessionStateProvider.LastException*.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>Opmerkingen over *opnieuw proberenTimeoutInMilliseconds*

Dit biedt een aantal logica opnieuw proberen om het geval te vereenvoudigen waarbij sommige sessiebewerking opnieuw moet worden geprobeerd vanwege problemen met het netwerk, terwijl u ook de opnieuw proberen time-out beheren of u volledig afmelden voor een nieuwe poging.

Als u *retryTimeoutInMilliseconds* instelt op een getal, bijvoorbeeld 2000, wordt het opnieuw geprobeerd voor 2000 milliseconden voordat u het als een fout behandelt. Dus om de sessiestatusprovider te hebben om deze logica voor opnieuw proberen toe te passen, configureert u alleen de time-out. De eerste poging zal gebeuren na 20 milliseconden, wat in de meeste gevallen voldoende is wanneer een netwerkglitch optreedt. Daarna zal het elke seconde opnieuw proberen totdat het een tijd uitzit. Direct na de time-out, zal het opnieuw proberen nog een keer om ervoor te zorgen dat het niet zal afgesneden van de time-out door (hooguit) een seconde.

Als u denkt dat u het niet opnieuw moet proberen (bijvoorbeeld wanneer u de Redis-server op dezelfde machine als uw toepassing uitvoert) of als u de logica voor nieuwe apparaten zelf wilt afhandelen, stelt u *opnieuw TimeoutInMilliseconds* in op 0.

### <a name="about-redisserializertype"></a>Over *redisSerializerType*

Standaard wordt de serialisatie om de waarden op Redis op te slaan uitgevoerd in een binaire indeling die wordt geleverd door de klasse **BinaryFormatter.** Gebruik *redisSerializerType* om de gekwalificeerde typenaam van een klasse op te geven die **Microsoft.Web.Redis.ISerializer** implementeert en de aangepaste logica heeft om de waarden te serialiseren en te deserialiseren. Hier is bijvoorbeeld een Json serializer-klasse met JSON.NET:

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

Ervan uitgaande dat deze klasse is gedefinieerd in een assemblage met de naam **MyCompanyDll,** u de parameter *redisSerializerType* instellen om het te gebruiken:

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>Uitvoercacherichtlijn

Voeg een OutputCache-richtlijn toe aan elke pagina waarvoor u de uitvoer in de cache wilt plaatsen.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

In het vorige voorbeeld blijven de paginagegevens in de cache 60 seconden in de cache en wordt een andere versie van de pagina in de cache opgeslagen voor elke parametercombinatie. Zie [@OutputCache](https://go.microsoft.com/fwlink/?linkid=320837)voor meer informatie over de outputcache-richtlijn .

Zodra deze stappen zijn uitgevoerd, is uw toepassing geconfigureerd om de Redis Output Cache Provider te gebruiken.

## <a name="third-party-output-cache-providers"></a>Leveranciers van uitvoercaches van derden

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ontsteken](https://apacheignite-net.readme.io/docs/aspnet-output-caching)


## <a name="next-steps"></a>Volgende stappen

Bekijk de [ASP.NET Session State Provider voor Azure Cache voor Redis.](cache-aspnet-session-state-provider.md)
