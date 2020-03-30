---
title: Werken met Core-hulpprogramma's voor Azure-functies
description: Lees hoe u Azure-functies codeert en test vanaf de opdrachtprompt of terminal op uw lokale computer voordat u ze uitvoert op Azure-functies.
ms.assetid: 242736be-ec66-4114-924b-31795fd18884
ms.topic: conceptual
ms.date: 03/13/2019
ms.custom: 80e4ff38-5174-43
ms.openlocfilehash: 19691a654162ee3855cb257fd42e29d2e1fc0157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276658"
---
# <a name="work-with-azure-functions-core-tools"></a>Werken met Core-hulpprogramma's voor Azure-functies

Met Azure Functions Core Tools u uw functies op uw lokale computer ontwikkelen en testen vanaf de opdrachtprompt of terminal. Uw lokale functies kunnen verbinding maken met live Azure-services en u uw functies op uw lokale computer debuggen met de volledige runtime van functies. U zelfs een functie-app implementeren voor uw Azure-abonnement.

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Het ontwikkelen van functies op uw lokale computer en het publiceren ervan naar Azure met behulp van Core Tools volgt de volgende basisstappen:

> [!div class="checklist"]
> * [Installeer de core tools en afhankelijkheden.](#v2)
> * [Maak een functie-app-project op basis van een taalspecifieke sjabloon.](#create-a-local-functions-project)
> * [Registreer trigger- en bindende extensies.](#register-extensions)
> * [Definieer Opslag en andere verbindingen.](#local-settings-file)
> * [Maak een functie op basis van een trigger en taalspecifieke sjabloon.](#create-func)
> * [Voer de functie lokaal uit.](#start)
> * [Publiceer het project naar Azure.](#publish)

## <a name="core-tools-versions"></a>Versies van Core Tools

Er zijn drie versies van Azure Functions Core Tools. De versie die u gebruikt, is afhankelijk van uw lokale ontwikkelomgeving, [taalkeuze](supported-languages.md)en ondersteuningsniveau:

+ **Versie 1.x**: Ondersteunt versie 1.x van de runtime van Azure-functies. Deze versie van de hulpprogramma's wordt alleen ondersteund op Windows-computers en is geïnstalleerd vanuit een [npm-pakket.](https://www.npmjs.com/package/azure-functions-core-tools)

+ [**Versie 2.x/3.x**](#v2): ondersteunt [versie 2.x of 3.x van de runtime van Azure-functies](functions-versions.md). Deze versies ondersteunen [Windows,](/azure/azure-functions/functions-run-local?tabs=windows#v2) [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)en [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2) en gebruiken platformspecifieke pakketmanagers of npm voor installatie.

Tenzij anders vermeld, de voorbeelden in dit artikel zijn voor versie 3.x.

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions Core Tools installeren

[Azure Functions Core Tools] bevat een versie van dezelfde runtime die azure-functies-runtime aanstuurt die u uitvoeren op uw lokale ontwikkelingscomputer. Het biedt ook opdrachten om functies te maken, verbinding te maken met Azure en functieprojecten te implementeren.

>[!IMPORTANT]
>U moet de [Azure CLI](/cli/azure/install-azure-cli) lokaal hebben geïnstalleerd om te kunnen publiceren naar Azure vanuit Azure Functions Core Tools.  

### <a name="version-2x-and-3x"></a><a name="v2"></a>Versie 2.x en 3.x

Versie 2.x/3.x van de hulpprogramma's maakt gebruik van de runtime van Azure Functions die is gebouwd op .NET Core. Deze versie wordt ondersteund op alle platforms .NET Core ondersteunt, inclusief [Windows,](/azure/azure-functions/functions-run-local?tabs=windows#v2) [macOS](/azure/azure-functions/functions-run-local?tabs=macos#v2)en [Linux](/azure/azure-functions/functions-run-local?tabs=linux#v2). 

> [!IMPORTANT]
> U de vereiste voor het installeren van de .NET Core SDK omzeilen met behulp van [extensiebundels.]

# <a name="windows"></a>[Windows](#tab/windows)

In de volgende stappen wordt gebruik gemaakt van npm om Core Tools in Windows te installeren. U ook gebruik maken van [Chocolatey](https://chocolatey.org/). Zie de [leesmij van](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#windows)de core tools voor meer informatie.

1. Installeer [Node.js], inclusief npm.
    - Voor versie 2.x van de gereedschappen worden alleen Node.js 8.5 en latere versies ondersteund.
    - Voor versie 3.x van de gereedschappen worden alleen Node.js 10 en latere versies ondersteund.

1. Installeer het core tools-pakket:

    ##### <a name="v2x"></a>v2.x

    ```cmd
    npm install -g azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```cmd
    npm install -g azure-functions-core-tools@3
    ```

   Het kan enkele minuten duren voordat npm het Core Tools-pakket downloadt en installeert.

1. Als u niet van plan bent [extensiebundels]te gebruiken, installeert u de [.NET Core 2.x SDK voor Windows.](https://www.microsoft.com/net/download/windows)

# <a name="macos"></a>[Macos](#tab/macos)

De volgende stappen gebruiken Homebrew om de Core Tools op macOS te installeren.

1. Installeer [Homebrew,](https://brew.sh/)als het nog niet is geïnstalleerd.

1. Installeer het core tools-pakket:

    ##### <a name="v2x"></a>v2.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools
    ```

    ##### <a name="v3x"></a>v3.x

    ```bash
    brew tap azure/functions
    brew install azure-functions-core-tools@3
    # if upgrading on a machine that has 2.x installed
    brew link --overwrite azure-functions-core-tools@3
    ```

# <a name="linux"></a>[Linux](#tab/linux)

De volgende stappen gebruiken [APT](https://wiki.debian.org/Apt) om Core Tools te installeren op uw Ubuntu/Debian Linux-distributie. Voor andere Linux-distributies, zie de [Core Tools readme](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installeer de GPG-sleutel voor Microsoft-pakketopslagplaatsen om de integriteit van het pakket te valideren:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Stel de lijst met .NET-ontwikkelingsbronnen in voordat u een APT-update uitvoert.

   Voer de opdracht uit om de APT-bronlijst voor Ubuntu in te stellen:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

   Voer de opdracht uit om de apt-bronlijst voor Debian in te stellen:

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Controleer `/etc/apt/sources.list.d/dotnetdev.list` het bestand voor een van de juiste Linux-versie strings hieronder vermeld:

    | Linux-distributie | Versie |
    | --------------- | ----------- |
    | Debian 9 | `stretch` |
    | Debian 8 | `jessie` |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Start de APT-bronupdate:

    ```bash
    sudo apt-get update
    ```

1. Installeer het core tools-pakket:

    ```bash
    sudo apt-get install azure-functions-core-tools
    ```

1. Als u niet van plan bent [om extensiebundels]te gebruiken, installeert u [.NET Core 2.x SDK voor Linux.](https://www.microsoft.com/net/download/linux)

---

## <a name="create-a-local-functions-project"></a>Een lokaal Functions-project maken

Een projectmap voor functies bevat de bestanden [host.json](functions-host-json.md) en [local.settings.json,](#local-settings-file)samen met submappen die de code voor afzonderlijke functies bevatten. Deze map is het equivalent van een functie-app in Azure. Zie de [handleiding Azure Functions developers](functions-reference.md#folder-structure)voor meer informatie over de mapstructuur Functies.

Versie 2.x vereist dat u een standaardtaal voor uw project selecteert wanneer deze wordt geïnitialiseerd. In versie 2.x gebruiken alle toegevoegde functies standaardtaalsjablonen. In versie 1.x geeft u de taal op telkens wanneer u een functie maakt.

Voer in het terminalvenster of vanuit een opdrachtprompt de volgende opdracht uit om de project- en lokale Git-opslagplaats te maken:

```
func init MyFunctionProj
```

Wanneer u een projectnaam opgeeft, wordt een nieuwe map met die naam gemaakt en geïnitialiseerd. Anders wordt de huidige map geïnitialiseerd.  
In versie 2.x moet u bij het uitvoeren van de opdracht een runtime voor uw project kiezen. 

<pre>
Select a worker runtime:
dotnet
node
python 
powershell
</pre>

Gebruik de pijl-omhoog-/pijl-omlaagtoetsen om een taal te kiezen en druk op Enter. Als u JavaScript- of TypeScript-functies wilt ontwikkelen, kiest u **knooppunt**en selecteert u de taal. TypeScript heeft [een aantal aanvullende vereisten](functions-reference-node.md#typescript). 

De uitvoer ziet eruit als het volgende voorbeeld voor een JavaScript-project:

<pre>
Select a worker runtime: node
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing C:\myfunctions\myMyFunctionProj\.vscode\extensions.json
Initialized empty Git repository in C:/myfunctions/myMyFunctionProj/.git/
</pre>

`func init`ondersteunt de volgende opties, die versie 2.x-only zijn, tenzij anders vermeld:

| Optie     | Beschrijving                            |
| ------------ | -------------------------------------- |
| **`--csharp`**<br/> **`--dotnet`** | Initialiseert een [C# klasse bibliotheek (.cs) project](functions-dotnet-class-library.md). |
| **`--csx`** | Initialiseert een [C#-scriptproject (.csx).](functions-reference-csharp.md) U moet `--csx` in volgende opdrachten opgeven. |
| **`--docker`** | Maak een Dockerfile voor een container met een `--worker-runtime`basisafbeelding die is gebaseerd op de gekozen . Gebruik deze optie wanneer u van plan bent te publiceren naar een aangepaste Linux-container. |
| **`--docker-only`** |  Hiermee voegt u een Dockerfile toe aan een bestaand project. Vraagt om de runtime van de werknemer als deze niet is opgegeven of ingesteld in local.settings.json. Gebruik deze optie wanneer u van plan bent een bestaand project te publiceren naar een aangepaste Linux-container. |
| **`--force`** | Initialiseer het project zelfs als er bestaande bestanden in het project zitten. Met deze instelling worden bestaande bestanden met dezelfde naam overschrijft. Andere bestanden in de projectmap worden niet beïnvloed. |
| **`--java`**  | Initialiseert een [Java-project.](functions-reference-java.md) |
| **`--javascript`**<br/>**`--node`**  | Initialiseert een [JavaScript-project](functions-reference-node.md). |
| **`--no-source-control`**<br/>**`-n`** | Hiermee voorkomt u de standaardcreatie van een Git-opslagplaats in versie 1.x. In versie 2.x wordt de git-repository niet standaard gemaakt. |
| **`--powershell`**  | Initialiseert een [PowerShell-project](functions-reference-powershell.md). |
| **`--python`**  | Initialiseert een [Python-project](functions-reference-python.md). |
| **`--source-control`** | Hiermee bepaalt u of er een git-repository is gemaakt. Standaard wordt er geen opslagplaats gemaakt. Wanneer `true`wordt een opslagplaats gemaakt. |
| **`--typescript`**  | Initialiseert een [TypeScript-project](functions-reference-node.md#typescript). |
| **`--worker-runtime`** | Hiermee stelt u de taalruntijd voor het project in. Ondersteunde waarden `csharp`zijn: `dotnet` `java`, `javascript``node` , , `powershell`(JavaScript), , `python`en `typescript`. Wanneer u niet bent ingesteld, wordt u gevraagd uw runtime te kiezen tijdens de initialisatie. |

> [!IMPORTANT]
> Standaard maakt versie 2.x van de Core Tools functie-appprojecten voor de .NET-runtime as [C#-klasseprojecten](functions-dotnet-class-library.md) (.csproj). Deze C#-projecten, die kunnen worden gebruikt met Visual Studio of Visual Studio Code, worden gecompileerd tijdens het testen en bij publicatie naar Azure. Als u in plaats daarvan dezelfde C#-scriptbestanden (.csx) wilt maken en gebruiken die zijn `--csx` gemaakt in versie 1.x en in de portal, moet u de parameter opnemen wanneer u functies maakt en implementeert.

[!INCLUDE [functions-core-tools-install-extension](../../includes/functions-core-tools-install-extension.md)]

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Standaard worden deze instellingen niet automatisch gemigreerd wanneer het project naar Azure wordt gepubliceerd. Gebruik `--publish-local-settings` de schakelaar [wanneer u publiceert](#publish) om ervoor te zorgen dat deze instellingen worden toegevoegd aan de functie-app in Azure. Houd er rekening mee dat waarden in **ConnectionStrings** nooit worden gepubliceerd.

De instellingen waarden van de functie-app kunnen ook in uw code worden gelezen als omgevingsvariabelen. Zie voor meer informatie de sectie Omgevingsvariabelen van deze taalspecifieke referentieonderwerpen:

* [C# vooraf gecompileerd](functions-dotnet-class-library.md#environment-variables)
* [C#-script (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

Wanneer er geen geldige tekenreeks [`AzureWebJobsStorage`] voor opslagverbindingen is ingesteld en de emulator niet wordt gebruikt, wordt het volgende foutbericht weergegeven:

> Ontbrekende waarde voor AzureWebJobsStorage in local.settings.json. Dit is vereist voor alle andere triggers dan HTTP. U 'func azure functionapp fetch-app-settings \<functionAppName'\>uitvoeren of een verbindingstekenreeks opgeven in local.settings.json.

### <a name="get-your-storage-connection-strings"></a>Uw opslagverbindingstekenreeksen aanschaffen

Zelfs wanneer u de Microsoft Azure Storage Emulator gebruikt voor ontwikkeling, u testen met een werkelijke opslagverbinding. Ervan uitgaande dat u al [een opslagaccount](../storage/common/storage-create-storage-account.md)hebt gemaakt, u op een van de volgende manieren een geldige tekenreeks voor opslagverbindingen krijgen:

- Zoek in de [Azure-portal]naar **opslagaccounts**en selecteer deze . 
  ![Opslagaccounts selecteren vanuit Azure-portal](./media/functions-run-local/select-storage-accounts.png)
  
  Selecteer uw opslagaccount, selecteer **Toegangssleutels** in **Instellingen**en kopieer vervolgens een van de waarden van de **verbindingstekenreeks.**
  ![Verbindingstekenreeks kopiëren vanuit Azure-portal](./media/functions-run-local/copy-storage-connection-portal.png)

- Gebruik [Azure Storage Explorer](https://storageexplorer.com/) om verbinding te maken met uw Azure-account. Vouw in de **Explorer**uw abonnement uit, vouw **Opslagaccounts**uit, selecteer uw opslagaccount en kopieer de primaire of secundaire verbindingstekenreeks.

  ![Verbindingstekenreeks kopiëren vanuit Storage Explorer](./media/functions-run-local/storage-explorer.png)

+ Gebruik Core Tools om de verbindingstekenreeks van Azure te downloaden met een van de volgende opdrachten:

  + Download alle instellingen van een bestaande functie-app:

    ```
    func azure functionapp fetch-app-settings <FunctionAppName>
    ```
  + Download de verbindingstekenreeks voor een specifiek opslagaccount:

    ```
    func azure storage fetch-connection-string <StorageAccountName>
    ```

    Wanneer u nog niet bent aangemeld bij Azure, wordt u daarom gevraagd.

## <a name="create-a-function"></a><a name="create-func"></a>Een functie maken

Voer de volgende opdracht uit om een functie te maken:

```
func new
```

In versie 2.x wordt `func new` u bij het uitvoeren van u gevraagd een sjabloon te kiezen in de standaardtaal van uw functie-app, dan wordt u ook gevraagd een naam voor uw functie te kiezen. In versie 1.x wordt u ook gevraagd om de taal te kiezen.

<pre>
Select a language: Select a template:
Blob trigger
Cosmos DB trigger
Event Grid trigger
HTTP trigger
Queue trigger
SendGrid
Service Bus Queue trigger
Service Bus Topic trigger
Timer trigger
</pre>

Functiecode wordt gegenereerd in een submap met de opgegeven functienaam, zoals u zien in de volgende triggeruitvoer in de wachtrij:

<pre>
Select a language: Select a template: Queue trigger
Function name: [QueueTriggerJS] MyQueueTrigger
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\index.js
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\readme.md
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\sample.dat
Writing C:\myfunctions\myMyFunctionProj\MyQueueTrigger\function.json
</pre>

U deze opties ook in de opdracht opgeven met de volgende argumenten:

| Argument     | Beschrijving                            |
| ------------------------------------------ | -------------------------------------- |
| **`--csx`** | (Versie 2.x) Hiermee genereert u dezelfde C#-scriptsjablonen (.csx) die worden gebruikt in versie 1.x en in de portal. |
| **`--language`**, **`-l`**| De programmeertaal van de sjabloon, zoals C#, F#, of JavaScript. Deze optie is vereist in versie 1.x. Gebruik deze optie niet in versie 2.x of kies een taal die overeenkomt met de runtime van de werknemer. |
| **`--name`**, **`-n`** | De functienaam. |
| **`--template`**, **`-t`** | Gebruik `func templates list` de opdracht om de volledige lijst met beschikbare sjablonen voor elke ondersteunde taal te bekijken.   |

Als u bijvoorbeeld een JavaScript HTTP-trigger in één opdracht wilt maken, voert u het als:

```
func new --template "Http Trigger" --name MyHttpTrigger
```

Voer het als nodig op om een functie te maken die in één opdracht wordt geactiveerd in één opdracht:

```
func new --template "Queue Trigger" --name QueueTriggerJS
```

## <a name="run-functions-locally"></a><a name="start"></a>Functies lokaal uitvoeren

Als u een project Functies wilt uitvoeren, voert u de host functies uit. De host maakt triggers mogelijk voor alle functies in het project. De opdracht start varieert, afhankelijk van uw projecttaal.

# <a name="c"></a>[C\#](#tab/csharp)

```
func start --build
```
# <a name="javascript"></a>[Javascript](#tab/node)

```
func start
```

# <a name="python"></a>[Python](#tab/python)

```
func start
```
Deze opdracht moet worden [uitgevoerd in een virtuele omgeving.](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#create-venv)

# <a name="typescript"></a>[TypeScript](#tab/ts)

```
npm install
npm start     
```

---

>[!NOTE]  
> Voor versie 1.x van de `host` runtime functies is de opdracht vereist, zoals in het volgende voorbeeld:
>
> ```
> func host start
> ```

`func start`ondersteunt de volgende opties:

| Optie     | Beschrijving                            |
| ------------ | -------------------------------------- |
| **`--no-build`** | Doe geen huidige project bouwen voordat u wordt uitgevoerd. Alleen voor dotnetprojecten. Standaard is ingesteld op false. Wordt niet ondersteund voor versie 1.x. |
| **`--cert`** | Het pad naar een .pfx-bestand met een privésleutel. Alleen gebruikt `--useHttps`met . Wordt niet ondersteund voor versie 1.x. |
| **`--cors-credentials`** | Verificatieverzoeken voor cross-origine toestaan (d.w.z. cookies en de header Verificatie) Die niet worden ondersteund voor versie 1.x. |
| **`--cors`** | Een door komma's gescheiden lijst van CORS-oorsprong, zonder spaties. |
| **`--language-worker`** | Argumenten om de taalwerknemer te configureren. U bijvoorbeeld foutopsporing voor taalwerknemers inschakelen door [foutopsporingspoort en andere vereiste argumenten op te](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers)geven. Wordt niet ondersteund voor versie 1.x. |
| **`--nodeDebugPort`**, **`-n`** | De poort voor de Node.js debugger te gebruiken. Standaard: een waarde vanaf launch.json of 5858. Versie 1.x alleen. |
| **`--password`** | Het wachtwoord of een bestand met het wachtwoord voor een .pfx-bestand. Alleen gebruikt `--cert`met . Wordt niet ondersteund voor versie 1.x. |
| **`--port`**, **`-p`** | De lokale haven om naar te luisteren. Standaardwaarde: 7071. |
| **`--pause-on-error`** | Pauzeer voor extra invoer voordat u het proces afsluit. Alleen gebruikt bij de lancering van Core Tools vanuit een geïntegreerde ontwikkelomgeving (IDE).|
| **`--script-root`**, **`--prefix`** | Wordt gebruikt om het pad op te geven naar de hoofdmap van de functie-app die moet worden uitgevoerd of geïmplementeerd. Dit wordt gebruikt voor gecompileerde projecten die projectbestanden genereren in een submap. Wanneer u bijvoorbeeld een C#-klassebibliotheekproject maakt, worden de host.json-, local.settings.json- en function.json-bestanden gegenereerd in een *hoofdsubmap* met een pad als `MyProject/bin/Debug/netstandard2.0`. Stel in dit geval het `--script-root MyProject/bin/Debug/netstandard2.0`voorvoegsel in als . Dit is de hoofdmap van de functie-app wanneer deze wordt uitgevoerd in Azure. |
| **`--timeout`**, **`-t`** | De time-out voor de host functies om te starten, in seconden. Standaard: 20 seconden.|
| **`--useHttps`** | Bind `https://localhost:{port}` aan in `http://localhost:{port}`plaats van aan . Standaard maakt deze optie een vertrouwd certificaat op uw computer.|

Wanneer de host Functies wordt gestart, wordt de URL van http-geactiveerde functies uitgevoerd:

<pre>
Found the following functions:
Host.Functions.MyHttpTrigger

Job host started
Http Function MyHttpTrigger: http://localhost:7071/api/MyHttpTrigger
</pre>

>[!IMPORTANT]
>Wanneer u lokaal wordt uitgevoerd, wordt de autorisatie niet afgedwongen voor HTTP-eindpunten. Dit betekent dat alle lokale `authLevel = "anonymous"`HTTP-aanvragen worden behandeld als . Zie het [HTTP-bindende artikel](functions-bindings-http-webhook-trigger.md#authorization-keys)voor meer informatie.

### <a name="passing-test-data-to-a-function"></a>Testgegevens doorgeven aan een functie

Als u uw functies lokaal wilt testen, start u [de host functies](#start) en oproepeindpunten op de lokale server met HTTP-aanvragen. Het eindpunt dat u aanroept, is afhankelijk van het type functie.

>[!NOTE]
> Voorbeelden in dit onderwerp gebruiken het cURL-gereedschap om HTTP-aanvragen vanaf de terminal of een opdrachtprompt te verzenden. U een tool naar keuze gebruiken om HTTP-aanvragen naar de lokale server te verzenden. De cURL tool is standaard beschikbaar op Linux-gebaseerde systemen en Windows 10 build 17063 en hoger. Op oudere Windows moet u eerst het [cURL-hulpprogramma](https://curl.haxx.se/)downloaden en installeren.

Zie [Strategieën voor het testen van uw code in Azure-functies](functions-test-a-function.md)voor meer algemene informatie over testfuncties.

#### <a name="http-and-webhook-triggered-functions"></a>HTTP- en webhookgeactiveerde functies

U roept het volgende eindpunt op om HTTP- en webhookgeactiveerde functies lokaal uit te voeren:

    http://localhost:{port}/api/{function_name}

Zorg ervoor dat u dezelfde servernaam en poort gebruikt waarop de functiehost luistert. U ziet dit in de uitvoer die wordt gegenereerd bij het starten van de functiehost. U deze URL aanroepen met behulp van elke HTTP-methode die door de trigger wordt ondersteund.

Met de volgende cURL-opdracht activeert u de `MyHttpTrigger` functie Snelstart vanuit een GET-aanvraag met de _naamparameter_ die is doorgegeven in de querytekenreeks.

```
curl --get http://localhost:7071/api/MyHttpTrigger?name=Azure%20Rocks
```

Het volgende voorbeeld is dezelfde functie die wordt aangeroepen vanuit een _post-aanvraag_ die naam doorgeeft in de aanvraaginstantie:

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST http://localhost:7071/api/MyHttpTrigger --data '{"name":"Azure Rocks"}'
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
curl --request POST http://localhost:7071/api/MyHttpTrigger --data "{'name':'Azure Rocks'}"
```
---

U GET-verzoeken indienen via een browser die gegevens doorgeeft in de querytekenreeks. Voor alle andere HTTP-methoden moet u cURL, Fiddler, Postman of een soortgelijk HTTP-testprogramma gebruiken.

#### <a name="non-http-triggered-functions"></a>Niet-HTTP-geactiveerde functies

Voor allerlei andere functies dan HTTP-triggers en webhooks en Event Grid-triggers u uw functies lokaal testen door een eindpunt van het beheer aan te roepen. Als u dit eindpunt aanroept met een HTTP POST-aanvraag op de lokale server, wordt de functie geactiveerd. 

Zie [Lokaal testen met viewerweb-app](functions-bindings-event-grid-trigger.md#local-testing-with-viewer-web-app)als u gebeurtenisrasterfuncties lokaal wilt testen.

U optioneel testgegevens doorgeven aan de uitvoering in de hoofdtekst van het POST-verzoek. Deze functionaliteit is vergelijkbaar met het tabblad **Testen** in de Azure-portal.

U roept het volgende beheerderseindpunt op om niet-HTTP-functies te activeren:

    http://localhost:{port}/admin/functions/{function_name}

Als u testgegevens wilt doorgeven aan het eindpunt van een functie voor beheerders, moet u de gegevens in de hoofdtekst van een bericht van een POST-verzoek leveren. De berichttekst moet de volgende JSON-indeling hebben:

```JSON
{
    "input": "<trigger_input>"
}
```

De `<trigger_input>` waarde bevat gegevens in een indeling die door de functie wordt verwacht. Het volgende cURL-voorbeeld is `QueueTriggerJS` een post naar een functie. In dit geval is de invoer een tekenreeks die overeenkomt met het bericht dat naar verwachting in de wachtrij wordt gevonden.

# <a name="bash"></a>[Bash](#tab/bash)
```bash
curl --request POST -H "Content-Type:application/json" --data '{"input":"sample queue data"}' http://localhost:7071/admin/functions/QueueTrigger
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```bash
curl --request POST -H "Content-Type:application/json" --data "{'input':'sample queue data'}" http://localhost:7071/admin/functions/QueueTrigger
```
---

#### <a name="using-the-func-run-command-version-1x-only"></a>De `func run` opdracht gebruiken (alleen versie 1.x)

>[!IMPORTANT]
> De `func run` opdracht wordt alleen ondersteund in versie 1.x van de gereedschappen. Zie het onderwerp [Hoe u azure-versies voor runtime-functies](set-runtime-version.md)target .

In versie 1.x u een functie `func run <FunctionName>` ook rechtstreeks aanroepen door invoergegevens voor de functie te gebruiken en te verstrekken. Deze opdracht is vergelijkbaar met het uitvoeren van een functie met het tabblad **Testen** in de Azure-portal.

`func run`ondersteunt de volgende opties:

| Optie     | Beschrijving                            |
| ------------ | -------------------------------------- |
| **`--content`**, **`-c`** | Inline-inhoud. |
| **`--debug`**, **`-d`** | Voeg een foutopsporing toe aan het hostproces voordat u de functie uitvoert.|
| **`--timeout`**, **`-t`** | Tijd om te wachten (in seconden) totdat de lokale functiehost klaar is.|
| **`--file`**, **`-f`** | De bestandsnaam die u als inhoud wilt gebruiken.|
| **`--no-interactive`** | Vraagt niet om invoer. Handig voor automatiseringsscenario's.|

Als u bijvoorbeeld een http-geactiveerde functie wilt aanroepen en de inhoudstekst wilt doorgeven, voert u de volgende opdracht uit:

```
func run MyHttpTrigger -c '{\"name\": \"Azure\"}'
```

## <a name="publish-to-azure"></a><a name="publish"></a>Publiceren naar Azure

De Azure Functions Core Tools ondersteunt twee typen implementatie: het implementeren van functieprojectbestanden rechtstreeks naar uw functie-app via [Zip Deploy](functions-deployment-technologies.md#zip-deploy) en het implementeren van een [aangepaste Docker-container.](functions-deployment-technologies.md#docker-container) U moet al [een functie-app hebben gemaakt in uw Azure-abonnement,](functions-cli-samples.md#create)waarvoor u uw code implementeert. Projecten waarvoor compilatie vereist is, moeten worden gebouwd, zodat de binaire bestanden kunnen worden geïmplementeerd.

>[!IMPORTANT]
>U moet de [Azure CLI](/cli/azure/install-azure-cli) lokaal hebben geïnstalleerd om vanuit Core Tools naar Azure te kunnen publiceren.  

Een projectmap kan taalspecifieke bestanden en mappen bevatten die niet mogen worden gepubliceerd. Uitgesloten items worden weergegeven in een .funcignore-bestand in de hoofdprojectmap.     

### <a name="deploy-project-files"></a><a name="project-file-deployment"></a>Projectbestanden implementeren

Als u uw lokale code wilt publiceren `publish` naar een functie-app in Azure, gebruikt u de opdracht:

```
func azure functionapp publish <FunctionAppName>
```

Deze opdracht wordt gepubliceerd naar een bestaande functie-app in Azure. Er wordt een foutmelding weergegeven als u `<FunctionAppName>` probeert te publiceren naar een abonnement dat niet bestaat in uw abonnement. Zie [Een functie-app](./scripts/functions-cli-create-serverless.md)maken voor serverloze uitvoering voor meer informatie over het maken van een functie-app vanuit de opdrachtprompt of het terminalvenster met de Azure CLI. Standaard maakt deze opdracht gebruik [van externe build](functions-deployment-technologies.md#remote-build) en implementeert u uw app om uit het implementatiepakket te worden [uitgevoerd.](run-functions-from-deployment-package.md) Als u deze aanbevolen implementatiemodus `--nozip` wilt uitschakelen, gebruikt u de optie.

>[!IMPORTANT]
> Wanneer u een functie-app maakt in de Azure-portal, wordt standaard versie 2.x van de runtime van de functie gebruikt. Volg de instructies in [Runtime uitvoeren op versie 1.x](functions-versions.md#creating-1x-apps)om de functie-app versie 1.x van de runtime te laten gebruiken.
> U de runtime-versie niet wijzigen voor een functie-app met bestaande functies.

Voor beide versies zijn de volgende publicatieopties van toepassing, 1.x en 2.x:

| Optie     | Beschrijving                            |
| ------------ | -------------------------------------- |
| **`--publish-local-settings -i`** |  Publicatie-instellingen in local.settings.json naar Azure, waarin wordt gevraagd om te overschrijven als de instelling al bestaat. Als u de Microsoft Azure Storage Emulator gebruikt, wijzigt u eerst de app-instelling in een [werkelijke opslagverbinding.](#get-your-storage-connection-strings) |
| **`--overwrite-settings -y`** | De prompt onderdrukken om app-instellingen te overschrijven wanneer `--publish-local-settings -i` deze worden gebruikt.|

De volgende publicatieopties worden alleen ondersteund in versie 2.x:

| Optie     | Beschrijving                            |
| ------------ | -------------------------------------- |
| **`--publish-settings-only`**, **`-o`** |  Publiceer alleen instellingen en sla de inhoud over. Standaard is prompt. |
|**`--list-ignored-files`** | Hiermee wordt een lijst weergegeven met bestanden die tijdens het publiceren worden genegeerd, die is gebaseerd op het .funcignore-bestand. |
| **`--list-included-files`** | Hiermee wordt een lijst weergegeven met bestanden die zijn gepubliceerd en die is gebaseerd op het .funcignore-bestand. |
| **`--nozip`** | Hiermee schakelt `Run-From-Package` u de standaardmodus uit. |
| **`--build-native-deps`** | Hiermee slaat u de map Genereren van .wheels over bij het publiceren van Python-functie-apps. |
| **`--build`**, **`-b`** | Voert buildactie uit bij het implementeren naar een Linux-functie-app. Accepteert: `remote` en `local`. |
| **`--additional-packages`** | Lijst met pakketten die u moet installeren bij het bouwen van native afhankelijkheden. Bijvoorbeeld: `python3-dev libevent-dev`. |
| **`--force`** | Verificatie vooraf publiceren in bepaalde scenario's negeren. |
| **`--csx`** | Een C#-scriptproject (.csx) publiceren. |
| **`--no-build`** | Maak geen .NET-klassebibliotheekfuncties. |
| **`--dotnet-cli-params`** | Bij het publiceren van gecompileerde C# (.csproj) functies, de kern tools noemt 'dotnet build --output bin /publish'. Alle parameters die hieraan worden doorgegeven, worden aan de opdrachtregel toegevoegd. |

### <a name="deploy-custom-container"></a>Aangepaste container implementeren

Met Azure Functions u uw functieproject implementeren in een [aangepaste Docker-container.](functions-deployment-technologies.md#docker-container) Zie [Een functie op Linux maken met een aangepaste afbeelding](functions-create-function-linux-custom-image.md)voor meer informatie. Aangepaste containers moeten een Dockerfile hebben. Als u een app wilt maken met een Dockerfile, gebruikt u de optie --dockerfile op `func init`.

```
func deploy
```

De volgende aangepaste opties voor het implementeren van containers zijn beschikbaar:

| Optie     | Beschrijving                            |
| ------------ | -------------------------------------- |
| **`--registry`** | De naam van een Docker-register waarbij de huidige gebruiker zich heeft aangemeld. |
| **`--platform`** | Hostingplatform voor de functie-app. Geldige opties zijn`kubernetes` |
| **`--name`** | Functie-app naam. |
| **`--max`**  | Stelt optioneel het maximum aantal functie-app-exemplaren in dat moet worden geïmplementeerd. |
| **`--min`**  | Stelt optioneel het minimumaantal functie-app-exemplaren in dat moet worden geïmplementeerd. |
| **`--config`** | Hiermee stelt u een optioneel implementatieconfiguratiebestand in. |

## <a name="monitoring-functions"></a>Bewakingsfuncties

De aanbevolen manier om de uitvoering van uw functies te controleren is door te integreren met Azure Application Insights. U ook uitvoeringslogboeken streamen naar uw lokale computer. Zie [Azure-functies controleren](functions-monitoring.md)voor meer informatie.

### <a name="application-insights-integration"></a>Integratie van Application Insights

Application Insights-integratie moet worden ingeschakeld wanneer u uw functie-app in Azure maakt. Als uw functie-app om de een of andere reden niet is verbonden met een instantie Application Insights, is het eenvoudig om deze integratie uit te brengen in de Azure-portal. 

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

### <a name="enable-streaming-logs"></a>Streaminglogboeken inschakelen

U een stroom van logboekbestanden bekijken die door uw functies wordt gegenereerd in een opdrachtregelsessie op uw lokale computer. 

#### <a name="native-streaming-logs"></a>Native streaming logs

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

Dit type streaminglogboeken vereist dat application insights-integratie wordt ingeschakeld voor uw functie-app.   


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwikkelen, testen en publiceren van Azure-functies met Azure Functions Core Tools [Microsoft learn module](https://docs.microsoft.com/learn/modules/develop-test-deploy-azure-functions-with-core-tools/) Azure Functions Core Tools is open source en gehost op [GitHub.](https://github.com/azure/azure-functions-cli)  
Als u een bug- of functieverzoek wilt indienen, [opent u een GitHub-probleem](https://github.com/azure/azure-functions-cli/issues).

<!-- LINKS -->

[Core-hulpprogramma's voor Azure-functies]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure-portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
[`FUNCTIONS_WORKER_RUNTIME`]: functions-app-settings.md#functions_worker_runtime
['AzureWebJobsStorage']: functions-app-settings.md#azurewebjobsstorage
[uitbreidingsbundels]: functions-bindings-register.md#extension-bundles
