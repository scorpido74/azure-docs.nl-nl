---
title: Overzicht van live streaming met Azure Media Services | Microsoft Documenten
description: In dit artikel vindt u een overzicht van live streaming met Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 99efe375fad142963214b09df24be70bc3bc9d99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77131600"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Overzicht van Live Streaming met Media Services

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

## <a name="overview"></a>Overzicht

Bij het leveren van live streaming-evenementen met Azure Media Services gaat het vaak om de volgende onderdelen:

* Een camera die wordt gebruikt voor het uitzenden van een gebeurtenis.
* Een coderingsprogramma voor live video dat de signalen van de camera converteert naar stromen die worden verzonden naar een service voor live streamen.

    Eventueel meerdere op tijd gesynchroniseerde coderingsprogramma’s. Voor bepaalde kritieke live gebeurtenissen die een zeer hoge beschikbaarheid en kwaliteit vereisen, kunt u het beste redundante coderingsprogramma’s (actief/actief) met tijdsynchronisatie gebruiken voor een naadloze failover zonder verlies van gegevens.
* Een service voor live streamen waarmee u het volgende kunt doen:

  * Live-inhoud met verschillende protocollen voor live streamen opnemen (bijvoorbeeld RTMP of Smooth Streaming).
  * (Optioneel) Uw stream coderen in een stream met een adaptieve bitsnelheid.
  * Een voorbeeld van uw livestream bekijken.
  * De opgenomen inhoud vastleggen en opnemen om deze later te streamen (Video-on-Demand).
  * De inhoud rechtstreeks aan uw klanten leveren via algemene protocollen voor streaming (bijvoorbeeld MPEG DASH, Smooth of HLS) of aan een Content Delivery Network (CDN) voor verdere distributie.

Met **Microsoft Azure Media Services** (AMS) kunt u de inhoud van uw live-streaming opnemen, coderen, een voorbeeld bekijken, opslaan en leveren.

Met Media Services u profiteren van [dynamische verpakkingen,](media-services-dynamic-packaging-overview.md)waarmee u uw livestreams uitzenden in MPEG DASH-, HLS- en Smooth Streaming-indelingen via de bijdragefeed die naar de service wordt verzonden. Je kijkers kunnen de live stream afspelen met alle HLS-, DASH- of Smooth Streaming-compatibele spelers. U Azure Media Player in uw web- of mobiele toepassingen gebruiken om uw stream in een van deze protocollen te leveren.

> [!NOTE]
> Vanaf 12 mei 2018 ondersteunen live kanalen niet langer het RTP/MPEG-2 transportstream ingest protocol. Migreer van RTP/MPEG-2 naar RTMP of gefragmenteerde MP4 -protocollen (Smooth Streaming).

## <a name="streaming-endpoints-channels-programs"></a>Streaming Eindpunten, Kanalen, Programma's

In Azure Media Services wordt alle functionaliteiten voor live streamen afgehandeld door **kanalen**, **programma's** en **streaming-eindpunten**, waaronder opnemen, opmaken, DVR, beveiliging, schaalbaarheid en redundantie.

Een **kanaal** vertegenwoordigt een pijplijn voor de verwerking van inhoud voor live-streaming. Een kanaal kan op de volgende manieren live invoerstromen ontvangen:

* Een on-premises live codering verzendt multi-bitrate **RTMP** of **Smooth Streaming** (gefragmenteerd MP4) naar het kanaal dat is geconfigureerd voor **passthrough**-levering. **Passthrough**-levering vindt plaats wanneer de opgenomen streams het **kanaal** passeren zonder verdere verwerking. U de volgende live-encoders gebruiken die multi-bitrate Smooth Streaming uitvoeren: MediaExcel, Ateme, Imagine Communications, Envivio, Cisco en Elemental. De volgende live encoders output RTMP: Telestream Wirecast, Haivision, Teradek, en Tricaster transcoders.  Een live coderingsprogramma kan ook een stream met één bitsnelheid verzenden naar een kanaal dat niet is ingeschakeld voor Live Encoding, maar dit wordt niet aanbevolen. Desgevraagd levert Media Services de stream aan klanten.

  > [!NOTE]
  > Het gebruik van de passthrough-methode is de meest voordelige manier om live te streamen wanneer u meerdere gebeurtenissen gedurende een langere periode streamt en u al hebt geïnvesteerd in on-premises coderingsprogramma’s. Zie de details over de [prijzen](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* Een on-premises live-encoder stuurt een stream met één bitrate naar het kanaal die is ingeschakeld om live codering met Media Services uit te voeren in een van de volgende indelingen: RTMP of Smooth Streaming (gefragmenteerde MP4). Van de volgende live-encoders met RTMP-uitvoer is bekend dat ze werken met kanalen van dit type: Telestream Wirecast. Het kanaal codeert de inkomende single-bitrate stream vervolgens live naar een (adaptieve) multi-bitrate videostream. Desgevraagd levert Media Services de stream aan klanten.

Vanaf de release mediaservices 2.10 u bij het maken van een kanaal opgeven op welke manier u wilt dat uw kanaal de invoerstream ontvangt en of u wilt dat het kanaal live codering van uw stream uitvoert. U hebt hiervoor twee opties:

* **None** (pass-through) – Geef deze waarde op als u van plan bent een on-premises live-encoder te gebruiken die multibitrate stream (een pass-through stream) zal produceren. In dit geval is de binnenkomende stroom doorgegeven aan de uitgang zonder codering. Dit is het gedrag van een kanaal voorafgaand aan 2.10 release.  
* **Standaard** : kies deze waarde als u van plan bent Media Services te gebruiken om uw livestream te coderen om een stream met meerdere bitrate's te streamen. Deze methode is voordeliger om snel op te schalen voor onregelmatige gebeurtenissen. Houd er rekening mee dat er een factureringseffect is voor live codering en u moet niet vergeten dat het verlaten van een live coderingskanaal in de status 'Uitvoeren' rekening zal houden met facturering.  Het wordt aanbevolen dat u onmiddellijk stopt met uw hardloopkanalen nadat uw live streaming-evenement is voltooid om extra uurkosten te voorkomen.

## <a name="comparison-of-channel-types"></a>Vergelijking van kanaaltypen

Volgende tabel biedt een handleiding voor het vergelijken van de twee kanaaltypen die worden ondersteund in Media Services

| Functie | Pass-through Kanaal | Standaardkanaal |
| --- | --- | --- |
| Enkele bitrate-invoer wordt gecodeerd in meerdere bitrates in de cloud |Nee |Ja |
| Maximale resolutie, aantal lagen |1080p, 8 lagen, 60+fps |720p, 6 lagen, 30 fps |
| Invoerprotocollen |RTMP, vloeiend streamen |RTMP, vloeiend streamen |
| Prijs |Bekijk de [prijspagina](https://azure.microsoft.com/pricing/details/media-services/) en klik op het tabblad 'Live video' |Bekijk de [prijspagina](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximale looptijd |24x7 |8 uur |
| Ondersteuning voor het invoegen van leien |Nee |Ja |
| Ondersteuning voor het signaleren van advertenties |Nee |Ja |
| Doorgeef CEA 608/708 bijschriften |Ja |Ja |
| Ondersteuning voor niet-uniforme input GOP's |Ja |Nee – input moet worden vastgesteld 2sec GOPs |
| Ondersteuning voor invoer van variabele framesnelheid |Ja |Nee – invoer moet een vaste framesnelheid zijn.<br/>Kleine variaties worden getolereerd, bijvoorbeeld tijdens scènes met hoge beweging. Maar encoder kan niet dalen tot 10 frames / sec. |
| Automatisch uitschakelen van kanalen wanneer de invoerfeed verloren gaat |Nee |Na 12 uur, als er geen programma wordt uitgevoerd |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Werken met kanalen die een multi-bitrate livestream van on-premises encoders ontvangen (pass-through)

Het volgende diagram toont de belangrijkste onderdelen van het AMS-platform die betrokken zijn bij de **passthrough**-werkstroom.

![Live-werkstroom](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Zie [Werken met kanalen die een multi-bitrate livestream van on-premises coderingsprogramma’s ontvangen](media-services-live-streaming-with-onprem-encoders.md) voor meer informatie.

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services

Het volgende diagram toont de belangrijke onderdelen van het AMS-platform die betrokken zijn bij de Live Streaming-werkstroom waarbij een kanaal live codering kan uitvoeren met Media Services.

![Live-werkstroom](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Zie [Werken met kanalen waarmee Live Encoding kan worden uitgevoerd met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md) voor meer informatie.

## <a name="description-of-a-channel-and-its-related-components"></a>Beschrijving van een kanaal en de bijbehorende onderdelen

### <a name="channel"></a>Kanaal

In Media Services zijn [Channel](https://docs.microsoft.com/rest/api/media/operations/channel)s verantwoordelijk voor de verwerking van live streaming content. Een kanaal biedt een invoereindpunt (inname VAN URL) dat u vervolgens aan een live transcoder verstrekt. Het kanaal ontvangt live input streams van de live transcoder en maakt het beschikbaar voor streaming via een of meer StreamingEndpoints. Kanalen bieden ook een voorbeeldeindpunt (preview-URL) dat u gebruikt om uw stream te bekijken en te valideren voordat u verder wordt verwerkt en geleverd.

U de inname-URL en de url van het voorbeeld krijgen wanneer u het kanaal maakt. Om deze URL's te krijgen, hoeft het kanaal niet in de beginstatus te staan. Wanneer u klaar bent om gegevens van een live transcoder in het kanaal te duwen, moet het kanaal worden gestart. Zodra de live transcoder gegevens begint in te nemen, u een voorbeeld van uw stream bekijken.

Elk Media Services-account kan meerdere kanalen, meerdere programma's en meerdere StreamingEndpoints bevatten. Afhankelijk van de bandbreedte en beveiligingsbehoeften kunnen StreamingEndpoint-services worden toegewezen aan een of meer kanalen. Elk StreamingEndpoint kan uit elk kanaal worden verwijderd.

Bij het maken van een kanaal u toegestane IP-adressen opgeven in een van de volgende indelingen: IpV4-adres met 4 nummers, CIDR-adresbereik.

### <a name="program"></a>Programma
Met een [programma](https://docs.microsoft.com/rest/api/media/operations/program) u de publicatie en opslag van segmenten in een livestream beheren. Kanalen beheren programma's. De kanaal-/programmarelatie lijkt erg op traditionele media waarbij een kanaal een constante stream met inhoud heeft en een programma is afgestemd op een bepaalde getimede gebeurtenis op dat kanaal.
U het aantal uren opgeven dat u de opgenomen inhoud voor het programma wilt behouden door de eigenschap **ArchiveWindowLength in** te stellen. Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur.

ArchiveWindowLength bepaalt ook de maximale hoeveelheid tijd die clients terug in de tijd kunnen zoeken vanuit de huidige live-positie. Programma's kunnen in de opgegeven tijdsduur worden uitgevoerd, maar de inhoud die achter de lengte van het venster valt, wordt altijd verwijderd. De waarde van deze eigenschap bepaalt ook hoe lang de cliëntmanifesten kunnen groeien.

Elk programma is gekoppeld aan een asset. Als u het programma wilt publiceren, moet u een locator maken voor het bijbehorende actief. Met deze locator kunt u een streaming-URL maken die u aan uw clients kunt leveren.

Een kanaal ondersteunt maximaal drie gelijktijdig actieve programma's, zodat u meerdere archieven van dezelfde binnenkomende stream kunt maken. Hierdoor kunt u verschillende onderdelen van een gebeurtenis naar behoefte publiceren en archiveren. Zo kan het voor uw bedrijf nodig zijn zes uur van een programma te archiveren, maar alleen de laatste tien minuten uit te zenden. Hiervoor moet u twee gelijktijdig actieve programma's maken. Het ene programma wordt ingesteld om zes uur van de gebeurtenis te archiveren, maar het programma wordt niet gepubliceerd. Het andere programma wordt ingesteld om tien minuten te archiveren en dit programma wordt wel gepubliceerd.

## <a name="billing-implications"></a>Implicaties voor facturering
Een kanaal begint met factureren zodra de status wordt overgezet naar 'Uitvoeren' via de API.  

In de volgende tabel ziet u hoe kanaalstaten toewijzen aan factureringsstatussen in de API- en Azure-portal. Houd er rekening mee dat de statussen iets verschillen tussen de API en Portal UX. Zodra een kanaal zich in de status 'Actief' bevindt via de API of in de status 'Gereed' of 'Streaming' in de Azure-portal, is facturering actief.

Als u wilt voorkomen dat het Kanaal u verder factureert, moet u het kanaal stoppen via de API of in de Azure-portal.
U bent verantwoordelijk voor het stoppen van uw kanalen wanneer u klaar bent met het kanaal. Als u het kanaal niet stopt, wordt de facturering voortgezet.

> [!NOTE]
> Wanneer ams met standaardkanalen werkt, wordt elk kanaal dat nog steeds in de status 'Actief' staat 12 uur nadat de invoerfeed is verloren, automatisch uitgeschakeld en worden er geen programma's uitgevoerd. U wordt echter nog steeds gefactureerd voor de tijd dat het Kanaal in de status 'Lopend' stond.
>
>

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Kanaalstatussen en hoe ze worden toegewezen aan de factureringsmodus
De huidige toestand van een kanaal. Mogelijke waarden zijn:

* **Gestopt.** Dit is de oorspronkelijke status van het Kanaal na de creatie ervan (tenzij autostart is geselecteerd in de portal.) Er vindt geen facturering plaats in deze status. In deze status kunnen de kanaaleigenschappen worden bijgewerkt, maar is streamen niet toegestaan.
* **Vanaf**. Het kanaal wordt gestart. Er vindt geen facturering plaats in deze status. Tijdens deze status is geen updates of streaming toegestaan. Als er een fout optreedt, keert het kanaal terug naar de status Gestopt.
* **Hardlopen**. Het Kanaal is in staat om live streams te verwerken. Het is nu facturering gebruik. U moet het kanaal stoppen om verdere facturering te voorkomen.
* **Stoppen**. Het kanaal wordt tegengehouden. Er vindt geen facturering plaats in deze tijdelijke status. Tijdens deze status is geen updates of streaming toegestaan.
* **Verwijderen**. Het kanaal wordt verwijderd. Er vindt geen facturering plaats in deze tijdelijke status. Tijdens deze status is geen updates of streaming toegestaan.

In de volgende tabel ziet u hoe kanaaltoestanden worden toegewezen aan de factureringsmodus.

| Kanaalstaat | Portal-gebruikersinterface-indicatoren | Is het Billing? |
| --- | --- | --- |
| Starten |Starten |Geen (tijdelijke status) |
| In uitvoering |Klaar (geen lopende programma's)<br/>of<br/>Streaming (ten minste één lopend programma) |JA |
| Stoppen |Stoppen |Geen (tijdelijke status) |
| Gestopt |Gestopt |Nee |

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Verwante onderwerpen
[Azure Media Services gefragmenteerde MP4 Live Inest-specificatie](../media-services-fmp4-live-ingest-overview.md)

[Werken met kanalen die zijn ingeschakeld om live codering uit te voeren met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Werken met kanalen die multibitrate live stream ontvangen van on-premises encoders](media-services-live-streaming-with-onprem-encoders.md)

[Quota en beperkingen](media-services-quotas-and-limitations.md).  

[Concepten voor Media Services](media-services-concepts.md)
