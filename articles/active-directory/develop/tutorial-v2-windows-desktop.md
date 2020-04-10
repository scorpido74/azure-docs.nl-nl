---
title: Aan de slag met Microsoft-identiteitsplatform Windows-bureaublad
description: Hoe een XAML-toepassing (Windows Desktop .NET) een toegangstoken kan krijgen en een API kan aanroepen die is beschermd door het Microsoft-identiteitsplatform.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: aa09d06af4706af3ae120f62a897c0bc632fb657
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990936"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>De Microsoft Graph-API aanroepen vanuit een Windows Desktop-app

Deze handleiding laat zien hoe een native Windows Desktop .NET (XAML)-toepassing een toegangstoken gebruikt om de Microsoft Graph API aan te roepen. De app heeft ook toegang tot andere API's waarvoor toegangstokens nodig zijn van een Microsoft-identiteitsplatform voor ontwikkelaars v2.0-eindpunt. Dit platform heette voorheen Azure AD.

Wanneer u de handleiding hebt voltooid, kan uw toepassing een beveiligde API aanroepen die persoonlijke accounts gebruikt (waaronder outlook.com, live.com en anderen). De toepassing gebruikt ook werk- en schoolaccounts van elk bedrijf of organisatie dat Azure Active Directory gebruikt.  

> [!NOTE]
> De gids vereist Visual Studio 2015 Update 3, Visual Studio 2017 of Visual Studio 2019. Heb je geen van deze versies? [Download Visual Studio 2019 gratis](https://www.visualstudio.com/downloads/).

>[!NOTE]
> Als u nieuw bent op het Microsoft-identiteitsplatform, raden we u aan te beginnen met de [Microsoft Graph API aanschaffen en microsoft graph-api aanteroepen vanuit een Windows-bureaublad-app.](quickstart-v2-windows-desktop.md)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Hoe de voorbeeld-app die door deze handleiding wordt gegenereerd, werkt

![Laat zien hoe de voorbeeld-app die door deze zelfstudie wordt gegenereerd, werkt](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

De voorbeeldtoepassing die u met deze handleiding maakt, maakt een Windows Desktop-toepassing mogelijk die de Microsoft Graph-API of een web-API opvraagt die tokens van een eindpunt van een Microsoft-identiteitsplatform accepteert. Voor dit scenario voegt u een token toe aan HTTP-aanvragen via de koptekst Autorisatie. Microsoft Authentication Library (MSAL) verwerkt tokenacquisitie en -vernieuwing.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Token-acquisitie verwerken voor toegang tot beveiligde web-API's

Nadat de gebruiker is geverifieerd, ontvangt de voorbeeldtoepassing een token dat u gebruiken om de Microsoft Graph API of een Web API op te vragen die is beveiligd door het Microsoft-identiteitsplatform voor ontwikkelaars.

API's zoals Microsoft Graph vereisen een token om toegang te verlenen tot specifieke bronnen. Een token is bijvoorbeeld vereist om het profiel van een gebruiker te lezen, toegang te krijgen tot de agenda van een gebruiker of e-mail te verzenden. Uw toepassing kan een toegangstoken aanvragen door MSAL te gebruiken om toegang te krijgen tot deze bronnen door API-scopes op te geven. Dit toegangstoken wordt vervolgens toegevoegd aan de http-autorisatiekop voor elk gesprek dat wordt uitgevoerd tegen de beveiligde bron.

MSAL beheert caching en verfrissende toegangstokens voor u, zodat uw toepassing dat niet hoeft te doen.

## <a name="nuget-packages"></a>NuGet-pakketten

In deze handleiding worden de volgende NuGet-pakketten gebruikt:

|Bibliotheek|Beschrijving|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft-verificatiebibliotheek (MSAL.NET)|

## <a name="set-up-your-project"></a>Uw project instellen

In deze sectie maakt u een nieuw project om aan te tonen hoe u een Windows Desktop .NET-toepassing (XAML) integreren met *Sign-In met Microsoft,* zodat de toepassing web-API's kan opvragen waarvoor een token vereist is.

De toepassing die u met deze handleiding maakt, geeft een knop weer die wordt gebruikt om een grafiek aan te roepen, een gebied om de resultaten op het scherm weer te geven en een afmeldingsknop.

> [!NOTE]
> Download je liever het Visual Studio-project van dit voorbeeld? [Download een project](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)en ga naar de [configuratiestap](#register-your-application) om het codevoorbeeld te configureren voordat u het uitvoert.
>

Ga als volgt te werk om uw toepassing te maken:

1. Selecteer **in** > Visual Studio Bestand**Nieuw** > **project**.
2. Selecteer **Onder Sjablonen**de optie Visual **C#**.
3. Selecteer **WPF App (.NET Framework),** afhankelijk van de versie van Visual Studio die u gebruikt.

## <a name="add-msal-to-your-project"></a>MSAL toevoegen aan uw project

1. Selecteer in Visual Studio **de** > optie Tools**NuGet Package Manager**> **Package Manager Console**.
2. Plak in het venster Package Manager Console de volgende opdracht Azure PowerShell:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Met deze opdracht wordt Microsoft-verificatiebibliotheek geïnstalleerd. MSAL verwerkt het aanschaffen, caching en vernieuwen van gebruikerstokens die worden gebruikt om toegang te krijgen tot de API's die worden beschermd door Azure Active Directory v2.0
    >

## <a name="register-your-application"></a>Uw toepassing registreren

U uw aanvraag op twee manieren registreren.

### <a name="option-1-express-mode"></a>Optie 1: Express-modus

U uw aanvraag snel registreren door het volgende te doen:
1. Ga naar de [Azure-portal - Toepassingsregistratie](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Voer een naam in voor de toepassing en selecteer **Registreren**.
1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.

### <a name="option-2-advanced-mode"></a>Optie 2: Geavanceerde modus

Ga als volgt te werk om de toepassing te registreren en de registratiegegevens van de toepassing toe te voegen aan uw oplossing:
1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
1. Navigeer naar de pagina Microsoft-identiteitsplatform voor ontwikkelaars [App-registraties.](https://go.microsoft.com/fwlink/?linkid=2083908)
1. Selecteer **Nieuwe registratie**.
   - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `Win-App-calling-MsGraph`.
   - Selecteer in de sectie **Ondersteunde accounttypen** de optie **Accounts in alle organisatiemappen en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com**.
   - Selecteer **Registreren** om de toepassing te maken.
1. Selecteer in de lijst met pagina’s voor de app de optie **Verificatie**.
   1. Ga in de sectie **URI's omleiden** in de lijst URI's omleiden:
   1. Selecteer **in** de kolom TYPE De optie **Openbare client/native (mobiel & desktop)**.
   1. Voer in de kolom **URI OMLEIDEN**`https://login.microsoftonline.com/common/oauth2/nativeclient`
1. Selecteer **Registreren**.
1. Ga naar Visual Studio, open het `Enter_the_Application_Id_here` *App.xaml.cs* bestand en vervang in het onderstaande codefragment de toepassings-id die u zojuist hebt geregistreerd en gekopieerd.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>De code toevoegen om MSAL te initialiseren

In deze stap maakt u een klasse om interactie met MSAL te verwerken, zoals het verwerken van tokens.

1. Open het *App.xaml.cs* bestand en voeg de verwijzing voor MSAL toe aan de klasse:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Werk de app-klasse als volgt bij:

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

## <a name="create-the-application-ui"></a>De gebruikersinterface van de toepassing maken

In deze sectie ziet u hoe een toepassing een beveiligde back-endserver zoals Microsoft Graph kan opvragen. 

Een *MainWindow.xaml-bestand* moet automatisch worden gemaakt als onderdeel van uw projectsjabloon. Open dit bestand en vervang het * \<>-knooppunt van* uw toepassing door de volgende code:

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

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>MSAL gebruiken om een token voor de Microsoft Graph API te krijgen

In deze sectie gebruikt u MSAL om een token voor de Microsoft Graph API te krijgen.

1. Voeg in het *MainWindow.xaml.cs* bestand de verwijzing naar MSAL toe aan de klasse:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. Vervang `MainWindow` de klassecode door het volgende:

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

Het `AcquireTokenInteractive` aanroepen van de methode resulteert in een venster dat gebruikers vraagt zich aan te melden. Toepassingen vereisen meestal dat gebruikers zich interactief aanmelden wanneer ze voor het eerst toegang nodig hebben tot een beveiligde bron. Mogelijk moeten ze zich ook aanmelden wanneer een stille bewerking om een token te verkrijgen mislukt (bijvoorbeeld wanneer het wachtwoord van een gebruiker is verlopen).

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

De `AcquireTokenSilent` methode verwerkt tokenacquisities en -verlengingen zonder enige interactie van de gebruiker. Nadat `AcquireTokenInteractive` wordt uitgevoerd voor de `AcquireTokenSilent` eerste keer, is de gebruikelijke methode om te gebruiken om tokens die toegang krijgen tot beschermde bronnen voor latere oproepen te verkrijgen, omdat oproepen om tokens te vragen of te vernieuwen in stilte worden gemaakt.

Uiteindelijk zal `AcquireTokenSilent` de methode mislukken. Redenen voor het mislukken kunnen zijn dat de gebruiker zich heeft afgemeld of zijn wachtwoord op een ander apparaat heeft gewijzigd. Wanneer MSAL detecteert dat het probleem kan worden opgelost door `MsalUiRequiredException` een interactieve actie te vereisen, wordt een uitzondering gemaakt. Uw toepassing kan deze uitzondering op twee manieren verwerken:

* Het kan onmiddellijk `AcquireTokenInteractive` een oproep doen tegen. Deze oproep leidt ertoe dat de gebruiker wordt gevraagd zich aan te melden. Dit patroon wordt meestal gebruikt in online toepassingen waar er geen offline inhoud beschikbaar is voor de gebruiker. Het voorbeeld dat door deze begeleide instelling wordt gegenereerd, volgt dit patroon, dat u in actie zien wanneer u het voorbeeld voor het eerst uitvoert. 

* Omdat geen enkele gebruiker `PublicClientApp.Users.FirstOrDefault()` de toepassing heeft gebruikt, een null-waarde bevat en er een `MsalUiRequiredException` uitzondering wordt gegenereerd. 

* De code in het voorbeeld verwerkt `AcquireTokenInteractive`vervolgens de uitzondering door aan te roepen , wat resulteert in het vragen van de gebruiker om zich aan te melden.

* Het kan in plaats daarvan een visuele indicatie geven aan gebruikers dat een interactieve aanmelding vereist is, zodat ze het juiste moment kunnen selecteren om in te loggen. Of de toepassing `AcquireTokenSilent` kan later opnieuw proberen. Dit patroon wordt vaak gebruikt wanneer gebruikers andere toepassingsfunctionaliteit kunnen gebruiken zonder onderbreking, bijvoorbeeld wanneer offline inhoud beschikbaar is in de toepassing. In dit geval kunnen gebruikers beslissen wanneer ze zich willen aanmelden om toegang te krijgen tot de beveiligde bron of de verouderde informatie vernieuwen. Als alternatief kan de toepassing `AcquireTokenSilent` besluiten om opnieuw te proberen wanneer het netwerk is hersteld nadat het tijdelijk niet beschikbaar is geweest.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Bel de Microsoft Graph API met behulp van het token dat u zojuist hebt verkregen

Voeg de volgende nieuwe `MainWindow.xaml.cs`methode toe aan uw . De methode wordt gebruikt `GET` om een aanvraag in te dienen tegen Graph API met behulp van een koptekst Autoriseren:

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
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Meer informatie over het maken van een REST-aanroep tegen een beveiligde API

In deze voorbeeldtoepassing gebruikt `GetHttpContentWithToken` u de `GET` methode om een HTTP-aanvraag in te dienen tegen een beveiligde bron waarvoor een token vereist is en vervolgens de inhoud terug te sturen naar de beller. Met deze methode wordt het verkregen token toegevoegd aan de koptekst HTTP Autorisatie. Voor dit voorbeeld is de bron het eindpunt van de Microsoft Graph API *me,* waarmee de profielgegevens van de gebruiker worden weergegeven.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Een methode toevoegen om een gebruiker af te melden

Als u een gebruiker wilt afmelden, voegt u de volgende methode toe aan uw `MainWindow.xaml.cs` bestand:

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
### <a name="more-information-about-user-sign-out"></a>Meer informatie over afmelding door gebruiker

De `SignOutButton_Click` methode verwijdert gebruikers uit de MSAL-gebruikerscache, die MSAL effectief vertelt om de huidige gebruiker te vergeten, zodat een toekomstig verzoek om een token te verkrijgen alleen zal slagen als het is gemaakt om interactief te zijn.

Hoewel de toepassing in dit voorbeeld afzonderlijke gebruikers ondersteunt, ondersteunt MSAL scenario's waarin meerdere accounts tegelijkertijd kunnen worden aangemeld. Een voorbeeld is een e-mailtoepassing waarbij een gebruiker meerdere accounts heeft.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Basistokengegevens weergeven

Als u basisinformatie over het token wilt weergeven, voegt u de volgende methode toe aan uw *MainWindow.xaml.cs* bestand:

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

Naast het toegangstoken dat wordt gebruikt om de Microsoft Graph API aan te roepen, verkrijgt MSAL na het inloggen van de gebruiker ook een ID-token. Dit token bevat een kleine subset van informatie die relevant is voor gebruikers. De `DisplayBasicTokenInfo` methode geeft de basisgegevens weer die in het token zijn opgenomen. Het geeft bijvoorbeeld de weergavenaam en id van de gebruiker weer, evenals de vervaldatum van het token en de tekenreeks die het toegangstoken zelf vertegenwoordigt. U de *API-aanroep van Microsoft Graph* meerdere keren selecteren en zien dat hetzelfde token opnieuw is gebruikt voor volgende aanvragen. U ook zien dat de vervaldatum wordt verlengd wanneer MSAL besluit dat het tijd is om het token te vernieuwen.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
