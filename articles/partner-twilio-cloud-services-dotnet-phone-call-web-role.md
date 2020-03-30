---
title: Bellen met Twilio (.NET) | Microsoft Documenten
description: Meer informatie over het voeren van een telefoongesprek en het verzenden van een sms-bericht met de Twilio API-service op Azure. Codevoorbeelden geschreven in .NET.
services: ''
documentationcenter: .net
author: georgewallace
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: gwallace
ms.openlocfilehash: 27b4f3cdd8f622a97cfc0853f79bb77d76673dcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69636141"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Bellen met Twilio in een webrol op Azure
In deze handleiding wordt uitgelegd hoe u Twilio gebruiken om te bellen vanaf een webpagina die wordt gehost in Azure. De resulterende toepassing vraagt de gebruiker om een gesprek te voeren met het opgegeven nummer en bericht, zoals weergegeven in de volgende screenshot.

![Azure-oproepformulier met Twilio en ASP.NET][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>Vereisten
U moet het volgende doen om de code in dit onderwerp te gebruiken:

1. Een Twilio-account en verificatietoken verkrijgen van de [Twilio-console.][twilio_console] Om aan de slag te gaan [https://www.twilio.com/try-twilio][try_twilio]met Twilio, meld je aan bij . U de [https://www.twilio.com/pricing][twilio_pricing]prijzen evalueren op. Zie voor [https://www.twilio.com/voice/api][twilio_api]informatie over de API van Twilio .
2. Voeg de *Twilio .NET-bibliotheek* toe aan uw webrol. Zie **De Twilio-bibliotheken toevoegen aan uw webrolproject**, later in dit onderwerp.

U moet bekend zijn met het maken van een [basiswebrol op Azure.][azure_webroles_get_started]

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>How to: Een webformulier maken voor het voeren van een gesprek
<a id="use_nuget"></a>Ga als u de Twilio-bibliotheken toevoegen aan uw webrolproject:

1. Open uw oplossing in Visual Studio.
2. Klik met de rechtermuisknop **op Verwijzingen**.
3. Klik **op NuGet-pakketten beheren**.
4. Klik **op Online**.
5. Typ *twilio*in het online zoekvak .
6. Klik **op Installeren** op het Twilio-pakket.

In de volgende code ziet u hoe u een webformulier maakt om gebruikersgegevens op te halen voor het voeren van een gesprek. In dit voorbeeld wordt een ASP.NET webrol met de naam **TwilioCloud** gemaakt.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>Hoe: De code maken om te bellen
De volgende code, die wordt aangeroepen wanneer de gebruiker het formulier voltooit, maakt het oproepbericht en genereert de oproep. In dit voorbeeld wordt de code uitgevoerd in de gebeurtenishandler onclick van de knop op het formulier. (Gebruik uw Twilio-account en verificatietoken in `accountSID` plaats `authToken` van de tijdelijke aanduidingswaarden die zijn toegewezen aan en in de onderstaande code.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

De aanroep wordt uitgevoerd en het Twilio-eindpunt, de API-versie en de aanroepstatus worden weergegeven. De volgende schermafbeelding toont de uitvoer van een voorbeeldrun.

![Azure-oproeprespons met Twilio en ASP.NET][twilio_dotnet_basic_form_output]

Meer informatie over TwiML [https://www.twilio.com/docs/api/twiml][twiml]is te vinden op . Meer informatie &lt;&gt; over Say en andere Twilio [https://www.twilio.com/docs/api/twiml/say][twilio_say]werkwoorden is te vinden op .

## <a name="next-steps"></a><a id="nextsteps"></a>Volgende stappen
Deze code is beschikbaar om u basisfunctionaliteit te laten zien met Twilio in een ASP.NET webrol op Azure. Voordat u in productie naar Azure gaat, u meer foutafhandeling of andere functies toevoegen. Bijvoorbeeld:

* In plaats van een webformulier te gebruiken, u Azure Blob-opslag of een Azure SQL Database-instantie gebruiken om telefoonnummers en oproeptekst op te slaan. Zie [De Azure Blob-opslagservice gebruiken in .NET][howto_blob_storage_dotnet]voor informatie over het gebruik van Blobs in Azure. Zie Azure SQL Database [gebruiken in .NET-toepassingen][howto_sql_azure_dotnet]voor informatie over het gebruik van SQL Database.
* U kunt `RoleEnvironment.getConfigurationSettings` de Twilio-account-id en het verificatietoken ophalen uit de configuratie-instellingen van uw implementatie, in plaats van de waarden in uw formulier hard te coderen. Zie `RoleEnvironment` [Microsoft.WindowsAzure.ServiceRuntime Namespace][azure_runtime_ref_dotnet]voor informatie over de klasse .
* Lees de Twilio-beveiligingsrichtlijnen op [https://www.twilio.com/docs/security][twilio_docs_security].
* Meer informatie over Twilio op [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a><a name="seealso"></a>Zie ook
* [Twilio gebruiken voor spraak- en sms-mogelijkheden vanuit Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
