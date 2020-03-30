---
title: Hoe Twilio te gebruiken voor spraak en sms (.NET) | Microsoft Documenten
description: Meer informatie over het voeren van een telefoongesprek en het verzenden van een sms-bericht met de Twilio API-service op Azure. Codevoorbeelden geschreven in .NET.
services: ''
documentationcenter: .net
author: georgewallace
ms.assetid: 74d4f3c9-f1cb-4968-b744-36b32cd0e834
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/24/2015
ms.author: gwallace
ms.openlocfilehash: 22b33d7b4b0ff69a2e751cadff70453f73ed4f8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69876808"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-from-azure"></a>Twilio gebruiken voor spraak- en sms-mogelijkheden vanuit Azure
Deze handleiding laat zien hoe u veelvoorkomende programmeertaken uitvoeren met de Twilio API-service op Azure. De scenario's die onder meer het maken van een telefoontje en het verzenden van een Short Message Service (SMS) bericht. Zie de sectie [Volgende stappen](#NextSteps) voor meer informatie over Twilio en het gebruik van spraak en sms in uw toepassingen.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Wat is Twilio?
Twilio is het aandrijven van de toekomst van zakelijke communicatie, waardoor ontwikkelaars spraak, VoIP en messaging in te sluiten in toepassingen. Ze virtualiseren alle infrastructuur die nodig is in een cloudgebaseerde, wereldwijde omgeving, waardoor deze wordt blootgelegd via het Twilio communications API-platform. Toepassingen zijn eenvoudig te bouwen en schaalbaar. Profiteer van flexibiliteit met betalen per gebruik en profiteer van de betrouwbaarheid van de cloud.

**Met Twilio Voice** kunnen uw toepassingen telefoongesprekken voeren en ontvangen. **Met Twilio SMS** kunnen uw applicaties sms-berichten versturen en ontvangen. **Met Twilio Client** u VoIP-gesprekken voeren vanaf elke telefoon, tablet of browser en webrtc ondersteunen.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio Prijzen en speciale aanbiedingen
Azure-klanten ontvangen een [speciale aanbieding:](https://www.twilio.com/azure)gratis $ 10 van Twilio-tegoed wanneer u uw Twilio-account upgradet. Dit Twilio-krediet kan worden toegepast op elk Twilio-gebruik ($ 10-tegoed dat overeenkomt met het verzenden van maar liefst 1.000 sms-berichten of het ontvangen van maximaal 1000 binnenkomende spraakminuten, afhankelijk van de locatie van uw telefoonnummer en bericht of oproepbestemming). Wissel dit Twilio-tegoed in en ga aan de slag bij [twilio.com/azure](https://twilio.com/azure).

Twilio is een pay-as-you-go service. Er zijn geen set-up kosten, en u uw account te sluiten op elk gewenst moment. Meer informatie vindt u bij [Twilio Pricing.](https://www.twilio.com/voice/pricing)

## <a name="concepts"></a><a id="Concepts"></a>Concepten
De Twilio API is een RESTful API die spraak- en sms-functionaliteit biedt voor toepassingen. Clientbibliotheken zijn beschikbaar in meerdere talen; zie [Twilio API-bibliotheken][twilio_libraries]voor een lijst .

Belangrijke aspecten van de Twilio API zijn Twilio werkwoorden en Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio werkwoorden
De API maakt gebruik van Twilio werkwoorden; Het ** &lt;werkwoord&gt; Zeg** instrueert Twilio bijvoorbeeld om hoorbaar een bericht af te leveren tijdens een oproep.

Het volgende is een lijst van Twilio werkwoorden.  Meer informatie over de andere werkwoorden en mogelijkheden via [Twilio Markup Language documentatie](https://www.twilio.com/docs/api/twiml).

* `<Dial>`: Verbindt de beller met een andere telefoon.
* `<Gather>`: Verzamelt numerieke cijfers die op het telefoontoetsenbord zijn ingevoerd.
* `<Hangup>`: Beëindigt een gesprek.
* `<Play>`: Hiermee wordt een audiobestand afgespeeld.
* `<Pause>`: Wacht stil voor een bepaald aantal seconden.
* `<Record>`: Registreert de stem van de beller en retourneert een URL van een bestand dat de opname bevat.
* `<Redirect>`: Overdrachten controle van een oproep of SMS naar de TwiML op een andere URL.
* `<Reject>`: Verwerpt een inkomende oproep naar uw Twilio-nummer zonder u te factureren
* `<Say>`: Converteert tekst naar spraak die wordt gemaakt tijdens een gesprek.
* `<Sms>`: Stuurt een sms-bericht.

### <a name="twiml"></a>TwiML
TwiML is een set xml-gebaseerde instructies op basis van de Twilio-werkwoorden die Twilio informeren over het verwerken van een oproep of sms.

Als voorbeeld zou de volgende TwiML de tekst **Hello World** omzetten in spraak.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Response>
  <Say>Hello World</Say>
</Response>
```

Wanneer uw toepassing de Twilio-API aanroept, is een van de API-parameters de URL die het TwiML-antwoord retourneert. Voor ontwikkelingsdoeleinden u door Twilio verstrekte URL's gebruiken om de TwiML-antwoorden te leveren die door uw toepassingen worden gebruikt. U ook uw eigen URL's hosten om de TwiML-antwoorden te produceren, en een andere optie is het gebruik van het **TwiMLResponse-object.**

Zie [TwiML][twiml]voor meer informatie over Twilio-werkwoorden, hun kenmerken en TwiML. Zie [Twilio API][twilio_api]voor meer informatie over de Twilio API.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Een Twilio-account maken
Als je klaar bent om een Twilio-account te krijgen, meld je dan aan bij [Try Twilio][try_twilio]. Je beginnen met een gratis account en je account later upgraden.

Wanneer u zich aanmeldt voor een Twilio-account, ontvangt u een account-id en een verificatietoken. Beide zullen nodig zijn om Twilio API-aanroepen te maken. Om ongeautoriseerde toegang tot uw account te voorkomen, houdt u uw verificatietoken veilig. Uw account-ID en verificatietoken zijn zichtbaar op de [twilio-accountpagina][twilio_account], respectievelijk in de velden met het label **ACCOUNT SID** en **AUTH TOKEN.**

## <a name="create-an-azure-application"></a><a id="create_app"></a>Een Azure-toepassing maken
Een Azure-toepassing die een twilio-toepassing host, verschilt niet van elke andere Azure-toepassing. U voegt de Twilio .NET-bibliotheek toe en configureert de rol voor het gebruik van de Twilio .NET-bibliotheken.
Zie [Een Azure-project maken met Visual Studio][vs_project]voor informatie over het maken van een eerste Azure-project.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Uw toepassing configureren om Twilio-bibliotheken te gebruiken
Twilio biedt een set .NET helperbibliotheken die verschillende aspecten van Twilio verpakken om eenvoudige en eenvoudige manieren te bieden om te communiceren met de Twilio REST API en Twilio Client om TwiML-reacties te genereren.

Twilio biedt vijf bibliotheken voor .NET-ontwikkelaars:

| Bibliotheek | Beschrijving |
| --- | --- |
| Twilio.API | De kern-Twilio-bibliotheek die de Twilio REST API omsluit in een vriendelijke .NET-bibliotheek. Deze bibliotheek is beschikbaar voor .NET, Silverlight en Windows Phone 7. |
| Twilio.TwiML | Biedt een .NET-vriendelijke manier om TwiML-markeringen te genereren. |
| Twilio.MVC | Voor ontwikkelaars die ASP.NET MVC gebruiken, bevat deze bibliotheek een TwilioController, TwiML ActionResult en het validatiekenmerk van aanvragen. |
| Twilio.WebMatrix | Voor ontwikkelaars die de gratis WebMatrix-ontwikkelingstool van Microsoft gebruiken, bevat deze bibliotheek Razor-syntaxhelpers voor verschillende Twilio-acties. |
| Twilio.Client.Capability | Bevat de Capability token generator voor gebruik met de Twilio Client JavaScript SDK. |

> [!Important]
> Alle bibliotheken vereisen .NET 3.5, Silverlight 4 of Windows Phone 7 of hoger.

De voorbeelden in deze handleiding maken gebruik van de Twilio.API-bibliotheek.

De bibliotheken kunnen worden [geïnstalleerd met behulp van de NuGet package manager extensie](https://www.twilio.com/docs/csharp/install) beschikbaar voor Visual Studio 2010 tot 2015.  De broncode wordt gehost op [GitHub][twilio_github_repo], die een Wiki bevat die volledige documentatie bevat voor het gebruik van de bibliotheken.

Microsoft Visual Studio 2010 installeert standaard versie 1.2 van NuGet. Het installeren van de Twilio-bibliotheken vereist versie 1.6 van NuGet of hoger. Zie [https://nuget.org/][nuget]voor informatie over het installeren of bijwerken van NuGet .

> [!NOTE]
> Als u de nieuwste versie van NuGet wilt installeren, moet u eerst de geladen versie verwijderen met behulp van Visual Studio Extension Manager. Hiervoor moet u Visual Studio als beheerder uitvoeren. Anders is de knop Verwijderen uitgeschakeld.
>
>

### <a name="to-add-the-twilio-libraries-to-your-visual-studio-project"></a><a id="use_nuget"></a>Ga als het gaat om de Twilio-bibliotheken toe te voegen aan uw Visual Studio-project:
1. Open uw oplossing in Visual Studio.
2. Klik met de rechtermuisknop **op Verwijzingen**.
3. Klik **op NuGet-pakketten beheren...**
4. Klik **op Online**.
5. Typ *twilio*in het online zoekvak .
6. Klik **op Installeren** op het Twilio-pakket.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Hoe: Een uitgaande oproep voeren
Hieronder ziet u hoe u een uitgaande oproep voeren met de klasse **CallResource.** Deze code maakt ook gebruik van een door Twilio geleverde site om de Twilio Markup Language (TwiML) reactie terug te geven. Vervang uw waarden voor de telefoonnummers **van** en **naar** en met telefoonnummers en zorg ervoor dat u het **telefoonnummer van** uw Twilio-account controleert voordat u de code uitvoert.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

// Use the Twilio-provided site for the TwiML response.
var url = "https://twimlets.com/message";
url = $"{url}?Message%5B0%5D=Hello%20World";

// Set the call From, To, and URL values to use for the call.
// This sample uses the sandbox number provided by
// Twilio to make the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri(url));
    }
```

Zie voor meer informatie over de parameters die zijn [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]doorgegeven aan de methode **CallResource.Create** .

Zoals gezegd maakt deze code gebruik van een door Twilio geleverde site om de TwiML-respons terug te sturen. U in plaats daarvan uw eigen site gebruiken om de TwiML-respons te bieden. Zie Voor meer informatie [Hoe: TwiML-antwoorden van uw eigen website](#howto_provide_twiml_responses)verstrekken.

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Hoe: Een sms-bericht verzenden
In de volgende schermafbeelding ziet u hoe u een sms-bericht verzendt met de klasse **MessageResource.** Het **van** nummer wordt door Twilio geleverd voor proefaccounts om sms-berichten te verzenden. Het **nummer moet** worden geverifieerd voor uw Twilio-account voordat u de code uitvoert.

```csharp
// Use your account SID and authentication token instead
// of the placeholders shown here.
const string accountSID = "your_twilio_account";
const string authToken = "your_twilio_authentication_token";

// Initialize the TwilioClient.
TwilioClient.Init(accountSID, authToken);

try
{
    // Send an SMS message.
    var message = MessageResource.Create(
        to: new PhoneNumber("+12069419717"),
        from: new PhoneNumber("+14155992671"),
        body: "This is my SMS message.");
}
catch (TwilioException ex)
{
    // An exception occurred making the REST call
    Console.WriteLine(ex.Message);
}
```

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>How to: TwiML Reacties van uw eigen website
Wanneer uw toepassing een aanroep naar de Twilio API initieert - bijvoorbeeld via de **CallResource.Create-methode** - stuurt Twilio uw aanvraag naar een URL die naar verwachting een TwiML-antwoord retourneert. Het voorbeeld in [Hoe: Een uitgaande oproep voeren,](#howto_make_call) [https://twimlets.com/message][twimlet_message_url] gebruikt de door Twilio verstrekte URL om het antwoord terug te sturen.

> [!NOTE]
> Hoewel TwiML is ontworpen voor gebruik door webservices, u de TwiML in uw browser bekijken. Klik bijvoorbeeld [https://twimlets.com/message][twimlet_message_url] om een `<Response>` leeg element te zien; klik als een [https://twimlets.com/message?Message%5B0%5D=Hello%20World](https://twimlets.com/message?Message%5B0%5D=Hello%20World) ander `<Response>` voorbeeld om &lt;een&gt; element te zien dat een element Weergeven bevat.
>

In plaats van te vertrouwen op de door Twilio verstrekte URL, u uw eigen URL-site maken die HTTP-antwoorden retourneert. U de site maken in elke taal die HTTP-antwoorden retourneert. In dit onderwerp wordt ervan uitgegaan dat u de URL host van een ASP.NET generieke handler.

De volgende ASP.NET Handler maakt een TwiML-reactie die **Hello World** op de oproep zegt.

```csharp
using System.Text;
using System.Web;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {
        public void ProcessRequest(HttpContext context)
        {
            const string twiMLResponse =
                "<Response><Say>Hello World.</Say></Response>";

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.ContentEncoding = Encoding.UTF8;
            context.Response.Write(twiMLResponse);
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```
    
Zoals u zien in het bovenstaande voorbeeld, is het TwiML-antwoord gewoon een XML-document. De Twilio.TwiML-bibliotheek bevat lessen die TwiML voor u genereren. In het onderstaande voorbeeld wordt de equivalente respons weergegeven, maar wordt de klasse **VoiceResponse** gebruikt.

```csharp
using System.Web;
using Twilio.TwiML;

namespace WebRole1
{
    /// <summary>
    /// Summary description for Handler1
    /// </summary>
    public class Handler1 : IHttpHandler
    {

        public void ProcessRequest(HttpContext context)
        {
            var twiml = new VoiceResponse();
            twiml.Say("Hello World.");

            context.Response.Clear();
            context.Response.ContentType = "text/xml";
            context.Response.Write(twiml.ToString());
            context.Response.End();
        }

        public bool IsReusable
        {
            get
            {
                return false;
            }
        }
    }
}
```

Zie voor meer informatie over [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml)TwiML .

Zodra u een manier hebt ingesteld om TwiML-antwoorden te geven, u die URL doorgeven aan de methode **CallResource.Create.** Als u bijvoorbeeld een webtoepassing met de naam MyTwiML hebt geïmplementeerd op een Azure-cloudservice en de naam van uw ASP.NET Handler mytwiml.ashx is, kan de URL worden doorgegeven aan **CallResource.Create** zoals weergegeven in het volgende codevoorbeeld:

```csharp
// This sample uses the sandbox number provided by Twilio to make the call.
// Place the call.
var call = CallResource.Create(
    to: new PhoneNumber("+NNNNNNNNNN"),
    from: new PhoneNumber("NNNNNNNNNN"),
    url: new Uri("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx"));
    }
```

Zie [Een telefoongesprek voeren met Twilio in een webrol op Azure][howto_phonecall_dotnet]voor meer informatie over het gebruik van Twilio op Azure met ASP.NET.

[!INCLUDE [twilio-additional-services-and-next-steps](../includes/twilio-additional-services-and-next-steps.md)]

[howto_phonecall_dotnet]: partner-twilio-cloud-services-dotnet-phone-call-web-role.md

[twimlet_message_url]: https://twimlets.com/message

[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls

[vs_project]:https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-azure-project-create
[nuget]:https://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
