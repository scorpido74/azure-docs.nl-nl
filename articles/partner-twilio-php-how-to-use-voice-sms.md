---
title: Hoe Twilio te gebruiken voor spraak en SMS (PHP) | Microsoft Documenten
description: Meer informatie over het voeren van een telefoongesprek en het verzenden van een sms-bericht met de Twilio API-service op Azure. Code samples geschreven in PHP.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: f9fb250109a1c9000eae8da0d6337c96f19f0f89
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410542"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>Hoe Twilio te gebruiken voor spraak- en sms-mogelijkheden in PHP
Deze handleiding laat zien hoe u veelvoorkomende programmeertaken uitvoeren met de Twilio API-service op Azure. De scenario's die onder meer het maken van een telefoontje en het verzenden van een Short Message Service (SMS) bericht. Zie de sectie [Volgende stappen](#NextSteps) voor meer informatie over Twilio en het gebruik van spraak en sms in uw toepassingen.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Wat is Twilio?
Twilio is het aandrijven van de toekomst van zakelijke communicatie, waardoor ontwikkelaars spraak, VoIP en messaging in te sluiten in toepassingen. Ze virtualiseren alle infrastructuur die nodig is in een cloudgebaseerde, wereldwijde omgeving, waardoor deze wordt blootgelegd via het Twilio communications API-platform. Toepassingen zijn eenvoudig te bouwen en schaalbaar. Profiteer van flexibiliteit met betalen per gebruik en profiteer van de betrouwbaarheid van de cloud.

**Met Twilio Voice** kunnen uw toepassingen telefoongesprekken voeren en ontvangen. **Met Twilio SMS** kan uw toepassing tekstberichten verzenden en ontvangen. **Met Twilio Client** u VoIP-gesprekken voeren vanaf elke telefoon, tablet of browser en webrtc ondersteunen.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio Prijzen en speciale aanbiedingen
Azure-klanten ontvangen een [speciale aanbieding:](https://www.twilio.com/azure)gratis $ 10 van Twilio-tegoed wanneer u uw Twilio-account upgradet. Dit Twilio-krediet kan worden toegepast op elk Twilio-gebruik ($ 10-tegoed dat overeenkomt met het verzenden van maar liefst 1.000 sms-berichten of het ontvangen van maximaal 1000 binnenkomende spraakminuten, afhankelijk van de locatie van uw telefoonnummer en bericht of oproepbestemming). Wissel dit Twilio-tegoed [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure)in en ga aan de slag met: .

Twilio is een pay-as-you-go service. Er zijn geen set-up kosten en u uw account op elk gewenst moment sluiten. Meer informatie vindt u bij [Twilio Pricing.][twilio_pricing]

## <a name="concepts"></a><a id="Concepts"></a>Concepten
De Twilio API is een RESTful API die spraak- en sms-functionaliteit biedt voor toepassingen. Clientbibliotheken zijn beschikbaar in meerdere talen; zie [Twilio API-bibliotheken][twilio_libraries]voor een lijst .

Belangrijke aspecten van de Twilio API zijn Twilio werkwoorden en Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Werkwoorden
De API maakt gebruik van Twilio werkwoorden; Het ** &lt;werkwoord&gt; Zeg** instrueert Twilio bijvoorbeeld om hoorbaar een bericht af te leveren tijdens een oproep.

Het volgende is een lijst van Twilio werkwoorden. Meer informatie over de andere werkwoorden en mogelijkheden via [Twilio Markup Language documentatie](https://www.twilio.com/docs/api/twiml).

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

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML is een set xml-gebaseerde instructies op basis van de Twilio-werkwoorden die Twilio informeren over het verwerken van een oproep of sms.

Als voorbeeld zou de volgende TwiML de tekst **Hello World** omzetten in spraak.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wanneer uw toepassing de Twilio-API aanroept, is een van de API-parameters de URL die het TwiML-antwoord retourneert. Voor ontwikkelingsdoeleinden u door Twilio verstrekte URL's gebruiken om de TwiML-antwoorden te leveren die door uw toepassingen worden gebruikt. U ook uw eigen URL's hosten om de TwiML-antwoorden te produceren, en een andere optie is het gebruik van het **TwiMLResponse-object.**

Zie [TwiML][twiml]voor meer informatie over Twilio-werkwoorden, hun kenmerken en TwiML. Zie [Twilio API][twilio_api]voor meer informatie over de Twilio API.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Een Twilio-account maken
Als je klaar bent om een Twilio-account te krijgen, meld je dan aan bij [Try Twilio][try_twilio]. Je beginnen met een gratis account en je account later upgraden.

Wanneer u zich aanmeldt voor een Twilio-account, ontvangt u een account-id en een verificatietoken. Beide zullen nodig zijn om Twilio API-aanroepen te maken. Om ongeautoriseerde toegang tot uw account te voorkomen, houdt u uw verificatietoken veilig. Uw account-ID en verificatietoken zijn zichtbaar op de [twilio-accountpagina][twilio_account], respectievelijk in de velden met het label **ACCOUNT SID** en **AUTH TOKEN.**

## <a name="create-a-php-application"></a><a id="create_app"></a>Een PHP-toepassing maken
Een PHP-toepassing die de Twilio-service gebruikt en wordt uitgevoerd in Azure, is niet anders dan elke andere PHP-toepassing die de Twilio-service gebruikt. Hoewel Twilio-services op RUST zijn gebaseerd en op verschillende manieren vanuit PHP kunnen worden aangeroepen, zal dit artikel zich richten op het gebruik van Twilio-services met [Twilio-bibliotheek voor PHP van GitHub.][twilio_php] Zie [https://www.twilio.com/docs/libraries/php][twilio_lib_docs]voor meer informatie over het gebruik van de Twilio-bibliotheek voor PHP .

Gedetailleerde instructies voor het bouwen en implementeren van een Twilio/PHP-toepassing op Azure zijn beschikbaar bij [Hoe maak je een telefoongesprek met Twilio in een PHP-toepassing op Azure.][howto_phonecall_php]

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Uw toepassing configureren om Twilio-bibliotheken te gebruiken
U uw toepassing configureren om de Twilio-bibliotheek voor PHP op twee manieren te gebruiken:

1. Download de Twilio-bibliotheek voor PHP[https://github.com/twilio/twilio-php][twilio_php]van GitHub () en voeg de **servicesmap** toe aan uw toepassing.
   
    OF
2. Installeer de Twilio-bibliotheek voor PHP als EEN PEAR-pakket. Het kan worden geïnstalleerd met de volgende opdrachten:
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Zodra u de Twilio-bibliotheek voor PHP hebt geïnstalleerd, u een **require_once-instructie** toevoegen aan de bovenkant van uw PHP-bestanden om naar de bibliotheek te verwijzen:

        require_once 'Services/Twilio.php';

Zie voor meer [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme]informatie .

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Hoe: Een uitgaande oproep voeren
Hieronder ziet u hoe u een uitgaande oproep voeren met de **Services_Twilio** klasse. Deze code maakt ook gebruik van een door Twilio geleverde site om de Twilio Markup Language (TwiML) reactie terug te geven. Vervang uw waarden voor de telefoonnummers **van en** **naar** en zorg ervoor dat u het telefoonnummer **van uw** Twilio-account controleert voordat u de code uitvoert.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "https://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Zoals gezegd maakt deze code gebruik van een door Twilio geleverde site om de TwiML-respons terug te sturen. U in plaats daarvan uw eigen site gebruiken om de TwiML-respons te bieden; Zie [TwiML-antwoorden van uw eigen website](#howto_provide_twiml_responses)voor meer informatie.

* **Opmerking:** Zie[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Hoe: Een sms-bericht verzenden
Hieronder ziet u hoe u een sms-bericht verzendt met de **Services_Twilio** klasse. Het **Van-nummer** wordt door Twilio geleverd voor proefaccounts om sms-berichten te verzenden. Het **aan-nummer** moet worden geverifieerd voor uw Twilio-account voordat de code wordt uitgevoerd.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>How to: TwiML Reacties van uw eigen website
Wanneer uw toepassing een aanroep naar de Twilio-API initieert, stuurt Twilio uw verzoek naar een URL die naar verwachting een TwiML-antwoord retourneert. In het bovenstaande voorbeeld wordt de [https://twimlets.com/message][twimlet_message_url]door Twilio verstrekte URL gebruikt. (Terwijl TwiML is ontworpen voor gebruik door Twilio, u de het in uw browser. Klik bijvoorbeeld [https://twimlets.com/message][twimlet_message_url] om een `<Response>` leeg element te zien; klik als een [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] ander `<Response>` voorbeeld om `<Say>` een element te zien dat een element bevat.)

In plaats van te vertrouwen op de door Twilio verstrekte URL, u uw eigen site maken die HTTP-antwoorden retourneert. U de site maken in elke taal die XML-antwoorden retourneert. dit onderwerp gaat ervan uit dat u PHP gebruikt om de TwiML te maken.

De volgende PHP-pagina resulteert in een TwiML-reactie die **Hello World** op de oproep zegt.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

Zoals u zien in het bovenstaande voorbeeld, is het TwiML-antwoord gewoon een XML-document. De Twilio-bibliotheek voor PHP bevat klassen die TwiML voor u genereren. Het onderstaande voorbeeld produceert de equivalente respons zoals hierboven weergegeven, maar gebruikt de klasse **Services\_Twilio\_Twiml** in de Twilio-bibliotheek voor PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Zie voor meer informatie over [https://www.twilio.com/docs/api/twiml][twiml_reference]TwiML . 

Zodra u uw PHP-pagina hebt ingesteld om TwiML-antwoorden te geven, gebruikt `Services_Twilio->account->calls->create` u de URL van de PHP-pagina terwijl de URL naar de methode is doorgegeven. Als u bijvoorbeeld een webtoepassing met de naam **MyTwiML** hebt geïmplementeerd op een Azure-gehoste service en de naam van de PHP-pagina **mytwiml.php**is, kan de URL worden doorgegeven aan **Services_Twilio->account->-calls->maken** zoals in het volgende voorbeeld wordt weergegeven:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Zie [Bellen met Twilio in een PHP-toepassing op Azure][howto_phonecall_php]voor meer informatie over het gebruik van Twilio in Azure.

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Hoe: Extra Twilio-services gebruiken
Naast de voorbeelden die hier worden weergegeven, biedt Twilio webgebaseerde API's die u gebruiken om extra Twilio-functionaliteit uit uw Azure-toepassing te gebruiken. Zie de [Twilio API-documentatie][twilio_api_documentation]voor meer informatie.

## <a name="next-steps"></a><a id="NextSteps"></a>Volgende stappen
Nu je de basis van de Twilio-service hebt geleerd, volg je deze links om meer te weten te komen:

* [Twilio-beveiligingsrichtlijnen][twilio_security_guidelines]
* [Twilio HowTo's en Voorbeeldcode][twilio_howtos]
* [Twilio Quickstart-zelfstudies][twilio_quickstarts] 
* [Twilio op GitHub][twilio_on_github]
* [Praat met Twilio Support][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
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
