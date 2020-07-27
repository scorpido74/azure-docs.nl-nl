---
title: Managed Cache Service-toepassingen migreren naar redis-Azure
description: Meer informatie over het migreren van Managed Cache Service en het In-Role Cache van toepassingen naar Azure cache voor redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: yegu
ms.openlocfilehash: 3f5cfccd1f85f68c619192496c62bf80ea8d4785
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87170171"
---
# <a name="migrate-from-managed-cache-service-to-azure-cache-for-redis-deprecated"></a>Migreren van Managed Cache Service naar Azure cache voor redis (afgeschaft)
Het migreren van uw toepassingen die gebruikmaken van Azure Managed Cache Service naar Azure cache voor redis, kan worden uitgevoerd met minimale wijzigingen in uw toepassing, afhankelijk van de Managed Cache Service functies die worden gebruikt door uw cache toepassing. Hoewel de Api's niet precies hetzelfde zijn, zijn ze vergelijkbaar en zijn veel van de bestaande code die Managed Cache Service gebruikt voor toegang tot een cache, opnieuw met minimale wijzigingen. In dit artikel wordt uitgelegd hoe u de benodigde configuratie-en toepassings wijzigingen kunt aanbrengen om uw Managed Cache Service-toepassingen te migreren voor het gebruik van Azure cache voor redis, en wordt getoond hoe sommige functies van Azure cache voor redis kunnen worden gebruikt voor het implementeren van de functionaliteit van een Managed Cache Service cache.

>[!NOTE]
>Managed Cache Service en In-Role Cache zijn [buiten gebruik gesteld](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) op 30 november 2016. Als u In-Role Cache implementaties hebt die u wilt migreren naar Azure cache voor redis, kunt u de stappen in dit artikel volgen.

## <a name="migration-steps"></a>Migratie stappen
De volgende stappen zijn vereist voor het migreren van een Managed Cache Service-toepassing om Azure cache te gebruiken voor redis.

* Managed Cache Service-functies voor redis toewijzen aan Azure-cache
* Een cache aanbieding kiezen
* Een cache maken
* De cache-clients configureren
  * De Managed Cache Service configuratie verwijderen
  * Een cache-client configureren met het NuGet-pakket stack Exchange. redis
* Managed Cache Service code migreren
  * Verbinding maken met de cache met behulp van de ConnectionMultiplexer-klasse
  * Toegang tot primitieve gegevens typen in de cache
  * Werken met .NET-objecten in de cache
* ASP.NET-sessie status-en uitvoer cache migreren naar Azure cache voor redis 

## <a name="map-managed-cache-service-features-to-azure-cache-for-redis"></a>Managed Cache Service-functies voor redis toewijzen aan Azure-cache
Azure Managed Cache Service en Azure cache voor redis zijn vergelijkbaar, maar implementeren een aantal functies op verschillende manieren. In deze sectie worden enkele van de verschillen beschreven en vindt u richt lijnen voor het implementeren van de functies van Managed Cache Service in azure cache voor redis.

| Managed Cache Service functie | Ondersteuning voor Managed Cache Service | Azure-cache voor redis-ondersteuning |
| --- | --- | --- |
| Benoemde caches |Er is een standaard cache geconfigureerd, en in de standaard-en Premium-cache aanbiedingen kunnen Maxi maal negen extra benoemde caches worden geconfigureerd, indien gewenst. |Azure cache voor redis heeft een configureerbaar aantal data bases (standaard van 16) dat kan worden gebruikt voor het implementeren van een vergelijk bare functionaliteit op benoemde caches. Zie [Wat zijn Redis-databases?](cache-faq.md#what-are-redis-databases) en [Standaardconfiguratie voor Redis-server](cache-configure.md#default-redis-server-configuration) voor meer informatie. |
| Hoge beschikbaarheid |Voorziet in hoge Beschik baarheid voor items in de cache in de standaard-en Premium-cache-aanbiedingen. Als items verloren zijn gegaan vanwege een fout, zijn back-upkopieën van de items in de cache nog steeds beschikbaar. Schrijf bewerkingen naar de replica cache worden synchroon gemaakt. |Hoge Beschik baarheid is beschikbaar in de Standard-en Premium-cache-aanbiedingen, die een primaire/replica-configuratie van twee knoop punten hebben (elke Shard in een Premium-cache heeft een primair/replica-paar). Schrijf bewerkingen naar de replica worden asynchroon uitgevoerd. Zie voor meer informatie [Azure cache for redis prijzen](https://azure.microsoft.com/pricing/details/cache/). |
| Meldingen |Hiermee kunnen clients asynchrone meldingen ontvangen wanneer er diverse cache bewerkingen worden uitgevoerd op een benoemde cache. |Client toepassingen kunnen redis pub/sub-of keys [Space-meldingen](cache-configure.md#keyspace-notifications-advanced-settings) gebruiken om een vergelijk bare functionaliteit te krijgen voor meldingen. |
| Lokale cache |Slaat een kopie van objecten in de cache lokaal op de client op voor extra snelle toegang. |Client toepassingen moeten deze functionaliteit implementeren met een woorden lijst of een vergelijk bare gegevens structuur. |
| Verwijderings beleid |Geen of LRU. Het standaard beleid is LRU. |Azure cache voor redis ondersteunt het volgende verwijderings beleid: vluchtig-LRU, AllKeys-LRU, vluchtig-wille keurig, AllKeys-wille keurig, vluchtig-TTL, verwijderen. Het standaard beleid is vluchtig-LRU. Zie [default redis server Configuration](cache-configure.md#default-redis-server-configuration)(Engelstalig) voor meer informatie. |
| Verloop beleid |Het standaard verloop beleid is absoluut en het standaard verloop interval is 10 minuten. Er zijn ook verschuivende en nooit-beleids regels beschikbaar. |Standaard worden items in de cache niet verlopen, maar een verval datum kan worden geconfigureerd op basis van een schrijf bewerking met behulp van het aantal Overloads van de cache. |
| Regio's en labels |Regio's zijn subgroepen voor items in de cache. Regio's bieden ook ondersteuning voor de annotatie van items in de cache met aanvullende beschrijvende teken reeksen met de naam tags. Regio's ondersteunen de mogelijkheid om Zoek bewerkingen uit te voeren op gelabelde items in die regio. Alle items in een regio bevinden zich in één knoop punt van het cache cluster. |een Azure-cache voor redis bestaat uit één knoop punt (tenzij redis-cluster is ingeschakeld), zodat het concept van Managed Cache Service regio's niet van toepassing is. Redis ondersteunt zoeken en Joker bewerkingen tijdens het ophalen van sleutels zodat beschrijvende Tags kunnen worden inge sloten in de sleutel namen en worden gebruikt om de items later op te halen. Zie [implementatie van cache tagging met redis](https://stackify.com/implementing-cache-tagging-redis/)voor een voor beeld van de implementatie van een tagging-oplossing met behulp van redis. |
| Serialisatie |Managed cache biedt ondersteuning voor NetDataContractSerializer, BinaryFormatter en het gebruik van aangepaste serialisatiefunctie. De standaard waarde is NetDataContractSerializer. |Het is de verantwoordelijkheid van de client toepassing om .NET-objecten te serialiseren voordat u ze in de cache plaatst, met de keuze van de serialisatiefunctie tot de ontwikkelaar van de client toepassing. Zie [werken met .net-objecten in de cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)voor meer informatie en voorbeeld code. |
| Cache-emulator |Managed cache biedt een lokale cache-emulator. |Azure cache voor redis heeft geen emulator, maar u kunt [de MSOpenTech-build van redis-server.exe lokaal uitvoeren](cache-faq.md#cache-emulator) om een emulator-ervaring te bieden. |

## <a name="choose-a-cache-offering"></a>Een cache aanbieding kiezen
Microsoft Azure cache voor redis is beschikbaar in de volgende lagen:

* **Basic**: één knooppunt. Meerdere groottes tot 53 GB.
* **Standard**: twee knooppunten (primair/replica). Meerdere groottes tot 53 GB. 99,9% SLA.
* **Premium**: twee knooppunten (primair/replica) met maximaal 10 shards. Meerdere grootten van 6 GB tot 1,2 TB. Alle functies van de lagen Standard en Premium bieden ondersteuning voor [Redis-cluster](cache-how-to-premium-clustering.md), [Redis-persistentie](cache-how-to-premium-persistence.md) en [Azure Virtual Network](cache-how-to-premium-vnet.md). 99,9% SLA.

Elke laag verschilt wat functies en prijzen betreft. De functies worden verderop in deze hand leiding behandeld en Zie [prijs informatie voor cache](https://azure.microsoft.com/pricing/details/cache/)voor meer informatie over prijzen.

Een start punt voor migratie is het kiezen van de grootte die overeenkomt met de grootte van uw vorige Managed Cache Service cache en vervolgens omhoog of omlaag schalen, afhankelijk van de vereisten van uw toepassing. Zie voor meer informatie over het kiezen van de juiste Azure-cache voor redis-aanbieding [wat Azure-cache voor redis-aanbieding en grootte moet ik gebruiken](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use).

## <a name="create-a-cache"></a>Een cache maken
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="configure-the-cache-clients"></a>De cache-clients configureren
Zodra de cache is gemaakt en geconfigureerd, is de volgende stap het verwijderen van de configuratie van de Managed Cache Service en het toevoegen van de Azure-cache voor redis-configuratie en-verwijzingen zodat de cache-clients toegang kunnen krijgen tot de cache.

* De Managed Cache Service configuratie verwijderen
* Een cache-client configureren met het NuGet-pakket stack Exchange. redis

### <a name="remove-the-managed-cache-service-configuration"></a>De Managed Cache Service configuratie verwijderen
Voordat de client toepassingen voor Azure-cache kunnen worden geconfigureerd voor redis, moeten de bestaande Managed Cache Service configuratie en assembly-verwijzingen worden verwijderd door het Managed Cache Service NuGet-pakket te verwijderen.

Als u het NuGet-pakket van Managed Cache Service wilt verwijderen, klikt u met de rechter muisknop op het client project in **Solution Explorer** en kiest u **NuGet-pakketten beheren**. Selecteer het knoop punt **geïnstalleerde pakketten** en typ W**indowsAzure. caching** in het vak geïnstalleerde pakketten zoeken. Selecteer **Windows** **Azure-cache** (of **Windows** **Azure caching** , afhankelijk van de versie van het NuGet-pakket), klik op **verwijderen**en klik vervolgens op **sluiten**.

![Azure Managed Cache Service NuGet-pakket verwijderen](./media/cache-migrate-to-redis/IC757666.jpg)

Als u Managed Cache Service het NuGet-pakket verwijdert, worden de Managed Cache Service-assembly's en de Managed Cache Service vermeldingen in de app.config of web.config van de client toepassing verwijderd. Omdat sommige aangepaste instellingen mogelijk niet worden verwijderd wanneer u het NuGet-pakket verwijdert, opent u web.config of app.config en zorgt u ervoor dat de volgende elementen worden verwijderd.

Zorg ervoor dat de `dataCacheClients` vermelding is verwijderd uit het- `configSections` element. Verwijder het hele element niet `configSections` ; Verwijder de vermelding alleen `dataCacheClients` als deze aanwezig is.

```xml
<configSections>
  <!-- Existing sections omitted for clarity. -->
  <section name="dataCacheClients"type="Microsoft.ApplicationServer.Caching.DataCacheClientsSection, Microsoft.ApplicationServer.Caching.Core" allowLocation="true" allowDefinition="Everywhere"/>
</configSections>
```

Zorg ervoor dat de `dataCacheClients` sectie wordt verwijderd. De `dataCacheClients` sectie ziet er ongeveer uit als in het volgende voor beeld.

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

Zodra de Managed Cache Service configuratie is verwijderd, kunt u de cache-client configureren, zoals wordt beschreven in de volgende sectie.

### <a name="configure-a-cache-client-using-the-stackexchangeredis-nuget-package"></a>Een cache-client configureren met het NuGet-pakket stack Exchange. redis
[!INCLUDE [redis-cache-configure](../../includes/redis-cache-configure-stackexchange-redis-nuget.md)]

## <a name="migrate-managed-cache-service-code"></a>Managed Cache Service code migreren
De API voor de cache voor stack Exchange. Azure voor redis-client is vergelijkbaar met de Managed Cache Service. In deze sectie vindt u een overzicht van de verschillen.

### <a name="connect-to-the-cache-using-the-connectionmultiplexer-class"></a>Verbinding maken met de cache met behulp van de ConnectionMultiplexer-klasse
In Managed Cache Service zijn verbindingen met de cache verwerkt door de `DataCacheFactory` klassen and `DataCache` . In azure cache voor redis worden deze verbindingen beheerd door de- `ConnectionMultiplexer` klasse.

Voeg de volgende instructie toe aan het begin van elk bestand dat u wilt gebruiken voor toegang tot de cache.

```csharp
using StackExchange.Redis
```

Als deze naam ruimte niet wordt opgelost, moet u ervoor zorgen dat u het NuGet-pakket stack Exchange. redis hebt toegevoegd, zoals beschreven in [Quick Start: Azure cache gebruiken voor redis met een .NET-toepassing](cache-dotnet-how-to-use-azure-redis-cache.md).

> [!NOTE]
> Houd er rekening mee dat de stack Exchange. redis-client .NET Framework 4 of hoger vereist.
> 
> 

Als u verbinding wilt maken met een Azure-cache voor redis-exemplaar, roept u de statische- `ConnectionMultiplexer.Connect` methode aan en geeft u het eind punt en de sleutel door. U kunt een exemplaar van `ConnectionMultiplexer` in uw toepassing delen door een statische eigenschap in te stellen die een verbonden exemplaar retourneert, zoals in het volgende voorbeeld. Deze benadering biedt een thread-veilige manier om één verbonden exemplaar te initialiseren `ConnectionMultiplexer` . In dit voor beeld `abortConnect` is ingesteld op False, wat betekent dat de aanroep slaagt, zelfs als er geen verbinding met de cache tot stand is gebracht. Een belangrijke functie van `ConnectionMultiplexer` is dat deze de verbinding met de cache automatisch herstelt als het netwerkprobleem of de andere oorzaken zijn opgelost.

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

Het cache-eind punt, sleutels en poorten kunnen worden opgehaald uit de **Azure-cache voor** de Blade redis voor uw cache-exemplaar. Zie [Azure cache voor redis-eigenschappen](cache-configure.md#properties)voor meer informatie.

Als de verbinding tot stand is gebracht, retourneert u een verwijzing naar de Azure-cache voor de redis-data base door de methode aan te roepen `ConnectionMultiplexer.GetDatabase` . Het object dat door de `GetDatabase`-methode wordt geretourneerd, is een lichtgewicht doorvoerobject en hoeft niet te worden opgeslagen.

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

De stack Exchange. redis-client gebruikt de- `RedisKey` en- `RedisValue` typen voor het openen en opslaan van items in de cache. Deze typen worden toegewezen aan de meeste primitieve taal typen, met inbegrip van teken reeksen, en worden vaak niet rechtstreeks gebruikt. Redis-teken reeksen zijn de meest eenvoudige soort redis-waarde en kunnen veel soorten gegevens bevatten, met inbegrip van geserialiseerde binaire streams, en terwijl u het type niet rechtstreeks gebruikt, gebruikt u de methoden die `String` in de naam staan. Voor de meeste primitieve gegevens typen kunt u items opslaan en ophalen uit de cache met behulp van de `StringSet` `StringGet` -en-methoden, tenzij u verzamelingen opslaat of andere redis-gegevens typen in de cache. 

`StringSet`en `StringGet` zijn vergelijkbaar met de Managed cache service `Put` en `Get` methoden, met één belang rijk verschil dat voordat u een .net-object in de cache instelt en ontvangt, het eerst moet worden geserialiseerd. 

Als `StringGet` het object bestaat, wordt dit geretourneerd, en als dit niet het geval is, wordt Null geretourneerd. In dit geval kunt u de waarde van de gewenste gegevens bron ophalen en deze in de cache opslaan voor later gebruik. Dit patroon wordt het cache-leggings patroon genoemd.

Als u de vervaldatum van een item in de cache wilt opgeven, gebruikt u de parameter `TimeSpan` van `StringSet`.

```csharp
cache.StringSet("key1", "value1", TimeSpan.FromMinutes(90));
```

Azure cache voor redis kan werken met .NET-objecten en primitieve gegevens typen, maar voordat een .NET-object in de cache kan worden opgeslagen, moet het worden geserialiseerd. Deze serialisatie is de verantwoordelijkheid van de toepassings ontwikkelaar en biedt de ontwikkel aars flexibiliteit in de keuze van de serialisatiefunctie. Zie [werken met .net-objecten in de cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)voor meer informatie en voorbeeld code.

## <a name="migrate-aspnet-session-state-and-output-caching-to-azure-cache-for-redis"></a>ASP.NET-sessie status-en uitvoer cache migreren naar Azure cache voor redis
Azure cache voor redis heeft providers voor zowel de ASP.NET-sessie status als de cache voor pagina-uitvoer. Als u uw toepassing wilt migreren die gebruikmaakt van de Managed Cache Service versies van deze providers, verwijdert u eerst de bestaande secties uit de web.config en configureert u vervolgens de Azure-cache voor redis-versies van de providers. Zie voor instructies over het gebruik van de Azure-cache voor redis ASP.NET-providers [ASP.net-sessie status provider voor Azure cache voor redis](cache-aspnet-session-state-provider.md) en [ASP.net-uitvoer cache provider voor Azure cache voor redis](cache-aspnet-output-cache-provider.md).

## <a name="next-steps"></a>Volgende stappen
Verken de [Azure-cache voor redis-documentatie](https://azure.microsoft.com/documentation/services/cache/) voor zelf studies, voor beelden, Video's en meer.

