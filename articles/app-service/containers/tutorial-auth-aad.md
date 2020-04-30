---
title: 'Zelf studie: gebruikers verifiëren E2E (Linux)'
description: Meer informatie over het gebruik van App Service verificatie en autorisatie om uw App Service Linux-apps end-to-end te beveiligen, inclusief toegang tot externe Api's.
keywords: app service, azure app service, authN, authZ, beveiligen, beveiliging, meerdere lagen, azure active directory, azure ad
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: seodec18
ms.openlocfilehash: d63c143ddbc702f7f3983a9ae5d6d5f2822d9fdc
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82560345"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service-on-linux"></a>Zelfstudie: Gebruikers end-to-end verifiëren en autoriseren in Azure App Service in Linux

[Azure App Service on Linux](app-service-linux-intro.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem. Daarnaast bevat App Service ingebouwde ondersteuning voor [verificatie en autorisatie van gebruikers](../overview-authentication-authorization.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). In deze zelfstudie leest u hoe u apps kunt beveiligen met verificatie en autorisatie van App Service. Er wordt gebruikgemaakt van een ASP.NET Core-app met een Angular.js-front-end, maar dat is slechts voor het voorbeeld. Verificatie en autorisatie van App Service ondersteunt runtime voor alle talen en u leert hoe u deze kunt toepassen op uw taal van voorkeur door de zelfstudie te volgen.

Er wordt gebruikgemaakt van de voorbeeld-app om te laten zien hoe u een zelfstandige app beveiligt (in [Verificatie en autorisatie inschakelen voor back-end-app](#enable-authentication-and-authorization-for-back-end-app)).

![Eenvoudige verificatie en autorisatie](./media/tutorial-auth-aad/simple-auth.png)

U leest ook hoe u een app met meerdere lagen beveiligt door namens de geverifieerde gebruiker een beveiligde back-end-API te openen, zowel door middel van [servercode](#call-api-securely-from-server-code) als [browsercode](#call-api-securely-from-browser-code).

![Geavanceerde verificatie en autorisatie](./media/tutorial-auth-aad/advanced-auth.png)

Dit zijn slechts enkele van de mogelijke scenario's voor verificatie en autorisatie in App Service. 

Hieronder vindt u een meer uitgebreide lijst met zaken die u in de zelfstudie leert:

> [!div class="checklist"]
> * Ingebouwde verificatie en autorisatie inschakelen
> * Apps beveiligen tegen niet-geverifieerde aanvragen
> * Azure Active Directory gebruiken als id-provider
> * Externe apps openen namens de aangemelde gebruiker
> * Aanroepen tussen services beveiligen met tokenverificatie
> * Toegangstokens gebruiken vanuit servercode
> * Toegangstokens gebruiken vanuit clientcode (browser)

U kunt de stappen in deze zelfstudie volgen voor macOS, Linux en Windows.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

* <a href="https://git-scm.com/" target="_blank">Git installeren</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">De nieuwste versie van .NET Core 3,1 SDK installeren</a>

## <a name="create-local-net-core-app"></a>Lokale .NET Core-app maken

In deze stap stelt u het lokale .NET Core-project in. U gebruikt hetzelfde project voor het implementeren van een back-end-API-app en een front-end-web-app.

### <a name="clone-and-run-the-sample-application"></a>De voorbeeldtoepassing klonen en uitvoeren

Voer de volgende opdrachten uit om de voorbeeldopslagplaats te klonen en voer deze uit.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

Ga naar `http://localhost:5000` en voeg taken toe, bewerk deze en verwijder ze. 

![ASP.NET Core-API lokaal uitvoeren](./media/tutorial-auth-aad/local-run.png)

Druk op `Ctrl+C` in de terminal als u ASP.NET Core wilt stoppen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Apps in Azure implementeren

In deze stap implementeert u het project in twee App Service-apps. De ene is de front-end-app en de andere is de back-end-app.

### <a name="configure-a-deployment-user"></a>Een implementatiegebruiker configureren

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Azure-resources maken

Voer in Azure Cloud Shell de volgende opdrachten uit om twee web-apps te maken. _ \<Front-end-app-name>_ en _ \<back-end-app-name>_ vervangen door twee globaal unieke app-namen (geldige tekens `a-z`zijn `0-9`, en `-`). Zie [een .net core-app maken in azure app service in Linux](quickstart-dotnetcore.md)voor meer informatie over elke opdracht.

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "DOTNETCORE|LTS" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "DOTNETCORE|LTS" --deployment-local-git --query deploymentLocalGitUrl
# Currently the following commands are required to set the .NET Core version properly
az webapp config set --resource-group myAuthResourceGroup --name <front-end-app-name> --linux-fx-version "DOTNETCORE|3.1"
az webapp config set --resource-group myAuthResourceGroup --name <back-end-app-name> --linux-fx-version "DOTNETCORE|3.1"
```

> [!NOTE]
> Sla de URL's van de externe Git-instanties voor de front-en-app en de back-end-app op. Deze worden in de uitvoer van `az webapp create` weergegeven.
>

### <a name="push-to-azure-from-git"></a>Pushen naar Azure vanaf Git

Voer in het _lokale terminalvenster_ de volgende Git-opdrachten uit om de implementatie in de back-end-app uit te voeren. Vervang _ \<deploymentLocalGitUrl-of-back-end-app>_ door de URL van het externe Git-netwerk dat u hebt opgeslagen in [Azure-resources maken](#create-azure-resources). Wanneer u door git Credential Manager om referenties wordt gevraagd, voert u de referenties voor [uw implementatie](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)in, niet de referenties die u gebruikt om u aan te melden bij de Azure Portal.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

Voer in het lokale terminalvenster de volgende Git-opdrachten uit om dezelfde code in de front-end-app te implementeren. Vervang _ \<deploymentLocalGitUrl-of-front-end-app>_ door de URL van het externe Git-netwerk dat u hebt opgeslagen in [Azure-resources maken](#create-azure-resources).

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>Naar de apps bladeren

Ga in een browser naar de volgende URL's om de twee apps in werking te zien.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

![ASP.NET Core-API uitvoeren in Azure App Service](./media/tutorial-auth-aad/azure-run.png)

> [!NOTE]
> Als de app opnieuw wordt gestart, hebt u wellicht gezien dat nieuwe gegevens zijn gewist. Dit gedrag is inherent aan het ontwerp omdat de ASP.NET Core-voorbeeld-app gebruikmaakt van een database in het geheugen.
>
>

## <a name="call-back-end-api-from-front-end"></a>Back-end-API aanroepen vanuit front-end

In deze stap legt u voor de servercode van de front-end-app vast dat toegang tot de back-end-API mogelijk is. Later schakelt u geverifieerde toegang in vanaf de front-end tot de back-end.

### <a name="modify-front-end-code"></a>Front-end-code wijzigen

Open _Controllers/TodoController.cs_ in de lokale opslagplaats. Voeg aan het begin van `TodoController` de klasse de volgende regels toe en vervang _ \<de back-end-app-name>_ door de naam van uw back-end-app:

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

Zoek de methode die is gedecoreerd `[HttpGet]` en vervang de code binnen de accolades door:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

In de eerste regel wordt een `GET /api/Todo`-aanroep naar de back-end-API-app uitgevoerd.

Zoek vervolgens de methode die is gedecoreerd `[HttpGet("{id}")]` en vervang de code binnen de accolades door:

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
return Content(data, "application/json");
```

In de eerste regel wordt een `GET /api/Todo/{id}`-aanroep naar de back-end-API-app uitgevoerd.

Zoek vervolgens de methode die is gedecoreerd `[HttpPost]` en vervang de code binnen de accolades door:

```cs
var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
var data = await response.Content.ReadAsStringAsync();
return Content(data, "application/json");
```

In de eerste regel wordt een `POST /api/Todo`-aanroep naar de back-end-API-app uitgevoerd.

Zoek vervolgens de methode die is gedecoreerd `[HttpPut("{id}")]` en vervang de code binnen de accolades door:

```cs
var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
return new NoContentResult();
```

In de eerste regel wordt een `PUT /api/Todo/{id}`-aanroep naar de back-end-API-app uitgevoerd.

Zoek vervolgens de methode die is gedecoreerd `[HttpDelete("{id}")]` en vervang de code binnen de accolades door:

```cs
var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
return new NoContentResult();
```

In de eerste regel wordt een `DELETE /api/Todo/{id}`-aanroep naar de back-end-API-app uitgevoerd.

Sla al uw wijzigingen op. Implementeer via het lokale terminalvenster de wijzigingen aan de front-end-app met de volgende Git-opdrachten:

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>Wijzigingen controleren

Ga naar `http://<front-end-app-name>.azurewebsites.net` en voeg enkele items toe, bijvoorbeeld `from front end 1` en `from front end 2`.

Ga naar `http://<back-end-app-name>.azurewebsites.net` om de items te zien die aan de front-end-app zijn toegevoegd. Voeg nu ook een paar items toe, zoals `from back end 1` en `from back end 2`, en vernieuw de front-end-app om de doorgevoerde wijzigingen te zien.

![ASP.NET Core-API uitvoeren in Azure App Service](./media/tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>Verificatie configureren

In deze stap schakelt u verificatie en autorisatie voor de twee apps in. U configureert ook de front-end-app om een toegangstoken te genereren dat u kunt gebruiken om geverifieerde aanroepen uit te voeren naar de back-end-app.

U gebruikt Azure Active Directory als id-provider. Zie [Verificatie van Azure Active Directory configureren voor de App Services-toepassing](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) voor meer informatie.

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>Verificatie en autorisatie voor de back-end-app inschakelen

Selecteer **resource groepen** in het menu [Azure Portal](https://portal.azure.com) of zoek naar een wille keurige pagina en selecteer *resource groepen* .

Zoek en selecteer de resource groep in **resource groepen**. Selecteer in **overzicht**de beheer pagina van uw back-end-app.

![ASP.NET Core-API uitvoeren in Azure App Service](./media/tutorial-auth-aad/portal-navigate-back-end.png)

Selecteer in het menu links van de back-end-app **verificatie/autorisatie**en schakel app service verificatie in door op **aan**te klikken.

Selecteer **Te ondernemen actie wanneer de aanvraag niet is geverifieerd** de optie **Aanmelden met Azure Active Directory**.

Selecteer **Azure Active Directory**onder **verificatie providers**.

![ASP.NET Core-API uitvoeren in Azure App Service](./media/tutorial-auth-aad/configure-auth-back-end.png)

Selecteer **Express**, accepteer de standaard instellingen om een nieuwe AD-app te maken en selecteer **OK**.

Selecteer op de pagina **verificatie/autorisatie** de optie **Opslaan**. 

Nadat u de melding met het bericht hebt `Successfully saved the Auth Settings for <back-end-app-name> App`weer gegeven, kunt u de portal-pagina vernieuwen.

Selecteer **Azure Active Directory** opnieuw en selecteer vervolgens de **Azure AD-App**.

Kopieer de **client-id** van de Azure AD-toepassing naar een Klad blok. U hebt deze waarde later nog nodig.

![ASP.NET Core-API uitvoeren in Azure App Service](./media/tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>Verificatie en autorisatie voor de front-end-app inschakelen

Volg dezelfde stappen voor de front-end-app, maar sla de laatste stap over. U hebt de client-ID niet nodig voor de front-end-app.

Ga, als u wilt, naar `http://<front-end-app-name>.azurewebsites.net`. U wordt nu doorgestuurd naar een beveiligde aanmeldingspagina. Nadat u zich hebt aangemeld, *hebt u nog steeds geen toegang tot de gegevens van de back-end-app*, omdat de back-end-app nu Azure Active Directory aanmelding vereist van de front-end-app. U moet drie dingen doen:

- De front-end toegang geven tot de back-end
- App Service configureren zodat een bruikbaar token wordt geretourneerd
- De token in de code gebruiken

> [!TIP]
> Als u tegen fouten aanloopt en de instellingen voor verificatie en autorisatie opnieuw configureert, kunnen de tokens in het tokenarchief mogelijk niet met de nieuwe instellingen opnieuw worden gegenereerd. Als u er zeker van wilt zijn dat de tokens opnieuw worden gegenereerd, dient u zich af te melden en vervolgens opnieuw aan te melden. U kunt dit doen door de browser in de privémodus te zetten en de browser vervolgens te sluiten en opnieuw in de privémodus te openen nadat u de instellingen in de apps hebt gewijzigd.

### <a name="grant-front-end-app-access-to-back-end"></a>Front-end-app toegang verlenen tot de back-end-app

Nu u voor beide apps verificatie en autorisatie hebt ingeschakeld, worden beide door een AD-toepassing ondersteund. In deze stap geeft u de front-end-app namens de gebruiker machtigingen voor toegang tot de back-end-app. (Technisch gesproken geeft u de _AD-toepassing_ van de front-end-app namens de gebruiker machtigingen voor toegang tot de _AD-toepassing_ van de back-end-app.)

Selecteer in het menu [Azure Portal](https://portal.azure.com) **Azure Active Directory** of zoek naar *Azure Active Directory* op een wille keurige pagina en selecteer deze.

Selecteer **app-registraties** > in eigendom zijnde**toepassingen** > **alle toepassingen in deze map weer geven**. Selecteer de naam van de front-end-app en selecteer vervolgens **API-machtigingen**.

![ASP.NET Core-API uitvoeren in Azure App Service](./media/tutorial-auth-aad/add-api-access-front-end.png)

Selecteer **een machtiging toevoegen**en selecteer vervolgens **api's mijn organisatie maakt gebruik** > **\<van back-end-app-name>**.

Selecteer in de pagina **API-machtigingen voor aanvragen** voor de back-end-app **gedelegeerde machtigingen** en **User_impersonation**en selecteer vervolgens **machtigingen toevoegen**.

![ASP.NET Core-API uitvoeren in Azure App Service](./media/tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>App Service configureren zodat een bruikbaar toegangstoken wordt geretourneerd

De front-end-app heeft nu de vereiste machtigingen voor toegang tot de back-end-app als de aangemelde gebruiker. In deze stap configureert u App Service-verificatie en -autorisatie zodat u een bruikbaar toegangstoken hebt voor toegang tot de back-end. Voor deze stap hebt u de client-ID van de back-end nodig, die u hebt gekopieerd [voor het inschakelen van verificatie en autorisatie voor de back-end-app](#enable-authentication-and-authorization-for-back-end-app).

Selecteer in het menu aan de front-end-app **resource Verkenner** onder **ontwikkelingsprogram ma's**en selecteer vervolgens **Go**.

De [Azure resource Explorer](https://resources.azure.com) wordt nu geopend met de front-end-app die u hebt geselecteerd in de resource structuur. Klik boven aan de pagina op **Lezen/schrijven** om de Azure-resources te kunnen bewerken.

![ASP.NET Core-API uitvoeren in Azure App Service](./media/tutorial-auth-aad/resources-enable-write.png)

Zoom in de linkernavigatiebalk naar **config** > **authsettings**.

Klik in de weergave **authsettings** op **Bewerken**. Stel `additionalLoginParams` in op de volgende JSON-teken reeks met behulp van de client-id die u hebt gekopieerd. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

![ASP.NET Core-API uitvoeren in Azure App Service](./media/tutorial-auth-aad/additional-login-params-front-end.png)

Sla de instellingen op door op **PUT** te klikken.

Uw apps zijn nu geconfigureerd. De front-end is nu gereed voor toegang tot de back-end met het juiste toegangstoken.

Zie [tokens van ID-provider vernieuwen](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#refresh-identity-provider-tokens)voor meer informatie over het configureren van het toegangs token voor andere providers.

## <a name="call-api-securely-from-server-code"></a>API veilig aanroepen vanuit servercode

In deze stap activeert u de eerder gewijzigde servercode, zodat u geverifieerde aanroepen naar de back-end-API kunt uitvoeren.

Uw front-end-app heeft nu de vereiste machtiging en de client-ID van de back-end wordt toegevoegd aan de aanmeldings parameters. Op die manier kan een toegangstoken voor verificatie van de back-end-app verkregen worden. App Service stelt dit token aan de servercode beschikbaar door in elke geverifieerde aanvraag een `X-MS-TOKEN-AAD-ACCESS-TOKEN`-header te injecteren (zie [Tokens ophalen in app-code](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code)).

> [!NOTE]
> Deze headers worden voor alle ondersteunde talen geïnjecteerd. U kunt ze openen met het standaardpatroon voor elke betreffende taal.

Open opnieuw _Controllers/TodoController.cs_ in de lokale opslagplaats. Voeg onder de `TodoController(TodoContext context)`-constructor de volgende code toe:

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

Met deze code wordt de standaard-HTTP-header `Authorization: Bearer <access-token>` aan alle externe API-aanroepen toegevoegd. In de ASP.NET Core MVC- `OnActionExecuting` aanvraag pijp lijn wordt uitgevoerd net vóór de betreffende actie, dus elk van uw uitgaande API-oproep geeft nu het toegangs token.

Sla al uw wijzigingen op. Implementeer via het lokale terminalvenster de wijzigingen aan de front-end-app met de volgende Git-opdrachten:

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

Meld u opnieuw aan bij `https://<front-end-app-name>.azurewebsites.net`. Klik op de pagina over de gebruiksovereenkomst voor gebruikersgegevens op **Accepteren**.

U moet nu net als eerder gegevens uit de back-end-app kunnen maken, lezen, bijwerken en verwijderen. Het enige verschil is dat beide apps nu worden beveiligd door App Service-verificatie en -autorisatie, waaronder de aanroepen tussen services.

Gefeliciteerd! De servercode heeft nu toegang tot de gegevens van de back-end namens de geverifieerde gebruiker.

## <a name="call-api-securely-from-browser-code"></a>API veilig vanuit browsercode aanroepen

In deze stap legt u vast dat de Angular.js-app van de front-end naar de back-end-API verwijst. Op die manier leert u hoe u het toegangstoken kunt ophalen en er API-aanroepen naar de back-end-app mee kunt uitvoeren.

Terwijl de servercode toegang heeft tot de aanvraag-headers, heeft de clientcode toegang tot `GET /.auth/me` om dezelfde toegangstokens te kunnen ophalen (zie [Tokens ophalen in app-code](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#retrieve-tokens-in-app-code)).

> [!TIP]
> In deze sectie worden de standaard-HTTP-methoden gebruikt om de veilige HTTP-aanroepen te demonstreren. U kunt echter de [micro soft-verificatie bibliotheek voor Java script](https://github.com/AzureAD/microsoft-authentication-library-for-js) gebruiken om het hoek. js-toepassings patroon te vereenvoudigen.
>

### <a name="configure-cors"></a>CORS configureren

In de Cloud Shell schakelt u CORS in voor de URL van uw client met [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) behulp van de opdracht. Vervang de tijdelijke aanduidingen voor de _ \<back-end-app>_ en _ \<de front-end-app-naam>_ .

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

Deze stap heeft geen betrekking op verificatie en autorisatie. U hebt deze echter nodig zodat de browser API-aanroepen tussen domeinen vanaf de Angular.js-app toestaat. Zie [CORS-functionaliteit toevoegen](../app-service-web-tutorial-rest-api.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#add-cors-functionality) voor meer informatie.

### <a name="point-angularjs-app-to-back-end-api"></a>Angular.js-app naar back-end-API laten verwijzen

Open _wwwroot/index.html_ in de lokale opslagplaats.

Stel in regel 51 de `apiEndpoint` variabele in op de HTTPS-URL van uw back-end-`https://<back-end-app-name>.azurewebsites.net`app (). Vervang de naam van de _ \<back-end-app>_ door de naam van uw app in app service.

Open _wwwroot/app/scripts/todoListSvc.js_ in de lokale opslagplaats en controleer of alle API-aanroepen door `apiEndpoint` vooraf worden gegaan. De Angular.js-app roept nu de back-end-API's aan. 

### <a name="add-access-token-to-api-calls"></a>Toegangstoken toevoegen aan API-aanroepen

Voeg in _wwwroot/app/scripts/todoListSvc.js_, boven de lijst met API-aanroepen (boven de regel `getItems : function(){`), de volgende functie aan de lijst toe:

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

Deze functie wordt aangeroepen om de standaard-`Authorization`-header in te stellen met het toegangstoken. De functie wordt in de volgende stap aangeroepen.

Open _wwwroot/app/scripts/app.js_ in de lokale opslagplaats en zoek de volgende code:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

Vervang het hele codeblok door de volgende code:

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

Door deze wijziging wordt de toewijzing `resolve` toegevoegd, waarmee `/.auth/me` wordt aangeroepen en het toegangstoken ingesteld. Hiermee wordt gegarandeerd dat u over het toegangstoken beschikt voordat de controller `todoListCtrl` wordt geïnstantieerd. Op die manier bevatten alle API-aanroepen van de controller het token.

### <a name="deploy-updates-and-test"></a>Updates implementeren en tests uitvoeren

Sla al uw wijzigingen op. Implementeer via het lokale terminalvenster de wijzigingen aan de front-end-app met de volgende Git-opdrachten:

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

Ga opnieuw naar `https://<front-end-app-name>.azurewebsites.net`. U moet nu rechtstreeks in de Angular.js-app gegevens in de back-end kunnen maken, lezen, bijwerken en verwijderen.

Gefeliciteerd! De clientcode heeft nu toegang tot de gegevens van de back-end namens de geverifieerde gebruiker.

## <a name="when-access-tokens-expire"></a>Wanneer de toegangstokens verlopen

Uw toegangstoken verloopt na bepaalde tijd. Voor meer informatie over het vernieuwen van uw toegangstokens zonder dat gebruikers zich opnieuw moeten verifiëren bij uw app raadpleegt u [Toegangstokens van id-providers vernieuwen](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#refresh-identity-provider-tokens).

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een resourcegroep gemaakt. Als u deze resources niet meer nodig denkt te hebben, verwijdert u de resourcegroep door de volgende opdracht in Cloud Shell uit te voeren:

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

Het kan een minuut duren voordat deze opdracht is uitgevoerd.

<a name="next"></a>
## <a name="next-steps"></a>Volgende stappen

Wat u hebt geleerd:

> [!div class="checklist"]
> * Ingebouwde verificatie en autorisatie inschakelen
> * Apps beveiligen tegen niet-geverifieerde aanvragen
> * Azure Active Directory gebruiken als id-provider
> * Externe apps openen namens de aangemelde gebruiker
> * Aanroepen tussen services beveiligen met tokenverificatie
> * Toegangstokens gebruiken vanuit servercode
> * Toegangstokens gebruiken vanuit clientcode (browser)

Ga door naar de volgende zelfstudie om te leren hoe u een aangepaste DNS-naam aan uw app kunt toewijzen.

> [!div class="nextstepaction"]
> [Een bestaande aangepaste DNS-naam toewijzen aan Azure App Service](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
