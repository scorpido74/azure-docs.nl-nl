---
title: Overzicht van Azure Media Services v3 met Widevine-licentiesjabloon
description: In dit onderwerp vindt u een overzicht van een Widevine-licentiesjabloon die wordt gebruikt om Widevine-licenties te configureren.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 94ce5e45a9a43e81020096ddc0a67429b286d9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705629"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Media Services v3 met overzicht van Widevine-licentiesjabloon

Met Azure Media Services u uw inhoud versleutelen met **Google Widevine.** Media Services biedt ook een service voor het leveren van Widevine-licenties. U API's van Azure Media Services gebruiken om Widevine-licenties te configureren. Wanneer een speler probeert uw met Widevine beschermde inhoud af te spelen, wordt een verzoek naar de licentieleveringsservice verzonden om de licentie te verkrijgen. Als de licentieservice de aanvraag goedkeurt, geeft de service de licentie uit. Het wordt verzonden naar de client en wordt gebruikt om de opgegeven inhoud te decoderen en af te spelen.

Een Widevine-licentieaanvraag wordt opgemaakt als een JSON-bericht.  

>[!NOTE]
> U een leeg bericht maken{}zonder waarden, gewoon. Vervolgens wordt een licentiesjabloon gemaakt met standaardwaarden. De standaardwerkt voor de meeste gevallen. Microsoft-gebaseerde licentie-levering scenario's moeten altijd gebruik maken van de standaardinstellingen. Als u de waarden 'provider' en 'content_id' moet instellen, moet een provider overeenkomen met de breedwaarden.

    {  
       "payload":"<license challenge>",
       "content_id": "<content id>"
       "provider": "<provider>"
       "allowed_track_types":"<types>",
       "content_key_specs":[  
          {  
             "track_type":"<track type 1>"
          },
          {  
             "track_type":"<track type 2>"
          },
          …
       ],
       "policy_overrides":{  
          "can_play":<can play>,
          "can persist":<can persist>,
          "can_renew":<can renew>,
          "rental_duration_seconds":<rental duration>,
          "playback_duration_seconds":<playback duration>,
          "license_duration_seconds":<license duration>,
          "renewal_recovery_duration_seconds":<renewal recovery duration>,
          "renewal_server_url":"<renewal server url>",
          "renewal_delay_seconds":<renewal delay>,
          "renewal_retry_interval_seconds":<renewal retry interval>,
          "renew_with_usage":<renew with usage>
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
Als er een bestaand beleid bestaat, hoeft u geen van de waarden in de specificaties van de inhoudssleutel op te geven. Het reeds bestaande beleid dat aan deze inhoud is gekoppeld, wordt gebruikt om de uitvoerbeveiliging te bepalen, zoals HDCP (High-bandwidth Digital Content Protection) en het Copy General Management System (CGMS). Als een reeds bestaand beleid niet is geregistreerd bij de Widevine-licentieserver, kan de inhoudsprovider de waarden in de licentieaanvraag injecteren.   

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
| policy_overrides&#46;can_play&#46; |Booleaans, waar of onwaar |Geeft aan dat het afspelen van de inhoud is toegestaan. De standaardinstelling is onwaar. |
| policy_overrides&#46;can_persist&#46; |Booleaans, waar of onwaar |Geeft aan dat de licentie mogelijk wordt voortgezet in niet-vluchtige opslag voor offline gebruik. De standaardinstelling is onwaar. |
| policy_overrides&#46;can_renew |Booleaans, waar of onwaar |Geeft aan dat verlenging van deze licentie is toegestaan. Als dit waar is, kan de duur van de licentie worden verlengd met heartbeat. De standaardinstelling is onwaar. |
| policy_overrides&#46;&#46;license_duration_seconds |int64 |Geeft het tijdvenster voor deze specifieke licentie aan. Een waarde van 0 geeft aan dat er geen limiet is aan de duur. Standaard is 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Geeft het tijdvenster aan terwijl afspelen is toegestaan. Een waarde van 0 geeft aan dat er geen limiet is aan de duur. Standaard is 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |Het weergavevenster van de tijd na het afspelen begint binnen de licentieduur. Een waarde van 0 geeft aan dat er geen limiet is aan de duur. Standaard is 0. |
| policy_overrides&#46;&#46;renewal_server_url |tekenreeks |Alle heartbeat-aanvragen (verlenging) voor deze licentie worden naar de opgegeven URL geleid. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Hoeveel seconden na license_start_time voor verlenging wordt eerst geprobeerd. Dit veld wordt alleen gebruikt als can_renew waar is. Standaard is 0. |
| policy_overrides&#46;&#46;renewal_retry_interval_seconds |int64 |Hiermee geeft u de vertraging in seconden op tussen de volgende aanvragen voor verlenging van de licentie, in geval van een storing. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Het venster van de tijd waarin het afspelen kan worden voortgezet terwijl de verlenging wordt geprobeerd, maar niet succesvol als gevolg van back-end problemen met de licentieserver. Een waarde van 0 geeft aan dat er geen limiet is aan de duur. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides&#46;renew_with_usage |Booleaans, waar of onwaar |Geeft aan dat de licentie wordt verzonden voor verlenging wanneer het gebruik begint. Dit veld wordt alleen gebruikt als can_renew waar is. |

## <a name="session-initialization"></a>Sessieinitialisatie
| Name | Waarde | Beschrijving |
| --- | --- | --- |
| provider_session_token |Base64-gecodeerde tekenreeks |Dit sessietoken wordt teruggestuurd in de licentie en bestaat in volgende verlengingen. Het sessietoken blijft niet langer bestaan dan sessies. |
| provider_client_token |Base64-gecodeerde tekenreeks |Clienttoken om de licentiereactie terug te sturen. Als de licentieaanvraag een clienttoken bevat, wordt deze waarde genegeerd. Het clienttoken blijft langer bestaan dan licentiesessies. |
| override_provider_client_token |Booleaans, waar of onwaar |Als false en de licentieaanvraag een clienttoken bevat, gebruikt u het token uit de aanvraag, zelfs als een clienttoken in deze structuur is opgegeven. Als dit waar is, gebruikt u altijd het token dat in deze structuur is opgegeven. |

## <a name="configure-your-widevine-license-with-net"></a>Uw Widevine-licentie configureren met .NET 

Media Services biedt een klasse waarmee u een Widevine-licentie configureren. Als u de licentie wilt maken, geeft u JSON door aan [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Als u de sjabloon wilt configureren, u het gewenste aantal instellen:

### <a name="directly-construct-a-json-string"></a>Direct een JSON-tekenreeks construeren

Deze methode kan foutgevoelig zijn. Het wordt aanbevolen om andere methode te gebruiken, beschreven in [De benodigde klassen definiëren en serialiseren tot JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a>Definieer benodigde klassen en serialiseer naar JSON

#### <a name="define-classes"></a>Klassen definiëren

In het volgende voorbeeld ziet u een voorbeeld van definities van klassen die worden toegewezen aan het WIDEvine JSON-schema. U de klassen instantiëren voordat u ze serialiseert naar JSON-tekenreeks.  

```csharp
public class PolicyOverrides
{
    public bool CanPlay { get; set; }
    public bool CanPersist { get; set; }
    public bool CanRenew { get; set; }
    public int RentalDurationSeconds { get; set; }    //Indicates the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int PlaybackDurationSeconds { get; set; }  //The viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    public int LicenseDurationSeconds { get; set; }   //Indicates the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
}

public class ContentKeySpec
{
    public string TrackType { get; set; }
    public int SecurityLevel { get; set; }
    public OutputProtection RequiredOutputProtection { get; set; }
}

public class OutputProtection
{
    public string HDCP { get; set; }
}

public class WidevineTemplate
{
    public string AllowedTrackTypes { get; set; }
    public ContentKeySpec[] ContentKeySpecs { get; set; }
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>De licentie configureren

Gebruik klassen die in de vorige sectie zijn gedefinieerd om JSON te maken die wordt gebruikt om [WidevineTemplate](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)te configureren:

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="next-steps"></a>Volgende stappen

Bekijk hoe u [kunt beschermen met DRM](protect-with-drm.md)
