---
title: Beheerde cacheservicetoepassingen migreren naar Redis - Azure
description: Meer informatie over het migreren van managed cache service- en in-rolecachetoepassingen naar Azure Cache voor Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 05/30/2017
ms.author: yegu
ms.openlocfilehash: 9596b8cb771f114cb09c5d6c6ae33b4fc4a8cada
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74122682"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis"></a>Migreren van Managed Cache Service naar Azure Cache voor Redis
Het migreren van uw toepassingen die Azure Managed Cache Service gebruiken naar Azure Cache voor Redis, kan worden uitgevoerd met minimale wijzigingen in uw toepassing, afhankelijk van de functies van managed cache service die door uw caching-toepassing worden gebruikt. Hoewel de API's niet precies hetzelfde zijn, lijken ze op elkaar en kan veel van uw bestaande code die managed cache service gebruikt om toegang te krijgen tot een cache, worden hergebruikt met minimale wijzigingen. In dit artikel ziet u hoe u de nodige configuratie- en toepassingswijzigingen aanbrengen om uw Managed Cache Service-toepassingen te migreren om Azure Cache voor Redis te gebruiken, en wordt weergegeven hoe sommige functies van Azure Cache voor Redis kunnen worden gebruikt om de functionaliteit van een Managed Cache Service-cache.

>[!NOTE]
>Managed Cache Service en In-Role Cache zijn op 30 november 2016 [met pensioen gegaan.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) Als u in-role cache-implementaties hebt die u wilt migreren naar Azure Cache voor Redis, u de stappen in dit artikel volgen.

## <a name="migration-steps"></a>Migratiestappen
De volgende stappen zijn vereist om een Beheerde Cache Service-toepassing te migreren om Azure Cache voor Redis te gebruiken.

* Functies beheerde cacheservice toewijzen aan Azure-cache voor Redis
* Kies een cache-aanbieding
* Een cache maken
* De cacheclients configureren
  * De serviceconfiguratie voor beheerde cache verwijderen
  * Een cacheclient configureren met het StackExchange.Redis NuGet-pakket
* Servicecode voor beheerde cache migreren
  * Verbinding maken met de cache met de klasse ConnectionMultiplexer
  * Toegang tot primitieve gegevenstypen in de cache
  * Werken met .NET-objecten in de cache
* Migreren ASP.NET sessiestatus en uitvoercache naar Azure-cache voor Redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Functies beheerde cacheservice toewijzen aan Azure-cache voor Redis
Azure Managed Cache Service en Azure Cache voor Redis zijn vergelijkbaar, maar implementeren sommige van hun functies op verschillende manieren. In deze sectie worden enkele verschillen beschreven en wordt richtlijnen gegeven voor het implementeren van de functies van Managed Cache Service in Azure Cache voor Redis.

| Functie Service voor beheerde cache | Ondersteuning voor Managed Cache Service | Azure-cache voor Redis-ondersteuning |
| --- | --- | --- |
| Benoemde caches |Een standaardcache is geconfigureerd en in de standaard- en Premium-cacheaanbiedingen kunnen desgewenst maximaal negen extra benoemde caches worden geconfigureerd. |Azure Cache voor Redis heeft een configureerbaar aantal databases (standaard van 16) die kunnen worden gebruikt om een vergelijkbare functionaliteit te implementeren als benoemde caches. Zie [Wat zijn Redis-databases?](cache-faq.md#what-are-redis-databases) en [Standaardconfiguratie voor Redis-server](cache-configure.md#default-redis-server-configuration) voor meer informatie. |
| Hoge beschikbaarheid |Biedt een hoge beschikbaarheid voor items in de cache in de standaard- en Premium-cacheaanbiedingen. Als items verloren gaan als gevolg van een storing, zijn er nog steeds back-ups van de items in de cache beschikbaar. Schrijft naar de secundaire cache worden synchroon gemaakt. |Hoge beschikbaarheid is beschikbaar in de standaard- en Premium-cache-aanbiedingen, die een primaire/replica-configuratie met twee clients hebben (elke shard in een Premium-cache heeft een primair/replicapaar). Schrijft naar de replica worden asynchroon gemaakt. Zie [Azure Cache for Redis-prijzen voor](https://azure.microsoft.com/pricing/details/cache/)meer informatie. |
| Meldingen |Hiermee kunnen clients asynchrone meldingen ontvangen wanneer verschillende cachebewerkingen plaatsvinden in een benoemde cache. |Clienttoepassingen kunnen Redis pub/sub- of [Keyspace-meldingen](cache-configure.md#keyspace-notifications-advanced-settings) gebruiken om een vergelijkbare functionaliteit te bereiken als meldingen. |
| Lokale cache |Slaat een kopie van objecten in de cache lokaal op de client op voor extra snelle toegang. |Clienttoepassingen moeten deze functionaliteit implementeren met behulp van een woordenboek of vergelijkbare gegevensstructuur. |
| Uitzettingsbeleid |Geen of LRU. Het standaardbeleid is LRU. |Azure Cache voor Redis ondersteunt het volgende uitzettingsbeleid: vluchtig-lru, allkeys-lru, vluchtig-willekeurig, allkeys-random, volatile-ttl, noeviction. Het standaardbeleid is volatiel-lru. Zie [Standaard-Redis-serverconfiguratie](cache-configure.md#default-redis-server-configuration)voor meer informatie . |
| Verloopbeleid |Het standaardverloopbeleid is Absoluut en het standaardverloopinterval is 10 minuten. Glijden en Nooit beleid zijn ook beschikbaar. |Standaard verlopen items in de cache niet, maar een vervaldatum kan per schrijfbasis worden geconfigureerd met behulp van overbelasting van de cacheset. |
| Regio's en tagging |Regio's zijn subgroepen voor items in de cache. Regio's ondersteunen ook de annotatie van items in de cache met extra beschrijvende tekenreeksen genaamd tags. Regio's ondersteunen de mogelijkheid om zoekbewerkingen uit te voeren op getagde items in die regio. Alle items binnen een regio bevinden zich binnen één knooppunt van het cachecluster. |een Azure-cache voor Redis bestaat uit één knooppunt (tenzij het Redis-cluster is ingeschakeld), zodat het concept van beheerde cacheserviceregio's niet van toepassing is. Redis ondersteunt zoek- en wildcardbewerkingen bij het ophalen van sleutels, zodat beschrijvende tags kunnen worden ingesloten in de sleutelnamen en worden gebruikt om de items later op te halen. Zie [Cache-tags implementeren met Redis](https://stackify.com/implementing-cache-tagging-redis/)voor een voorbeeld van het implementeren van een tagoplossing met Redis. |
| Serialisatie |Managed Cache ondersteunt NetDataContractSerializer, BinaryFormatter en het gebruik van aangepaste serialisators. De standaardinstelling is NetDataContractSerializer. |Het is de verantwoordelijkheid van de clienttoepassing om .NET-objecten te serialiseren voordat deze in de cache worden plaatst, met de keuze van de serializer tot de ontwikkelaar van de clienttoepassing. Zie [Werken met .NET-objecten in de cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)voor meer informatie en voorbeeldcode. |
| Cache emulator |Managed Cache biedt een lokale cache emulator. |Azure Cache voor Redis heeft geen emulator, maar u [de MSOpenTech-build van redis-server.exe lokaal uitvoeren](cache-faq.md#cache-emulator) om een emulator-ervaring te bieden. |

## <a name="choose-a-cache-offering"></a>Kies een cache-aanbieding
Microsoft Azure Cache voor Redis is beschikbaar in de volgende lagen:

* **Basic**: één knooppunt. Meerdere groottes tot 53 GB.
* **Standard**: twee knooppunten (primair/replica). Meerdere groottes tot 53 GB. 99,9% SLA.
* **Premium**: twee knooppunten (primair/replica) met maximaal 10 shards. Meerdere formaten van 6 GB tot 1,2 TB. Alle functies van de lagen Standard en Premium bieden ondersteuning voor [Redis-cluster](cache-how-to-premium-clustering.md), [Redis-persistentie](cache-how-to-premium-persistence.md) en [Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9% SLA.

Elke laag verschilt wat functies en prijzen betreft. De functies worden later in deze handleiding behandeld en zie voor meer informatie over de prijzen [cacheprijsdetails.](https://azure.microsoft.com/pricing/details/cache/)

Een startpunt voor migratie is om de grootte te kiezen die overeenkomt met de grootte van de vorige cache van de Managed Cache Service en vervolgens omhoog of omlaag te schalen, afhankelijk van de vereisten van uw toepassing. Zie [wat Azure Cache voor Redis-aanbieding en grootte moet ik gebruiken voor](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)meer informatie over het kiezen van de juiste Azure-cache voor Redis-aanbieding.

## <a name="create-a-cache"></a>Een cache maken
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>De cacheclients configureren
Zodra de cache is gemaakt en geconfigureerd, is de volgende stap het verwijderen van de configuratie van de Managed Cache Service en het toevoegen van de Configuratie en verwijzingen in Azure Cache voor Redis, zodat cacheclients toegang hebben tot de cache.

* De serviceconfiguratie voor beheerde cache verwijderen
* Een cacheclient configureren met het StackExchange.Redis NuGet-pakket

### <a name="remove-the-managed-cache-service-configuration"></a>De serviceconfiguratie voor beheerde cache verwijderen
Voordat de clienttoepassingen kunnen worden geconfigureerd voor Azure Cache voor Redis, moeten de bestaande configuratie- en assemblageverwijzingen van managed cache service worden verwijderd door het NuGet-pakket managed cache service te verwijderen.

Als u het NuGet-pakket managed cache service wilt verwijderen, klikt u met de rechtermuisknop op het clientproject in **Solution Explorer** en kiest **u NuGet-pakketten beheren.** Selecteer het knooppunt **Geïnstalleerde pakketten** en typ W**indowsAzure.Caching** in het vak Geïnstalleerde pakketten zoeken. Selecteer **Windows** **Azure Cache** (of **Windows** **Azure Caching,** afhankelijk van de versie van het NuGet-pakket), klik op **Verwijderen**en klik vervolgens op **Sluiten**.

![Azure Managed Cache Service NuGet-pakket verwijderen](./media/cache-migrate-to-redis/IC757666.jpg)

Als u het NuGet-pakket Managed Cache Service ontverwijdert, worden de verzamelingen van managed cacheservice en de service Managed Cache-vermeldingen verwijderd in de app.config of web.config van de clienttoepassing. Omdat sommige aangepaste instellingen mogelijk niet worden verwijderd bij het verwijderen van het NuGet-pakket, opent u web.config of app.config en zorgt u ervoor dat de volgende elementen worden verwijderd.

Zorg ervoor `dataCacheClients` dat de `configSections` vermelding uit het element wordt verwijderd. Verwijder niet het `configSections` hele element; gewoon verwijderen `dataCacheClients` van de vermelding, als het aanwezig is.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Zorg ervoor `dataCacheClients` dat de sectie wordt verwijderd. De `dataCacheClients` sectie zal vergelijkbaar zijn met het volgende voorbeeld.

```xml
<dataCacheClients>
  <dataCacheClientname="default">
    <!--To use the in-role flavor of Azure Cache, set identifier to be the cache cluster role name -->
    <!--To use the Azure Managed Cache Service, set identifier to be the endpoint of the cache cluster -->
    <autoDiscoverisEnabled="true"identifier="[Cache role name or Service Endpoint]"/>

    <!--<localCache isEnabled="true" sync="TimeoutBased" objectCount="100000" ttlValue="300" />-->
    <!--Use this section to specify security settings for connecting to your cache. This section is not required if your cache is hosted on a role that is a part of your cloud service. -->
    <!--<securityProperties mode="Message" sslEnabled="true">
      <messageSecurity authorizationInfo="[Authentication Key]" />
    </securityProperties>-->
  </dataCacheClient>
</dataCacheClients>
```

Zodra de configuratie van de Managed Cache Service is verwijderd, u de cacheclient configureren zoals beschreven in de volgende sectie.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Een cacheclient configureren met het StackExchange.Redis NuGet-pakket
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Servicecode voor beheerde cache migreren
De API voor de StackExchange.Azure-cache voor Redis-client is vergelijkbaar met de Managed Cache Service. In deze sectie vindt u een overzicht van de verschillen.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Verbinding maken met de cache met de klasse ConnectionMultiplexer
In Managed Cache Service werden verbindingen met `DataCacheFactory` `DataCache` de cache afgehandeld door de klassen en klassen. In Azure Cache voor Redis worden `ConnectionMultiplexer` deze verbindingen beheerd door de klasse.

Voeg de volgende instructie toe aan de bovenkant van elk bestand waarvan u toegang wilt krijgen tot de cache.

```csharp
using StackExchange.Redis
```

Als deze naamruimte niet wordt opgelost, moet u ervoor zorgen dat u het StackExchange.Redis NuGet-pakket hebt toegevoegd zoals beschreven in [Quickstart: Azure Cache voor Redis gebruiken met een .NET-toepassing](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Houd er rekening mee dat de StackExchange.Redis-client .NET Framework 4 of hoger vereist.
> 
> 

Als u verbinding wilt maken met een `ConnectionMultiplexer.Connect` Azure-cache voor bijvoorbeeld Redis, roept u de statische methode aan en geeft u het eindpunt en de sleutel door. U kunt een exemplaar van `ConnectionMultiplexer` in uw toepassing delen door een statische eigenschap in te stellen die een verbonden exemplaar retourneert, zoals in het volgende voorbeeld. Deze aanpak biedt een draadveilige manier om `ConnectionMultiplexer` één verbonden instantie te initialiseren. In dit `abortConnect` voorbeeld is ingesteld op false, wat betekent dat de aanroep zal slagen, zelfs als er geen verbinding met de cache is gemaakt. Een belangrijke functie van `ConnectionMultiplexer` is dat deze de verbinding met de cache automatisch herstelt als het netwerkprobleem of de andere oorzaken zijn opgelost.

```csharp
private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
{
    return ConnectionMultiplexer.Connect("contoso5.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");
});

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

Het cacheeindpunt, de sleutels en de poorten kunnen worden verkregen uit de **Azure Cache for Redis-blade** voor uw cache-exemplaar. Zie [Azure Cache voor eigenschappen van Redis voor](cache-configure.md#properties)meer informatie .

Zodra de verbinding tot stand is gebracht, retourneert u `ConnectionMultiplexer.GetDatabase` een verwijzing naar de Azure Cache for Redis-database door de methode aan te roepen. Het object dat door de `GetDatabase`-methode wordt geretourneerd, is een lichtgewicht doorvoerobject en hoeft niet te worden opgeslagen.

```csharp
IDatabase cache = Connection.GetDatabase();

// Perform cache operations using the cache object...
// Simple put of integral data types into the cache
cache.StringSet("key1", "value");
cache.StringSet("key2", 25);

// Simple get of data types from the cache
string key1 = cache.StringGet("key1");
int key2 = (int)cache.StringGet("key2");
```

De StackExchange.Redis-client `RedisKey` `RedisValue` gebruikt de typen en typen voor het openen en opslaan van items in de cache. Deze typen worden toegewezen aan de meeste primitieve taaltypen, waaronder tekenreeksen, en worden vaak niet direct gebruikt. Redis Strings zijn de meest elementaire vorm van Redis waarde, en kan veel soorten gegevens bevatten, met inbegrip van geserialiseerde binaire stromen, en terwijl u het type niet direct mag gebruiken, gebruikt u methoden die in de naam bevatten. `String` Voor de meeste primitieve gegevenstypen slaat u items `StringSet` `StringGet` uit de cache op en haalt u deze op met behulp van de methoden en methoden, tenzij u verzamelingen of andere Redis-gegevenstypen opslaat in de cache. 

`StringSet`en `StringGet` zijn vergelijkbaar met `Put` de `Get` Managed Cache Service en methoden, met een groot verschil is dat voordat u in- en krijgen van een .NET object in de cache moet je serialiseren het eerst. 

Wanneer `StringGet`u aanroept , als het object bestaat, wordt het geretourneerd en als dit niet het geval is, wordt null geretourneerd. In dit geval u de waarde ophalen uit de gewenste gegevensbron en deze opslaan in de cache voor later gebruik. Dit patroon staat bekend als het cache-aside patroon.

Als u de vervaldatum van een item in de cache wilt opgeven, gebruikt u de parameter `TimeSpan` van `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure Cache voor Redis kan werken met .NET-objecten en primitieve gegevenstypen, maar voordat een .NET-object in de cache kan worden opgeslagen, moet het worden geserialiseerd. Deze serialisatie is de verantwoordelijkheid van de applicatie ontwikkelaar, en geeft de ontwikkelaar flexibiliteit in de keuze van de serializer. Zie [Werken met .NET-objecten in de cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)voor meer informatie en voorbeeldcode.

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>Migreren ASP.NET sessiestatus en uitvoercache naar Azure-cache voor Redis
Azure Cache for Redis heeft providers voor zowel ASP.NET sessiestatus als paginauitvoercaching. Als u uw toepassing wilt migreren die de versies van managed cache service van deze providers gebruikt, verwijdert u eerst de bestaande secties van uw web.config en configureert u de Azure-cache voor Redis-versies van de providers. Zie [ASP.NET Sessiestatusprovider voor Azure Cache voor Red ASP.NETis voor](cache-aspnet-session-state-provider.md) instructies voor het gebruik van de Azure-cache [voor Red ASP.NETis.](cache-aspnet-output-cache-provider.md)

## <a name="next-steps"></a>Volgende stappen
Ontdek de [Azure Cache for Redis-documentatie](https://azure.microsoft.com/documentation/services/cache/) voor zelfstudies, voorbeelden, video's en meer.

