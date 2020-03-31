---
title: Azure-functies ontwikkelen met Behulp van Visual Studio-code
description: Meer informatie over het ontwikkelen en testen van Azure-functies met behulp van de Azure Functions-extensie voor Visual Studio Code.
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 2d33b7dddf29d37d174bdb7734e9048bc1658840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277165"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Azure-functies ontwikkelen met Behulp van Visual Studio-code

Met de [Azure Functions-extensie voor Visual Studio Code] u lokaal functies ontwikkelen en implementeren in Azure. Als deze ervaring de eerste is met Azure-functies, u meer informatie krijgen bij [Een inleiding tot Azure-functies](functions-overview.md).

De azure-functie-extensie biedt de volgende voordelen:

* Bewerk, bouw en voer functies uit op uw lokale ontwikkelingscomputer.
* Publiceer uw Azure Functions-project rechtstreeks naar Azure.
* Schrijf uw functies in verschillende talen en profiteer van de voordelen van Visual Studio Code.

De extensie kan worden gebruikt met de volgende talen, die worden ondersteund door de runtime van Azure-functies vanaf versie 2.x:

* [C# gecompileerd](functions-dotnet-class-library.md)
* [C#-script](functions-reference-csharp.md)<sup>*</sup>
* [Javascript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [Powershell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Vereist dat u [C#-script instelt als uw standaardprojecttaal](#c-script-projects).

In dit artikel zijn voorbeelden momenteel alleen beschikbaar voor JavaScript-functies (Node.js) en C# klassebibliotheek.  

In dit artikel vindt u informatie over het gebruik van de azure-functies-extensie om functies te ontwikkelen en te publiceren naar Azure. Voordat u dit artikel leest, moet u [uw eerste functie maken met behulp van Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Meng lokale ontwikkeling en portalontwikkeling niet voor één functie-app. Wanneer u publiceert van een lokaal project naar een functie-app, overschrijft het implementatieproces alle functies die u in de portal hebt ontwikkeld.

## <a name="prerequisites"></a>Vereisten

Voordat u de [Azure Functions-extensie][Azure Functions voor Visual Studio-code]installeert en uitvoert, moet u aan deze vereisten voldoen:

* [Visual Studio Code](https://code.visualstudio.com/) geïnstalleerd op een van de [ondersteunde platforms.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* Een actief Azure-abonnement.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Andere bronnen die u nodig hebt, zoals een Azure-opslagaccount, worden in uw abonnement gemaakt wanneer u [publiceert met Behulp van Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> U functies lokaal ontwikkelen en publiceren naar Azure zonder ze lokaal te hoeven starten en uitvoeren. Als u uw functies lokaal wilt uitvoeren, moet u aan een aantal aanvullende vereisten voldoen, waaronder een automatische download van Azure Functions Core Tools. Zie [Aanvullende vereisten voor het lokaal uitvoeren van een project](#additional-requirements-for-running-a-project-locally)voor meer informatie.

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Een Azure Functions-project maken

Met de extensie Functies u een functie-app-project maken, samen met uw eerste functie. In de volgende stappen wordt uitgelegd hoe u een http-geactiveerde functie maakt in een nieuw project Functies. [HTTP trigger](functions-bindings-http-webhook.md) is de eenvoudigste functie trigger template aan te tonen.

1. Selecteer in **Azure: functies**het pictogram **Functie maken:**

    ![Een functie maken](./media/functions-develop-vs-code/create-function.png)

1. Selecteer de map voor uw functie-app-project en **selecteer vervolgens een taal voor uw functieproject.**

1. Als u de Core Tools nog niet hebt geïnstalleerd, wordt u gevraagd **een versie** van de Core Tools te selecteren om te installeren. Kies versie 2.x of een latere versie. 

1. Selecteer de **functiesjabloon HTTP-trigger** of u **Overslaan voorlopig** selecteren om een project zonder functie te maken. U later altijd [een functie aan uw project toevoegen.](#add-a-function-to-your-project)

    ![De sjabloon voor de HTTP-trigger kiezen](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Typ **HttpExample** voor de functienaam en selecteer Enter en selecteer **Functieautorisatie.** Voor dit autorisatieniveau moet u een [functiesleutel](functions-bindings-http-webhook-trigger.md#authorization-keys) verstrekken wanneer u het functieeindpunt aanroept.

    ![Functieautorisatie selecteren](./media/functions-develop-vs-code/create-function-auth.png)

    Er wordt een functie gemaakt in de door u gekozen taal en in de sjabloon voor een HTTP-geactiveerde functie.

    ![Functiesjabloon die door HTTP wordt geactiveerd in Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Gegenereerde projectbestanden

De projectsjabloon maakt een project in de door u gekozen taal en installeert vereiste afhankelijkheden. Voor elke taal heeft het nieuwe project de volgende bestanden:

* **host.json**: Hiermee u de host functies configureren. Deze instellingen zijn van toepassing wanneer u functies lokaal uitvoert en wanneer u deze uitvoert in Azure. Zie [host.json referentie voor](functions-host-json.md)meer informatie .

* **local.settings.json**: Hiermee worden instellingen onderhouden die worden gebruikt wanneer u functies lokaal uitvoert. Deze instellingen worden alleen gebruikt wanneer u functies lokaal uitvoert. Zie [Bestand Lokale instellingen](#local-settings-file)voor meer informatie .

    >[!IMPORTANT]
    >Omdat het bestand local.settings.json geheimen kan bevatten, moet u het uitsluiten van uw projectbronbeheer.

Afhankelijk van uw taal worden deze andere bestanden gemaakt:

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs klassebibliotheekbestand](functions-dotnet-class-library.md#functions-class-library-project) dat de functie implementeert.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

* Een package.json-bestand in de hoofdmap.

* Een Map HttpExample met het [function.json-definitiebestand](functions-reference-node.md#folder-structure) en het [bestand index.js](functions-reference-node.md#exporting-a-function), een Bestand Node.js dat de functiecode bevat.

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

Op dit punt u invoer- en uitvoerbindingen aan uw functie toevoegen door [het bestand function.json te wijzigen](#add-a-function-to-your-project) of door een parameter toe te voegen aan een [c#-klassebibliotheekfunctie](#add-a-function-to-your-project).

U ook [een nieuwe functie aan uw project toevoegen.](#add-a-function-to-your-project)

## <a name="install-binding-extensions"></a>Binding-extensies installeren

Behalve voor HTTP- en timertriggers worden bindingen geïmplementeerd in uitbreidingspakketten. U moet de extensiepakketten voor de triggers en bindingen installeren die ze nodig hebben. Het proces voor het installeren van bindende extensies is afhankelijk van de taal van uw project.

# <a name="c"></a>[C\#](#tab/csharp)

Voer de opdracht [dotnetadd package](/dotnet/core/tools/dotnet-add-package) uit in het terminalvenster om de uitbreidingspakketten te installeren die u in uw project nodig hebt. Met de volgende opdracht wordt de Azure Storage-extensie geïnstalleerd, die bindingen voor blob-, wachtrij- en tabelopslag implementeert.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[Javascript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Een functie toevoegen aan uw project

U een nieuwe functie toevoegen aan een bestaand project met behulp van een van de vooraf gedefinieerde triggersjablonen voor functies. Als u een nieuwe functietrigger wilt toevoegen, selecteert u F1 om het opdrachtpalet te openen en zoekt en voert u de opdracht **Azure-functies uit: Functie maken**. Volg de aanwijzingen om het triggertype te kiezen en definieer de vereiste kenmerken van de trigger. Als voor uw trigger een toegangssleutel of verbindingstekenreeks nodig is om verbinding te maken met een service, moet u deze klaarmaken voordat u de functietrigger maakt.

De resultaten van deze actie zijn afhankelijk van de taal van uw project:

# <a name="c"></a>[C\#](#tab/csharp)

Er wordt een nieuw C#-klassebibliotheekbestand (.cs) aan uw project toegevoegd.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

Er wordt een nieuwe map gemaakt in het project. De map bevat een nieuw function.json-bestand en het nieuwe JavaScript-codebestand.

---

## <a name="add-input-and-output-bindings"></a>Invoer- en uitvoerbindingen toevoegen

U uw functie uitbreiden door invoer- en uitvoerbindingen toe te voegen. Het proces voor het toevoegen van bindingen is afhankelijk van de taal van uw project. Zie Azure Functions triggers [and bindings concepts](functions-triggers-bindings.md)voor meer informatie over bindingen.

De volgende voorbeelden maken verbinding `outqueue`met een opslagwachtrij met de naam `MyStorageConnection` , waarbij de verbindingstekenreeks voor het opslagaccount is ingesteld in de toepassingsinstelling in local.settings.json.

# <a name="c"></a>[C\#](#tab/csharp)

Werk de functiemethode bij om `Run` de volgende parameter toe te voegen aan de methodedefinitie:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Voor deze code moet `using` u de volgende instructie toevoegen:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

De `msg` parameter `ICollector<T>` is een type dat een verzameling berichten vertegenwoordigt die naar een uitvoerbinding zijn geschreven wanneer de functie is voltooid. U voegt een of meer berichten toe aan de verzameling. Deze berichten worden naar de wachtrij verzonden wanneer de functie is voltooid.

Zie de [bindingsdocumentatie voor wachtrijopslaguitvoer](functions-bindings-storage-queue-output.md) voor meer informatie.

# <a name="javascript"></a>[Javascript](#tab/nodejs)

Met Visual Studio Code u bindingen toevoegen aan uw function.json-bestand door een handige set prompts te volgen. Als u een binding wilt maken, klikt u met de rechtermuisknop (Ctrl+klik op macOS) het **bestand function.json** in uw functiemap en selecteert u **Binding toevoegen:**

![Een binding toevoegen aan een bestaande JavaScript-functie ](media/functions-develop-vs-code/function-add-binding.png)

Hieronder volgen voorbeeldvragen om een nieuwe opslaguitvoerbinding te definiëren:

| Vraag | Waarde | Beschrijving |
| -------- | ----- | ----------- |
| **Bindingsrichting selecteren** | `out` | De binding is een outputbinding. |
| **Binding met richting selecteren** | `Azure Queue Storage` | De binding is een Azure Storage-wachtrijbinding. |
| **De naam die wordt gebruikt om deze binding in uw code te identificeren** | `msg` | Naam die de bindende parameter waarnaar in uw code wordt verwezen identificeert. |
| **De wachtrij waarnaar het bericht wordt verzonden** | `outqueue` | De naam van de wachtrij waar de binding naar schrijft. Wanneer de *queueName* niet bestaat, wordt deze met de binding bij het eerste gebruik gemaakt. |
| **Selecteer instelling in 'local.settings.json'** | `MyStorageConnection` | De naam van een toepassingsinstelling die de verbindingstekenreeks voor het opslagaccount bevat. De `AzureWebJobsStorage` instelling bevat de verbindingstekenreeks voor het opslagaccount dat u met de functie-app hebt gemaakt. |

In dit voorbeeld wordt de volgende `bindings` binding toegevoegd aan de array in het bestand function.json:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

U dezelfde bindingsdefinitie ook rechtstreeks aan uw function.json toevoegen.

In uw functiecode `msg` wordt de binding `context`geopend vanuit de , zoals in dit voorbeeld:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Zie de [bindingsverwijzing voor de opslaguitvoer wachtrij](functions-bindings-storage-queue-output.md) voor meer informatie.

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Publiceren naar Azure

Met Visual Studio Code u uw functieproject rechtstreeks naar Azure publiceren. In dit proces maakt u een functie-app en de bijbehorende resources in uw Azure-abonnement. De functie-app biedt een context waar u uw functies kunt uitvoeren. Het project wordt in uw Azure-abonnement verpakt en geïmplementeerd in de nieuwe functie-app.

Wanneer u publiceert van Visual Studio Code naar een nieuwe functie-app in Azure, krijgt u zowel een snel gekozen app-pad als een geavanceerd pad aangeboden. 

Wanneer u publiceert vanuit Visual Studio Code, maakt u gebruik van de [Zip-implementatietechnologie.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Snelle functie-app maken

Wanneer u **kiest voor + Nieuwe functie-app maken in Azure...**, genereert de extensie automatisch waarden voor de Azure-resources die uw functie-app nodig heeft. Deze waarden zijn gebaseerd op de naam van de functie-app die u kiest. Zie het [snelstartartikel](functions-create-first-function-vs-code.md#publish-the-project-to-azure)van Visual Studio Code voor een voorbeeld van het gebruik van standaardinstellingen om uw project te publiceren naar een nieuwe functie-app in Azure.

Als u expliciete namen wilt opgeven voor de gemaakte resources, moet u het geavanceerde pad voor maken kiezen.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Een project publiceren naar een nieuwe functie-app in Azure met behulp van geavanceerde opties

De volgende stappen publiceren uw project naar een nieuwe functie-app die is gemaakt met geavanceerde opties voor het maken:

1. Selecteer in het gebied **Azure: Functions** het pictogram **Implementeren naar functie-app.**

    ![Instellingen voor functie-app](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Als u niet bent aangemeld, wordt u gevraagd **u aan te melden bij Azure.** U ook **een gratis Azure-account maken.** Nadat u zich vanuit de browser hebt ingelogd, gaat u terug naar Visual Studio Code.

1. Als u meerdere abonnementen hebt, **selecteert u een abonnement** voor de functie-app en selecteert u + Nieuwe **functie-app maken in Azure... _Geavanceerd_**. Met _deze geavanceerde_ optie hebt u meer controle over de resources die u in Azure maakt. 

1. Geef de volgende aanwijzingen deze informatie op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Functie-app selecteren in Azure | Nieuwe functie-app maken in Azure | Typ bij de volgende prompt een wereldwijd unieke naam die uw nieuwe functie-app identificeert en selecteer Enter. Geldige tekens voor de naam van en functie-app zijn `a-z`, `0-9` en `-`. |
    | Een besturingssysteem selecteren | Windows | De functie-app wordt uitgevoerd op Windows. |
    | Een hostingplan selecteren | Verbruiksabonnement | Er wordt een serverloze [consumption-hosting](functions-scale.md#consumption-plan) gebruikt. |
    | Een runtime selecteren voor uw nieuwe app | Uw projecttaal | De runtime moet overeenkomen met het project dat u publiceert. |
    | Een resourcegroep selecteren voor nieuwe bronnen | Nieuwe resourcegroep maken | Typ bij de volgende prompt een `myResourceGroup`naam van een resourcegroep, zoals , en selecteer enter. U ook een bestaande resourcegroep selecteren. |
    | Een opslagaccount selecteren | Nieuw opslagaccount maken | Typ bij de volgende prompt een wereldwijd unieke naam voor het nieuwe opslagaccount dat door uw functie-app wordt gebruikt en selecteer Enter. De namen van het opslagaccount moeten tussen de 3 en 24 tekens lang zijn en kunnen alleen getallen en kleine letters bevatten. U ook een bestaand account selecteren. |
    | Een locatie selecteren voor nieuwe bronnen | regio | Selecteer een locatie in een [gebied](https://azure.microsoft.com/regions/) bij u in de buurt of in de buurt van andere services waartoe uw functies toegang hebben. |

    Er wordt een melding weergegeven nadat uw functie-app is gemaakt en het implementatiepakket is toegepast. Selecteer **Uitvoer weergeven** in deze melding om de resultaten van maken en implementeren weer te geven, inclusief de Azure-resources die u hebt gemaakt.

## <a name="republish-project-files"></a>Projectbestanden opnieuw publiceren

Wanneer u [continue implementatie](functions-continuous-deployment.md)instelt, wordt uw functie-app in Azure bijgewerkt wanneer bronbestanden worden bijgewerkt op de locatie van de verbonden bron. We raden continue implementatie aan, maar u uw projectbestandsupdates ook opnieuw publiceren vanuit Visual Studio Code.

> [!IMPORTANT]
> Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven.

1. Selecteer in Visual Studio Code F1 om het opdrachtpalet te openen. Zoek en selecteer Azure-functies in het **opdrachtpalet: Implementeren naar functie-app**.

1. Als u niet bent aangemeld, wordt u gevraagd **u aan te melden bij Azure.** Nadat u zich vanuit de browser hebt aangemeld, gaat u terug naar Visual Studio Code. Als u meerdere abonnementen hebt, **selecteert u een abonnement** dat uw functie-app bevat.

1. Selecteer uw bestaande functie-app in Azure. Wanneer u wordt gewaarschuwd voor het overschrijven van alle bestanden in de functie-app, selecteert u **Implementeren** om de waarschuwing te herkennen en door te gaan.

Het project wordt opnieuw opgebouwd, opnieuw verpakt en geüpload naar Azure. Het bestaande project wordt vervangen door het nieuwe pakket en de functie-app wordt opnieuw opgestart.

## <a name="get-the-url-of-the-deployed-function"></a>De URL van de geïmplementeerde functie ophalen

Als u een http-geactiveerde functie wilt aanroepen, hebt u de URL van de functie nodig wanneer deze wordt geïmplementeerd in uw functie-app. Deze URL bevat alle vereiste [functietoetsen.](functions-bindings-http-webhook-trigger.md#authorization-keys) U de extensie gebruiken om deze URL's te krijgen voor uw geïmplementeerde functies.

1. Selecteer F1 om het opdrachtpalet te openen en zoek en voer de opdracht **Azure Functions: Copy Function URL**uit.

1. Volg de aanwijzingen om uw functie-app in Azure te selecteren en vervolgens de specifieke HTTP-trigger die u wilt aanroepen.

De functie-URL wordt gekopieerd naar het klembord, samen `code` met de vereiste sleutels die door de queryparameter worden doorgegeven. Gebruik een HTTP-tool om POST-aanvragen of een browser voor GET-aanvragen in te dienen bij de externe functie.  

## <a name="run-functions-locally"></a>Functies lokaal uitvoeren

Met de extensie Azure Functions u een project Functies uitvoeren op uw lokale ontwikkelingscomputer. De lokale runtime is dezelfde runtime die uw functie-app in Azure host. Lokale instellingen worden gelezen in het [bestand local.settings.json](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Aanvullende vereisten voor het lokaal uitvoeren van een project

Als u uw functieproject lokaal wilt uitvoeren, moet u aan deze aanvullende vereisten voldoen:

* Installeer versie 2.x of hoger van [Azure Functions Core Tools](functions-run-local.md#v2). Het Core Tools-pakket wordt automatisch gedownload en geïnstalleerd wanneer u het project lokaal start. Core Tools bevat de volledige runtime van Azure-functies, dus downloaden en installeren kan enige tijd duren.

* Installeer de specifieke vereisten voor de taal van uw keuze:

    | Taal | Vereiste |
    | -------- | --------- |
    | **C #** | [C# extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[.NET Core CLI-hulpprogramma's](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Foutopsporing voor Java-extensie](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 of hoger](https://maven.apache.org/) |
    | **Javascript** | [Knooppunt.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[Python 3.6.8](https://www.python.org/downloads/) aanbevolen|

    <sup>*</sup>Actieve LTS- en OnderhoudsLTS-versies (aanbevolen 8.11.1 en 10.14.1).

### <a name="configure-the-project-to-run-locally"></a>Het project configureren om lokaal uit te voeren

De runtime Van De functies gebruikt een Azure Storage-account intern voor alle andere triggertypen dan HTTP en webhooks. U moet de sleutel **Values.AzureWebJobsStorage** dus instellen op een geldige tekenreeks voor azure-opslagaccountverbindingen.

In deze sectie wordt de [Azure Storage-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) met Azure Storage [Explorer](https://storageexplorer.com/) gebruikt om verbinding te maken met en de tekenreeks voor de opslagverbinding op te halen.

Ga als u de tekenreeks voor de verbinding met het opslagaccount in:

1. Open in Visual Studio **Cloud Explorer,** vouw **uw opslagaccount** > **uit**en selecteer **eigenschappen** en kopieer de waarde van de tekenreeks **primaire verbindingstekenreeks.**

2. Open in uw project het bestand local.settings.json en stel de waarde van de **AzureWebJobsStorage-sleutel** in op de verbindingstekenreeks die u hebt gekopieerd.

3. Herhaal de vorige stap om unieke sleutels toe te voegen aan de array **Waarden** voor alle andere verbindingen die door uw functies worden vereist.

Zie [Bestand Lokale instellingen](#local-settings-file)voor meer informatie .

### <a name="debugging-functions-locally"></a>Foutopsporing functies lokaal  

Als u uw functies wilt debuggen, selecteert u F5. Als u Core Tools [Core Tools][Azure Functions Core Tools]nog niet hebt gedownload, wordt u daarom gevraagd. Wanneer Core Tools is geïnstalleerd en uitgevoerd, wordt de uitvoer weergegeven in de terminal. Dit is hetzelfde `func host start` als het uitvoeren van de opdracht Core Tools vanuit de Terminal, maar met extra buildtaken en een gekoppelde foutopsporing.  

Wanneer het project wordt uitgevoerd, u uw functies activeren zoals u zou doen wanneer het project wordt geïmplementeerd in Azure. Wanneer het project wordt uitgevoerd in de foutopsporingsmodus, worden breekpunten zoals verwacht geraakt in Visual Studio Code.

De aanvraag-URL voor HTTP-triggers wordt weergegeven in de uitvoer in de terminal. Functietoetsen voor HTTP-triggers worden niet gebruikt wanneer een project lokaal wordt uitgevoerd. Zie [Strategieën voor het testen van uw code in Azure-functies voor](functions-test-a-function.md)meer informatie.  

Zie [Werken met Azure Functions Core Tools Azure]Functions Core[Tools]voor meer informatie.

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Standaard worden deze instellingen niet automatisch gemigreerd wanneer het project wordt gepubliceerd naar Azure. Nadat het publiceren is voltooid, krijgt u de mogelijkheid om instellingen te publiceren van local.settings.json naar uw functie-app in Azure. Zie [Toepassingsinstellingen publiceren](#publish-application-settings)voor meer informatie .

Waarden in **ConnectionStrings** worden nooit gepubliceerd.

De waarden van de functietoepassingsinstellingen kunnen ook in uw code als omgevingsvariabelen worden gelezen. Zie voor meer informatie de secties Omgevingsvariabelen van deze taalspecifieke referentieartikelen:

* [C# vooraf gecompileerd](functions-dotnet-class-library.md#environment-variables)
* [C#-script (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [Javascript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Toepassingsinstellingen in Azure

De instellingen in het bestand local.settings.json in uw project moeten dezelfde zijn als de toepassingsinstellingen in de functie-app in Azure. Alle instellingen die u aan local.settings.json toevoegt, moeten ook worden toegevoegd aan de functie-app in Azure. Deze instellingen worden niet automatisch geüpload wanneer u het project publiceert. Ook alle instellingen die u in uw functie-app [in de portal](functions-how-to-use-azure-function-app-settings.md#settings) maakt, moeten worden gedownload naar uw lokale project.

### <a name="publish-application-settings"></a>Toepassingsinstellingen publiceren

De eenvoudigste manier om de vereiste instellingen naar uw functie-app in Azure te publiceren, is door de koppeling **Uploadinstellingen** te gebruiken die wordt weergegeven nadat u uw project hebt gepubliceerd:

![Toepassingsinstellingen uploaden](./media/functions-develop-vs-code/upload-app-settings.png)

U ook instellingen publiceren met de opdracht **Azure-functies: Lokale instelling uploaden** in het opdrachtpalet. U afzonderlijke instellingen toevoegen aan toepassingsinstellingen in Azure met behulp van de opdracht **Azure-functies: Nieuwe instelling toevoegen.**

> [!TIP]
> Zorg ervoor dat u uw bestand local.settings.json opslaat voordat u het publiceert.

Als het lokale bestand is versleuteld, wordt het opnieuw gedecodeerd, gepubliceerd en versleuteld. Als er instellingen zijn met conflicterende waarden op de twee locaties, wordt u gevraagd te kiezen hoe u verder gaat.

Bestaande app-instellingen weergeven in het **azure: het** gebied Functies door uw abonnement, uw functie-app en **toepassingsinstellingen**uit te breiden.

![Functie-app-instellingen weergeven in Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Downloadinstellingen van Azure

Als u toepassingsinstellingen in Azure hebt gemaakt, u deze downloaden naar uw bestand local.settings.json met behulp van de opdracht **Azure-functies: Externe instellingen downloaden.**

Net als bij het uploaden, als het lokale bestand is versleuteld, wordt het opnieuw gedecodeerd, bijgewerkt en versleuteld. Als er instellingen zijn met conflicterende waarden op de twee locaties, wordt u gevraagd te kiezen hoe u verder gaat.

## <a name="monitoring-functions"></a>Bewakingsfuncties

Wanneer u [functies lokaal uitvoert, worden](#run-functions-locally)logboekgegevens gestreamd naar de Terminal-console. U ook logboekgegevens opvragen wanneer uw project Functions wordt uitgevoerd in een functie-app in Azure. U verbinding maken met streaminglogboeken in Azure om bijna realtime logboekgegevens te bekijken, of u Application Insights inschakelen voor een vollediger inzicht in hoe uw functie-app zich gedraagt.

### <a name="streaming-logs"></a>Logboeken streamen

Wanneer u een toepassing ontwikkelt, is het vaak handig om logboekinformatie in bijna realtime te zien. U een stroom van logboekbestanden bekijken die door uw functies worden gegenereerd. Deze uitvoer is een voorbeeld van streaminglogboeken voor een aanvraag naar een http-geactiveerde functie:

![Streaming logs output voor HTTP trigger](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Zie [Logboeken streamen](functions-monitoring.md#streaming-logs)voor meer informatie.

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> Streaminglogboeken ondersteunen slechts één exemplaar van de functiehost. Wanneer uw functie wordt geschaald naar meerdere exemplaren, worden gegevens uit andere instanties niet weergegeven in de logboekstroom. [Live Metrics Stream](../azure-monitor/app/live-stream.md) in Application Insights ondersteunt meerdere exemplaren. Hoewel ook in bijna realtime, streaming analytics is gebaseerd op [gesamplede gegevens](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

We raden u aan de uitvoering van uw functies te controleren door uw functie-app te integreren met Application Insights. Wanneer u een functie-app maakt in de Azure-portal, vindt deze integratie standaard plaats. Wanneer u uw functie-app maakt tijdens het publiceren van Visual Studio, moet u Application Insights zelf integreren.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Zie [Azure-functies controleren](functions-monitoring.md)voor meer informatie.

## <a name="c-script-projects"></a>C-scriptprojecten\#

Standaard worden alle C#-projecten gemaakt als [C# gecompileerde klassenbibliotheekprojecten](functions-dotnet-class-library.md). Als u liever met C#-scriptprojecten werkt, moet u C#-script selecteren als standaardtaal in de extensie-instellingen voor Azure Functions:

1. Selecteer**Instellingen** **voor bestandsvoorkeuren** > **Preferences** > .

1. Ga naar**Azure-functies**voor > **gebruikersinstellingen-extensies** > . **User Settings**

1. **C#Script selecteren** in **azure-functie: projecttaal**.

Nadat u deze stappen hebt voltooid, bevatten `--csx` oproepen naar de onderliggende Core Tools de optie, die projectbestanden van C# script (.csx) genereert en publiceert. Wanneer u deze standaardtaal hebt opgegeven, worden alle projecten die u standaard maakt voor C#-scriptprojecten. U wordt niet gevraagd een projecttaal te kiezen wanneer een standaardinstelling is ingesteld. Als u projecten in andere talen wilt maken, moet u deze instelling wijzigen of verwijderen uit het bestand user settings.json. Nadat u deze instelling hebt verwijderd, wordt u opnieuw gevraagd uw taal te kiezen wanneer u een project maakt.

## <a name="command-palette-reference"></a>Naslagverwijzing opdrachtpalet

De azure-functie-extensie biedt een handige grafische interface in het gebied voor interactie met uw functie-apps in Azure. Dezelfde functionaliteit is ook beschikbaar als opdrachten in het opdrachtpalet (F1). Deze opdrachten voor Azure-functies zijn beschikbaar:

|Azure-functies, opdracht  | Beschrijving  |
|---------|---------|
|**Nieuwe instellingen toevoegen**  |  Hiermee maakt u een nieuwe toepassingsinstelling in Azure. Zie [Toepassingsinstellingen publiceren](#publish-application-settings)voor meer informatie . Mogelijk moet u deze instelling ook [downloaden naar uw lokale instellingen.](#download-settings-from-azure) |
| **Implementatiebron configureren** | Verbindt uw functie-app in Azure met een lokale Git-opslagplaats. Zie [Continue implementatie voor Azure-functies voor](functions-continuous-deployment.md)meer informatie. |
| **Verbinding maken met GitHub Repository** | Verbindt uw functie-app met een GitHub-repository. |
| **URL van de functie kopiëren** | Hier krijgt u de externe URL van een http-geactiveerde functie die wordt uitgevoerd in Azure. Zie [De URL van de geïmplementeerde functie ophalen](#get-the-url-of-the-deployed-function)voor meer informatie. |
| **Functie-app maken in Azure** | Hiermee maakt u een nieuwe functie-app in uw abonnement in Azure. Zie voor meer informatie de sectie over het [publiceren naar een nieuwe functie-app in Azure](#publish-to-azure).        |
| **Instellingen decoderen** | Decodeert [lokale instellingen](#local-settings-file) die zijn versleuteld door **Azure-functies: Instellingen versleutelen**.  |
| **Functie-app verwijderen** | Hiermee verwijdert u een functie-app uit uw abonnement in Azure. Als er geen andere apps in het App-service-abonnement staan, krijgt u de optie om dat ook te verwijderen. Andere bronnen, zoals opslagaccounts en resourcegroepen, worden niet verwijderd. Als u alle resources wilt verwijderen, moet u in plaats daarvan [de brongroep verwijderen.](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources) Uw lokale project wordt niet beïnvloed. |
|**Verwijderen, functie**  | Hiermee verwijdert u een bestaande functie uit een functie-app in Azure. Omdat deze verwijdering geen invloed heeft op uw lokale project, u in plaats daarvan overwegen de functie lokaal te verwijderen en [uw project opnieuw te publiceren.](#republish-project-files) |
| **Proxy verwijderen** | Hiermee verwijdert u een azure-functieproxy uit uw functie-app in Azure. Zie [Werken met Azure Functions Proxies](functions-proxies.md)voor meer informatie over proxy's. |
| **Instelling verwijderen** | Hiermee verwijdert u een functie-app-instelling in Azure. Deze verwijdering heeft geen invloed op de instellingen in het bestand local.settings.json. |
| **Verbinding verbreken met Repo**  | Hiermee verwijdert u de [continue implementatieverbinding](functions-continuous-deployment.md) tussen een functie-app in Azure en een bronbeheeropslagplaats. |
| **Externe instellingen downloaden** | Downloadt de instellingen van de gekozen functie-app in Azure naar het bestand local.settings.json. Als het lokale bestand is versleuteld, wordt het opnieuw gedecodeerd, bijgewerkt en versleuteld. Als er instellingen zijn met conflicterende waarden op de twee locaties, wordt u gevraagd te kiezen hoe u verder gaat. Zorg ervoor dat u wijzigingen opslaat in uw bestand local.settings.json voordat u deze opdracht uitvoert. |
| **Instellingen bewerken** | Hiermee wijzigt u de waarde van een bestaande functie-app-instelling in Azure. Deze opdracht heeft geen invloed op de instellingen in het bestand local.settings.json.  |
| **Instellingen versleutelen** | Versleutelt afzonderlijke `Values` items in de array in de [lokale instellingen.](#local-settings-file) In dit `IsEncrypted` bestand is `true`ook ingesteld op , die aangeeft dat de lokale runtime instellingen zal decoderen voordat u ze gebruikt. Versleutel lokale instellingen om het risico op het lekken van waardevolle informatie te verminderen. In Azure worden toepassingsinstellingen altijd versleuteld opgeslagen. |
| **Functie nu uitvoeren** | Hiermee wordt handmatig een [functie gestart die door een timer wordt geactiveerd](functions-bindings-timer.md) in Azure. Deze opdracht wordt gebruikt voor het testen. Zie [Een niet-geactiveerde functie](functions-manually-run-non-http.md)handmatig uitvoeren voor meer informatie over het activeren van niet-HTTP-functies in Azure. |
| **Project voor gebruik initialiseren met VS-code** | Hiermee voegt u de vereiste Visual Studio Code-projectbestanden toe aan een bestaand project Functies. Gebruik deze opdracht om te werken met een project dat u hebt gemaakt met Behulp van Core Tools. |
| **Azure Functions Core-hulpprogramma's installeren of bijwerken** | Installeert of werkt [Azure Functions Core Tools], die wordt gebruikt om functies lokaal uit te voeren. |
| **Opnieuw implementeren**  | Hiermee u projectbestanden opnieuw implementeren vanuit een verbonden Git-opslagplaats naar een specifieke implementatie in Azure. Als u lokale updates van Visual Studio Code opnieuw wilt publiceren, [publiceert u uw project opnieuw.](#republish-project-files) |
| **Naamvan instellingen wijzigen** | Hiermee wijzigt u de sleutelnaam van een bestaande functie-app-instelling in Azure. Deze opdracht heeft geen invloed op de instellingen in het bestand local.settings.json. Nadat u de naam van de instellingen in Azure hebt gewijzigd, moet u [deze wijzigingen downloaden in het lokale project.](#download-settings-from-azure) |
| **Opnieuw starten** | Start de functie-app opnieuw in Azure. Het implementeren van updates start ook de functie-app opnieuw op. |
| **AzureWebJobsStorage instellen**| Hiermee stelt u `AzureWebJobsStorage` de waarde in van de toepassingsinstelling. Deze instelling is vereist door Azure Functions. Deze is ingesteld wanneer een functie-app wordt gemaakt in Azure. |
| **Begin** | Hiermee start u een gestopte functie-app in Azure. |
| **Streaminglogboeken starten** | Hiermee worden de streaminglogboeken voor de functie-app in Azure gestart. Gebruik streaminglogboeken tijdens het oplossen van problemen op afstand in Azure als u logboekregistratiegegevens in bijna realtime wilt zien. Zie [Logboeken streamen](#streaming-logs)voor meer informatie. |
| **Stoppen** | Hiermee wordt een functie-app gestopt die wordt uitgevoerd in Azure. |
| **Streaminglogboeken stoppen** | Hiermee worden de streaminglogboeken voor de functie-app in Azure gestopt. |
| **Schakelen als sleufinstelling** | Wanneer ingeschakeld, zorgt u ervoor dat een toepassingsinstelling blijft bestaan voor een bepaalde implementatiesleuf. |
| **Azure Functions Core Tools verwijderen** | Hiermee verwijdert u Azure Functions Core Tools, waarvoor de extensie dit vereist. |
| **Lokale instellingen uploaden** | Uploadt instellingen van uw bestand local.settings.json naar de gekozen functie-app in Azure. Als het lokale bestand is versleuteld, wordt het opnieuw gedecodeerd, geüpload en versleuteld. Als er instellingen zijn met conflicterende waarden op de twee locaties, wordt u gevraagd te kiezen hoe u verder gaat. Zorg ervoor dat u wijzigingen opslaat in uw bestand local.settings.json voordat u deze opdracht uitvoert. |
| **Bekijk Commit in GitHub** | Toont u de nieuwste commit in een specifieke implementatie wanneer uw functie-app is verbonden met een repository. |
| **Implementatielogboeken weergeven** | Toont u de logboeken voor een specifieke implementatie naar de functie-app in Azure. |

## <a name="next-steps"></a>Volgende stappen

Zie Werken met Azure Functions [Core Tools](functions-run-local.md)voor meer informatie over Azure Functions Core Tools.

Zie [Naslaginformatie over Azure Functions C#developer](functions-dotnet-class-library.md)voor meer informatie over het ontwikkelen van functies als .NET-klassenbibliotheken. In dit artikel worden ook koppelingen weergegeven naar voorbeelden van het gebruik van kenmerken om de verschillende soorten bindingen te declareren die worden ondersteund door Azure-functies.

[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions (Azure Functions-extensie voor Visual Studio Code)
[Core-hulpprogramma's voor Azure-functies]: functions-run-local.md
