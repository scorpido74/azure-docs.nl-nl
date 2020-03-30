---
title: Hoe maak je een telefoontje van Twilio (Java) | Microsoft Documenten
description: Meer informatie over het voeren van een telefoongesprek vanaf een webpagina met Twilio in een Java-toepassing op Azure.
services: ''
documentationcenter: java
author: georgewallace
ms.assetid: 0381789e-e775-41a0-a784-294275192b1d
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 168ec65cfd0ff4e87c33324daa353b554111c8aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73838555"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-java-application-on-azure"></a>Bellen met Twilio in een Java-toepassing op Azure
In het volgende voorbeeld ziet u hoe u Twilio gebruiken om te bellen vanaf een webpagina die wordt gehost in Azure. De resulterende toepassing zal de gebruiker om de waarden van het telefoongesprek vragen, zoals getoond in volgende screenshot.

![Azure-oproepformulier met Twilio en Java][twilio_java]

Je moet het volgende doen om de code in dit onderwerp te gebruiken:

1. Een Twilio-account en verificatietoken aanschaffen. Om aan de slag te gaan [https://www.twilio.com/pricing][twilio_pricing]met Twilio, evalueert u de prijzen op . U zich [https://www.twilio.com/try-twilio][try_twilio]aanmelden bij. Zie voor [https://www.twilio.com/api][twilio_api]informatie over de API van Twilio .
2. Verkrijg de Twilio JAR. Bij [https://github.com/twilio/twilio-java][twilio_java_github], u de GitHub bronnen downloaden en maak je eigen JAR, of download een pre-built JAR (met of zonder afhankelijkheden).
   De code in dit onderwerp is geschreven met behulp van de vooraf gebouwde TwilioJava-3.3.8-met-afhankelijkheden JAR.
3. Voeg de JAR toe aan je Java-bouwpad.
4. Als u Eclipse gebruikt om deze Java-toepassing te maken, neemt u de Twilio JAR op in uw toepassingsimplementatiebestand (WAR) met behulp van de functie implementatieassemblage van Eclipse. Als u Eclipse niet gebruikt om deze Java-toepassing te maken, moet u ervoor zorgen dat de Twilio JAR wordt opgenomen in dezelfde Azure-rol als uw Java-toepassing en wordt toegevoegd aan het klassenpad van uw toepassing.
5. Zorg ervoor dat uw cacerts keystore het Equifax Secure Certificate Authority-certificaat bevat met MD5-vingerafdruk 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (het serienummer is 35:DE:F4:CF en de SHA1-vingerafdruk is D2:32:09:AD:23:D4 3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Dit is het certificaat van de [https://api.twilio.com][twilio_api_service] certificaatautoriteit (CA) voor de service, dat wordt aangeroepen wanneer u Twilio API's gebruikt. Zie Een certificaat toevoegen aan de Java CA Certificate [Store][add_ca_cert]voor informatie over het toevoegen van dit CA-certificaat aan de cacert-winkel van uw JDK.

Bovendien is vertrouwdheid met de informatie bij [Het maken van een Hello World-toepassing met behulp van de Azure Toolkit voor Eclipse][azure_java_eclipse_hello_world], of met andere technieken voor het hosten van Java-toepassingen in Azure als u Eclipse niet gebruikt, een aanrader.

## <a name="create-a-web-form-for-making-a-call"></a>Een webformulier maken voor het voeren van een gesprek
In de volgende code ziet u hoe u een webformulier maakt om gebruikersgegevens op te halen voor het voeren van een gesprek. Voor de toepassing van dit voorbeeld is een nieuw dynamisch webproject, genaamd **TwilioCloud,** gemaakt en **is callform.jsp** toegevoegd als JSP-bestand.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Automated call form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Make this call</b>.</p>
     <br/>
      <form action="makecall.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="callTo" value="" />
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="callFrom" value="" />
           </td>
         </tr>
         <tr>
           <td>Call message:</td>
           <td><input type="text" size=400 name="callText" value="Hello. This is the call text. Good bye." />
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Make this call" />
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-make-the-call"></a>De code maken om te bellen
De volgende code, die wordt aangeroepen wanneer de gebruiker het formulier voltooid dat wordt weergegeven door callform.jsp, maakt het oproepbericht en genereert de oproep. Voor de toepassing van dit voorbeeld wordt het JSP-bestand **makecall.jsp** genoemd en is het toegevoegd aan het **TwilioCloud-project.** (Gebruik uw Twilio-account en verificatietoken in plaats van de tijdelijke aanduidingswaarden die zijn toegewezen aan **accountSID** en **authToken** in de onderstaande code.)

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
    import="java.util.*"
    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
    pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Call processing happens here</title>
    </head>
    <body>
        <b>This is my make call page.</b><p/>
     <%
    try 
    {
         // Use your account SID and authentication token instead
         // of the placeholders shown here.
         String accountSID = "your_twilio_account";
         String authToken = "your_twilio_authentication_token";

         // Instantiate an instance of the Twilio client.     
         TwilioRestClient client;
         client = new TwilioRestClient(accountSID, authToken);

         // Retrieve the account, used later to retrieve the CallFactory.
         Account account = client.getAccount();

         // Display the client endpoint. 
         out.println("<p>Using Twilio endpoint " + client.getEndpoint() + ".</p>");

         // Display the API version.
         String APIVERSION = TwilioRestClient.DEFAULT_VERSION;
         out.println("<p>Twilio client API version is " + APIVERSION + ".</p>");

         // Retrieve the values entered by the user.
         String callTo = request.getParameter("callTo");  
         // The Outgoing Caller ID, used for the From parameter,
         // must have previously been verified with Twilio.
         String callFrom = request.getParameter("callFrom");
         String userText = request.getParameter("callText");

         // Replace spaces in the user's text with '%20', 
         // to make the text suitable for a URL.
         userText = userText.replace(" ", "%20");

         // Create a URL using the Twilio message and the user-entered text.
         String Url="https://twimlets.com/message";
         Url = Url + "?Message%5B0%5D=" + userText;

         // Display the message URL.
         out.println("<p>");
         out.println("The URL is " + Url);
         out.println("</p>");

         // Place the call From, To and URL values into a hash map. 
         HashMap<String, String> params = new HashMap<String, String>();
         params.put("From", callFrom);
         params.put("To", callTo);
         params.put("Url", Url);

         CallFactory callFactory = account.getCallFactory();
         Call call = callFactory.create(params);
         out.println("<p>Call status: " + call.getStatus()  + "</p>"); 
    } 
    catch (TwilioRestException e) 
    {
        out.println("<p>TwilioRestException encountered: " + e.getMessage() + "</p>");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    catch (Exception e) 
    {
        out.println("<p>Exception encountered: " + e.getMessage() + "");
        out.println("<p>StackTrace: " + e.getStackTrace().toString() + "</p>");
    }
    %>
    </body>
    </html>

Naast het maken van de oproep, geeft makecall.jsp het Twilio-eindpunt, de API-versie en de aanroepstatus weer. Een voorbeeld is de volgende schermafbeelding:

![Azure-oproeprespons met Twilio en Java][twilio_java_response]

## <a name="run-the-application"></a>De toepassing uitvoeren
Hieronder volgen de stappen op hoog niveau om uw toepassing uit te voeren; details voor deze stappen zijn te vinden op [Het maken van een Hello World-toepassing met behulp van de Azure Toolkit voor Eclipse.][azure_java_eclipse_hello_world]

1. Exporteer uw TwilioCloud WAR naar de Map Azure **approot.** 
2. Wijzig **startup.cmd** om uw TwilioCloud WAR uit te pakken.
3. Compileer uw toepassing voor de compute emulator.
4. Start uw implementatie in de compute emulator.
5. Open een browser `http://localhost:8080/TwilioCloud/callform.jsp`en voer uit.
6. Voer waarden in het formulier in, klik op **Deze aanroep voeren**en bekijk de resultaten in makecall.jsp.

Wanneer u klaar bent om te implementeren in Azure, hercompileert u opnieuw voor implementatie naar de cloud, implementeert u deze naar Azure en voert u http://*your_hosted_name*.cloudapp.net/TwilioCloud/callform.jsp in de browser uit (uw waarde vervangen door *your_hosted_name).*

## <a name="next-steps"></a>Volgende stappen
Deze code is beschikbaar gesteld om u basisfunctionaliteit te laten zien met Twilio in Java op Azure. Voordat u in productie naar Azure gaat, u meer foutafhandeling of andere functies toevoegen. Bijvoorbeeld:

* In plaats van een webformulier te gebruiken, u Azure-opslagblobs of SQL Database gebruiken om telefoonnummers en beltekst op te slaan. Zie De Blob Storage Service gebruiken vanaf Java voor informatie over het gebruik van Azure-opslagblobs in [Java.][howto_blob_storage_java] 
* U **RoleEnvironment.getConfigurationSettings** gebruiken om de Twilio-account-id en het verificatietoken op te halen uit de configuratie-instellingen van uw implementatie, in plaats van de waarden in makecall.jsp hard te coderen. Zie [De Azure Service Runtime Library gebruiken in JSP][azure_runtime_jsp]voor informatie over de klasse **RoleEnvironment.**
* De code makecall.jsp kent een door Twilio verstrekte URL [https://twimlets.com/message][twimlet_message_url]toe aan de variabele **Url.** Deze URL biedt een Twilio Markup Language (TwiML) reactie die Twilio informeert hoe verder te gaan met de oproep. De TwiML die wordt geretourneerd, kan bijvoorbeeld een ** &lt;werkwoord Zeggen&gt; ** bevatten dat resulteert in tekst die met de ontvanger van de oproep wordt gesproken. In plaats van de door Twilio verstrekte URL te gebruiken, u uw eigen service bouwen om te reageren op het verzoek van Twilio; voor meer informatie, zie [Hoe Twilio te gebruiken voor spraak- en sms-mogelijkheden in Java.][howto_twilio_voice_sms_java] Meer informatie over TwiML [https://www.twilio.com/docs/api/twiml][twiml]is te vinden op , en meer informatie over [https://www.twilio.com/docs/api/twiml/say][twilio_say] ** &lt;Say&gt; ** en andere Twilio werkwoorden is te vinden op .
* Lees de beveiligingsrichtlijnen van [https://www.twilio.com/docs/security][twilio_docs_security]Twilio op .

Zie voor meer informatie over [https://www.twilio.com/docs][twilio_docs]Twilio.

## <a name="see-also"></a>Zie ook
* [Hoe Twilio te gebruiken voor spraak- en sms-mogelijkheden op Java][howto_twilio_voice_sms_java]
* [Een certificaat toevoegen aan de Java CA-certificaatarchief][add_ca_cert]

[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_java_github]: https://github.com/twilio/twilio-java
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[azure_java_eclipse_hello_world]: https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app 
[howto_twilio_voice_sms_java]: partner-twilio-java-how-to-use-voice-sms.md
[howto_blob_storage_java]: https://www.windowsazure.com/develop/java/how-to-guides/blob-storage/
[howto_sql_azure_java]: https://msdn.microsoft.com/library/windowsazure/hh749029.aspx
[azure_runtime_jsp]: https://msdn.microsoft.com/library/windowsazure/hh690948.aspx
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[twilio_java]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaCallForm.jpg
[twilio_java_response]: ./media/partner-twilio-java-phone-call-example/WA_TwilioJavaMakeCall.jpg
