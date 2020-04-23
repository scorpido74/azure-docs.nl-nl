---
title: Achtergrondtaken uitvoeren met WebJobs
description: Meer informatie over het gebruik van WebJobs om achtergrondtaken uit te voeren in Azure App Service. Kies uit verschillende scriptindelingen en voer deze uit met CRON-expressies.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 028551f04b2e44e9456e2f7343159ad9b52fd25f
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085141"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Achtergrondtaken uitvoeren met WebJobs in Azure App Service

In dit artikel ziet u hoe u WebJobs implementeert met behulp van de [Azure-portal](https://portal.azure.com) om een uitvoerbaar of script te uploaden. Zie [WebJobs implementeren met Visual Studio](webjobs-dotnet-deploy-vs.md)voor informatie over het ontwikkelen en implementeren van WebJobs met Behulp van Visual Studio.

## <a name="overview"></a>Overzicht
WebJobs is een functie van [Azure App Service](index.yml) waarmee u een programma of script uitvoeren in hetzelfde exemplaar als een web-app, API-app of mobiele app. Er zijn geen extra kosten verbonden aan het gebruik van WebJobs.

> [!IMPORTANT]
> WebJobs wordt nog niet ondersteund voor App Service op Linux.

De Azure WebJobs SDK kan met WebJobs worden gebruikt om veel programmeertaken te vereenvoudigen. Zie [Wat is de WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)voor meer informatie.

Azure Functions biedt een andere manier om programma's en scripts uit te voeren. Zie [Kiezen tussen Stroom, Logische Apps, Functies en WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)voor een vergelijking tussen WebJobs en Functies.

## <a name="webjob-types"></a>WebJob-typen

In de volgende tabel worden de verschillen tussen *doorlopende* en *geactiveerde* WebJobs beschreven.


|Continu  |Geactiveerd  |
|---------|---------|
| Hiermee wordt onmiddellijk gestart wanneer de WebJob wordt gemaakt. Om te voorkomen dat de taak eindigt, doet het programma of script doorgaans zijn werk binnen een eindeloze lus. Als de taak eindigt, u deze opnieuw opstarten. | Start alleen wanneer deze handmatig of volgens een schema wordt geactiveerd. |
| Wordt uitgevoerd op alle exemplaren waarop de web-app wordt uitgevoerd. U de WebJob optioneel beperken tot één exemplaar. |Wordt uitgevoerd op één exemplaar dat Azure selecteert voor taakverdeling.|
| Ondersteunt foutopsporing op afstand. | Ondersteunt geen foutopsporing op afstand.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Ondersteunde bestandstypen voor scripts of programma's

De volgende bestandstypen worden ondersteund:

* .cmd, .bat, .exe (met Windows cmd)
* .ps1 (powershell gebruiken)
* .sh (met behulp van Bash)
* .php (met PHP)
* .py (met Behulp van Python)
* .js (met Behulp van Node.js)
* .jar (met Java)

## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a>Een continue WebJob maken

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Ga in de [Azure-portal](https://portal.azure.com)naar de pagina **App Service** van uw App Service-web-app, API-app of mobiele app.

2. Selecteer **WebJobs**.

   ![WebJobs selecteren](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Selecteer op de pagina **WebJobs** de optie **Toevoegen**.

    ![WebJob-pagina](./media/web-sites-create-web-jobs/wjblade.png)

3. Gebruik de **webjobinstellingen toevoegen** zoals opgegeven in de tabel.

   ![WebJob-pagina toevoegen](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Instelling      | Voorbeeldwaarde   | Beschrijving  |
   | ------------ | ----------------- | ------------ |
   | **Naam** | myContinuousWebJob | Een naam die uniek is in een App Service-app. Moet beginnen met een letter of een getal en mag geen andere speciale tekens bevatten dan "-" en "_". |
   | **Bestand uploaden** | ConsoleApp.zip | Een *.zip-bestand* dat uw uitvoerbare of scriptbestand bevat, evenals alle ondersteunende bestanden die nodig zijn om het programma of script uit te voeren. De ondersteunde uitvoerbare of scriptbestandstypen worden weergegeven in de sectie [Ondersteunde bestandstypen.](#acceptablefiles) |
   | **Type** | Continu | De [WebJob-typen](#webjob-types) worden eerder in dit artikel beschreven. |
   | **Schalen** | Meerdere instantie | Alleen beschikbaar voor Continuous WebJobs. Hiermee bepaalt u of het programma of script op alle instanties of slechts één instantie wordt uitgevoerd. De optie om op meerdere instanties uit te voeren, is niet van toepassing op de [prijzenlagen](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Gratis of Gedeeld . | 

4. Klik op **OK**.

   De nieuwe WebJob wordt weergegeven op de pagina **WebJobs.**

   ![Lijst met WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. Als u een doorlopende WebJob wilt stoppen of opnieuw wilt starten, klikt u met de rechtermuisknop op de WebJob in de lijst en klikt u op **Stoppen** of **starten**.

    ![Een continue WebJob stoppen](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a>Een handmatig geactiveerde WebJob maken

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Ga in de [Azure-portal](https://portal.azure.com)naar de pagina **App Service** van uw App Service-web-app, API-app of mobiele app.

2. Selecteer **WebJobs**.

   ![WebJobs selecteren](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Selecteer op de pagina **WebJobs** de optie **Toevoegen**.

    ![WebJob-pagina](./media/web-sites-create-web-jobs/wjblade.png)

3. Gebruik de **webjobinstellingen toevoegen** zoals opgegeven in de tabel.

   ![WebJob-pagina toevoegen](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Instelling      | Voorbeeldwaarde   | Beschrijving  |
   | ------------ | ----------------- | ------------ |
   | **Naam** | myTriggeredWebJob | Een naam die uniek is in een App Service-app. Moet beginnen met een letter of een getal en mag geen andere speciale tekens bevatten dan "-" en "_".|
   | **Bestand uploaden** | ConsoleApp.zip | Een *.zip-bestand* dat uw uitvoerbare of scriptbestand bevat, evenals alle ondersteunende bestanden die nodig zijn om het programma of script uit te voeren. De ondersteunde uitvoerbare of scriptbestandstypen worden weergegeven in de sectie [Ondersteunde bestandstypen.](#acceptablefiles) |
   | **Type** | Geactiveerd | De [WebJob-typen](#webjob-types) worden eerder in dit artikel beschreven. |
   | **Triggers** | Handmatig | |

4. Klik op **OK**.

   De nieuwe WebJob wordt weergegeven op de pagina **WebJobs.**

   ![Lijst met WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Als u de WebJob wilt uitvoeren, klikt u met de rechtermuisknop op de naam in de lijst en klikt u op **Uitvoeren**.
   
    ![WebJob uitvoeren](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a>Een geplande WebJob maken

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. Ga in de [Azure-portal](https://portal.azure.com)naar de pagina **App Service** van uw App Service-web-app, API-app of mobiele app.

2. Selecteer **WebJobs**.

   ![WebJobs selecteren](./media/web-sites-create-web-jobs/select-webjobs.png)

2. Selecteer op de pagina **WebJobs** de optie **Toevoegen**.

   ![WebJob-pagina](./media/web-sites-create-web-jobs/wjblade.png)

3. Gebruik de **webjobinstellingen toevoegen** zoals opgegeven in de tabel.

   ![WebJob-pagina toevoegen](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Instelling      | Voorbeeldwaarde   | Beschrijving  |
   | ------------ | ----------------- | ------------ |
   | **Naam** | myScheduledWebJob | Een naam die uniek is in een App Service-app. Moet beginnen met een letter of een getal en mag geen andere speciale tekens bevatten dan "-" en "_". |
   | **Bestand uploaden** | ConsoleApp.zip | Een *.zip-bestand* dat uw uitvoerbare of scriptbestand bevat, evenals alle ondersteunende bestanden die nodig zijn om het programma of script uit te voeren. De ondersteunde uitvoerbare of scriptbestandstypen worden weergegeven in de sectie [Ondersteunde bestandstypen.](#acceptablefiles) |
   | **Type** | Geactiveerd | De [WebJob-typen](#webjob-types) worden eerder in dit artikel beschreven. |
   | **Triggers** | Gepland | Schakel de functie Altijd in om de planning betrouwbaar te laten werken. Always On is alleen beschikbaar in de prijzen voor basis-, standaard- en premiumprijzen.|
   | **CRON-expressie** | 0 0/20 * * * * | [CRON-expressies](#ncrontab-expressions) worden beschreven in de volgende sectie. |

4. Klik op **OK**.

   De nieuwe WebJob wordt weergegeven op de pagina **WebJobs.**

   ![Lijst met WebJobs](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>NCRONTAB-expressies

U een [NCRONTAB-expressie](../azure-functions/functions-bindings-timer.md#ncrontab-expressions) invoeren `settings.job` in de portal of een bestand opnemen aan de hoofdmap van uw WebJob *.zip-bestand,* zoals in het volgende voorbeeld:

```json
{
    "schedule": "0 */15 * * * *"
}
```

Zie [Een geactiveerde WebJob plannen voor](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)meer informatie.

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="view-the-job-history"></a><a name="ViewJobHistory"></a>Bekijk de functiegeschiedenis

1. Selecteer de WebJob waarvoor u de geschiedenis wilt zien en selecteer vervolgens de knop **Logboeken.**
   
   ![Knop Logboeken](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. Selecteer op de pagina **WebJob-details** een tijd om details voor één keer te bekijken.
   
   ![WebJob-gegevens](./media/web-sites-create-web-jobs/webjobdetails.png)

3. Selecteer op de pagina **Details van WebJob Run** de optie Uitvoer **schakelen** om de tekst van de logboekinhoud weer te geven.
   
    ![Details van webtaakuitvoeren](./media/web-sites-create-web-jobs/webjobrundetails.png)

   Als u de uitvoertekst in een apart browservenster wilt bekijken, selecteert u **Download**. Als u de tekst zelf wilt downloaden, klikt u met de rechtermuisknop op **downloaden** en gebruikt u uw browseropties om de inhoud van het bestand op te slaan.
   
5. Selecteer de koppeling **WebJobs-broodkruimel** boven aan de pagina om naar een lijst met WebJobs te gaan.

    ![WebJob broodkruimel](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![Lijst met WebJobs in het geschiedenisdashboard](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="next-steps"></a><a name="NextSteps"></a>Volgende stappen

De Azure WebJobs SDK kan met WebJobs worden gebruikt om veel programmeertaken te vereenvoudigen. Zie [Wat is de WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)voor meer informatie.
