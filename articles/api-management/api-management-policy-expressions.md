---
title: Expressies voor Azure API Management-beleid | Microsoft Docs
description: Meer informatie over beleids expressies in azure API Management.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244002"
---
# <a name="api-management-policy-expressions"></a>API Management-beleids expressies
In dit artikel wordt de syntaxis van beleids C# expressies in 7 beschreven. Elke expressie heeft toegang tot de impliciet verschafte [context](api-management-policy-expressions.md#ContextVariables) variabele en een toegestane [subset](api-management-policy-expressions.md#CLRTypes) van .NET Framework typen.

Voor meer informatie:

- Zie context informatie aan uw back-end-service leveren. Gebruik de [para meter query reeks instellen](api-management-transformation-policies.md#SetQueryStringParameter) en stel het beleid voor [http-headers](api-management-transformation-policies.md#SetHTTPheader) in om deze informatie op te geven.
- Lees hoe u het JWT-beleid [valideren](api-management-access-restriction-policies.md#ValidateJWT) gebruikt voor het vooraf machtigen van toegang tot bewerkingen op basis van token claims.
- Zie een [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) -tracering gebruiken om te zien hoe beleids regels worden geëvalueerd en de resultaten van deze evaluaties.
- Lees hoe u expressies kunt gebruiken met het beleid [ophalen van cache](api-management-caching-policies.md#GetFromCache) en [opslaan in cache](api-management-caching-policies.md#StoreToCache) om API Management antwoord caching te configureren. Stel een duur in die overeenkomt met de antwoord cache van de back-end-service zoals opgegeven door de `Cache-Control`-instructie van de back-upservice.
- Zie het filteren van inhoud uitvoeren. Verwijder gegevens elementen uit het antwoord dat is ontvangen van de back-end met behulp van de [controle stroom](api-management-advanced-policies.md#choose) en [Stel hoofd](api-management-transformation-policies.md#SetBody) beleid in.
- Zie [API-Management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) github opslag plaats voor informatie over het downloaden van de beleids instructies.


## <a name="Syntax"></a>Syntaxis
Expressies met één instructie worden inge sloten in `@(expression)`, waarbij `expression` een goed opgemaakte C# expressie instructie is.

Expressies met meerdere instructies worden inge sloten in `@{expression}`. Alle code paden binnen expressies met meerdere instructies moeten eindigen met een `return`-instructie.

## <a name="PolicyExpressionsExamples"></a>Vindt

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

## <a name="PolicyExpressionsUsage"></a>Belasting
Expressies kunnen worden gebruikt als kenmerk waarden of tekst waarden in een API Management [beleid](api-management-policies.md) (tenzij de verwijzing naar het beleid anders aangeeft).

> [!IMPORTANT]
> Wanneer u beleids expressies gebruikt, is er slechts beperkte verificatie van de beleids expressies wanneer het beleid is gedefinieerd. Expressies worden tijdens runtime uitgevoerd door de gateway. eventuele uitzonde ringen die door beleids expressies worden gegenereerd, resulteren in een runtime-fout.

## <a name="CLRTypes"></a>.NET Framework typen die zijn toegestaan in beleids expressies
De volgende tabel bevat de .NET Framework typen en de leden die zijn toegestaan in beleids expressies.

|Type|Ondersteunde leden|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Alle|
|Newtonsoft.Json.JsonConvert|Serializeobject verwachtte, DeserializeObject|
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
|System. matrix|Alle|
|System.BitConverter|Alle|
|System. Boolean|Alle|
|System.Byte|Alle|
|System. char|Alle|
|System. Collections. generic. dictionary < TKey, TValue >|Alle|
|System. Collections. generic. Hashset\<T >|Alle|
|System. Collections. generic. ICollection\<T >|Alle|
|System. Collections. generic. IDictionary < TKey, TValue >|Alle|
|System. Collections. generic. IEnumerable\<T >|Alle|
|System. Collections. generic. IEnumerator\<T >|Alle|
|System. Collections. generic. IList\<T >|Alle|
|System. Collections. generic. IReadOnlyCollection\<T >|Alle|
|System. Collections. generic. IReadOnlyDictionary < TKey, TValue >|Alle|
|System. Collections. generic. ISet\<T >|Alle|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Alle|
|System. Collections. generic. list\<T >|Alle|
|System. Collections. generic. Queue\<T >|Alle|
|System. Collections. generic. stack\<T >|Alle|
|Systeem. Convert|Alle|
|System.DateTime|(Constructor), toevoegen, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, datum, dag, DayOfWeek, DayOfYear, DaysInMonth, uur, IsDaylightSavingTime, IsLeapYear, MaxValue, milliseconde, minuut, MinValue, maand, nu , Parseren, seconde, aftrekken, Ticks, TimeOfDay, vandaag, ToString, UtcNow, year|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Alle|
|Systeem. decimaal|Alle|
|Systeem. Double|Alle|
|System. Exception|Alle|
|System. GUID|Alle|
|System. Int16|Alle|
|System. Int32|Alle|
|System. Int64|Alle|
|System. IO. StringReader|Alle|
|System. IO. StringWriter|Alle|
|System. LINQ. overzicht|Alle|
|System.Math|Alle|
|System.MidpointRounding|Alle|
|System.Net.WebUtility|Alle|
|System. Nullbaar|Alle|
|Systeem. wille keurig|Alle|
|System.SByte|Alle|
|System. Security. Cryptography. AsymmetricAlgorithm|Alle|
|System.Security.Cryptography.CipherMode|Alle|
|System.Security.Cryptography.HashAlgorithm|Alle|
|System.Security.Cryptography.HashAlgorithmName|Alle|
|System.Security.Cryptography.HMAC|Alle|
|System.Security.Cryptography.HMACMD5|Alle|
|System.Security.Cryptography.HMACSHA1|Alle|
|System.Security.Cryptography.HMACSHA256|Alle|
|System.Security.Cryptography.HMACSHA384|Alle|
|System.Security.Cryptography.HMACSHA512|Alle|
|System.Security.Cryptography.KeyedHashAlgorithm|Alle|
|System.Security.Cryptography.MD5|Alle|
|System.Security.Cryptography.Oid|Alle|
|System.Security.Cryptography.PaddingMode|Alle|
|System.Security.Cryptography.RNGCryptoServiceProvider|Alle|
|System.Security.Cryptography.RSA|Alle|
|System.Security.Cryptography.RSAEncryptionPadding|Alle|
|System.Security.Cryptography.RSASignaturePadding|Alle|
|System.Security.Cryptography.SHA1|Alle|
|System.Security.Cryptography.SHA1Managed|Alle|
|System.Security.Cryptography.SHA256|Alle|
|System.Security.Cryptography.SHA256Managed|Alle|
|System.Security.Cryptography.SHA384|Alle|
|System.Security.Cryptography.SHA384Managed|Alle|
|System.Security.Cryptography.SHA512|Alle|
|System.Security.Cryptography.SHA512Managed|Alle|
|System. Security. Cryptography. SymmetricAlgorithm|Alle|
|System.Security.Cryptography.X509Certificates.PublicKey|Alle|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Alle|
|System. Security. Cryptography. X509Certificates. X500DistinguishedName|Naam|
|System. Security. Cryptography. X509Certificates. X509Certificate|Alle|
|System. Security. Cryptography. X509Certificates. X509Certificate2|Alle|
|System.Security.Cryptography.X509Certificates.X509ContentType|Alle|
|System.Security.Cryptography.X509Certificates.X509NameType|Alle|
|System. single|Alle|
|System. String|Alle|
|System. StringComparer|Alle|
|System. StringComparison|Alle|
|System. StringSplitOptions|Alle|
|System.Text.Encoding|Alle|
|System.Text.RegularExpressions.Capture|Index, lengte, waarde|
|System.Text.RegularExpressions.CaptureCollection|Aantal, item|
|System.Text.RegularExpressions.Group|Vastleg ging, geslaagd|
|System.Text.RegularExpressions.GroupCollection|Aantal, item|
|System.Text.RegularExpressions.Match|Leeg, groepen, resultaat|
|System.Text.RegularExpressions.Regex|(Constructor), IsMatch, overeenkomst, overeenkomsten, vervangen, unesc, Split|
|System.Text.RegularExpressions.RegexOptions|Alle|
|System. Text. String Builder|Alle|
|System.TimeSpan|Alle|
|System.TimeZone|Alle|
|System. time zone info. matrix adjustmentrule|Alle|
|System.TimeZoneInfo.TransitionTime|Alle|
|System.TimeZoneInfo|Alle|
|System. tuple|Alle|
|System. UInt16|Alle|
|System. UInt32|Alle|
|System. UInt64|Alle|
|System.Uri|Alle|
|System. UriPartial|Alle|
|System.Xml.Linq.Extensions|Alle|
|System.Xml.Linq.XAttribute|Alle|
|System.Xml.Linq.XCData|Alle|
|System.Xml.Linq.XComment|Alle|
|System.Xml.Linq.XContainer|Alle|
|System.Xml.Linq.XDeclaration|Alle|
|System.Xml.Linq.XDocument|Alle, behalve van: laden|
|System.Xml.Linq.XDocumentType|Alle|
|System.Xml.Linq.XElement|Alle|
|System.Xml.Linq.XName|Alle|
|System.Xml.Linq.XNamespace|Alle|
|System.Xml.Linq.XNode|Alle|
|System.Xml.Linq.XNodeDocumentOrderComparer|Alle|
|System.Xml.Linq.XNodeEqualityComparer|Alle|
|System.Xml.Linq.XObject|Alle|
|System.Xml.Linq.XProcessingInstruction|Alle|
|System.Xml.Linq.XText|Alle|
|System.Xml.XmlNodeType|Alle|

## <a name="ContextVariables"></a>Context variabele
Een variabele met de naam `context` impliciet beschikbaar in elke beleids [expressie](api-management-policy-expressions.md#Syntax). De leden van het apparaat bieden informatie die relevant is voor de `\request`. Alle `context` leden zijn alleen-lezen.

|Context variabele|Toegestane methoden, eigenschappen en parameter waarden|
|----------------------|-------------------------------------------------------|
|context|[API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Implementatie](#ref-context-deployment)<br /><br /> Verstreken: time span-time interval tussen de waarde van tijds tempel en huidige tijd<br /><br /> [Last error](#ref-context-lasterror)<br /><br /> [Bewerking](#ref-context-operation)<br /><br /> [Voortplant](#ref-context-product)<br /><br /> [Aanvraag](#ref-context-request)<br /><br /> Aanvraag-id: GUID-unieke aanvraag-id's<br /><br /> [Antwoord](#ref-context-response)<br /><br /> [Abonnement](#ref-context-subscription)<br /><br /> Tijds tempel: datum/tijd waarop de aanvraag is ontvangen<br /><br /> Tracering: BOOL-geeft aan of tracering is in-of uitgeschakeld <br /><br /> [Gebruiker](#ref-context-user)<br /><br /> [Variabelen](#ref-context-variables): IReadOnlyDictionary < teken reeks, object ><br /><br /> Trace annuleren (bericht: teken reeks)|
|<a id="ref-context-api"></a>context. Inschakelen|Id: teken reeks<br /><br /> IsCurrentRevision: BOOL<br /><br />  Name: teken reeks<br /><br /> Pad: teken reeks<br /><br /> Revisie: teken reeks<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Versie: teken reeks |
|<a id="ref-context-deployment"></a>context. Inhoudsdistributiepad|Regio: teken reeks<br /><br /> ServiceName: teken reeks<br /><br /> Certificaten: IReadOnlyDictionary < teken reeks, X509Certificate2 >|
|<a id="ref-context-lasterror"></a>context. Last error|Bron: teken reeks<br /><br /> Reden: teken reeks<br /><br /> Bericht: teken reeks<br /><br /> Bereik: teken reeks<br /><br /> Sectie: teken reeks<br /><br /> Pad: teken reeks<br /><br /> PolicyId: teken reeks<br /><br /> Voor meer informatie over context. Last error, Zie [fout afhandeling](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>context. Schijf|Id: teken reeks<br /><br /> Methode: teken reeks<br /><br /> Name: teken reeks<br /><br /> UrlTemplate: teken reeks|
|<a id="ref-context-product"></a>context. Voortplant|Api's: IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: BOOL<br /><br /> Groepen: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> Id: teken reeks<br /><br /> Name: teken reeks<br /><br /> Status: Enum ProductState {NotPublished, gepubliceerd}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: BOOL|
|<a id="ref-context-request"></a>context. Schot|Hoofd tekst: [IMessageBody](#ref-imessagebody) of `null` als de aanvraag geen hoofd tekst heeft.<br /><br /> Certificaat: System. Security. Cryptography. X509Certificates. X509Certificate2<br /><br /> [Headers](#ref-context-request-headers): IReadOnlyDictionary < teken reeks, teken reeks [] ><br /><br /> IpAddress: teken reeks<br /><br /> MatchedParameters: IReadOnlyDictionary < teken reeks, teken reeks ><br /><br /> Methode: teken reeks<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>teken reeks context. Request. headers. GetValueOrDefault (koptekstnaam: teken reeks, defaultValue: teken reeks)|kopnaam: teken reeks<br /><br /> defaultValue: teken reeks<br /><br /> Retourneert waarden van een door komma's gescheiden aanvraag header of `defaultValue` als de header niet wordt gevonden.|
|<a id="ref-context-response"></a>context. Beantwoord|Hoofd tekst: [IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers): IReadOnlyDictionary < teken reeks, teken reeks [] ><br /><br /> Status code: int<br /><br /> StatusReason: teken reeks|
|<a id="ref-context-response-headers"></a>teken reeks context. Response. headers. GetValueOrDefault (koptekstnaam: teken reeks, defaultValue: teken reeks)|kopnaam: teken reeks<br /><br /> defaultValue: teken reeks<br /><br /> Retourneert door komma's gescheiden waarden voor een antwoord header of `defaultValue` als de header niet wordt gevonden.|
|<a id="ref-context-subscription"></a>context. Abonnees|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: teken reeks<br /><br /> Sleutel: teken reeks<br /><br /> Name: teken reeks<br /><br /> PrimaryKey: teken reeks<br /><br /> Secundaire sleutel: teken reeks<br /><br /> Start date: DateTime?|
|<a id="ref-context-user"></a>context. Gebruiker|E-mail: teken reeks<br /><br /> Voor naam: teken reeks<br /><br /> Groepen: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> Id: teken reeks<br /><br /> Identiteiten: IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: String<br /><br /> Opmerking: teken reeks<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|Id: teken reeks<br /><br /> Name: teken reeks<br /><br /> Pad: teken reeks<br /><br /> Protocollen: IEnumerable < teken reeks\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|Id: teken reeks<br /><br /> Name: teken reeks|
|<a id="ref-imessagebody"></a>IMessageBody|Als < T\>(preserveContent: BOOL = false): waarbij T: String, byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> De methoden `context.Request.Body.As<T>` en `context.Response.Body.As<T>` worden gebruikt om de tekst van een aanvraag en een antwoord bericht in een opgegeven type `T`te lezen. Standaard gebruikt de methode de oorspronkelijke hoofd tekst van het bericht en wordt deze weer gegeven nadat deze is geretourneerd. Als u wilt voor komen dat de methode op een kopie van de hoofd stroom wordt toegepast, stelt u de para meter `preserveContent` in op `true`. [Hier](api-management-transformation-policies.md#SetBody) kunt u een voor beeld bekijken.|
|<a id="ref-iurl"></a>IUrl|Host: teken reeks<br /><br /> Pad: teken reeks<br /><br /> Poort: int<br /><br /> [Query](#ref-iurl-query): IReadOnlyDictionary < teken reeks, teken reeks [] ><br /><br /> Query string: teken reeks<br /><br /> Schema: teken reeks|
|<a id="ref-iuseridentity"></a>IUserIdentity|Id: teken reeks<br /><br /> Provider: teken reeks|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Header: teken reeks<br /><br /> Query: teken reeks|
|<a id="ref-iurl-query"></a>string IUrl. query. GetValueOrDefault (queryParameterName: String, defaultValue: String)|queryParameterName: teken reeks<br /><br /> defaultValue: teken reeks<br /><br /> Retourneert door komma's gescheiden waarden voor query parameters of `defaultValue` als de para meter niet is gevonden.|
|<a id="ref-context-variables"></a>T-context. Variabelen. GetValueOrDefault < T\>(variabelenaam: teken reeks, defaultValue: T)|variabelenaam: teken reeks<br /><br /> defaultValue: T<br /><br /> Retourneert een variabele waarde cast om `T` of `defaultValue` te typen als de variabele niet is gevonden.<br /><br /> Deze methode genereert een uitzonde ring als het opgegeven type niet overeenkomt met het werkelijke type van de geretourneerde variabele.|
|BasicAuthCredentials AsBasic (invoer: deze teken reeks)|invoer: teken reeks<br /><br /> Als de invoer parameter een geldige waarde voor de header verificatie aanvraag voor HTTP-basis authenticatie bevat, retourneert de methode een object van het type `BasicAuthCredentials`; anders retourneert de methode null.|
|BOOL TryParseBasic (invoer: deze teken reeks, resultaat: out BasicAuthCredentials)|invoer: teken reeks<br /><br /> resultaat: uit-BasicAuthCredentials<br /><br /> Als de invoer parameter een geldige HTTP Basic Authentication-autorisatie waarde in de aanvraag header bevat, retourneert de methode `true` en de resultaat parameter bevat een waarde van het type `BasicAuthCredentials`; anders retourneert de methode `false`.|
|BasicAuthCredentials|Wacht woord: teken reeks<br /><br /> UserId: teken reeks|
|JWT-AsJwt (invoer: deze teken reeks)|invoer: teken reeks<br /><br /> Als de invoer parameter een geldige JWT-token waarde bevat, retourneert de methode een object van het type `Jwt`; anders retourneert de methode `null`.|
|BOOL TryParseJwt (invoer: deze teken reeks, resultaat: out JWT)|invoer: teken reeks<br /><br /> resultaat: out JWT<br /><br /> Als de invoer parameter een geldige JWT-token waarde bevat, retourneert de methode `true` en de resultaat parameter bevat een waarde van het type `Jwt`; anders retourneert de methode `false`.|
|JWT|Algoritme: teken reeks<br /><br /> Doel groep: IEnumerable < teken reeks\><br /><br /> Claims: IReadOnlyDictionary < teken reeks, teken reeks [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: teken reeks<br /><br /> Verlener: teken reeks<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Onderwerp: teken reeks<br /><br /> Type: teken reeks|
|string JWT. claims. GetValueOrDefault (claimnaam: teken reeks, defaultValue: teken reeks)|claimnaam: teken reeks<br /><br /> defaultValue: teken reeks<br /><br /> Retourneert door komma's gescheiden claim waarden of `defaultValue` als de header niet wordt gevonden.|
|byte [] versleutelen (invoer: deze byte [], alg: teken reeks, sleutel: byte [], IV: byte [])|invoer-platte tekst die moet worden versleuteld<br /><br />Alg: naam van een symmetrische versleutelings algoritme<br /><br />sleutel versleutelings sleutel<br /><br />IV-initialisatie vector<br /><br />Retourneert versleutelde tekst zonder opmaak.|
|byte [] versleutelen (invoer: deze byte [], alg: System. Security. Cryptography. SymmetricAlgorithm)|invoer-platte tekst die moet worden versleuteld<br /><br />Alg-versleutelings algoritme<br /><br />Retourneert versleutelde tekst zonder opmaak.|
|byte [] versleutelen (invoer: deze byte [], alg: System. Security. Cryptography. SymmetricAlgorithm, sleutel: byte [], IV: byte [])|invoer-platte tekst die moet worden versleuteld<br /><br />Alg-versleutelings algoritme<br /><br />sleutel versleutelings sleutel<br /><br />IV-initialisatie vector<br /><br />Retourneert versleutelde tekst zonder opmaak.|
|byte [] ontsleutelen (invoer: deze byte [], alg: teken reeks, sleutel: byte [], IV: byte [])|invoer-coderings tekst die moet worden ontsleuteld<br /><br />Alg: naam van een symmetrische versleutelings algoritme<br /><br />sleutel versleutelings sleutel<br /><br />IV-initialisatie vector<br /><br />Retourneert Lees bare tekst.|
|byte [] ontsleutelen (invoer: deze byte [], alg: System. Security. Cryptography. SymmetricAlgorithm)|invoer-coderings tekst die moet worden ontsleuteld<br /><br />Alg-versleutelings algoritme<br /><br />Retourneert Lees bare tekst.|
|byte [] ontsleutelen (invoer: deze byte [], alg: System. Security. Cryptography. SymmetricAlgorithm, sleutel: byte [], IV: byte [])|invoer-coderings tekst die moet worden ontsleuteld<br /><br />Alg-versleutelings algoritme<br /><br />sleutel versleutelings sleutel<br /><br />IV-initialisatie vector<br /><br />Retourneert Lees bare tekst.|
|BOOL VerifyNoRevocation (invoer: deze System. Security. Cryptography. X509Certificates. X509Certificate2)|Voert een X. 509-keten validatie uit zonder de intrekkings status van het certificaat te controleren.<br /><br />invoer certificaat object<br /><br />Retourneert `true` als de validatie slaagt. `false` als de validatie is mislukt.|


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van beleid:

+ [Beleid in API Management](api-management-howto-policies.md)
+ [Api's transformeren](transform-api.md)
+ [Beleids verwijzing](api-management-policy-reference.md) voor een volledige lijst met beleids instructies en hun instellingen
+ [Voor beelden van beleid](policy-samples.md)
