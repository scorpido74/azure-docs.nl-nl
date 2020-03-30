---
title: De e-mailservice SendGrid gebruiken (.NET) | Microsoft Documenten
description: Meer informatie over het verzenden van e-mail met de e-mailservice SendGrid op Azure. Codevoorbeelden geschreven in C# en gebruik de .NET API.
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.openlocfilehash: 33df6b5c8c5c16a6eb896944de05068affc2b407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062202"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>E-mail verzenden met SendGrid met Azure
## <a name="overview"></a>Overzicht
In deze handleiding wordt uitgelegd hoe u veelvoorkomende programmeertaken uitvoeren met de e-mailservice SendGrid op Azure. De samples zijn\# geschreven in C en ondersteunen .NET Standard 1.3. De scenario's die onder meer worden behandeld, zijn het maken van e-mail, het verzenden van e-mail, het toevoegen van bijlagen en het inschakelen van verschillende e-mail- en trackinginstellingen. Zie de sectie [Volgende stappen][Next steps] voor meer informatie over SendGrid en het verzenden van e-mail.

## <a name="what-is-the-sendgrid-email-service"></a>Wat is de SendGrid Email Service?
SendGrid is een [cloudgebaseerde e-mailservice] die betrouwbare [transactionele e-maillevering,]schaalbaarheid en realtime analyses biedt, samen met flexibele API's die aangepaste integratie eenvoudig maken. Veelvoorkomende SendGrid-gebruiksvoorbeelden zijn:

* Automatisch ontvangstbewijzen of aankoopbevestigingen naar klanten verzenden.
* Het beheren van distributielijsten voor het verzenden van klanten maandelijkse flyers en promoties.
* Het verzamelen van realtime statistieken voor zaken als geblokkeerde e-mail en klantbetrokkenheid.
* Het doorsturen van vragen van klanten.
* Inkomende e-mails verwerken.

Ga voor meer [https://sendgrid.com](https://sendgrid.com) informatie naar de [C#-bibliotheek][sendgrid-csharp] GitHub repo van SendGrid of SendGrid.

## <a name="create-a-sendgrid-account"></a>Een SendGrid-account maken
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Verwijzen naar de SendGrid .NET-klassebibliotheek
Het [SendGrid NuGet-pakket](https://www.nuget.org/packages/Sendgrid) is de eenvoudigste manier om de SendGrid API te krijgen en uw toepassing te configureren met alle afhankelijkheden. NuGet is een Visual Studio-extensie die is meegeleverd met Microsoft Visual Studio 2015 en hoger, waardoor het eenvoudig is om bibliotheken en hulpprogramma's te installeren en bij te werken.

> [!NOTE]
> Als u NuGet wilt installeren als u een versie van Visual Studio [https://www.nuget.org](https://www.nuget.org)eerder uitvoert dan Visual Studio 2015, gaat u naar NuGet en klikt u op **nuget installeren.**
>
>

Ga als volgt te werk om het SendGrid NuGet-pakket in uw toepassing te installeren:

1. Klik op **Nieuw project** en selecteer een **sjabloon**.

   ![Een nieuw project maken][create-new-project]
2. Klik in **Solution Explorer**met de rechtermuisknop **op Verwijzingen**en klik vervolgens op **NuGet-pakketten beheren**.

   ![SendGrid NuGet-pakket][SendGrid-NuGet-package]
3. Zoek **naar SendGrid** en selecteer het item **SendGrid** in de resultatenlijst.
4. Selecteer de nieuwste stabiele versie van het Nuget-pakket in de vervolgkeuzelijst van de versie om te kunnen werken met het objectmodel en API's die in dit artikel worden gedemonstreerd.

   ![SendGrid-pakket][sendgrid-package]
5. Klik **op Installeren** om de installatie te voltooien en sluit dit dialoogvenster.

De .NET-klassenbibliotheek van SendGrid heet **SendGrid**. Het bevat de volgende naamruimten:

* **SendGrid** voor communicatie met de API van SendGrid.
* **SendGrid.Helpers.Mail** voor helpermethoden om eenvoudig SendGridMessage-objecten te maken waarin wordt aangegeven hoe u e-mails verzendt.

Voeg de volgende codenaamruimtedeclaratie toe aan de bovenkant van een C#-bestand waarin u programmatisch toegang wilt krijgen tot de e-mailservice SendGrid.

    using SendGrid;
    using SendGrid.Helpers.Mail;

## <a name="how-to-create-an-email"></a>Hoe: Een e-mail maken
Gebruik het object **SendGridMessage** om een e-mailbericht te maken. Zodra het berichtobject is gemaakt, u eigenschappen en methoden instellen, waaronder de e-mailafzender, de e-mailontvanger en het onderwerp en de hoofdtekst van de e-mail.

In het volgende voorbeeld wordt uitgelegd hoe u een volledig ingevuld e-mailobject maakt:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress("jeff@example.com", "Jeff Smith"),
        new EmailAddress("anna@example.com", "Anna Lidman"),
        new EmailAddress("peter@example.com", "Peter Saddow")
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

Zie [sendgrid-csharp][sendgrid-csharp] op GitHub voor meer informatie over alle eigenschappen en methoden die worden ondersteund door het type **SendGrid.**

## <a name="how-to-send-an-email"></a>Hoe: Een e-mail verzenden
Nadat u een e-mailbericht hebt gemaakt, u het verzenden via de API van SendGrid. U ook [. NET is ingebouwde bibliotheek.][NET-library]

Voor het verzenden van e-mail moet u uw SendGrid API-sleutel leveren. Als u informatie wilt over het configureren van API-sleutels, gaat u naar de [API-sleutelsdocumentatie][documentation]van SendGrid.

U deze referenties opslaan via uw Azure-portal door op Toepassingsinstellingen te klikken en de sleutel-/waardeparen toe te voegen onder App-instellingen.

 ![Azure-app-instellingen][azure_app_settings]

 Vervolgens u ze als volgt openen:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

In de volgende voorbeelden ziet u hoe u een e-mailbericht verzendt met de SendGrid Web API met een consoletoepassing.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }
    
## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>How to: E-mail verzenden vanuit ASP .NET Core API met de klasse MailHelper

Het onderstaande voorbeeld kan worden gebruikt om één e-mail te verzenden `MailHelper` naar `SendGrid.Helpers.Mail` meerdere personen uit de ASP .NET Core API met behulp van de klasse van de naamruimte. Voor dit voorbeeld gebruiken we ASP .NET Core 1.0. 

In dit voorbeeld is de API-sleutel opgeslagen in het `appsettings.json` bestand, dat kan worden overschreven vanuit de Azure-portal, zoals in de bovenstaande voorbeelden wordt weergegeven.

De inhoud `appsettings.json` van het bestand moet er hetzelfde uitzien als:

    {
       "Logging": {
       "IncludeScopes": false,
       "LogLevel": {
       "Default": "Debug",
       "System": "Information",
       "Microsoft": "Information"
         }
       },
     "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }

Eerst moeten we de onderstaande `Startup.cs` code toevoegen in het bestand van het .NET Core API-project. Dit is vereist zodat we `SENDGRID_API_KEY` toegang `appsettings.json` hebben tot het bestand met behulp van afhankelijkheidsinjectie in de API-controller. De `IConfiguration` interface kan worden geïnjecteerd bij de constructeur `ConfigureServices` van de controller na het toevoegen van het in de onderstaande methode. De inhoud `Startup.cs` van het bestand ziet er als volgt uit na het toevoegen van de vereiste code:

        public IConfigurationRoot Configuration { get; }

        public void ConfigureServices(IServiceCollection services)
        {
            // Add mvc here
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }

Bij de controller kunnen `IConfiguration` we, na het `CreateSingleEmailToMultipleRecipients` injecteren `MailHelper` van de interface, de methode van de klasse gebruiken om één e-mail naar meerdere ontvangers te sturen. De methode accepteert een extra `showAllRecipients`booleaanse parameter met de naam . Deze parameter kan worden gebruikt om te bepalen of e-mailontvangers elkaars e-mailadres kunnen zien in de sectie Aan van e-mailheader. De voorbeeldcode voor de controller moet zijn zoals hieronder 

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using SendGrid;
    using SendGrid.Helpers.Mail;
    using Microsoft.Extensions.Configuration;

    namespace SendgridMailApp.Controllers
    {
        [Route("api/[controller]")]
        public class NotificationController : Controller
        {
           private readonly IConfiguration _configuration;

           public NotificationController(IConfiguration configuration)
           {
             _configuration = configuration;
           }      
        
           [Route("SendNotification")]
           public async Task PostMessage()
           {
              var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
              var client = new SendGridClient(apiKey);
              var from = new EmailAddress("test1@example.com", "Example User 1");
              List<EmailAddress> tos = new List<EmailAddress>
              {
                  new EmailAddress("test2@example.com", "Example User 2"),
                  new EmailAddress("test3@example.com", "Example User 3"),
                  new EmailAddress("test4@example.com","Example User 4")
              };
            
              var subject = "Hello world email from Sendgrid ";
              var htmlContent = "<strong>Hello world with HTML content</strong>";
              var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
              var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
              var response = await client.SendEmailAsync(msg);
          }
       }
    }
    
## <a name="how-to-add-an-attachment"></a>Hoe: Een bijlage toevoegen
Bijlagen kunnen aan een bericht worden toegevoegd door de **methode Addattachment aan** te roepen en minimaal de bestandsnaam en base64-gecodeerde inhoud op te geven die u wilt toevoegen. U meerdere bijlagen opnemen door deze methode eenmalig aan te roepen voor elk bestand dat u wilt toevoegen of met de methode **Bijlagen toevoegen.** In het volgende voorbeeld wordt het toevoegen van een bijlage aan een bericht weergegeven:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Hoe: E-mailinstellingen gebruiken om voetteksten, tracking en analyses in te schakelen
SendGrid biedt extra e-mailfunctionaliteit door het gebruik van e-mailinstellingen en tracking-instellingen. Deze instellingen kunnen worden toegevoegd aan een e-mailbericht om specifieke functionaliteit in te schakelen, zoals click tracking, Google Analytics, abonnementstracking, enzovoort. Zie de [instellingendocumentatie][settings-documentation]voor een volledige lijst met apps.

Apps kunnen worden toegepast op **E-mailberichten van SendGrid** met methoden die zijn geïmplementeerd als onderdeel van de klasse **SendGridMessage.** In de volgende voorbeelden worden de filterfilters voor het bijhouden van de voettekst en klikken gedemonstreerd:

In de volgende voorbeelden worden de filterfilters voor het bijhouden van de voettekst en klikken gedemonstreerd:

### <a name="footer-settings"></a>Voettekstinstellingen
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Klik op bijhouden
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Hoe: Extra SendGrid-services gebruiken
SendGrid biedt verschillende API's en webhooks die u gebruiken om extra functionaliteit binnen uw Azure-toepassing te gebruiken. Zie de [SendGrid API-verwijzing][SendGrid API documentation]voor meer informatie .

## <a name="next-steps"></a>Volgende stappen
Nu u de basisbeginselen van de SendGrid-e-mailservice hebt geleerd, volgt u deze koppelingen voor meer informatie.

* SendGrid\# C bibliotheek repo: [sendgrid-csharp][sendgrid-csharp]
* SendGrid API-documentatie:<https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[cloudgebaseerde e-mailservice]: https://sendgrid.com/solutions
[transactionele e-mailbezorging]: https://sendgrid.com/use-cases/transactional-email

