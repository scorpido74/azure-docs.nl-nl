---
title: Bellen vanuit Twilio (PHP) | Microsoft Documenten
description: Meer informatie over het voeren van een telefoongesprek en het verzenden van een sms-bericht met de Twilio API-service op Azure. Voorbeelden zijn voor PHP applicatie.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: fb1623c4a409f1c6cba94bad56d773e166d2b182
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637311"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Bellen met Twilio in een PHP-toepassing op Azure
In het volgende voorbeeld ziet u hoe u Twilio gebruiken om te bellen vanaf een PHP-webpagina die wordt gehost in Azure. De resulterende toepassing zal de gebruiker om de waarden van het telefoongesprek vragen, zoals getoond in volgende screenshot.

![Azure-oproepformulier met Twilio en PHP][twilio_php]

Je moet het volgende doen om de code in dit onderwerp te gebruiken:

1. Een Twilio-account en verificatietoken ophalen van uw [Twilio-console.][twilio_console] Om aan de slag te gaan [https://www.twilio.com/pricing][twilio_pricing]met Twilio, evalueert u de prijzen op . Je je aanmelden voor [https://www.twilio.com/try-twilio][try_twilio]een proefaccount op.
2. Verkrijg de [Twilio bibliotheek voor PHP](https://github.com/twilio/twilio-php) of installeer het als een PEAR pakket. Zie het [leesmijbestand voor](https://github.com/twilio/twilio-php/blob/master/README.md)meer informatie .
3. Installeer de Azure SDK voor PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Een webformulier maken voor het voeren van een gesprek
In de volgende HTML-code ziet u hoe u een webpagina **(callform.html)** maakt die gebruikersgegevens ophaalt voor het voeren van een oproep:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>De code maken om te bellen
De volgende code laat zien hoe **makecall.php**te bouwen , die wordt aangeroepen wanneer de gebruiker het formulier wordt weergegeven door **callform.html**. De onderstaande code maakt het oproepbericht en genereert de oproep. Zorg er ook voor dat u uw Twilio-account en verificatietoken van de [Twilio-console][twilio_console] gebruikt in plaats van de tijdelijke aanduidingswaarden die zijn toegewezen aan **$sid** en **$token** in de onderstaande code.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Naast het maken van de oproep, **maakt call.php** een aantal metagegevens van de oproep weer, zoals wordt weergegeven in de afbeelding hieronder. Zie [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties]voor meer informatie over metagegevens van oproepen .

![Azure-oproeprespons met Twilio en PHP][twilio_php_response]

## <a name="run-the-application"></a>De toepassing uitvoeren
De volgende stap is het [implementeren van uw toepassing naar Azure Web Apps met Git](app-service/app-service-web-get-started-php.md) (hoewel niet alle informatie die er relevant is). 

## <a name="next-steps"></a>Volgende stappen
Deze code is beschikbaar om u basisfunctionaliteit te laten zien met Twilio in PHP op Azure. Voordat u in productie naar Azure gaat, u meer foutafhandeling of andere functies toevoegen. Bijvoorbeeld:

* In plaats van een webformulier te gebruiken, u Azure-opslagblobs of SQL Database gebruiken om telefoonnummers en beltekst op te slaan. Zie [Azure Storage gebruiken met PHP-toepassingen][howto_blob_storage_php]voor informatie over het gebruik van Azure-opslagblobs in PHP. Zie SQL Database gebruiken met [PHP-toepassingen][howto_sql_azure_php]voor informatie over het gebruik van SQL Database in PHP.
* De **makecall.php-code** maakt gebruik van[https://twimlets.com/message][twimlet_message_url]door Twilio verstrekte URL () om een Twilio Markup Language (TwiML) reactie te geven die Twilio informeert hoe verder te gaan met de oproep. De TwiML die wordt geretourneerd, `<Say>` kan bijvoorbeeld een werkwoord bevatten dat resulteert in tekst die met de ontvanger van de oproep wordt gesproken. In plaats van de door Twilio verstrekte URL te gebruiken, u uw eigen service bouwen om te reageren op het verzoek van Twilio; zie [Hoe twilio te gebruiken voor spraak- en sms-mogelijkheden in PHP][howto_twilio_voice_sms_php]voor meer informatie. Meer informatie over TwiML [https://www.twilio.com/docs/api/twiml][twiml]is te vinden `<Say>` op , en meer informatie [https://www.twilio.com/docs/api/twiml/say][twilio_say]over en andere Twilio werkwoorden is te vinden op .
* Lees de beveiligingsrichtlijnen van [https://www.twilio.com/docs/security][twilio_docs_security]Twilio op .

Zie voor meer informatie over [https://www.twilio.com/docs][twilio_docs]Twilio.

## <a name="see-also"></a>Zie ook
* [Hoe Twilio te gebruiken voor spraak- en sms-mogelijkheden in PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: https://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: https://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
