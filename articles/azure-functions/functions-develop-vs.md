---
title: Azure Functions ontwikkelen met Visual Studio
description: Meer informatie over het ontwikkelen en testen van Azure Functions met behulp van Azure Functions-Hulpprogram Ma's voor Visual Studio 2019.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: b3d683153a1e70f4c65dcc5e401f4ee702af8b49
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277100"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Azure Functions ontwikkelen met Visual Studio  

Met Visual Studio kunt u klassen bibliotheek functies ontwikkelen, C# testen en implementeren in Azure. Als deze ervaring uw eerste met Azure Functions is, kunt u meer te weten komen over [een inleiding tot Azure functions](functions-overview.md).

Visual Studio biedt de volgende voor delen bij het ontwikkelen van uw functies: 

* U kunt functies bewerken, bouwen en uitvoeren op uw lokale ontwikkel computer. 
* Publiceer uw Azure Functions-project rechtstreeks naar Azure en maak indien nodig Azure-resources. 
* Gebruik C# kenmerken om functie bindingen rechtstreeks in de C# code te declareren.
* Ontwikkel en implementeer vooraf gecompileerde C# functies. De vooraf vervulde functies bieden een betere koude start dan C# op scripts gebaseerde functies. 
* Codeer uw functies C# in terwijl u profiteert van de voor delen van Visual Studio Development. 

In dit artikel vindt u informatie over het gebruik van Visual Studio C# voor het ontwikkelen van klassen bibliotheek functies en het publiceren ervan naar Azure. Voordat u dit artikel leest, moet u de [functies Snelstartgids voor Visual Studio](functions-create-your-first-function-visual-studio.md)volt ooien. 

Tenzij anders aangegeven, worden de procedures en voor beelden weer gegeven voor Visual Studio 2019. 

## <a name="prerequisites"></a>Vereisten

Azure Functions-Hulpprogram Ma's zijn opgenomen in de Azure Development-werk belasting van Visual Studio, te beginnen met Visual Studio 2017. Zorg ervoor dat u de werk belasting van **Azure Development** opneemt in uw Visual Studio-installatie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Andere resources die u nodig hebt, zoals een Azure Storage account, worden tijdens het publicatie proces in uw abonnement gemaakt.

> [!NOTE]
> In Visual Studio 2017 installeert de werk belasting Azure Development de Azure Functions-Hulpprogram Ma's als een afzonderlijke extensie. Wanneer u uw Visual Studio 2017 bijwerkt, moet u er ook voor zorgen dat u de [meest recente versie](#check-your-tools-version) van de Azure functions-hulpprogram ma's gebruikt. In de volgende secties ziet u hoe u de extensie van de Azure Functions-Hulpprogram Ma's in Visual Studio 2017 kunt controleren en (indien nodig) bijwerken. 
>
> U kunt deze sectie overs Laan wanneer u Visual Studio 2019 gebruikt.

### <a name="check-your-tools-version"></a>Controleer de versie van uw hulpprogram ma's in Visual Studio 2017

1. Kies in het menu **extra** de optie **extensies en updates**. Vouw **geïnstalleerde** > - **hulpprogram ma's** uit en kies **Azure functions en hulpprogram ma's voor webjobs**.

    ![De versie van de functie hulpprogramma's controleren](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Noteer de geïnstalleerde **versie**. U kunt deze versie vergelijken met de meest recente versie die wordt vermeld [in de release opmerkingen](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md). 

1. Als uw versie ouder is, werkt u uw hulpprogramma's in Visual Studio zoals wordt weergegeven in de volgende sectie.

### <a name="update-your-tools-in-visual-studio-2017"></a>Werk uw hulpprogram ma's bij in Visual Studio 2017

1. Vouw in het dialoog venster **extensies en updates** het onderdeel **updates** > **Visual Studio Marketplace**uit, kies **Azure functions en Hulpprogram ma's voor webjobs** en selecteer **bijwerken**.

    ![De functie hulpprogram ma's-versie bijwerken](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Nadat de hulpprogramma's-update is gedownload, sluit u Visual Studio aan de hulpprogramma's bijwerken met behulp van het installatieprogramma VSIX-trigger.

1. Klik in het installatie programma op **OK** om te starten en **Wijzig** vervolgens de hulpprogram ma's. 

1. Nadat de update is voltooid, kiest u **sluiten** en Start Visual Studio opnieuw.

> [!NOTE]  
> In Visual Studio 2019 en hoger wordt de uitbrei ding Azure Functions tools bijgewerkt als onderdeel van Visual Studio.  

## <a name="create-an-azure-functions-project"></a>Een Azure Functions-project maken

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

De project sjabloon maakt een C# project, installeert het `Microsoft.NET.Sdk.Functions` NuGet-pakket en stelt het doel raamwerk in. Het nieuwe project heeft de volgende bestanden:

* **host. json**: Hiermee kunt u de host voor functies configureren. Deze instellingen zijn van toepassing op zowel lokaal als in Azure. Zie voor meer informatie [host. json Reference](functions-host-json.md).

* **Local. settings. json**: onderhoudt de instellingen die worden gebruikt bij het lokaal uitvoeren van functies. Deze instellingen worden niet gebruikt wanneer ze worden uitgevoerd in Azure. Zie [Local Settings file](#local-settings-file)(Engelstalig) voor meer informatie.

    >[!IMPORTANT]
    >Omdat het bestand local. settings. json geheimen kan bevatten, moet u dit uitsluiten van uw project broncode beheer. De instelling **kopiëren naar uitvoer Directory** voor dit bestand moet altijd **als nieuwe kopie worden gekopieerd**. 

Zie voor meer informatie [functions Class Library project](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Instellingen in Local. settings. json worden niet automatisch geüpload wanneer u het project publiceert. Om ervoor te zorgen dat deze instellingen ook in uw functie-app in azure aanwezig zijn, moet u deze uploaden nadat u uw project hebt gepubliceerd. Zie functie-app- [instellingen](#function-app-settings)voor meer informatie.

Waarden in **Connections Tring** worden nooit gepubliceerd.

De waarden van de functie-app-instellingen kunnen ook in uw code worden gelezen als omgevings variabelen. Zie [omgevings variabelen](functions-dotnet-class-library.md#environment-variables)voor meer informatie.

## <a name="configure-the-project-for-local-development"></a>Het project voor lokale ontwikkeling configureren

De functions runtime maakt intern gebruik van een Azure Storage-account. Voor alle trigger typen behalve HTTP en webhooks moet u de **waarden. AzureWebJobsStorage** -sleutel instellen op een geldig Azure Storage-account Connection String. De functie-app kan ook de [Azure-opslag emulator](../storage/common/storage-use-emulator.md) gebruiken voor de **AzureWebJobsStorage** -verbindings instelling die door het project wordt vereist. Als u de emulator wilt gebruiken, stelt u de waarde van **AzureWebJobsStorage** in op `UseDevelopmentStorage=true`. Wijzig deze instelling in een werkelijk opslag account connection string vóór de implementatie.

Het opslag account connection string instellen:

1. Open in Visual Studio **Cloud Explorer**, vouw het **opslag account** uit ** > uw opslag account**en kopieer vervolgens op het tabblad **Eigenschappen** de waarde van de **primaire verbindings reeks** .

2. Open in uw project het bestand local. settings. json en stel de waarde van de sleutel **AzureWebJobsStorage** in op de Connection String die u hebt gekopieerd.

3. Herhaal de vorige stap om unieke sleutels toe te voegen aan de matrix **waarden** voor andere verbindingen die uw functies vereisen. 

## <a name="add-a-function-to-your-project"></a>Een functie toevoegen aan uw project

In C# Class Library-functies worden de bindingen die worden gebruikt door de functie gedefinieerd door het Toep assen van kenmerken in de code. Wanneer u de functie triggers maakt op basis van de beschik bare sjablonen, worden de trigger kenmerken voor u toegepast. 

1. Klik in **Solution Explorer** met de rechtermuisknop op het projectknooppunt en selecteerNieuw item > **Toevoegen**. Selecteer **Azure function**, typ een **naam** voor de klasse en klik op **toevoegen**.

2. Kies uw trigger, stel de bindings eigenschappen in en klik op **maken**. In het volgende voor beeld ziet u de instellingen bij het maken van een door de wachtrij opslag geactiveerde functie. 

    ![Een door een wachtrij geactiveerde functie maken](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    In deze trigger wordt een connection string gebruikt met een sleutel met de naam **QueueStorage**. Deze connection string instelling moet worden gedefinieerd in het [bestand local. settings. json](functions-run-local.md#local-settings-file).

3. Bekijk de zojuist toegevoegde klasse. U ziet een statische methode **Run** , die wordt voorzien van het kenmerk **functie naam** . Dit kenmerk geeft aan dat de methode het toegangs punt voor de functie is.

    De volgende C# klasse vertegenwoordigt bijvoorbeeld een getriggerde Basic-wachtrij opslag:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

    Er wordt een binding-specifiek kenmerk toegepast op elke bindings parameter die is opgegeven voor de toegangs punt methode. Het kenmerk neemt de bindings gegevens op als para meters. In het vorige voor beeld heeft de eerste para meter een **Queue trigger** kenmerk toegepast, waarmee wordt aangegeven dat de door de wachtrij geactiveerde functie is. De naam van de wachtrij en de connection string instelling worden door gegeven als para meters aan het kenmerk **Queue trigger** . Zie [Azure Queue-opslag bindingen voor Azure functions](functions-bindings-storage-queue-trigger.md)voor meer informatie.

U kunt de bovenstaande procedure gebruiken om meer functies aan uw functie-app-project toe te voegen. Elke functie in het project kan een andere trigger hebben, maar een functie moet precies één trigger hebben. Zie [Azure functions triggers en bindingen concepten](functions-triggers-bindings.md)voor meer informatie.

## <a name="add-bindings"></a>Bindingen toevoegen

Net als bij triggers worden de invoer-en uitvoer bindingen aan uw functie toegevoegd als bindings kenmerken. Voeg als volgt bindingen toe aan een functie:

1. Zorg ervoor dat u [het project hebt geconfigureerd voor lokale ontwikkeling](#configure-the-project-for-local-development).

2. Voeg het juiste NuGet-extensie pakket toe voor de specifieke binding. Zie voor meer informatie [lokale C# ontwikkeling met Visual Studio](./functions-bindings-register.md#local-csharp) in het artikel triggers en bindingen. De binding-specifieke NuGet-pakket vereisten vindt u in het referentie artikel voor de binding. U kunt bijvoorbeeld pakket vereisten voor de trigger Event Hubs vinden in het [artikel over het event hubs binden van bindingen](functions-bindings-event-hubs.md).

3. Als er app-instellingen zijn die aan de binding moeten worden toegevoegd, voegt u deze toe aan de verzameling **waarden** in het [lokale instellings bestand](functions-run-local.md#local-settings-file). Deze waarden worden gebruikt wanneer de functie lokaal wordt uitgevoerd. Wanneer de functie wordt uitgevoerd in de functie-app in azure, worden de instellingen van de [functie-app](#function-app-settings) gebruikt.

4. Voeg het juiste bindings kenmerk toe aan de hand tekening van de methode. In het volgende voor beeld wordt met een wachtrij bericht de functie geactiveerd en wordt met de uitvoer binding een nieuw wachtrij bericht gemaakt met dezelfde tekst in een andere wachtrij.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   De verbinding met de wachtrij opslag wordt opgehaald uit de `AzureWebJobsStorage` instelling. Zie het naslag artikel voor de specifieke binding voor meer informatie. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>Functies testen

Met Azure Functions Core-hulpprogramma's kunt u Azure Functions-projecten uitvoeren op uw lokale ontwikkelcomputer. De eerste keer dat u een functie vanuit Visual Studio start, wordt u gevraagd deze hulpprogramma's te installeren.

Druk op F5 om de functie testen. Accepteer desgevraagd de aanvraag van Visual Studio om Azure Functions Core (CLI)-hulpprogramma's te downloaden en installeren. Mogelijk moet u ook een firewall-uitzondering inschakelen, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen.

Wanneer het project wordt uitgevoerd, kunt u uw code testen zoals u de geïmplementeerde functie zou testen. Zie [strategieën voor het testen van uw code in azure functions](functions-test-a-function.md)voor meer informatie. Bij het uitvoeren van de foutopsporingsmodus worden onderbrekings punten in Visual Studio op de verwachte wijze bereikt. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Zie [Azure functions lokaal coderen en testen](functions-run-local.md)voor meer informatie over het gebruik van de Azure functions core tools.

## <a name="publish-to-azure"></a>Publiceren naar Azure

Bij het publiceren vanuit Visual Studio wordt een van de volgende twee implementatie methoden gebruikt:

* [Web Deploy](functions-deployment-technologies.md#web-deploy-msdeploy): pakketten en implementeert Windows-apps op elke IIS-server.
* [Zip-implementatie met uitvoeren vanaf pakket ingeschakeld](functions-deployment-technologies.md#zip-deploy): aanbevolen voor Azure functions implementaties.

Gebruik de volgende stappen om uw project te publiceren naar een functie-app in Azure.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>Instellingen voor functie-app

De instellingen die u hebt toegevoegd in het bestand local. settings. json moeten ook worden toegevoegd aan de functie-app in Azure. Deze instellingen worden niet automatisch geüpload wanneer u het project publiceert.

De eenvoudigste manier om de vereiste instellingen te uploaden naar uw functie-app in Azure is door de koppeling **Toepassings instellingen beheren...** te gebruiken die wordt weer gegeven nadat u uw project hebt gepubliceerd.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Hiermee wordt het dialoog venster **Toepassings instellingen** weer gegeven voor de functie-app, waar u nieuwe toepassings instellingen kunt toevoegen of bestaande wijzigen.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** vertegenwoordigt een instellings waarde in het bestand local. settings. json, en **Remote** is de huidige instelling in de functie-app in Azure.  Kies **instelling toevoegen** om een nieuwe app-instelling te maken. Gebruik de **waarde invoegen uit lokale** koppeling om een instellings waarde naar het **externe** veld te kopiëren. Wijzigingen in behandeling worden naar het lokale instellingen bestand en de functie-app geschreven wanneer u **OK**selecteert.

> [!NOTE]
> Standaard is het bestand local. settings. json niet ingecheckt in broncode beheer. Dit betekent dat wanneer u een project van een lokale functie kloont van broncode beheer, het project geen lokaal. settings. JSON-bestand heeft. In dit geval moet u het bestand local. settings. json hand matig maken in de hoofdmap van het project, zodat het dialoog venster **Toepassings instellingen** werkt zoals verwacht. 

U kunt ook toepassings instellingen op een van de volgende manieren beheren:

* [Het Azure Portal gebruiken](functions-how-to-use-azure-function-app-settings.md#settings).
* [Met de optie `--publish-local-settings` publiceren in de Azure functions core tools](functions-run-local.md#publish).
* [De Azure CLI gebruiken](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>Bewakings functies

De aanbevolen manier om de uitvoering van uw functies te controleren is door uw functie-app te integreren met Azure-toepassing Insights. Wanneer u een functie-app maakt in de Azure Portal, wordt deze integratie standaard voor u uitgevoerd. Wanneer u echter uw functie-app tijdens het publiceren van Visual Studio maakt, wordt de integratie in uw functie-app in azure niet uitgevoerd.

Application Insights voor de functie-app inschakelen:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Zie [Azure functions bewaken](functions-monitoring.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure functions lokaal coderen en testen](functions-run-local.md)voor meer informatie over de Azure functions core tools.

Zie [Azure functions C# Naslag informatie voor ontwikkel aars](functions-dotnet-class-library.md)voor meer informatie over het ontwikkelen van functies als .net-klassen bibliotheken. In dit artikel vindt u ook koppelingen naar voor beelden van het gebruik van kenmerken voor het declareren van de verschillende typen bindingen die door Azure Functions worden ondersteund.    
