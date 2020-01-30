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
ms.date: 09/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: dc3bb6882963205e17e37f52ec9dcdffecdf9e21
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843113"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker-resources beheren

Voordat u een QnA Maker knowledge bases maken kunt, moet u eerst een QnA Maker-service in Azure instellen. Iedereen met autorisatie voor het maken van nieuwe resources in een abonnement kunt een QnA Maker-service instellen.

## <a name="types-of-keys-in-qna-maker"></a>Typen sleutels in QnA Maker

Uw QnA Maker-service behandelt twee soorten sleutels: **abonnements sleutels** en **eindpunt sleutels**.

![Sleutelbeheer](../media/qnamaker-how-to-key-management/key-management.png)

|Name|Locatie|Doel|
|--|--|--|
|Abonnementssleutel|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Deze sleutels worden gebruikt voor toegang tot de Api's van de [QnA Maker Management-service](https://go.microsoft.com/fwlink/?linkid=2092179). Met deze Api's kunt u de vragen en antwoorden in uw Knowledge Base bewerken en uw kennis database publiceren. Deze sleutels worden gemaakt wanneer u een nieuwe QnA Maker-service maakt.<br><br>Deze sleutels vindt u op de pagina **sleutels** van de **Cognitive Services** resource.|
|Eindpuntsleutel|[QnA Maker Portal](https://www.qnamaker.ai)|Deze sleutels worden gebruikt om toegang te krijgen tot het gepubliceerde Knowledge Base-eind punt om een antwoord te krijgen op de vraag van een gebruiker. Normaal gesp roken gebruikt u dit eind punt in uw chat-bot of in de client toepassings code die verbinding maakt met de QnA Maker-service. Deze sleutels worden gemaakt wanneer u uw QnA Maker Knowledge Base publiceert.<br><br>Deze sleutels vindt u op de pagina **Service-instellingen** . Ga naar deze pagina vanuit het menu van de gebruiker in de rechter bovenhoek van de pagina in de vervolg keuzelijst.|

## <a name="create-a-new-qna-maker-service"></a>Een nieuwe QnA Maker-service maken

Met deze procedure maakt u de Azure-resources die nodig zijn voor het beheren van de inhoud van de Knowledge Base. Nadat u deze stappen hebt voltooid, vindt u de _abonnements_ sleutels op de pagina **sleutels** voor de resource in de Azure Portal.

1. Meld u aan bij de Azure Portal en [Maak een QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) -resource.

1. Selecteer **maken** nadat u de voor waarden hebt gelezen:

    ![Een nieuwe QnA Maker-service maken](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Selecteer in **QnA Maker**de juiste lagen en regio's:

    ![Een nieuwe QnA Maker-service - prijscategorie en regio's maken](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Voer in het veld **naam** een unieke naam in om deze QnA Maker service aan te duiden. Deze naam duidt ook het QnA Maker-eind punt aan waaraan uw kennis grondslagen worden gekoppeld.
    * Kies het **abonnement** waarmee de QnA Maker resource wordt geïmplementeerd.
    * Selecteer de **prijs categorie** voor de QnA Maker-beheer Services (Portal-en beheer-api's). Bekijk [meer informatie over de prijzen van de SKU](https://aka.ms/qnamaker-pricing).
    * Een nieuwe **resource groep** maken (aanbevolen) of een bestaande gebruiken om deze QnA Maker-resource te implementeren. QnA Maker maakt verschillende Azure-resources. Wanneer u een resource groep maakt om deze resources te bevatten, kunt u deze resources eenvoudig vinden, beheren en verwijderen op basis van de naam van de resource groep.
    * Selecteer een **locatie voor de resource groep**.
    * Kies de **prijs categorie voor zoeken** in de Azure Cognitive Search-service. Als de optie gratis laag niet beschikbaar is (grijs weer gegeven), betekent dit dat u al een gratis service hebt geïmplementeerd via uw abonnement. In dat geval moet u beginnen met de laag basis. Zie de [prijs informatie voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).
    * Kies de **Zoek locatie** waar u Azure Cognitive Search-indexen wilt implementeren. Beperkingen voor het opslaan van klant gegevens kunnen helpen bij het bepalen van de locatie die u kiest voor Azure Cognitive Search.
    * Voer in het veld **app-naam** een naam in voor uw Azure app service-exemplaar.
    * Standaard App Service standaard ingesteld op de standaard laag (S1). U kunt het abonnement wijzigen nadat de is gemaakt. Meer informatie over [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/).
    * Kies de **locatie** van de Website waar app service worden geïmplementeerd.

        > [!NOTE]
        > De **Zoek locatie** kan afwijken van de **locatie**van de website.

    * Kies of u **Application Insights**wilt inschakelen. Als **Application Insights** is ingeschakeld, QnA Maker telemetrie verzamelt op verkeer, chatlogs en fouten.
    * Kies de **app Insights-locatie** waar de Application Insights resource wordt geïmplementeerd.
    * Voor kosten besparingen kunt u enkele, maar niet alle Azure-resources die zijn gemaakt voor QnA Maker [delen](#share-existing-services-with-qna-maker) .

1. Nadat alle velden zijn gevalideerd, selecteert u **maken**. Het proces kan een paar minuten duren.

1. Nadat de implementatie is voltooid, ziet u de volgende resources die zijn gemaakt in uw abonnement:

   ![De resource gemaakt in een nieuwe QnA Maker-service](../media/qnamaker-how-to-setup-service/resources-created.png)

    De resource met het _Cognitive Services_ type heeft uw _abonnements_ sleutels.

## <a name="find-subscription-keys-in-the-azure-portal"></a>Abonnements sleutels zoeken in de Azure Portal

U kunt uw abonnements sleutels weer geven en opnieuw instellen in de Azure Portal, waar u de QnA Maker resource hebt gemaakt.

1. Ga naar de QnA Maker resource in de Azure Portal en selecteer de resource met het _Cognitive Services_ type:

    ![Lijst met resources voor QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Ga naar **sleutels**:

    ![Abonnementssleutel](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Eindpunt sleutels zoeken in de QnA Maker Portal

Het eind punt bevindt zich in dezelfde regio als de resource omdat de eindpunt sleutels worden gebruikt voor het aanroepen van de Knowledge Base.

Eindpunt-sleutels kunnen worden beheerd vanuit de [QnA Maker portal](https://qnamaker.ai).

1. Meld u aan bij de [QnA Maker-Portal](https://qnamaker.ai), ga naar uw profiel en selecteer vervolgens service- **instellingen**:

    ![Eindpuntsleutel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Uw sleutels weer geven of herstellen:

    ![Eindpunt sleutel beheer](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Vernieuw uw sleutels als u denkt dat ze zijn aangetast. Hiervoor kunnen eventueel bijbehorende wijzigingen in de client toepassing of bot code worden vereist.

## <a name="share-existing-services-with-qna-maker"></a>Bestaande Services delen met QnA Maker

QnA Maker maakt verschillende Azure-resources. Gebruik de volgende tabel om te begrijpen wat u wel en niet kunt delen om het beheer te beperken en te profiteren van het delen van kosten:

|Service|Delen|Reden|
|--|--|--|
|Cognitive Services|X|Niet mogelijk door het ontwerp|
|App Service-plan|✔|Er is een vaste schijf ruimte toegewezen voor een App Service plan. Als andere apps die hetzelfde App Service plan delen, veel schijf ruimte gebruiken, ondervinden de QnAMaker App Service-instantie problemen.|
|App Service|X|Niet mogelijk door het ontwerp|
|Application Insights|✔|Kan worden gedeeld|
|Zoekservice|✔|1. `testkb` is een gereserveerde naam voor de QnAMaker-service. het kan niet worden gebruikt door anderen.<br>2. het synoniem van de map met de naam `synonym-map` is gereserveerd voor de QnAMaker-service.<br>3. het aantal gepubliceerde kennis grondslagen is beperkt door de zoek service laag. Als er gratis indexen beschikbaar zijn, kunnen andere services deze gebruiken.|

Meer informatie over de [app service](../../../app-service/index.yml) en de [Zoek service](../../../search/index.yml).

## <a name="using-a-single-search-service"></a>Eén zoek service gebruiken

Als u een QnA-service en de bijbehorende afhankelijkheden (zoals zoeken) maakt via de portal, wordt er een zoek service voor u gemaakt en gekoppeld aan de QnA Maker-service. Nadat deze resources zijn gemaakt, kunt u de App Service-instelling bijwerken om een eerder bestaande zoek service te gebruiken en de toepassing verwijderen die u zojuist hebt gemaakt.

Als u een QnA-service maakt via Azure Resource Manager sjablonen, kunt u alle resources maken en de App Service maken om een bestaande zoek service te gebruiken.


## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>QnA Maker configureren voor het gebruik van verschillende Cognitive Search resource

Als u een QnA-service en de bijbehorende afhankelijkheden (zoals zoeken) maakt via de portal, wordt er een zoek service voor u gemaakt en gekoppeld aan de QnA Maker-service. Nadat deze resources zijn gemaakt, kunt u de App Service-instelling bijwerken om een eerder bestaande zoek service te gebruiken en de toepassing verwijderen die u zojuist hebt gemaakt.

De **app service** resource van QnA Maker maakt gebruik van de Cognitive Search resource. Als u de Cognitive Search resource wilt wijzigen die door QnA Maker wordt gebruikt, moet u de instelling wijzigen in de Azure Portal.

1. Haal de **beheerders sleutel** en de **naam** op van de Cognitive Search resource die u QnA Maker wilt gebruiken.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en zoek de **app service** die aan uw QnA Maker-resource zijn gekoppeld. Beide met dezelfde naam hebben.

1. Selecteer **instellingen**en vervolgens **configuratie**. Hiermee worden alle bestaande instellingen voor de App Service van de QnA Maker weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![scherm opname van Azure Portal waarin App Service configuratie-instellingen worden weer gegeven](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Wijzig de waarden voor de volgende sleutels:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Als u de nieuwe instellingen wilt gebruiken, moet u de app service opnieuw starten. Selecteer **overzicht**en selecteer **opnieuw opstarten**.

    > [!div class="mx-imgBorder"]
    > ![scherm opname van Azure Portal opnieuw opstarten App Service nadat de configuratie-instellingen zijn gewijzigd](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Als u een QnA-service maakt via Azure Resource Manager sjablonen, kunt u alle resources maken en de App Service maken om een bestaande zoek service te gebruiken.

## <a name="upgrade-qna-maker"></a>QnA Maker bijwerken

|Upgraden|Reden|
|--|--|
|[Upgrade uitvoeren](#upgrade-qna-maker-sku) QnA Maker Management-SKU|U wilt meer vragen en antwoorden in uw Knowledge Base hebben.|
|[Upgrade uitvoeren](#upgrade-app-service) App Service SKU|Uw Knowledge Base moet meer aanvragen van uw client-app leveren, zoals een chat-bot.|
|[Upgrade uitvoeren](#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search-service|U bent van plan om veel kennis bases te hebben.|


### <a name="upgrade-qna-maker-sku"></a>QnA Maker SKU bijwerken

Als u meer vragen en antwoorden in uw Knowledge Base wilt hebben, moet u de prijs categorie van QnA Maker service upgraden naar uw huidige laag.

Een upgrade uitvoeren van de QnA Maker Management SKU:

1. Ga in de Azure Portal naar uw QnA Maker-resource en selecteer **prijs categorie**.

    ![QnA Maker resource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Kies de juiste SKU en druk op **selecteren**.

    ![QnA Maker prijzen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>App Service bijwerken

 Als uw Knowledge Base meer aanvragen van uw client-app wil leveren, moet u uw App Service prijs categorie bijwerken.

U kunt App Service [schalen](https://docs.microsoft.com/azure/app-service/manage-scale-up) of uitschalen.

Ga naar de App Service resource in de Azure Portal en selecteer de optie **Omhoog schalen** of **uitschalen** .

![QnA Maker App Service schaal](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>De Azure Cognitive Search-service upgraden

Als u van plan bent een groot aantal Knowledge bases te hebben, moet u de prijs categorie van uw Azure Cognitive Search-service bijwerken.

Op dit moment kunt u geen in-place upgrade van de SKU voor Azure Search uitvoeren. U kunt echter een nieuwe Azure Search-resource met de gewenste SKU maken, de gegevens herstellen naar de nieuwe resource en deze vervolgens koppelen aan de QnA Maker-stack. Voer de volgende stappen uit om dit te doen:

1. Maak een nieuwe Azure-Zoek resource in het Azure Portal en selecteer de gewenste SKU.

    ![QnA Maker Azure Search-resource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Herstel de indexen van de oorspronkelijke Azure Search-resource naar de nieuwe. Zie de [voorbeeld code voor het terugzetten van back-ups](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Nadat de gegevens zijn hersteld, gaat u naar uw nieuwe Azure Search-resource, selecteert u **sleutels**en noteert u de **naam** en de **beheerders sleutel**:

    ![QnA Maker Azure-Zoek sleutels](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Als u de nieuwe Azure Search-resource wilt koppelen aan de QnA Maker stack, gaat u naar het QnA Maker App Service-exemplaar.

    ![App Service instantie QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecteer **Toepassings instellingen** en wijzig de instellingen in de velden **AzureSearchName** en **AzureSearchAdminKey** uit stap 3.

    ![Instelling voor QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Start het App Service-exemplaar opnieuw.

    ![Het QnA Maker App Service-exemplaar opnieuw starten](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Down load de meest recente runtime-updates

De QnAMaker-runtime maakt deel uit van het Azure App Service-exemplaar dat wordt geïmplementeerd wanneer u [een QnAMaker-service](./set-up-qnamaker-service-azure.md) in de Azure Portal maakt. Updates worden periodiek gesteld in de runtime. Het QnA Maker App Service-exemplaar bevindt zich in de modus automatisch bijwerken na de site-extensie release van april 2019 (versie 5 +). Deze update is ontworpen om te zorgen dat er geen downtime is tijdens de upgrade.

U kunt uw huidige versie controleren op https://www.qnamaker.ai/UserSettings. Als uw versie ouder is dan versie 5. x, moet u App Service opnieuw opstarten om de meest recente updates toe te passen:

1. Ga naar de QnAMaker-service (resource groep) in het [Azure Portal](https://portal.azure.com).

    ![QnAMaker-Azure-resourcegroep](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecteer de App Service instantie en open de sectie **overzicht** .

    ![QnAMaker App Service-instantie](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Start App Service opnieuw. Het update proces moet binnen een paar seconden worden voltooid. Alle afhankelijke toepassingen of botsingen die gebruikmaken van deze QnAMaker-service, zijn niet beschikbaar voor eind gebruikers tijdens de herstart periode.

    ![Het QnAMaker App Service-exemplaar opnieuw starten](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="management-service-region"></a>Regio beheer service

De beheer service van QnA Maker wordt alleen gebruikt voor de QnA Maker Portal en voor initiële gegevens verwerking. Deze service is alleen beschikbaar in de regio vs-West. Er worden geen klant gegevens opgeslagen in deze service vs-West.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [app service](../../../app-service/index.yml) en de [Zoek service](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Maken en een kennisdatabase publiceren](../Quickstarts/create-publish-knowledge-base.md)
