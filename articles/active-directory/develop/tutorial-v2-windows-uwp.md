---
title: Aan de slag met micro soft Identity platform UWP | Azure
description: Hoe Universeel Windows-platform-toepassingen (UWP) een API kunnen aanroepen die toegangs tokens vereist voor het micro soft Identity platform-eind punt.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2a243b11c2789afe0b2eb7ffd8de032dc10d8d8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423338"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Microsoft Graph-API aanroepen vanuit een Universeel Windows-platform toepassing (XAML)

> [!div renderon="docs"]

In deze hand leiding wordt uitgelegd hoe een systeem eigen Universeel Windows-platform-toepassing (UWP) een toegangs token kan aanvragen. De toepassing roept vervolgens Microsoft Graph-API aan. De hand leiding is ook van toepassing op andere Api's waarvoor toegangs tokens van het micro soft Identity platform-eind punt zijn vereist.

Aan het einde van deze hand leiding roept uw toepassing een beveiligde API aan met persoonlijke accounts. Voor beelden zijn outlook.com, live.com en anderen. Uw toepassing roept ook werk-en school accounts aan van een bedrijf of organisatie met Azure Active Directory (Azure AD).

>[!NOTE]
> Voor deze hand leiding moet Visual Studio met Universeel Windows-platform Development zijn geïnstalleerd. Zie [instellen](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) voor instructies voor het downloaden en configureren van Visual Studio voor het ontwikkelen van universeel Windows-platform-apps.

## <a name="how-this-guide-works"></a>De werking van deze handleiding

![Toont hoe de voor beeld-app die door deze zelf studie wordt gegenereerd, werkt](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

In deze hand leiding wordt een voorbeeld toepassing voor UWP gemaakt waarmee Microsoft Graph-API wordt opgevraagd. Voor dit scenario wordt een token toegevoegd aan HTTP-aanvragen via de autorisatie-header. Micro soft Authentication Library (MSAL) verwerkt verwervingen en vernieuwingen van tokens.

## <a name="nuget-packages"></a>NuGet-pakketten

In deze hand leiding wordt het volgende NuGet-pakket gebruikt:

|Bibliotheek|Beschrijving|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>Uw project instellen

In deze sectie vindt u stapsgewijze instructies voor het integreren van een Windows Desktop .NET-toepassing (XAML) met aanmelden bij micro soft. Vervolgens kan de toepassing Web-Api's opvragen waarvoor een token is vereist, zoals Microsoft Graph-API.

In deze hand leiding wordt een toepassing gemaakt waarmee een knop wordt weer gegeven die Graph API en een knop om u af te melden. Er worden ook tekst vakken weer gegeven die de resultaten van de aanroepen bevatten.

> [!NOTE]
> Wilt u het Visual Studio-project van dit voor beeld downloaden in plaats van het te maken? [Down load een project](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) en ga naar de stap [toepassings registratie](#register-your-application "stap toepassings registratie") om het code voorbeeld te configureren voordat het wordt uitgevoerd.

### <a name="create-your-application"></a>Uw toepassing maken

1. Open Visual Studio en selecteer **een nieuw project maken**.
1. Kies in **een nieuw project maken de**optie **lege app (universeel Windows)** voor C# en selecteer **volgende**.
1. In **uw nieuwe project configureren**, de app een naam en selecteer **maken**.
1. Als u hierom wordt gevraagd, selecteert u in **nieuw universeel Windows-platform project**een wille keurige versie voor de **doel** -en **minimum** versie en selecteert u **OK**.

   ![Minimum-en doel versies](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Micro soft-verificatie bibliotheek toevoegen aan uw project

1. Selecteer in Visual Studio **Tools** > **NuGet Package Manager** > **Package Manager Console**.
1. Kopieer en plak de volgende opdracht in het venster **Package Manager-console** :

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Met deze opdracht [wordt de micro soft-verificatie bibliotheek](https://aka.ms/msal-net)geïnstalleerd. MSAL verkrijgt, bewaart en vernieuwt gebruikers tokens die toegang hebben tot Api's die worden beveiligd door het micro soft Identity-platform.

### <a name="create-your-applications-ui"></a>De gebruikers interface van uw toepassing maken

Visual Studio maakt *MainPage. xaml* als onderdeel van de project sjabloon. Open dit bestand en vervang het **raster** knooppunt van uw toepassing door de volgende code:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>MSAL gebruiken om een token op te halen voor de Microsoft Graph-API

In deze sectie wordt uitgelegd hoe u MSAL gebruikt om een token op te halen voor Microsoft Graph-API. Breng wijzigingen aan in het *MainPage.xaml.cs* -bestand.

1. Voeg in *MainPage.xaml.cs*de volgende verwijzingen toe:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Vervang uw `MainPage`-klasse door de volgende code:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant with the information about the accounts allowed to sign in in your application:
        //   - for Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

         // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.            
         IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false); 
         IAccount firstAccount = accounts.FirstOrDefault();

         try
         {
          authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
         }
         catch (MsalUiRequiredException ex)
         {
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    }
    ```

#### Een gebruikers token interactief ophalen<a name="more-information"></a>

De methode `AcquireTokenInteractive` resulteert in een venster waarin de gebruiker wordt gevraagd zich aan te melden. In toepassingen moeten gebruikers doorgaans de eerste keer zich aanmelden om toegang te krijgen tot een beveiligde bron. Ze moeten zich mogelijk ook aanmelden wanneer een stille bewerking voor het verkrijgen van een token mislukt. Een voor beeld hiervan is wanneer het wacht woord van een gebruiker is verlopen.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Met de `AcquireTokenSilent` methode worden verwervingen en vernieuwingen van tokens verwerkt zonder tussen komst van de gebruiker. Nadat `AcquireTokenInteractive` voor de eerste keer wordt uitgevoerd en de gebruiker om referenties wordt gevraagd, gebruikt u de methode `AcquireTokenSilent` om tokens aan te vragen voor latere aanroepen. Deze methode verwerft tokens op de achtergrond. MSAL verwerkt de token cache en de verlenging.

Uiteindelijk mislukt de `AcquireTokenSilent` methode. De oorzaak van de fout is het toevoegen van een gebruiker die het wacht woord heeft afgemeld of gewijzigd op een ander apparaat. Wanneer MSAL detecteert dat het probleem een interactieve actie vereist, wordt een uitzonde ring `MsalUiRequiredException` gegenereerd. Uw toepassing kan deze uitzonde ring op twee manieren afhandelen:

* Uw toepassing roept direct `AcquireTokenInteractive`. Met deze aanroep wordt de gebruiker gevraagd zich aan te melden. Normaal gesp roken gebruikt u deze methode voor online toepassingen waarbij er geen offline-inhoud beschikbaar is voor de gebruiker. Het voor beeld dat door deze begeleide installatie wordt gegenereerd, volgt het patroon. De eerste keer dat u het voor beeld uitvoert, ziet u dat deze in actie is.

   Omdat geen enkele gebruiker de toepassing heeft gebruikt, heeft `accounts.FirstOrDefault()` een null-waarde en wordt een uitzonde ring gegenereerd `MsalUiRequiredException`.

   De code in het voor beeld behandelt vervolgens de uitzonde ring door `AcquireTokenInteractive`aan te roepen. Met deze aanroep wordt de gebruiker gevraagd zich aan te melden.

* Uw toepassing bevat een visuele indicatie voor gebruikers die zich moeten aanmelden. Vervolgens kunnen ze het juiste tijdstip selecteren om zich aan te melden. De toepassing kan later opnieuw `AcquireTokenSilent`. Gebruik deze benadering wanneer gebruikers de functionaliteit van andere toepassingen zonder onderbreking kunnen gebruiken. Een voor beeld hiervan is wanneer offline-inhoud beschikbaar is in de toepassing. In dit geval kunnen gebruikers bepalen wanneer ze zich willen aanmelden. De toepassing kan opnieuw `AcquireTokenSilent` nadat het netwerk tijdelijk niet beschikbaar was.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Microsoft Graph-API aanroepen met behulp van het token dat u zojuist hebt verkregen

Voeg de volgende nieuwe methode toe aan *MainPage.xaml.cs*:

   ```csharp
   /// <summary>
   /// Perform an HTTP GET request to a URL using an HTTP Authorization header
   /// </summary>
   /// <param name="url">The URL</param>
   /// <param name="token">The token</param>
   /// <returns>String containing the results of the GET operation</returns>
   public async Task<string> GetHttpContentWithToken(string url, string token)
   {
       var httpClient = new System.Net.Http.HttpClient();
       System.Net.Http.HttpResponseMessage response;
       try
       {
           var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
           // Add the token in Authorization header
           request.Headers.Authorization = 
             new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
           response = await httpClient.SendAsync(request);
           var content = await response.Content.ReadAsStringAsync();
           return content;
       }
       catch (Exception ex)
        {
           return ex.ToString();
       }
    }
   ```

 Met deze methode wordt een `GET` aanvraag van Graph API met behulp van een `Authorization`-header.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Meer informatie over het maken van een REST-aanroep voor een beveiligde API

In deze voorbeeld toepassing maakt de `GetHttpContentWithToken` methode een HTTP-`GET` aanvraag voor een beveiligde bron die een token vereist. Vervolgens retourneert de methode de inhoud naar de aanroeper. Met deze methode wordt het verkregen token toegevoegd aan de **http-autorisatie-** header. Voor dit voor beeld is de resource het Microsoft Graph API **me** -eind punt, waarin de profiel gegevens van de gebruiker worden weer gegeven.

### <a name="add-a-method-to-sign-out-the-user"></a>Een methode voor het afmelden van de gebruiker toevoegen

Als u de gebruiker wilt afmelden, voegt u de volgende methode toe aan *MainPage.xaml.cs*:

   ```csharp
   /// <summary>
   /// Sign out the current user
   /// </summary>
   private async void SignOutButton_Click(object sender, RoutedEventArgs e)
   {
       IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
       IAccount firstAccount = accounts.FirstOrDefault();

       try
       {
           await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
               ResultText.Text = "User has signed out";
               this.CallGraphButton.Visibility = Visibility.Visible;
                   this.SignOutButton.Visibility = Visibility.Collapsed;
               });
           }
           catch (MsalException ex)
           {
               ResultText.Text = $"Error signing out user: {ex.Message}";
           }
       }
   ```

> [!NOTE]
> MSAL.NET maakt gebruik van asynchrone methoden om tokens te verkrijgen of accounts te manipuleren. U moet de acties van de gebruikers interface ondersteunen in de UI-thread. Dit is de reden voor het aanroepen van `Dispatcher.RunAsync` en de voorzorgsmaatregelen voor het aanroepen van `ConfigureAwait(false)`.

#### Meer informatie over afmelden<a name="more-information-on-sign-out"></a>

Met de methode `SignOutButton_Click` wordt de gebruiker uit de MSAL-gebruikers cache verwijderd. Deze methode vertelt MSAL de huidige gebruiker te verg eten. Een toekomstige aanvraag voor het verkrijgen van een token slaagt alleen als deze interactief is.

De toepassing in dit voor beeld ondersteunt één gebruiker. MSAL ondersteunt scenario's waarbij de gebruiker zich kan aanmelden bij meer dan een account. Een voor beeld is een e-mail toepassing waarbij een gebruiker meerdere accounts heeft.

### <a name="display-basic-token-information"></a>Elementaire token gegevens weer geven

Voeg de volgende methode toe aan *MainPage.xaml.cs* om basis informatie over het token weer te geven:

   ```csharp
   /// <summary>
   /// Display basic information contained in the token. Needs to be called from the UI thread.
   /// </summary>
   private void DisplayBasicTokenInfo(AuthenticationResult authResult)
   {
       TokenInfoText.Text = "";
       if (authResult != null)
       {
           TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
           TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
       }
   }
   ```

#### Meer informatie<a name="more-information-1"></a>

ID-tokens die zijn verkregen met behulp van **OpenID Connect Connect** , bevatten ook een kleine subset van informatie die betrekking heeft op de gebruiker. `DisplayBasicTokenInfo` bevat basis informatie in het token. Deze informatie bevat de weergave naam en-ID van de gebruiker. Het bevat ook de verval datum van het token en de teken reeks die het toegangs token zelf vertegenwoordigt. Als u de knop **Call Microsoft Graph API** meerdere keren selecteert, ziet u dat hetzelfde token opnieuw is gebruikt voor latere aanvragen. U kunt ook zien hoe lang de verloop datum moet worden verlengd wanneer MSAL beslist dat het token wordt vernieuwd.

### <a name="display-message"></a>Bericht weer geven

Voeg de volgende nieuwe methode toe aan *MainPage.xaml.cs*:

   ```csharp
   /// <summary>
   /// Displays a message in the ResultText. Can be called from any thread.
   /// </summary>
   private async Task DisplayMessageAsync(string message)
   {
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
            () =>
            {
                ResultText.Text = message;
            });
        }
   ```

## <a name="register-your-application"></a>Uw toepassing registreren

U moet uw toepassing nu registreren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer **Azure Active Directory** > **App-registraties**.
1. Selecteer **nieuwe registratie**. Voer een zinvolle toepassings naam in die wordt weer gegeven voor gebruikers van de app, bijvoorbeeld *UWP-app-Call-MSGraph*.
1. Onder **ondersteunde account typen**selecteert u **accounts in elke organisatie-map en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox)** en selecteert u **registreren** om door te gaan.
1. Zoek op de pagina overzicht de waarde van de **toepassing (client)** en kopieer deze. Ga terug naar Visual Studio, open *MainPage.xaml.cs*en vervang de waarde van `ClientId` door deze waarde.

Verificatie voor uw toepassing configureren:

1. Ga terug naar de [Azure Portal](https://portal.azure.com)en selecteer **verificatie**onder **beheren**.
1. Controleer in de sectie **omleidings-uri's** | **voorgestelde omleidings-uri's voor open bare clients (Mobile, Desktop)** **https://login.microsoftonline.com/common/oauth2/nativeclient** .
1. Selecteer **Opslaan**.

API-machtigingen voor uw toepassing configureren:

1. Selecteer onder **beheren**de optie **API-machtigingen**.
1. Selecteer **een machtiging toevoegen** en zorg ervoor dat u **micro soft-api's**hebt geselecteerd.
1. Selecteer **Microsoft Graph**.
1. Selecteer **gedelegeerde machtigingen**, zoek naar *gebruiker. Lees* en controleer of **User. Read** is geselecteerd.
1. Als u wijzigingen hebt aangebracht, selecteert u **machtigingen toevoegen** om ze op te slaan.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Geïntegreerde verificatie inschakelen voor federatieve domeinen (optioneel)

Als u geïntegreerde Windows-verificatie wilt inschakelen wanneer deze wordt gebruikt in combi natie met een federatief Azure AD-domein, moet het toepassings manifest extra mogelijkheden inschakelen. Ga terug naar uw toepassing in Visual Studio.

1. Open *package. appxmanifest*.
1. Selecteer **mogelijkheden** en schakel de volgende instellingen in:

   * **Ondernemings verificatie**
   * **Particuliere netwerken (client & Server)**
   * **Gedeelde gebruikers certificaten**

> [!IMPORTANT]
> [Geïntegreerde Windows-verificatie](https://aka.ms/msal-net-iwa) is niet standaard geconfigureerd voor dit voor beeld. Toepassingen die `Enterprise Authentication`-of `Shared User Certificates`-mogelijkheden aanvragen, vereisen een hoger verificatie niveau van de Windows Store. Het is ook niet alle ontwikkel aars nodig om het hogere verificatie niveau uit te voeren. Schakel deze instelling alleen in als u geïntegreerde Windows-verificatie met een federatief Azure AD-domein nodig hebt.

## <a name="test-your-code"></a>Uw code testen

Als u uw toepassing wilt testen, selecteert u F5 om uw project uit te voeren in Visual Studio. Het hoofd venster wordt weer gegeven:

![Gebruikers interface van de toepassing](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Wanneer u klaar bent om te testen, selecteert u **Microsoft Graph-API aanroepen**. Gebruik vervolgens een Azure AD-organisatie account of een Microsoft-account, zoals live.com of outlook.com, om u aan te melden. De eerste keer dat een gebruiker dit uitvoert, wordt in de toepassing een venster weer gegeven waarin de gebruiker wordt gevraagd zich aan te melden.

### <a name="consent"></a>Vergunning

De eerste keer dat u zich bij uw toepassing aanmeldt, wordt er een venster voor toestemming weer gegeven dat vergelijkbaar is met het volgende. Selecteer **Ja** om expliciet toestemming te geven voor toegang:

![Venster toegangs bevestiging](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Verwachte resultaten

U ziet de gebruikers profiel gegevens die worden geretourneerd door de API-aanroep van Microsoft Graph op het scherm met de **API-aanroep resultaten** :

![Scherm met resultaten van API-aanroep](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

U ziet ook basis informatie over het token dat is verkregen via `AcquireTokenInteractive` of `AcquireTokenSilent` in het vak **token info** :

|Eigenschap  |Indeling  |Beschrijving |
|---------|---------|---------|
|`Username` |`user@domain.com` |De gebruikers naam waarmee de gebruiker wordt geïdentificeerd.|
|`Token Expires` |`DateTime` |Het tijdstip waarop het token verloopt. MSAL breidt de verloop datum uit door de token indien nodig te vernieuwen.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Meer informatie over bereiken en gedelegeerde machtigingen

Microsoft Graph-API vereist dat het `user.read` bereik het profiel van een gebruiker kan lezen. Deze scope wordt standaard toegevoegd in elke toepassing die is geregistreerd in de portal voor toepassings registratie. Andere Api's voor Microsoft Graph en aangepaste Api's voor uw back-endserver vereisen mogelijk extra scopes. Microsoft Graph-API vereist bijvoorbeeld het `Calendars.Read` bereik om de agenda's van de gebruiker weer te geven.

Als u toegang wilt krijgen tot de agenda's van de gebruiker in de context van een toepassing, voegt u de `Calendars.Read` gedelegeerde machtiging toe aan de registratie gegevens van de toepassing. Voeg het `Calendars.Read` bereik vervolgens toe aan de `acquireTokenSilent`-aanroep.

> [!NOTE]
> Gebruikers kunnen worden gevraagd om aanvullende toestemmingen wanneer u het aantal bereiken verhoogt.

## <a name="known-issues"></a>Bekende problemen

### <a name="issue-1"></a>Probleem 1

Er wordt een van de volgende fout berichten weer gegeven wanneer u zich aanmeldt bij uw toepassing in een federatief Azure AD-domein:

* Er is geen geldig client certificaat gevonden in de aanvraag.
* Er zijn geen geldige certificaten gevonden in het certificaat archief van de gebruiker.
* Probeer opnieuw een andere verificatie methode te kiezen.

Oorzaak: de mogelijkheden voor ondernemings en certificaten zijn niet ingeschakeld.

Oplossing: Volg de stappen in [geïntegreerde verificatie inschakelen voor federatieve domeinen (optioneel)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Probleem 2

U schakelt [geïntegreerde verificatie in voor federatieve domeinen](#enable-integrated-authentication-on-federated-domains-optional) en probeert Windows hello te gebruiken op een Windows 10-computer om u aan te melden bij een omgeving waarvoor multi-factor Authentication is geconfigureerd. De lijst met certificaten wordt weer gegeven. Als u echter kiest voor het gebruik van uw pincode, wordt het venster vastmaken nooit weer gegeven.

Oorzaak: dit probleem is een bekende beperking van de Web authentication Broker in UWP-toepassingen die worden uitgevoerd op Windows 10 Desktop. Het werkt prima op Windows 10 Mobile.

Tijdelijke oplossing: Selecteer **Aanmelden met andere opties**. Selecteer vervolgens **Aanmelden met een gebruikers naam en wacht woord**. Selecteer **uw wacht woord opgeven**. Ga vervolgens door met het verificatie proces voor de telefoon.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien:

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
