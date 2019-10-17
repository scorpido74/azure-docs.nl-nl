---
title: Verificatie toevoegen aan uw Universeel Windows-platform-app (UWP) | Microsoft Docs
description: 'Informatie over het gebruik van Azure App Service Mobile Apps om gebruikers van uw Universeel Windows-platform (UWP)-app te verifiëren met behulp van verschillende id-providers, waaronder: AAD, Google, Facebook, Twitter en micro soft.'
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d5012ccc503e48785e23ff00564bbc9f6735eea8
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388536"
---
# <a name="add-authentication-to-your-windows-app"></a>Verificatie toevoegen aan uw Windows-app
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan bij [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht
In dit onderwerp wordt beschreven hoe u Cloud verificatie kunt toevoegen aan uw mobiele app. In deze zelf studie voegt u verificatie toe aan het project Universeel Windows-platform (UWP) Quick start voor Mobile Apps met behulp van een id-provider die door Azure App Service wordt ondersteund. Nadat de authenticatie door de back-end van uw mobiele app is geverifieerd en geautoriseerd, wordt de waarde van de gebruikers-ID weer gegeven.

Deze zelf studie is gebaseerd op de Mobile Apps Quick Start. U moet eerst de zelf studie [aan de slag met Mobile apps](app-service-mobile-windows-store-dotnet-get-started.md)volt ooien.

## <a name="register"></a>Registreer uw app voor verificatie en configureer de App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Uw app toevoegen aan de toegestane externe omleidings-Url's

Voor beveiligde verificatie moet u een nieuw URL-schema definiëren voor uw app. Hierdoor kan het verificatie systeem terugkeren naar uw app nadat het verificatie proces is voltooid. In deze zelf studie gebruiken we het URL-schema _AppName_ in. U kunt echter elk gewenst URL-schema gebruiken. Deze moet uniek zijn voor uw mobiele toepassing. De omleiding aan de server zijde inschakelen:

1. Selecteer uw App Service in de [Azure Portal](https://ms.portal.azure.com).

2. Klik op de menu optie voor **verificatie/autorisatie** .

3. Voer in de **toegestane externe omleidings-url's**`url_scheme_of_your_app://easyauth.callback` in.  De **url_scheme_of_your_app** in deze teken reeks is het URL-schema voor uw mobiele toepassing.  De standaard URL-specificatie voor een protocol moet volgen (alleen letters en cijfers gebruiken en beginnen met een letter).  U moet een notitie maken van de teken reeks die u kiest, omdat u de code van uw mobiele toepassing moet aanpassen aan het URL-schema op verschillende locaties.

4. Klik op **Opslaan**.

## <a name="permissions"></a>Machtigingen voor geverifieerde gebruikers beperken
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

U kunt nu controleren of anonieme toegang tot uw back-end is uitgeschakeld. Als het UWP-app-project is ingesteld als het start project, implementeert u de app en voert u deze uit. Controleer of er een niet-verwerkte uitzonde ring met de status code 401 (niet toegestaan) wordt gegenereerd nadat de app is gestart. Dit gebeurt omdat de app de code van uw mobiele app probeert te openen als een niet-geverifieerde gebruiker, maar voor de tabel *TodoItem* nu verificatie is vereist.

Vervolgens werkt u de app bij om gebruikers te verifiëren voordat ze resources van uw App Service aanvragen.

## <a name="add-authentication"></a>Verificatie toevoegen aan de app
1. In het UWP-app project bestand MainPage.xaml.cs en voeg het volgende code fragment toe:
   
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
   
    Deze code verifieert de gebruiker met een Facebook-aanmelding. Als u een andere ID-provider gebruikt dan Facebook, wijzigt u de waarde van **MobileServiceAuthenticationProvider** hierboven in de waarde voor uw provider.
2. Vervang de methode **OnNavigatedTo ()** in MainPage.xaml.cs. Vervolgens voegt u een knop **Aanmelden** toe aan de app die verificatie activeert.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Voeg het volgende code fragment toe aan de MainPage.xaml.cs:
   
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
4. Open het project bestand MainPage. xaml, zoek het-element dat de knop **Opslaan** definieert en vervang dit door de volgende code:
   
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
5. Voeg het volgende code fragment toe aan de App.xaml.cs:

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
6. Open Package. appxmanifest-bestand, navigeer naar **declaraties**in de vervolg keuzelijst **beschik bare aangiften** , selecteer **protocol** en klik op de knop **toevoegen** . Configureer nu de **Eigenschappen** van de **protocol** declaratie. In **weergave naam**, voegt u de naam die u wilt weer geven toe aan gebruikers van uw toepassing. Voeg uw {url_scheme_of_your_app} toe aan de **naam**.
7. Druk op de toets F5 om de app uit te voeren, klik op de knop **Aanmelden** en meld u aan bij de app met uw gekozen ID-provider. Nadat uw aanmelding is gelukt, wordt de app zonder fouten uitgevoerd en kunt u een query uitvoeren op uw back-end en zo gegevens bijwerken.

## <a name="tokens"></a>Het verificatie token opslaan op de client
In het vorige voor beeld is een standaard aanmelding weer gegeven. hiervoor moet de client contact opnemen met de ID-provider en de App Service elke keer dat de app wordt gestart. Deze methode is niet alleen inefficiënt, u kunt deze gebruiken om problemen op te lossen. veel klanten proberen op hetzelfde moment de app te starten. Een betere benadering is het in de cache opslaan van het autorisatie token dat door uw App Service is geretourneerd en om dit eerst te gebruiken voordat u een aanmelding op basis van een provider gebruikt.

> [!NOTE]
> U kunt het token dat is uitgegeven door App Services, opslaan in de cache, ongeacht of u door de client beheerde of door de service beheerde verificatie gebruikt. In deze zelf studie wordt gebruikgemaakt van service-beheerde authenticatie.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u deze basis verificatie-zelf studie hebt voltooid, kunt u door gaan met een van de volgende zelf studies:

* [Pushmeldingen toevoegen aan uw app](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Informatie over het toevoegen van ondersteuning van pushmeldingen aan uw app en het configureren van de backend voor mobiele apps voor gebruik van Azure Notification Hubs voor het verzenden van pushmeldingen.
* [Offlinesynchronisatie voor uw app inschakelen](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Informatie over het toevoegen van offlineondersteuning aan uw app met een back-end voor mobiele apps. Met offlinesynchronisatie kunnen eindgebruikers interactie aangaan met een mobiele app&mdash;gegevens weergeven, toevoegen of wijzigen&mdash;ook als er geen netwerkverbinding is.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
