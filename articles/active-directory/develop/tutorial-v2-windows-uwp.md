---
title: Microsoft identity platform UWP aan de slag | Azure
description: Hoe Universal Windows Platform applications (UWP) een API kan aanroepen waarvoor toegangstokens vereist zijn van het eindpunt van het Microsoft-identiteitsplatform.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 0dc70aa67a1414c08ec70e2e034f4ab12b194c0a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535941"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Microsoft Graph API aanroepen vanuit een Universele Windows Platform-toepassing (XAML)

> [!div renderon="docs"]

In deze handleiding wordt uitgelegd hoe een native Universal Windows Platform (UWP)-toepassing een toegangstoken kan aanvragen. De toepassing roept vervolgens Microsoft Graph API aan. De handleiding is ook van toepassing op andere API's waarvoor toegangstokens van het eindpunt van het Microsoft-identiteitsplatform nodig zijn.

Aan het einde van deze handleiding roept uw toepassing een beveiligde API aan met behulp van persoonlijke accounts. Voorbeelden zijn outlook.com, live.com en anderen. Uw toepassing roept ook werk- en schoolaccounts op van elk bedrijf of organisatie met Azure Active Directory (Azure AD).

>[!NOTE]
> Deze handleiding vereist Visual Studio met Universal Windows Platform ontwikkeling geïnstalleerd. Zie [Instellen](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) voor instructies voor het downloaden en configureren van Visual Studio om Universele Windows Platform-apps te ontwikkelen.

>[!NOTE]
> Als u nieuw bent op het Microsoft-identiteitsplatform, raden we u aan om snel te beginnen met [het aanroepen van de Microsoft Graph API vanaf een UwP-toepassing (Universal Windows Platform).](quickstart-v2-uwp.md)

## <a name="how-this-guide-works"></a>De werking van deze handleiding

![Laat zien hoe de voorbeeld-app die door deze zelfstudie wordt gegenereerd, werkt](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Met deze handleiding wordt een voorbeeld-UWP-toepassing gemaakt die microsoft graph-api opvraagt. Voor dit scenario wordt een token toegevoegd aan HTTP-aanvragen met de kopautorisatie. Microsoft Authentication Library (MSAL) verwerkt tokenacquisities en -verlengingen.

## <a name="nuget-packages"></a>NuGet-pakketten

In deze handleiding wordt het volgende NuGet-pakket gebruikt:

|Bibliotheek|Beschrijving|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>Uw project instellen

In deze sectie worden stapsgewijze instructies gegeven om een Windows Desktop .NET-toepassing (XAML) te integreren met Sign-In met Microsoft. Vervolgens kan de toepassing web-API's opvragen waarvoor een token nodig is, zoals Microsoft Graph API.

Met deze handleiding wordt een toepassing gemaakt met een knop die de Graph API opvraagt en een knop om af te melden. Het toont ook tekstvakken die de resultaten van de oproepen bevatten.

> [!NOTE]
> Wilt u het Visual Studio-project van dit voorbeeld downloaden in plaats van het te maken? [Download een project](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) en ga naar de [aanvraagregistratiestap](#register-your-application "aanvraagregistratiestap") om het codevoorbeeld te configureren voordat het wordt uitgevoerd.

### <a name="create-your-application"></a>Uw toepassing maken

1. Open Visual Studio en selecteer **Een nieuw project maken**.
1. Kies in **Een nieuw project maken**de optie Lege app **(Universele Windows)** voor C# en selecteer **Volgende**.
1. Geef **in Uw nieuwe project configureren**de naam van de app en selecteer **Maken**.
1. Selecteer desgevraagd in **New Universal Windows Platform Project**een versie voor **doel-** en **minimumversies** en selecteer **OK**.

   ![Minimum- en doelversies](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Microsoft-verificatiebibliotheek toevoegen aan uw project

1. Selecteer in Visual Studio **de** > optie Tools**NuGet Package Manager** > **Package Manager Console**.
1. Kopieer en plak de volgende opdracht in het venster **Package Manager Console:**

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Met deze opdracht installeert [microsoft-verificatiebibliotheek](https://aka.ms/msal-net). MSAL verwerft, caches en ververst gebruikerstokens die toegang krijgen tot API's die worden beschermd door het identiteitsplatform van Microsoft.

### <a name="create-your-applications-ui"></a>De gebruikersinterface van uw toepassing maken

Visual Studio maakt *MainPage.xaml* als onderdeel van uw projectsjabloon. Open dit bestand en vervang het **rasterknooppunt** van uw toepassing door de volgende code:

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

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>MSAL gebruiken om een token voor Microsoft Graph API te krijgen

In deze sectie ziet u hoe u MSAL gebruikt om een token voor Microsoft Graph API te krijgen. Wijzigingen aanbrengen *MainPage.xaml.cs* in het MainPage.xaml.cs-bestand.

1. Voeg in *MainPage.xaml.cs*de volgende referenties toe:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Vervang `MainPage` uw klasse door de volgende code:

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

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen<a name="more-information"></a>

De `AcquireTokenInteractive` methode resulteert in een venster dat gebruikers vraagt zich aan te melden. Toepassingen vereisen meestal dat gebruikers zich de eerste keer interactief aanmelden om toegang te krijgen tot een beveiligde bron. Mogelijk moeten ze zich ook aanmelden wanneer een stille bewerking om een token te verkrijgen mislukt. Een voorbeeld is wanneer het wachtwoord van een gebruiker is verlopen.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

De `AcquireTokenSilent` methode verwerkt tokenacquisities en -verlengingen zonder enige interactie van de gebruiker. Nadat `AcquireTokenInteractive` u voor het eerst is uitgevoerd en de `AcquireTokenSilent` gebruiker om referenties heeft gevraagd, gebruikt u de methode om tokens aan te vragen voor latere oproepen. Die methode verwerft tokens in stilte. MSAL verwerkt tokencache en -vernieuwing.

Uiteindelijk, `AcquireTokenSilent` de methode mislukt. Redenen voor het mislukken zijn onder andere een gebruiker die zich heeft afgemeld of zijn wachtwoord op een ander apparaat heeft gewijzigd. Wanneer MSAL detecteert dat het probleem een `MsalUiRequiredException` interactieve actie vereist, wordt er een uitzondering gemaakt. Uw toepassing kan deze uitzondering op twee manieren verwerken:

* Uw sollicitatie `AcquireTokenInteractive` belt onmiddellijk. Deze oproep leidt ertoe dat de gebruiker wordt gevraagd zich aan te melden. Normaal gesproken, gebruik maken van deze aanpak voor online toepassingen waar er geen offline inhoud beschikbaar voor de gebruiker. Het voorbeeld dat door deze begeleide instelling wordt gegenereerd, volgt het patroon. U ziet het in actie wanneer u het voorbeeld voor het eerst uitvoert.

   Omdat geen enkele gebruiker `accounts.FirstOrDefault()` de toepassing heeft gebruikt, `MsalUiRequiredException` een null-waarde bevat en een uitzondering gooit.

   De code in het voorbeeld verwerkt `AcquireTokenInteractive`vervolgens de uitzondering door aan te roepen . Deze oproep leidt ertoe dat de gebruiker wordt gevraagd zich aan te melden.

* Uw toepassing geeft gebruikers een visuele indicatie dat ze zich moeten aanmelden. Vervolgens kunnen ze het juiste moment selecteren om in te loggen. De toepassing kan `AcquireTokenSilent` later opnieuw proberen. Gebruik deze aanpak wanneer gebruikers andere toepassingsfunctionaliteit kunnen gebruiken zonder onderbreking. Een voorbeeld hiervan is wanneer offline inhoud beschikbaar is in de toepassing. In dit geval kunnen gebruikers beslissen wanneer ze zich willen aanmelden. De toepassing kan `AcquireTokenSilent` opnieuw proberen nadat het netwerk tijdelijk niet beschikbaar was.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Microsoft Graph API aanroepen met behulp van het token dat u zojuist hebt verkregen

Voeg de volgende nieuwe methode toe aan *MainPage.xaml.cs:*

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

 Met deze `GET` methode wordt een aanvraag `Authorization` ingediend bij Graph API met behulp van een koptekst.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Meer informatie over het maken van een REST-aanroep tegen een beveiligde API

In deze voorbeeldtoepassing `GetHttpContentWithToken` maakt de `GET` methode een HTTP-aanvraag tegen een beveiligde bron waarvoor een token vereist is. Vervolgens retourneert de methode de inhoud naar de beller. Met deze methode wordt het verkregen token toegevoegd aan de **koptekst HTTP Autorisatie.** Voor dit voorbeeld is de bron het eindpunt van de Microsoft Graph API **me,** waarmee de profielgegevens van de gebruiker worden weergegeven.

### <a name="add-a-method-to-sign-out-the-user"></a>Een methode toevoegen om de gebruiker af te melden

Als u de gebruiker wilt afmelden, voegt u de volgende methode toe aan *MainPage.xaml.cs:*

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
> MSAL.NET gebruikt asynchrone methoden om tokens te verwerven of accounts te manipuleren. U moet u de gebruikersinterface-acties in de thread UI ondersteunen. Dit is de `Dispatcher.RunAsync` reden voor de `ConfigureAwait(false)`oproep en de voorzorgsmaatregelen om te bellen.

#### <a name="more-information-about-signing-out"></a>Meer informatie over afmelden<a name="more-information-on-sign-out"></a>

De `SignOutButton_Click` methode verwijdert de gebruiker uit de MSAL-gebruikerscache. Deze methode vertelt MSAL effectief om de huidige gebruiker te vergeten. Een toekomstig verzoek om een token te verkrijgen, wordt alleen voltooid als het interactief is.

De toepassing in dit voorbeeld ondersteunt één gebruiker. MSAL ondersteunt scenario's waarin de gebruiker zich op meer dan één account kan aanmelden. Een voorbeeld is een e-mailtoepassing waarbij een gebruiker meerdere accounts heeft.

### <a name="display-basic-token-information"></a>Basistokengegevens weergeven

Voeg de volgende methode toe aan *MainPage.xaml.cs* om basisinformatie over het token weer te geven:

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

#### <a name="more-information"></a>Meer informatie<a name="more-information-1"></a>

ID-tokens die zijn verkregen met **OpenID Connect** bevatten ook een kleine subset van informatie die relevant is voor de gebruiker. `DisplayBasicTokenInfo`geeft basisinformatie in het token weer. Deze informatie omvat de weergavenaam en id van de gebruiker. Het bevat ook de vervaldatum van het token en de tekenreeks die het toegangstoken zelf vertegenwoordigt. Als u de **API-aanroep van Microsoft Graph** meerdere keren selecteert, ziet u dat hetzelfde token opnieuw is gebruikt voor latere aanvragen. U ook de vervaldatum verlengd zien wanneer MSAL besluit dat het tijd is om het token te vernieuwen.

### <a name="display-message"></a>Bericht weergeven

Voeg de volgende nieuwe methode toe aan *MainPage.xaml.cs:*

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

Nu moet u uw aanvraag registreren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **Azure Active Directory** > **App-registraties**.
1. Selecteer **Nieuwe registratie**. Voer een zinvolle toepassingsnaam in die wordt weergegeven aan gebruikers van de app, bijvoorbeeld *UWP-App-calling-MSGraph.*
1. Selecteer onder **Ondersteunde accounttypen** **accounts in een organisatiemap en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox)** en selecteer **Vervolgens Registreren** om door te gaan.
1. Zoek op de overzichtspagina de **id-waarde van de toepassing (client)** en kopieer deze. Ga terug naar Visual *MainPage.xaml.cs*Studio, open MainPage.xaml.cs `ClientId` en vervang de waarde van deze waarde.

Verificatie configureren voor uw toepassing:

1. Terug in de [Azure-portal](https://portal.azure.com)selecteert u **verificatie**onder **Beheren**.
1. Schakel in de sectie **URL's** | die worden voorgesteld om te leiden**naar URL's voor openbare clients (mobiel, desktop)** de controle **https://login.microsoftonline.com/common/oauth2/nativeclient**.
1. Selecteer **Opslaan**.

API-machtigingen voor uw toepassing configureren:

1. Selecteer **API-machtigingen** **onder Beheren**.
1. Selecteer **Een machtiging toevoegen** en controleer of u **Microsoft-API's**hebt geselecteerd.
1. Selecteer **Microsoft Graph**.
1. Selecteer **Gedelegeerde machtigingen**, zoek naar *User.Read* en controleer of **User.Read** is geselecteerd.
1. Als u wijzigingen hebt aangebracht, selecteert u **Machtigingen toevoegen** om deze op te slaan.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Geïntegreerde verificatie inschakelen op federatieve domeinen (optioneel)

Als u Windows-geïntegreerde verificatie wilt inschakelen wanneer deze wordt gebruikt met een gefedereerd Azure AD-domein, moet het toepassingsmanifest extra mogelijkheden inschakelen. Ga terug naar je sollicitatie in Visual Studio.

1. Open *Package.appxmanifest*.
1. Selecteer **Mogelijkheden** en schakel de volgende instellingen in:

   * **Bedrijfsverificatie**
   * **Privénetwerken (client & server)**
   * **Gedeelde gebruikerscertificaten**

> [!IMPORTANT]
> [Geïntegreerde Windows-verificatie](https://aka.ms/msal-net-iwa) is niet standaard geconfigureerd voor dit voorbeeld. Toepassingen die `Enterprise Authentication` `Shared User Certificates` aanvragen of mogelijkheden vereisen een hoger niveau van verificatie door de Windows Store. Ook willen niet alle ontwikkelaars het hogere niveau van verificatie uitvoeren. Schakel deze instelling alleen in als u Windows Integrated Authentication nodig hebt met een gefedereerd Azure AD-domein.

## <a name="test-your-code"></a>Uw code testen

Als u uw toepassing wilt testen, selecteert u F5 om uw project uit te voeren in Visual Studio. Het hoofdvenster wordt weergegeven:

![Gebruikersinterface van de toepassing](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Wanneer u klaar bent om te testen, selecteert u **Microsoft Graph API aanroepen**. Gebruik vervolgens een Azure AD-organisatieaccount of een Microsoft-account, zoals live.com of outlook.com, om u aan te melden. De eerste keer dat een gebruiker dit uitvoert, geeft de toepassing een venster weer waarin de gebruiker wordt gevraagd zich aan te melden.

### <a name="consent"></a>Toestemming

De eerste keer dat u zich aanmeldt bij uw aanvraag, krijgt u een toestemmingsscherm te zien dat vergelijkbaar is met het volgende. Selecteer **Ja** om expliciet toestemming te geven voor toegang:

![Scherm toegangstoestemming](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Verwachte resultaten

U ziet gebruikersprofielgegevens die worden geretourneerd door de API-aanroep van Microsoft Graph in het scherm **API-oproepresultaten:**

![Scherm API-oproepresultaten](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

U ziet ook basisinformatie over `AcquireTokenInteractive` het `AcquireTokenSilent` token dat is verkregen via of in het vak **Tokeninfo:**

|Eigenschap  |Indeling  |Beschrijving |
|---------|---------|---------|
|`Username` |`user@domain.com` |De gebruikersnaam die de gebruiker identificeert.|
|`Token Expires` |`DateTime` |Het tijdstip waarop het token verloopt. MSAL verlengt de vervaldatum door het token zo nodig te vernieuwen.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Meer informatie over scopes en gedelegeerde machtigingen

Microsoft Graph API `user.read` vereist het bereik om het profiel van een gebruiker te lezen. Dit bereik wordt standaard toegevoegd in elke toepassing die is geregistreerd in het Portal voor toepassingsregistratie. Andere API's voor Microsoft Graph en aangepaste API's voor uw back-endserver vereisen mogelijk extra scopes. Microsoft Graph API vereist `Calendars.Read` bijvoorbeeld het bereik om de agenda's van de gebruiker weer te geven.

Als u de agenda's van de gebruiker wilt `Calendars.Read` openen in de context van een toepassing, voegt u de gedelegeerde machtiging toe aan de registratiegegevens van de toepassing. Voeg vervolgens `Calendars.Read` het `acquireTokenSilent` bereik toe aan het gesprek.

> [!NOTE]
> Gebruikers kunnen worden gevraagd om extra toestemmingen als u het aantal scopes te verhogen.

## <a name="known-issues"></a>Bekende problemen

### <a name="issue-1"></a>Probleem 1

U ontvangt een van de volgende foutmeldingen wanneer u zich aanmeldt bij uw toepassing op een gefedereerd Azure AD-domein:

* Geen geldig clientcertificaat gevonden in de aanvraag.
* Geen geldige certificaten gevonden in het certificaatarchief van de gebruiker.
* Probeer opnieuw een andere verificatiemethode te kiezen.

Oorzaak: Enterprise- en certificaatmogelijkheden zijn niet ingeschakeld.

Oplossing: Volg de stappen in [Geïntegreerde verificatie inschakelen op federatieve domeinen (optioneel).](#enable-integrated-authentication-on-federated-domains-optional)

### <a name="issue-2"></a>Probleem 2

U schakelt [geïntegreerde verificatie in op federatieve domeinen](#enable-integrated-authentication-on-federated-domains-optional) en probeert Windows Hello te gebruiken op een Windows 10-computer om u aan te melden bij een omgeving met multi-factor authenticatie geconfigureerd. De lijst met certificaten wordt gepresenteerd. Als u er echter voor kiest om uw pincode te gebruiken, wordt het pincodevenster nooit weergegeven.

Oorzaak: Dit probleem is een bekende beperking van de webverificatiebroker in UWP-toepassingen die worden uitgevoerd op Windows 10-bureaublad. Het werkt prima op Windows 10 Mobile.

Tijdelijke oplossing: Selecteer **Aanmelden met andere opties**. Selecteer **Vervolgens Aanmelden met een gebruikersnaam en wachtwoord**. Selecteer **Uw wachtwoord opgeven**. Ga dan door het verificatieproces voor telefoons.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
