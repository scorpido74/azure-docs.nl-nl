---
title: Bescherm SPA-backend met OAuth 2.0 met Azure Active Directory B2C en Azure API Management.
description: Bescherm een API met OAuth 2.0 met Azure Active Directory B2C, Azure API Management en Easy Auth om te worden aangeroepen vanuit een JavaScript SPA.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new
ms.openlocfilehash: 55acea360de11c5fcc699d65daf92cf24dfd691d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475473"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>Bescherm SPA-backend met OAuth 2.0, Azure Active Directory B2C en Azure API Management

In dit scenario ziet u hoe u uw Azure API Management-exemplaar configureert om een API te beschermen.
We gebruiken het OAuth 2.0-protocol met Azure AD B2C, naast API-beheer, om een back-end van Azure Functions te beveiligen met EasyAuth.

## <a name="aims"></a>Doelstellingen
We gaan kijken hoe API-beheer kan worden gebruikt in een vereenvoudigd scenario met Azure Functions en Azure AD B2C. U maakt een JavaScript-app (JS) die een API aanroept, die gebruikers aanmeldt met Azure AD B2C. Vervolgens gebruikt u de validate-jwt-beleidsfuncties van API Management om de Backend API te beschermen.

Voor de verdediging in de diepte gebruiken we EasyAuth om het token opnieuw te valideren in de back-end API.

## <a name="prerequisites"></a>Vereisten
Als u de stappen in dit artikel wilt volgen, moet u het volgende hebben:
* Een Azure (StorageV2) V2-opslagaccount voor algemeen gebruik om de frontend JS-app voor één pagina te hosten
* Een Azure API Management-exemplaar 
* Een lege Azure Function-app (met de V2 .NET Core-runtime op een Windows-verbruiksplan) om de zogenaamde API te hosten
* Een Azure AD B2C-tenant, gekoppeld aan een abonnement 

Hoewel u in de praktijk resources in dezelfde regio zou gebruiken in productieworkloads, is voor dit handleidingartikel de regio van implementatie niet belangrijk.

## <a name="overview"></a>Overzicht
Hier is een illustratie van de componenten in gebruik en de stroom tussen hen zodra dit proces is voltooid.
![Componenten in gebruik en stroom](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "Componenten in gebruik en stroom")

Hier is een snel overzicht van de stappen:

1. De Azure AD B2C-aanroepen (Frontend,API-beheer) en API-toepassingen maken met scopes en API-toegang verlenen
1. Het aanmeldings- of aanmeldingsbeleid maken om gebruikers in staat te stellen zich aan te melden bij Azure AD B2C 
1. API-beheer configureren met de nieuwe Azure AD B2C-client--i-d's en -sleutels voor OAuth2-gebruikersautorisatie inschakelen in de ontwikkelaarsconsole
1. De functie-API bouwen
1. Configureer de functie-API om EasyAuth in te schakelen met de nieuwe Azure AD B2C Client ID's en -sleutels en vergrendel af op APIM VIP 
1. De API-definitie bouwen in API-beheer
1. Oauth2 instellen voor de API-configuratie van API-beheer
1. Stel het **CORS-beleid** in en voeg het **validate-jwt-beleid** toe om het OAuth-token voor elke binnenkomende aanvraag te valideren
1. De aanroepende toepassing bouwen om de API te gebruiken
1. Upload de JS SPA Sample
1. De voorbeeld-JS-clientapp configureren met de nieuwe Azure AD B2C-client-id's en -sleutels 
1. De clienttoepassing testen

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C configureren 
Open het Azure AD B2C-blad in de portal en volg de volgende stappen.
1. Het tabblad **Toepassingen** selecteren 
1. Klik op de knop 'Toevoegen' en maak drie toepassingen voor de volgende doeleinden
   * De Frontend Client.
   * De API voor backendfunctie.
   * [Optioneel] De API Management-ontwikkelaarsportal (tenzij u Azure API-beheer uitvoert in de verbruikslaag, meer hierover later in dit scenario).
1. Stel WebApp / Web API in voor alle 3 toepassingen en stel 'Impliciete stroom toestaan' in op ja voor alleen de Frontend-client.
1. Stel nu de App ID URI in, kies iets unieks en relevants voor de service die wordt gemaakt.
1. Gebruik tijdelijke aanduidingen voor het antwoord https://localhosturls voor nu, zoals , zullen we deze urls later bijwerken.
1. Klik op 'Maken', herhaal vervolgens stap 2-5 voor elk van de drie apps hierboven, en houd de AppID URI, naam en Applicatie-id voor later gebruik voor alle drie de apps vast.
1. Open de API Management Developer Portal-toepassing in de lijst met toepassingen en selecteer het tabblad *Sleutels* (onder Algemeen) en klik op 'Sleutel genereren' om een auth-toets te genereren
1. Bij het klikken op opslaan, neem de sleutel ergens veilig voor later gebruik - merk op dat deze plek is de enige kans krijg je te bekijken en kopiëren van deze sleutel.
1. Selecteer nu het tabblad *Gepubliceerde scopes* (onder API-toegang)
1. Maak en noem een scope voor uw functie-API en neem de scope op en vul de volledige scopewaarde in en klik vervolgens op 'Opslaan'.
   > [!NOTE]
   > Azure AD B2C-scopes zijn effectief machtigingen binnen uw API waartoe andere toepassingen toegang kunnen vragen via het API-toegangsblad van hun toepassingen, waardoor u in feite alleen toepassingsmachtigingen hebt gemaakt voor uw zogenaamde API.
1. Open de andere twee toepassingen en kijk onder het tabblad *API Access.*
1. Geef ze toegang tot de backend API-scope en de standaard-api die er al was ('Toegang tot het profiel van de gebruiker').
1. Genereer ze een sleutel door het tabblad *Toetsen* onder 'Algemeen' te selecteren om een auth-toets te genereren en die sleutels ergens veilig voor later op te nemen.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Een gebruikersstroom aanmelden of aanmelden maken
1. Terug naar de hoofdmap (of 'Overzicht') van het Azure AD B2C-blad 
1. Selecteer vervolgens 'Gebruikersstromen (beleid)' en klik op 'Nieuwe gebruikersstroom'
1. Kies het type gebruikersstroom 'Aanmelden en aanmelden'
1. Geef het beleid een naam en neem het op voor later.
1. Controleer vervolgens onder 'Identiteitsproviders' 'Meld gebruikersnaam' (dit kan 'E-mailaanmelden' zeggen) en klik op OK. 
1. Klik onder 'Gebruikerskenmerken en -claims' op 'Meer weergeven...' kies vervolgens de claimopties die uw gebruikers moeten invoeren en die zijn geretourneerd in het token. Controleer ten minste 'Weergavenaam' en 'E-mailadres' om te verzamelen en terug te keren, en klik op 'OK', en klik vervolgens op 'Maken'.
1. Selecteer het beleid dat u in de lijst hebt gemaakt en klik op de knop Gebruikersstroom uitvoeren.
1. Met deze actie wordt het run user flow blade geopend, selecteert u de frontend-toepassing en registreert u het adres van het b2clogin.com domein dat wordt weergegeven onder de vervolgkeuzelijst voor 'Domein selecteren'.
1. Klik op de link bovenaan om het 'bekende openid configuratieeindpunt' te openen en de authorization_endpoint en token_endpoint waarden vast te leggen, evenals van de waarde van de link zelf als het bekende openid configuratieeindpunt.

   > [!NOTE]
   > Met B2C-beleidsregels u de Azure AD B2C-aanmeldingseindpunten blootleggen om verschillende gegevenscomponenten vast te leggen en gebruikers op verschillende manieren aan te melden. In dit geval hebben we een aanmeldings- of aanmeldingseindpunt geconfigureerd, dat een bekend configuratieeindpunt blootlegde, specifiek ons gemaakte beleid werd in de URL geïdentificeerd door de p=-parameter.
   > 
   > Zodra dit is gedaan - u hebt nu een functioneel Business to Consumer-identiteitsplatform dat gebruikers in meerdere toepassingen zal ondertekenen. 
   > Als u wilt u hier op de knop 'Gebruikersstroom uitvoeren' klikken (om door het aanmeldings- of aanmeldingsproces te gaan) en een gevoel te krijgen voor wat het in de praktijk zal doen, maar de omleidingsstap aan het einde mislukt omdat de app nog niet is geïmplementeerd.

## <a name="build-the-function-api"></a>De functie-API bouwen
1. Terug naar uw standaard Azure AD-tenant in de Azure-portal, zodat we items in uw abonnement opnieuw kunnen configureren 
1. Ga naar het blade functie-apps van de Azure-portal, open uw lege functie-app en maak vervolgens een nieuwe In-Portal 'Webhook + API'-functie via de quickstart.
1. Plak de voorbeeldcode van onderen in Run.csx boven de bestaande code die wordt weergegeven.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > De c# script functie code die u net geplakt gewoon een lijn aan de functies logs logs, en retourneert de tekst "Hello World" met een aantal dynamische gegevens (de datum en tijd).

3. Selecteer 'Integreren' in het linkerblad en selecteer vervolgens 'Geavanceerde editor' in de rechterbovenhoek van het deelvenster.
4. Plak de onderstaande voorbeeldcode boven de bestaande json.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. Ga terug naar het tabblad HttpTrigger1, klik op 'URL van functie ophalen' en kopieer vervolgens de URL die wordt weergegeven.

   > [!NOTE]
   > De bindingen die u zojuist hebt gemaakt, vertellen functies om te reageren op anonieme http GET-verzoeken op de URL die u zojuist hebt gekopieerd. (https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) Nu hebben we een schaalbare serverless https API, die in staat is om een zeer eenvoudige payload terug te sturen.
   > U nu testen bellen met deze API vanuit een webbrowser met behulp van de URL hierboven, u ook strippen de ?code = geheim gedeelte van de URL en bewijzen dat Azure Functions zal een 401 fout terug.

## <a name="configure-and-secure-the-function-api"></a>De functie-API configureren en beveiligen
1. Twee extra gebieden in de functie-app moeten worden geconfigureerd (Auth- en netwerkbeperkingen).
1. Ten eerste laten we verificatie / autorisatie configureren, dus klik op &lt;de&gt; naam van de functie-app (naast het pictogram Z-functies) om de overzichtspagina weer te geven.
1. Selecteer vervolgens het tabblad 'Platformfuncties' en selecteer 'Verificatie / autorisatie'.
1. Schakel de functie Verificatie van appservice in.
1. Kies onder 'Verificatieproviders' 'Azure Active Directory' en kies 'Geavanceerd' in de switch Beheermodus.
1. De toepassings-id van de backendfunctie-API plakken (van Azure AD B2C in het vak 'Client-id')
1. Plak het bekende eindpunt van de open id-configuratie uit het aanmeldings- of aanmeldingsbeleid in het vak URL van de uitgever (we hebben deze configuratie eerder opgenomen).
1. Selecteer OK.
1. Stel de actie in die u wilt uitvoeren wanneer de aanvraag niet is geverifieerd in de vervolgkeuzelijst 'Aanmelden bij Azure Active Directory' en klik op Opslaan.

   > [!NOTE]
   > Nu is uw functie-API geïmplementeerd en moet u 401 reacties geven als de juiste sleutel niet wordt geleverd en gegevens retourneren wanneer een geldig verzoek wordt gepresenteerd.
   > U hebt extra beveiliging toegevoegd in EasyAuth door de optie 'Inloggen met Azure AD' te configureren om niet-geverifieerde aanvragen te verwerken. Houd er rekening mee dat dit het ongeautoriseerde verzoekgedrag tussen de Backend Function App en Frontend SPA zal veranderen, aangezien EasyAuth een 302-omleiding naar AAD zal geven in plaats van een 401 Niet geautoriseerde reactie, zullen we dit later corrigeren met API Management.
   > We hebben nog steeds geen IP-beveiliging toegepast, als u een geldige sleutel en OAuth2-token hebt, kan iedereen dit overal bellen - idealiter willen we alle verzoeken dwingen om via API Management te komen.
   > Als u de API Management-verbruikslaag gebruikt, u deze vergrendeling niet uitvoeren door VIP omdat er geen specifiek statisch IP-adres voor die laag is, u moet vertrouwen op de methode om uw API-aanroepen te vergrendelen via de gedeelde geheime functiesleutel , dus stappen 11-13 zijn niet mogelijk.

1. Het mes 'Verificatie / autorisatie' sluiten 
1. Selecteer 'Netwerken' en selecteer vervolgens 'Toegangsbeperkingen'
1. Vergrendel vervolgens de toegestane functie-app IP's naar het API Management-exemplaar VIP. Deze VIP wordt getoond in het API-beheer - overzichtsgedeelte van de portal.
1. Als u wilt blijven communiceren met de functies portal, en om de optionele stappen hieronder uit te voeren, moet u hier ook uw eigen openbare IP-adres of CIDR-bereik toevoegen.
1. Zodra er een vermelding toestaan in de lijst is, voegt Azure een impliciete weigeringsregel toe om alle andere adressen te blokkeren. 

U moet CIDR-opgemaakte adressenblokken toevoegen aan het deelvenster IP-beperkingen. When you need to add a single address such as the API Management VIP, you need to add it in the format xx.xx.xx.xx.

   > [!NOTE]
   > Nu mag uw functie-API nergens anders bereikbaar zijn dan via API-beheer of uw adres.
   
## <a name="import-the-function-app-definition"></a>De functie-app-definitie importeren
1. Open het *API-beheerblad*en open *vervolgens uw instantie.*
1. Selecteer het API-blad in de sectie API-beheer van uw instantie.
1. Kies in het deelvenster 'Een nieuwe API toevoegen' de optie 'Functie-app' en selecteer vervolgens 'Vol' boven aan de pop-up.
1. Klik op Bladeren, kies de functie-app die u host van de API binnenin en klik op Selecteren.
1. Geef de API een naam en beschrijving voor het interne gebruik van API Management en voeg deze toe aan het 'onbeperkte' product.
1. Zorg ervoor dat u de basis-URL voor later gebruik opneemt en klik vervolgens op Maken.

## <a name="configure-oauth2-for-api-management"></a>Oauth2 configureren voor API-beheer

1. Selecteer vervolgens het Oauth 2.0-blad op het tabblad Beveiliging en klik op 'Toevoegen'
1. Geef waarden op voor *Weergavenaam* en *beschrijving* voor het toegevoegde Oauth-eindpunt (deze waarden worden in de volgende stap weergegeven als een Oauth2-eindpunt).
1. U elke waarde invoeren in de URL van de clientregistratiepagina, omdat deze waarde niet wordt gebruikt.
1. Controleer het type *Impliciete Auth-subsidie* en laat het type autorisatiecodeverlenen controleren.
1. Ga naar de velden *Autorisatie-* en *Token-eindpunt* en voer de waarden in die u eerder hebt vastgelegd uit het bekende xml-document voor configuratie.
1. Schuif omlaag en vul een *parameter extra hoofdtekst* genaamd 'resource' in met de backendfunctie-API-client-id van de Azure AD B2C-app-registratie
1. Selecteer 'Clientreferenties', stel de client-id in op de app-id van de ontwikkelaarsconsole-app - sla deze stap over als u het beheermodel voor verbruiks-API-beheer gebruikt.
1. Stel het clientgeheim in op de sleutel die u eerder hebt opgenomen - sla deze stap over als u het api-beheermodel voor verbruik gebruikt.
1. Tot slot, nu opnemen van de redirect_uri van de auth code subsidie van API Management voor later gebruik.

## <a name="set-up-oauth2-for-your-api"></a>Oauth2 instellen voor uw API
1. Uw API verschijnt aan de linkerkant van de portal onder de sectie 'Alle API's' en opent uw API door erop te klikken.
1. Selecteer het tabblad 'Instellingen'.
1. Werk uw instellingen bij door "Oauth 2.0" te selecteren op de keuzerondje voor gebruikersautorisatie.
1. Selecteer de Oauth-server die u eerder hebt gedefinieerd.
1. Schakel het selectievakje 'Scope overschrijven' in en voer het bereik in dat u eerder hebt geregistreerd voor de backend API-aanroep.

   > [!NOTE]
   > Nu hebben we een API Management-exemplaar dat weet hoe je toegangstokens van Azure AD B2C krijgen om aanvragen te autoriseren en onze Oauth2 Azure Active Directory B2C-configuratie begrijpt.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>Het **CORS-** en **validate-jwt-beleid** instellen

> De volgende secties moeten worden gevolgd, ongeacht de apim-laag die wordt gebruikt. 

1. Ga terug naar het tabblad ontwerpen en kies 'Alle API's' en klik vervolgens op de knop codeweergave om de beleidseditor weer te geven.
1. Bewerk de inkomende sectie en plak de onderstaande xml zodat deze als volgt leest.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. Bewerk de openid-config-url die overeenkomt met uw bekende Azure AD B2C-eindpunt voor het aanmeldings- of aanmeldingsbeleid.
1. Bewerk de claimwaarde om overeen te komen met de geldige toepassings-id, ook wel client-id voor de backend API-toepassing genoemd en opslaan.

   > [!NOTE]
   > Api-beheer kan nu reageren op cross origin-aanvragen voor JS SPA-apps en het zal throttling, tariefbeperkende en prevalidatie uitvoeren van het JWT auth-token dat wordt doorgegeven voordat het verzoek wordt doorgestuurd naar de Functie-API.

   > [!NOTE]
   > De volgende sectie is optioneel en is niet van toepassing op de **sectie Verbruik,** die de ontwikkelaarsportal niet ondersteunt.
   > Als u niet van plan bent om de ontwikkelaarsportal te gebruiken of deze niet gebruiken omdat u de verbruikslaag gebruikt, slaat u deze stap over en springt u rechtstreeks naar [Bouw de JavaScript SPA om de API te gebruiken.](#build-the-javascript-spa-to-consume-the-api)

## <a name="optional-configure-the-developer-portal"></a>[Optioneel] De ontwikkelaarsportal configureren

1. Open het Azure AD B2C-blad en navigeer naar de toepassingsregistratie voor de ontwikkelaarsportal
1. Stel de vermelding 'Url van het antwoord' in op de vermelding die u hebt genoteerd toen u de redirect_uri van de auth-codesubsidie eerder in API-beheer configureerde.

   Nu de OAuth 2.0-gebruikersautorisatie is `Echo API`ingeschakeld op de , verkrijgt de Developer Console een toegangstoken voor de gebruiker, voordat de API wordt aangeroepen.

1. Blader naar elke `Echo API` bewerking onder de in de ontwikkelaarsportal en selecteer **Probeer deze** om u naar de ontwikkelaarsconsole te brengen.
1. Let op een nieuw item in de sectie **Autorisatie,** overeenkomend met de autorisatieserver die u zojuist hebt toegevoegd.
1. Selecteer **Autorisatiecode** in de vervolgkeuzelijst autorisatie en u wordt gevraagd zich aan te melden bij de Azure AD-tenant. Als je al bent aangemeld met het account, wordt je mogelijk niet gevraagd.
1. Na een succesvolle aanmelding wordt een `Authorization: Bearer` koptekst aan de aanvraag toegevoegd, met een toegangstoken van Azure AD B2C gecodeerd in Base64. 
1. Selecteer **Verzenden** en u de API met succes aanroepen.

   > [!NOTE]
   > Nu is API-beheer in staat om tokens te verwerven voor de ontwikkelaarsportal om uw API te testen en is het in staat om de definitie ervan te begrijpen en de juiste testpagina in de dev-portal weer te geven.

1. Klik in het overzichtsblad van de API Management-portal op 'Developer Portal' om u aan te melden als beheerder van de API.
1. Hier kunnen u en andere geselecteerde consumenten van uw API ze testen en aanroepen vanaf een console.
1. Selecteer 'Producten', kies vervolgens 'Onbeperkt', kies vervolgens de API die we eerder hebben gemaakt en klik op 'PROBEER IT'
1. Verberg de API-abonnementssleutel en kopieer deze ergens veilig samen met de url van het verzoek die u later nodig hebt.
1. Selecteer ook Impliciet, in de oauth auth dropdown en je kan hebben om hier te verifiëren met een pop-up.
1. Klik op 'Verzenden' en als alles goed is, moet uw functie-app reageren met een hallo bericht via API-beheer met een 200 OK-bericht en een JSON.

   > [!NOTE]
   > Gefeliciteerd, u hebt nu Azure AD B2C, API Management en Azure-functies die samenwerken om een API te publiceren, te beveiligen en te gebruiken. Het is u misschien opgevallen dat de API in feite twee keer beveiligd is met deze methode, één keer met de API Management Ocp-Subscription-Key Header en eenmaal met de Autorisatie: OWT aan toonder.
   > U zou juist zijn, omdat dit voorbeeld een JavaScript Single Page Application is, gebruiken we de API-beheersleutel alleen voor tariefbeperkende en factureringsoproepen.
   > De werkelijke autorisatie en verificatie wordt verwerkt door Azure AD B2C en is ingekapseld in de JWT, die twee keer wordt gevalideerd, één keer door API-beheer en vervolgens door Azure-functies.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>Bouw de JavaScript SPA om de API te gebruiken
1. Het blad voor opslagaccounts openen in de Azure-portal 
1. Selecteer het account dat u hebt gemaakt en selecteer het blad 'Statische website' in de sectie Instellingen (als u geen optie 'Statische website' ziet, controleert u of u een V2-account hebt gemaakt).
1. Stel de statische webhostingfunctie in op 'ingeschakeld' en stel de naam van het indexdocument in op 'index.html', klik vervolgens op 'opslaan'.
1. Noteer de inhoud van het primaire eindpunt, want op deze locatie wordt de frontendsite gehost. 

   > [!NOTE]
   > U Azure Blob Storage + CDN herschrijven of Azure App Service gebruiken, maar de statische websitehostingfunctie van Blob Storage geeft ons een standaardcontainer voor het weergeven van statische webinhoud / html / js / css van Azure Storage en leidt een standaardpagina voor ons af voor nul werk.

## <a name="upload-the-js-spa-sample"></a>Upload het JS SPA-voorbeeld
1. Selecteer nog steeds in het blad van het opslagaccount het 'Blobs'-blad in de sectie Blob-service en klik op de $web-container die in het rechterdeelvenster wordt weergegeven.
1. Sla de onderstaande code op in een bestand lokaal op uw machine als index.html en upload vervolgens de bestandsindex.html naar de $web container.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. Blader naar het primaire eindpunt van de statische website dat u eerder in de laatste sectie hebt opgeslagen.

   > [!NOTE]
   > Gefeliciteerd, u hebt zojuist een JavaScript-app voor één pagina geïmplementeerd in Azure Storage Omdat we de JS-app nog niet hebben geconfigureerd met uw sleutels voor de api of de JS-app hebben geconfigureerd met uw Azure AD B2C-gegevens , werkt de pagina nog niet als u deze opent.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>De JS SPA configureren voor Azure AD B2C
1. Nu weten we waar alles is: we kunnen de SPA configureren met het juiste API Management API-adres en de juiste Azure AD B2C-toepassing / client-iD's
1. Ga terug naar het azure-portalopslagblad en klik op index.html en kies vervolgens 'Blob bewerken' 
1. Werk de auth-details bij om overeen te komen met uw front-end toepassing die u eerder in B2C hebt geregistreerd, waarbij u erop valt dat de 'b2cScopes'-waarden voor de API-backend zijn.
1. De webApi-sleutel en api-url zijn te vinden in het api-beheertestvenster voor de API-bewerking.
1. Maak een APIM-abonnementssleutel door naar het API-beheer terug te gaan naar het API-beheerblad, 'Abonnementen' te selecteren en op 'Abonnement toevoegen' te klikken en vervolgens de record op te slaan. Als u op de Ellipsis (...) naast de gemaakte rij klikt, u de toetsen weergeven, zodat u de primaire sleutel kopiëren.
1. Het moet er ongeveer zo uitzien als de onderstaande code:-  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. Op Opslaan klikken

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>De omleidings-URI's instellen voor de Azure AD B2C-frontend-app
1. Open het Azure AD B2C-blad en navigeer naar de toepassingsregistratie voor de JavaScript Frontend-toepassing
1. De URL van de omleiding instellen op de URL die u hebt genoteerd toen u het primaire eindpunt van de statische website hierboven instelde

   > [!NOTE] 
   > Deze configuratie zal resulteren in een client van de frontend-toepassing die een toegangstoken ontvangt met de juiste claims van Azure AD B2C.
   > De SPA zal in staat zijn om dit toe te voegen als een drager token in de https header in de call to the backend API.
   > API-beheer valideert vooraf de token- en rate-limit-aanroepen naar het eindpunt door de abonneesleutel, voordat het verzoek wordt doorgegeven aan de ontvangende Azure Function API.
   > De SPA zal het antwoord in de browser.

   > *Gefeliciteerd, u hebt Azure AD B2C, Azure API Management, Azure Functions, Azure App Service Authorization geconfigureerd om in perfecte harmonie te werken!*

   > [!NOTE]
   > Nu hebben we een eenvoudige app met een eenvoudige beveiligde API, laten we het testen.

## <a name="test-the-client-application"></a>De clienttoepassing testen
1. Open de URL van de voorbeeld-app die u hebt genoteerd vanaf het opslagaccount dat u eerder hebt gemaakt
1. Klik in de rechterbovenhoek op Aanmelden, met deze klik wordt uw Azure AD B2C-aanmeldings- of aanmeldingsprofiel weergegeven.
1. Advertentie Aanmelden in het gedeelte 'Aangemeld als' van het scherm wordt ingevuld vanaf uw JWT.
1. Klik nu op 'Call Web Api' en u moet de pagina bijwerken met de waarden die vanuit uw beveiligde API worden teruggestuurd.

## <a name="and-were-done"></a>En we zijn klaar.
De bovenstaande stappen kunnen worden aangepast en bewerkt om veel verschillende toepassingen van Azure AD B2C met API-beheer mogelijk te maken.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [Azure Active Directory en OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Bekijk meer [video's](https://azure.microsoft.com/documentation/videos/index/?services=api-management) over API Management.
* Zie [Verificatie van wederzijds certificaat](api-management-howto-mutual-certificates.md)voor andere manieren om uw back-endservice te beveiligen.
* [Een API-beheerservice-instantie maken](get-started-create-service-instance.md).
* [Beheer uw eerste API.](import-and-publish.md)
