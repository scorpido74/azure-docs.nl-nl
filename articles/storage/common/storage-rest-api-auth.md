---
title: Call REST API-bewerkingen met shared key-autorisatie
titleSuffix: Azure Storage
description: Gebruik de Azure Storage REST API om een aanvraag in te dienen voor Blob-opslag met behulp van de autorisatie van Gedeelde sleutel.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f5c6125b850062450516e7fc0b19c2e0d5d6f577
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916061"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>Call REST API-bewerkingen met shared key-autorisatie

In dit artikel ziet u hoe u de API's voor Azure Storage REST aanroept, inclusief hoe u de koptekst Autorisatie vormen. Het is geschreven vanuit het oogpunt van een ontwikkelaar die niets weet over REST en geen idee hoe je een REST call te maken. Nadat u meer te weten komt over het aanroepen van een REST-bewerking, u deze kennis gebruiken om andere Azure Storage REST-bewerkingen te gebruiken.

## <a name="prerequisites"></a>Vereisten

De voorbeeldtoepassing bevat de blobcontainers voor een opslagaccount. Als u de code in dit artikel wilt uitproberen, hebt u de volgende items nodig:

- Installeer [Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) met de **Azure-ontwikkelworkload.**

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- Een opslagaccount voor algemene doeleinden. Zie [Een opslagaccount maken](storage-account-create.md)als u nog geen opslagaccount hebt.

- In het voorbeeld in dit artikel ziet u hoe u de containers in een opslagaccount weergeven. Als u de uitvoer wilt weergeven, voegt u enkele containers toe aan blobopslag in het opslagaccount voordat u begint.

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

De voorbeeldtoepassing is een consoletoepassing die is geschreven in C#.

Gebruik [git](https://git-scm.com/) om een kopie van de toepassing naar uw ontwikkelomgeving te downloaden.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Met deze opdracht wordt de opslagplaats naar uw lokale git-map gekloond. Als u de Visual Studio-oplossing wilt openen, zoekt u naar de map storage-dotnet-rest-api-with-auth, opent u deze en dubbelklikt u op StorageRestApiAuth.sln.

## <a name="about-rest"></a>Over REST

REST staat voor *representatieve staatsoverdracht*. Voor een specifieke definitie, kijk op [Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer).

REST is een architectuur waarmee u via een internetprotocol, zoals HTTP/HTTPS, met een service communiceren. REST is onafhankelijk van de software die op de server of de client wordt uitgevoerd. De REST API kan worden aangeroepen vanaf elk platform dat HTTP/HTTPS ondersteunt. U een toepassing schrijven die wordt uitgevoerd op een Mac, Windows, Linux, een Android-telefoon of -tablet, iPhone, iPod of website, en dezelfde REST-API gebruiken voor al deze platforms.

Een aanroep naar de REST API bestaat uit een aanvraag, die wordt gedaan door de client, en een antwoord, dat wordt geretourneerd door de service. In het verzoek stuurt u een URL met informatie over welke bewerking u wilt aanroepen, de resource waarop u moet reageren, eventuele queryparameters en kopteksten en afhankelijk van de bewerking die is aangeroepen, een payload van gegevens. Het antwoord van de service bevat een statuscode, een set antwoordkoppen en afhankelijk van de bewerking die is aangeroepen, een payload van gegevens.

## <a name="about-the-sample-application"></a>Informatie over de voorbeeldtoepassing

De voorbeeldtoepassing geeft een lijst van de containers in een opslagaccount. Zodra u begrijpt hoe de informatie in de REST API-documentatie correleert met uw werkelijke code, zijn andere REST-oproepen gemakkelijker te achterhalen.

Als u de [Blob Service REST API](/rest/api/storageservices/Blob-Service-REST-API)bekijkt, ziet u alle bewerkingen die u uitvoeren op blob-opslag. De opslagclientbibliotheken zijn wikkels rond de REST API's – ze maken het gemakkelijk voor u om toegang te krijgen tot opslag zonder de REST API's rechtstreeks te gebruiken. Maar zoals hierboven vermeld, wilt u soms de REST-API gebruiken in plaats van een opslagclientbibliotheek.

## <a name="list-containers-operation"></a>Containerbewerking weergeven

Bekijk de referentie voor de [bewerking ListContainers.](/rest/api/storageservices/List-Containers2) Deze informatie helpt u te begrijpen waar sommige velden vandaan komen in de aanvraag en het antwoord.

**Aanvraagmethode:** GET. Dit werkwoord is de HTTP-methode die u opgeeft als eigenschap van het aanvraagobject. Andere waarden voor dit werkwoord zijn HEAD, PUT en DELETE, afhankelijk van de API die u aanroept.

**Vraag URI:** `https://myaccount.blob.core.windows.net/?comp=list`.De aanvraag URI is gemaakt van `http://myaccount.blob.core.windows.net` het blob-opslagaccounteindpunt en de resourcetekenreeks `/?comp=list`.

[URI-parameters:](/rest/api/storageservices/List-Containers2#uri-parameters)er zijn aanvullende queryparameters die u gebruiken bij het aanroepen van ListContainers. Een paar van deze parameters zijn *time-out* voor de oproep (in seconden) en *voorvoegsel*, die wordt gebruikt voor het filteren.

Een andere nuttige parameter is *maxresults:* als er meer containers beschikbaar zijn dan deze waarde, bevat de antwoordgroep een *NextMarker-element* dat aangeeft dat de volgende container moet terugkeren op de volgende aanvraag. Als u deze functie wilt gebruiken, geeft u de *nextmarkerwaarde* op als *de markeringsparameter* in de URI wanneer u het volgende verzoek indient. Bij het gebruik van deze functie, is het analoog aan paging door de resultaten.

Als u extra parameters wilt gebruiken, wordt deze toegevoegd aan de resourcetekenreeks met de waarde, zoals dit voorbeeld:

```
/?comp=list&timeout=60&maxresults=100
```

[Kopteksten aanvragen:](/rest/api/storageservices/List-Containers2#request-headers)**:** in deze sectie worden de vereiste en optionele aanvraagkoppen weergegeven. Er zijn drie van de *Authorization* kopteksten vereist: een autorisatiekoptekst, *x-ms-datum* (bevat de UTC-tijd voor de aanvraag) en *x-ms-versie* (geeft de te gebruiken versie van de REST API aan). Het opnemen van *x-ms-client-request-id* in de headers is optioneel - u de waarde voor dit veld op alles instellen; het wordt geschreven naar de opslaganalyseslogboeken wanneer logboekregistratie is ingeschakeld.

[Aanvraag body](/rest/api/storageservices/List-Containers2#request-body)**:** Er is geen aanvraag body voor ListContainers. Aanvraagbody wordt gebruikt voor alle PUT-bewerkingen bij het uploaden van blobs, evenals SetContainerAccessPolicy, waarmee u een XML-lijst met opgeslagen toegangsbeleidsregels verzenden om toe te passen. Beleid voor opgeslagen toegang wordt besproken in het artikel [Met Behulp van Gedeelde toegangshandtekeningen (SAS)](storage-sas-overview.md).

[Statuscode voor](/rest/api/storageservices/List-Containers2#status-code)**antwoord:** vertelt over eventuele statuscodes die u moet weten. In dit voorbeeld is een HTTP-statuscode van 200 ok. Voor een volledige lijst met HTTP-statuscodes raadpleegt u [definities van statuscode](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html). Zie [Common REST API-foutcodes](/rest/api/storageservices/common-rest-api-error-codes) voor foutcodes die specifiek zijn voor de API's voor opslagrest als u foutcodes voor opslagrest wilt weergeven

[Reactiekoppen](/rest/api/storageservices/List-Containers2#response-headers)**:** Deze omvatten *inhoudstype*; *x-ms-request-id,* dat is de aanvraag-ID die u hebt doorgegeven; *x-ms-versie*, die de versie van de gebruikte Blob-service aangeeft; en de *datum*, die in UTC en vertelt hoe laat het verzoek is gemaakt.

[Antwoordstructuur:](/rest/api/storageservices/List-Containers2#response-body)dit veld is een XML-structuur die de gevraagde gegevens verstrekt. In dit voorbeeld is het antwoord een lijst met containers en hun eigenschappen.

## <a name="creating-the-rest-request"></a>Het REST-verzoek maken

Voor beveiliging bij het uitvoeren in productie, altijd https gebruiken in plaats van HTTP. Voor deze oefening moet u HTTP gebruiken, zodat u de aanvraag- en antwoordgegevens bekijken. Als u de aanvraag- en antwoordgegevens wilt bekijken in de werkelijke REST-oproepen, u [Fiddler](https://www.telerik.com/fiddler) of een soortgelijke toepassing downloaden. In de Visual Studio-oplossing worden de naam en de sleutel van het opslagaccount hardcoded in de klasse. De methode ListContainersAsyncREST geeft de naam en opslagaccountsleutel door aan de methoden die worden gebruikt om de verschillende onderdelen van de REST-aanvraag te maken. In een echte toepassing bevinden de naam en sleutel van het opslagaccount zich in een configuratiebestand, omgevingsvariabelen of worden opgehaald uit een Azure Key Vault.

In ons voorbeeldproject bevindt de code voor het maken van de kopautorisatie zich in een afzonderlijke klasse. Het idee is dat je de hele klas zou kunnen nemen en het toevoegen aan je eigen oplossing en het gebruiken "zoals het is." De autorisatiekopcode werkt voor de meeste REST API-aanroepen naar Azure Storage.

Ga naar ListContainersAsyncREST in Program.cs om de aanvraag, een httprequestberichtobject, te maken. De stappen voor het bouwen van de aanvraag zijn:

- Maak de URI die moet worden gebruikt voor het aanroepen van de service.
- Maak het object HttpRequestMessage en stel de payload in. De payload is null voor ListContainersAsyncREST omdat we niets doorgeven.
- Voeg de aanvraagkoppen voor x-ms-datum en x-ms-versie toe.
- Haal de autorisatiekoptekst op en voeg deze toe.

Enkele basisinformatie die u nodig hebt:

- Voor ListContainers **method** is `GET`de methode . Deze waarde wordt ingesteld bij het momentmaken van de aanvraag.
- De **resource** is het querygedeelte van de URI dat aangeeft `/?comp=list`welke API wordt aangeroepen, dus de waarde is . Zoals eerder vermeld, staat de bron op de referentiedocumentatiepagina met de informatie over de [API ListContainers.](/rest/api/storageservices/List-Containers2)
- De URI wordt geconstrueerd door het eindpunt van de Blob-service voor dat opslagaccount te maken en de resource samen te brengen. De waarde voor **aanvraag** `http://contosorest.blob.core.windows.net/?comp=list`URI eindigt op .
- Voor ListContainers is **requestBody** null en zijn er geen extra **headers.**

Verschillende API's kunnen andere parameters hebben om in door te geven, zoals *ifMatch*. Een voorbeeld van waar u ifMatch gebruiken wanneer u PutBlob aanroept. In dat geval stelt u ifMatch in op een eTag en wordt de blob alleen bijgewerkt als de eTag die u opgeeft overeenkomt met de huidige eTag op de blob. Als iemand anders de blob heeft bijgewerkt sinds het ophalen van de eTag, wordt de wijziging niet overschreven.

Ten eerste, `uri` stel `payload`de en de .

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Vervolgens u de aanvraag instantiëren `GET` en de methode instellen op en de URI leveren.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Voeg de aanvraagkoppen `x-ms-date` `x-ms-version`toe voor en . Deze plaats in de code is ook waar u eventuele extra aanvraag headers die nodig zijn voor het gesprek toe te voegen. In dit voorbeeld zijn er geen extra kopteksten. Een voorbeeld van een API die doorloopt in extra headers is de ACL-bewerking Set Container. Deze API-aanroep voegt een koptekst toe met de naam "x-ms-blob-public-access" en de waarde voor het toegangsniveau.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

Roep de methode aan waarmee de autorisatiekoptekst wordt gemaakt en voeg deze toe aan de aanvraagkoppen. U ziet later in het artikel hoe u de autorisatiekop tekst maakt. De methodenaam is GetAuthorizationHeader, die u zien in dit codefragment:

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

Op dit `httpRequestMessage` punt bevat u de REST-aanvraag, compleet met de autorisatiekoppen.

## <a name="send-the-request"></a>De aanvraag verzenden

Nu u de aanvraag hebt gemaakt, u de SendAsync-methode aanroepen om deze naar Azure Storage te verzenden. Controleer of de waarde van de statuscode 200 is, wat betekent dat de bewerking is geslaagd. Ontlijn vervolgens het antwoord. In dit geval krijgt u een XML-lijst met containers. Laten we eens kijken naar de code voor het aanroepen van de methode GetRESTRequest om de aanvraag te maken, de aanvraag uit te voeren en vervolgens het antwoord voor de lijst met containers te onderzoeken.

```csharp
    // Send the request.
    using (HttpResponseMessage httpResponseMessage =
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200),
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Als u een netwerksniffer zoals [Fiddler](https://www.telerik.com/fiddler) uitvoert wanneer u naar SendAsync belt, u de aanvraag- en antwoordgegevens zien. We gaan eens kijken. De naam van de opslagrekening is *contosorest.*

**Verzoek:**

```
GET /?comp=list HTTP/1.1
```

**Kopteksten aanvragen:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Statuscode en antwoordkoppen die na uitvoering zijn geretourneerd:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Antwoordbody (XML):** Voor de bewerking Lijstcontainers wordt de lijst met containers en hun eigenschappen weergegeven.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Nu u begrijpt hoe u de aanvraag maakt, de service belt en de resultaten ontdoet, laten we eens kijken hoe u de autorisatiekoptekst maakt. Het maken van die header is ingewikkeld, maar het goede nieuws is dat als je eenmaal de code werkt, het werkt voor alle API's van de opslagservice.

## <a name="creating-the-authorization-header"></a>De autorisatiekoptekst maken

> [!TIP]
> Azure Storage ondersteunt nu Azure Active Directory-integratie (Azure AD) voor blobs en wachtrijen. Azure AD biedt een veel eenvoudigere ervaring voor het autoriseren van een aanvraag voor Azure Storage. Zie Toestaan met Azure Active Directory voor meer informatie over het gebruik van Azure AD om REST-bewerkingen te [autoriseren.](/rest/api/storageservices/authorize-with-azure-active-directory) Zie Toegang tot Azure Storage verifiëren [met Azure Storage met Azure Active Directory](storage-auth-aad.md)voor een overzicht van azure AD-integratie met Azure Storage.

Er is een artikel waarin conceptueel (geen code) wordt uitgelegd hoe [u aanvragen voor Azure Storage autoriseren.](/rest/api/storageservices/authorize-requests-to-azure-storage)

Laten we destilleren dat artikel tot precies nodig is en tonen de code.

Gebruik eerst de machtiging Gedeelde sleutel. De indeling autorisatiekoptekst ziet er als volgt uit:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Het handtekeningveld is een Op hash gebaseerde Message Authentication Code (HMAC) die is gemaakt op basis van de aanvraag en wordt berekend met behulp van het SHA256-algoritme en vervolgens gecodeerd met Base64-codering. Heb je dat? (Hang daar, je hebt niet eens gehoord van het woord *heilig getint* nog niet.)

In dit codefragment wordt de indeling van de handtekeningtekenreeks Gedeelde sleutel weergegeven:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

De meeste van deze velden worden zelden gebruikt. Voor Blob-opslag geeft u WERKWOORD, md5, inhoudslengte, Canonicalized Headers en Canonicalized Resource op. U de anderen leeg (maar zet in de `\n` zodat het weet dat ze leeg zijn).

Wat zijn CanonicalizedHeaders en CanonicalizedResource? Goede vraag. In feite, wat betekent canonicalized? Microsoft Word herkent het niet eens als een woord. Hier is wat [Wikipedia zegt over canonicalisatie:](https://en.wikipedia.org/wiki/Canonicalization) *In de informatica, canonicalisatie (soms standaardisatie of normalisatie) is een proces voor het omzetten van gegevens die meer dan een mogelijke vertegenwoordiging in een "standaard", "normale", of canonieke vorm heeft.* In normaal-spreken, betekent dit om de lijst van punten (zoals headers in het geval van Canonicalized Headers) te nemen en hen in een vereist formaat te standaardiseren. Kortom, Microsoft besloten op een formaat en je moet overeenkomen.

Laten we beginnen met die twee canonicalized velden, omdat ze nodig zijn om de koptekst Autorisatie te maken.

### <a name="canonicalized-headers"></a>Canonicalized headers

Als u deze waarde wilt maken, haalt u de kopteksten op die beginnen `[key:value\n]` met "x-ms-" en sorteert u ze en maakt u ze op in een reeks instanties, samengevoegd tot één tekenreeks. In dit voorbeeld zien de canonicalized headers er als volgt uit:

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Hier is de code die wordt gebruikt om die uitvoer te maken:

```csharp
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
        where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
        orderby kvp.Key
        select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

### <a name="canonicalized-resource"></a>Canonicalized resource

Dit deel van de tekenreeks voor handtekeningen vertegenwoordigt het opslagaccount waarop de aanvraag is gericht. Vergeet niet dat `<http://contosorest.blob.core.windows.net/?comp=list>`de aanvraag URI is`contosorest` , met de werkelijke accountnaam (in dit geval). In dit voorbeeld wordt dit geretourneerd:

```
/contosorest/\ncomp:list
```

Als u queryparameters hebt, bevat dit voorbeeld ook deze parameters. Hier is de code, die ook extra queryparameters en queryparameters met meerdere waarden verwerkt. Vergeet niet dat je deze code bouwt om te werken voor alle REST API's. U wilt alle mogelijkheden opnemen, zelfs als de ListContainers-methode ze niet allemaal nodig heeft.

```csharp
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item.ToLower()).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Nu de canonicalized strings zijn ingesteld, laten we eens kijken hoe de autorisatie header zelf te maken. U begint met het maken van een tekenreeks van de berichthandtekening in de indeling van StringToSign die eerder in dit artikel werd weergegeven. Dit concept is gemakkelijker uit te leggen met behulp van opmerkingen in de code, dus hier is het, de laatste methode die de autorisatie koptekst retourneert:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + signature);
    return authHV;
}
```

Wanneer u deze code uitvoert, ziet de resulterende MessageSignature er als volgt uit:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Hier is de uiteindelijke waarde voor AuthorizationHeader:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

De AuthorizationHeader is de laatste koptekst die in de aanvraagkoppen wordt geplaatst voordat het antwoord wordt geplaatst.

Dat dekt alles wat u moet weten om een klasse samen te stellen waarmee u een verzoek maken om de API's voor opslagservices te bellen.

## <a name="example-list-blobs"></a>Voorbeeld: Blobs weergeven

Laten we eens kijken hoe u de code wijzigen om de activiteit Lijstblobs voor *containercontainer-1*aan te roepen. Deze code is bijna identiek aan de code voor het aanbieden van containers, de enige verschillen zijn de URI en hoe u de reactie onteert.

Als u de referentiedocumentatie voor [ListBlobs](/rest/api/storageservices/List-Blobs)bekijkt, ziet u dat de methode *GET* is en dat de RequestURI:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

Wijzig in ListContainersAsyncREST de code waarmee de URI wordt ingesteld op de API voor ListBlobs. De containernaam is **container-1**.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Waar u vervolgens het antwoord verwerkt, wijzigt u de code om blobs te zoeken in plaats van containers.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Wanneer u dit voorbeeld uitvoert, krijgt u resultaten zoals het volgende:

**Canonicalized headers:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Canonicalized resource:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**Berichthandtekening:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**Autorisatiekoptekst:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

De volgende waarden zijn van [Fiddler:](https://www.telerik.com/fiddler)

**Verzoek:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Kopteksten aanvragen:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Statuscode en antwoordkoppen die na uitvoering zijn geretourneerd:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Antwoordbody (XML):** Met dit XML-antwoord wordt de lijst met blobs en hun eigenschappen weergegeven.

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Samenvatting

In dit artikel hebt u geleerd hoe u een aanvraag indienen voor de REST API voor blobopslagopslag. Met de aanvraag u een lijst met containers of een lijst met blobs in een container ophalen. U hebt geleerd hoe u de autorisatiehandtekening voor de REST API-aanroep maakt en hoe u deze gebruiken in de REST-aanvraag. Ten slotte heb je geleerd hoe je de reactie te onderzoeken.

## <a name="next-steps"></a>Volgende stappen

- [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api)
- [Bestandsservice REST API](/rest/api/storageservices/file-service-rest-api)
- [Wachtrijservice REST API](/rest/api/storageservices/queue-service-rest-api)
- [Tabelservice REST API](/rest/api/storageservices/table-service-rest-api)
