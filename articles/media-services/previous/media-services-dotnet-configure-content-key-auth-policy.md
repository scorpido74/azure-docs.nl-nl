---
title: Een autorisatiebeleid voor inhoudssleutel configureren met behulp van de Media Services .NET SDK | Microsoft Documenten
description: Meer informatie over het configureren van een autorisatiebeleid voor een inhoudssleutel met behulp van de Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 58d52cd194ca4391c61f2477189984273df1198a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251204"
---
# <a name="configure-a-content-key-authorization-policy"></a>Een autorisatiebeleid voor inhoudssleutel configureren

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Overzicht
 U Azure Media Services gebruiken om MPEG-DASH-, Smooth Streaming- en HTTP Live Streaming (HLS)-streams (HLS) te leveren die zijn beveiligd met de Advanced Encryption Standard (AES) met behulp van 128-bits versleutelingssleutels of [PlayReady digital rights management (DRM).](https://www.microsoft.com/playready/overview/) Met Media Services u dashstreams ook versleuteld leveren met Widevine DRM. Zowel PlayReady als Widevine zijn versleuteld volgens de specificatie Common Encryption (ISO/IEC 23001-7 CENC).

Media Services biedt ook een key/license delivery service van waaruit klanten AES-toetsen of PlayReady/Widevine-licenties kunnen verkrijgen om de versleutelde inhoud af te spelen.

Als u wilt dat Media Services een asset versleutelt, moet u een versleutelingssleutel (CommonEncryption of EnvelopeEncryption) aan het activum koppelen. Zie [ContentKeys maken met .NET](media-services-dotnet-create-contentkey.md)voor meer informatie. U moet ook autorisatiebeleid configureren voor de sleutel (zoals beschreven in dit artikel).

Wanneer een stream door een speler wordt aangevraagd, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met Behulp van AES- of DRM-versleuteling. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. Als u wilt bepalen of de gebruiker gemachtigd is om de sleutel te krijgen, evalueert de service het autorisatiebeleid dat u voor de sleutel hebt opgegeven.

Media Services ondersteunt meerdere manieren om gebruikers te verifiëren die sleutels aanvragen. Het autorisatiebeleid voor inhoudssleutel kan een of meer autorisatiebeperkingen bevatten. De opties zijn open of token beperking. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in het eenvoudige webtoken[(SWT)-formaat](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)en het JSON Web Token[(JWT)-formaat.](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)

Media Services biedt geen STS. U een aangepaste STS maken of Azure Access Control Service gebruiken om tokens uit te geven. De STS moet zijn geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel- en uitgifteclaims die u hebt opgegeven in de configuratie van tokenbeperkingen (zoals beschreven in dit artikel). Als het token geldig is en de claims in het token overeenkomen met de claims die zijn geconfigureerd voor de inhoudssleutel, retourneert de service voor het leveren van de mediaservicesdesleutel de versleutelingssleutel naar de client.

Raadpleeg voor meer informatie de volgende artikelen:

- [JWT-tokenverificatie](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integratie van azure media services owin mvc-gebaseerde app met Azure Active Directory en beperken van de levering van inhoudssleutels op basis van JWT-claims](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Sommige overwegingen zijn van toepassing
* Wanneer uw Media Services-account is gemaakt, wordt er een standaard streaming-eindpunt met de status Gestopt aan uw account toegevoegd. Om te beginnen met het streamen van uw inhoud en te profiteren van dynamische verpakking en dynamische versleuteling, moet uw streaming-eindpunt zich in de status 'Lopend' bevindt. 
* Uw asset moet een set adaptieve bitrate MP4's of adaptieve bitrate Smooth Streaming-bestanden bevatten. Zie [Een actief coderen voor](media-services-encode-asset.md)meer informatie .
* Upload en codeer uw assets met de optie AssetCreationOptions.StorageEncrypted.
* Als u van plan bent meerdere inhoudssleutels te hebben waarvoor dezelfde beleidsconfiguratie vereist is, raden we u aan één autorisatiebeleid te maken en het opnieuw te gebruiken met meerdere inhoudssleutels.
* De key delivery service caches ContentKeyAuthorizationPolicy en de bijbehorende objecten (beleidsopties en beperkingen) gedurende 15 minuten. U ContentKeyAuthorizationPolicy maken en opgeven om een tokenbeperking te gebruiken, te testen en het beleid vervolgens bij te werken naar de open beperking. Dit proces duurt ongeveer 15 minuten voordat het beleid overschakelt naar de open versie van het beleid.
* Als u het leveringsbeleid voor uw asset toevoegt of bijwerkt, moet u een eventuele locator verwijderen en een nieuwe locator maken.
* Momenteel u progressieve downloads niet versleutelen.
* Een streamingeindpunt voor Media Services stelt de waarde van de CORS-header 'Access-Control-Allow-Origin' in preflight-respons in als de wildcard '\*'. Deze waarde werkt goed bij de meeste spelers, waaronder Azure Media Player, Roku en JWPlayer, en anderen. Sommige spelers die dashjs gebruiken, werken echter niet omdat xmlHttpRequest in hun dashjs niet toestaat\*dat xmlhttprequest in de referentiemodus is ingesteld als de waarde van 'Access-Control-Allow-Origin'. Als tijdelijke oplossing voor deze beperking in dashjs kan Media Services dat domein opgeven in de preflight-antwoordkop als u uw client vanuit één domein host. Open voor hulp een ondersteuningsticket via de Azure-portal.

## <a name="aes-128-dynamic-encryption"></a>Dynamische versleuteling van AES-128
### <a name="open-restriction"></a>Open beperking
Open beperking betekent dat het systeem de sleutel levert aan iedereen die een sleutelverzoek doet. Deze beperking kan nuttig zijn voor testdoeleinden.

In het volgende voorbeeld wordt een open autorisatiebeleid maakt en wordt toegevoegd aan de inhoudssleutel:
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Tokenbeperking
In deze sectie wordt beschreven hoe u een autorisatiebeleid voor inhoudssleutel maakt en deze koppelt aan de inhoudssleutel. Het autorisatiebeleid beschrijft aan welke autorisatievereisten moet worden voldaan om te bepalen of de gebruiker gemachtigd is om de sleutel te ontvangen. Bevat de lijst met verificatiesleutels bijvoorbeeld de sleutel waarmee het token is ondertekend?

Als u de optie tokenbeperking wilt configureren, moet u een XML gebruiken om de autorisatievereisten van het token te beschrijven. Xml voor tokenbeperkingmoet voldoen aan het volgende XML-schema:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
Wanneer u het beleid met een tokenbeperking configureert, moet u de primaire verificatiesleutel, de uitgever en de doelgroepparameters opgeven. De primaire verificatiesleutel bevat de sleutel waarmee het token is ondertekend. De uitgever is de STS die het token uitgeeft. De doelgroep (ook wel scope genoemd) beschrijft de intentie van het token of de bron waartoe het token toegang geeft. De service voor het leveren van de sleutel van Media Services valideert dat deze waarden in het token overeenkomen met de waarden in de sjabloon.

Wanneer u de Media Services SDK voor .NET gebruikt, u de klasse TokenRestrictionTemplate gebruiken om het beperkingstoken te genereren.
In het volgende voorbeeld wordt een autorisatiebeleid met een tokenbeperking. In dit voorbeeld moet de client een token presenteren dat een ondertekeningssleutel (VerificationKey), een tokenuitgever en vereiste claims bevat.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>Testtoken
Ga als volgt te werk om een testtoken te krijgen op basis van de tokenbeperking die is gebruikt voor het sleutelautorisatiebeleid:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>Dynamische Versleuteling van PlayReady
U Media Services gebruiken om de rechten en beperkingen te configureren die de PlayReady DRM-runtime wilt afdwingen wanneer een gebruiker beveiligde inhoud probeert af te spelen. 

Wanneer u uw inhoud beschermt met PlayReady, is een van de dingen die u in uw autorisatiebeleid moet opgeven een XML-tekenreeks die de [playready-licentiesjabloon](media-services-playready-license-template-overview.md)definieert. In de klassen Media Services SDK voor .NET helpen de klassen PlayReadyLicenseResponseTemplate en PlayReadyLicenseTemplate u bij het definiëren van de PlayReady-licentiesjabloon.

Zie [PlayReady en/of Widevine dynamische gemeenschappelijke versleuteling](media-services-protect-with-playready-widevine.md)gebruiken voor meer informatie over het versleutelen van uw inhoud met PlayReady en Widevine.

### <a name="open-restriction"></a>Open beperking
Open beperking betekent dat het systeem de sleutel levert aan iedereen die een sleutelverzoek doet. Deze beperking kan nuttig zijn voor testdoeleinden.

In het volgende voorbeeld wordt een open autorisatiebeleid maakt en wordt toegevoegd aan de inhoudssleutel:

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>Tokenbeperking
Als u de optie tokenbeperking wilt configureren, moet u een XML gebruiken om de autorisatievereisten van het token te beschrijven. Xml voor tokenbeperking moet voldoen aan het XML-schema in de sectie 'Tokenbeperkingsschema'.

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

Zie de sectie "[Testtoken"](#test-token)om een testtoken te krijgen op basis van de tokenbeperking die is gebruikt voor het sleutelautorisatiebeleid. 

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>Typen die worden gebruikt wanneer u ContentKeyAuthorizationPolicy definieert
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a name="tokentype"></a><a id="TokenType"></a>TokenType (TokenType)

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en onderworpen aan de servicevoorwaarden en het privacybeleid van Google, Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
Zie Een beleid voor het leveren van een [asset-beleid configureren](media-services-dotnet-configure-asset-delivery-policy.md)nu u het autorisatiebeleid van de inhoudssleutel hebt geconfigureerd.

