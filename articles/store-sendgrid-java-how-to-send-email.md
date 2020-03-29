---
title: De e-mailservice SendGrid (Java) gebruiken | Microsoft Documenten
description: Meer informatie over het verzenden van e-mail met de e-mailservice SendGrid op Azure. Code monsters geschreven in Java.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 8ae948e9c79cff4cd0c896b250743fd9dc521752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876515"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>E-mail verzenden via SendGrid vanuit Java
In deze handleiding wordt uitgelegd hoe u veelvoorkomende programmeertaken uitvoeren met de e-mailservice SendGrid op Azure. De monsters zijn geschreven in Java. De scenario's die onder meer worden behandeld, zijn **het maken van e-mail,** **het verzenden van e-mail,** **het toevoegen van bijlagen,** **het gebruik van filters**en het bijwerken **van eigenschappen**. Zie de sectie [Volgende stappen](#next-steps) voor meer informatie over SendGrid en het verzenden van e-mail.

## <a name="what-is-the-sendgrid-email-service"></a>Wat is de SendGrid Email Service?
SendGrid is een [cloudgebaseerde e-mailservice] die betrouwbare [transactionele e-maillevering,]schaalbaarheid en realtime analyses biedt, samen met flexibele API's die aangepaste integratie eenvoudig maken. Veelvoorkomende sendgrid-gebruiksscenario's zijn:

* Automatisch ontvangstbewijzen naar klanten verzenden
* Het beheren van distributielijsten voor het verzenden van klanten maandelijkse e-flyers en speciale aanbiedingen
* Realtime statistieken verzamelen voor zaken als geblokkeerde e-mail en reactievermogen van klanten
* Rapporten genereren om trends te identificeren
* Vragen van klanten doorsturen
* E-mailmeldingen van uw toepassing

Zie <https://sendgrid.com> voor meer informatie.

## <a name="create-a-sendgrid-account"></a>Een SendGrid-account maken
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>How to: Gebruik de javax.mail bibliotheken
Verkrijg de javax.mail bibliotheken, <https://www.oracle.com/technetwork/java/javamail> bijvoorbeeld van en importeer ze in uw code. Op hoog niveau is het volgende proces voor het gebruik van de javax.mail-bibliotheek om e-mail te verzenden met SMTP:

1. Geef de SMTP-waarden op, inclusief de SMTP-server, die voor SendGrid smtp.sendgrid.net is.

```
        import java.util.Properties;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. Breid de *klasse javax.mail.Authenticator* uit en retourneer bij de implementatie van de *getPasswordAuthentication-methode* uw SendGrid-gebruikersnaam en -wachtwoord.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Maak een geverifieerde e-mailsessie via een *object javax.mail.Session.*  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Maak uw bericht en wijs **aan**, **Van**, **Onderwerp** en inhoudwaarden toe. Dit wordt weergegeven in de sectie [Hoe: Een e-mail maken.](#how-to-create-an-email)
4. Stuur het bericht via een *object javax.mail.Transport.* Dit wordt weergegeven in de sectie [Hoe: Een e-mail verzenden][#how-naar-verzenden-een-e-mail] wordt weergegeven.

## <a name="how-to-create-an-email"></a>Hoe: Een e-mail maken
Hieronder ziet u hoe u waarden voor een e-mail opgeeft.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>Hoe: Een e-mail verzenden
Hieronder ziet u hoe u een e-mail verzendt.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Hoe: Een bijlage toevoegen
In de volgende code ziet u hoe u een bijlage toevoegt.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Hoe: Filters gebruiken om voetteksten, tracking en analyses in te schakelen
SendGrid biedt extra e-mailfunctionaliteit door het gebruik van *filters.* Dit zijn instellingen die kunnen worden toegevoegd aan een e-mailbericht om specifieke functionaliteit in te schakelen, zoals het inschakelen van kliktracking, Google analytics, het bijhouden van abonnementen, enzovoort. Zie [Filterinstellingen][Filter Settings]voor een volledige lijst met filters .

* Het volgende laat zien hoe u een voettekstfilter invoegt dat resulteert in HTML-tekst die onder aan de verzonden e-mail wordt weergegeven.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Een ander voorbeeld van een filter is click tracking. Stel dat uw e-mailtekst een hyperlink bevat, zoals de volgende, en dat u de klikfrequentie wilt bijhouden:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* Gebruik de volgende code om de kliktracking in te schakelen:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Instellen: e-maileigenschappen bijwerken
Sommige e-maileigenschappen kunnen worden overschreven met **de eigenschap set** of worden toegevoegd met eigenschap **toevoegen**.

Als u bijvoorbeeld **ReplyTo-adressen** wilt opgeven, gebruikt u het volgende:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

Als u een **Cc-ontvanger** wilt toevoegen, gebruikt u het volgende:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>How to: Extra SendGrid-services gebruiken
SendGrid biedt webgebaseerde API's die u gebruiken om extra SendGrid-functionaliteit uit uw Azure-toepassing te gebruiken. Zie de [SendGrid API-documentatie][SendGrid API documentation]voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Nu u de basisbeginselen van de SendGrid-e-mailservice hebt geleerd, volgt u deze koppelingen voor meer informatie.

* Voorbeeld dat aantoont dat SendGrid wordt gebruikt in een Azure-implementatie: [E-mail verzenden met SendGrid vanuit Java in een Azure-implementatie](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK:<https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API-documentatie:<https://sendgrid.com/docs/API_Reference/index.html>
* Speciale sendGrid-aanbieding voor Azure-klanten:<https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[cloudgebaseerde e-mailservice]: https://sendgrid.com/email-solutions
[transactionele e-mailbezorging]: https://sendgrid.com/transactional-email
