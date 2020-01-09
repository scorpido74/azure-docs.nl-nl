---
title: Aan de slag met het micro soft-identiteits platform Windows-bureau blad | Microsoft Docs
description: Hoe een Windows Desktop .NET (XAML)-toepassing een toegangs token kan ophalen en een API kan aanroepen die wordt beveiligd door het micro soft Identity-platform.
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
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c29a06496bb1303849250f049e4e7444a5a5ddf3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423359"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>De Microsoft Graph-API aanroepen vanuit een Windows-bureau blad-app

In deze hand leiding wordt gedemonstreerd hoe een systeem eigen Windows Desktop .NET-toepassing (XAML) gebruikmaakt van een toegangs token om de Microsoft Graph-API aan te roepen. De app kan ook toegang krijgen tot andere Api's waarvoor toegangs tokens zijn vereist van een micro soft Identity-platform voor ontwikkel aars v 2.0-eind punt. Dit platform heette voorheen Azure AD.

Wanneer u de gids hebt voltooid, kan uw toepassing een beveiligde API aanroepen die gebruikmaakt van persoonlijke accounts (waaronder outlook.com, live.com en andere). De toepassing gebruikt ook werk-en school accounts van een bedrijf of organisatie die gebruikmaakt van Azure Active Directory.  

> [!NOTE]
> Voor de gids is Visual Studio 2015 update 3, Visual Studio 2017 of Visual Studio 2019 vereist. Hebt u geen van deze versies? [Down load Visual Studio 2019 gratis](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hoe de voor beeld-app die wordt gegenereerd door deze hand leiding werkt

![Toont hoe de voor beeld-app die door deze zelf studie wordt gegenereerd, werkt](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

De voorbeeld toepassing die u met deze hand leiding maakt, maakt een Windows-bureaublad toepassing mogelijk die de Microsoft Graph-API of een web-API die tokens van een micro soft Identity-platform-eind punt accepteert, opvraagt. Voor dit scenario voegt u een token toe aan HTTP-aanvragen via de autorisatie-header. Micro soft Authentication Library (MSAL) verwerkt het ophalen en vernieuwen van tokens.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Verwerving van tokens voor toegang tot beveiligde web-Api's

Nadat de gebruiker is geverifieerd, ontvangt de voorbeeld toepassing een token dat u kunt gebruiken voor het opvragen van Microsoft Graph-API of een web-API die wordt beveiligd door het micro soft Identity-platform voor ontwikkel aars.

Api's zoals Microsoft Graph vereisen een token om toegang tot specifieke bronnen toe te staan. Een token is bijvoorbeeld vereist om het profiel van een gebruiker te lezen, de agenda van een gebruiker te openen of een e-mail te verzenden. Uw toepassing kan een toegangs token aanvragen met behulp van MSAL om toegang te krijgen tot deze bronnen door de API-scopes op te geven. Dit toegangs token wordt vervolgens toegevoegd aan de HTTP-autorisatie-header voor elke aanroep die wordt gedaan voor de beveiligde bron.

MSAL beheert de caching en vernieuwt de toegangs tokens voor u, zodat uw toepassing niet hoeft te worden.

## <a name="nuget-packages"></a>NuGet-pakketten

Deze hand leiding maakt gebruik van de volgende NuGet-pakketten:

|Bibliotheek|Beschrijving|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Micro soft Authentication Library (MSAL.NET)|

## <a name="set-up-your-project"></a>Uw project instellen

In deze sectie maakt u een nieuw project om te laten zien hoe u een Windows Desktop .NET-toepassing (XAML) integreert met *Aanmelden bij micro soft* , zodat de toepassing Web-api's kan opvragen waarvoor een token is vereist.

De toepassing die u met deze hand leiding maakt, toont een knop die wordt gebruikt voor het aanroepen van een grafiek, een gebied om de resultaten weer te geven op het scherm en een knop voor afmelden.

> [!NOTE]
> Wilt u in plaats daarvan het Visual Studio-project van dit voor beeld downloaden? [Down load een project](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)en ga naar de [configuratie stap](#register-your-application) om het code voorbeeld te configureren voordat u het uitvoert.
>

Ga als volgt te werk om uw toepassing te maken:

1. Selecteer in Visual Studio **Bestand** > **Nieuw** > **Project**.
2. Onder **sjablonen**selecteert u **Visual C#** .
3. Selecteer **WPF-app (.NET Framework)** , afhankelijk van de versie van Visual Studio die u gebruikt.

## <a name="add-msal-to-your-project"></a>MSAL toevoegen aan uw project

1. Selecteer in Visual Studio **Tools** > **NuGet Package Manager**> **Package Manager Console**.
2. Plak in het venster Package Manager-console de volgende Azure PowerShell opdracht:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Met deze opdracht wordt de micro soft-verificatie bibliotheek geïnstalleerd. MSAL zorgt voor het verkrijgen, in de cache opslaan en vernieuwen van gebruikers tokens die worden gebruikt voor toegang tot de Api's die worden beveiligd door Azure Active Directory v 2.0
    >

## <a name="register-your-application"></a>Uw toepassing registreren

U kunt uw toepassing op twee manieren registreren.

### <a name="option-1-express-mode"></a>Optie 1: Express-modus

U kunt uw toepassing snel registreren door het volgende te doen:
1. Ga naar de [registratie van Azure Portal-toepassing](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Voer een naam in voor de toepassing en selecteer **Registreren**.
1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.

### <a name="option-2-advanced-mode"></a>Optie 2: geavanceerde modus

Ga als volgt te werk om de toepassing te registreren en de registratiegegevens van de toepassing toe te voegen aan uw oplossing:
1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
1. Selecteer **nieuwe registratie**.
   - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `Win-App-calling-MsGraph`.
   - Selecteer in de sectie **Ondersteunde accounttypen** de optie **Accounts in alle organisatiemappen en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com**.
   - Selecteer **Registreren** om de toepassing te maken.
1. Selecteer in de lijst met pagina’s voor de app de optie **Verificatie**.
   1. In de sectie **omleidings-uri's** in de lijst omleidings-uri's:
   1. Selecteer in de kolom **type** de optie **open bare client/systeem eigen (mobiele & bureau blad)** .
   1. Voer in de kolom **omleidings-URI** `https://login.microsoftonline.com/common/oauth2/nativeclient`
1. Selecteer **Registreren**.
1. Ga naar Visual Studio, open het *app.xaml.cs* -bestand en vervang `Enter_the_Application_Id_here` in het code fragment hieronder door de toepassings-id die u zojuist hebt geregistreerd en gekopieerd.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>De code toevoegen om MSAL te initialiseren

In deze stap maakt u een klasse voor het afhandelen van interactie met MSAL, zoals het afhandelen van tokens.

1. Open het *app.xaml.cs* -bestand en voeg vervolgens de verwijzing voor MSAL toe aan de klasse:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Werk de app-klasse bij naar het volgende:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>De gebruikers interface van de toepassing maken

In deze sectie wordt uitgelegd hoe een toepassing een beveiligde back-endserver kan opvragen, zoals Microsoft Graph. 

Een *mainwindow. xaml* -bestand moet automatisch worden gemaakt als onderdeel van de project sjabloon. Open dit bestand en vervang het *\<raster >* knoop punt van uw toepassing door de volgende code:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Gebruik MSAL om een token op te halen voor de Microsoft Graph-API

In deze sectie gebruikt u MSAL om een token op te halen voor de Microsoft Graph-API.

1. Voeg in het bestand *mainwindow.xaml.cs* de verwijzing voor MSAL toe aan de klasse:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Vervang de `MainWindow` class code door het volgende:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Meer informatie

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Het aanroepen van de `AcquireTokenInteractive` methode resulteert in een venster waarin de gebruiker wordt gevraagd zich aan te melden. Toepassingen vereisen doorgaans dat gebruikers zich interactief aanmelden wanneer ze voor het eerst toegang hebben tot een beveiligde bron. Ze moeten zich mogelijk ook aanmelden wanneer een stille bewerking voor het verkrijgen van een token mislukt (bijvoorbeeld wanneer het wacht woord van een gebruiker is verlopen).

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Met de `AcquireTokenSilent` methode worden verwervingen en vernieuwingen van tokens verwerkt zonder tussen komst van de gebruiker. Nadat `AcquireTokenInteractive` voor de eerste keer is uitgevoerd, is `AcquireTokenSilent` de gebruikelijke methode voor het verkrijgen van tokens die toegang hebben tot beveiligde resources voor volgende aanroepen, omdat aanroepen naar aanvragen of vernieuwings tokens op de achtergrond worden uitgevoerd.

Uiteindelijk mislukt de `AcquireTokenSilent` methode. Mogelijke oorzaken zijn dat de gebruiker zich afmeldt of hun wacht woord heeft gewijzigd op een ander apparaat. Wanneer MSAL detecteert dat het probleem kan worden opgelost door een interactieve actie te vereisen, wordt een uitzonde ring van `MsalUiRequiredException` gestart. Uw toepassing kan deze uitzonde ring op twee manieren afhandelen:

* Dit kan direct een oproep doen tegen `AcquireTokenInteractive`. Met deze aanroep wordt de gebruiker gevraagd zich aan te melden. Dit patroon wordt meestal gebruikt in online toepassingen waarbij er geen offline-inhoud beschikbaar is voor de gebruiker. Het voor beeld dat door deze begeleide installatie wordt gegenereerd, volgt dit patroon, dat u in actie kunt zien wanneer u het voor beeld voor het eerst uitvoert. 

* Omdat geen enkele gebruiker de toepassing heeft gebruikt, bevat `PublicClientApp.Users.FirstOrDefault()` een null-waarde en wordt er een `MsalUiRequiredException` uitzonde ring gegenereerd. 

* De code in het voor beeld behandelt vervolgens de uitzonde ring door `AcquireTokenInteractive`aan te roepen, waardoor de gebruiker wordt gevraagd zich aan te melden.

* Het kan in plaats daarvan een visuele indicatie presen teren aan gebruikers die een interactieve aanmelding vereist, zodat ze de juiste tijd kunnen selecteren om zich aan te melden. Of de toepassing kan later opnieuw `AcquireTokenSilent`. Dit patroon wordt vaak gebruikt wanneer gebruikers andere toepassings functionaliteit kunnen gebruiken zonder onderbreking, bijvoorbeeld wanneer offline-inhoud beschikbaar is in de toepassing. In dit geval kunnen gebruikers bepalen wanneer ze zich willen aanmelden om toegang te krijgen tot de beveiligde resource of de verouderde informatie te vernieuwen. De toepassing kan er ook voor kiezen om `AcquireTokenSilent` opnieuw uit te voeren wanneer het netwerk wordt hersteld nadat het tijdelijk niet beschikbaar is.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>De Microsoft Graph-API aanroepen met behulp van het token dat u zojuist hebt verkregen

Voeg de volgende nieuwe methode toe aan uw `MainWindow.xaml.cs`. De methode wordt gebruikt om een `GET` aanvraag te doen tegen Graph API met behulp van een machtigings header:

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
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Meer informatie over het maken van een REST-aanroep voor een beveiligde API

In deze voorbeeld toepassing gebruikt u de methode `GetHttpContentWithToken` om een HTTP-`GET` aanvraag te doen tegen een beveiligde resource waarvoor een token is vereist en vervolgens de inhoud naar de aanroeper kan worden geretourneerd. Met deze methode wordt het verkregen token toegevoegd aan de HTTP-autorisatie-header. Voor dit voor beeld is de resource het Microsoft Graph API *me* -eind punt, waarin de profiel gegevens van de gebruiker worden weer gegeven.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Een methode toevoegen om een gebruiker af te melden

Als u een gebruiker wilt afmelden, voegt u de volgende methode toe aan uw `MainWindow.xaml.cs`-bestand:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync(); 

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>Meer informatie over gebruikers afmelden

Met de `SignOutButton_Click`-methode worden gebruikers uit de MSAL-gebruikers cache verwijderd, waardoor de huidige gebruiker in feite wordt gevraagd om een token te verkrijgen, alleen als dit interactief wordt gemaakt.

Hoewel de toepassing in dit voor beeld enkele gebruikers ondersteunt, ondersteunt MSAL scenario's waarbij meerdere accounts tegelijkertijd kunnen worden aangemeld. Een voor beeld is een e-mail toepassing waarbij een gebruiker meerdere accounts heeft.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Elementaire token gegevens weer geven

Als u basis informatie over het token wilt weer geven, voegt u de volgende methode toe aan uw *mainwindow.xaml.cs* -bestand:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

<!--start-collapse-->
### <a name="more-information"></a>Meer informatie

Naast het toegangs token dat wordt gebruikt om de Microsoft Graph-API aan te roepen, verkrijgt MSAL ook nadat de gebruiker zich heeft aangemeld, een ID-token. Dit token bevat een kleine subset van informatie die relevant is voor gebruikers. De methode `DisplayBasicTokenInfo` geeft de basis informatie weer die in het token is opgenomen. Zo worden de weergave naam en-ID van de gebruiker weer gegeven, evenals de verval datum van het token en de teken reeks die het toegangs token zelf vertegenwoordigt. U kunt de knop *Call Microsoft Graph API* meerdere keren selecteren en zien dat hetzelfde token opnieuw is gebruikt voor volgende aanvragen. U kunt ook zien hoe lang de verval datum wordt verlengd wanneer MSAL beslist dat het token moet worden vernieuwd.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
