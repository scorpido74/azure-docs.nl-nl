---
title: store-sendgrid-java-how-to-send-email-voorbeeld
description: E-mail verzenden via SendGrid vanuit Java in een Azure-implementatie
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: af096a73-6985-4350-92e4-ce1722c8d72f
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: vibhork;dominic.may@sendgrid.com;elmer.thomas@sendgrid.com
ms.openlocfilehash: 35307848c09391ae4468afc00adafd8171aaaa7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876487"
---
# <a name="how-to-send-email-using-sendgrid-from-java-in-an-azure-deployment"></a>E-mail verzenden via SendGrid vanuit Java in een Azure-implementatie
In het volgende voorbeeld ziet u hoe u SendGrid gebruiken om e-mails te verzenden vanaf een webpagina die in Azure wordt gehost. De resulterende toepassing zal de gebruiker om e-mailwaarden vragen, zoals getoond in de volgende screenshot.

![E-mailformulier][emailform]

De resulterende e-mail ziet er vergelijkbaar uit als de volgende screenshot.

![E-mailbericht][emailsent]

Je moet het volgende doen om de code in dit onderwerp te gebruiken:

1. Verkrijg de javax.mail JARs, bijvoorbeeld van <https://www.oracle.com/technetwork/java/javamail/index.html>.
2. Voeg de JARs toe aan uw Java-buildpad.
3. Als u Eclipse gebruikt om deze Java-toepassing te maken, u de SendGrid-bibliotheken opnemen in het implementatiebestand (WAR) van uw toepassing met behulp van de implementatieassemblagefunctie van Eclipse. Als u Eclipse niet gebruikt om deze Java-toepassing te maken, moet u ervoor zorgen dat de bibliotheken in dezelfde Azure-rol als uw Java-toepassing worden opgenomen en aan het klassenpad van uw toepassing worden toegevoegd.

Je moet ook je eigen SendGrid gebruikersnaam en wachtwoord hebben, om de e-mail te kunnen verzenden. Zie [E-mail verzenden via SendGrid vanaf Java](store-sendgrid-java-how-to-send-email.md)om aan de slag te gaan met SendGrid.

Bovendien wordt vertrouwdheid met de informatie bij [Het maken van een Hello World-toepassing voor Azure in Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app?view=azure-java-stable)of met andere technieken voor het hosten van Java-toepassingen in Azure als u Eclipse niet gebruikt, een aanrader.

## <a name="create-a-web-form-for-sending-email"></a>Een webformulier maken voor het verzenden van e-mail
In de volgende code ziet u hoe u een webformulier maakt om gebruikersgegevens op te halen voor het verzenden van e-mail. Voor deze inhoud wordt het JSP-bestand **emailform.jsp**genoemd.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email form</title>
    </head>
    <body>
     <p>Fill in all fields and click <b>Send this email</b>.</p>
     <br/>
      <form action="sendemail.jsp" method="post">
       <table>
         <tr>
           <td>To:</td>
           <td><input type="text" size=50 name="emailTo">
           </td>
         </tr>
         <tr>
           <td>From:</td>
           <td><input type="text" size=50 name="emailFrom">
           </td>
         </tr>
         <tr>
           <td>Subject:</td>
           <td><input type="text" size=100 name="emailSubject" value="My email subject">
           </td>
         </tr>
         <tr>
           <td>Text:</td>
           <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
           </td>
         </tr>
         <tr>
           <td>SendGrid user name:</td>
           <td><input type="text" name="sendGridUser">
           </td>
         </tr>
         <tr>
           <td>SendGrid password:</td>
           <td><input type="password" name="sendGridPassword">
           </td>
         </tr>
         <tr>
           <td colspan=2><input type="submit" value="Send this email">
           </td>
         </tr>
       </table>
     </form>
     <br/>
    </body>
    </html>

## <a name="create-the-code-to-send-the-email"></a>De code maken om de e-mail te verzenden
De volgende code, die wordt aangeroepen wanneer u het formulier ine-mailform.jsp invult, maakt het e-mailbericht en verzendt het. Voor deze inhoud wordt het JSP-bestand **sendemail.jsp**genoemd.

    <%@ page language="java" contentType="text/html; charset=ISO-8859-1"
        pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Email processing happens here</title>
    </head>
    <body>
        <b>This is my send mail page.</b><p/>
     <%

     final String sendGridUser = request.getParameter("sendGridUser");
     final String sendGridPassword = request.getParameter("sendGridPassword");

     class SMTPAuthenticator extends Authenticator
     {
       public PasswordAuthentication getPasswordAuthentication()
       {
            String username = sendGridUser;
            String password = sendGridPassword;

            return new PasswordAuthentication(username, password);   
       }
     }
     try
     {

         // The SendGrid SMTP server.
         String SMTP_HOST_NAME = "smtp.sendgrid.net";

         Properties properties;

         properties = new Properties();

         // Specify SMTP values.
         properties.put("mail.transport.protocol", "smtp");
         properties.put("mail.smtp.host", SMTP_HOST_NAME);
         properties.put("mail.smtp.port", 587);
         properties.put("mail.smtp.auth", "true");

         // Display the email fields entered by the user. 
         out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
         out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
         out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
         out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");

         // Create the authenticator object.
         Authenticator authenticator = new SMTPAuthenticator();

         // Create the mail session object.
         Session mailSession;
         mailSession = Session.getDefaultInstance(properties, authenticator);

         // Display debug information to stdout, useful when using the
         // compute emulator during development.
         mailSession.setDebug(true);

         // Create the message and message part objects.
         MimeMessage message;
         Multipart multipart;
         MimeBodyPart messagePart; 

         message = new MimeMessage(mailSession);

         multipart = new MimeMultipart("alternative");
         messagePart = new MimeBodyPart();
         messagePart.setContent(request.getParameter("emailText"), "text/html");
         multipart.addBodyPart(messagePart);            

         // Specify the email To, From, Subject and Content. 
         message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
         message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
         message.setSubject(request.getParameter("emailSubject")); 
         message.setContent(multipart);

         // Uncomment the following if you want to add a footer.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");

         // Uncomment the following if you want to enable click tracking.
         // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");

         Transport transport;
         transport = mailSession.getTransport();
         // Connect the transport object.
         transport.connect();
         // Send the message.
         transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
         // Close the connection.
         transport.close();

        out.println("<p>Email processing completed.</p>");

     }
     catch (Exception e)
     {
         out.println("<p>Exception encountered: " + 
                            e.getMessage()     +
                            "</p>");   
     }
    %>

    </body>
    </html>

Naast het verzenden van de e-mail, e-mailform.jsp biedt een resultaat voor de gebruiker; een voorbeeld is de volgende schermafbeelding:

![E-mailresultaat verzenden][emailresult]

## <a name="next-steps"></a>Volgende stappen
Implementeer uw toepassing naar de compute emulator en voer in een browser run emailform.jsp waarden in het formulier in, klik op **Deze e-mail verzenden**en bekijk vervolgens de resultaten in sendemail.jsp.

Deze code is beschikbaar gesteld om u te laten zien hoe u SendGrid in Java op Azure gebruiken. Voordat u in productie naar Azure gaat, u meer foutafhandeling of andere functies toevoegen. Bijvoorbeeld: 

* U Azure storage blobs of SQL Database gebruiken om e-mailadressen en e-mailberichten op te slaan, in plaats van een webformulier te gebruiken. Zie De Blob Storage Service gebruiken vanaf Java voor informatie over het gebruik van Azure-opslagblobs in [Java.](https://azure.microsoft.com/develop/java/how-to-guides/blob-storage/) Zie SQL Database gebruiken in Java voor informatie over het gebruik van SQL Database [in Java.](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-java)
* Zie [E-mail verzenden via SendGrid vanaf Java](store-sendgrid-java-how-to-send-email.md)voor meer informatie over het gebruik van SendGrid in Java.

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg
