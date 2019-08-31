---
title: Een QnA Maker-service instellen-QnA Maker
titleSuffix: Azure Cognitive Services
description: Voordat u een QnA Maker knowledge bases maken kunt, moet u eerst een QnA Maker-service in Azure instellen. Iedereen met autorisatie voor het maken van nieuwe resources in een abonnement kunt een QnA Maker-service instellen.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec154a7e437621f377c503642b186cf166016cc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195316"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker-resources beheren

Voordat u een QnA Maker knowledge bases maken kunt, moet u eerst een QnA Maker-service in Azure instellen. Iedereen met autorisatie voor het maken van nieuwe resources in een abonnement kunt een QnA Maker-service instellen.

## <a name="types-of-keys-in-qna-maker"></a>Typen sleutels in QnA Maker

Uw service QnA Maker omgaat met twee soorten sleutels, **abonnementssleutels** en **eindpunt sleutels**.

![Sleutelbeheer](../media/qnamaker-how-to-key-management/key-management.png)

|Name|Location|Doel|
|--|--|--|
|Abonnementssleutel|[Azure-portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Deze sleutels worden gebruikt voor toegang tot de Api's van de [QnA Maker Management-service](https://go.microsoft.com/fwlink/?linkid=2092179). Met deze Api's kunt u de vragen en antwoorden in uw Knowledge Base bewerken en uw kennis database publiceren. Deze sleutels worden gemaakt wanneer u een nieuwe QnA Maker-service maakt.<br><br>Deze sleutels vindt u op de pagina **sleutels** van de **Cognitive Services** resource.|
|Eindpunt sleutel|[QnA Maker Portal](http://www.qnamaker.ai)|Deze sleutels worden gebruikt om toegang te krijgen tot het gepubliceerde Knowledge Base-eind punt om een antwoord te krijgen op de vraag van een gebruiker. Normaal gesp roken gebruikt u dit eind punt in uw chat-bot of client toepassings code die verbinding maakt met de QnA Maker-service. Deze sleutels worden gemaakt wanneer u uw QnA Maker Knowledge Base publiceert.<br><br>Ga naar de pagina **Service-instellingen** en zoek deze sleutels. Ga naar deze pagina in het menu boven en rechts van de gebruiker als keuze in de vervolg keuzelijst.|

## <a name="create-a-new-qna-maker-service"></a>Een nieuwe QnA Maker-service maken

Met deze procedure maakt u de Azure-resources die nodig zijn voor het beheren van de inhoud van de Knowledge Base. Wanneer u deze stappen hebt voltooid, vindt u de _abonnements_ sleutels op de pagina **sleutels** voor de resource in de Azure Portal.

1. Meld u aan bij de Azure Portal en [Maak een QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) -resource.

1. Selecteer **maken** nadat u de voor waarden hebt gelezen.

    ![Een nieuwe QnA Maker-service maken](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. In **QnA Maker**, selecteer de juiste lagen en regio's.

    ![Een nieuwe QnA Maker-service - prijscategorie en regio's maken](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Vul de **naam** met een unieke naam voor deze QnA Maker-service. Deze naam identificeert ook de QnA Maker-eindpunt waarnaar uw knowledge bases gekoppeld worden.
    * Kies de **abonnement** in die de QnA Maker-resource wordt geïmplementeerd.
    * Selecteer de **prijs categorie** voor de QnA Maker-beheer Services (Portal-en beheer-api's). Zie [hier](https://aka.ms/qnamaker-pricing) voor meer informatie over de prijzen van de SKU's.
    * Maak een nieuwe **resourcegroep** (aanbevolen) of gebruik een bestaande resourcegroep waarin u kunt deze resource QnA Maker implementeren. QnA Maker maakt verschillende Azure-resources. Wanneer u een resource groep maakt om deze resources te bevatten, kunt u deze resources eenvoudig vinden, beheren en verwijderen op basis van de naam van de resource groep.
    * Selecteer een **locatie voor de resource groep**.
    * Kies de **zoeken prijscategorie** van de Azure Search-service. Als u de gratis laag-optie grijs weergegeven, betekent dat u hebt al een gratis Azure Search-laag in uw abonnement. In dat geval moet u eerst de laag Basic Azure Search. Zie de details van prijzen van Azure search [hier](https://azure.microsoft.com/pricing/details/search/).
    * Kies de **zoeklocatie** waar u Azure Search-gegevens om te worden geïmplementeerd. Beperkingen in waar de gegevens van de klant moet worden opgeslagen, wordt de locatie die u voor Azure Search kiest informeren.
    * Geef een naam aan uw appservice in **appnaam**.
    * Standaard wordt de appservice standaard naar de prijscategorie standard (S1). U kunt het abonnement wijzigen nadat de is gemaakt. Zie meer informatie over prijzen voor App service [hier](https://azure.microsoft.com/pricing/details/app-service/).
    * Kies de **locatie van de Website** waarop de App-Service wordt geïmplementeerd.

        > [!NOTE]
        > De locatie van de zoekopdracht kan afwijken van de locatie van de Website.

    * Kies of u om in te schakelen **Application Insights** of niet. Als **Application Insights** is ingeschakeld, QnA Maker telemetrie verzamelt op verkeer, chatlogs en fouten.
    * Kies de **App insights-locatie** waarop Application Insights-resource wordt geïmplementeerd.
    * Voor kosten besparingen kunt u enkele, maar niet alle Azure-resources die zijn gemaakt voor QnA Maker [delen](#share-existing-services-with-qna-maker) . 

1. Wanneer alle velden zijn gevalideerd, selecteert u **maken**. Het kan een paar minuten duren voordat het proces is voltooid.

1. Wanneer de implementatie is voltooid, ziet u de volgende bronnen in uw abonnement gemaakt.

    ![De resource gemaakt in een nieuwe QnA Maker-service](../media/qnamaker-how-to-setup-service/resources-created.png)

    De resource met het type _Cognitive Services_ heeft uw _abonnements_ sleutels.

## <a name="find-subscription-keys-in-azure-portal"></a>Abonnements sleutels zoeken in Azure Portal

U kunt de sleutels van uw abonnement weer geven en opnieuw instellen, die u van de Azure Portal bewerken waar u de QnA Maker resource hebt gemaakt. 

1. Ga naar de QnA Maker resource in de Azure Portal en selecteer de resource met het type _Cognitive Services_.

    ![Lijst met resources voor QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Ga naar **sleutels**.

    ![Abonnementssleutel](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-qna-maker-portal"></a>Eindpunt sleutels zoeken in QnA Maker Portal

Eindpunt-sleutels kunnen worden beheerd vanuit de [QnA Maker portal](https://qnamaker.ai).

1. Meld u aan bij de [QnA Maker-Portal](https://qnamaker.ai), ga naar uw profiel en klik vervolgens op service- **instellingen**.

    ![Eindpuntsleutel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Weergeven of opnieuw instellen van uw sleutels.

    ![belangrijkste endpoint-beheerder](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Als u denkt dat ze zijn aangetast, vernieuwt u uw sleutels. Hiervoor kunnen eventueel bijbehorende wijzigingen in de client toepassing of bot code worden vereist.

## <a name="share-existing-services-with-qna-maker"></a>Bestaande Services delen met QnA Maker

QnA Maker maakt verschillende Azure-resources. Gebruik de volgende tabel om te begrijpen wat u wel en niet kunt delen om het beheer te beperken en te profiteren van het delen van kosten:

|Service|Delen|Reason|
|--|--|--|
|Cognitive Services|X|Niet mogelijk door het ontwerp|
|App Service-abonnement|✔|Vaste schijf ruimte toegewezen aan een app service-plan. Als andere apps, het delen van hetzelfde App Service-abonnement, een aanzienlijke hoeveelheid schijf ruimte gebruiken, QnAMaker App Service worden uitgevoerd in problemen.|
|App Service|X|Niet mogelijk door het ontwerp|
|Application Insights|✔|Kan worden gedeeld|
|Zoekservice|✔|1. `testkb` is een gereserveerde naam voor de QnAMaker-service en kan niet worden gebruikt door anderen.<br>2. Synoniem map met de naam `synonym-map` is gereserveerd voor de QnAMaker-service.<br>3. Het aantal gepubliceerde Kb's wordt beperkt door de zoek service laag. Als er gratis indexen beschikbaar zijn, kunnen andere services deze gebruiken.|

### <a name="using-a-single-search-service"></a>Eén zoek service gebruiken

Als u een QnA-service en de bijbehorende afhankelijkheden (zoals zoeken) maakt via de portal, wordt er een zoek service voor u gemaakt en gekoppeld aan de QnA Maker-service. Nadat deze resources zijn gemaakt, kunt u de app service-instelling bijwerken om een eerder bestaande zoek service te gebruiken en de zojuist gemaakte zoek service verwijderen.

Als u een QnA-service maakt via Azure Resource Manager sjablonen, kunt u alle resources maken en het maken van de app service beheren om een bestaande zoek service te gebruiken. 

## <a name="upgrade-qna-maker"></a>QnA Maker bijwerken

|Upgrade|Reason|
|--|--|
|[Upgrade uitvoeren](#upgrade-qna-maker-sku) QnA Maker Management-SKU|U moet meer vragen en antwoorden in uw Knowledge Base hebben.|
|[Upgrade uitvoeren](#upgrade-app-service) App Service-SKU|Uw Knowledge Base moet meer aanvragen van uw client-app leveren, zoals een chat sessie.|
|[Upgrade uitvoeren](#upgrade-azure-search-service) Azure Search-service|U bent van plan om veel kennis bases te hebben.|


### <a name="upgrade-qna-maker-sku"></a>QnA Maker SKU bijwerken

Als u meer vragen en antwoorden in uw Knowledge Base nodig hebt, moet u de prijs categorie van QnA Maker service upgraden naar uw huidige laag. 

Een upgrade uitvoeren van de QnA Maker Management SKU:

1. Ga in de Azure Portal naar uw QnA Maker-resource en selecteer **prijs categorie**.

    ![QnA Maker resource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Kies de juiste SKU en druk op **selecteren**.

    ![QnA Maker prijzen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>App service bijwerken

 Als uw Knowledge Base meer aanvragen van uw client-app nodig heeft, moet u de prijs categorie van uw app service bijwerken.

U kunt de app service [omhoog](https://docs.microsoft.com/azure/app-service/manage-scale-up) of omlaag schalen.

Ga naar de app service-resource in het Azure Portal en selecteer de gewenste opties voor **omhoog** of **omlaag** schalen.

![App service-schaal QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-azure-search-service"></a>Azure Search-service bijwerken

Als u van plan bent een groot aantal Knowledge bases te hebben, moet u uw Azure Search service prijs categorie bijwerken. 

Het is momenteel niet mogelijk om een in-place upgrade uit te voeren van de Azure Search-SKU. U kunt echter een nieuwe Azure Search-resource met de gewenste SKU maken, de gegevens herstellen naar de nieuwe resource en deze vervolgens koppelen aan de QnA Maker-stack.

1. Maak een nieuwe Azure-Zoek resource in het Azure Portal en kies de gewenste SKU.

    ![QnA Maker Azure Search-resource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Herstel de indexen van de oorspronkelijke Azure Search-resource naar de nieuwe. Zie de voorbeeld code voor het [](https://github.com/pchoudhari/QnAMakerBackupRestore)terugzetten van back-ups.

1. Zodra de gegevens zijn hersteld, gaat u naar uw nieuwe Azure Search-resource, selecteert u **sleutels**en noteert u de **naam** en de **beheerders sleutel**.

    ![QnA Maker Azure-Zoek sleutels](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Als u de nieuwe Azure Search-resource wilt koppelen aan de QnA Maker stack, gaat u naar de QnA Maker app-service.

    ![QnA Maker appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecteer **Toepassings instellingen** en vervang de velden **AzureSearchName** en **AzureSearchAdminKey** uit stap 3.

    ![Appservice-instelling van QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Start de App-service.

    ![QnA Maker appservice opnieuw starten](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-latest-runtime-updates"></a>Recente runtime-updates downloaden

De QnAMaker-runtime maakt deel uit van de Azure App Service geïmplementeerd bij het [maken van een QnAMaker-service](./set-up-qnamaker-service-azure.md) in azure Portal. Updates worden periodiek gesteld in de runtime. QnA Maker App Service bevindt zich in de modus automatisch bijwerken na de release van de site-uitbrei ding van apr 2019 (versie 5 +). Dit is al zo ontworpen dat er tijdens upgrades geen uitval tijd in rekening wordt gehouden. 

U kunt uw huidige versie controleren op https://www.qnamaker.ai/UserSettings. Als uw versie ouder is dan versie 5. x, moet u de App Service opnieuw opstarten om de meest recente updates toe te passen.

1. Ga naar uw QnAMaker-service (resourcegroep) in de [Azure-portal](https://portal.azure.com)

    ![QnAMaker-Azure-resourcegroep](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Klik op de Service-App en opent u de sectie overzicht

     ![QnAMaker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Start de App-service. Het zou binnen een paar seconden voltooid. Alle afhankelijke toepassingen of botsingen die gebruikmaken van deze QnAMaker-service, zijn niet beschikbaar voor eind gebruikers tijdens de herstart periode.

    ![QnAMaker appservice-opnieuw opstarten](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Regio beheer service

De beheer service van QnA Maker, die alleen wordt gebruikt voor de QnA Maker Portal & voor initiële gegevens verwerking, is alleen beschikbaar in VS-West. Er worden geen klant gegevens opgeslagen in deze service vs-West.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Maken en een kennisdatabase publiceren](../Quickstarts/create-publish-knowledge-base.md)
