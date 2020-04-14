---
title: Verloop van webinhoud beheren in Azure CDN | Microsoft Documenten
description: Meer informatie over het beheren van de vervaldatum van Azure Web Apps/Cloud Services, ASP.NET of IIS-inhoud in Azure CDN.
services: cdn
documentationcenter: .NET
author: asudbring
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: 4598e6cee6ffbaaeb2a99727842fcd17fe0046c7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260561"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Verlooptijd van webinhoud in Azure CDN beheren
> [!div class="op_single_selector"]
> * [Azure-webinhoud](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob-opslag](cdn-manage-expiration-of-blob-content.md)
> 

Bestanden van openbaar toegankelijke origin-webservers kunnen in de cache worden opgeslagen in het Azure Content Delivery Network (CDN) totdat hun time-to-live (TTL) is verstreken. De TTL wordt `Cache-Control` bepaald door de header in het HTTP-antwoord van de origin-server. In dit artikel wordt `Cache-Control` beschreven hoe u kopteksten instelt voor de webapps-functie van Microsoft Azure App Service, Azure Cloud Services, ASP.NET-toepassingen en IIS-sites (Internet Information Services), die allemaal op dezelfde manier zijn geconfigureerd. U `Cache-Control` de koptekst instellen met configuratiebestanden of programmatisch. 

U ook cache-instellingen vanuit de Azure-portal beheren door [cdn-cachingregels in te](cdn-caching-rules.md)stellen. Als u een of meer cachingregels maakt en hun cachinggedrag instelt op **Cache overschrijven** of **omzeilen,** worden de in dit artikel besproken cache van de oorsprong genegeerd. Zie [Hoe caching werkt](cdn-how-caching-works.md)voor informatie over algemene cachingconcepten.

> [!TIP]
> U ervoor kiezen om geen TTL in te stellen op een bestand. In dit geval past Azure CDN automatisch een standaard TTL van zeven dagen toe, tenzij u cachingregels hebt ingesteld in de Azure-portal. Deze standaard TTL is alleen van toepassing op algemene webdelivery-optimalisaties. Voor grote bestandsoptimalisaties is de standaard TTL één dag en voor mediastreamingoptimalisaties is de standaardTTL één jaar.
> 
> Zie [Overzicht van het Azure Content Delivery Network](cdn-overview.md)voor meer informatie over hoe Azure CDN werkt om de toegang tot bestanden en andere bronnen te versnellen.
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Cache-Control-headers instellen met cdn-cachingregels
De voorkeursmethode voor het instellen `Cache-Control` van de koptekst van een webserver is het gebruik van cachingregels in de Azure-portal. Zie Azure [CDN-cachinggedrag beheren met cachingregels](cdn-caching-rules.md)voor meer informatie over cdn-cachingregels.

> [!NOTE] 
> Caching-regels zijn alleen beschikbaar voor **Azure CDN Standard van Verizon** en Azure **CDN Standard van Akamai-profielen.** Voor **Azure CDN Premium van Verizon-profielen** moet u de [Azure CDN-regelsengine](cdn-rules-engine.md) in de **portal Beheren** gebruiken voor vergelijkbare functionaliteit.

**Ga als volgt te werk om naar de pagina REGELS voor cdn-caching te navigeren:**

1. Selecteer in de Azure-portal een CDN-profiel en selecteer vervolgens het eindpunt voor de webserver.

1. Selecteer in het linkerdeelvenster onder Instellingen de optie **Regels voor opslaan in cache**.

   ![Knop REGELS voor cdn-caching](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   De pagina **Regels voor opslaan in cache** wordt weergegeven.

   ![CDN-cachepagina](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Ga als volgt te werk om de cachebeheerkoppen van een webserver in te stellen met behulp van algemene cachingregels:**

1. Stel **onder Regels voor cache globaal**query het **cachinggedrag** in **op Querytekenreeksen negeren** en Stel **Caching-gedrag** in **op Overschrijven**.
      
1. Voer 3600 in voor de vervaldatum van de **cache**in het vak **Seconden** of 1 in het vak **Uren.** 

   ![Voorbeeld van cdn-regels voor caching](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Deze algemene cachingregel stelt een cacheduur van één uur in en is van invloed op alle aanvragen voor het eindpunt. Het overschrijft `Cache-Control` `Expires` alle of HTTP-headers die worden verzonden door de oorsprongsserver die is opgegeven door het eindpunt.   

1. Selecteer **Opslaan**.

**Ga als volgt te werk om de cachebeheerkoppen van een webserverbestand in te stellen met aangepaste cachingregels:**

1. Maak **onder Aangepaste cachingregels**twee overeenkomende voorwaarden:

     a. Stel de **voorwaarde Overeenkomen** in voor de eerste wedstrijdvoorwaarde op **Pad** en voer de waarde Overeenkomen `/webfolder1/*` **in.** **Stel Caching-gedrag** in **op Overschrijven** en voer 4 in het vak **Uren** in.

     b. Stel voor de tweede wedstrijdvoorwaarde de `/webfolder1/file1.txt` voorwaarde **Overeenkomen** in op **Pad** en voer de waarde Overeenkomen **in.** **Stel Caching-gedrag** in **op Overschrijven** en voer 2 in het vak **Uren** in.

    ![Voorbeeld van aangepaste cachingregels van CDN](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    De eerste aangepaste cachingregel stelt een cacheduur van `/webfolder1` vier uur in voor bestanden in de map op de oorsprongsserver die door uw eindpunt is opgegeven. De tweede regel overschrijft alleen `file1.txt` de eerste regel voor het bestand en stelt hiervoor een cacheduur van twee uur in.

1. Selecteer **Opslaan**.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Cache-control-headers instellen met configuratiebestanden
Voor statische inhoud, zoals afbeeldingen en stijlbladen, u de updatefrequentie beheren door de **configuratiebestanden van applicationHost.config** of **Web.config** voor uw webtoepassing te wijzigen. Als u `Cache-Control` de koptekst voor `<system.webServer>/<staticContent>/<clientCache>` uw inhoud wilt instellen, gebruikt u het element in beide bestanden.

### <a name="using-applicationhostconfig-files"></a>ApplicationHost.config-bestanden gebruiken
Het **bestand ApplicationHost.config** is het hoofdbestand van het IIS-configuratiesysteem. De configuratie-instellingen in een **Bestand ApplicationHost.config** zijn van invloed op alle toepassingen op de site, maar worden overschreven door de instellingen van **web.config-bestanden** die voor een webtoepassing bestaan.

### <a name="using-webconfig-files"></a>Web.config-bestanden gebruiken
Met een **Web.config-bestand** u de manier aanpassen waarop uw volledige webtoepassing of een specifieke map op uw webtoepassing zich gedraagt. U hebt doorgaans ten minste één **Web.config-bestand** in de hoofdmap van uw webtoepassing. Voor elk **Web.config-bestand** in een specifieke map zijn de configuratie-instellingen van invloed op alles in die map en de submappen, tenzij ze op submapniveau worden overschreven door een ander **Web.config-bestand.** 

U bijvoorbeeld een `<clientCache>` element in een **Web.config-bestand** instellen in de hoofdmap van uw webtoepassing om alle statische inhoud in uw webtoepassing gedurende drie dagen in de cache te plaatsen. U ook een **Web.config-bestand** toevoegen in een submap met meer variabele inhoud (bijvoorbeeld `\frequent`) en het element instellen `<clientCache>` om de inhoud van de submap zes uur in de cache op te zetten. Het nettoresultaat is dat inhoud op de hele website drie dagen in `\frequent` de cache wordt opgeslagen, met uitzondering van alle inhoud in de map, die slechts zes uur in de cache wordt opgeslagen.  

In het volgende voorbeeld van XML-configuratiebestanden ziet u hoe u het `<clientCache>` element instelt op een maximale leeftijd van drie dagen:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Als u het kenmerk **cacheControlMaxAge** wilt gebruiken, moet u `UseMaxAge`de waarde van het kenmerk **cacheControlMode** instellen op . Met deze instelling is de `Cache-Control: max-age=<nnn>`HTTP-koptekst en -richtlijn toegevoegd aan het antwoord. De indeling van de tijdspannewaarde voor `<days>.<hours>:<min>:<sec>`het kenmerk **cacheControlMaxAge** is . De waarde ervan wordt omgezet in seconden `Cache-Control` `max-age` en wordt gebruikt als de waarde van de richtlijn. Zie `<clientCache>` [ClientCache->\<clientcache clientCache voor ](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)meer informatie over het element.  

## <a name="setting-cache-control-headers-programmatically"></a>Kopteksten cachebeheer programmatisch instellen
Voor ASP.NET toepassingen bedien u het cdn-cachinggedrag programmatisch door de eigenschap **HttpResponse.Cache** van de .NET-API in te stellen. Zie [HttpResponse.Cache Property](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) en [HttpCachePolicy Class](/dotnet/api/system.web.httpcachepolicy)voor informatie over de eigenschap **HttpResponse.Cache.**  

Voer de volgende stappen uit om toepassingsinhoud in ASP.NET programmatisch cacheop te cachen:
   1. Controleer of de inhoud is gemarkeerd `HttpCacheability` `Public`als cachebaar door in te stellen op . 
   1. Stel een cachevalidator in door `HttpCachePolicy` een van de volgende methoden aan te roepen:
      - Oproep `SetLastModified` om een tijdstempelwaarde `Last-Modified` voor de koptekst in te stellen.
      - Bel `SetETag` om een waarde `ETag` voor de koptekst in te stellen.
   1. Geef eventueel een vervaldatum van `SetExpires` de cache op `Expires` door te bellen om een waarde voor de koptekst in te stellen. Anders zijn de standaardcacheheuristiekdie eerder in dit document is beschreven, van toepassing.

Als u bijvoorbeeld een uur inhoud in de cache wilt plaatsen, voegt u de volgende C#-code toe:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>De header Cache-Control testen
U eenvoudig de TTL-instellingen van uw webinhoud verifiëren. Test met de [hulpprogramma's voor ontwikkelaars](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)van `Cache-Control` uw browser of uw webinhoud de antwoordkop bevat. U ook een tool zoals **wget,** [Postman](https://www.getpostman.com/)of [Fiddler](https://www.telerik.com/fiddler) gebruiken om de antwoordkoppen te onderzoeken.

## <a name="next-steps"></a>Volgende stappen
* [Lees meer over het **clientCache-element**](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Lees de documentatie voor de eigenschap **HttpResponse.Cache**](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [Lees de documentatie voor de **klasse HttpCachePolicy**](/dotnet/api/system.web.httpcachepolicy)  
* [Meer informatie over cachingconcepten](cdn-how-caching-works.md)
