---
title: Axinom gebruiken om Widevine-licenties te leveren aan Azure Media Services | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Azure Media Services (AMS) gebruiken om een stream te leveren die dynamisch is versleuteld door AMS met zowel PlayReady- als Widevine-DRM's. De PlayReady-licentie is afkomstig van Media Services PlayReady-licentieserver en widevine-licentie wordt geleverd door axinom-licentieserver.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.openlocfilehash: 2ec3276b9b02c29b80d46e5fd31298c909857182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197161"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Axinom gebruiken om Widevine-licenties te leveren aan Azure Media Services 
> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Overzicht
Azure Media Services (AMS) heeft dynamische bescherming van Google Widevine toegevoegd (zie [de blog van Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) voor meer informatie). Daarnaast heeft Azure Media Player (AMP) ook Widevine-ondersteuning toegevoegd (zie [AMP-document](https://amp.azure.net/libs/amp/latest/docs/) voor meer informatie). Dit is een belangrijke prestatie in het streamen van DASH-inhoud die wordt beschermd door CENC met multi-native-DRM (PlayReady en Widevine) op moderne browsers die zijn uitgerust met MSE en EME.

Vanaf de Media Services .NET SDK versie 3.5.2 u met Media Services Widevine-licentiesjabloon configureren en Widevine-licenties krijgen. U kunt ook de volgende AMS-partners gebruiken om Widevine-licenties te leveren: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/) en [castLabs](https://castlabs.com/company/partners/azure/).

In dit artikel wordt beschreven hoe u Widevine-licentieserver integreren en testen die door Axinom wordt beheerd. Het gaat met name om:  

* Dynamische algemene versleuteling configureren met multi-DRM (PlayReady en Widevine) met bijbehorende URL's voor licentieverwerving;
* Het genereren van een JWT-token om te voldoen aan de vereisten van de licentieserver;
* Ontwikkelen van de Azure Media Player-app, die licentieverwerving met JWT-tokenverificatie verwerkt;

Het volledige systeem en de stroom van inhoudssleutel, sleutel-ID, sleutelzaad, JTW-token en de bijbehorende claims kunnen het best worden beschreven in het volgende diagram:

![DASH en CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Content Protection
Zie de blog van Mingfei voor het configureren van dynamische beveiliging en het belangrijkste leveringsbeleid: [Hoe u Widevine-verpakkingen configureert met Azure Media Services.](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)

U dynamische CENC-beveiliging configureren met multi-DRM voor DASH-streaming met beide van de volgende opties:

1. PlayReady-beveiliging voor Microsoft Edge en IE11, die een beperking van de tokenautorisatie kunnen hebben. Het beleid met tokenbeperkte kenmerken moet vergezeld gaan van een token dat is uitgegeven door een Secure Token Service (STS), zoals Azure Active Directory;
2. Widevine-beveiliging voor Chrome, het kan tokenverificatie vereisen met token die is uitgegeven door een andere STS. 

Zie de sectie [JWT-tokengeneratie](media-services-axinom-integration.md#jwt-token-generation) waarom Azure Active Directory niet kan worden gebruikt als EEN STS voor de Widevine-licentieserver van Axinom.

### <a name="considerations"></a>Overwegingen
1. U moet het axinom-opgegeven sleutelzaad (88880000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000 Axinom-licentieserver geeft alle licenties uit die inhoudssleutels bevatten op basis van hetzelfde sleutelzaad, dat geldig is voor zowel testen als productie.
2. De URL voor het aanschaffen [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense)van de Widevine-licentie voor het testen: . Zowel HTTP als HTTS zijn toegestaan.

## <a name="azure-media-player-preparation"></a>Voorbereiding van Azure Media Player
AMP v1.4.0 ondersteunt het afspelen van AMS-inhoud die dynamisch is verpakt met zowel PlayReady als Widevine DRM.
Als widevine-licentieserver geen tokenverificatie vereist, hoeft u niets extra's te doen om een DASH-inhoud te testen die door Widevine wordt beschermd. Het AMP-team biedt bijvoorbeeld een eenvoudig [voorbeeld,](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html)waarbij u het zien werken in Microsoft Edge en IE11 met PlayReady en Chrome met Widevine.
De Widevine-licentieserver van Axinom vereist JWT-tokenverificatie. Het JWT-token moet worden ingediend met licentieaanvraag via een HTTP-header "X-AxDRM-Message". Hiervoor moet u de volgende javascript toevoegen aan de webpagina met AMP voordat u de bron instelt:

    <script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>

De rest van AMP-code is standaard AMP API zoals in AMP-document [hier.](https://amp.azure.net/libs/amp/latest/docs/)

De bovenstaande javascript voor het instellen van aangepaste autorisatie header is nog steeds een korte termijn aanpak voordat de officiële lange termijn aanpak in AMP wordt vrijgegeven.

## <a name="jwt-token-generation"></a>JWT-tokengeneratie
Axinom Widevine-licentieserver voor het testen vereist JWT-tokenverificatie. Bovendien is een van de claims in het JWT-token van een complex objecttype in plaats van primitief gegevenstype.

Helaas kan Azure AD alleen JWT-tokens uitgeven met primitieve typen. Op dezelfde manier u met .NET Framework API (System.IdentityModel.Tokens.SecurityTokenHandler en JwtPayload) alleen complexe objecttypen invoeren als claims. Echter, de vorderingen zijn nog steeds geserialiseerd als string. Daarom kunnen we geen van de twee gebruiken voor het genereren van het JWT-token voor Widevine-licentieaanvraag.

John Sheehan's [JWT NuGet-pakket](https://www.nuget.org/packages/JWT) voldoet aan de behoeften, dus we gaan dit NuGet-pakket gebruiken.

Hieronder vindt u de code voor het genereren van JWT-token met de benodigde claims zoals vereist door Axinom Widevine-licentieserver voor het testen:

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.IdentityModel.Tokens;
    using System.IdentityModel.Protocols.WSTrust;
    using System.Security.Claims;

    namespace OpenIdConnectWeb.Utils
    {
        public class JwtUtils
        {
            //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
            public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
            {
                byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

                var payload = new Dictionary<string, object>()
                             {
                                 { "version", 1 },
                                 { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                                 { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
                             };

                string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

                return token;
            }

            //convert hex string to byte[]
            public static byte[] ConvertHexStringToByteArray(string hexString)
            {
                if (hexString.Length % 2 != 0)
                {
                    throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
                }

                byte[] HexAsBytes = new byte[hexString.Length / 2];
                for (int index = 0; index < HexAsBytes.Length; index++)
                {
                    string byteValue = hexString.Substring(index * 2, 2);
                    HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
                }

                return HexAsBytes;
            }

        }  

    }  

Axinom Widevine-licentieserver

    <add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
    <add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
    <add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
    <add key="ax:keyseed" value="8888000000000000000000000000000000000000" />

### <a name="considerations"></a>Overwegingen
1. Hoewel ams PlayReady-licentieleveringsservice "Bearer=" vereist voorafgaand aan een verificatietoken, gebruikt axinom Widevine-licentieserver het niet.
2. De Axinom-communicatiesleutel wordt gebruikt als ondertekeningssleutel. De sleutel is een hex string, maar het moet worden behandeld als een reeks bytes niet een string bij het coderen. Dit wordt bereikt door de methode ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Sleutel-id ophalen
Het is u misschien opgevallen dat in de code voor het genereren van een JWT-token sleutel-ID vereist is. Aangezien het JWT-token klaar moet zijn voor het laden van AMP-speler, moet de sleutel-ID worden opgehaald om JWT-token te genereren.

Natuurlijk zijn er meerdere manieren om de sleutel-ID in handen te krijgen. U bijvoorbeeld sleutel-ID opslaan samen met metagegevens van inhoud in een database. U de sleutel-id ophalen uit het DASH MPD-bestand (MediaPresentatiebeschrijving). De onderstaande code is voor de laatste.

    //get key_id from DASH MPD
    public static string GetKeyID(string dashUrl)
    {
        if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
        {
            dashUrl += "(format=mpd-time-csf)";
        }

        XPathDocument objXPathDocument = new XPathDocument(dashUrl);
        XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
        XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
        objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
        objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
        objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
        objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
        objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
        objXmlNamespaceManager.PushScope();

        XPathNodeIterator objXPathNodeIterator;
        objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

        string key_id = string.Empty;
        if (objXPathNodeIterator.MoveNext())
        {
            key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
        }

        return key_id;
    }

## <a name="summary"></a>Samenvatting

Met de nieuwste toevoeging van Widevine-ondersteuning in zowel Azure Media Services Content Protection als Azure Media Player, kunnen we streaming van DASH + Multi-native-DRM (PlayReady + Widevine) implementeren met zowel PlayReady-licentieservice in AMS- als Widevine-licentie server van Axinom voor de volgende moderne browsers:

* Chrome
* Microsoft Edge op Windows 10
* IE 11 op Windows 8.1 en Windows 10
* Zowel Firefox (Desktop) als Safari op Mac (niet iOS) worden ook ondersteund via Silverlight en dezelfde URL met Azure Media Player

De volgende parameters zijn vereist in de mini-oplossing gebruik te maken van Axinom Widevine licentie server. Behalve voor key ID, worden de rest van de parameters geleverd door Axinom op basis van hun Widevine server setup.

| Parameter | Hoe het wordt gebruikt |
| --- | --- |
| Communicatiesleutel-ID |Moet worden opgenomen als waarde van de claim "com_key_id" in JWT-token (zie [deze](media-services-axinom-integration.md#jwt-token-generation) sectie). |
| Communicatiesleutel |Moet worden gebruikt als de ondertekeningssleutel van JWT-token (zie [deze](media-services-axinom-integration.md#jwt-token-generation) sectie). |
| Sleutelzaad |Moet worden gebruikt om inhoudssleutel te genereren met een bepaalde inhoudssleutel-ID (zie [deze](media-services-axinom-integration.md#content-protection) sectie). |
| URL voor het verwerven van Widevine-licenties |Moet worden gebruikt bij het configureren van het beleid voor het leveren van activa voor DASH-streaming (zie [deze](media-services-axinom-integration.md#content-protection) sectie). |
| Inhoudssleutel-id |Moet worden opgenomen als onderdeel van de waarde van de claim van het Rechtbericht van JWT-token (zie [deze](media-services-axinom-integration.md#jwt-token-generation) sectie). |

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Erkenningen
We willen graag de volgende mensen erkennen die hebben bijgedragen aan het opstellen van dit document: Kristjan Jõgi van Axinom, Mingfei Yan en Amit Rajput.

