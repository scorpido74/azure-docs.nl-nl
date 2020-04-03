---
title: Verpakkings- en herkomstfouten van Azure Media Services | Microsoft Documenten
description: In dit onderwerp worden fouten beschreven die u mogelijk ontvangt van de Azure Media Services Streaming Endpoint (Orgin)-service.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: 7d3a85e6fcc5b9d1c5ca1511cd7edd05ff5d9ae4
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582690"
---
# <a name="streaming-endpoint-origin-errors"></a>Fouten in streamingeindpunt (Origin) 

In dit onderwerp worden fouten beschreven die u mogelijk ontvangt van de [Streaming Endpoint-service](streaming-endpoint-concept.md)van Azure Media Services .

## <a name="400-bad-request"></a>400 Slecht verzoek

Het verzoek bevat ongeldige informatie en wordt afgewezen met deze foutcodes en om een van de volgende redenen:

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|Een syntaxis of opmaakfout van een URL. Voorbeelden hiervan zijn aanvragen voor een ongeldig type, een ongeldig fragment of een ongeldig spoor. |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|Het verzoek heeft geen versleutelingstag in de URL. Cmaf-aanvragen vereisen een versleutelingstag in de URL. Andere protocollen die zijn geconfigureerd met meer dan één versleutelingstype vereisen ook de versleutelingstag voor disambiguatie. |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|Het verzoek om op te slaan om aan de aanvraag te voldoen, is mislukt met een fout in slechte aanvraag. |

## <a name="403-forbidden"></a>403 Verboden

Het verzoek is niet toegestaan om een van de volgende redenen:

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|Het verzoek om op te slaan om aan de aanvraag te voldoen, is mislukt met een verificatiefout. Dit kan gebeuren als de opslagsleutels zijn gedraaid en de service de opslagsleutels niet kan synchroniseren. <br/><br/>Neem contact op met Azure-ondersteuning door naar [Help + ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) te gaan in de Azure-portal.|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |Er is een fout op de opslagbewerking, de toegang is mislukt vanwege onvoldoende accountmachtigingen. |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EG |Het verzoek tot opslag om aan de aanvraag te voldoen, is mislukt omdat het opslagaccount is uitgeschakeld. |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |Fout opslagbewerking, toegang is mislukt door algemene fouten. |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |De uitvoerindeling is geblokkeerd vanwege de configuratie in het streamingbeleid. |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |Versleuteling is vereist voor de inhoud, leveringsbeleid is vereist voor de uitvoerindeling. |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |Versleuteling is niet ingesteld in de instellingen voor het leveringsbeleid. |

## <a name="404-not-found"></a>404 Niet gevonden

De bewerking probeert te reageren op een resource die niet meer bestaat. De bron is bijvoorbeeld al verwijderd.

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |Het gevraagde spoor wordt niet gevonden. |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |De gevraagde bron wordt niet gevonden. |
|MPE_UNAUTHORIZED |0x80890244 |De toegang is ongeautoriseerd. |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |De gevraagde tijdstempel wordt niet gevonden. |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |Het gevraagde dynamische manifestfilter wordt niet gevonden. |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |De gevraagde fragmentindex is buiten het geldige bereik. |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |Live media-items kunnen niet worden gevonden om moov buffer te krijgen. |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |Kan het fragment niet vinden op het gevraagde tijdstip voor een bepaald nummer.<br/><br/>Het kan zijn dat het fragment niet opgeslagen is. Probeer een andere laag van de presentatie met mogelijk een fragment. |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |Kan de mediavermelding voor de gevraagde bitrate in het manifest niet vinden. <br/><br/>Het kan zijn dat de speler vroeg om een video track van een bepaalde bitrate die niet in het manifest.|
|MPE_METADATA_NOT_FOUND |0x80890257 |Kan bepaalde metagegevens in het manifest niet vinden of kunnen geen rebase uit de opslag vinden. |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |Fout opslagbewerking, resource niet gevonden. |

## <a name="409-conflict"></a>409 Conflict

De id die is `PUT` verstrekt `POST` voor een resource op een of bewerking is genomen door een bestaande resource. Gebruik een andere id voor de resource om dit probleem op te lossen.

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |Fout in opslagbewerking, conflictfout.  |

## <a name="410"></a>410

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|Voor live streaming, wanneer het filter met forceEndTimestamp is ingesteld op true, is de start- of eindtijdstempel buiten het huidige DVR-venster.|

## <a name="412-precondition-failure"></a>412 Voorwaardefout

De bewerking heeft een eTag opgegeven die verschilt van de versie die beschikbaar is op de server, dat wil zeggen een optimistische gelijktijdigheidsfout. Probeer het verzoek opnieuw na het lezen van de nieuwste versie van de bron en het bijwerken van de eTag op de aanvraag.

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |Het gevraagde fragment is nog niet klaar.|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|Opslagoperatiefout, een fout in de voorwaarde.|

## <a name="415-unsupported-media-type"></a>415 Niet-ondersteunde mediatype

De payload-indeling die door de client wordt verzonden, is in een niet-ondersteunde indeling.

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| Moet geen versleuteling toepassen op reeds versleutelde inhoud.|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |De versleuteling is ongeldig voor de invoerindeling.|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| Het type leveringsbeleid is ongeldig.|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |De oorspronkelijke instellingen kunnen worden gedeeld door meerdere uitvoerindelingen.|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|De media-indeling of -type wordt niet ondersteund. Media Services biedt bijvoorbeeld geen ondersteuning voor het aantal kwaliteitsniveaus dat meer dan 64 is. In FLV-videotag ondersteunt Media Services geen videoframe met meerdere SPS en meerdere PPS.|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| De invoerindeling van de gevraagde asset wordt niet ondersteund. Media Services ondersteunt Smooth (live), MP4 (VoD) en Progressive download formaten.|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|De gevraagde uitvoerindeling wordt niet ondersteund. Media Services ondersteunt Smooth-, DASH-,CSF-, CMAF-, HLS-(v3-, v4-, CMAF- en Progressive-downloadindelingen.|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|Aangetroffen niet-ondersteunde versleuteling type.|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|Het gevraagde mediatype wordt niet ondersteund door de uitvoerindeling. De ondersteunde typen zijn video, audio of "SUBT" ondertiteling.|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|De bronassetmedia zijn gecodeerd met een mediaformaat dat niet compatibel is met de uitvoerindeling.|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|Het bronelement is gecodeerd met een video-indeling die niet compatibel is met de uitvoerindeling. H.264, AVC, H.265 (HEVC, hev1 of hvc1) worden ondersteund.|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|Het bronelement is gecodeerd met een audioformaat dat niet compatibel is met de uitvoerindeling. Ondersteunde audioformaten zijn OC, E-AC3 (DD+), Dolby DTS.|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|Het bronbeveiligde element kan niet worden geconverteerd naar de uitvoerindeling.|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|De beveiligingsindeling wordt niet ondersteund door de uitvoerindeling.|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|De beveiligingsindeling wordt niet ondersteund door de invoerindeling.|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|Ongeldige video NAL-eenheid, bijvoorbeeld, alleen de eerste NAL in het voorbeeld kan een AUD zijn.|
|MPE_INVALID_NALU_SIZE|0x80890260|Ongeldige NAL-eenheidsgrootte.|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|Ongeldige NAL-eenheidslengtewaarde.|
|MPE_FILTER_INVALID|0x80890236|Ongeldige dynamische manifestfilters.|
|MPE_FILTER_VERSION_INVALID|0x80890237|Ongeldige of niet-ondersteunde filterversies.|
|MPE_FILTER_TYPE_INVALID|0x80890238|Ongeldig filtertype.|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|Ongeldig bereik wordt opgegeven door het filter.|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|Ongeldig spoorkenmerk wordt opgegeven door het filter.|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|De ongeldige lengte van het presentatievenster wordt opgegeven door het filter.|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|Ongeldige live back-off wordt opgegeven door het filter.|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|Slechts één absTimeInHNS-element wordt ondersteund in oudere filters.|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|Er zijn helemaal geen streams meer na het toepassen van de filters.|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|De live back off is voorbij het DVR-venster.|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|De live back-off is groter dan het presentatievenster.|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|Meer dan tien (10) maximaal toegestane standaardfilters.|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|Meerdere operator van eerste videokwaliteit is niet toegestaan in gecombineerde aanvraagfilters.|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|Het aantal bitrate-kenmerken van de eerste kwaliteit moet één (1) zijn.|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|De duur van het HLS-segment moet kleiner zijn dan een derde van het DVR-venster en HLS.|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|De fragmentduur moet minder dan of gelijk zijn aan ongeveer 20 seconden of de invoerkwaliteitsniveaus zijn niet uitgelijnd in de tijd.|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS-specifieke fout, kan niet vinden van de ReservedBox wanneer het moet aanwezig zijn in de DTSSpecficBox tijdens DTS box parsing.|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS-specifieke fout, geen kanalen gevonden in de DTSSpecficBox tijdens DTS box parsing.|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS-specifieke fout, monstertype mismatch in de DTSSpecficBox.|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS-specifieke fout, multi-asset is ingesteld, maar DTSH monster type mismatch.|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS-specifieke fout, core stream grootte is ongeldig.|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS-specifieke fout, voorbeeldresolutie is ongeldig.|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS-specifieke fout, substream extensie index is ongeldig.|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS-specifieke fout, substream bloknummer is ongeldig.|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS-specifieke fout, bemonsteringsfrequentie is ongeldig.|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS-specifieke fout, de referentieklokcode in substream-extensie is ongeldig.|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS-specifieke fout, het aantal opnieuw instellen van luidsprekers is ongeldig.|

Zie voor versleutelingsartikelen en voorbeelden:

- [Concept: contentprotection](content-protection-overview.md)
- [Concept: Beleid voor inhoudssleutel](content-key-policy-concept.md)
- [Concept: Streamingbeleid](streaming-policy-concept.md)
- [Voorbeeld: beveiligen met AES-versleuteling](protect-with-aes128.md)
- [Voorbeeld: beschermen met DRM](protect-with-drm.md)

Zie voor filterrichtlijnen:

- [Concept: dynamische manifesten](filters-dynamic-manifest-overview.md)
- [Concept: filters](filters-concept.md)
- [Voorbeeld: filters maken met REST API's](filters-dynamic-manifest-rest-howto.md)
- [Voorbeeld: filters maken met .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Voorbeeld: filters maken met CLI](filters-dynamic-manifest-cli-howto.md)

Voor levende artikelen en voorbeelden, zie:

- [Concept: live streaming overzicht](live-streaming-overview.md)
- [Concept: Live Events en Live Outputs](live-events-outputs-concept.md)
- [Voorbeeld: zelfstudie voor live streaming](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Bereik niet satisfiable

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|Fout opslagbewerking, geretourneerde http 416-fout, ongeldig bereik.|

## <a name="500-internal-server-error"></a>500 Interne serverfout

Tijdens de verwerking van de aanvraag stuit Media Services op een fout die voorkomt dat de verwerking doorgaat.  

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Ontvangen en vertaald uit Winhttp foutcode van ERROR_WINHTTP_TIMEOUT (0x00002ee2).|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Ontvangen en vertaald uit Winhttp foutcode van ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe).|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Ontvangen en vertaald uit Winhttp foutcode van ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7).|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|Fout opslagbewerking, algemene internalerror van een van de HTTP 500-fouten.|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|Fout opslagbewerking, algemene OperationTimedOut van een van de HTTP 500-fouten.|
|MPE_STORAGE_FAILURE|0x808900F2|Fout op opslagbewerking, andere HTTP 500-fouten dan InternalError of OperationTimedOut.|

## <a name="503-service-unavailable"></a>503 Service niet beschikbaar

De server kan momenteel geen aanvragen ontvangen. Deze fout kan worden veroorzaakt door overmatige verzoeken aan de service. Media Services beperking van het gebruik van resources voor toepassingen die buitensporig verzoek indienen bij de service.

> [!NOTE]
> Controleer de foutbericht en de foutcodetekenreeks om meer gedetailleerde informatie te krijgen over de reden waarom u de 503-fout hebt ontvangen. Deze fout betekent niet altijd beperking.
> 

|Foutcode|Hexadecimale waarde |Foutbeschrijving|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|Fout opslagbewerking, ontvangen HTTP-server-activiteitsfout 503.|

## <a name="ask-questions-give-feedback-get-updates"></a>Stel vragen, geef feedback, ontvang updates

Bekijk het communityartikel [van Azure Media Services](media-services-community.md) om verschillende manieren te zien waarop u vragen stellen, feedback geven en updates ontvangen over Media Services.

## <a name="see-also"></a>Zie ook

- [Foutcodes voor codering](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Azure Media Services-concepten](concepts-overview.md)
- [Quota en limieten](limits-quotas-constraints.md)

## <a name="next-steps"></a>Volgende stappen

[Voorbeeld: toegang tot ErrorCode en Bericht van ApiException met .NET](configure-connect-dotnet-howto.md#connect-to-the-net-client)
