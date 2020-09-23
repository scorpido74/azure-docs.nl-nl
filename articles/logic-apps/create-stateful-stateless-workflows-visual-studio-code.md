---
title: Automation-werk stromen (preview) maken in Visual Studio code
description: Maak stateless of stateful Automation-werk stromen met de extensie Azure Logic Apps (preview) in Visual Studio code om apps, gegevens, Cloud Services en on-premises systemen te integreren
services: logic-apps
ms.suite: integration
ms.reviewer: deli, rohitha, vikanand, hongzili, sopai, absaafan, logicappspm
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 94d970390f62107a82dc586605d34dd61cae0c26
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995007"
---
# <a name="create-stateful-or-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Stateful of stateless werk stromen maken in Visual Studio code met de extensie Azure Logic Apps (preview)

> [!IMPORTANT]
> Deze mogelijkheid is beschikbaar als open bare preview, wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Als u logische app-werk stromen wilt maken die kunnen worden geïntegreerd in apps, gegevens, Cloud Services en systemen, kunt u Visual Studio code en de uitbrei ding Azure Logic Apps (preview) gebruiken om [ *stateful* en *stateless* logische app-werk stromen](#stateful-stateless)te bouwen en uit te voeren.

![Scherm opname van Visual Studio code en Logic app workflow.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

De Logic apps die u met de open bare preview-extensie maakt, maken gebruik van het resource type voor de nieuwe **logische app (preview)** en worden gestroomd door de [Azure functions](../azure-functions/functions-overview.md) runtime. Dit nieuwe resource type kan meerdere werk stromen bevatten en is op sommige manieren gelijk aan het resource type **functie-app** , dat meerdere functies kan bevatten.

Ondertussen bestaat het bron type oorspronkelijk **Logic apps** nog steeds voor u om te maken en te gebruiken in Visual Studio code en in de Azure Portal. De ervaringen voor het oorspronkelijke resource type zijn echter gescheiden van het nieuwe bron type. Op dit moment kunnen zowel de resource typen **Logic apps** als de **logische app (preview)** tegelijkertijd in Visual Studio Code en in de Azure Portal aanwezig zijn. U kunt alle geïmplementeerde Logic apps in uw Azure-abonnement weer geven en openen, maar ze worden weer gegeven en worden afzonderlijk in hun eigen categorieën en secties bewaard.

In dit artikel vindt u een overzicht op hoog niveau [van deze open bare preview](#whats-new), worden verschillende aspecten van het resource type **Logic app (preview)** beschreven en wordt uitgelegd hoe u deze resource maakt met behulp van Visual Studio code:

* Hoe [stateful en stateless](#stateful-stateless) Logic apps van elkaar verschillen.

* Hoe u aan de [installatie vereisten](#prerequisites) voldoet en [Visual Studio code instelt](#set-up) voor de open bare preview-extensie.

* Nieuwe werk stromen voor **logische apps (preview)** maken door [een project te maken en een werk stroom sjabloon te selecteren](#create-project).

* Deze nieuwe logische apps rechtstreeks vanuit Visual Studio code publiceren [naar Azure](#publish-azure) of [naar een docker-container](#deploy-docker) die u overal kunt uitvoeren. Zie [Wat is docker](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)? voor meer informatie over docker.

<a name="whats-new"></a>

## <a name="whats-in-this-public-preview"></a>Wat is er in deze open bare preview?

De uitbrei ding Azure Logic Apps (preview) biedt veel actuele en aanvullende Logic Apps mogelijkheden voor uw lokale ontwikkel ervaring in Visual Studio code, bijvoorbeeld:

* Bouw Logic apps voor integratie-en automatiserings werk stromen vanaf [300 + connectors](/connectors/connector-reference/connector-reference-logicapps-connectors) voor SaaS-apps (Software-as-a-Service) en PaaS (platform-as-a-Service) en services plus connectors voor on-premises systemen.

  * Sommige beheerde connectors, zoals Azure Service Bus, Azure Event Hubs en SQL Server, worden op dezelfde manier uitgevoerd als ingebouwde systeem eigen triggers en acties zoals de HTTP-actie.

  * Maak en implementeer logische apps die overal kunnen worden uitgevoerd, omdat de Azure Logic Apps service de verbindings reeksen voor Shared Access Signature (SAS) genereert die door deze Logic apps kunnen worden gebruikt voor het verzenden van aanvragen naar het runtime-eind punt voor de Cloud verbinding. De Logic Apps-service slaat deze verbindings reeksen op met andere toepassings instellingen, zodat u deze waarden eenvoudig kunt opslaan in Azure Key Vault wanneer u naar Azure implementeert.

    > [!NOTE]
    > Standaard heeft een resource- **app (preview)** de door het [systeem toegewezen identiteit](../logic-apps/create-managed-service-identity.md) automatisch ingeschakeld voor het verifiëren van verbindingen tijdens runtime. Deze identiteit wijkt af van de verificatie referenties of connection string die u gebruikt bij het maken van een verbinding. Als u deze identiteit uitschakelt, werken verbindingen niet tijdens runtime.

* Maak stateless Logic-apps die alleen in het geheugen worden uitgevoerd, zodat ze sneller zijn voltooid, snellere reageren, een hogere door voer hebben en de kost prijs minder zijn om te worden uitgevoerd omdat de run-runs en gegevens tussen acties niet behouden blijven in de externe opslag. Optioneel kunt u de uitvoerings geschiedenis inschakelen voor eenvoudiger fout opsporing. Zie [stateful versus stateless Logic apps](#stateful-stateless)voor meer informatie.

* Test uw logische apps lokaal in de Visual Studio code Development Environment.

* Publiceer en implementeer uw Logic apps rechtstreeks vanuit Visual Studio code naar verschillende hosting omgevingen, zoals [Azure app service](../app-service/environment/intro.md) en [docker-containers](/dotnet/core/docker/introduction).

> [!NOTE]
> Raadpleeg de [pagina met bekende problemen](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)met de uitbrei ding github voor meer informatie over bekende problemen.

<a name="stateful-stateless"></a>

## <a name="stateful-versus-stateless-logic-apps"></a>Stateful versus stateless Logic apps

* *Stateful*

  Maak stateful Logic apps wanneer u gegevens uit eerdere gebeurtenissen wilt houden, controleren of ernaar wilt verwijzen. Deze Logic apps bewaren de invoer en uitvoer voor elke actie en hun werk stroom statussen in externe opslag, waardoor de details van de uitvoering en de geschiedenis mogelijk worden gecontroleerd nadat elke uitvoering is voltooid. Stateful Logic apps bieden hoge tolerantie als of wanneer er sprake is van storingen. Nadat de services en systemen zijn hersteld, kunt u onderbroken logische app-uitvoeringen opnieuw bouwen op basis van de opgeslagen status en de Logic apps opnieuw uitvoeren om deze te volt ooien. Stateful werk stromen kunnen Maxi maal een jaar worden uitgevoerd.

* *Stateless*

  Maak stateless Logic apps wanneer u geen gegevens van eerdere gebeurtenissen hoeft op te slaan, te controleren of ernaar te verwijzen. Deze Logic apps bewaren de invoer en uitvoer voor elke actie en hun werk stroom statussen worden alleen in het geheugen opgeslagen, in plaats van deze gegevens over te dragen naar externe opslag. Als gevolg hiervan hebben stateless Logic apps kortere uitvoeringen die doorgaans niet langer zijn dan vijf minuten, snellere prestaties met snellere reactie tijden, een hogere door Voer en lagere uitvoerings kosten, omdat de details van de uitvoering en de geschiedenis niet in de externe opslag worden bewaard. Als er echter storingen optreden, worden onderbroken uitvoeringen niet automatisch hersteld. de oproepende functie moet de onderbroken uitvoeringen hand matig opnieuw verzenden. Voor eenvoudiger fout opsporing kunt u de [uitvoerings geschiedenis](#run-history) voor stateless Logic apps inschakelen.

  Stateless werk stromen ondersteunen momenteel alleen acties voor [beheerde connectors](../connectors/apis-list.md#managed-api-connectors), niet voor triggers. Als u uw werk stroom wilt starten, selecteert u de [ingebouwde aanvraag, Event hubs of service bus trigger](../connectors/apis-list.md#built-ins). Zie [niet-ondersteunde mogelijkheden](#unsupported)voor meer informatie over niet-ondersteunde triggers, acties en connectors.

Zie [geneste gedrags verschillen tussen stateful en stateless Logic](#nested-behavior)apps voor verschillen in de werking van geneste logische apps tussen stateful en stateless Logic apps.

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Prijsmodel

Wanneer u het resource type van de nieuwe **logische app (preview-versie)** implementeert, wordt u gevraagd om een hosting abonnement te selecteren, met name het [app service plan of het Premium-abonnement](../azure-functions/functions-scale.md) dat moet worden gebruikt als het prijs model. Als u het App Service plan selecteert, wordt u ook gevraagd om een [prijs categorie](../app-service/overview-hosting-plans.md)te selecteren. Tijdens de open bare preview worden met Logic apps op App Service geen *extra* kosten boven op het geselecteerde abonnement gemaakt.

Stateful Logic apps gebruiken [externe opslag](../azure-functions/functions-scale.md#storage-account-requirements), zodat het Azure Storage prijs model van toepassing is op opslag transacties die de Azure Logic apps runtime uitvoert. Wacht rijen worden bijvoorbeeld gebruikt voor de planning, terwijl tabellen en blobs worden gebruikt voor het opslaan van werk stroom statussen.

Raadpleeg de volgende onderwerpen voor meer informatie over de prijs modellen die van toepassing zijn op dit nieuwe resource type:

* [Schaal en hosting van Azure Functions](../azure-functions/functions-scale.md)
* [Een in Azure App Service omhoog schalen](../app-service/manage-scale-up.md)
* [Prijs informatie voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
* [Prijs informatie voor App Service](https://azure.microsoft.com/pricing/details/app-service/windows/)
* [Prijs informatie voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/)

## <a name="prerequisites"></a>Vereisten

### <a name="access-and-connectivity"></a>Toegang en connectiviteit

* Toegang tot internet, zodat u de vereisten kunt downloaden, vanuit Visual Studio code verbinding moet maken met uw Azure-account en publiceert vanuit Visual Studio code naar Azure, een docker-container of een andere omgeving.

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Als u in dit artikel dezelfde voorbeeld logische app wilt maken, hebt u een Office 365 Outlook-e-mail account nodig dat gebruikmaakt van een werk-of school account van micro soft om u aan te melden.

  Als u een andere [e-mail connector wilt gebruiken die wordt ondersteund door Azure Logic apps](/connectors/), zoals Outlook.com of [Gmail](../connectors/connectors-google-data-security-privacy-policy.md), kunt u nog steeds het voor beeld volgen en zijn de algemene stappen hetzelfde, maar uw gebruikers interface en opties kunnen op sommige manieren verschillen. Als u bijvoorbeeld de Outlook.com-connector gebruikt, gebruikt u uw persoonlijke Microsoft-account in plaats van u aan te melden.

### <a name="tools"></a>Hulpprogramma's

* [Visual Studio code 1.30.1 (januari 2019) of hoger](https://code.visualstudio.com/). Dit is gratis. Down load en installeer ook deze extra hulpprogram ma's voor Visual Studio code, als u deze nog niet hebt:

  * [Azure-account extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account), die een gemeen schappelijke Azure-aanmelding en-filter ervaring biedt voor alle andere Azure-extensies in Visual Studio code.

  * [C# voor Visual Studio code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp), waarmee u met de functie F5 uw logische app kunt uitvoeren.

  * [Azure functions core tools](../azure-functions/functions-run-local.md), versie [3.0.2931](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.2931) of [2.7.2936](https://github.com/Azure/azure-functions-core-tools/releases/tag/2.7.2936), via het micro soft-installatie programma (MSI). Deze hulpprogram ma's bevatten een versie van dezelfde runtime die de Azure Functions runtime aanstuurt die in Visual Studio code wordt uitgevoerd.

    > [!IMPORTANT]
    > Als u een installatie hebt die ouder is dan deze versies, moet u eerst die versie verwijderen of ervoor zorgen dat de omgevings variabele PATH verwijst naar de versie die u downloadt en installeert.
    >
    > Als u de [ **inline code** actie](../logic-apps/logic-apps-add-run-inline-code.md) voor het uitvoeren van Java script-code wilt gebruiken, moet u de Azure functions runtime versie 3x gebruiken, omdat de actie geen ondersteuning biedt voor versie 2 x. Deze actie wordt momenteel niet ondersteund in Linux-besturings systemen.

  * [Azure Logic apps (preview)-extensie voor Visual Studio code](https://go.microsoft.com/fwlink/p/?linkid=2143167). Deze uitbrei ding voor open bare preview biedt u de mogelijkheid om stateful en stateless Logic apps te maken en ze lokaal te testen in Visual Studio code.

    Op dit moment kunt u zowel de oorspronkelijke **Azure Logic apps** extensie als de nieuwe uitbrei ding **voor de Azure Logic apps (preview)** tegelijk in Visual Studio code installeren. Als u het pictogram van Azure selecteert op de Visual Studio code-werk balk, kunt u alle Logic apps weer geven die zijn geïmplementeerd in azure, maar elk resource type wordt weer gegeven in hun eigen uitbreidings secties, **Logic apps** en **Azure Logic apps (preview)**.

    > [!IMPORTANT]
    > Als u logische apps hebt gemaakt met behulp van de extensie **Azure Logic apps (private preview)** , werken deze Logic apps niet met de open bare preview-extensie. U kunt deze logische apps echter migreren door de uitbrei ding van de persoonlijke preview-versie te verwijderen, de vereiste opschoning uit te voeren en de uitbrei ding voor open bare preview te installeren. U kunt vervolgens een nieuw project maken in Visual Studio code en het **werk stroom. definitie** bestand van uw eerder gemaakte logische app kopiëren naar het nieuwe project.
    >
    > Voordat u de open bare preview-extensie installeert, moet u ervoor zorgen dat u eerdere versies verwijdert en deze artefacten verwijdert:
    >
    > * De map **micro soft. Azure. functions. ExtensionBundle. workflows** , die eerdere uitbreidings bundels bevat en die zich in een van beide paden bevindt:
    >
    >   * `C:\Users\<username>\AppData\Local\Temp\Functions\ExtensionBundles`
    >
    >   * `C:\Users\<username>.azure-functions-core-tools\Functions\ExtensionBundles`
    >
    > * De map **micro soft. Azure. workflows. webjobs. extension** , de [NuGet](/nuget/what-is-nuget) cache voor de extensie van de persoonlijke preview en bevindt zich op dit pad:
    >
    >   `C:\Users\<username>\.nuget\packages`

    Voer de volgende stappen uit om de extensie **Azure Logic apps (preview)** te installeren:

    1. Selecteer in Visual Studio code op de werk balk links de optie **uitbrei dingen**.

    1. Typ in het zoekvak voor extensies `azure logic apps preview` . Selecteer **Azure Logic apps (preview)** installeren in de lijst met resultaten **>** **Install**.

       Nadat de installatie is voltooid, wordt de uitbrei ding van de open bare preview weer gegeven in de lijst **extensies: installed** .

       ![Scherm opname van de lijst met geïnstalleerde extensies van Visual Studio code met de extensie ' Azure Logic Apps (preview) ' onderstreept.](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

* Als u de logische app wilt testen die u in dit artikel hebt gemaakt, hebt u een hulp programma nodig waarmee u aanroepen kunt verzenden naar de trigger voor aanvragen. Dit is de eerste stap in de logische app. Als u zo'n hulp programma niet hebt, kunt u [postman](https://www.postman.com/downloads/)downloaden, installeren en gebruiken.

* U kunt een [Application Insights](../azure-monitor/app/app-insights-overview.md) resource toevoegen en gebruiken voor eenvoudiger diagnostische logboek registratie en tracering. U kunt deze resource maken tijdens de implementatie van de logische app of in de Azure Portal nadat u uw logische app hebt geïmplementeerd.

### <a name="storage-requirements"></a>Opslag vereisten

Het maken van de nieuwe **logische app (preview)** -resource is momenteel niet beschikbaar op Mac OS. Voor Windows of andere besturings systemen, zoals Linux, moet u echter deze opslag vereiste instellen.

1. Down load en Installeer [Azure Storage Emulator 5,10](https://go.microsoft.com/fwlink/p/?linkid=717179).

1. Als u de emulator wilt uitvoeren, moet u een lokale installatie van SQL DB hebben, zoals de gratis [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658). Zie [de Azure Storage-emulator gebruiken voor ontwikkeling en testen](../storage/common/storage-use-emulator.md)voor meer informatie.

   > [!IMPORTANT]
   > Voordat u de ontwerp functie voor logische apps opent om de werk stroom van de logische app te maken, moet u de emulator starten. Anders wordt er een bericht weer gegeven dat de `Workflow design time could not be started` .
   >
   > ![Scherm opname van de Azure Storage-emulator wordt uitgevoerd.](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Visual Studio Code instellen

1. Als u er zeker van wilt zijn dat alle uitbrei dingen correct zijn geïnstalleerd, laadt of start u Visual Studio code opnieuw.

1. Hiermee wordt ingeschakeld of gecontroleerd of Visual Studio code automatisch extensie-updates zoekt en installeert zodat uw open bare preview-extensie de nieuwste updates ontvangt.

   Voer de volgende stappen uit om deze instelling te controleren:

   1. Ga in het menu **bestand** naar **voor keur** **>** **instellingen**.

   1. Ga op het tabblad **gebruiker** naar **onderdelen** **>** **extensies**.

   1. Controleer of **automatisch controleren van updates** en **automatisch bijwerken** is geselecteerd.

1. Schakel deze optie in of uit om de extensie **-instellingen voor Azure Logic apps (preview)** in Visual Studio code in te stellen:

   * **Azure Logic Apps v2: paneel modus**
   * **Azure Logic Apps v2: Project runtime**

   1. Ga in het menu **bestand** naar **voor keur** **>** **instellingen**.

   1. Ga op het tabblad **gebruiker** naar **>** **extensies** **>** **Azure Logic apps (preview)**.

   1. Controleer onder **Azure Logic apps v2: deel venster modus**, of **modus inschakelen** is geselecteerd. Stel onder **Azure Logic apps v2: Project runtime**de versie in op **~ 3** of **~ 2**, op basis van de [Azure functions core tools versie](#prerequisites) die u eerder hebt geïnstalleerd.

      > [!IMPORTANT]
      > Als u de [ **inline code** actie](../logic-apps/logic-apps-add-run-inline-code.md) voor het uitvoeren van Java script-code wilt gebruiken, moet u ervoor zorgen dat u Project runtime versie 3 gebruikt, omdat de actie geen ondersteuning biedt voor versie 2. Deze actie wordt momenteel niet ondersteund in Linux-besturings systemen.

      ![Scherm opname van Visual Studio code-instellingen voor de extensie Azure Logic Apps (preview).](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

1. Selecteer het Azure-pictogram op de werkbalk van Visual Studio Code.

   ![Scherm afbeelding van de werk balk Visual Studio code en het geselecteerde Azure-pictogram.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. In het deel venster Azure, onder **Azure: Logic apps (preview)**, selecteert **u aanmelden bij Azure**. Wanneer de pagina Visual Studio-code verificatie wordt weer gegeven, meldt u zich aan met uw Azure-account.

   ![Scherm opname van het Azure-venster en de geselecteerde koppeling voor Azure-aanmelding.](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   Nadat u zich hebt aangemeld, worden in het deel venster Azure de abonnementen in uw Azure-account weer gegeven. Als de verwachte abonnementen niet worden weer gegeven of als u wilt dat in het deel venster alleen specifieke abonnementen worden weer gegeven, volgt u deze stappen:

   1. Verplaats de aanwijzer in de lijst abonnementen naast het eerste abonnement totdat de knop **abonnementen selecteren** (filter pictogram) wordt weer gegeven. Selecteer het filter pictogram.

      ![Scherm opname van het Azure-venster en het geselecteerde filter pictogram.](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      Of Selecteer uw Azure-account in de status balk van Visual Studio code. 

   1. Wanneer er een andere abonnementen lijst wordt weer gegeven, selecteert u de gewenste abonnementen en selecteert u **OK**.

<a name="create-project"></a>

## <a name="create-a-local-project"></a>Een lokaal project maken

Voordat u uw logische app kunt maken, moet u een lokaal project maken, zodat u uw logische app kunt beheren en implementeren vanuit Visual Studio code. Het onderliggende project lijkt veel op een Azure Functions project, ook wel bekend als een functie-app-project.

1. Maak op uw computer een *lege* lokale map die moet worden gebruikt voor het project dat u later in Visual Studio code gaat maken.

1. Sluit alle geopende mappen in Visual Studio code.

1. Selecteer in het deel venster Azure naast **Azure: Logic apps (preview)** de optie **Nieuw project maken** (pictogram dat een map en bliksem Schicht weer geven).

   ![Scherm opname van de Azure-venster werkbalk met ' nieuw project maken ' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Als Windows Defender firewall u vraagt om netwerk toegang te verlenen voor `Code.exe` , dat wil zeggen Visual Studio code, en voor `func.exe` , de Azure functions core tools, selecteert u **particuliere netwerken, zoals mijn thuis-of bedrijfs netwerk,** **>** **toegang toestaan**.

1. Blader naar de locatie waar u de projectmap hebt gemaakt, Selecteer deze map en ga door.

   ![Scherm opname van het dialoog venster ' map selecteren ' met een nieuw gemaakte projectmap en de knop selecteren geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. Selecteer in de lijst met sjablonen die wordt weer gegeven, een **stateful werk stroom** of **stateless werk stroom**. In dit voor beeld wordt een **stateful werk stroom**geselecteerd.

   ![Scherm opname van de lijst met werk stroom sjablonen waarvoor een stateful werk stroom is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. Geef een naam op voor de werk stroom van uw logische app en druk op ENTER. In dit voor beeld wordt `example-workflow` de naam gebruikt.

   ![Scherm opname van het vak ' een nieuwe stateful werk stroom maken (3/4) ' en ' voor beeld-werk stroom ' als werk stroom naam.](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

1. Selecteer in de volgende lijst die wordt weer gegeven, de optie **openen in huidig venster**.

   ![Scherm opname van de lijst met ' openen in het huidige venster ' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-location.png)

   Visual Studio code wordt opnieuw geladen, het deel venster Verkenner wordt geopend en het project wordt weer gegeven, waarin nu automatisch gegenereerde project bestanden zijn opgenomen. Het project bevat bijvoorbeeld een map waarin de naam van de werk stroom van de logische app wordt weer gegeven. In deze map bevat het `workflow.json` bestand de onderliggende JSON-definitie van de werk stroom van uw logische app.

   ![Scherm opname van het Verkenner-venster met de projectmap, de map werk stroom en het bestand workflow.jsop.](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

Open vervolgens deworkflow.jsin het bestand in de ontwerp functie ** voor** logische apps.

### <a name="open-the-workflow-definition-file-in-logic-app-designer"></a>Het definitie bestand van de werk stroom openen in de ontwerp functie voor logische apps

Voordat u het definitie bestand van de werk stroom in de ontwerp functie probeert te openen, moet u ervoor zorgen dat de Azure Storage emulator wordt uitgevoerd als Visual Studio code wordt uitgevoerd in Windows of Linux. Raadpleeg de [vereisten](#prerequisites)voor meer informatie.

1. Vouw de projectmap voor uw werk stroom uit. Open de **workflow.jsin** het snelmenu van het bestand en selecteer **openen in Designer**.

   ![Scherm opname van het deel venster Verkenner en het snelkoppelings venster voor de workflow.jsin het bestand ' openen in Designer ' is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

   Als het fout bericht wordt weer gegeven, controleert u of `Workflow design time could not be started` de Azure Storage-emulator wordt uitgevoerd. Probeer anders de volgende suggesties voor probleem oplossing:

   Controleer in Visual Studio code de uitvoer van de preview-uitbrei ding.

   1. Selecteer in het menu **weer gave** de optie **uitvoer**.

   1. Selecteer in de lijst op de titel balk van de **uitvoer** **Azure Logic apps** zodat u de uitvoer voor de preview-uitbrei ding kunt bekijken, bijvoorbeeld:

      ![Scherm afbeelding van het uitvoer venster van Visual Studio code waarvoor ' Azure Logic Apps ' is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

   1. Controleer de uitvoer en controleer of dit fout bericht wordt weer gegeven:

      ```text
      A host error has occurred during startup operation '<operation-ID>'.
      System.Private.CoreLib: The file 'C:\Users\<your-username>\AppData\Local\Temp\Functions\
      ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.1\bin\
      DurableTask.AzureStorage.dll' already exists.
      Value cannot be null. (Parameter 'provider')
      Application is shutting down...
      Initialization cancellation requested by runtime.
      Stopping host...
      Host shutdown completed.
      ```

      Deze fout kan optreden als u eerder hebt geprobeerd om de ontwerp functie te openen en vervolgens het project uit te zetten of te verwijderen. Om deze fout op te lossen, verwijdert u de map **ExtensionBundles** op deze locatie **. ..\Users \\ {uw-username} \AppData\Local\Temp\Functions\ExtensionBundles**en probeer het **workflow.js** opnieuw te openen in het bestand in de ontwerp functie.

1. Selecteer in de lijst **connectors in azure inschakelen** de optie **connectors van Azure gebruiken**. Dit is van toepassing op alle beheerde connectors die beschikbaar zijn in de Azure Portal, niet alleen voor connectors voor Azure-Services.

   ![Scherm opname van het Verkenner-deel venster met de lijst ' connectors in azure inschakelen ' open en connectors van Azure gebruiken geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

1. Selecteer in de lijst resource groepen de optie **nieuwe resource groep maken**.

   ![Scherm opname van het Verkenner-deel venster met de lijst resource groepen en ' nieuwe resource groep maken ' geselecteerd](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. Geef een naam op voor de resource groep en druk op ENTER. In dit voorbeeld wordt `example-logic-app-preview-rg` gebruikt.

   ![Scherm opname van het deel venster Verkenner en de naam van de resource groep.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. Zoek en selecteer in de lijst locaties een Azure-regio die u wilt gebruiken voor het maken van uw resource groep en-resources. In dit voor beeld wordt **West-Centraal VS**gebruikt.

   > [!NOTE]
   > Op dit moment worden niet alle regio's ondersteund, hoewel er updates worden uitgevoerd. Raadpleeg de [pagina met bekende problemen](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)met de extensie github voor meer informatie.

   ![Scherm afbeelding met het Verkenner-deel venster met de lijst met locaties en ' West-Centraal VS ' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   Nadat u deze stap hebt uitgevoerd, opent Visual Studio code de ontwerp functie voor logische apps.

   > [!NOTE]
   > Wanneer Visual Studio code de ontwerp tijd API van de werk stroom start, wordt er een bericht weer gegeven dat het opstarten enkele seconden kan duren. U kunt dit bericht negeren of **OK**selecteren.

   Nadat de ontwerp functie voor logische apps wordt weer gegeven, wordt de prompt **een bewerking kiezen** wordt weer gegeven in de ontwerp functie en wordt standaard geselecteerd, waarin het deel venster **actie toevoegen** wordt weer gegeven.

   ![Scherm opname van Logic app Designer.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. Voeg vervolgens [een trigger en acties](#add-trigger-actions) toe aan uw werk stroom.

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>Een trigger en acties toevoegen

Nadat u de ontwerp functie voor logische apps hebt geopend vanuit uw **workflow.jsin** het snelmenu van het bestand, wordt de prompt **een bewerking kiezen** weer gegeven in de ontwerp functie en standaard ingeschakeld. U kunt nu beginnen met het maken van uw werk stroom door een trigger en acties toe te voegen.

De werk stroom van de logische app in dit voor beeld maakt gebruik van deze trigger en deze acties:

* De ingebouwde [aanvraag trigger](../connectors/connectors-native-reqres.md), **wanneer er een HTTP-aanvraag wordt ontvangen**, die inkomende aanroepen of aanvragen ontvangt en een eind punt maakt dat andere services of logische apps kan aanroepen.

* De [Office 365 Outlook-actie](../connectors/connectors-create-api-office365-outlook.md), **een e-mail verzenden**.

* De ingebouwde [reactie actie](../connectors/connectors-native-reqres.md)die u gebruikt om een antwoord te verzenden en gegevens terug te sturen naar de aanroeper.

### <a name="add-the-request-trigger"></a>De aanvraag trigger toevoegen

1. Zorg ervoor dat naast de ontwerp functie in het deel venster **een trigger toevoegen** onder het zoekvak **een bewerking kiezen de optie** **ingebouwd** is geselecteerd, zodat u een trigger kunt selecteren die systeem eigen wordt uitgevoerd.

1. Voer in het zoekvak **een bewerking kiezen** het selectie vakje in `when a http request` en selecteer de ingebouwde aanvraag trigger die **wordt genoemd wanneer een HTTP-aanvraag wordt ontvangen**.

   ![Scherm opname van Logic app Designer en * * een trigger toevoegen * * deel venster met de trigger wanneer een HTTP-aanvraag is ontvangen geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   Wanneer de trigger wordt weer gegeven op de Designer, wordt het detail venster van de trigger geopend om de eigenschappen, instellingen en andere acties van de trigger weer te geven.

   ![Scherm opname van Logic app Designer met de trigger geselecteerd wanneer een HTTP-aanvraag is ontvangen en het deel venster trigger Details geopend.](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > Als het detail venster niet wordt weer gegeven, zorgt u ervoor dat de trigger is geselecteerd in de ontwerp functie.

1. Voer de volgende stappen uit als u een item in de ontwerp functie wilt verwijderen:

   1. Selecteer het item op de ontwerp functie.

   1. In het detail venster van het item dat aan de rechter kant wordt geopend, selecteert u de knop met weglatings tekens (**...**) **>** **verwijderen**. Selecteer **OK**om de verwijdering te bevestigen.

      ![Scherm opname van het geselecteerde item in de ontwerp functie met het deel venster Details openen en met de optie voor het selecteren van de knop verwijderen.](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

### <a name="add-the-office-365-outlook-action"></a>De Office 365 Outlook-actie toevoegen

1. Selecteer in de ontwerp functie, onder de trigger die u hebt toegevoegd, de optie **nieuwe stap**.

   De prompt **een bewerking kiezen** wordt weer gegeven in de ontwerp functie en het **deel venster actie toevoegen** wordt opnieuw geopend, zodat u de volgende actie kunt selecteren.

1. Selecteer in het deel venster **actie toevoegen** onder het zoekvak **een bewerking kiezen** de optie **Azure** zodat u een actie kunt vinden en selecteren voor een beheerde connector die in Azure is geïmplementeerd.

   In dit voor beeld wordt de Office 365 Outlook-actie geselecteerd en gebruikt, **een e-mail (v2) verzenden**.

   ![Scherm opname van Logic app Designer en * * een actie toevoegen * * deel venster met Office 365 Outlook de actie een e-mail verzenden is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. Selecteer **Aanmelden** in het detail venster van de actie zodat u een verbinding kunt maken met uw e-mail account.

   ![Scherm opname van Logic app Designer en * * het deel venster e-mail (v2) * * verzenden als ' Aanmelden ' is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Wanneer Visual Studio code u vraagt om toestemming voor toegang tot uw e-mail account, selecteert u **openen**.

   ![Scherm opname van de Visual Studio-code prompt om toegang toe te staan.](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > Om toekomstige prompts te voor komen, selecteert u **vertrouwde domeinen configureren** zodat u de verificatie pagina als vertrouwd domein kunt toevoegen.

1. Volg de volgende vragen om u aan te melden, toegang toe te staan en terug te keren naar Visual Studio code.

   > [!NOTE]
   > Als er te veel tijd wordt door gegeven voordat u de prompts voltooit, wordt het verificatie proces geduurd en mislukt. In dit geval gaat u terug naar de Designer en probeert u opnieuw aan te melden om de verbinding te maken.

1. Wanneer de uitbrei ding Azure Logic Apps Preview u vraagt om toestemming voor toegang tot uw e-mail account, selecteert u **openen**. Volg de volgende prompt om toegang toe te staan.

   ![Scherm opname van de prompt van de voorbeeld uitbreiding om toegang toe te staan.](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > Selecteer **niet opnieuw vragen voor deze extensie**om toekomstige prompts te voor komen.

1. Als de actie **een E-mail verzenden** niet wordt weer gegeven in de ontwerp functie, selecteert u deze actie.

1. Geef op het tabblad **para meters** van het detail venster van de actie de vereiste informatie voor de actie op, bijvoorbeeld:

   ![Scherm opname van Logic app Designer met Details voor Office 365 Outlook "een e-mail verzenden".](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Aan** | Ja | <*uw-e-mailadres*> | De e-mail ontvanger, die uw e-mail adres kan zijn voor test doeleinden. In dit voor beeld wordt het fictieve e-mail adres gebruikt `sophiaowen@fabrikam.com` . |
   | **Onderwerp** | Ja | `An email from your example workflow` | Het onderwerp van de e-mail |
   | **Hoofdtekst** | Ja | `Hello from your example workflow!` | De inhoud van de hoofd tekst van de e-mail |
   ||||

   > [!NOTE]
   > Als u wijzigingen wilt aanbrengen in het detail venster op het tabblad **instellingen**, **uitvoeren na**of **statisch resultaat** , moet u ervoor zorgen dat u **klaar bent** om deze wijzigingen door te voeren voordat u de tabbladen verwisselt of de focus naar de ontwerper wijzigt. Als dat niet het geval is, blijven de wijzigingen niet behouden in Visual Studio code. Raadpleeg de [pagina met bekende problemen](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)met de extensie github voor meer informatie.

1. Selecteer in de ontwerp functie **Opslaan**.

Daarna kunt u uw werk stroom lokaal opsporen en testen in Visual Studio code.

<a name="debug-test-locally"></a>

## <a name="debug-and-test-your-logic-app"></a>Fouten opsporen en uw logische app testen

1. U kunt [de uitvoerings geschiedenis voor die werk stroom voor](#run-history)meer informatie over het eenvoudiger opsporen van fouten in een stateless werk stroom voor logische apps.

1. Open het menu **uitvoeren** op de werk balk van Visual Studio code en selecteer **Start Debugging** (F5).

   Het **Terminal** venster wordt geopend, zodat u de foutopsporingssessie kunt controleren.

1. Zoek nu de call back-URL voor het eind punt op de aanvraag trigger.

   1. Open het deel venster Verkenner opnieuw zodat u het project kunt weer geven.

   1. Selecteer **overzicht**in het snelmenu **workflow.js** van het bestand.

      ![Scherm opname van het deel venster Verkenner en het snelkoppelings venster voor de workflow.jsin het bestand ' overzicht ' is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. Zoek de waarde van de **call back-URL** , die er ongeveer zo uitziet als deze URL voor de voorbeeld aanvraag trigger:

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![Scherm opname van de overzichts pagina van uw werk stroom met call back-URL](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. Als u de call back-URL wilt testen door de werk stroom van de logische app te activeren, opent u [postman](https://www.postman.com/downloads/) of uw favoriete hulp programma voor het maken en verzenden van aanvragen.

   In dit voor beeld wordt het gebruik van Postman voortgezet. Zie voor meer informatie [verzen ding aan de slag](https://learning.postman.com/docs/getting-started/introduction/).

   1. Selecteer op de werk balk van Postman de optie **Nieuw**.

      ![Scherm afbeelding met de knop Nieuw is geselecteerd](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. Selecteer in het deel venster **Nieuw** , onder **bouw stenen**, **aanvraag**.

   1. Geef in het venster **aanvraag opslaan** onder **naam van aanvraag**een naam op voor de aanvraag, bijvoorbeeld `Test workflow trigger` .

   1. Onder **Selecteer een verzameling of map om op te slaan**, selecteert u **verzameling maken**.

   1. Geef onder **alle verzamelingen**een naam op voor de verzameling die u wilt maken voor het organiseren van uw aanvragen, druk op ENTER en selecteer **opslaan in <*verzameling-naam* > **. In dit voor beeld wordt `Logic Apps requests` de naam van de verzameling gebruikt.

      Het aanvraag venster van Postman wordt geopend, zodat u een aanvraag kunt verzenden naar de call back-URL voor de aanvraag trigger.

      ![Scherm opname van de weer gave met het geopende aanvraag venster](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Ga terug naar Visual Studio code. Kopieer op de pagina overzicht van de werk stroom de eigenschaps waarde van de **call back-URL** .

   1. Ga terug naar de Postman. In het deel venster aanvraag, in de lijst met methoden, die momenteel de methode **Get** als standaard aanvraag bevat, plakt u de call back-URL die u eerder hebt gekopieerd in het vak Adres en selecteert u **verzenden**.

      ![Scherm afbeelding waarin de URL van de Postman en de retour aanroep wordt weer gegeven in het adresvak met de knop verzenden geselecteerd](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      De voorbeeld werk stroom voor logische apps verzendt een e-mail bericht dat lijkt op dit voor beeld:

      ![Scherm opname van Outlook-e-mail, zoals beschreven in het voor beeld](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Ga in Visual Studio code terug naar de overzichts pagina van uw werk stroom.

   Als u een stateful werk stroom hebt gemaakt, worden op de pagina overzicht de status en geschiedenis van de werk stroom weer gegeven nadat de aanvraag die u hebt verzonden, de werk stroom heeft geactiveerd. Zie voor meer informatie over uitvoerings statussen de [geschiedenis van uitvoeringen controleren](../logic-apps/monitor-logic-apps.md#review-runs-history).

   ![Scherm opname van de overzichts pagina van uw werk stroom met de status en geschiedenis van de uitvoering](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   > [!TIP]
   > Als de uitvoerings status niet wordt weer gegeven, kunt u de pagina overzicht vernieuwen door **vernieuwen**te selecteren.

1. Als u de statussen voor elke stap in een specifieke uitvoering en de invoer en uitvoer van de stap wilt bekijken, selecteert u de knop met weglatings tekens (**...**) voor die uitvoering en selecteert u vervolgens **uitvoeren weer geven**.

   ![Scherm afbeelding van de rij met de uitvoerings geschiedenis van de werk stroom met de knop met weglatings tekens en ' show run ' geselecteerd](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio code toont de uitvoerings statussen voor elke actie.

1. Als u de invoer en uitvoer voor elke stap wilt bekijken, vouwt u de stap uit die u wilt inspecteren. Selecteer onbewerkte **invoer weer geven** of **onbewerkte uitvoer weer geven**om de onbewerkte invoer en uitvoer voor die stap verder te bekijken.

   ![Scherm opname van de status van elke stap in de werk stroom plus de invoer en uitvoer in de uitgevouwen actie ' een e-mail verzenden '](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. Als u de foutopsporingssessie wilt stoppen, selecteert u in het menu **uitvoeren** de optie **fout opsporing stoppen** (SHIFT + F5).

<a name="return-response"></a>

## <a name="return-a-response-to-the-caller"></a>Een antwoord op de aanroeper retour neren

Als u een antwoord wilt terugsturen naar de aanroeper die een aanvraag naar uw logische app heeft verzonden, kunt u de ingebouwde [reactie actie](../connectors/connectors-native-reqres.md) gebruiken voor een werk stroom die begint met de trigger voor aanvragen.

1. Selecteer op de Logic app Designer onder de actie **een E-mail verzenden** de optie **nieuwe stap**.

   De prompt **een bewerking kiezen** wordt weer gegeven in de ontwerp functie en het **deel venster actie toevoegen** wordt opnieuw geopend, zodat u de volgende actie kunt selecteren.

1. Zorg ervoor dat in het deel venster **actie toevoegen** onder het zoekvak **een actie kiezen de optie** **ingebouwd** is geselecteerd. Typ in het zoekvak `response` en selecteer de actie **antwoord** .

   ![Scherm opname van Logic app Designer met de actie reactie geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   Wanneer de **reactie** actie wordt weer gegeven in de ontwerp functie, wordt het detail venster van de actie automatisch geopend.

   ![Scherm opname van de logica van de ontwerp functie voor logische apps met het detail venster ' antwoord ', en de eigenschap ' Body ' die is ingesteld op de waarde van de eigenschap Body van de actie ' e-mail verzenden '.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. Geef op het tabblad **para meters** de vereiste gegevens op voor de functie die u wilt aanroepen.

   In dit voor beeld wordt de eigenschaps waarde **Body** geretourneerd die de uitvoer is van de actie **een e-mail verzenden** .

   1. Klik in het eigenschappenvak van de **hoofd tekst** , zodat de lijst met dynamische inhoud wordt weer gegeven en de beschik bare uitvoer waarden van de voor gaande trigger en acties in de werk stroom worden getoond.

      ![Scherm opname van het detail venster van de actie ' respons ' met de muis aanwijzer binnen de eigenschap ' Body ' zodat de lijst met dynamische inhoud wordt weer gegeven.](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. Selecteer **hoofd tekst**in de lijst met dynamische inhoud onder **een e-mail verzenden**.

      ![Scherm opname van de lijst met open dynamische inhoud. In de lijst, onder de kop ' e-mail verzenden ', is de uitvoer waarde ' Body ' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      Wanneer u klaar bent, wordt de eigenschap **Body** van de reactie actie nu ingesteld op **de uitvoer waarde** van de actie **een e-mail bericht verzenden** .

      ![Scherm opname van de status van elke stap in de werk stroom plus de invoer en uitvoer in de uitgevouwen reactie actie.](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. Selecteer in de ontwerp functie **Opslaan**.

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>Uw logische app opnieuw testen

Nadat u updates hebt gemaakt voor uw logische app, kunt u een andere test uitvoeren door de debugger opnieuw uit te voeren in Visual Studio en een andere aanvraag te verzenden om uw bijgewerkte logische app te activeren. Dit is vergelijkbaar met de stappen in [fout opsporing en test uw logische app](#debug-test-locally).

1. Open het menu **uitvoeren** op de werk balk van Visual Studio code en selecteer **Start Debugging** (F5).

1. Stuur in postman of uw hulp programma voor het maken en verzenden van aanvragen een andere aanvraag om uw werk stroom te activeren.

1. Als u een stateful werk stroom hebt gemaakt, controleert u de status van de meest recente uitvoering op de pagina overzicht van de werk stroom. Als u de status, invoer en uitvoer voor elke stap in die uitvoering wilt weer geven, selecteert u de knop met weglatings tekens (**...**) voor die uitvoering en selecteert u vervolgens **uitvoeren weer geven**.

   Hier volgt een voor beeld van de stap-voor-stap-status voor een uitvoering nadat de voorbeeld werk stroom is bijgewerkt met de reactie actie.

   ![Scherm opname van de status van elke stap in de bijgewerkte werk stroom plus de invoer en uitvoer in de uitgevouwen reactie actie.](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. Als u de foutopsporingssessie wilt stoppen, selecteert u in het menu **uitvoeren** de optie **fout opsporing stoppen** (SHIFT + F5).

<a name="publish-azure"></a>

## <a name="publish-to-azure"></a>Publiceren naar Azure

Vanuit Visual Studio code kunt u uw project rechtstreeks implementeren in azure, dat uw logische app publiceert met het nieuwe resource type **Logic app (preview)** . Net als bij de resource van de functie-app in Azure Functions moet u voor dit nieuwe bron type een [hosting-abonnement en prijs categorie](../app-service/overview-hosting-plans.md)selecteren, die u tijdens de implementatie kunt instellen. Raadpleeg de volgende onderwerpen voor meer informatie over het hosten van plannen en prijzen:

* [Een in Azure App Service omhoog schalen](../app-service/manage-scale-up.md)
* [Schaal en hosting van Azure Functions](../azure-functions/functions-scale.md)

U kunt uw logische app als een nieuwe resource publiceren, waarmee automatisch extra benodigde resources worden gemaakt, zoals een [Azure Storage account, vergelijkbaar met de vereisten van de functie-app](../azure-functions/storage-considerations.md). Of u kunt uw logische app publiceren naar een eerder geïmplementeerde **logische app (preview)** -resource, die tijdens het implementatie proces wordt overschreven in Azure.

### <a name="publish-as-a-new-logic-app-preview-resource"></a>Publiceren als een nieuwe logische app (preview)-resource

1. Selecteer het Azure-pictogram op de werkbalk van Visual Studio Code.

1. Selecteer op de werk balk van het deel venster **Azure: Logic apps (preview)** de optie **implementeren naar logische app**.

   ![Scherm opname van het deel venster Azure: Logic Apps (preview) en de werk balk van het deel venster met ' implementeren op logische app ' geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. Selecteer in de lijst die door Visual Studio code wordt geopend, een van de volgende opties:

   * **Een nieuwe logische app maken (preview) in azure** (snel)
   * **Een nieuwe logische app maken (preview) in azure Advanced**
   * Een eerder geïmplementeerde **logische app (preview)** -resource, indien aanwezig

   Dit voor beeld gaat verder met het **maken van een nieuwe logische app (preview) in azure Advanced**.

   ![Scherm opname van het deel venster ' Azure: Logic Apps (preview) ' met een lijst waarin ' nieuwe logische app maken (preview) in azure ' is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. Voer de volgende stappen uit om een nieuwe resource **voor een logische app (preview)** te maken:

   1. Geef een wereld wijd unieke naam op voor de nieuwe logische app. Dit is de naam die u moet gebruiken voor de **logische app (preview)** -resource.

      ![Scherm opname van het deel venster ' Azure: Logic Apps (preview) ' en een prompt om een naam op te geven voor de nieuwe logische app die u wilt maken.](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. Selecteer een hosting abonnement voor uw nieuwe logische app, een [**app service plan**](../azure-functions/functions-scale.md#app-service-plan) of [**Premium**](../azure-functions/functions-scale.md#premium-plan). In dit voor beeld wordt **app service plan**geselecteerd.

      ![Scherm opname van het deel venster "Azure: Logic Apps (preview)" en een prompt om "App Service plan" of "Premium" te selecteren.](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. Maak een nieuw App Service plan of selecteer een bestaand abonnement. In dit voor beeld wordt **nieuw app service plan maken**geselecteerd.

      ![Scherm opname van het deel venster ' Azure: Logic Apps (preview) ' en een prompt om een nieuw App Service plan te maken of een bestaand App Service plan te selecteren.](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. Geef een naam op voor uw App Service plan en selecteer vervolgens een [prijs categorie](../app-service/overview-hosting-plans.md) voor het plan. In dit voor beeld wordt het **gratis abonnement F1** geselecteerd.

      ![Scherm opname van het deel venster ' Azure: Logic Apps (preview) ' en een prompt om een prijs categorie te selecteren.](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. Voor optimale prestaties zoekt en selecteert u dezelfde resource groep als uw project voor de implementatie.

      > [!NOTE]
      > Hoewel u een andere resource groep kunt maken of gebruiken, kan dit van invloed zijn op de prestaties. Als u een andere resource groep maakt of kiest, maar niet annuleert nadat de bevestigings prompt wordt weer gegeven, wordt uw implementatie ook geannuleerd.

   1. Voor stateful werk stromen selecteert u **Nieuw opslag account maken** of een bestaand opslag account.

      ![Scherm opname van het deel venster ' Azure: Logic Apps (preview) ' en een prompt voor het maken of selecteren van een opslag account.](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. Voor eenvoudiger diagnostische logboek registratie en tracerings functie kunt u een bestaande Application Insights resource selecteren. Anders kunt u **nieuwe Application Insights resource maken**selecteren of Application Insights in de Azure Portal instellen nadat u uw app hebt geïmplementeerd.

      Voordat u implementeert, moet u ervoor zorgen dat u het `logLevel` object toevoegt aan het `logging` object in de **host.jsvoor** het bestand dat bestaat op het hoofd niveau van het project en de `Host.Triggers.Workflow` in te stellen op `Information` , bijvoorbeeld:

      ```json
      "logLevel": {
         "Host.Triggers.Workflow": "Information"
      },
      ```

      Het **host.js** bestand kan er als volgt uitzien:

      ```json
      {
         "version": "2.0",
         "logging": {
            "applicationInsights": {
               "samplingExcludedTypes": "Request",
               "samplingSettings": {
                  "isEnabled": true
               }
            },
            "logLevel": {
               "Host.Triggers.Workflow": "Information"
            }
         }
      }
      ``` 

   Wanneer u klaar bent, begint Visual Studio code het maken en implementeren van de resources die nodig zijn voor het publiceren van uw logische app.

1. Als u het implementatie proces wilt controleren en controleren, selecteert u in het menu **weer gave** de optie **uitvoer**. Selecteer in de lijst werk balk van uitvoer venster **Azure Logic apps**.

   ![Scherm opname van het uitvoer venster met de Azure Logic Apps geselecteerd in de lijst met werk balken, samen met de voortgang en statussen van de implementatie.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Wanneer Visual Studio code de implementatie van uw werk stroom voor logische apps naar Azure voltooit, wordt dit bericht weer gegeven:

   ![Scherm afbeelding met een bericht dat de implementatie naar Azure is voltooid.](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   Gefeliciteerd, uw logische app is nu live in Azure en is standaard ingeschakeld.

Vervolgens leert [u hoe u uw geïmplementeerde logische app kunt vinden in de Azure Portal](#find-manage-deployed-workflows-portal) of [in Visual Studio code](#find-manage-deployed-workflows-vs-code).

### <a name="enable-monitoring-for-deployed-logic-app-preview-resources"></a>Bewaking inschakelen voor resources van geïmplementeerde logische apps (preview-versie)

Voer de volgende stappen uit om uitvoerings geschiedenis en bewaking in te scha kelen op een geïmplementeerde **logische app (preview)** :

1. Zoek en selecteer in de [Azure Portal](https://portal.azure.com)de geïmplementeerde **logische app (preview)** -resource.

1. Selecteer **CORS**in het menu van de resource onder **API**.

1. Voeg het Joker teken (*) toe in het deel venster **CORS** onder **toegestane oorsprongen**.

1. Wanneer u klaar bent, selecteert u op de **CORS** -werk balk de optie **Opslaan**.

   ![Scherm opname van de Azure Portal met een geïmplementeerde Logic Apps (preview)-resource. In het menu resource is ' CORS ' geselecteerd met een nieuwe vermelding voor ' toegestane oorsprongen ' ingesteld op het Joker teken ' * '.](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="find-manage-deployed-workflows-vs-code"></a>

## <a name="find-and-manage-deployed-logic-apps-in-visual-studio-code"></a>Geïmplementeerde Logic apps zoeken en beheren in Visual Studio code

In Visual Studio code kunt u alle geïmplementeerde Logic apps in uw Azure-abonnement weer geven, of het nu de oorspronkelijke **Logic apps** of het resource type van de **logische app (preview)** is en taken selecteren die u helpen bij het beheren van deze Logic apps. Voor toegang tot beide resource typen hebt u echter zowel de **Azure Logic apps** als de **Azure Logic apps (preview)-** uitbrei dingen voor Visual Studio code nodig.

1. Selecteer op de werk balk links het pictogram van Azure. Vouw in het deel venster **Azure: Logic apps (preview)** uw abonnement uit, waarin alle geïmplementeerde Logic apps voor dat abonnement worden weer gegeven.

1. Zoek en selecteer de logische app die u wilt beheren. Open het snelmenu van de logische app en selecteer de taak die u wilt uitvoeren.

   U kunt bijvoorbeeld taken selecteren, zoals stoppen, starten, opnieuw starten of verwijderen van uw geïmplementeerde logische app.

   ![Scherm opname van Visual Studio code met het geopende uitbreidings venster Azure Logic Apps (preview) en de geïmplementeerde werk stroom.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. Als u de geïmplementeerde logische app wilt openen in de Azure Portal, opent u in Visual Studio code het snelmenu van de logische app en selecteert u **openen in portal**.

   De Azure Portal in uw browser wordt geopend, meldt u automatisch aan bij de portal als u bent aangemeld bij Visual Studio code en toont uw logische app.

   ![Scherm opname van de Azure Portal-pagina voor uw logische app in Visual Studio code.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   U kunt zich ook afzonderlijk aanmelden bij de Azure Portal, het zoekvak van de portal gebruiken om uw logische app te zoeken en vervolgens uw logische app in de lijst met resultaten te selecteren.

   ![Scherm afbeelding met de Azure Portal en de zoek balk met zoek resultaten voor geïmplementeerde logische app, die wordt weer gegeven.](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="find-manage-deployed-workflows-portal"></a>

## <a name="find-and-manage-deployed-logic-apps-in-the-portal"></a>Geïmplementeerde logische apps zoeken en beheren in de portal

In de Azure Portal kunt u alle geïmplementeerde Logic apps bekijken die zich in uw Azure-abonnement bevinden, ongeacht of ze het oorspronkelijke **Logic apps** bron type of het bron type van de **logische app (preview-versie)** zijn. Op dit moment wordt elk resource type geordend en beheerd als afzonderlijke categorieën in Azure.

> [!NOTE]
> Voor de open bare preview kunt u alleen geïmplementeerde resources voor **logische apps (preview)** in de Azure portal weer geven en geen nieuwe **logische app-resources (preview)** maken. U kunt deze Logic apps alleen maken in Visual Studio code. U kunt echter [werk stromen toevoegen](#add-workflows) aan geïmplementeerde Logic apps met dit resource type.

Voer de volgende stappen uit om logische apps te vinden die het resource type **logische app (preview)** hebben:

1. Voer in het zoekvak van Azure Portal in `logic app preview` . Wanneer de lijst met resultaten wordt weer gegeven, selecteert u onder **Services**de optie **logische app (preview)**.

   ![Scherm opname van het zoekvak van Azure Portal met de Zoek tekst ' Logic app preview '.](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. Zoek en selecteer in het deel venster **logische app (preview)** de logische app die u hebt geïmplementeerd vanuit Visual Studio code.

   ![Scherm opname van de Azure Portal en de logische app-resources (preview) die in azure zijn geïmplementeerd.](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   De Azure Portal de afzonderlijke resource pagina voor de geselecteerde logische app wordt geopend.

   ![Scherm opname van de resource pagina van de werk stroom van uw logische app in de Azure Portal.](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. Als u de werk stromen in deze logische app wilt weer geven, selecteert u **werk stromen**in het menu van de logische app.

   In het deel venster **werk stromen** worden alle werk stromen weer gegeven in de huidige logische app. In dit voor beeld ziet u de werk stroom die u hebt gemaakt in Visual Studio code.

   ![Scherm opname van de resource pagina ' Logic app (preview) ' met het deel venster werk stromen open en de geïmplementeerde werk stroom](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. Als u een werk stroom wilt weer geven, selecteert u in het deel venster **werk stromen** die werk stroom.

   Het werk stroom venster wordt geopend en toont meer informatie en taken die u kunt uitvoeren op die werk stroom.

   Als u bijvoorbeeld de stappen in de werk stroom wilt weer geven, selecteert u **Designer**.

   ![Scherm opname van het geselecteerde werk stroom venster Overzicht, terwijl in het werk stroom menu de geselecteerde opdracht "Designer" wordt weer gegeven.](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   De ontwerp functie voor logische apps wordt geopend en toont de werk stroom die u hebt gemaakt in Visual Studio code. U kunt nu wijzigingen aanbrengen in deze werk stroom in de Azure Portal.

   ![Scherm afbeelding met de ontwerp functie voor logische apps en werk stromen die vanuit Visual Studio code worden geïmplementeerd.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflows"></a>

## <a name="add-a-workflow-to-deployed-logic-apps"></a>Een werk stroom toevoegen aan geïmplementeerde Logic apps

Via de Azure Portal kunt u lege werk stromen toevoegen aan een **logische app (preview)** -resource die u hebt geïmplementeerd vanuit Visual Studio code en deze werk stromen opbouwt in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com)naar de resource van de geïmplementeerde **logische app (preview)** en selecteer deze.

1. Selecteer **werk stromen**in het menu van de logische app. Selecteer **toevoegen**in het deel venster **werk stromen** .

   ![Scherm opname van het deel venster werk stromen en de werk balk van de geselecteerde logische app met de opdracht toevoegen is geselecteerd.](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. Geef in het deel venster **nieuwe werk stroom** naam op voor de werk stroom. Selecteer **stateful** of **stateless** **>** **Create**.

   Nadat Azure uw nieuwe werk stroom heeft geïmplementeerd, die wordt weer gegeven in het deel venster **werk stromen** , selecteert u die werk stroom om beheer en andere taken uit te voeren, zoals het openen van de ontwerp functie voor logische apps of de code weergave.

   ![Scherm opname van de geselecteerde werk stroom met beheer-en controle opties.](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   Als u bijvoorbeeld de ontwerp functie voor een nieuwe werk stroom opent, ziet u een leeg canvas. U kunt deze werk stroom nu maken in de Azure Portal.

   ![Scherm afbeelding met de ontwerp functie voor logische apps en een lege werk stroom.](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker-container"></a>Implementeren naar docker-container

Met behulp van de [opdracht regel interface (CLI) van .net core](/dotnet/core/tools/)kunt u uw project bouwen en vervolgens uw build publiceren. U kunt vervolgens een [docker-container](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container) bouwen en gebruiken als het doel voor het implementeren van uw werk stroom voor logische apps. Raadpleeg de volgende onderwerpen voor meer informatie:

* [Inleiding tot containers en docker](/dotnet/architecture/microservices/container-docker-introduction/)
* [Inleiding tot .NET en docker](/dotnet/core/docker/introduction)
* [Docker-terminologie](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)
* [Zelf studie: aan de slag met docker](/visualstudio/docker/tutorials/docker-tutorial)

1. Als u uw project wilt maken, opent u een opdracht regel prompt en voert u de volgende opdracht uit:

   `dotnet build -c release`

   Zie de pagina [DotNet-build](/dotnet/core/tools/dotnet-build/) -verwijzing voor meer informatie.

1. Publiceer uw build door de volgende opdracht uit te voeren:

   `dotnet publish -c release`

   Zie de pagina met [DotNet Publish](/dotnet/core/tools/dotnet-publish/) -verwijzing voor meer informatie.

1. Bouw een docker-container met behulp van een docker-bestand voor een .NET-werk stroom en voer deze opdracht uit:

   `docker build --tag local/workflowcontainer .`

   Hier ziet u een voor beeld van een docker-bestand voor een .NET-werk stroom, maar vervangt u *de <-*> waarde van de Azure Storage-account met de Connection String die u eerder hebt opgeslagen, zoals in dit voor beeld:

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageaccount;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ```text
   FROM mcr.microsoft.com/azure-functions/dotnet:3.0.14492-appservice

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AZURE_FUNCTIONS_ENVIRONMENT Development
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot
   ENV AzureFunctionsJobHost__Logging__Console__IsEnabled=true
   ENV FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY ./bin/Release/netcoreapp3.1/publish/ /home/site/wwwroot
   ```

   Zie [docker build](https://docs.docker.com/engine/reference/commandline/build/)voor meer informatie.

1. Voer de container lokaal uit met behulp van deze opdracht:

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   Zie [docker run](https://docs.docker.com/engine/reference/commandline/run/)(Engelstalig) voor meer informatie.

1. Als u de call back-URL voor de aanvraag trigger wilt ophalen, verzendt u deze aanvraag:

   `POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2019-10-01-edge-preview&code={master-key}`

   De <*hoofd sleutel*> waarde wordt gedefinieerd in het Azure Storage-account dat u hebt ingesteld voor `AzureWebJobsStorage` in het bestand **Azure-webjobs-geheimen/{Deployment-name}/host.jsop**, waar u de waarde in deze sectie kunt vinden:

   ```json
   {
     <...>
     "masterKey": {
        "name": "master",
        "value": "<master-key>",
        "encrypted": false
     },
     <...>
   }
   ```

<a name="run-history"></a>

## <a name="run-history-for-stateless-logic-app-workflows"></a>Uitvoerings geschiedenis voor stateless logische app-werk stromen

Als u de werk stroom voor een stateless logische app gemakkelijker wilt kunnen opsporen, kunt u de uitvoerings geschiedenis voor die werk stroom inschakelen en de uitvoerings geschiedenis uitschakelen wanneer u klaar bent.

### <a name="for-a-stateless-logic-app-workflow-in-visual-studio-code"></a>Voor een staatloze werk stroom voor een logische app in Visual Studio code

Ga als volgt te werk als u de werk stroom stateless Logic app lokaal in Visual Studio code gebruikt en uitvoert:

1. Zoek en vouw in uw project de map **werk stroom-Designtime** uit. Zoek en open de **local.settings.jsin** het bestand.

1. Voeg de `Workflow.<yourWorkflowName>.operationOptions` eigenschap toe en stel de waarde in op `WithStatelessRunHistory` , bijvoorbeeld:

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflow.<yourWorkflowName>.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. Als u de uitvoerings geschiedenis wilt uitschakelen wanneer u klaar bent, verwijdert u de `Workflow.<yourWorkflowName>.OperationOptions` eigenschap en de waarde ervan of stelt u de eigenschap in op `None` .

### <a name="for-a-stateless-logic-app-workflow-in-the-azure-portal"></a>Voor een staatloze werk stroom voor logische apps in de Azure Portal

Als u uw project al hebt geïmplementeerd op de Azure Portal, voert u de volgende stappen uit:

1. Zoek en open de resource van de **logische app (preview)** In de [Azure Portal](https://portal.azure.com).

1. Selecteer in het menu van de logische app onder **instellingen**de optie **configuratie**.

1. Op het tabblad **Toepassings instellingen** selecteert u **nieuwe toepassings instelling**.

1. Voer in het deel venster **toepassings instelling toevoegen/bewerken** in het vak **naam** de naam in van de bewerkings optie: 

   `Workflow.<yourWorkflowName>.OperationOptions`

1. Voer in het vak **waarde** de volgende waarde in: `WithStatelessRunHistory`

   Bijvoorbeeld:

   ![Scherm opname van de Azure Portal-en Logic app-resource (preview) met de ' configuratie ' > ' nieuwe toepassings instelling ' < ' deel venster toepassings instelling toevoegen/bewerken ' van de toepassing en de optie werk stroom. <yourWorkflowName>OperationOptions ' ingesteld op ' WithStatelessRunHistory '.](./media/create-stateful-stateless-workflows-visual-studio-code/stateless-operation-options-run-history.png)

1. Wanneer u gereed bent, selecteert u **OK**. Selecteer in het deel venster **configuratie** de optie **Opslaan**.

<a name="nested-behavior"></a>

## <a name="nested-behavior-differences-between-stateful-and-stateless-logic-apps"></a>Verschillen in genest gedrag tussen stateful en stateless Logic apps

U kunt [een logische app-werk stroom aanroepen](../logic-apps/logic-apps-http-endpoint.md) vanuit andere logische app-werk stromen met behulp van de trigger voor [aanvragen](../connectors/connectors-native-reqres.md) , [http-webhook](../connectors/connectors-native-webhook.md) -trigger of beheerde connector triggers die het [ApiConnectionWehook-type](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) hebben en HTTPS-aanvragen kunnen ontvangen.

Hier volgen de gedrags patronen die geneste logische app-werk stromen kunnen volgen nadat een bovenliggende werk stroom een onderliggende werk stroom aanroept:

* Asynchroon polling-patroon

  Het bovenliggende item wordt niet gewacht op een antwoord op de eerste aanroep, maar controleert doorlopend de uitvoerings geschiedenis van het kind totdat het onderliggende item is voltooid. Stateful werk stromen volgen standaard dit patroon. Dit is ideaal voor langlopende onderliggende werk stromen die mogelijk de [time-outlimieten voor aanvragen](../logic-apps/logic-apps-limits-and-config.md)overschrijden.

* Synchroon patroon ("vuur en verg eten")

  Het onderliggend item erkent de oproep door onmiddellijk een `202 ACCEPTED` reactie te retour neren en de bovenliggende actie wordt voortgezet zonder te hoeven wachten op de resultaten van het onderliggende item. In plaats daarvan ontvangt het bovenliggende item de resultaten wanneer het onderliggende element is voltooid. Onderliggende stateful werk stromen die geen reactie actie bevatten, volgen altijd het synchrone patroon. Voor onderliggende stateful werk stromen is de uitvoerings geschiedenis beschikbaar die u kunt controleren.

  Als u dit gedrag wilt inschakelen, stelt u in de JSON-definitie van de werk stroom de `OperationOptions` eigenschap in op `DisableAsyncPattern` . Zie [trigger-en actie typen-bewerkings opties](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)voor meer informatie.

* Activeren en wachten

  Voor een onderliggend werk stroom voor een onderliggend niveau wordt gewacht op een reactie die de resultaten van het onderliggende item retourneert. Dit patroon werkt op vergelijk bare wijze als het gebruik van de ingebouwde [http-trigger of actie](../connectors/connectors-native-http.md) voor het aanroepen van een onderliggende werk stroom. Onderliggende stateless werk stromen die geen antwoord actie bevatten, retour neren onmiddellijk een `202 ACCEPTED` antwoord, maar het bovenliggende item wacht tot het onderliggende item is voltooid voordat de volgende actie kan worden voortgezet. Deze gedragingen zijn alleen van toepassing op werk stromen met een onderliggend niveau.

In deze tabel wordt het gedrag van de onderliggende werk stroom opgegeven op basis van het feit of het bovenliggende en het onderliggende niveau stateful, stateless of gemengde werk stroom typen zijn:

| Bovenliggende werk stroom | Onderliggende werk stroom | Onderliggend gedrag |
|-----------------|----------------|----------------|
| Stateful | Stateful | Asynchroon of synchroon met `operationOptions=DisableSynPattern` instelling |
| Stateful | Stateless | Activeren en wachten |
| Stateless | Stateful | Synchroon |
| Stateless | Stateless | Activeren en wachten |
||||

## <a name="limits"></a>Limieten

Hoewel veel [bestaande limieten voor Azure Logic apps](../logic-apps/logic-apps-limits-and-config.md) hetzelfde zijn voor dit resource type, zijn hier de verschillen in deze open bare preview-extensie:

* Beheerde connectors: 50 aanvragen per minuut per verbinding

* Voor de actie [inline code voor Java script](../logic-apps/logic-apps-add-run-inline-code.md) -actie zijn deze limieten gewijzigd:

  * De limiet voor code tekens neemt toe van 1.024 tekens tot 100.000 tekens.

  * De tijds limiet voor het uitvoeren van de code neemt toe van vijf seconden tot 15 seconden.

<a name="unsupported"></a>

## <a name="unavailable-or-unsupported-capabilities"></a>Niet-beschik bare of niet-ondersteunde mogelijkheden

Deze mogelijkheden zijn niet beschikbaar of worden niet ondersteund voor deze open bare preview-versie:

* Het maken van de nieuwe **logische app (preview)-** resource is momenteel niet beschikbaar op Mac OS.

* Aangepaste connectors, webhook-triggers en de verschuivende venster trigger worden niet ondersteund in deze preview.

* Voor stateless logische app-werk stromen kunt u alleen acties voor [beheerde connectors](../connectors/apis-list.md#managed-api-connectors)toevoegen, niet voor triggers. Als u uw werk stroom wilt starten, gebruikt u de [ingebouwde aanvraag, Event hubs of service bus trigger](../connectors/apis-list.md#built-ins).

* In Azure Portal kunt u geen nieuwe logische apps maken met het resource type van de nieuwe **logische app (preview-versie)** . U kunt deze Logic apps alleen maken in Visual Studio code. Nadat u logische apps hebt geïmplementeerd met dit resource type van Visual Studio code naar Azure, kunt u echter [nieuwe werk stromen toevoegen aan die Logic apps](#add-workflows).

* **Verbruiks** hosting plannen worden niet ondersteund voor de implementatie van een logische app.

## <a name="next-steps"></a>Volgende stappen

We horen graag van u over uw ervaringen met deze open bare preview-extensie.

* Voor fouten of problemen [maakt u uw problemen in github](https://github.com/Azure/logicapps/issues).
* Voor vragen, aanvragen, opmerkingen en andere feedback [gebruikt u dit feedback formulier](https://aka.ms/lafeedback).