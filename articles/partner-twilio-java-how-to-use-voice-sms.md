---
title: Hoe Twilio te gebruiken voor spraak en SMS (Java) | Microsoft Documenten
description: Meer informatie over het voeren van een telefoongesprek en het verzenden van een sms-bericht met de Twilio API-service op Azure. Code monsters geschreven in Java.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 18e93ce18ed746612996399dc1aeb258abd26165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69637212"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Hoe Twilio te gebruiken voor spraak- en sms-mogelijkheden op Java
Deze handleiding laat zien hoe u veelvoorkomende programmeertaken uitvoeren met de Twilio API-service op Azure. De scenario's die onder meer het maken van een telefoontje en het verzenden van een Short Message Service (SMS) bericht. Zie de sectie [Volgende stappen](#NextSteps) voor meer informatie over Twilio en het gebruik van spraak en sms in uw toepassingen.

## <a name="what-is-twilio"></a><a id="WhatIs"></a>Wat is Twilio?
Twilio is een audiowebservice-API waarmee u uw bestaande webtalen en -vaardigheden gebruiken om spraak- en sms-toepassingen te bouwen. Twilio is een service van derden (geen Azure-functie en geen Microsoft-product).

**Met Twilio Voice** kunnen uw toepassingen telefoongesprekken voeren en ontvangen. **Met Twilio SMS** kunnen uw applicaties sms-berichten maken en ontvangen. **Met Twilio Client** kunnen uw toepassingen spraakcommunicatie mogelijk maken via bestaande internetverbindingen, waaronder mobiele verbindingen.

## <a name="twilio-pricing-and-special-offers"></a><a id="Pricing"></a>Twilio Prijzen en speciale aanbiedingen
Informatie over Twilio-prijzen is beschikbaar bij [Twilio Pricing.][twilio_pricing] Azure-klanten ontvangen een [speciale aanbieding:][special_offer]een gratis tegoed van 1000 sms'jes of 1000 binnenkomende minuten. Ga naar . [https://ahoy.twilio.com/azure][special_offer]

## <a name="concepts"></a><a id="Concepts"></a>Concepten
De Twilio API is een RESTful API die spraak- en sms-functionaliteit biedt voor toepassingen. Clientbibliotheken zijn beschikbaar in meerdere talen; zie [Twilio API-bibliotheken][twilio_libraries]voor een lijst .

Belangrijke aspecten van de Twilio API zijn Twilio werkwoorden en Twilio Markup Language (TwiML).

### <a name="twilio-verbs"></a><a id="Verbs"></a>Twilio Werkwoorden
De API maakt gebruik van Twilio werkwoorden; Het ** &lt;werkwoord&gt; Zeg** instrueert Twilio bijvoorbeeld om hoorbaar een bericht af te leveren tijdens een oproep.

Het volgende is een lijst van Twilio werkwoorden.

* **Kies:&gt;Verbindt de beller met een andere &lt;** telefoon.
* Verzamelen : Verzamelt numerieke cijfers die op het telefoontoetsenbord zijn ingevoerd. ** &lt;&gt;**
* **Ophangen:&gt;Beëindigt een gesprek. &lt;**
* Afspelen : Hiermee wordt een audiobestand afgespeeld. ** &lt;&gt;**
* **Wachtrij:&gt;Voeg de wachtrij toe aan een wachtrij met bellers. &lt;**
* **Pauze:&gt;Wacht stil voor een bepaald aantal &lt;** seconden.
* **Record:&gt;Registreert de stem van de beller en retourneert een URL van een bestand dat de opname &lt;** bevat.
* Omleiding : Hiermee wordt de controle over een oproep of sms-bericht overgezet naar de TwiML op een andere URL. ** &lt;&gt;**
* **Weigeren:&gt;verwerpt een inkomende oproep naar uw Twilio-nummer zonder u te factureren. &lt;**
* **Zeg:&gt;converteert tekst naar spraak die wordt gemaakt tijdens een &lt;** gesprek.
* **Sms:&gt;Stuurt een sms-bericht. &lt;**

### <a name="twiml"></a><a id="TwiML"></a>TwiML
TwiML is een set xml-gebaseerde instructies op basis van de Twilio-werkwoorden die Twilio informeren over het verwerken van een oproep of sms.

Als voorbeeld, de volgende TwiML zou converteren van de tekst **Hello World!** om te spreken.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

Wanneer uw toepassing de Twilio-API aanroept, is een van de API-parameters de URL die het TwiML-antwoord retourneert. Voor ontwikkelingsdoeleinden u door Twilio verstrekte URL's gebruiken om de TwiML-antwoorden te leveren die door uw toepassingen worden gebruikt. U ook uw eigen URL's hosten om de TwiML-antwoorden te produceren, en een andere optie is het gebruik van het **TwiMLResponse-object.**

Zie [TwiML][twiml]voor meer informatie over Twilio-werkwoorden, hun kenmerken en TwiML. Zie [Twilio API][twilio_api]voor meer informatie over de Twilio API.

## <a name="create-a-twilio-account"></a><a id="CreateAccount"></a>Een Twilio-account maken
Als je klaar bent om een Twilio-account te krijgen, meld je dan aan bij [Try Twilio][try_twilio]. Je beginnen met een gratis account en je account later upgraden.

Wanneer u zich aanmeldt voor een Twilio-account, ontvangt u een account-id en een verificatietoken. Beide zullen nodig zijn om Twilio API-aanroepen te maken. Om ongeautoriseerde toegang tot uw account te voorkomen, houdt u uw verificatietoken veilig. Uw account-ID en verificatietoken zijn zichtbaar op de [Twilio-console][twilio_console], respectievelijk in de velden met het label **ACCOUNT SID** en **AUTH TOKEN.**

## <a name="create-a-java-application"></a><a id="create_app"></a>Een Java-toepassing maken
1. Verkrijg de Twilio JAR en voeg deze toe aan je Java-buildpad en je WAR-implementatieassemblage. Bij [https://github.com/twilio/twilio-java][twilio_java], u de GitHub bronnen downloaden en maak je eigen JAR, of download een pre-built JAR (met of zonder afhankelijkheden).
2. Zorg ervoor dat de **cacerts** keystore van uw JDK het Equifax Secure Certificate Authority-certificaat bevat met MD5-vingerafdruk 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (het serienummer is 3 5:DE:F4:CF en de SHA1-vingerafdruk is D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Dit is het certificaat van de [https://api.twilio.com][twilio_api_service] certificaatautoriteit (CA) voor de service, dat wordt aangeroepen wanneer u Twilio API's gebruikt. Zie [Een certificaat toevoegen aan de Java CA Certificate Store][add_ca_cert]voor informatie over het waarborgen dat de **cacerts-keystore** van uw JDK het juiste CA-certificaat bevat.

Gedetailleerde instructies voor het gebruik van de Twilio-clientbibliotheek voor Java zijn beschikbaar bij [Hoe maak je een telefoongesprek met Twilio in een Java-toepassing op Azure.][howto_phonecall_java]

## <a name="configure-your-application-to-use-twilio-libraries"></a><a id="configure_app"></a>Uw toepassing configureren om Twilio-bibliotheken te gebruiken
Binnen uw code u **importinstructies** toevoegen aan de bovenkant van uw bronbestanden voor de Twilio-pakketten of -klassen die u in uw toepassing wilt gebruiken.

Voor Java-bronbestanden:

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

Voor JSP-bronbestanden (Java Server Page):

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
Afhankelijk van welke Twilio-pakketten of klassen u wilt gebruiken, kunnen uw **importoverzichten** verschillen.

## <a name="how-to-make-an-outgoing-call"></a><a id="howto_make_call"></a>Hoe: Een uitgaande oproep voeren
Hieronder ziet u hoe u een uitgaande oproep voeren met de klasse **Bellen.** Deze code maakt ook gebruik van een door Twilio geleverde site om de Twilio Markup Language (TwiML) reactie terug te geven. Vervang uw waarden voor de **van** en **naar** telefoonnummers en zorg ervoor dat u het **telefoonnummer van** uw Twilio-account controleert voordat u de code uitvoert.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Zie [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]voor meer informatie over de parameters die zijn doorgegeven aan de methode **Call.creator** .

Zoals gezegd maakt deze code gebruik van een door Twilio geleverde site om de TwiML-respons terug te sturen. U in plaats daarvan uw eigen site gebruiken om de TwiML-respons te bieden; Zie [TwiML-antwoorden in een Java-toepassing op Azure](#howto_provide_twiml_responses)voor meer informatie.

## <a name="how-to-send-an-sms-message"></a><a id="howto_send_sms"></a>Hoe: Een sms-bericht verzenden
Hieronder ziet u hoe u een sms-bericht verzendt met de klasse **Message.** Het **nummer,** **4155992671,** wordt door Twilio geleverd voor proefaccounts om sms-berichten te verzenden. Het **nummer moet** worden geverifieerd voor uw Twilio-account voordat de code wordt uitgevoerd.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

Zie [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms]voor meer informatie over de parameters die zijn doorgegeven aan de methode **Message.creator** .

## <a name="how-to-provide-twiml-responses-from-your-own-website"></a><a id="howto_provide_twiml_responses"></a>How to: TwiML Reacties van uw eigen website
Wanneer uw toepassing een aanroep naar de Twilio API initieert, bijvoorbeeld via de **CallCreator.create-methode,** stuurt Twilio uw aanvraag naar een URL die naar verwachting een TwiML-antwoord zal retourneren. In het bovenstaande voorbeeld wordt de [https://twimlets.com/message][twimlet_message_url]door Twilio verstrekte URL gebruikt. (Hoewel TwiML is ontworpen voor gebruik door webservices, u de TwiML in uw browser bekijken. Klik bijvoorbeeld [https://twimlets.com/message][twimlet_message_url] om een leeg ** &lt;reactieelement&gt; ** te zien; klik in een [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world] ander voorbeeld op een ** &lt;reactieelement&gt; ** dat een ** &lt;element Weerzeggen&gt; ** bevat.)

In plaats van te vertrouwen op de door Twilio verstrekte URL, u uw eigen URL-site maken die HTTP-antwoorden retourneert. U de site maken in elke taal die HTTP-antwoorden retourneert. in dit onderwerp wordt ervan uitgegaan dat u de URL op een JSP-pagina host.

De volgende JSP pagina resulteert in een TwiML reactie die **Hello World zegt!** aan het gesprek.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

De volgende JSP-pagina resulteert in een TwiML-antwoord dat tekst zegt, verschillende pauzes heeft en informatie zegt over de Twilio API-versie en de Azure-rolnaam.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

De **parameter ApiVersion** is beschikbaar in Twilio-spraakaanvragen (geen sms-verzoeken). Zie <https://www.twilio.com/docs/api/twiml/twilio_request> en <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, om de beschikbare aanvraagparameters voor Twilio-spraak- en sms-verzoeken te bekijken. De functie **RoleName-omgeving** is beschikbaar als onderdeel van een Azure-implementatie. (Als u aangepaste omgevingsvariabelen wilt toevoegen zodat deze kunnen worden opgehaald bij **System.getenv,** raadpleegt u de sectie omgevingsvariabelen bij [Diverse functieconfiguratie-instellingen][misc_role_config_settings].)

Zodra u uw JSP-pagina hebt ingesteld om TwiML-antwoorden te geven, gebruikt u de URL van de JSP-pagina terwijl de URL wordt doorgegeven aan de **methode Call.creator.** Als u bijvoorbeeld een webtoepassing met de naam MyTwiML hebt geïmplementeerd op een Azure-gehoste service en de naam van de JSP-pagina mytwiml.jsp is, kan de URL worden doorgegeven aan **Call.creator** zoals weergegeven in het volgende:

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

Een andere optie om te reageren met TwiML is via de **VoiceResponse-klasse,** die beschikbaar is in het **com.twilio.twiml-pakket.**

Zie [Bellen met Twilio in een Java-toepassing op Azure][howto_phonecall_java]voor meer informatie over het gebruik van Twilio in Azure.

## <a name="how-to-use-additional-twilio-services"></a><a id="AdditionalServices"></a>Hoe: Extra Twilio-services gebruiken
Naast de voorbeelden die hier worden weergegeven, biedt Twilio webgebaseerde API's die u gebruiken om extra Twilio-functionaliteit uit uw Azure-toepassing te gebruiken. Zie de [Twilio API-documentatie][twilio_api_documentation]voor meer informatie.

## <a name="next-steps"></a><a id="NextSteps"></a>Volgende stappen
Nu je de basis van de Twilio-service hebt geleerd, volg je deze links om meer te weten te komen:

* [Twilio-beveiligingsrichtlijnen][twilio_security_guidelines]
* [Twilio HowTo's en Voorbeeldcode][twilio_howtos]
* [Twilio Quickstart-zelfstudies][twilio_quickstarts]
* [Twilio op GitHub][twilio_on_github]
* [Praat met Twilio Support][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
