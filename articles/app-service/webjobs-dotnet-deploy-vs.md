---
title: Webjobs ontwikkelen en implementeren met behulp van VS
description: Meer informatie over het ontwikkelen van Azure WebJobs in Visual Studio en het implementeren ervan naar Azure App Service, inclusief het maken van een geplande taak.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: cf2437443350cf9b21b6abc0d0dd1dbd5d22e41f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083188"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Webtaken ontwikkelen en implementeren met behulp van Visual Studio - Azure App Service

In dit artikel wordt uitgelegd hoe u met Visual Studio een console toepassings project implementeert in een web-app in [app service](overview.md) als een [Azure-Webtaak](https://go.microsoft.com/fwlink/?LinkId=390226). Zie [achtergrond taken uitvoeren met Webjobs](webjobs-create.md)voor meer informatie over het implementeren van webjobs met behulp van de [Azure Portal](https://portal.azure.com).

U kunt meerdere webjobs publiceren naar één web-app. Zorg ervoor dat elke Webtaak in een web-app een unieke naam heeft.

Met versie 3. x van de [Azure WEBJOBS SDK](webjobs-sdk-how-to.md) kunt u webjobs ontwikkelen die worden uitgevoerd als .net core-apps of .NET Framework-apps, terwijl versie 2. x alleen de .NET Framework ondersteunt. De manier waarop u een webjobs-project implementeert, verschilt voor .NET core-projecten versus .NET Framework.

## <a name="webjobs-as-net-core-console-apps"></a>Webjobs als .NET core-console-apps

Wanneer u versie 3. x van de webjobs gebruikt, kunt u webjobs maken en publiceren als .NET core console-apps. Zie [aan de slag met de Azure WEBJOBS SDK voor op gebeurtenissen gebaseerde achtergrond verwerking](webjobs-sdk-get-started.md)voor stapsgewijze instructies voor het maken en publiceren van een .net core-console toepassing naar Azure als Webtaak.

> [!NOTE]
> .NET core-webjobs kunnen niet worden gekoppeld aan webprojecten. Als u uw Webtaak wilt implementeren met een web-app, moet u [uw Webtaak maken als .NET Framework-console-app](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Implementeren naar Azure App Service

Het publiceren van een .NET core-Webtaak naar App Service vanuit Visual Studio maakt gebruik van dezelfde hulp middelen als voor het publiceren van een ASP.NET Core-app.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>Webtaak typen

Een Webtaak die is gepubliceerd vanuit een .NET Core-Console project, wordt standaard alleen uitgevoerd wanneer deze is geactiveerd of op aanvraag. U kunt het project ook bijwerken om [uit te voeren volgens een planning](#scheduled-execution) of continu uitvoeren.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Geplande uitvoering

Wanneer u een .NET core-console toepassing naar Azure publiceert, worden er een nieuwe *instellingen gemaakt. taak* bestand wordt toegevoegd aan het project. Gebruik dit bestand om een uitvoerings schema voor uw Webtaak in te stellen. Zie [een geactiveerde Webtaak plannen](#scheduling-a-triggered-webjob)voor meer informatie.

#### <a name="continuous-execution"></a>Doorlopende uitvoering

U kunt Visual Studio gebruiken om de Webtaak te wijzigen zodat deze continu wordt uitgevoerd wanneer altijd aan is ingeschakeld in Azure.

1. Als u dit nog niet hebt gedaan, [kunt u het project publiceren naar Azure](#deploy-to-azure-app-service).

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Kies op het tabblad **publiceren** de optie **instellingen**. 

1. Kies in het dialoog venster **Profiel instellingen** **doorlopend** voor **type Webtaak**en kies **Opslaan**.

    ![Dialoog venster publicatie-instellingen voor een Webtaak](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selecteer **publiceren** om de Webtaak opnieuw te publiceren met de bijgewerkte instellingen.

## <a name="webjobs-as-net-framework-console-apps"></a>Webjobs als .NET Framework-console-apps  

Wanneer in Visual Studio een .NET Framework console toepassings project wordt geïmplementeerd, worden runtime bestanden gekopieerd naar de juiste map in de web-app (*App_Data/Jobs/Continuous* voor doorlopend webjobs en *App_Data/Jobs/triggered* voor geplande of on-demand webjobs).

Aan een project met webjobs zijn de volgende items toegevoegd:

* Het NuGet-pakket [micro soft. Web. webjobs. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) .
* Een [webjob-publish-settings.jsin](#publishsettings) het bestand met instellingen voor implementatie en scheduler. 

![Diagram waarin wordt weer gegeven wat er wordt toegevoegd aan een console-app om implementatie als Webtaak in te scha kelen](./media/webjobs-dotnet-deploy-vs/convert.png)

U kunt deze items toevoegen aan een bestaand console toepassings project of een sjabloon gebruiken om een nieuw console toepassings project met webjobs te maken. 

U kunt een project zelf als Webtaak implementeren of koppelen aan een webproject, zodat het automatisch wordt geïmplementeerd wanneer u het webproject implementeert. Voor het koppelen van projecten bevat Visual Studio de naam van het project met webtaken in een [webjobs-list.jsvoor](#webjobslist) het bestand in het webproject.

![Diagram van het project taak koppeling naar webproject wordt weer gegeven](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Vereisten

Als u Visual Studio 2015 gebruikt, installeert u de [Azure SDK voor .net (Visual Studio 2015)](https://azure.microsoft.com/downloads/).

Als u Visual Studio 2017 gebruikt, installeert u de [werk belasting Azure Development](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>De implementatie van webjobs inschakelen voor een bestaand console toepassings project

U hebt hiervoor twee opties:

* [Automatische implementatie met een webproject inschakelen](#convertlink).

  Configureer een bestaand console toepassings project, zodat het automatisch als Webtaak wordt geïmplementeerd wanneer u een webproject implementeert. Gebruik deze optie wanneer u uw Webtaak wilt uitvoeren in dezelfde Web-app waarin u de gerelateerde webtoepassing uitvoert.

* [Implementatie inschakelen zonder een webproject](#convertnolink).

  Configureer een bestaand console toepassings project om als Webtaak te implementeren, zonder een koppeling naar een webproject. Gebruik deze optie als u een Webtaak zelf wilt uitvoeren in een web-app, zonder dat er een webtoepassing in de web-app wordt uitgevoerd. U kunt dit doen om de resources van uw webtoepassing onafhankelijk van uw webtoepassingsgegevens te schalen.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a>Automatische implementatie van webjobs met een webproject inschakelen

1. Klik met de rechter muisknop op het webproject in **Solution Explorer**en klik vervolgens op **Add**  >  **bestaand project als Azure-Webtaak**toevoegen.
   
    ![Bestaand project als Azure-Webtaak](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Het dialoog venster [Azure-Webtaak toevoegen](#configure) wordt weer gegeven.
2. Selecteer in de vervolg keuzelijst **project naam** het project van de console toepassing dat u als Webtaak wilt toevoegen.
   
    ![Het project selecteren in het dialoog venster Azure-Webtaak toevoegen](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Voltooi het dialoog venster [Azure-Webtaak toevoegen](#configure) en klik vervolgens op **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a>De implementatie van webjobs zonder een webproject inschakelen
1. Klik met de rechter muisknop op het console toepassings project in **Solution Explorer**en klik vervolgens op **publiceren als Azure-Webtaak...**. 
   
    ![Publiceren als Azure-Webtaak](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Het dialoog venster [Azure-Webtaak toevoegen](#configure) wordt weer gegeven, met het project geselecteerd in het vak **project naam** .
2. Voltooi het dialoog venster [Azure-Webtaak toevoegen](#configure) en klik vervolgens op **OK**.
   
   De wizard **Publish Web** wordt weer gegeven.  Als u niet onmiddellijk wilt publiceren, sluit u de wizard. De instellingen die u hebt ingevoerd, worden opgeslagen voor wanneer u [het project wilt implementeren](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Een nieuw project maken dat is ingeschakeld voor webjobs
Als u een nieuw project met webjobs wilt maken, kunt u de project sjabloon console toepassing gebruiken en de implementatie van webtaken inschakelen, zoals wordt uitgelegd in [de vorige sectie](#convert). Als alternatief kunt u de sjabloon webjobs nieuw-project gebruiken:

* [De sjabloon webjobs New-Project gebruiken voor een onafhankelijke Webtaak](#createnolink)
  
    Een project maken en configureren voor implementatie door zichzelf als Webtaak, zonder een koppeling naar een webproject. Gebruik deze optie als u een Webtaak zelf wilt uitvoeren in een web-app, zonder dat er een webtoepassing in de web-app wordt uitgevoerd. U kunt dit doen om de resources van uw webtoepassing onafhankelijk van uw webtoepassingsgegevens te schalen.
* [De sjabloon webjobs New-Project gebruiken voor een Webtaak die is gekoppeld aan een webproject](#createlink)
  
    Een project maken dat is geconfigureerd om automatisch te worden geïmplementeerd als Webtaak wanneer een webproject in dezelfde oplossing wordt geïmplementeerd. Gebruik deze optie wanneer u uw Webtaak wilt uitvoeren in dezelfde Web-app waarin u de gerelateerde webtoepassing uitvoert.

> [!NOTE]
> De webjobs New-Project-sjabloon installeert automatisch NuGet-pakketten en bevat code in *Program.cs* voor de [webjobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Als u de webjobs SDK niet wilt gebruiken, verwijdert u de `host.RunAndBlock` instructie in *Program.cs*of wijzigt u deze.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a>De sjabloon webjobs New-Project gebruiken voor een onafhankelijke Webtaak
1. Klik op **bestand**  >  **Nieuw project**en klik in het dialoog venster **Nieuw project** op **Cloud**  >  **Azure Webtaak (.NET Framework)**.
   
    ![Dialoog venster Nieuw project met websjabloon](./media/webjobs-dotnet-deploy-vs/np.png)
2. Volg de instructies die eerder worden weer gegeven om [de console toepassing een onafhankelijk Webjobs-project te maken](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a>De sjabloon webjobs New-Project gebruiken voor een Webtaak die is gekoppeld aan een webproject
1. Klik met de rechter muisknop op het webproject in **Solution Explorer**en klik vervolgens op **Add**  >  **Nieuw Azure-project voor Webtaak**toevoegen.
   
    ![Nieuw menu-item van Azure Webtaak-project](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Het dialoog venster [Azure-Webtaak toevoegen](#configure) wordt weer gegeven.
2. Voltooi het dialoog venster [Azure-Webtaak toevoegen](#configure) en klik vervolgens op **OK**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Het dialoog venster Azure-Webtaak toevoegen
In het dialoog venster **Azure-Webtaak toevoegen** kunt u de naam van de Webtaak en de instelling voor de uitvoerings modus voor uw Webtaak invoeren. 

![Dialoog venster Azure-Webtaak toevoegen](./media/webjobs-dotnet-deploy-vs/aaw2.png)

De velden in dit dialoog venster komen overeen met velden in het dialoog venster **Webtaak toevoegen** van de Azure Portal. Zie [achtergrond taken uitvoeren met Webjobs](webjobs-create.md)voor meer informatie.

> [!NOTE]
> * Zie [opdracht regel of continue levering van Azure WebJobs inschakelen](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)voor meer informatie over de implementatie van de opdracht regel.
> * Als u een Webtaak implementeert en vervolgens besluit dat u het type Webtaak wilt wijzigen en opnieuw wilt implementeren, moet u de *webjobs-publish-settings.jsin* het bestand verwijderen. Hierdoor worden de publicatie opties opnieuw weer gegeven in Visual Studio, zodat u het type Webtaak kunt wijzigen.
> * Als u een Webtaak implementeert en later de uitvoerings modus wijzigt van doorlopend naar niet-doorlopend of omgekeerd, maakt Visual Studio een nieuwe Webtaak in azure wanneer u de implementatie opnieuw uitvoert. Als u andere plannings instellingen wijzigt, maar de uitvoerings modus hetzelfde verlaat of overschakelt tussen gepland en op aanvraag, wordt de bestaande taak in Visual Studio bijgewerkt in plaats van een nieuwe te maken.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.jsop
Wanneer u een console toepassing voor webjobs-implementatie configureert, installeert Visual Studio het [micro soft. Web. webjobs. Publiceer](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) het NuGet-pakket en slaat de plannings gegevens op in een *webjob-publish-settings.js* in een bestand in de map project *Eigenschappen* van het webjobs-project. Hier volgt een voor beeld van dat bestand:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

U kunt dit bestand rechtstreeks bewerken en Visual Studio biedt IntelliSense. Het bestands schema wordt opgeslagen op [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) en kan daar worden weer gegeven.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.jsop
Wanneer u een project met webjobs koppelt aan een webproject, slaat Visual Studio de naam van het webjobs-project op in een *webjobs-list.js* in het bestand in de map *Eigenschappen* van het webproject. De lijst bevat mogelijk meerdere webjobs-projecten, zoals wordt weer gegeven in het volgende voor beeld:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
  "WebJobs": [
    {
      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
    },
    {
      "filePath": "../WebJob1/WebJob1.csproj"
    }
  ]
}
```

U kunt dit bestand rechtstreeks bewerken en Visual Studio biedt IntelliSense. Het bestands schema wordt opgeslagen op [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) en kan daar worden weer gegeven.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Een webjobs-project implementeren
Een webjobs-project dat u hebt gekoppeld aan een webproject, wordt automatisch geïmplementeerd met het webproject. Zie voor meer informatie over de implementatie van webprojecten de **procedures voor**het  >  **implementeren van apps** in de linkernavigatiebalk.

Als u een webjobs-project zelf wilt implementeren, klikt u met de rechter muisknop op het project in **Solution Explorer** en klikt u op **publiceren als Azure-Webtaak...**. 

![Publiceren als Azure-Webtaak](./media/webjobs-dotnet-deploy-vs/paw.png)

Voor een onafhankelijke Webtaak wordt dezelfde wizard **Publish Web** die wordt gebruikt voor webprojecten weer gegeven, maar er zijn minder instellingen beschikbaar die kunnen worden gewijzigd.

## <a name="scheduling-a-triggered-webjob"></a>Een geactiveerde webtaak plannen

Webjobs gebruikt een *Settings. Job* -bestand om te bepalen wanneer een Webtaak wordt uitgevoerd. Gebruik dit bestand om een uitvoerings schema voor uw Webtaak in te stellen. In het volgende voor beeld wordt elk uur van 9 tot 5 uur uitgevoerd:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Dit bestand moet zich in de hoofdmap van de map webjobs bevinden, naast het script van uw Webtaak, zoals `wwwroot\app_data\jobs\triggered\{job name}` of `wwwroot\app_data\jobs\continuous\{job name}` . Wanneer u een Webtaak implementeert vanuit Visual Studio, markeert u de `settings.job` Bestands eigenschappen als een **kopie indien nieuwer**. 

Wanneer u [een Webtaak maakt op basis van de Azure Portal](webjobs-create.md), wordt het bestand settings. job voor u gemaakt.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON-expressies

Webjobs maakt gebruik van dezelfde CRON-expressies voor planning als de trigger timer in Azure Functions. Zie [Timer trigger voor Azure functions voor](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)meer informatie over cron-ondersteuning.

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>Naslag informatie over Settings. job

De volgende instellingen worden ondersteund door webjobs:

| **Instelling** | **Type**  | **Beschrijving** |
| ----------- | --------- | --------------- |
| `is_in_place` | Alles | Hiermee kan de taak worden uitgevoerd in plaats zonder dat deze eerst naar een tijdelijke map wordt gekopieerd. Zie [werkmap voor webtaken](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)voor meer informatie. |
| `is_singleton` | Continu | Voer de webjobs alleen uit op één instantie wanneer deze is uitgeschaald. Zie [een continue taak als Singleton instellen](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)voor meer informatie. |
| `schedule` | Geactiveerd | Voer de Webtaak uit op basis van een CRON schema. Zie het [referentie artikel timer trigger](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)voor meer informatie. |
| `stopping_wait_time`| Alles | Hiermee staat u het beheer van het afsluit gedrag toe. Zie [correct afsluiten](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)voor meer informatie. |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de webjobs SDK](webjobs-sdk-how-to.md)
