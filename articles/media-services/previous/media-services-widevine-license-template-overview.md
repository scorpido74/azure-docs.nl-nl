---
title: Overzicht van widevine-licentiesjablonen | Microsoft Documenten
description: In dit onderwerp vindt u een overzicht van een Widevine-licentiesjabloon die wordt gebruikt om Widevine-licenties te configureren.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: c7511279e66ab598e4ae3c26f053915b7393b39d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978387"
---
# <a name="widevine-license-template-overview"></a>Overzicht van Widevine-licentiesjablonen 
U Azure Media Services gebruiken om Google Widevine-licenties te configureren en aan te vragen. Wanneer de speler probeert om uw Widevine-beschermde inhoud af te spelen, wordt een verzoek verzonden naar de licentie levering dienst om een licentie te verkrijgen. Als de licentieservice de aanvraag goedkeurt, geeft de service de licentie uit. Het wordt verzonden naar de client en wordt gebruikt om de opgegeven inhoud te decoderen en af te spelen.

Een Widevine-licentieaanvraag wordt opgemaakt als een JSON-bericht.  

>[!NOTE]
> U een leeg bericht maken{}zonder waarden, gewoon. Vervolgens wordt een licentiesjabloon gemaakt met standaardwaarden. De standaardwerkt voor de meeste gevallen. Microsoft-gebaseerde licentie-levering scenario's moeten altijd gebruik maken van de standaardinstellingen. Als u de waarden 'provider' en 'content_id' moet instellen, moet een provider overeenkomen met de breedwaarden.

    {  
       "payload": "<license challenge>",
       "content_id": "<content id>" 
       "provider": "<provider>"
       "allowed_track_types": "<types>",
       "content_key_specs": [  
          {  
             "track_type": "<track type 1>"
          },
          {  
             "track_type": "<track type 2>"
          },
          …
       ],
       "policy_overrides": {  
          "can_play": <can play>,
          "can persist": <can persist>,
          "can_renew": <can renew>,
          "rental_duration_seconds": <rental duration>,
          "playback_duration_seconds": <playback duration>,
          "license_duration_seconds": <license duration>,
          "renewal_recovery_duration_seconds": <renewal recovery duration>,
          "renewal_server_url": "<renewal server url>",
          "renewal_delay_seconds": <renewal delay>,
          "renewal_retry_interval_seconds": <renewal retry interval>,
          "renew_with_usage": <renew with usage>
       }
    }

## <a name="json-message"></a>JSON-bericht
| Name | Waarde | Beschrijving |
| --- | --- | --- |
| nettolading |Base64-gecodeerde tekenreeks |De licentieaanvraag die door een klant is verzonden. |
| content_id |Base64-gecodeerde tekenreeks |Id gebruikt om de sleutel-ID en inhoudssleutel voor elke content_key_specs,track_type. |
| Provider |tekenreeks |Wordt gebruikt om inhoudssleutels en -beleid op te zoeken. Als microsoft-sleutellevering wordt gebruikt voor de levering van Widevine-licenties, wordt deze parameter genegeerd. |
| policy_name |tekenreeks |Naam van een eerder geregistreerd beleid. Optioneel. |
| allowed_track_types |Enum |SD_ONLY of SD_HD. Hiermee bepaalt u welke inhoudssleutels in een licentie zijn opgenomen. |
| content_key_specs |Array van JSON structuren, zie de sectie "Content key specs."  |Een fijnmazige besturingselement waarop inhoudssleutels moeten worden teruggegeven. Zie voor meer informatie de sectie 'Specificaties van inhoudssleutel'. Er kan slechts één van de allowed_track_types- en content_key_specs-waarden worden opgegeven. |
| use_policy_overrides_exclusively |Booleaans, waar of onwaar |Gebruik beleidskenmerken die zijn opgegeven door policy_overrides en laat alle eerder opgeslagen beleidsregels weg. |
| policy_overrides |JSON-structuur, zie de sectie 'Beleidsoverschrijvingen'. |Beleidsinstellingen voor deze licentie.  In het geval dat dit actief een vooraf gedefinieerd beleid heeft, worden deze opgegeven waarden gebruikt. |
| session_init |JSON structuur, zie de sectie "Sessie initialisatie." |Optionele gegevens worden doorgegeven aan de licentie. |
| parse_only |Booleaans, waar of onwaar |De licentieaanvraag wordt ontleed, maar er wordt geen licentie afgegeven. Waarden uit de licentieaanvraag worden echter geretourneerd in het antwoord. |

## <a name="content-key-specs"></a>Specificaties van inhoudssleutel
Als er een bestaand beleid bestaat, hoeft u geen van de waarden in de specificaties van de inhoudssleutel op te geven. Het reeds bestaande beleid dat aan deze inhoud is gekoppeld, wordt gebruikt om de uitvoerbeveiliging te bepalen, zoals HDCP (High-bandwidth Digital Content Protection) en het Copy General Management System (CGMS). Als een bestaand beleid niet is geregistreerd bij de Widevine-licentieserver, kan de inhoudsprovider de waarden in de licentieaanvraag injecteren.   

Elke content_key_specs waarde moet worden opgegeven voor alle tracks, ongeacht de use_policy_overrides_exclusively optie. 

| Name | Waarde | Beschrijving |
| --- | --- | --- |
| content_key_specs. track_type |tekenreeks |Een track type naam. Als content_key_specs is opgegeven in de licentieaanvraag, moet u alle tracktypen expliciet opgeven. Als u dit niet doet, hoeft u niet langer dan 10 seconden terug te spelen. |
| content_key_specs  <br/> security_level |uint32 |Hiermee definieert u vereisten voor robuustheid van de client voor afspelen. <br/> - Software-gebaseerde white-box cryptografie is vereist. <br/> - Software cryptografie en een versluierde decoder zijn vereist. <br/> - De belangrijkste materiaal- en cryptografiebewerkingen moeten worden uitgevoerd binnen een hardwarematige vertrouwde uitvoeringsomgeving. <br/> - De cryptografie en decodering van inhoud moet worden uitgevoerd binnen een hardware-backed vertrouwde uitvoering omgeving.  <br/> - De cryptografie, decodering en alle verwerking van de media (gecomprimeerd en ongecomprimeerd) moeten worden afgehandeld binnen een hardware-backed vertrouwde uitvoeringsomgeving. |
| content_key_specs <br/> required_output_protection.hdc |touw, een van HDCP_NONE, HDCP_V1, HDCP_V2 |Geeft aan of HDCP vereist is. |
| content_key_specs <br/>sleutel |Basis64-<br/>gecodeerde tekenreeks |Inhoudssleutel om te gebruiken voor deze track. Indien dit is bepaald, is de track_type of key_id vereist. De contentprovider kan deze optie gebruiken om de inhoudssleutel voor dit nummer te injecteren in plaats van de Widevine-licentieserver een sleutel te laten genereren of opzoeken. |
| content_key_specs.key_id |Base64-gecodeerde tekenreeks binaire, 16 bytes |Unieke id voor de sleutel. |

## <a name="policy-overrides"></a>Beleidsoverschrijvingen
| Name | Waarde | Beschrijving |
| --- | --- | --- |
| policy_overrides. can_play |Booleaans, waar of onwaar |Geeft aan dat het afspelen van de inhoud is toegestaan. De standaardinstelling is onwaar. |
| policy_overrides. can_persist |Booleaans, waar of onwaar |Geeft aan dat de licentie mogelijk wordt voortgezet in niet-vluchtige opslag voor offline gebruik. De standaardinstelling is onwaar. |
| policy_overrides. can_renew |Booleaans, waar of onwaar |Geeft aan dat verlenging van deze licentie is toegestaan. Als dit waar is, kan de duur van de licentie worden verlengd met heartbeat. De standaardinstelling is onwaar. |
| policy_overrides. license_duration_seconds |int64 |Geeft het tijdvenster voor deze specifieke licentie aan. Een waarde van 0 geeft aan dat er geen limiet is aan de duur. Standaard is 0. |
| policy_overrides. rental_duration_seconds |int64 |Geeft het tijdvenster aan terwijl afspelen is toegestaan. Een waarde van 0 geeft aan dat er geen limiet is aan de duur. Standaard is 0. |
| policy_overrides. playback_duration_seconds |int64 |Het weergavevenster van de tijd na het afspelen begint binnen de licentieduur. Een waarde van 0 geeft aan dat er geen limiet is aan de duur. Standaard is 0. |
| policy_overrides. renewal_server_url |tekenreeks |Alle heartbeat-aanvragen (verlenging) voor deze licentie worden naar de opgegeven URL geleid. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides. renewal_delay_seconds |int64 |Hoeveel seconden na license_start_time voor verlenging wordt eerst geprobeerd. Dit veld wordt alleen gebruikt als can_renew waar is. Standaard is 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 |Hiermee geeft u de vertraging in seconden op tussen de volgende aanvragen voor verlenging van de licentie, in geval van een storing. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |Het venster van de tijd waarin het afspelen kan worden voortgezet terwijl de verlenging wordt geprobeerd, maar niet succesvol als gevolg van back-end problemen met de licentieserver. Een waarde van 0 geeft aan dat er geen limiet is aan de duur. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides. renew_with_usage |Booleaans, waar of onwaar |Geeft aan dat de licentie wordt verzonden voor verlenging wanneer het gebruik begint. Dit veld wordt alleen gebruikt als can_renew waar is. |

## <a name="session-initialization"></a>Sessieinitialisatie
| Name | Waarde | Beschrijving |
| --- | --- | --- |
| provider_session_token |Base64-gecodeerde tekenreeks |Dit sessietoken wordt teruggestuurd in de licentie en bestaat in volgende verlengingen. Het sessietoken blijft niet langer bestaan dan sessies. |
| provider_client_token |Base64-gecodeerde tekenreeks |Clienttoken om de licentiereactie terug te sturen. Als de licentieaanvraag een clienttoken bevat, wordt deze waarde genegeerd. Het clienttoken blijft langer bestaan dan licentiesessies. |
| override_provider_client_token |Booleaans, waar of onwaar |Als false en de licentieaanvraag een clienttoken bevat, gebruikt u het token uit de aanvraag, zelfs als een clienttoken in deze structuur is opgegeven. Als dit waar is, gebruikt u altijd het token dat in deze structuur is opgegeven. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Configureer uw Widevine-licenties met behulp van .NET-typen
Media Services biedt .NET API's die u gebruiken om uw Widevine-licenties te configureren. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Klassen zoals gedefinieerd in de Media Services .NET SDK
De volgende klassen zijn de definities van deze typen:

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Voorbeeld
In het volgende voorbeeld ziet u hoe u .NET API's gebruikt om een eenvoudige Widevine-licentie te configureren:

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Dynamische algemene versleuteling met PlayReady en/of Widevine gebruiken](media-services-protect-with-playready-widevine.md)

