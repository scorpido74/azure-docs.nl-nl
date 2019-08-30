---
title: Azure Functions in python maken en implementeren met Visual Studio code
description: De Visual Studio code extension for Azure Functions gebruiken om serverloze functies te maken in Python en deze te implementeren in Azure.
services: functions
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: glenga
ms.openlocfilehash: 4f5c10536992f51ac61815507a3869e521520299
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70170715"
---
# <a name="deploy-python-to-azure-functions-with-visual-studio-code"></a>Python implementeren voor Azure Functions met Visual Studio code

In deze zelf studie gebruikt u Visual Studio code en de uitbrei ding Azure Functions voor het maken van een serverloze HTTP-eind punt met python en om ook een verbinding (of ' binding ') toe te voegen aan de opslag. Azure Functions voert uw code in een serverloze omgeving uit zonder een virtuele machine in te richten of een web-app te publiceren. Met de extensie Azure Functions voor Visual Studio code wordt het proces van het gebruik van functies aanzienlijk vereenvoudigd door automatisch veel configuratie problemen te verwerken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De Azure Functions-extensie installeren
> * Een door HTTP geactiveerde functie maken
> * Lokaal fouten opsporen
> * Toepassings instellingen synchroniseren
> * Streaming-logboeken weer geven
> * Verbinding maken met Azure Storage

Als u problemen ondervindt met een van de stappen in deze zelf studie, horen we graag over de details. Gebruik de knop voor **een probleem** aan het einde van elke sectie om gedetailleerde feedback in te dienen.

## <a name="prerequisites"></a>Vereisten

- Een [Azure-abonnement](#azure-subscription).
- [Visual Studio code met de](#visual-studio-code-python-and-the-azure-functions-extension) uitbrei ding Azure functions.
- Het [Azure functions core tools](#azure-functions-core-tools).

### <a name="azure-subscription"></a>Azure-abonnement

Als u geen Azure-abonnement hebt, [meldt u zich nu](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension) aan voor een gratis account van 30 dagen met $200 in azure-tegoed om een wille keurige combi natie van services uit te proberen.

### <a name="visual-studio-code-python-and-the-azure-functions-extension"></a>Visual Studio code, python en de uitbrei ding Azure Functions

Installeer de volgende software:

- Python 3.6. x zoals vereist door Azure Functions. [Python 3.6.8](https://www.python.org/downloads/release/python-368/) is de meest recente versie van 3.6. x.
- [Visual Studio Code](https://code.visualstudio.com/).
- De [python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python) zoals beschreven in [Visual Studio code python-zelf studie-vereisten](https://code.visualstudio.com/docs/python/python-tutorial).
- De [uitbrei ding Azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). Voor algemene informatie gaat u naar de [vscode-Azurefunctions github-opslag plaats](https://github.com/Microsoft/vscode-azurefunctions).

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Volg de instructies voor uw besturings systeem op het [werk met Azure functions core tools](functions-run-local.md#v2). De hulpprogram ma's zelf zijn geschreven in .NET core en het pakket met kern Hulpprogramma's wordt het beste geïnstalleerd met behulp van het node. js-pakket beheer, NPM. Daarom moet u ook .NET core en node. js installeren, zelfs voor python-code. U kunt echter wel de .NET core-vereiste gebruiken met behulp van ' uitbreidings bundels ', zoals beschreven in de bovengenoemde documentatie. Ongeacht het geval hoeft u deze onderdelen slechts eenmaal te installeren, waarna Visual Studio code automatisch vraagt om updates te installeren.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Zodra de functions-extensie is geïnstalleerd, meldt u zich aan bij uw Azure **-account door te navigeren naar Azure: Functie** Verkenner, selecteer **Aanmelden bij Azure**en volg de aanwijzingen.

![Meld u aan bij Azure via Visual Studio code](media/tutorial-vs-code-serverless-python/azure-sign-in.png)

Nadat u zich hebt aangemeld, controleert u of het e-mail account van uw Azure-abonnement wordt weer gegeven in de status balk:

![Visual Studio code-status balk met Azure-account](media/tutorial-vs-code-serverless-python/azure-account-status-bar.png)

De naam die u aan uw abonnement hebt toegewezen, wordt ook **weer gegeven in Azure: Functions** Explorer (' primair ' in de onderstaande afbeelding):

![Visual Studio code Azure App Service Explorer met abonnementen](media/tutorial-vs-code-serverless-python/azure-subscription-view.png)

> [!NOTE]
> Als u de fout melding **' kan abonnement met naam [abonnements-id] ' niet vinden**, kan dit zijn omdat u zich achter een proxy bevindt en de Azure API niet kunt bereiken. Configureer `HTTP_PROXY` en`HTTPS_PROXY` omgevings variabelen met uw proxy gegevens in uw terminal:
>
> ```bash
> # macOS/Linux
> export HTTPS_PROXY=https://username:password@proxy:8080
> export HTTP_PROXY=http://username:password@proxy:8080
> ```
>
> ```ps
> # Windows
> set HTTPS_PROXY=https://username:password@proxy:8080
> set HTTP_PROXY=http://username:password@proxy:8080
> ```

### <a name="verify-prerequisites"></a>Vereisten verifiëren

Als u wilt controleren of alle Azure functions-hulpprogram ma's zijn geïnstalleerd, opent u het Visual Studio code-opdracht palet ( **F1), selecteert u de terminal: Maak een nieuwe geïntegreerde** Terminal opdracht en voer de opdracht `func`uit zodra de terminal wordt geopend:

![Vereisten voor Azure Functions kern hulpprogramma's controleren](media/tutorial-vs-code-serverless-python/check-prereqs.png)

De uitvoer die begint met het Azure Functions logo (u moet de uitvoer naar boven schuiven) geeft aan dat de Azure Functions Core Tools aanwezig zijn.

Als de `func` opdracht niet wordt herkend, controleert u of de map waar u de Azure functions core tools hebt geïnstalleerd, is opgenomen in de omgevings variabele PATH.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=01-verify-prerequisites)

## <a name="create-the-function"></a>De functie maken

1. Code voor Azure Functions wordt beheerd binnen een functions- _project_, dat u eerst maakt voordat u de code maakt. In **Azure: In** de functie Verkenner (geopend met het pictogram Azure aan de linkerkant), selecteert u het **nieuwe project** -opdracht pictogram, of opent u het opdracht palet ( **F1) en selecteert u Azure functions: Nieuw project**maken.

    ![De knop Nieuw project maken in de functie Verkenner](media/tutorial-vs-code-serverless-python/project-create-new.png)

1. In de onderstaande vragen:

    | Vraag | Value | Description | 
    | --- | --- | --- |
    | Geef een map op voor het project | Huidige geopende map | De map waarin het project moet worden gemaakt. Mogelijk wilt u het project in een submap maken. |
    | Selecteer een taal voor uw functie-app-project | **Python** | De taal die moet worden gebruikt voor de functie. deze bepaalt de sjabloon die wordt gebruikt voor de code. |
    | Selecteer een sjabloon voor de eerste functie van uw project | **HTTP-trigger** | Een functie die gebruikmaakt van een HTTP-trigger wordt uitgevoerd wanneer er een HTTP-aanvraag wordt gedaan aan het eind punt van de functie. (Er zijn diverse andere triggers voor Azure Functions. Zie [Wat kan ik doen met functions?](functions-overview.md#what-can-i-do-with-functions)voor meer informatie.) |
    | Een functie naam opgeven | HttpExample | De naam wordt gebruikt voor een submap die de code van de functie bevat samen met de configuratie gegevens, en definieert ook de naam van het HTTP-eind punt. Gebruik "HttpExample" in plaats van de standaard HTTP trigger te accepteren om de functie zelf te onderscheiden van de trigger. |
    | Verificatieniveau | **Functieassembly** | Aanroepen van het functie-eind punt vereisen een [functie toets](functions-bindings-http-webhook.md#authorization-keys). |
    | Selecteer hoe u uw project wilt openen | **In het huidige venster openen** | Hiermee opent u het project in het huidige venster van Visual Studio code. |

1. Na korte tijd wordt een bericht aangegeven dat het nieuwe project is gemaakt. In de **Explorer**wordt de submap gemaakt voor de functie. 

1. Als deze nog niet is geopend, opent u  *\_het\_ \_bestand\_init. py* dat de standaard functie code bevat:

    [![Resultaat van het maken van een nieuw python-functie project](media/tutorial-vs-code-serverless-python/project-create-results.png)](media/tutorial-vs-code-serverless-python/project-create-results.png)

    > [!NOTE]
    > Wanneer Visual Studio code aangeeft dat er geen Python-interpreter is geselecteerd wanneer u  *\_\_init\_. py \_* opent, opent u het opdracht palet (F1), selecteert u **de python: Selecteer de** opdracht interpreter en selecteer vervolgens de virtuele omgeving in de lokale `.env` map (die is gemaakt als onderdeel van het project). De omgeving moet zijn gebaseerd op python 3.6 x, zoals eerder is vermeld onder [vereisten](#prerequisites).
    >
    > ![De virtuele omgeving selecteren die is gemaakt met het project](media/tutorial-vs-code-serverless-python/select-venv-interpreter.png)

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=02-create-function)

## <a name="examine-the-code-files"></a>De code bestanden controleren

In de zojuist gemaakte submap van de functie _HttpExample_ zijn drie bestanden  *\_:\_ \_\_init. py* bevat de code van de functie, *Function. json* beschrijft de functie in azure Functions en *sample. dat* is een voorbeeld gegevensbestand. U kunt *voorbeeld. dat* desgewenst verwijderen, omdat deze alleen bestaat om aan te geven dat u andere bestanden kunt toevoegen aan de submap.

We bekijken eerst de *functie. json* , vervolgens de code in  *\_ \_init\_\_. py*.

### <a name="functionjson"></a>function.json

Het bestand function. json bevat de benodigde configuratie-informatie voor het Azure Functions-eind punt:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

De `scriptFile` eigenschap geeft het opstart bestand voor de code aan en die code moet een python-functie met `main`de naam bevatten. U kunt de code in meerdere bestanden indelen, zolang het bestand dat hier wordt opgegeven `main` , een functie bevat.

Het `bindings` element bevat twee objecten: een om binnenkomende aanvragen te beschrijven en de andere om het HTTP-antwoord te beschrijven. Voor binnenkomende aanvragen (`"direction": "in"`) reageert de functie op http Get-of post-aanvragen en vereist dat u de functie sleutel opgeeft. De reactie (`"direction": "out"`) is een HTTP-antwoord dat elke waarde retourneert die wordt geretourneerd `main` door de python-functie.

### <a name="__initpy__"></a>\_\_init.py\_\_

Wanneer u een nieuwe functie maakt, bevat Azure functions standaard Python-code  *\_in\_ \_\_init. py*:

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

De belangrijkste onderdelen van de code zijn als volgt:

- U moet importeren `func` vanuit `azure.functions`; de logboek registratie module importeren is optioneel, maar wordt aanbevolen.
- De vereiste `main` python-functie ontvangt `func.request` een object `req`met de naam en retourneert een waarde `func.HttpResponse`van het type. Meer informatie over de mogelijkheden van deze objecten vindt u in de [func. HttpRequest](/python/api/azure-functions/azure.functions.httprequest?view=azure-python) en [func. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse?view=azure-python) -verwijzingen.
- De hoofd tekst `main` van de aanvraag wordt verwerkt en er wordt een antwoord gegenereerd. In dit geval zoekt de code naar een `name` para meter in de URL. Als er een fout optreedt, wordt gecontroleerd of de aanvraag tekst JSON bevat `func.HttpRequest.get_json`(met) en dat de JSON `name` een waarde bevat ( `get` met behulp van de methode van `get_json`het JSON-object dat wordt geretourneerd door).
- Als er een naam wordt gevonden, retourneert de code de teken reeks "Hallo" met de toegevoegde naam; anders wordt er een fout bericht weer gegeven.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=03-examine-code-files)

## <a name="debug-locally"></a>Lokaal fouten opsporen

1. Wanneer u het functions-project maakt, maakt de Visual Studio code-extensie ook een `.vscode/launch.json` start configuratie met een enkele configuratie met de naam **koppelen aan python-functies**. Met deze configuratie kunt u gewoon op F5 drukken of de debug Explorer gebruiken om het project te starten:

    ![Debug Explorer waarin de functies worden gestart configuratie starten](media/tutorial-vs-code-serverless-python/launch-configuration.png)

1. Wanneer u het fout opsporingsprogramma start, wordt er een terminal geopend met de uitvoer van Azure Functions, met inbegrip van een samen vatting van de beschik bare eind punten. Uw URL kan verschillen als u een andere naam gebruikt dan ' HttpExample ':

    ```output
    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Gebruik **CTRL + klikken** of **cmd + Klik** op de URL in het venster Visual Studio code- **uitvoer** om een browser te openen voor dat adres, of start een browser en plak deze in dezelfde URL. In beide gevallen is `api/<function_name>`het eind punt in dit geval. `api/HttpExample` Omdat deze URL geen para meter name bevat, zou het browser venster gewoon moeten worden weer gegeven: "Geef een naam op in de query reeks of in de hoofd tekst van de aanvraag", afhankelijk van het pad in de code.

1. Probeer nu een para meter name toe te voegen aan het `http://localhost:7071/api/HttpExample?name=VS%20Code`gebruik, zoals, en in het browser venster moet het bericht ' Hello Visual Studio code! ' worden weer gegeven. Dit geeft aan dat u het pad naar de code hebt uitgevoerd.

1. Als u de naam waarde in een JSON-aanvraag tekst wilt door geven, kunt u een hulp programma zoals krul gebruiken met de JSON inline:

    ```bash
    # Mac OS/Linux: modify the URL if you're using a different function name
    curl --header "Content-Type: application/json" --request POST \
        --data {"name":"Visual Studio Code"} http://localhost:7071/api/HttpExample
    ```

    ```ps
    # Windows (escaping on the quotes is necessary; also modify the URL
    # if you're using a different function name)
    curl --header "Content-Type: application/json" --request POST \
        --data {"""name""":"""Visual Studio Code"""} http://localhost:7071/api/HttpExample
    ```

    U kunt ook een bestand maken zoals *Data. json* dat de `{"name":"Visual Studio Code"}` opdracht `curl --header "Content-Type: application/json" --request POST --data @data.json http://localhost:7071/api/HttpExample`bevat en gebruikt.

1. Als u fouten wilt opsporen in de functie, stelt u een `name = req.params.get('name')` onderbrekings punt in op de regel die het lezen en maken van een aanvraag naar de URL opnieuw uitvoert. Het fout opsporingsprogramma voor Visual Studio-code moet op die regel worden gestopt, zodat u variabelen kunt onderzoeken en de code door lopen. (Zie [de zelf studie over Visual Studio code-de debugger configureren en uitvoeren](https://code.visualstudio.com/docs/python/python-tutorial.md#configure-and-run-the-debugger)(Engelstalig) voor een kort overzicht van de basis fout opsporing.)

1. Als u er zeker van bent dat u de functie lokaal hebt getest, stopt u de debugger (met de opdracht **debug** > **Stop debuggen** menu of de opdracht **Disconnect** op de werk balk voor fout opsporing).

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=04-test-debug)

## <a name="deploy-to-azure-functions"></a>Implementeren in Azure Functions

In deze stappen gebruikt u de extensie functions om een functie-app in azure te maken, samen met andere vereiste Azure-resources. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen. Er is ook een Azure Storage account vereist voor gegevens en een [hosting abonnement](functions-scale.md#hosting-plan-support). Al deze resources zijn ingedeeld in één resource groep.

1. **In Azure: In** de functie Verkenner selecteert **u de opdracht implementeren naar functie-app** of opent u het opdracht palet (F1) en **selecteert u de Azure functions: Implementeren naar functie-app** opdracht. De functie-app is waar uw python-project wordt uitgevoerd in Azure.

    ![Implementeren naar functie-app-opdracht](media/tutorial-vs-code-serverless-python/deploy-command.png)

1. Wanneer u hierom wordt gevraagd, selecteert u **nieuwe functie-app maken in azure**en geeft u een naam op die uniek is voor Azure (meestal met uw persoonlijke of bedrijfs naam en met andere unieke id's), kunt u letters, cijfers en afbreek streepjes gebruiken. Als u eerder een functie-app hebt gemaakt, wordt de naam ervan weer gegeven in deze lijst met opties.

1. De uitbrei ding voert de volgende acties uit, die u kunt bekijken in Visual Studio code Popup-berichten en het **uitvoer** venster (het proces duurt een paar minuten):

    - Een resource groep maken met de naam die u hebt opgegeven (afbreek streepjes verwijderen).
    - Maak in die resource groep het opslag account, het hosting plan en de functie-app. Standaard wordt een [verbruiks abonnement](functions-scale.md#consumption-plan) gemaakt. Als u uw functies in een speciaal plan wilt uitvoeren, moet u [publiceren met geavanceerde opties voor maken inschakelen](functions-develop-vs-code.md).
    - Implementeer uw code in de functie-app.

    **Azure: In** functions Explorer wordt ook de voortgang weer gegeven:

    ![Voortgangs indicator voor implementatie in Azure: Functie Verkenner](media/tutorial-vs-code-serverless-python/deploy-progress.png)

1. Zodra de implementatie is voltooid, wordt met de uitbrei ding van de Azure Functions een bericht weer gegeven met knoppen voor drie extra acties:

    ![Bericht dat een geslaagde implementatie met aanvullende acties aangeeft](media/tutorial-vs-code-serverless-python/deployment-popup.png)

    Zie de volgende secties voor **stroom logboeken** en het **uploaden van instellingen**. Zie voor **weer gave-uitvoer**stap 5 hieronder.

1. Na de implementatie toont het **uitvoer** venster ook het open bare eind punt op Azure:

    ```output
    HTTP Trigger Urls:
      HttpExample: https://vscode-azure-functions.azurewebsites.net/api/HttpExample
    ```

    Gebruik dit eind punt om dezelfde tests uit te voeren die u lokaal hebt uitgevoerd, met behulp van URL-para meters en/of aanvragen met JSON-gegevens in de hoofd tekst van de aanvraag. De resultaten van het open bare eind punt moeten overeenkomen met de resultaten van het lokale eind punt dat u eerder hebt getest.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=05-deploy)

### <a name="stream-logs"></a>Logboeken streamen

Ondersteuning voor logboek streaming is momenteel in ontwikkeling, zoals beschreven in [probleem 589](https://github.com/microsoft/vscode-azurefunctions/issues/589) voor de extensie Azure functions. Met de knop **Stream logs** in het pop-upbericht wordt uiteindelijk de logboek uitvoer op Azure verbonden met Visual Studio code. U kunt de logboek stroom ook starten en stoppen in de **Azure functions** Explorer door met de rechter muisknop op het functie project te klikken en **streaming-Logboeken starten** te selecteren of streaming-logboeken te **stoppen**.

Op dit moment zijn deze opdrachten echter nog niet operationeel. Streaming van Logboeken is beschikbaar in een browser door de volgende opdracht uit te `<app_name>` voeren, waarbij u vervangt door de naam van uw functions-app in Azure:

```bash
# Replace <app_name> with the name of your Functions app on Azure
func azure functionapp logstream <app_name> --browser
```

### <a name="sync-local-settings-to-azure"></a>Lokale instellingen synchroniseren met Azure

Met de knop voor het uploaden van de **instellingen** in de pop-upvenster implementatie bericht worden alle wijzigingen toegepast die u hebt aangebracht in het bestand *Local. settings. json* in Azure. U kunt de opdracht ook aanroepen in de **Azure functions** Explorer door het project knooppunt functies uit te vouwen, met de rechter muisknop op **Toepassings instellingen**te klikken en **lokale instellingen uploaden te selecteren...** . U kunt ook het opdracht palet gebruiken om de **Azure functions te selecteren: Opdracht voor het** uploaden van lokale instellingen.

Door instellingen te uploaden, worden de bestaande instellingen bijgewerkt en worden nieuwe instellingen die zijn gedefinieerd in *Local. settings. json*, toegevoegd. Bij het uploaden worden geen instellingen van Azure verwijderd die niet worden vermeld in het lokale bestand. Als u deze instellingen wilt verwijderen, vouwt u het knoop punt **Toepassings instellingen** in de **Azure functions** Explorer uit, klikt u met de rechter muisknop op de instelling en selecteert u **instelling verwijderen...** . U kunt de instellingen ook rechtstreeks op de Azure Portal bewerken.

Als u wijzigingen wilt Toep assen die u via de portal of via **Azure Explorer** in het bestand *Local. settings. json* aanbrengt, klikt u met de rechter muisknop op het knoop punt **Toepassings instellingen** en selecteert u de opdracht **externe instellingen downloaden...** . U kunt ook het opdracht palet gebruiken om de **Azure functions te selecteren: Down load de** opdracht externe instellingen.

## <a name="add-a-second-function"></a>Een tweede functie toevoegen

Na uw eerste implementatie kunt u wijzigingen aanbrengen in uw code, zoals het toevoegen van extra functies en het opnieuw implementeren van de app.

1. **In Azure: Functies** Verkenner, selecteer de opdracht **functie maken** of gebruik **Azure functions: Maak een** functie vanuit het opdracht palet. Geef de volgende details op voor de functie:

    - Sjabloon: HTTP-trigger
    - Naam: "DigitsOfPi"
    - Autorisatie niveau: Anoniem

1. In Visual Studio code File Explorer is een submap met de naam van de functie die opnieuw bestanden bevat met de naam  *\_ \_\_\_init. py*, *Function. json*en *sample. dat*.

1. Vervang de inhoud van  *\_ \_init.py\_zodat deze overeenkomt met de volgende code. Hiermee wordt een teken reeks gegenereerd met de waarde van pi tot een aantal cijfers die in de URL zijn opgegeven (deze code gebruikt alleen een URL-para meter)\_*

    ```python
    import logging

    import azure.functions as func

    """ Adapted from the second, shorter solution at http://www.codecodex.com/wiki/Calculate_digits_of_pi#Python
    """

    def pi_digits_Python(digits):
        scale = 10000
        maxarr = int((digits / 4) * 14)
        arrinit = 2000
        carry = 0
        arr = [arrinit] * (maxarr + 1)
        output = ""

        for i in range(maxarr, 1, -14):
            total = 0
            for j in range(i, 0, -1):
                total = (total * j) + (scale * arr[j])
                arr[j] = total % ((j * 2) - 1)
                total = total / ((j * 2) - 1)

            output += "%04d" % (carry + (total / scale))
            carry = total % scale

        return output;

    def main(req: func.HttpRequest) -> func.HttpResponse:
        logging.info('DigitsOfPi HTTP trigger function processed a request.')

        digits_param = req.params.get('digits')

        if digits_param is not None:
            try:
                digits = int(digits_param)
            except ValueError:
                digits = 10   # A default

            if digits > 0:
                digit_string = pi_digits_Python(digits)

                # Insert a decimal point in the return value
                return func.HttpResponse(digit_string[:1] + '.' + digit_string[1:])

        return func.HttpResponse(
             "Please pass the URL parameter ?digits= to specify a positive number of digits.",
             status_code=400
        )
    ```

1. Omdat de code alleen HTTP Get, de *functie Modify. json* ondersteunt, zodat `"methods"` de verzameling alleen `"get"` bevat (dat wil zeggen `"post"`, verwijderen). Het hele bestand moet er als volgt uitzien:

    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ]
        },
        {
          "type": "http",
          "direction": "out",
          "name": "$return"
        }
      ]
    }
    ```

1. Start het fout opsporingsprogramma door op F5 te drukken of de opdracht **fout** > opsporing**starten** in het menu te selecteren. In het **uitvoer** venster moeten nu beide eind punten in uw project worden weer gegeven:

    ```output
    Http Functions:

            DigitsOfPi: [GET] http://localhost:7071/api/DigitsOfPi

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ```

1. Maak in een browser of vanuit krul een aanvraag naar `http://localhost:7071/api/DigitsOfPi?digits=125` en Bekijk de uitvoer. (Mogelijk ziet u dat het code algoritme niet helemaal nauw keurig is, maar we laten de verbeteringen aan u.) Stop het fout opsporingsprogramma wanneer u klaar bent.

1. Implementeer de code opnieuw met behulp van de **implementeren naar functie-app** in **Azure: Functie** Verkenner. Als u hierom wordt gevraagd, selecteert u de functie-app eerder gemaakt.

1. Zodra de implementatie is voltooid (dit duurt enkele minuten!), worden in het **uitvoer** venster de open bare eind punten weer gegeven waarmee u uw tests kunt herhalen.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=06-second-function)

## <a name="add-a-binding-to-write-messages-to-azure-storage"></a>Een binding toevoegen voor het schrijven van berichten naar Azure Storage

Met een _binding_ kunt u uw functie code verbinden met resources, zoals Azure Storage, zonder dat u gegevens toegangs code hoeft te schrijven. Een binding wordt gedefinieerd in het bestand *Function. json* en kan zowel invoer als uitvoer vertegenwoordigen. Een functie kan meerdere invoer-en uitvoer bindingen gebruiken, maar slechts één trigger. Zie [Azure functions triggers en bindingen](functions-triggers-bindings.md)voor meer informatie.

In deze sectie voegt u een opslag binding toe aan de functie HttpExample die eerder in deze zelf studie is gemaakt. De functie maakt gebruik van deze binding om bij elke aanvraag berichten te schrijven naar de opslag. De vraag opslag maakt gebruik van hetzelfde standaard opslag account dat wordt gebruikt door de functie-app. Als u van plan bent om intensief gebruik van opslag ruimte te maken, kunt u overwegen om echter een afzonderlijk account in te stellen.

1. Synchroniseer de externe instellingen voor uw Azure functions project in het bestand *Local. settings. json* door het opdracht palet te openen en **Azure functions te selecteren: Externe instellingen**downloaden. Open *Local. settings. json* en controleer of het een waarde bevat voor `AzureWebJobsStorage`. Deze waarde is het connection string voor het opslag account.

1. Klik in `HttpExample` de map met de rechter muisknop op de *functie. json*, selecteer **binding toevoegen**:

    ![De opdracht binding toevoegen in de Visual Studio code Explorer](media/tutorial-vs-code-serverless-python/add-binding-command.png)

1. In de prompts die volgen in Visual Studio code selecteert of geeft u de volgende waarden op:

    | Vraag | Te leveren waarde |
    | --- | --- |
    | Bindings richting instellen | af |
    | Binding selecteren met richting uit | Azure Queue Storage |
    | De naam die wordt gebruikt om deze binding in uw code aan te duiden | msg |
    | De wachtrij waarnaar het bericht wordt verzonden | outqueue |
    | Selecteer de instelling van *Local. settings. json* (voor de opslag verbinding wordt gevraagd) | AzureWebJobsStorage |

1. Nadat u deze selecties hebt gemaakt, controleert u of de volgende binding is toegevoegd aan het *Function. json* -bestand:

    ```json
        {
          "type": "queue",
          "direction": "out",
          "name": "msg",
          "queueName": "outqueue",
          "connection": "AzureWebJobsStorage"
        }
    ```

1. Nu u de binding hebt geconfigureerd, kunt u deze in uw functie code gebruiken. Opnieuw wordt de zojuist gedefinieerde binding in uw code weer gegeven als een argument voor `main` de functie  *\_in\_ \_\_init. py*. U kunt bijvoorbeeld `msg` het  *\_ \_bestand\_init\_. py* in HttpExample aanpassen aan het volgende, dat laat zien hoe het argument wordt gebruikt voor het schrijven van een tijds tempel bericht met de naam die wordt gebruikt in de schot. De opmerkingen geven uitleg over de specifieke wijzigingen:

    ```python
    import logging
    import datetime  # MODIFICATION: added import
    import azure.functions as func

    # MODIFICATION: the added binding appears as an argument; func.Out[func.QueueMessage]
    # is the appropriate type for an output binding with "type": "queue" (in function.json).
    def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> func.HttpResponse:
        logging.info('Python HTTP trigger function processed a request.')

        name = req.params.get('name')
        if not name:
            try:
                req_body = req.get_json()
            except ValueError:
                pass
            else:
                name = req_body.get('name')

        if name:
            # MODIFICATION: write the a message to the message queue, using msg.set
            msg.set(f"Request made for {name} at {datetime.datetime.now()}")

            return func.HttpResponse(f"Hello {name}!")
        else:
            return func.HttpResponse(
                 "Please pass a name on the query string or in the request body",
                 status_code=400
            )
    ```

1. Als u deze wijzigingen lokaal wilt testen, start u de debugger opnieuw in Visual Studio code door op F5 te drukken of de opdracht **debug** > **Start Debugging** te selecteren. Net als in het **uitvoer** venster moeten de eind punten in uw project worden weer gegeven.

1. Ga in een browser naar de URL `http://localhost:7071/api/HttpExample?name=VS%20Code` voor het maken van een aanvraag voor het HttpExample-eind punt, dat ook een bericht naar de wachtrij moet schrijven.

1. U kunt een van de volgende drie methoden gebruiken om te controleren of het bericht is geschreven naar de wachtrij voor outwachtrijen (zoals genoemd in de binding):

    1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en navigeer naar de resource groep met uw functions-project. In die resource groep, lokaal en navigeert u naar het opslag account voor het project en navigeert u naar **wacht rijen**. Navigeer op die pagina naar outwachtrij, waarin alle vastgelegde berichten moeten worden weer gegeven.

    1. Navigeer en onderzoek de wachtrij met behulp van de Azure Storage Explorer, die kan worden geïntegreerd met Visual Studio, zoals wordt beschreven in [functies verbinden om te Azure Storage met Visual Studio code](functions-add-output-binding-storage-queue-vs-code.md), met name de sectie [de uitvoer wachtrij onderzoeken](functions-add-output-binding-storage-queue-vs-code.md#examine-the-output-queue) .

    1. Gebruik de Azure CLI om een query uit te kunnen uitvoeren op de opslag wachtrij, zoals beschreven in [query uitvoeren op de opslag wachtrij](functions-add-output-binding-storage-queue-python.md#query-the-storage-queue).
    
1. Als u in de Cloud wilt testen, implementeert u de code opnieuw met behulp van **de **implementeren naar functie-app** in Azure: Functie** Verkenner. Als u hierom wordt gevraagd, selecteert u de functie-app eerder gemaakt. Zodra de implementatie is voltooid (dit duurt enkele minuten!), worden in het **uitvoer** venster weer de open bare eind punten weer gegeven waarmee u uw tests kunt herhalen.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/PWZWZ52?tutorial=python-functions-extension&step=07-storage-binding)

## <a name="clean-up-resources"></a>Resources opschonen

De functie-app die u hebt gemaakt, bevat resources die minimale kosten kunnen oplopen (Zie de [prijzen van functies](https://azure.microsoft.com/pricing/details/functions/)). Als u de resources wilt opschonen, klikt u met de **rechter muisknop op de functie-app in Azure: Functie** Verkenner en selecteer **functie-app verwijderen**. U kunt ook de [Azure Portal](https://portal.azure.com)bekijken, **resource groepen** selecteren in het navigatie deel venster aan de linkerkant, de resource groep selecteren die is gemaakt in het proces van deze zelf studie en vervolgens de opdracht **resource groep verwijderen** gebruiken.

## <a name="next-steps"></a>Volgende stappen

Gefeliciteerd met het volt ooien van dit overzicht van het implementeren van python-code op Azure Functions! U bent nu klaar om veel meer serverloze functies te maken.

Zoals eerder vermeld, kunt u meer informatie over de uitbrei ding van functies vinden door de GitHub-opslag plaats, [vscode-azurefunctions](https://github.com/Microsoft/vscode-azurefunctions), te bezoeken. Problemen en bijdragen zijn ook welkom.

Lees het [Azure functions overzicht](functions-overview.md) om de verschillende triggers te verkennen die u kunt gebruiken.

Ga naar het [Azure python-ontwikkelaars centrum](/azure/python/?view=azure-python)voor meer informatie over Azure-Services die u kunt gebruiken vanuit Python, met inbegrip van gegevens opslag naast AI-en Machine Learning-Services.

Er zijn ook andere Azure-extensies voor Visual Studio code die u mogelijk kunt vinden. Zoek alleen op ' Azure ' in de Extensions Explorer:

![Azure-extensies voor Visual Studio code](media/tutorial-vs-code-serverless-python/azure-extensions.png)

Enkele populaire extensies zijn:

- [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
- [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)
- [Azure CLI-Hulpprogram Ma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
- [Azure Resource Manager-Hulpprogram Ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
