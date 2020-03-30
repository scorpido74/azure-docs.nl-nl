---
title: Azure API Management-beleidsexpressies | Microsoft Documenten
description: Meer informatie over beleidsexpressies in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244002"
---
# <a name="api-management-policy-expressions"></a>API-beheerbeleidsexpressies
In dit artikel wordt de syntaxis van beleidsexpressies in C# 7 besproken. Elke expressie heeft toegang tot de impliciet verstrekte [contextvariabele](api-management-policy-expressions.md#ContextVariables) en een toegestane [subset](api-management-policy-expressions.md#CLRTypes) van .NET Framework-typen.

Voor meer informatie:

- Bekijk hoe u contextinformatie verstrekken aan uw backendservice. Gebruik de [parameter Querytekenreeks instellen](api-management-transformation-policies.md#SetQueryStringParameter) en [HTTP-kopteksten instellen](api-management-transformation-policies.md#SetHTTPheader) om deze informatie te verstrekken.
- Bekijk hoe u het [JWT-beleid valideren](api-management-access-restriction-policies.md#ValidateJWT) gebruiken om toegang tot bewerkingen vooraf te autoriseren op basis van tokenclaims.
- Bekijk hoe u een [API Inspector-tracering](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) gebruikt om te zien hoe het beleid wordt geëvalueerd en hoe de resultaten van die evaluaties.
- Bekijk hoe u expressies gebruikt met het beleid [Ophalen van cache](api-management-caching-policies.md#GetFromCache) en Opslag naar [cache](api-management-caching-policies.md#StoreToCache) om api-beheerresponscache te configureren. Stel een duur in die overeenkomt met de reactiecache van de `Cache-Control` backendservice zoals opgegeven in de richtlijn van de back-service.
- Bekijk hoe u inhoudsfiltering uitvoert. Verwijder gegevenselementen uit het antwoord dat vanaf de back-end is ontvangen met behulp van het beleid [Controlestroom](api-management-advanced-policies.md#choose) en [Hoofdtekst instellen.](api-management-transformation-policies.md#SetBody)
- Zie de [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) GitHub repo om de beleidsoverzichten te downloaden.


## <a name="syntax"></a><a name="Syntax"></a>Syntaxis
Enkele instructieexpressies `@(expression)`zijn `expression` ingesloten , waar is een goed gevormde C# expressie-instructie.

Multi-statement expressies `@{expression}`zijn ingesloten in . Alle codepaden binnen expressies met `return` meerdere regels moeten eindigen met een instructie.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a>Voorbeelden

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Gebruik
Expressies kunnen worden gebruikt als kenmerkwaarden of tekstwaarden in een [API-beheerbeleid](api-management-policies.md) (tenzij de beleidsverwijzing anders specificeert).

> [!IMPORTANT]
> Wanneer u beleidsexpressies gebruikt, is er slechts beperkte verificatie van de beleidsexpressies wanneer het beleid wordt gedefinieerd. Expressies worden uitgevoerd door de gateway tijdens de uitvoering, eventuele uitzonderingen gegenereerd door beleidsexpressies resulteren in een runtime-fout.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a>.NET Framework-typen toegestaan in beleidsuitingen
In de volgende tabel worden de typen .NET Framework en hun leden weergegeven die zijn toegestaan in beleidsexpressies.

|Type|Ondersteunde leden|
|--------------|-----------------------|
|Newtonsoft.Json.Opmaak|Alle|
|Newtonsoft.Json.Json|SerialiserenObject, DeserialiserenObject|
|Newtonsoft.Json.Linq.Extensions|Alle|
|Newtonsoft.Json.Linq.JArray|Alle|
|Newtonsoft.Json.Linq.JConstructor|Alle|
|Newtonsoft.Json.Linq.JContainer|Alle|
|Newtonsoft.Json.Linq.JObject|Alle|
|Newtonsoft.Json.Linq.JProperty|Alle|
|Newtonsoft.Json.Linq.JRaw|Alle|
|Newtonsoft.Json.Linq.JToken|Alle|
|Newtonsoft.Json.Linq.JTokenType|Alle|
|Newtonsoft.Json.Linq.JValue|Alle|
|System.Array|Alle|
|System.BitConverter|Alle|
|System.Boolean System.Boolean System.Boolean System.|Alle|
|System.Byte System.Byte|Alle|
|System.Char System.Char|Alle|
|System.Collections.Generic.Dictionary<TKey, TValue>|Alle|
|System.Collections.Generic.HashSet\<T>|Alle|
|System.Collections.Generic.ICollection\<T>|Alle|
|System.Collections.Generic.IDictionary<TKey, TValue>|Alle|
|System.Collections.Generic.IEnumerable\<T>|Alle|
|System.Collections.Generic.IEnumerator\<T>|Alle|
|System.Collections.Generic.IList\<T>|Alle|
|System.Collections.Generic.IReadOnlyCollection\<T>|Alle|
|System.Collections.Generic.ireadOnlyDictionary<TKey, TValue>|Alle|
|System.Collections.Generic.ISet\<T>|Alle|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Alle|
|System.Collections.Generic.List\<T>|Alle|
|System.Collections.Generic.Queue\<T>|Alle|
|System.Collections.Generic.Stack\<T>|Alle|
|System.Convert|Alle|
|System.DateTime|(constructor), Add, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, Date, Day, DayOfWeek, DayOfYear, DaysInMonth, Hour, IsdaylightSavingTime, IsLeapYear, MaxValue, Milliseconde, Minute, MinValue, Month, Now , Parse, Tweede, Aftrekken, Teken, TimeOfDay, Vandaag, ToString, UtcNow, Jaar|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Alle|
|Systeem.Decimal|Alle|
|System.Double|Alle|
|System.Exception|Alle|
|System.Guid System.Guid|Alle|
|System.Int16|Alle|
|System.Int32|Alle|
|System.Int64|Alle|
|System.IO.StringReader|Alle|
|System.IO.StringWriter|Alle|
|System.Linq.Enumerable|Alle|
|System.Math System.Math|Alle|
|System.MidpointRounding|Alle|
|System.Net.WebUtility|Alle|
|System.Nullable|Alle|
|System.Random System.Random System.Random System.|Alle|
|System.SByte System.SByte|Alle|
|System.Security.Cryptography.AsymmetricAlgoritme|Alle|
|System.Security.Cryptography.CipherMode|Alle|
|System.Security.Cryptography.HashAlgorithm|Alle|
|System.Security.Cryptography.HashAlgorithmName|Alle|
|System.Security.Cryptografie.HMAC|Alle|
|System.Security.Cryptography.HMACMD5|Alle|
|System.Security.Cryptography.HMACSHA1|Alle|
|System.Security.Cryptography.HMACSHA256|Alle|
|System.Security.Cryptography.HMACSHA384|Alle|
|System.Security.Cryptography.HMACSHA512|Alle|
|System.Security.Cryptography.KeyedHashAlgorithm|Alle|
|System.Security.Cryptografie.MD5|Alle|
|System.Security.Cryptoography.Oid System.Security.Cryptography.Oid|Alle|
|System.Security.Cryptography.PaddingMode|Alle|
|System.Security.Cryptography.RNGCryptoServiceProvider|Alle|
|System.Security.Cryptoography.RSA|Alle|
|System.Security.Cryptography.RSAEncryptionPadding|Alle|
|System.Security.Cryptography.RSASignaturePadding|Alle|
|System.Security.Cryptografie.SHA1|Alle|
|System.Security.Cryptography.SHA1Managed|Alle|
|System.Security.Cryptography.SHA256|Alle|
|System.Security.Cryptography.SHA256Beheerd|Alle|
|System.Security.Cryptografie.SHA384|Alle|
|System.Security.Cryptography.SHA384Beheerd|Alle|
|System.Security.Cryptografie.SHA512|Alle|
|System.Security.Cryptography.SHA512Managed|Alle|
|System.Security.Cryptography.SymmetricAlgorithm System.Security.Cryptography.SymmetricAlgorithm System.Security.Cryptography.SymmetricAlgorithm System.|Alle|
|System.Security.Cryptography.X509Certificates.PublicKey|Alle|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Alle|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Name|
|System.Security.Cryptography.X509Certificaten.X509Certificaat|Alle|
|System.Security.Cryptography.X509Certificaten.X509Certificate2|Alle|
|System.Security.Cryptography.X509Certificaten.X509ContentType|Alle|
|System.Security.Cryptography.X509Certificaten.X509NameType|Alle|
|System.Single|Alle|
|System.String|Alle|
|System.StringComparer|Alle|
|System.StringComparison|Alle|
|System.StringSplitOptions|Alle|
|System.Text.Encoding|Alle|
|System.Text.RegularExpressions.Capture|Index, Lengte, Waarde|
|System.Text.RegularExpressions.CaptureCollection|Aantal, Object|
|System.Text.RegularExpressions.Group|Vangt, Succes|
|System.Text.RegularExpressions.GroupCollection|Aantal, Object|
|System.Text.RegularExpressions.Match|Leeg, Groepen, Resultaat|
|System.Text.RegularExpressions.Regex|(Constructor), IsMatch, Match, Matches, Replace, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Alle|
|System.Text.StringBuilder|Alle|
|System.TimeSpan|Alle|
|System.TimeZone|Alle|
|Regel system.timezoneinfo.adjustmentrule|Alle|
|System.TimeZoneInfo.TransitionTime|Alle|
|System.TimeZoneInfo|Alle|
|System.Tuple|Alle|
|System.UInt16|Alle|
|System.UInt32|Alle|
|System.UInt64|Alle|
|System.Uri System.Uri|Alle|
|System.UriPartial System.Uri|Alle|
|System.Xml.Linq.Extensions|Alle|
|System.Xml.Linq.XAttribute|Alle|
|System.Xml.Linq.XCData|Alle|
|System.Xml.Linq.XComment|Alle|
|System.Xml.Linq.XContainer|Alle|
|System.Xml.Linq.XDe|Alle|
|System.Xml.Linq.XDocument|Alles, behalve: Laden|
|System.Xml.Linq.XDocumentType|Alle|
|System.Xml.Linq.XElement|Alle|
|System.Xml.Linq.XName|Alle|
|System.Xml.Linq.XNamespace|Alle|
|System.Xml.Linq.XNode|Alle|
|System.Xml.Linq.XNodeDocumentOrderComparer|Alle|
|System.Xml.Linq.XNodeEqualityComparer|Alle|
|System.Xml.Linq.XObject|Alle|
|System.Xml.Linq.XProcessingInstructie|Alle|
|System.Xml.Linq.XText|Alle|
|System.Xml.XmlNodeType|Alle|

## <a name="context-variable"></a><a name="ContextVariables"></a>Contextvariabele
Een variabele `context` met de naam is impliciet beschikbaar in elke [beleidsexpressie](api-management-policy-expressions.md#Syntax). Haar leden verstrekken informatie `\request`die relevant is voor de . Alle `context` leden zijn alleen-lezen.

|Contextvariabele|Toegestane methoden, eigenschappen en parameterwaarden|
|----------------------|-------------------------------------------------------|
|context|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Implementatie](#ref-context-deployment)<br /><br /> Verstreken: TimeSpan - tijdsinterval tussen de waarde van Timestamp en huidige tijd<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Bewerking](#ref-context-operation)<br /><br /> [Product](#ref-context-product)<br /><br /> [Aanvraag](#ref-context-request)<br /><br /> RequestId: Guid - unieke aanvraag-id<br /><br /> [Reactie](#ref-context-response)<br /><br /> [Abonnement](#ref-context-subscription)<br /><br /> Tijdstempel: DateTime - moment in de tijd waarop de aanvraag is ontvangen<br /><br /> Tracering: bool - geeft aan of tracering is ingeschakeld of uitgeschakeld <br /><br /> [Gebruiker](#ref-context-user)<br /><br /> [Variabelen](#ref-context-variables): IReadOnlyDictionary<tekenreeks, object><br /><br /> lege traceer(bericht: tekenreeks)|
|<a id="ref-context-api"></a>Context. Api|Id: tekenreeks<br /><br /> IsCurrentRevision: bool<br /><br />  Naam: tekenreeks<br /><br /> Pad: tekenreeks<br /><br /> Revisie: tekenreeks<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Versie: tekenreeks |
|<a id="ref-context-deployment"></a>Context. Implementatie|Regio: tekenreeks<br /><br /> ServiceName: tekenreeks<br /><br /> Certificaten: iReadOnlyDictionary<tekenreeks, X509Certificate2>|
|<a id="ref-context-lasterror"></a>Context. Laatste fout|Bron: tekenreeks<br /><br /> Reden: tekenreeks<br /><br /> Bericht: tekenreeks<br /><br /> Bereik: tekenreeks<br /><br /> Sectie: tekenreeks<br /><br /> Pad: tekenreeks<br /><br /> PolicyId: tekenreeks<br /><br /> Voor meer informatie over context. LastError, zie [Foutafhandeling](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>Context. Bewerking|Id: tekenreeks<br /><br /> Methode: tekenreeks<br /><br /> Naam: tekenreeks<br /><br /> UrlTemplate: tekenreeks|
|<a id="ref-context-product"></a>Context. Product|Apis: IEnumerable<[IApi](#ref-iapi)\><br /><br /> GoedkeuringVereist: bool<br /><br /> Groepen: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: tekenreeks<br /><br /> Naam: tekenreeks<br /><br /> Staat: enum ProductState {NotPublished, Published}<br /><br /> AbonnementLimiet: int?<br /><br /> AbonnementVereist: bool|
|<a id="ref-context-request"></a>Context. Verzoek|Body: [IMessageBody](#ref-imessagebody) of `null` als verzoek heeft geen lichaam.<br /><br /> Certificaat: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Kopteksten](#ref-context-request-headers): IReadOnlyDictionary<tekenreeks, tekenreeks[]><br /><br /> IpAddress: tekenreeks<br /><br /> MatchedParameters: IReadOnlyDictionary<tekenreeks,><br /><br /> Methode: tekenreeks<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>tekenreekscontext. Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: tekenreeks<br /><br /> standaardwaarde: tekenreeks<br /><br /> Retourneert door komma's `defaultValue` gescheiden fouttekstwaarden of als de koptekst niet wordt gevonden.|
|<a id="ref-context-response"></a>Context. Reactie|Lichaam: [IMessageBody](#ref-imessagebody)<br /><br /> [Kopteksten](#ref-context-response-headers): IReadOnlyDictionary<tekenreeks, tekenreeks[]><br /><br /> StatusCode: int<br /><br /> StatusReason: tekenreeks|
|<a id="ref-context-response-headers"></a>tekenreekscontext. Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: tekenreeks<br /><br /> standaardwaarde: tekenreeks<br /><br /> Retourneert door komma's `defaultValue` gescheiden antwoordkopwaarden of als de koptekst niet wordt gevonden.|
|<a id="ref-context-subscription"></a>Context. Abonnement|CreatedTime: DateTime<br /><br /> Einddatum: DateTime?<br /><br /> Id: tekenreeks<br /><br /> Sleutel: tekenreeks<br /><br /> Naam: tekenreeks<br /><br /> Primaire sleutel: tekenreeks<br /><br /> SecondaryKey: tekenreeks<br /><br /> Startdatum: DateTime?|
|<a id="ref-context-user"></a>Context. Gebruiker|E-mail: tekenreeks<br /><br /> Voornaam: tekenreeks<br /><br /> Groepen: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: tekenreeks<br /><br /> Identiteiten: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> Achternaam: tekenreeks<br /><br /> Opmerking: tekenreeks<br /><br /> Registratiedatum: Datum|
|<a id="ref-iapi"></a>IApi (IApi)|Id: tekenreeks<br /><br /> Naam: tekenreeks<br /><br /> Pad: tekenreeks<br /><br /> Protocollen: IEnumerable<string\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameternames: [iSubscriptionKeyParameternames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: tekenreeks<br /><br /> Naam: tekenreeks|
|<a id="ref-imessagebody"></a>IMessageBody|Als<\>T (preserveContent: bool = false): Waar T: tekenreeks, byte[],JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> De `context.Request.Body.As<T>` `context.Response.Body.As<T>` methoden en methoden worden gebruikt om een aanvraag- `T`en antwoordberichtinstellingen in een bepaald type te lezen. Standaard gebruikt de methode de oorspronkelijke berichtbronstroom en maakt deze niet beschikbaar nadat deze is geretourneerd. Om te voorkomen dat door de methode te laten `preserveContent` werken `true`op een kopie van de lichaamsstroom, stelt u de parameter in op . Ga [hier](api-management-transformation-policies.md#SetBody) om een voorbeeld te zien.|
|<a id="ref-iurl"></a>IUrl|Host: tekenreeks<br /><br /> Pad: tekenreeks<br /><br /> Haven: int<br /><br /> [Query:](#ref-iurl-query)iReadOnlyDictionary<tekenreeks, tekenreeks[]><br /><br /> Querytekenreeks: tekenreeks<br /><br /> Regeling: tekenreeks|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: tekenreeks<br /><br /> Provider: tekenreeks|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Koptekst: tekenreeks<br /><br /> Query: tekenreeks|
|<a id="ref-iurl-query"></a>tekenreeks IUrl.Query.GetValueOrDefault(queryParameterName: tekenreeks, standaardwaarde: tekenreeks)|queryParameterName: tekenreeks<br /><br /> standaardwaarde: tekenreeks<br /><br /> Retourneert door komma's `defaultValue` gescheiden queryparameterwaarden of als de parameter niet wordt gevonden.|
|<a id="ref-context-variables"></a>T context. Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: tekenreeks<br /><br /> standaardwaarde: T<br /><br /> Retourneert variabele `T` waarde `defaultValue` die wordt gegoten op tekst of als de variabele niet wordt gevonden.<br /><br /> Met deze methode wordt een uitzondering gemaakt als het opgegeven type niet overeenkomt met het werkelijke type van de geretourneerde variabele.|
|BasicAuthCredentials AsBasic(invoer: deze tekenreeks)|invoer: tekenreeks<br /><br /> Als de invoerparameter een geldige http Basic Authentication authorization authorization request `BasicAuthCredentials`header value bevat, retourneert de methode een object van het type ; anders retourneert de methode null.|
|bool TryParseBasic(input: deze tekenreeks, resultaat: out BasicAuthCredentials)|invoer: tekenreeks<br /><br /> resultaat: out BasicAuthCredentials<br /><br /> Als de invoerparameter een geldige HTTP Basic Authentication-autorisatiewaarde `true` bevat in de aanvraagkop `BasicAuthCredentials`wordt de methode geretourneerd en bevat de resultaatparameter een waarde van het type ; anders retourneert `false`de methode .|
|Basisreferenties van Basisreferenties|Wachtwoord: tekenreeks<br /><br /> UserId: tekenreeks|
|Jwt AsJwt(input: deze tekenreeks)|invoer: tekenreeks<br /><br /> Als de invoerparameter een geldige JWT-tokenwaarde bevat, `Jwt`retourneert de methode een object van het type ; anders retourneert `null`de methode .|
|bool TryParseJwt(input: deze tekenreeks, resultaat: uit Jwt)|invoer: tekenreeks<br /><br /> resultaat: out Jwt<br /><br /> Als de invoerparameter een geldige JWT-tokenwaarde bevat, wordt de methode geretourneerd `true` en bevat de resultaatparameter een waarde van het type `Jwt`; anders retourneert `false`de methode .|
|Jwt Jwt|Algoritme: tekenreeks<br /><br /> Publiek: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<tekenreeks, tekenreeks[]><br /><br /> Expiratietijd: DateTime?<br /><br /> Id: tekenreeks<br /><br /> Uitgever: tekenreeks<br /><br /> UitgegevenOp: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Onderwerp: tekenreeks<br /><br /> Tekst: tekenreeks|
|tekenreeks Jwt.Claims.GetValueOrDefault(claimName: tekenreeks, standaardwaarde: tekenreeks)|claimName: tekenreeks<br /><br /> standaardwaarde: tekenreeks<br /><br /> Retourneert door komma's gescheiden claimwaarden of `defaultValue` als de koptekst niet wordt gevonden.|
|byte[] Versleutelen(invoer: deze byte[], alg: tekenreeks, toets:byte[], iv:byte[])|invoer - plaintext die moet worden versleuteld<br /><br />alg - naam van een symmetrisch versleutelingsalgoritme<br /><br />sleutel - versleutelingssleutel<br /><br />iv - initialisatievector<br /><br />Retourneert versleutelde plaintext.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|invoer - plaintext die moet worden versleuteld<br /><br />alg - encryptie-algoritme<br /><br />Retourneert versleutelde plaintext.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|invoer - plaintext die moet worden versleuteld<br /><br />alg - encryptie-algoritme<br /><br />sleutel - versleutelingssleutel<br /><br />iv - initialisatievector<br /><br />Retourneert versleutelde plaintext.|
|byte[] Decoderen(input: deze byte[], alg: tekenreeks, toets:byte[], iv:byte[])|invoer - cypher tekst te ontsleutelen<br /><br />alg - naam van een symmetrisch versleutelingsalgoritme<br /><br />sleutel - versleutelingssleutel<br /><br />iv - initialisatievector<br /><br />Retourneert plaintext.|
|byte[] Decoderen(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|invoer - cypher tekst te ontsleutelen<br /><br />alg - encryptie-algoritme<br /><br />Retourneert plaintext.|
|byte[] Decoderen(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|invoer - cypher tekst te ontsleutelen<br /><br />alg - encryptie-algoritme<br /><br />sleutel - versleutelingssleutel<br /><br />iv - initialisatievector<br /><br />Retourneert plaintext.|
|bool VerifyNoRevocation(input: this System.Security.Cryptography.X509Certificates.X509Certificate2)|Hiermee voert u een X.509-ketenvalidatie uit zonder de status van certificaatintrekking te controleren.<br /><br />invoer - certificaatobject<br /><br />Retourneert `true` als de validatie slaagt; `false` als de validatie mislukt.|


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over beleid:

+ [Beleid in API-beheer](api-management-howto-policies.md)
+ [API's transformeren](transform-api.md)
+ [Beleidsreferentie](api-management-policy-reference.md) voor een volledige lijst met beleidsoverzichten en hun instellingen
+ [Beleidsvoorbeelden](policy-samples.md)
