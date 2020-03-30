---
title: Filters definiëren in Azure Media Services
description: In dit onderwerp wordt beschreven hoe u filters maakt, zodat uw client deze kan gebruiken om specifieke secties van een stream te streamen. Media Services creëert dynamische manifesten om deze selectieve streaming te bereiken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.openlocfilehash: fdf29924da31db0347938df89e698cb258c2336b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251464"
---
# <a name="filters"></a>Filters

Wanneer uw klant uw inhoud aan klanten levert (Live Streaming events of Video on Demand), heeft uw klant mogelijk meer flexibiliteit nodig dan wat wordt beschreven in het manifestbestand van het standaardassetbestand. Azure Media Services biedt [dynamische manifesten](filters-dynamic-manifest-overview.md) op basis van vooraf gedefinieerde filters. 

Filters zijn regels aan de serverzijde waarmee uw klanten dingen kunnen doen zoals: 

- Speel slechts een gedeelte van een video af (in plaats van de hele video af te spelen). Bijvoorbeeld:
  - Het manifest verkleinen om een subclip van een live-gebeurtenis weer te geven ('subclipfiltering'), of
  - Het begin van een video bijsnijden ('een video bijsnijden').
- Lever alleen de opgegeven weergaves en/of opgegeven taaltracks die worden ondersteund door het apparaat dat wordt gebruikt om de inhoud af te spelen ('weergavefiltering'). 
- Het presentatievenster (DVR) aanpassen om een beperkte lengte van het DVR-venster in de speler te bieden ("presentatievenster aanpassen").

Met Media Services u **accountfilters** en **assetfilters** voor uw inhoud maken. Daarnaast u uw vooraf gemaakte filters koppelen aan een **Streaming Locator.**

## <a name="defining-filters"></a>Filters definiëren

Er zijn twee soorten filters: 

* [Accountfilters](https://docs.microsoft.com/rest/api/media/accountfilters) (globaal) - kunnen worden toegepast op elk item in het Azure Media Services-account en hebben een levensduur van het account.
* [Asset Filters](https://docs.microsoft.com/rest/api/media/assetfilters) (lokaal) - kan alleen worden toegepast op een actief waarmee het filter is gekoppeld aan de creatie, hebben een levensduur van het item. 

**Accountfilters** en typen **activafilters** hebben exact dezelfde eigenschappen voor het definiëren/beschrijven van het filter. Behalve bij het maken van het **assetfilter,** moet u de naam van het actief opgeven waarmee u het filter wilt koppelen.

Afhankelijk van uw scenario bepaalt u welk type filter geschikter is (Asset Filter of AccountFilter). Accountfilters zijn geschikt voor apparaatprofielen (weergavefiltering) waarbij assetfilters kunnen worden gebruikt om een specifiek element bij te snijden.

U gebruikt de volgende eigenschappen om de filters te beschrijven. 

|Name|Beschrijving|
|---|---|
|firstKwaliteit|De eerste kwaliteit bitrate van het filter.|
|presentatieTimeRange|Het tijdsbereik van de presentatie. Deze eigenschap wordt gebruikt voor het filteren van manifestbegin-/eindpunten, de lengte van het presentatievenster en de startpositie live. <br/>Zie [PresentationTimeRange](#presentationtimerange)voor meer informatie.|
|Tracks|De selectievoorwaarden van de tracks. Zie [tracks voor](#tracks) meer informatie|

### <a name="presentationtimerange"></a>presentatieTimeRange

Gebruik deze eigenschap met **assetfilters**. Het wordt afgeraden om de eigenschap in te stellen met **accountfilters.**

|Name|Beschrijving|
|---|---|
|**endTimestamp**|Van toepassing op Video on Demand (VoD).<br/>Voor de live streaming-presentatie wordt deze stilletjes genegeerd en toegepast wanneer de presentatie eindigt en de stream VoD wordt.<br/>Dit is een lange waarde die een absoluut eindpunt van de presentatie vertegenwoordigt, afgerond op de dichtstbijzijnde volgende GOP-start. Het apparaat is de tijdschaal, dus een endTimestamp van 18000000000 zou zijn voor 3 minuten.<br/>Gebruik startTimestamp en endTimestamp om de fragmenten in de afspeellijst (manifest) bij te snijden.<br/>StartTimestamp=40000000 en endTimestamp=100000000 genereren bijvoorbeeld een afspeellijst met fragmenten van tussen de 4 seconden en 10 seconden van de VoD-presentatie. Als een fragment de grens overschrijdt, wordt het hele fragment in het manifest opgenomen.|
|**forceEndTimestamp**|Geldt alleen voor live streaming.<br/>Geeft aan of de eigenschap endTimestamp aanwezig moet zijn. Als dit waar is, moet endTimestamp worden opgegeven of wordt een foute aanvraagcode geretourneerd.<br/>Toegestane waarden: onwaar, waar.|
|**liveBackoffDuur**|Geldt alleen voor live streaming.<br/> Deze waarde definieert de nieuwste live positie die een klant kan zoeken.<br/>Met deze eigenschap u de live afspeelpositie uitstellen en een serverbuffer voor spelers maken.<br/>De eenheid voor deze eigenschap is tijdschaal (zie hieronder).<br/>De maximale live back-off duur is 300 seconden (30000000000).<br/>Een waarde van 20000000000 betekent bijvoorbeeld dat de meest recente beschikbare inhoud 20 seconden vertraagd is van de echte live edge.|
|**presentatieVensterduur**|Geldt alleen voor live streaming.<br/>Gebruik presentationWindowDuration om een schuifvenster met fragmenten toe te passen om in een afspeellijst op te nemen.<br/>De eenheid voor deze eigenschap is tijdschaal (zie hieronder).<br/>Stel bijvoorbeeld presentatieWindowDuration=12000000000 in om een schuifvenster van twee minuten toe te passen. Media binnen 2 minuten na de live edge worden opgenomen in de afspeellijst. Als een fragment de grens overschrijdt, wordt het hele fragment opgenomen in de afspeellijst. De minimale duur van het presentatievenster is 60 seconden.|
|**startTimestamp**|Is van toepassing op Video on Demand (VoD) of Live Streaming.<br/>Dit is een lange waarde die een absoluut beginpunt van de stream vertegenwoordigt. De waarde wordt afgerond op de dichtstbijzijnde volgende GOP-start. Het apparaat is de tijdschaal, dus een startTimestamp van 150000000 zou voor 15 seconden.<br/>Gebruik startTimestamp en endTimestampp om de fragmenten in de afspeellijst (manifest) bij te snijden.<br/>StartTimestamp=40000000 en endTimestamp=100000000 genereren bijvoorbeeld een afspeellijst met fragmenten van tussen de 4 seconden en 10 seconden van de VoD-presentatie. Als een fragment de grens overschrijdt, wordt het hele fragment in het manifest opgenomen.|
|**Tijdschaal**|Is van toepassing op alle tijdstempels en duur in een presentatietijdbereik, opgegeven als het aantal stappen in één seconde.<br/>Standaard is 10000000 - tien miljoen stappen in een seconde, waar elke toename 100 nanoseconden lang zou zijn.<br/>Als u bijvoorbeeld een startTimestamp op 30 seconden wilt instellen, gebruikt u een waarde van 300000000 000 bij het gebruik van de standaardtijdschaal.|

### <a name="tracks"></a>Tracks

U geeft een lijst op met eigenschapsvoorwaarden voor filtertracks (FilterTrackPropertyConditions) op basis waarvan de tracks van uw stream (Live Streaming of Video on Demand) moeten worden opgenomen in dynamisch gemaakt manifest. De filters worden gecombineerd met behulp van een logische **EN en** **of OR-bewerking.**

De eigenschapsvoorwaarden van filterspoor beschrijven spoortypen, waarden (beschreven in de volgende tabel) en bewerkingen (Gelijk, NotEqual). 

|Name|Beschrijving|
|---|---|
|**Bitrate**|Gebruik de bitrate van het spoor voor het filteren.<br/><br/>De aanbevolen waarde is een bereik van bitrates, in bits per seconde. Bijvoorbeeld "0-2427000".<br/><br/>Opmerking: hoewel u een specifieke bitratewaarde gebruiken, zoals 250000 (bits per seconde), wordt deze benadering niet aanbevolen, omdat de exacte bitrates van de ene asset naar de andere kunnen fluctueren.|
|**Fourcc**|Gebruik de FourCC-waarde van het spoor voor het filteren.<br/><br/>De waarde is het eerste element van de indeling codecs, zoals gespecificeerd in [RFC 6381](https://tools.ietf.org/html/rfc6381). Momenteel worden de volgende codecs ondersteund: <br/>Voor Video: "avc1", "hev1", "hvc1"<br/>Voor audio: "mp4a", "ec-3"<br/><br/>Als u de FourCC-waarden voor tracks in een asset wilt bepalen, krijgt u het manifestbestand op en onderzoekt u deze.|
|**Language**|Gebruik de taal van de track voor het filteren.<br/><br/>De waarde is de tag van een taal die u wilt opnemen, zoals gespecificeerd in RFC 5646. Bijvoorbeeld "en".|
|**Naam**|Gebruik de naam van het spoor om te filteren.|
|**Type**|Gebruik het type spoor voor het filteren.<br/><br/>De volgende waarden zijn toegestaan: "video", "audio" of "tekst".|

### <a name="example"></a>Voorbeeld

In het volgende voorbeeld wordt een filter Live Streaming gedefinieerd: 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>Filters koppelen aan Streaming Locator

U een lijst met [activa- of accountfilters](filters-concept.md) opgeven op uw [streaminglocator.](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body) De [Dynamische Verpakker](dynamic-packaging-overview.md) past deze lijst met filters toe samen met de filters die uw client in de URL opgeeft. Deze combinatie genereert een [dynamisch manifest](filters-dynamic-manifest-overview.md), dat is gebaseerd op filters in de URL + filters die u opgeeft op de Streaming Locator. 

Zie de volgende voorbeelden:

* [Filters koppelen aan Streaming Locator - .NET](filters-dynamic-manifest-dotnet-howto.md#associate-filters-with-streaming-locator)
* [Filters koppelen aan Streaming Locator - CLI](filters-dynamic-manifest-cli-howto.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Filters bijwerken
 
**Streaming Locators** zijn niet updatable, terwijl filters kunnen worden bijgewerkt. 

Het wordt afgeraden om de definitie van filters die zijn gekoppeld aan een actief gepubliceerde **Streaming Locator**bij te werken, vooral wanneer CDN is ingeschakeld. Streamingservers en CDN's kunnen interne caches bevatten die kunnen leiden tot verouderde gegevens in de cache die moeten worden geretourneerd. 

Als de filterdefinitie moet worden gewijzigd, u overwegen een nieuw filter te maken en deze toe te voegen aan de URL **van streaming locator** of een nieuwe **streaminglocator** te publiceren die rechtstreeks naar het filter verwijst.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen ziet u hoe u filters programmatisch maken.  

- [Filters maken met REST API's](filters-dynamic-manifest-rest-howto.md)
- [Filters maken met .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Filters maken met CLI](filters-dynamic-manifest-cli-howto.md)

