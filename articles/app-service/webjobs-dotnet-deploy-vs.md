---
title: WebJobs ontwikkelen en implementeren met VS
description: Meer informatie over het ontwikkelen van Azure WebJobs in Visual Studio en het implementeren ervan naar Azure App Service, inclusief het maken van een geplande taak.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: vs-azure
ms.date: 02/18/2019
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: 75e1f5fbfa41ac310d2a737dcfe28199715a094b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312884"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio---azure-app-service"></a>Webtaken ontwikkelen en implementeren met behulp van Visual Studio - Azure App Service

In dit artikel wordt uitgelegd hoe u Visual Studio gebruiken om een consoletoepassingsproject te implementeren in een web-app in [App Service](overview.md) als [Azure WebJob.](https://go.microsoft.com/fwlink/?LinkId=390226) Zie [Achtergrondtaken uitvoeren met WebJobs](webjobs-create.md)voor informatie over het implementeren van WebJobs met behulp van de [Azure-portal.](https://portal.azure.com)

U meerdere WebJobs publiceren in één web-app. Zorg ervoor dat elke WebJob in een web-app een unieke naam heeft.

Met versie 3.x van de [Azure WebJobs SDK](webjobs-sdk-how-to.md) u WebJobs ontwikkelen die worden uitgevoerd als .NET Core-apps of .NET Framework-apps, terwijl versie 2.x alleen het .NET Framework ondersteunt. De manier waarop u een WebJobs-project implementeert, is anders voor .NET Core-projecten versus .NET Framework-projecten.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs als .NET Core-console-apps

Wanneer u versie 3.x van webjobs gebruikt, u WebJobs maken en publiceren als .NET Core-console-apps. Zie [Aan de slag met de Azure WebJobs SDK voor gebeurtenisgestuurde achtergrondverwerking](webjobs-sdk-get-started.md)voor het maken en publiceren van stapsgewijze instructies voor het maken en publiceren van een .NET Core-consoletoepassing in Azure als webjob.

> [!NOTE]
> .NET Core WebJobs kan niet worden gekoppeld aan webprojecten. Als u uw WebJob met een web-app moet implementeren, moet u [uw WebJob maken als een .NET Framework-console-app](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Implementeren naar Azure App Service

Het publiceren van een .NET Core WebJob naar App Service van Visual Studio gebruikt dezelfde tooling als het publiceren van een ASP.NET Core-app.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

### <a name="webjob-types"></a>WebJob-typen

Standaard wordt een WebJob die is gepubliceerd vanuit een .NET Core-consoleproject alleen uitgevoerd wanneer deze wordt geactiveerd of op aanvraag. U het project ook bijwerken om [een planning](#scheduled-execution) uit te voeren of continu uit te voeren.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

#### <a name="scheduled-execution"></a>Geplande uitvoering

Wanneer u een .NET Core-consoletoepassing publiceert in Azure, wordt een nieuw *bestand instellingen.job* toegevoegd aan het project. Gebruik dit bestand om een uitvoeringsschema voor uw WebJob in te stellen. Zie [Een geactiveerde WebJob plannen voor](#scheduling-a-triggered-webjob)meer informatie.

#### <a name="continuous-execution"></a>Continue uitvoering

U Visual Studio gebruiken om de WebJob te wijzigen om continu te worden uitgevoerd wanneer Always On is ingeschakeld in Azure.

1. Als u dit nog niet hebt gedaan, [publiceert u het project naar Azure.](#deploy-to-azure-app-service)

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Publiceren**.

1. Kies Op het tabblad **Publiceren** de optie **Instellingen**. 

1. Kies in het dialoogvenster **Profielinstellingen** de optie **Continu** voor **WebJob-type**en kies **Opslaan**.

    ![Dialoogvenster Publicatie-instellingen voor een WebJob](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Selecteer **Publiceren** om de WebJob opnieuw te publiceren met de bijgewerkte instellingen.

## <a name="webjobs-as-net-framework-console-apps"></a>WebJobs als .NET Framework console-apps  

Wanneer Visual Studio een WebJobs-project met .NET Framework Console Application implementeert, worden runtime-bestanden gekopieerd naar de juiste map in de web-app *(App_Data/taken/continu* voor continue WebJobs en *App_Data/taken/geactiveerd* voor geplande of on-demand WebJobs).

Aan een project met WebJobs zijn de volgende items toegevoegd:

* Het [NuGet-pakket microsoft.web.webjobs.publish.](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/)
* Een [webjob-publish-settings.json-bestand](#publishsettings) met instellingen voor implementatie en planninger. 

![Diagram met wat is toegevoegd aan een console-app om implementatie als WebJob in te schakelen](./media/webjobs-dotnet-deploy-vs/convert.png)

U deze items toevoegen aan een bestaand consoletoepassingsproject of een sjabloon gebruiken om een nieuw consoletoepassingsproject met WebJobs te maken. 

U een project zelf implementeren als WebJob of koppelen aan een webproject, zodat het automatisch wordt geïmplementeerd wanneer u het webproject implementeert. Als u projecten wilt koppelen, bevat Visual Studio de naam van het webjobs-project in een [webjobs-list.json-bestand](#webjobslist) in het webproject.

![Diagram met WebJob-project koppeling naar webproject](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Vereisten

Als u Visual Studio 2015 gebruikt, installeert u de [Azure SDK voor .NET (Visual Studio 2015).](https://azure.microsoft.com/downloads/)

Als u Visual Studio 2017 gebruikt, installeert u de [azure-ontwikkelworkload](https://docs.microsoft.com/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-application-project"></a><a id="convert"></a>WebJobs-implementatie inschakelen voor een bestaand consoletoepassingsproject

U hebt hiervoor twee opties:

* [Automatische implementatie inschakelen met een webproject](#convertlink).

  Configureer een bestaand consoletoepassingsproject zodat het automatisch wordt geïmplementeerd als webjob wanneer u een webproject implementeert. Gebruik deze optie wanneer u uw WebJob wilt uitvoeren in dezelfde web-app waarin u de gerelateerde webtoepassing uitvoert.

* [Implementatie inschakelen zonder webproject](#convertnolink).

  Een bestaand consoletoepassingsproject configureren om zelf als WebJob te worden geïmplementeerd, zonder koppeling naar een webproject. Gebruik deze optie wanneer u een WebJob in een web-app zelf wilt uitvoeren, zonder dat er een webtoepassing wordt uitgevoerd in de web-app. U dit doen om uw WebJob-bronnen onafhankelijk van uw webtoepassingsbronnen te kunnen schalen.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a>Automatische WebJobs-implementatie inschakelen met een webproject

1. Klik met de rechtermuisknop op het webproject in **Solution Explorer**en klik vervolgens op Bestaand project **toevoegen** > **als Azure WebJob**.
   
    ![Bestaand project als Azure WebJob](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Het dialoogvenster [Azure WebJob toevoegen](#configure) wordt weergegeven.
2. Selecteer in de vervolgkeuzelijst **Projectnaam** het project Consoletoepassing dat u als WebJob wilt toevoegen.
   
    ![Project selecteren in dialoogvenster Azure WebJob toevoegen](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Vul het dialoogvenster [Azure WebJob toevoegen in](#configure) en klik op **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a>WebJobs-implementatie inschakelen zonder webproject
1. Klik met de rechtermuisknop op het consoletoepassingsproject in **Solution Explorer**en klik vervolgens op Publiceren als **Azure WebJob...**. 
   
    ![Publiceren als Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Het dialoogvenster [Azure WebJob toevoegen](#configure) wordt weergegeven, waarbij het project is geselecteerd in het vak **Projectnaam.**
2. Vul het dialoogvenster [Azure WebJob toevoegen](#configure) in en klik op **OK**.
   
   De wizard **Web publiceren** wordt weergegeven.  Als u niet onmiddellijk wilt publiceren, sluit u de wizard. De instellingen die u hebt ingevoerd, worden opgeslagen voor wanneer u het project wilt [implementeren.](#deploy)

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Een nieuw webjobs-project maken
Als u een nieuw webjobs-project wilt maken, u de projectsjabloon Console-toepassing gebruiken en webjobs-implementatie inschakelen zoals uitgelegd in [de vorige sectie](#convert). Als alternatief u de webjobs-sjabloon voor nieuw project gebruiken:

* [De webjobs-sjabloon voor nieuw project gebruiken voor een onafhankelijke WebJob](#createnolink)
  
    Maak een project en configureer het om zelf te implementeren als webjob, zonder koppeling naar een webproject. Gebruik deze optie wanneer u een WebJob in een web-app zelf wilt uitvoeren, zonder dat er een webtoepassing wordt uitgevoerd in de web-app. U dit doen om uw WebJob-bronnen onafhankelijk van uw webtoepassingsbronnen te kunnen schalen.
* [De webprojectsjabloon WebJobs gebruiken voor een WebJob die is gekoppeld aan een webproject](#createlink)
  
    Maak een project dat is geconfigureerd om automatisch te implementeren als webjob wanneer een webproject in dezelfde oplossing wordt geïmplementeerd. Gebruik deze optie wanneer u uw WebJob wilt uitvoeren in dezelfde web-app waarin u de gerelateerde webtoepassing uitvoert.

> [!NOTE]
> De webjobs-sjabloon voor nieuw projecten installeert automatisch NuGet-pakketten en bevat code in *Program.cs* voor de [WebJobs SDK.](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs) Als u de WebJobs SDK niet wilt gebruiken, `host.RunAndBlock` verwijdert of wijzigt u de instructie in *Program.cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a>De webjobs-sjabloon voor nieuw project gebruiken voor een onafhankelijke WebJob
1. Klik **op Nieuw** > **project bestand**en klik vervolgens in het dialoogvenster Nieuw **project** op **Cloud** > Azure**WebJob (.NET Framework).**
   
    ![Dialoogvenster Nieuw project met webjobsjabloon](./media/webjobs-dotnet-deploy-vs/np.png)
2. Volg de eerder weergegeven aanwijzingen om [van het consoletoepassingsproject een onafhankelijk WebJobs-project](#convertnolink)te maken.

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a>De webprojectsjabloon WebJobs gebruiken voor een WebJob die is gekoppeld aan een webproject
1. Klik met de rechtermuisknop op het webproject in **Solution Explorer**en klik vervolgens op Nieuw**Azure WebJob-project** **toevoegen** > .
   
    ![Nieuwe Azure WebJob Project-menu-invoer](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Het dialoogvenster [Azure WebJob toevoegen](#configure) wordt weergegeven.
2. Vul het dialoogvenster [Azure WebJob toevoegen](#configure) in en klik op **OK**.

### <a name="the-add-azure-webjob-dialog"></a><a id="configure"></a>Het dialoogvenster Azure WebJob toevoegen
Met het dialoogvenster **Azure WebJob toevoegen** u de webjobnaam en de modus-instelling voor uw WebJob invoeren. 

![Dialoogvenster Azure WebJob toevoegen](./media/webjobs-dotnet-deploy-vs/aaw2.png)

De velden in dit dialoogvenster komen overeen met velden in het dialoogvenster **WebJob toevoegen** van de Azure-portal. Zie [Achtergrondtaken uitvoeren met WebJobs](webjobs-create.md)voor meer informatie.

> [!NOTE]
> * Zie [Opdrachtregel inschakelen of Continue levering van Azure WebJobs voor](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/)informatie over de implementatie van opdrachtregel .
> * Als u een WebJob implementeert en vervolgens besluit dat u het type WebJob wilt wijzigen en opnieuw wilt implementeren, moet u het bestand *webjobs-publish-settings.json* verwijderen. Hierdoor toont Visual Studio de publicatieopties opnieuw, zodat u het type WebJob wijzigen.
> * Als u een WebJob implementeert en later de runmode wijzigt van continu naar niet-continu of vice versa, maakt Visual Studio een nieuwe WebJob in Azure wanneer u opnieuw wordt geïmplementeerd. Als u andere planningsinstellingen wijzigt, maar de uitvoeringsmodus hetzelfde laat staan of schakelt tussen Gepland en On Demand, werkt Visual Studio de bestaande taak bij in plaats van een nieuwe taak te maken.
> 
> 

### <a name="webjob-publish-settingsjson"></a><a id="publishsettings"></a>webjob-publish-settings.json
Wanneer u een consoletoepassing voor webjobs-implementatie configureert, installeert Visual Studio het NuGet-pakket [Microsoft.WebJobs.Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet en slaat u planningsgegevens op in een *webjob-publish-settings.json-bestand* in de map *ProjectEigenschappen* van het WebJobs-project. Hier is een voorbeeld van dat bestand:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "null",
          "endTime": "null",
          "jobRecurrenceFrequency": "null",
          "interval": null,
          "runMode": "Continuous"
        }

U dit bestand rechtstreeks bewerken en Visual Studio biedt IntelliSense. Het bestandsschema wordt [https://schemastore.org](https://schemastore.org/schemas/json/webjob-publish-settings.json) opgeslagen en kan daar worden bekeken.  

### <a name="webjobs-listjson"></a><a id="webjobslist"></a>webjobs-list.json
Wanneer u een project met WebJobs koppelt aan een webproject, slaat Visual Studio de naam van het WebJobs-project op in een *webjobs-list.json-bestand* in de map *Eigenschappen* van het webproject. De lijst kan meerdere WebJobs-projecten bevatten, zoals in het volgende voorbeeld wordt weergegeven:

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

U dit bestand rechtstreeks bewerken en Visual Studio biedt IntelliSense. Het bestandsschema wordt [https://schemastore.org](https://schemastore.org/schemas/json/webjobs-list.json) opgeslagen en kan daar worden bekeken.

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Een WebJobs-project implementeren
Een WebJobs-project dat u aan een webproject hebt gekoppeld, wordt automatisch geïmplementeerd in het webproject. Zie **Handleidingen** > **voor implementatie van implementatie van webprojecten** in de linkernavigatie voor informatie over de implementatie van webprojecten.

Als u een WebJobs-project zelf wilt implementeren, klikt u met de rechtermuisknop op het project in **Solution Explorer** en klikt u op Publiceren als **Azure WebJob...**. 

![Publiceren als Azure WebJob](./media/webjobs-dotnet-deploy-vs/paw.png)

Voor een onafhankelijke WebJob wordt dezelfde wizard **Web publiceren** weergegeven die wordt gebruikt voor webprojecten, maar met minder instellingen beschikbaar om te wijzigen.

## <a name="scheduling-a-triggered-webjob"></a>Een geactiveerde webtaak plannen

WebJobs gebruikt een *bestand settings.job* om te bepalen wanneer een WebJob wordt uitgevoerd. Gebruik dit bestand om een uitvoeringsschema voor uw WebJob in te stellen. Het volgende voorbeeld loopt elk uur van 9.00 tot 17.00 uur:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Dit bestand moet zich aan de hoofdmap van de map WebJobs bevinden, samen met het script van uw WebJob, zoals `wwwroot\app_data\jobs\triggered\{job name}` of `wwwroot\app_data\jobs\continuous\{job name}`. Wanneer u een WebJob implementeert `settings.job` vanuit Visual Studio, markeert u de bestandseigenschappen als **Kopiëren als nieuwer**. 

Wanneer u [een WebJob maakt vanuit de Azure-portal,](webjobs-create.md)wordt het bestand settings.job voor u gemaakt.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="cron-expressions"></a>CRON-expressies

WebJobs gebruikt dezelfde CRON-expressies voor het plannen als de timertrigger in Azure-functies. Zie het [verwijzingsartikel timertrigger](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)voor meer informatie over CRON-ondersteuning.

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

### <a name="settingsjob-reference"></a>settings.job reference

De volgende instellingen worden ondersteund door WebJobs:

| **Instelling** | **Type**  | **Beschrijving** |
| ----------- | --------- | --------------- |
| `is_in_place` | Alle | Hiermee kan de taak worden uitgevoerd zonder eerst naar een tijdelijke map te worden gekopieerd. Zie [WebJobs werkmap voor](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory)meer informatie. |
| `is_singleton` | Continu | Voer de WebJobs alleen uit op één exemplaar wanneer deze wordt uitgeschaald. Zie [Een doorlopende taak instellen als singleton voor](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)meer informatie. |
| `schedule` | Geactiveerd | Voer de WebJob uit volgens een CRON-schema. Zie het [verwijzingsartikel timertrigger](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)voor meer informatie. |
| `stopping_wait_time`| Alle | Hiermee u het afsluitgedrag beheren. Zie [Sierlijke afsluiting](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown)voor meer informatie. |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de WebJobs SDK](webjobs-sdk-how-to.md)
