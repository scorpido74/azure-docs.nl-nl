---
title: Een HTTP-eind punt in Azure Functions aanpassen
description: Meer informatie over het aanpassen van een HTTP trigger-eind punt in Azure Functions
author: mattchenderson
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.openlocfilehash: f7729f5acb8b7b95004265f6802ba2feb1bc3cd7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096762"
---
# <a name="customize-an-http-endpoint-in-azure-functions"></a>Een HTTP-eind punt in Azure Functions aanpassen

In dit artikel leert u hoe u met Azure Functions uiterst schaal bare Api's kunt bouwen. Azure Functions wordt geleverd met een aantal ingebouwde HTTP-triggers en -bindingen waarmee u gemakkelijk een eindpunt in een groot aantal talen kunt maken, waaronder Node.JS, C# en meer. In dit artikel gaat u een HTTP-trigger aanpassen voor het verwerken van specifieke acties in het API-ontwerp. Ook treft u voorbereidingen voor het uitbreiden van uw API door deze te integreren met Azure Functions-proxy's en mock-API's in te stellen. Dit doet u allemaal in de serverloze Functions-rekenomgeving, zodat u zich geen zorgen hoeft te maken over het schalen van resources: u hoeft zich alleen te richten op uw API-logica.

## <a name="prerequisites"></a>Vereisten 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

De resulterende functie wordt gebruikt voor de rest van dit artikel.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Open Azure Portal. Meld u hiervoor met uw Azure-account aan bij [https://portal.azure.com](https://portal.azure.com).

## <a name="customize-your-http-function"></a>Uw HTTP-functie aanpassen

Standaard is uw functie die wordt geactiveerd via HTTP geconfigureerd voor het accepteren van elke HTTP-methode. Er is ook een standaard-URL van het formulier `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>`. Als u de snelstart hebt gevolgd, ziet `<funcname>` er waarschijnlijk ongeveer uit als 'HttpTriggerJS1'. In deze sectie wijzigt u de functie om in plaats daarvan alleen te reageren op GET-aanvragen voor de route `/api/hello`. 

1. Navigeer in de Azure-portal naar uw functie. Selecteer **Integreren** in de linkernavigatiebalk.

    ![Een HTTP-functie aanpassen](./media/functions-create-serverless-api/customizing-http.png)

1. Gebruik de HTTP-triggerinstellingen zoals die zijn opgegeven in de tabel.

    | Veld | Voorbeeldwaarde | Description |
    |---|---|---|
    | Toegestane HTTP-methoden | Geselecteerde methoden | Hiermee wordt bepaald welke HTTP-methoden kunnen worden gebruikt om deze functie aan te roepen |
    | Geselecteerde HTTP-methoden | GET | Hiermee is het alleen geselecteerde HTTP-methoden toegestaan om deze functie aan te roepen |
    | Routesjabloon | /hello | Hiermee wordt bepaald welke route wordt gebruikt om deze functie aan te roepen |
    | Autorisatieniveau | Anoniem | Optioneel: Optioneel: hierdoor is uw functie toegankelijk zonder een API-sleutel |

    > [!NOTE] 
    > U hebt het basispadvoorvoegsel `/api` niet in de routesjabloon opgenomen, aangezien dit wordt verwerkt door een algemene instelling.

1. Klik op **Opslaan**.

Meer informatie over het aanpassen van HTTP-functies vindt u in [HTTP-bindingen in Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook).

### <a name="test-your-api"></a>Uw API testen

Test vervolgens uw functie om te zien hoe deze werkt met het nieuwe API-gebied.
1. Ga terug naar de ontwikkelpagina door in het linkernavigatievenster op de naam van de functie te klikken.
1. Klik op **Functie-URL ophalen** en kopieer de URL. U ziet dat deze nu gebruikmaakt van de route `/api/hello`.
1. Kopieer de URL naar een nieuw browsertabblad of de REST-client van uw voorkeur. Browsers gebruiken standaard GET.
1. Voeg parameters toe aan de gebruikte queryreeks in uw URL, bijvoorbeeld `/api/hello/?name=John`
1. Druk op Enter om te controleren of alles werkt. U ziet het antwoord "*Hello John*"
1. U kunt ook proberen het eindpunt aan te roepen met een andere HTTP-methode om te bevestigen dat de functie niet wordt uitgevoerd. Hiervoor moet u een REST-client, zoals cURL, Postman of Fiddler gebruiken.

## <a name="proxies-overview"></a>Overzicht van Azure Functions-proxy's

In de volgende sectie werkt uw API via een proxy. Met Azure Functions-proxy's kunt u aanvragen doorsturen naar andere resources. U definieert een HTTP-eindpunt, net zoals met de HTTP-trigger, maar in plaats van het schrijven van code die moet worden uitgevoerd als dit eindpunt wordt aangeroepen, geeft u een URL naar een externe implementatie op. Hiermee kunt u meerdere API-bronnen combineren in één API-gebied dat voor clients eenvoudig te gebruiken is. Dit is vooral handig als u uw API als microservices wilt bouwen.

Een proxy kan naar elke HTTP-bron verwijzen, zoals:
- Azure Functions 
- API Apps in [Azure App Service](https://docs.microsoft.com/azure/app-service/overview)
- Docker-containers in [App Service in Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro)
- Elke andere gehoste API

Zie [Werken met Azure Functions-proxy's] voor meer informatie over proxy's.

## <a name="create-your-first-proxy"></a>Uw eerste proxy maken

In deze sectie maakt u een nieuwe proxy die als een front-end voor uw algehele API fungeert. 

### <a name="setting-up-the-frontend-environment"></a>De front-endomgeving instellen

Herhaal de stappen uit [Een functie-app maken](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) om een nieuwe functie-app te maken waarin u uw proxy maakt. De URL van deze nieuwe app gaat fungeren als de front-end voor onze API en de functie-app die u eerder hebt bewerkt gaat als een back-end fungeren.

1. Navigeer naar uw nieuwe front-end functie-app in de portal.
1. Selecteer **Platformfuncties** en kies **Toepassingsinstellingen**.
1. Schuif omlaag naar **Toepassingsinstellingen** waar de sleutel-/waardeparen zijn opgeslagen en maak een nieuwe instelling met de sleutel 'HELLO_HOST'. Stel de waarde ervan in op de host van uw back-end functie-app, zoals `<YourBackendApp>.azurewebsites.net`. Deze maakt deel uit van de URL die u eerder hebt gekopieerd tijdens het testen van uw HTTP-functie. Later in de configuratie verwijst u naar deze instelling.

    > [!NOTE] 
    > App-instellingen worden aanbevolen voor de configuratie van de host om een in code vastgelegde omgevingsafhankelijkheid voor de proxy te voorkomen. Het gebruik van app-instellingen betekent dat u de proxyconfiguratie tussen omgevingen kunt verplaatsen en de omgevingsspecifieke app-instellingen worden toegepast.

1. Klik op **Opslaan**.

### <a name="creating-a-proxy-on-the-frontend"></a>Een proxy maken op de front-end

1. Navigeer terug naar uw front-end functie-app in de portal.
1. Klik in de navigatiebalk links op het plusteken '+' naast 'Proxy's'.
    ![Een proxy maken](./media/functions-create-serverless-api/creating-proxy.png)
1. Gebruik proxyinstellingen zoals aangegeven in de tabel. 

    | Veld | Voorbeeldwaarde | Description |
    |---|---|---|
    | Name | HelloProxy | Een beschrijvende naam die alleen wordt gebruikt voor beheer |
    | Routesjabloon | /api/remotehello | Hiermee wordt bepaald welke route wordt gebruikt om deze proxy aan te roepen |
    | URL van back-end | https://%HELLO_HOST%/api/hello | Geeft het eindpunt aan waarnaar de aanvraag moet worden geproxied |
    
1. Houd er rekening mee dat Azure Functions-proxy's niet het basispadvoorvoegsel `/api` verstrekken en dat dit moet worden opgenomen in de routesjabloon.
1. De syntaxis van `%HELLO_HOST%` verwijst naar de app-instelling die u eerder hebt gemaakt. De omgezette URL verwijst naar uw oorspronkelijke functie.
1. Klik op **Create**.
1. U kunt uw nieuwe proxy uitproberen door de proxy-URL kopiëren en deze te testen in de browser of met de HTTP-client van uw voorkeur.
    1. Gebruik voor een anonieme functie:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?name="Proxies"`
    1. Gebruik voor een functie met autorisatie:
        1. `https://YOURPROXYAPP.azurewebsites.net/api/remotehello?code=YOURCODE&name="Proxies"`

## <a name="create-a-mock-api"></a>Een mock-API maken

U gaat nu een proxy gebruiken om een mock-API te maken voor uw oplossing. Hiermee kan de clientontwikkeling worden voortgezet, zonder dat de back-end volledig hoeft te zijn geïmplementeerd. Later in het ontwikkelproces kunt u een nieuwe functie-app maken die ondersteuning biedt voor deze logica en uw proxy naar deze app omleiden.

Voor het maken van deze mock-API, maken we een nieuwe proxy, deze keer met de [App Service-editor](https://github.com/projectkudu/kudu/wiki/App-Service-Editor). Navigeer eerst naar uw nieuwe functie-app in de portal. Selecteer **Platformfuncties** en klik onder **Ontwikkelhulpprogramma's** op **App Service-editor**. Als u hierop klikt, wordt de App Service-editor op een nieuw tabblad geopend.

Selecteer `proxies.json` in de linkernavigatiebalk. Dit is het bestand waarin de configuratie voor al uw proxy's wordt opgeslagen. Als u een van de [Functions-implementatiemethoden](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) gebruikt, is dit het bestand dat u beheert in broncodebeheer. Zie [Geavanceerde configuratie van proxy's](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration) voor meer informatie over dit bestand.

Als u alle stappen hebt gevolgd, ziet uw proxies.json er als volgt uit:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

U gaat nu uw mock-API toevoegen. Vervang het proxies.json-bestand door het volgende:

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/remotehello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

Hiermee wordt een nieuwe proxy 'GetUserByName' toegevoegd, zonder de eigenschap backendUri. In plaats van een andere resource aan te roepen, wordt de standaardreactie van Azure Functions-proxy's gewijzigd via het negeren van een antwoord. Het negeren van aanvragen en antwoorden kan ook worden gebruikt in combinatie met een back-end-URL. Dit is met name handig wanneer u een proxy uitvoert met een ouder systeem, waar u mogelijk headers, queryparameters, enzovoort moet wijzigen. Zie [Aanvragen en antwoorden negeren in proxy's](https://docs.microsoft.com/azure/azure-functions/functions-proxies) voor meer informatie over het negeren van aanvragen en antwoorden.

Test uw mock-API door het `<YourProxyApp>.azurewebsites.net/api/users/{username}`-eindpunt aan te roepen met behulp van een browser of de REST-client van uw voorkeur. Vervang _{username}_ door een tekenreekswaarde die een gebruikersnaam vertegenwoordigt.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een API kunt bouwen en aanpassen op Azure Functions. U hebt ook geleerd hoe u meerdere API's, inclusief mocks, kunt samenbrengen als een geïntegreerd API-gebied. U kunt deze technieken gebruiken om meer en minder complexe API's te ontwikkelen, die alle worden uitgevoerd op het serverloze rekenmodel dat wordt geleverd door Azure Functions.

De volgende informatiebronnen kunnen nuttig zijn als u uw API verder ontwikkelt:

- [HTTP-bindingen in Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [Werken met Azure Functions-proxy's]
- [Een Azure Functions-API documenteren (preview)](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[Werken met Azure Functions-proxy's]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
