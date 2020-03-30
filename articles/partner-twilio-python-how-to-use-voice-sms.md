---
title: Hoe Twilio te gebruiken voor spraak en SMS (Python) | Microsoft Documenten
description: Meer informatie over het voeren van een telefoongesprek en het verzenden van een sms-bericht met de Twilio API-service op Azure. Codevoorbeelden geschreven in Python.
services: ''
documentationcenter: python
author: georgewallace
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: gwallace
ms.openlocfilehash: edbc9eef6b5f0af2e70152b66228cdf09ef31110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72242188"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Hoe twilio te gebruiken voor spraak- en sms-mogelijkheden in Python
Deze handleiding laat zien hoe u veelvoorkomende programmeertaken uitvoeren met de Twilio API-service op Azure. De scenario's die onder meer het maken van een telefoontje en het verzenden van een Short Message Service (SMS) bericht. Zie de sectie [Volgende stappen](#NextSteps) voor meer informatie over Twilio en het gebruik van spraak en sms in uw toepassingen.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Wat is Twilio?
Twilio is het aandrijven van de toekomst van zakelijke communicatie, waardoor ontwikkelaars spraak, VoIP en messaging in te sluiten in toepassingen. Ze virtualiseren alle infrastructuur die nodig is in een cloudgebaseerde, wereldwijde omgeving, waardoor deze wordt blootgelegd via het Twilio communications API-platform. Toepassingen zijn eenvoudig te bouwen en schaalbaar. Profiteer van flexibiliteit met betalen per gebruik en profiteer van de betrouwbaarheid van de cloud.

**Met Twilio Voice** kunnen uw toepassingen telefoongesprekken voeren en ontvangen.
**Met Twilio SMS** kan uw toepassing tekstberichten verzenden en ontvangen.
**Met Twilio Client** u VoIP-gesprekken voeren vanaf elke telefoon, tablet of browser en webrtc ondersteunen.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio Prijzen en speciale aanbiedingen
Azure-klanten ontvangen een [speciale aanbieding][special_offer] van $ 10 twilio-tegoed wanneer u uw Twilio-account upgradet. Dit Twilio-krediet kan worden toegepast op elk Twilio-gebruik ($ 10-tegoed dat overeenkomt met het verzenden van maar liefst 1.000 sms-berichten of het ontvangen van maximaal 1000 binnenkomende spraakminuten, afhankelijk van de locatie van uw telefoonnummer en bericht of oproepbestemming). Wissel dit [Twilio-tegoed in][special_offer] en ga aan de slag.

Twilio is een pay-as-you-go service. Er zijn geen set-up kosten en u uw account op elk gewenst moment sluiten. Meer informatie vindt u bij [Twilio Pricing.][twilio_pricing]

## <a name="concepts"></a><a id="Concepts"></a>Concepten
De Twilio API is een RESTful API die spraak- en sms-functionaliteit biedt voor toepassingen. Clientbibliotheken zijn beschikbaar in meerdere talen; zie [Twilio API-bibliotheken][twilio_libraries]voor een lijst .

Belangrijke aspecten van de Twilio API zijn Twilio werkwoorden en Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Werkwoorden
De API maakt gebruik van Twilio werkwoorden; Het ** &lt;werkwoord&gt; Zeg** instrueert Twilio bijvoorbeeld om hoorbaar een bericht af te leveren tijdens een oproep.

Het volgende is een lijst van Twilio werkwoorden. Meer informatie over de andere werkwoorden en mogelijkheden via [Twilio Markup Language documentatie][twiml].

* **Kies:&gt;Verbindt de beller met een andere &lt;** telefoon.
* Verzamelen : Verzamelt numerieke cijfers die op het telefoontoetsenbord zijn ingevoerd. ** &lt;&gt;**
* **Ophangen:&gt;Beëindigt een gesprek. &lt;**
* **Pauze:&gt;Wacht stil voor een bepaald aantal &lt;** seconden.
* Afspelen : Hiermee wordt een audiobestand afgespeeld. ** &lt;&gt;**
* **Wachtrij:&gt;Voeg de wachtrij toe aan een wachtrij met bellers. &lt;**
* **Record:&gt;registreert de stem van de beller en retourneert een URL van een bestand dat de opname &lt;** bevat.
* Omleiding : Hiermee wordt de controle over een oproep of sms-bericht overgezet naar de TwiML op een andere URL. ** &lt;&gt;**
* **Weigeren:&gt;verwerpt een inkomende oproep naar uw Twilio-nummer zonder u te factureren. &lt;**
* **Zeg:&gt;converteert tekst naar spraak die wordt gemaakt tijdens een &lt;** gesprek.
* **Sms:&gt;Stuurt een sms-bericht. &lt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML is een set xml-gebaseerde instructies op basis van de Twilio-werkwoorden die Twilio informeren over het verwerken van een oproep of sms.

Als voorbeeld zou de volgende TwiML de tekst **Hello World** omzetten in spraak.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

Wanneer uw toepassing de Twilio-API aanroept, is een van de API-parameters de URL die het TwiML-antwoord retourneert. Voor ontwikkelingsdoeleinden u door Twilio verstrekte URL's gebruiken om de TwiML-antwoorden te leveren die door uw toepassingen worden gebruikt. U ook uw eigen URL's hosten om de TwiML-antwoorden te produceren, en een andere optie is om het `TwiMLResponse` object te gebruiken.

Zie [TwiML][twiml]voor meer informatie over Twilio-werkwoorden, hun kenmerken en TwiML. Zie [Twilio API][twilio_api]voor meer informatie over de Twilio API.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Een Twilio-account maken
Wanneer u klaar bent om een Twilio-account te krijgen, meld u dan aan bij [Try Twilio][try_twilio]. Je beginnen met een gratis account en je account later upgraden.

Wanneer u zich aanmeldt voor een Twilio-account, ontvangt u een SID-account en een verificatietoken. Beide zullen nodig zijn om Twilio API-aanroepen te maken. Om ongeautoriseerde toegang tot uw account te voorkomen, houdt u uw verificatietoken veilig. Uw account SID en verificatietoken zijn zichtbaar in de [Twilio-console][twilio_console], respectievelijk in de velden met het label **ACCOUNT SID** en **AUTH TOKEN.**

## <a name="create-a-python-application"></a><a id="create_app"></a>Een Python-toepassing maken
Een Python-toepassing die de Twilio-service gebruikt en wordt uitgevoerd in Azure, is niet anders dan elke andere Python-toepassing die de Twilio-service gebruikt. Hoewel Twilio-services op RUST zijn gebaseerd en op verschillende manieren vanuit Python kunnen worden aangeroepen, zal dit artikel zich richten op het gebruik van Twilio-services met [Twilio-bibliotheek voor Python van GitHub.][twilio_python] Zie [https://www.twilio.com/docs/libraries/python][twilio_lib_docs]voor meer informatie over het gebruik van de Twilio-bibliotheek voor Python .

Ten eerste [stel een nieuwe Azure Linux VM][azure_vm_setup] in om als host te fungeren voor uw nieuwe Python-webtoepassing. Zodra de virtuele machine wordt uitgevoerd, moet u uw toepassing bloot stellen op een openbare poort zoals hieronder beschreven.

### <a name="add-an-incoming-rule"></a>Een binnenkomende regel toevoegen
  1. Ga naar de pagina [Netwerkbeveiligingsgroep][azure_nsg].
  2. Selecteer de netwerkbeveiligingsgroep die overeenkomt met uw virtuele machine.
  3. Regel toevoegen en **uitgaande regel** voor **poort 80**. Zorg ervoor dat u inkomende vanaf elk adres toestaat.

### <a name="set-the-dns-name-label"></a>Het DNS-naamlabel instellen
  1. Ga naar de pagina [De openbare IP-adressen][azure_ips].
  2. Selecteer het openbare IP dat overeenkomt met uw virtuele machine.
  3. Stel het **DNS-naamlabel** in de sectie **Configuratie in.** In het geval van dit voorbeeld zal het er ongeveer zo uitzien *uw-domein-label*.centralus.cloudapp.azure.com

Zodra u in staat bent om verbinding te maken via SSH aan de Virtuele Machine u het Web Framework van uw keuze (de twee meest bekende in Python zijn [Flask](http://flask.pocoo.org/) en [Django](https://www.djangoproject.com)). U een van hen `pip install` gewoon installeren door de opdracht uit te voeren.

Houd er rekening mee dat we de Virtuele Machine hebben geconfigureerd om alleen verkeer op poort 80 toe te staan. Zorg er dus voor dat u de toepassing configureert om deze poort te gebruiken.

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Uw toepassing configureren om Twilio-bibliotheken te gebruiken
U uw toepassing configureren om de Twilio-bibliotheek voor Python op twee manieren te gebruiken:

* Installeer de Twilio-bibliotheek voor Python als een Pip-pakket. Het kan worden geïnstalleerd met de volgende opdrachten:
   
        $ pip install twilio

    OF

* Download de Twilio-bibliotheek voor Python[https://github.com/twilio/twilio-python][twilio_python]van GitHub ( ) en installeer deze als volgt:

        $ python setup.py install

Zodra u de Twilio-bibliotheek voor Python `import` hebt geïnstalleerd, u deze vervolgens in uw Python-bestanden plaatsen:

        import twilio

Zie [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.md)voor meer informatie.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Hoe: Een uitgaande oproep voeren
Het volgende laat zien hoe u een uitgaande oproep voeren. Deze code maakt ook gebruik van een door Twilio geleverde site om de Twilio Markup Language (TwiML) reactie terug te geven. Vervang uw waarden voor de **from_number** en **to_number** telefoonnummers en zorg ervoor dat u het **from_number** telefoonnummer voor uw Twilio-account hebt geverifieerd voordat u de code uitvoert.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "https://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

Zoals gezegd maakt deze code gebruik van een door Twilio geleverde site om de TwiML-respons terug te sturen. U in plaats daarvan uw eigen site gebruiken om de TwiML-respons te bieden; Zie [TwiML-antwoorden van uw eigen website](#howto_provide_twiml_responses)voor meer informatie.

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Hoe: Een sms-bericht verzenden
Hieronder ziet u hoe u een `TwilioRestClient` sms-bericht verzendt met de klasse. Het **from_number** nummer wordt door Twilio verstrekt voor proefaccounts om sms-berichten te verzenden. Het **to_number-nummer** moet worden geverifieerd voor uw Twilio-account voordat de code wordt uitgevoerd.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>How to: TwiML Reacties van uw eigen website
Wanneer uw toepassing een aanroep naar de Twilio-API initieert, stuurt Twilio uw verzoek naar een URL die naar verwachting een TwiML-antwoord retourneert. In het bovenstaande voorbeeld wordt de [https://twimlets.com/message][twimlet_message_url]door Twilio verstrekte URL gebruikt. (Hoewel TwiML is ontworpen voor gebruik door Twilio, u het bekijken in uw browser. Klik bijvoorbeeld [https://twimlets.com/message][twimlet_message_url] om een `<Response>` leeg element te zien; klik als een [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] ander `<Response>` voorbeeld om `<Say>` een element te zien dat een element bevat.)

In plaats van te vertrouwen op de door Twilio verstrekte URL, u uw eigen site maken die HTTP-antwoorden retourneert. U de site maken in elke taal die XML-antwoorden retourneert. dit onderwerp gaat ervan uit dat u Python gebruikt om de TwiML te maken.

De volgende voorbeelden zullen de output van een TwiML reactie die **hello world** zegt op de oproep.

Met Kolf:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Met Django:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

Zoals u zien in het bovenstaande voorbeeld, is het TwiML-antwoord gewoon een XML-document. De Twilio-bibliotheek voor Python bevat klassen die TwiML voor u genereren. Het onderstaande voorbeeld produceert de equivalente respons `twiml` zoals hierboven wordt weergegeven, maar gebruikt de module in de Twilio-bibliotheek voor Python:

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

Zie voor meer informatie over [https://www.twilio.com/docs/api/twiml][twiml_reference]TwiML .

Zodra u uw Python-toepassing hebt ingesteld om TwiML-antwoorden te geven, `client.calls.create` gebruikt u de URL van de toepassing terwijl de URL naar de methode is doorgegeven. Als u bijvoorbeeld een webtoepassing met de naam **MyTwiML** hebt geïmplementeerd in een Azure-gehoste service, u de url als webhook gebruiken, zoals in het volgende voorbeeld wordt weergegeven:

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Hoe: Extra Twilio-services gebruiken
Naast de voorbeelden die hier worden weergegeven, biedt Twilio webgebaseerde API's die u gebruiken om extra Twilio-functionaliteit uit uw Azure-toepassing te gebruiken. Zie de [Twilio API-documentatie][twilio_api]voor meer informatie.

## <a name="next-steps"></a><a id="NextSteps"></a>Volgende stappen
Nu u de basis van de Twilio-service hebt geleerd, volgt u deze links om meer te weten te komen:

* [Twilio-beveiligingsrichtlijnen][twilio_security_guidelines]
* [Twilio HowTo-gidsen en voorbeeldcode][twilio_howtos]
* [Twilio Quickstart-zelfstudies][twilio_quickstarts]
* [Twilio op GitHub][twilio_on_github]
* [Praat met Twilio Support][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
