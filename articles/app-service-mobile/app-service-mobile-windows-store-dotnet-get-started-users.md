---
title: Verificatie toevoegen aan uw UWP-app
description: Meer informatie over het gebruik van mobiele apps van Azure App Service om gebruikers van uw UWP-app (Universal Windows Platform) te verifiëren bij identiteitsproviders zoals AAD, Google, Facebook, Twitter en Microsoft.
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 32d4313b345964a2db13d68e83f81756a4acf0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77458933"
---
# <a name="add-authentication-to-your-windows-app"></a>Verificatie toevoegen aan uw Windows-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>Overzicht
In dit onderwerp ziet u hoe u cloudverificatie toevoegt aan uw mobiele app. In deze zelfstudie voegt u verificatie toe aan het QuickStart-project (Universal Windows Platform) voor mobiele apps met behulp van een identiteitsprovider die wordt ondersteund door Azure App Service. Nadat de back-end van uw mobiele app is geverifieerd en geautoriseerd, wordt de waarde van de gebruikersnaam weergegeven.

Deze zelfstudie is gebaseerd op de quickstart van Mobiele Apps. U moet eerst de zelfstudie voltooien [Aan de slag met mobiele apps](app-service-mobile-windows-store-dotnet-get-started.md).

## <a name="register-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Uw app registreren voor verificatie en de App-service configureren
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Uw app toevoegen aan de toegestane URL's voor omleiding

Veilige verificatie vereist dat u een nieuw URL-schema voor uw app definieert. Hierdoor kan het verificatiesysteem worden omgeleid naar uw app zodra het verificatieproces is voltooid. In deze zelfstudie gebruiken we de _URL-appname_ overal. U echter elk URL-schema gebruiken dat u kiest. Het moet uniek zijn voor uw mobiele applicatie. Ga als u de omleiding aan de serverzijde inschakelen:

1. Selecteer uw App-service in de [Azure-portal.](https://ms.portal.azure.com)

2. Klik op de optie **Verificatie / Autorisatie.**

3. Voer in de toegestane externe `url_scheme_of_your_app://easyauth.callback` **omleidings-URL's**.  De **url_scheme_of_your_app** in deze tekenreeks is het URL-schema voor uw mobiele toepassing.  Het moet de normale URL-specificatie voor een protocol volgen (gebruik alleen letters en cijfers en begin met een letter).  U moet een notitie maken van de tekenreeks die u kiest, omdat u uw mobiele toepassingscode op verschillende plaatsen moet aanpassen met het URL-schema.

4. Klik op **Opslaan**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Machtigingen beperken tot geverifieerde gebruikers
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Nu u controleren of anonieme toegang tot uw backend is uitgeschakeld. Met het PROJECT UWP-app ingesteld als het start-upproject, implementeer en voer u de app uit; controleer of een niet-behandelde uitzondering met een statuscode van 401 (Ongeautoriseerd) wordt verhoogd nadat de app is gestart. Dit gebeurt omdat de app probeert toegang te krijgen tot uw mobiele app-code als een niet-geverifieerde gebruiker, maar de *TodoItem-tabel* vereist nu verificatie.

Vervolgens werkt u de app bij om gebruikers te verifiëren voordat u resources opvraagt bij uw App-service.

## <a name="add-authentication-to-the-app"></a><a name="add-authentication"></a>Verificatie toevoegen aan de app
1. In het projectbestand van de UWP-app MainPage.xaml.cs en het volgende codefragment toevoegen:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Deze code verifieert de gebruiker met een Facebook-login. Als u een andere identiteitsprovider dan Facebook gebruikt, wijzigt u de waarde van **MobileServiceAuthenticationProvider** hierboven in de waarde voor uw provider.
2. Vervang de methode **OnNavigatedTo()** in MainPage.xaml.cs. Vervolgens voegt u een **knop Aanmelden** toe aan de app die verificatie activeert.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Voeg het volgende codefragment toe aan de MainPage.xaml.cs:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. Open het projectbestand MainPage.xaml, zoek het element dat de knop **Opslaan** definieert en vervang het door de volgende code:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Voeg het volgende codefragment toe aan de App.xaml.cs:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Open package.appxmanifest-bestand, navigeer naar **Declaratie**, selecteer in de vervolgkeuzelijst **Beschikbare declaratie,** selecteer **Protocol** en klik op **Knop Toevoegen.** Configureer nu de **eigenschappen** van de **protocoldeclaratie.** Voeg in **Weergavenaam**de naam toe die u wilt weergeven aan gebruikers van uw toepassing. Voeg in **Naam**uw {url_scheme_of_your_app} toe.
7. Druk op de F5-toets om de app uit te voeren, klik op de knop **Aanmelden** en meld u aan bij de app bij de door u gekozen identiteitsprovider. Nadat uw aanmelding is geslaagd, wordt de app foutloos uitgevoerd en u uw backend opvragen en gegevens bijwerken.

## <a name="store-the-authentication-token-on-the-client"></a><a name="tokens"></a>Het verificatietoken opslaan op de client
In het vorige voorbeeld werd een standaardaanmelding getoond, waarbij de client contact moet opnemen met zowel de identiteitsprovider als de app-service telkens wanneer de app wordt gestart. Niet alleen is deze methode inefficiënt, u problemen met gebruiksrelaties tegenkomen als veel klanten tegelijkertijd proberen uw app te starten. Een betere aanpak is om het autorisatietoken dat door uw App-service is geretourneerd, in de cache te bewaren en dit eerst te gebruiken voordat u een op provider gebaseerde aanmelding gebruikt.

> [!NOTE]
> U het token dat door App Services is uitgegeven, in de cache opslaan, ongeacht of u verificatie met clientbeheerde of servicebeheerde verificatie gebruikt. Deze zelfstudie maakt gebruik van servicebeheerverificatie.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u deze basisverificatiezelfstudie hebt voltooid, u overwegen verder te gaan naar een van de volgende zelfstudies:

* [Pushmeldingen toevoegen aan uw app](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Informatie over het toevoegen van ondersteuning van pushmeldingen aan uw app en het configureren van de backend voor mobiele apps voor gebruik van Azure Notification Hubs voor het verzenden van pushmeldingen.
* [Offlinesynchronisatie voor uw app inschakelen](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Informatie over het toevoegen van offlineondersteuning aan uw app met een back-end voor mobiele apps. Met offlinesynchronisatie kunnen eindgebruikers interactie aangaan met een mobiele app&mdash;gegevens weergeven, toevoegen of wijzigen&mdash;ook als er geen netwerkverbinding is.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
