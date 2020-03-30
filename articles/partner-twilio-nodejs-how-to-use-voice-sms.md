---
title: Twilio gebruiken voor spraak-, VoIP- en sms-berichten in Azure
description: Meer informatie over het voeren van een telefoongesprek en het verzenden van een sms-bericht met de Twilio API-service op Azure. Code samples geschreven in Node.js.
services: ''
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 164bedffcf9a1aca9f1fa46dea254fb928abcf04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637274"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Twilio gebruiken voor spraak-, VoIP- en sms-berichten in Azure
In deze handleiding wordt uitgelegd hoe u apps bouwen die communiceren met Twilio en node.js op Azure.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Wat is Twilio?
Twilio is een API-platform dat het voor ontwikkelaars gemakkelijk maakt om telefoongesprekken te voeren en te ontvangen, tekstberichten te verzenden en te ontvangen en VoIP-aanroepen in browsergebaseerde en native mobiele toepassingen in te sluiten. Laten we kort gaan over hoe dit werkt voordat duiken in.

### <a name="receiving-calls-and-text-messages"></a>Oproepen en sms-berichten ontvangen
Twilio stelt ontwikkelaars in staat om [programmeerbare telefoonnummers][purchase_phone] te kopen die kunnen worden gebruikt om zowel oproepen als sms-berichten te verzenden en te ontvangen. Wanneer een Twilio-nummer een binnenkomend gesprek of sms ontvangt, stuurt Twilio uw webapplicatie een HTTP POST- of GET-verzoek, waarin u wordt gevraagd om instructies over hoe u de oproep of tekst moet afhandelen. Uw server reageert op het HTTP-verzoek van Twilio met [TwiML,][twiml]een eenvoudige set XML-tags die instructies bevatten over het afhandelen van een oproep of tekst. We zullen voorbeelden van TwiML in slechts een moment zien.

### <a name="making-calls-and-sending-text-messages"></a>Bellen en sms-berichten verzenden
Door HTTP-verzoeken in te dienen bij de Twilio webservice-API, kunnen ontwikkelaars tekstberichten verzenden of uitgaande telefoongesprekken starten. Voor uitgaande gesprekken moet de ontwikkelaar ook een URL opgeven die TwiML-instructies retourneert voor het afhandelen van het uitgaande gesprek zodra deze is verbonden.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>VoIP-mogelijkheden insluiten in ui-code (JavaScript, iOS of Android)
Twilio biedt een SDK aan de clientzijde die elke desktopwebbrowser, iOS-app of Android-app kan veranderen in een VoIP-telefoon. In dit artikel zullen we ons richten op het gebruik van VoIP-bellen in de browser. Naast de *Twilio JavaScript SDK* die in de browser wordt uitgevoerd, moet een server-side applicatie (onze node.js-toepassing) worden gebruikt om een "capability token" uit te geven aan de JavaScript-client. U meer lezen over het gebruik van VoIP met node.js [op de Twilio dev blog][voipnode].

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Aanmelden voor Twilio (Microsoft Korting)
Voordat u Twilio-services gebruikt, moet u zich eerst [aanmelden voor een account.][signup] Microsoft Azure-klanten ontvangen een speciale korting - [zorg ervoor dat u zich hier aanmeldt!][signup]

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>Een Azure-website van node.js maken en implementeren
Vervolgens moet u een node.js-website maken die op Azure wordt uitgevoerd. [De officiële documentatie om dit te doen is hier te vinden.][azure_new_site] Op een hoog niveau, zult u het volgende doen:

* Aanmelden voor een Azure-account, als u er nog geen hebt
* De Azure-beheerconsole gebruiken om een nieuwe website te maken
* Ondersteuning voor bronbeheer toevoegen (we gaan ervan uit dat u git hebt gebruikt)
* Een bestand `server.js` maken met een eenvoudige node.js-webtoepassing
* Implementeren van deze eenvoudige toepassing naar Azure

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>De Twilio-module configureren
Vervolgens zullen we beginnen met het schrijven van een eenvoudige node.js applicatie die gebruik maakt van de Twilio API. Voordat we beginnen, moeten we onze Twilio-accountreferenties configureren.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>Twilio-referenties configureren in variabelen voor systeemomgeving
Om geverifieerde verzoeken te doen tegen de Twilio back-end, hebben we ons account SID en auth token nodig, die fungeren als de gebruikersnaam en wachtwoordset voor ons Twilio-account. De veiligste manier om deze te configureren voor gebruik met de knooppuntmodule in Azure is via systeemomgevingsvariabelen, die u rechtstreeks in de Azure-beheerconsole instellen.

Selecteer uw node.js-website en klik op de koppeling CONFIGUREREN.  Als u een beetje naar beneden scrolt, ziet u een gebied waar u configuratie-eigenschappen voor uw toepassing instellen.  Voer uw Twilio-accountreferenties in ([gevonden op uw Twilio][twilio_console] `TWILIO_ACCOUNT_SID` Console `TWILIO_AUTH_TOKEN`) zoals weergegeven - zorg ervoor dat u ze een naam geeft en , respectievelijk:

![Azure-beheerconsole][azure-admin-console]

Zodra u deze variabelen hebt geconfigureerd, start u de toepassing opnieuw op in de Azure-console.

### <a name="declaring-the-twilio-module-in-packagejson"></a>De Twilio-module declareren in package.json
Vervolgens moeten we een package.json maken om onze afhankelijkheidsafhankelijkheid van de nodemodule via [npm]te beheren. Maak op hetzelfde `server.js` niveau als het bestand dat u hebt gemaakt `package.json`in de zelfstudie *Azure/node.js* een bestand met de naam .  Plaats in dit bestand het volgende:

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

Dit verklaart de twilio module als een afhankelijkheid, evenals de populaire [Express web framework][express] en de EJS template engine.  Oké, nu zijn we er klaar voor - laten we wat code schrijven!

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>Een uitgaand gesprek voeren
Laten we een eenvoudig formulier maken dat een oproep plaatst naar een nummer dat we kiezen. Open, `server.js`en voer de volgende code in. Let op, waar staat "CHANGE_ME" - zet de naam van uw Azure-website daar:

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

Maak vervolgens een `views` map met de aannaam `index.ejs` - maak in deze map een bestand met de volgende inhoud:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

Implementeer nu uw website in Azure en open uw huis. U moet uw telefoonnummer in het tekstveld kunnen invoeren en een oproep van uw Twilio-nummer kunnen ontvangen!

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>Een sms-bericht verzenden
Laten we nu een gebruikersinterface en een logica voor formulierafhandeling instellen om een sms-bericht te verzenden. Open `server.js`stellen en voeg de volgende code `app.post`toe na de laatste oproep aan:

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

Voeg `views/index.ejs`in een ander formulier toe onder het eerste formulier dat een nummer en een sms-bericht indient:

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

Implementeer uw toepassing opnieuw naar Azure en u moet nu in staat zijn om dat formulier in te dienen en uzelf (of een van uw beste vrienden) een sms-bericht te sturen!

<a id="nextsteps"/>

## <a name="next-steps"></a>Volgende stappen
Je hebt nu de basis geleerd van het gebruik van node.js en Twilio om apps te bouwen die communiceren. Maar deze voorbeelden nauwelijks krassen op het oppervlak van wat mogelijk is met Twilio en node.js. Ga voor meer informatie met Twilio met node.js naar de volgende bronnen:

* [Officiële moduledocumenten][docs]
* [Zelfstudie over VoIP met node.js-toepassingen][voipnode]
* [Votr - een real-time SMS stemapplicatie met node.js en CouchDB (drie delen)][votr]
* [Programmeerprogrammeren in de browser koppelen met node.js][pair]

We hopen dat je houdt van hacking node.js en Twilio op Azure!

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
