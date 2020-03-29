---
title: Hoe Twilio te gebruiken voor spraak en SMS (Ruby) | Microsoft Documenten
description: Meer informatie over het voeren van een telefoongesprek en het verzenden van een sms-bericht met de Twilio API-service op Azure. Code monsters geschreven in Ruby.
services: ''
documentationcenter: ruby
author: georgewallace
ms.assetid: 60e512f6-fa47-47c0-aedc-f19bb72a1158
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 4822e6feb29f5a17c653a60937b895ec584e0ee4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637206"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-ruby"></a>Hoe Twilio te gebruiken voor spraak- en sms-mogelijkheden in Ruby
Deze handleiding laat zien hoe u veelvoorkomende programmeertaken uitvoeren met de Twilio API-service op Azure. De scenario's die onder meer het maken van een telefoontje en het verzenden van een Short Message Service (SMS) bericht. Zie de sectie [Volgende stappen](#NextSteps) voor meer informatie over Twilio en het gebruik van spraak en sms in uw toepassingen.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Wat is Twilio?
Twilio is een audiowebservice-API waarmee u uw bestaande webtalen en -vaardigheden gebruiken om spraak- en sms-toepassingen te bouwen. Twilio is een service van derden (geen Azure-functie en geen Microsoft-product).

**Met Twilio Voice** kunnen uw toepassingen telefoongesprekken voeren en ontvangen. **Met Twilio SMS** kunnen uw applicaties sms-berichten maken en ontvangen. **Met Twilio Client** kunnen uw toepassingen spraakcommunicatie mogelijk maken via bestaande internetverbindingen, waaronder mobiele verbindingen.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio Prijzen en speciale aanbiedingen
Informatie over Twilio-prijzen is beschikbaar bij [Twilio Pricing.][twilio_pricing] Azure-klanten ontvangen een [speciale aanbieding:][special_offer]een gratis tegoed van 1000 sms'jes of 1000 binnenkomende minuten. Ga naar . [https://ahoy.twilio.com/azure][special_offer]  

## <a name="concepts"></a><a id="Concepts"></a>Concepten
De Twilio API is een RESTful API die spraak- en sms-functionaliteit biedt voor toepassingen. Clientbibliotheken zijn beschikbaar in meerdere talen; zie [Twilio API-bibliotheken][twilio_libraries]voor een lijst .

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML is een set xml-gebaseerde instructies die Twilio informeren over het verwerken van een oproep of sms.

Als voorbeeld zou de volgende TwiML de tekst **Hello World** omzetten in spraak.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Alle TwiML-documenten hebben `<Response>` als hoofdelement. Van daaruit gebruikt u Twilio-werkwoorden om het gedrag van uw toepassing te definiëren.

### <a name="twiml-verbs"></a><a id="Verbs"></a>TwiML Werkwoorden
Twilio-werkwoorden zijn XML-tags die Twilio vertellen wat hij moet **doen.** Het ** &lt;werkwoord&gt; Zeg** instrueert Twilio bijvoorbeeld om een bericht hoorbaar af te leveren tijdens een oproep. 

Het volgende is een lijst van Twilio werkwoorden.

* **Kies:&gt;Verbindt de beller met een andere &lt;** telefoon.
* Verzamelen : Verzamelt numerieke cijfers die op het telefoontoetsenbord zijn ingevoerd. ** &lt;&gt;**
* **Ophangen:&gt;Beëindigt een gesprek. &lt;**
* Afspelen : Hiermee wordt een audiobestand afgespeeld. ** &lt;&gt;**
* **Pauze:&gt;Wacht stil voor een bepaald aantal &lt;** seconden.
* **Record:&gt;Registreert de stem van de beller en retourneert een URL van een bestand dat de opname &lt;** bevat.
* Omleiding : Hiermee wordt de controle over een oproep of sms-bericht overgezet naar de TwiML op een andere URL. ** &lt;&gt;**
* **Weigeren:&gt;weigert een inkomende oproep naar uw Twilio-nummer zonder u te factureren &lt;**
* **Zeg:&gt;converteert tekst naar spraak die wordt gemaakt tijdens een &lt;** gesprek.
* **Sms:&gt;Stuurt een sms-bericht. &lt;**

Zie [TwiML][twiml]voor meer informatie over Twilio-werkwoorden, hun kenmerken en TwiML. Zie [Twilio API][twilio_api]voor meer informatie over de Twilio API.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Een Twilio-account maken
Als je klaar bent om een Twilio-account te krijgen, meld je dan aan bij [Try Twilio][try_twilio]. Je beginnen met een gratis account en je account later upgraden.

Wanneer u zich aanmeldt voor een Twilio-account, krijgt u een gratis telefoonnummer voor uw aanvraag. Je ontvangt ook een account SID en een auth-token. Beide zullen nodig zijn om Twilio API-aanroepen te maken. Om ongeautoriseerde toegang tot uw account te voorkomen, houdt u uw verificatietoken veilig. Uw account SID en auth token zijn zichtbaar op de [Twilio account pagina][twilio_account], in de velden met het label **ACCOUNT SID** en AUTH **TOKEN**, respectievelijk.

### <a name="verify-phone-numbers"></a><a id="VerifyPhoneNumbers"></a>Telefoonnummers verifiëren
Naast het nummer dat u door Twilio krijgt gegeven, u ook nummers verifiëren die u beheert (d.w.z. uw mobiele telefoon of telefoonnummer thuis) voor gebruik in uw toepassingen. 

Zie [Nummers beheren][verify_phone]voor informatie over het verifiëren van een telefoonnummer.

## <a name="create-a-ruby-application"></a><a id="create_app"></a>Een Ruby-toepassing maken
Een Ruby-toepassing die de Twilio-service gebruikt en wordt uitgevoerd in Azure, is niet anders dan elke andere Ruby-toepassing die de Twilio-service gebruikt. Hoewel Twilio-services RESTful zijn en op verschillende manieren vanuit Ruby kunnen worden gebeld, zal dit artikel zich richten op het gebruik van Twilio-services met [Twilio helperbibliotheek voor Ruby.][twilio_ruby]

Stel eerst [een nieuwe Azure Linux VM in][azure_vm_setup] om als host te fungeren voor uw nieuwe Ruby-webtoepassing. Negeer de stappen met het maken van een Rails-app, stel de VM in. Zorg ervoor dat u een eindpunt maakt met een externe poort van 80 en een interne poort van 5000.

In de onderstaande voorbeelden zullen we [Sinatra][sinatra]gebruiken, een zeer eenvoudig webframework voor Ruby. Maar u zeker gebruik maken van de Twilio helper bibliotheek voor Ruby met een ander web framework, met inbegrip van Ruby on Rails.

SSH in uw nieuwe VM en maak een directory voor uw nieuwe app. Maak in die map een bestand met de naam Gemfile en kopieer de volgende code erin:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Op de opdrachtregel uitvoeren `bundle install`. Hiermee worden de bovenstaande afhankelijkheden geïnstalleerd. Maak vervolgens een `web.rb`bestand met de naam . Dit is waar de code voor uw web-app woont. Plak de volgende code erin:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

Op dit punt moet je in `ruby web.rb -p 5000`staat zijn de opdracht uit te voeren. Dit zal spin-up een kleine webserver op poort 5000. U moet in uw browser naar deze app kunnen bladeren door naar de URL te gaan die u voor uw Azure VM hebt ingesteld. Zodra u uw web-app in de browser bereiken, bent u klaar om te beginnen met het bouwen van een Twilio-app.

## <a name="configure-your-application-to-use-twilio"></a><a id="configure_app"></a>Uw toepassing configureren om Twilio te gebruiken
U uw web-app configureren om de `Gemfile` Twilio-bibliotheek te gebruiken door uw te updaten om deze regel op te nemen:

    gem 'twilio-ruby'

Voer op de `bundle install`opdrachtregel uit. Nu `web.rb` open en met inbegrip van deze lijn aan de top:

    require 'twilio-ruby'

U bent nu helemaal klaar om de Twilio helper-bibliotheek voor Ruby te gebruiken in uw web-app.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Hoe: Een uitgaande oproep voeren
Het volgende laat zien hoe u een uitgaande oproep voeren. Belangrijke concepten zijn het gebruik van de Twilio helper bibliotheek voor Ruby om REST API-aanroepen te maken en TwiML renderen. Vervang uw waarden voor de telefoonnummers **van en** **naar** en zorg ervoor dat u het telefoonnummer **van uw** Twilio-account controleert voordat u de code uitvoert.

Voeg deze `web.md`functie toe aan:

    # Set your account ID and authentication token.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from = "NNNNNNNNNNN";

    # The number of the phone receiving call.
    to = "NNNNNNNNNNN";

    # Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";

    get '/make_call' do
      # Create the call client.
      client = Twilio::REST::Client.new(sid, token);

      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

Als u zich `http://yourdomain.cloudapp.net/make_call` opent in een browser, wordt de oproep naar de Twilio-API geactiveerd om te bellen. De eerste twee `client.account.calls.create` parameters in zijn vrij vanzelfsprekend: `from` het nummer van `to`de oproep is en het nummer van de oproep is . 

De derde`url`parameter ( ) is de URL die Twilio vraagt om instructies te krijgen over wat te doen zodra de oproep is aangesloten. In dit geval hebben we`http://yourdomain.cloudapp.net`een URL ( ) die een `<Say>` eenvoudige TwiML document retourneert en maakt gebruik van het werkwoord om wat tekst-naar-spraak te doen en te zeggen "Hello Monkey" aan de persoon die de oproep.

## <a name="how-to-receive-an-sms-message"></a><a id="howto_receive_sms"></a>Hoe: Een sms-bericht ontvangen
In het vorige voorbeeld hebben we een **uitgaand** telefoongesprek gestart. Laten we deze keer het telefoonnummer gebruiken dat Twilio ons gaf tijdens het aanmelden om een **binnenkomend** sms-bericht te verwerken.

Log eerst in op uw [Twilio-dashboard.][twilio_account] Klik op "Numbers" in de bovenste navigatie en klik vervolgens op het Twilio nummer dat u hebt opgegeven. U ziet twee URL's die u configureren. Een URL voor spraakaanvraag en een URL van sms-aanvragen. Dit zijn de URL's die Twilio belt wanneer er een telefoongesprek wordt gevoerd of een sms naar uw nummer wordt verzonden. De URL's worden ook wel "webhooks" genoemd.

We willen binnenkomende sms-berichten verwerken, dus laten we `http://yourdomain.cloudapp.net/sms_url`de URL bijwerken naar . Klik onder aan de pagina op Wijzigingen opslaan. Nu, terug `web.rb` in laten we het programma van onze applicatie om dit te behandelen:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Zorg ervoor dat u na het aanbrengen van de wijziging uw web-app opnieuw start. Neem nu je telefoon en stuur een sms naar je Twilio nummer. Je moet onmiddellijk een SMS-antwoord dat zegt: "Hey, bedankt voor de ping! Twilio en Azure rock!".

## <a name="how-to-use-additional-twilio-services"></a><a id="additional_services"></a>Hoe: Extra Twilio-services gebruiken
Naast de voorbeelden die hier worden weergegeven, biedt Twilio webgebaseerde API's die u gebruiken om extra Twilio-functionaliteit uit uw Azure-toepassing te gebruiken. Zie de [Twilio API-documentatie][twilio_api_documentation]voor meer informatie.

### <a name="next-steps"></a><a id="NextSteps"></a>Volgende stappen
Nu je de basis van de Twilio-service hebt geleerd, volg je deze links om meer te weten te komen:

* [Twilio-beveiligingsrichtlijnen][twilio_security_guidelines]
* [Twilio HowTos en Voorbeeldcode][twilio_howtos]
* [Twilio Quickstart-zelfstudies][twilio_quickstarts] 
* [Twilio op GitHub][twilio_on_github]
* [Praat met Twilio Support][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: https://docs.microsoft.com/azure/virtual-machines/linux/classic/ruby-rails-web-app
