---
title: Een QnA Maker-service instellen - QnA Maker
description: Voordat u kennisbases van QnA Maker maken, moet u eerst een QnA Maker-service in Azure instellen. Iedereen met toestemming om nieuwe bronnen in een abonnement te maken, kan een QnA Maker-service instellen.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 8ec57f441ba58227e45398c35c7931dc75fa658f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131715"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker-bronnen beheren

Voordat u kennisbases van QnA Maker maken, moet u eerst een QnA Maker-service in Azure instellen. Iedereen met toestemming om nieuwe bronnen in een abonnement te maken, kan een QnA Maker-service instellen.

Een goed begrip van de volgende concepten is handig voordat u uw resource maakt:

* [QnA Maker-bronnen](../Concepts/azure-resources.md)
* [Sleutels ontwerpen en publiceren](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Een nieuwe QnA Maker-service maken

Met deze procedure worden de Azure-resources gebruikt die nodig zijn om de inhoud van de knowledge base te beheren. Nadat u deze stappen hebt voltooid, vindt u de _abonnementssleutels_ op de pagina **Sleutels** voor de bron in de Azure-portal.

1. Meld u aan bij de Azure-portal en [maak een QnA Maker-bron.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)

1. Selecteer **Maken** nadat u de algemene voorwaarden hebt gelezen:

    ![Een nieuwe QnA Maker-service maken](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Selecteer in **QnA Maker**de juiste lagen en regio's:

    ![Een nieuwe QnA Maker-service maken - prijscategorie en regio's](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Voer in het veld **Naam** een unieke naam in om deze QnA Maker-service te identificeren. Deze naam identificeert ook het QnA Maker-eindpunt waar uw kennisbanken aan worden gekoppeld.
    * Kies het **abonnement** waaronder de QnA Maker-bron wordt geïmplementeerd.
    * Selecteer de **prijslaag** voor de beheerservices van QnA Maker (portal- en beheer-API's). Meer [informatie over SKU-prijzen](https://aka.ms/qnamaker-pricing).
    * Maak een nieuwe **Resourcegroep** (aanbevolen) of gebruik een bestaande groep waarin deze QnA Maker-bron kan worden geïmplementeerd. QnA Maker maakt verschillende Azure-bronnen. Wanneer u een resourcegroep maakt om deze resources vast te houden, u deze resources eenvoudig vinden, beheren en verwijderen op basis van de naam van de brongroep.
    * Selecteer een locatie van de **resourcegroep**.
    * Kies de **prijscategorie Zoeken** van de Azure Cognitive Search-service. Als de optie Gratis laag niet beschikbaar is (wordt gedimd weergegeven), betekent dit dat u al een gratis service hebt geïmplementeerd via uw abonnement. In dat geval moet u beginnen met de laag Basis. Zie [prijsdetails azure cognitive search](https://azure.microsoft.com/pricing/details/search/).
    * Kies de **zoeklocatie** waar u Azure Cognitive Search-indexen wilt implementeren. Beperkingen voor waar klantgegevens moeten worden opgeslagen, helpen bij het bepalen van de locatie die u kiest voor Azure Cognitive Search.
    * Voer in het veld **App-naam** een naam in voor uw Azure App Service-exemplaar.
    * App Service standaard ingesteld op de standaardlaag (S1). U het plan na het maken wijzigen. Meer informatie over [de prijzen van App-service](https://azure.microsoft.com/pricing/details/app-service/).
    * Kies de **locatie van** de website waar De App-service wordt geïmplementeerd.

        > [!NOTE]
        > De **zoeklocatie** kan afwijken van de locatie van de **website.**

    * Kies of u **toepassingsinzichten**wilt inschakelen. Als **Application Insights** is ingeschakeld, verzamelt QnA Maker telemetrie op verkeer, chatlogboeken en fouten.
    * Kies de **locatie app-inzichten** waar de resource Application Insights wordt geïmplementeerd.
    * Voor kostenbesparingsmaatregelen u sommige, maar niet alle Azure-resources die zijn gemaakt voor QnA [Maker, delen.](#configure-qna-maker-to-use-different-cognitive-search-resource)

1. Nadat alle velden zijn gevalideerd, selecteert u **Maken**. Het proces kan enkele minuten in beslag nemen.

1. Nadat de implementatie is voltooid, ziet u de volgende bronnen die in uw abonnement zijn gemaakt:

   ![Resource heeft een nieuwe QnA Maker-service gemaakt](../media/qnamaker-how-to-setup-service/resources-created.png)

    De bron met het type _Cognitive Services_ heeft uw _abonnementssleutels._

## <a name="find-subscription-keys-in-the-azure-portal"></a>Abonnementssleutels zoeken in de Azure-portal

U uw abonnementssleutels bekijken en opnieuw instellen vanuit de Azure-portal, waar u de QnA Maker-bron hebt gemaakt.

1. Ga naar de QnA Maker-bron in de Azure-portal en selecteer de bron met het type _Cognitive Services:_

    ![QnA Maker-resourcelijst](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Ga naar **Toetsen:**

    ![Abonnementssleutel](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="find-endpoint-keys-in-the-qna-maker-portal"></a>Eindpunttoetsen zoeken in de QnA Maker-portal

Het eindpunt bevindt zich in dezelfde regio als de resource, omdat de eindpunttoetsen worden gebruikt om een oproep te doen naar de kennisbank.

Eindpunttoetsen kunnen worden beheerd vanuit de [QnA Maker-portal.](https://qnamaker.ai)

1. Meld u aan bij de [QnA Maker-portal,](https://qnamaker.ai)ga naar uw profiel en selecteer **Service-instellingen:**

    ![Eindpunttoets](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Uw toetsen weergeven of opnieuw instellen:

    > [!div class="mx-imgBorder"]
    > ![Eindpuntsleutelmanager](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Ververs je sleutels als je denkt dat ze zijn gecompromitteerd. Hiervoor zijn mogelijk overeenkomstige wijzigingen in uw clienttoepassing of botcode nodig.

### <a name="upgrade-qna-maker-sku"></a>Upgrade QnA Maker SKU

Wanneer u meer vragen en antwoorden in uw kennisbank wilt hebben, u uw prijscategorie voor QnA Maker-service upgraden.

Ga als lid van het Management SKU van QnA Maker:

1. Ga naar uw QnA Maker-bron in de Azure-portal en selecteer **Prijscategorie**.

    ![QnA Maker-bron](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Kies de juiste SKU en druk op **Selecteren**.

    ![QnA Maker prijzen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

### <a name="upgrade-app-service"></a>App-service bijwerken

 Wanneer uw kennisbank meer aanvragen van uw client-app moet indienen, upgradet u de prijscategorie App Service.

U App Service [opschalen](https://docs.microsoft.com/azure/app-service/manage-scale-up) of uitschalen.

Ga naar de App Service-bron in de Azure-portal en selecteer de optie **Opschalen** of **Uitschalen** zoals vereist.

![QnA Maker App Service-schaal](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>De Azure Cognitive Search-service upgraden

Als u van plan bent veel kennisbases te hebben, u de prijscategorie Azure Cognitive Search upgraden.

Momenteel u geen in-place upgrade van de Azure search SKU uitvoeren. U echter een nieuwe Azure-zoekbron maken met de gewenste SKU, de gegevens herstellen naar de nieuwe bron en deze koppelen aan de stapel QnA Maker. Voer de volgende stappen uit om dit te doen:

1. Maak een nieuwe Azure-zoekbron in de Azure-portal en selecteer de gewenste SKU.

    ![QnA Maker Azure-zoekbron](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Herstel de indexen van uw oorspronkelijke Azure-zoekbron naar de nieuwe. Zie de [voorbeeldcode voor back-upherstel](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Nadat de gegevens zijn hersteld, gaat u naar uw nieuwe Azure-zoekbron, selecteert u **Sleutels**en schrijft u de **sleutel Naam** en **beheerder**op:

    ![QnA Maker Azure-zoeksleutels](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Als u de nieuwe Azure-zoekbron wilt koppelen aan de qnA-knopstapel, gaat u naar de instantie QnA Maker App Service.

    ![QnA Maker App Service-instantie](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecteer **Toepassingsinstellingen** en wijzig de instellingen in de velden **AzureSearchName** en **AzureSearchAdminKey** vanaf stap 3.

    ![QnA Maker App Service-instelling](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Start het exemplaar van de App-service opnieuw.

    ![Het exemplaar qnA Maker App-service opnieuw starten](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="get-the-latest-runtime-updates"></a>Ontvang de nieuwste runtime-updates

De Runtime van QnAMaker maakt deel uit van het Azure App Service-exemplaar dat wordt geïmplementeerd wanneer u [een QnAMaker-service](./set-up-qnamaker-service-azure.md) maakt in de Azure-portal. Updates worden periodiek gemaakt om de runtime. Het qnA Maker App-service-exemplaar bevindt zich in de automatische updatemodus na de release van de siteextensie van april 2019 (versie 5+). Deze update is ontworpen om zero downtime tijdens upgrades te verzorgen.

U uw huidige https://www.qnamaker.ai/UserSettingsversie controleren op. Als uw versie ouder is dan versie 5.x, moet u de App Service opnieuw starten om de nieuwste updates toe te passen:

1. Ga naar uw QnAMaker-service (resourcegroep) in de [Azure-portal.](https://portal.azure.com)

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure-brongroep](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecteer de instantie App-service en open de sectie **Overzicht.**

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service-instantie](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. App-service opnieuw starten. Het updateproces moet in een paar seconden zijn voltooid. Afhankelijke toepassingen of bots die deze QnAMaker-service gebruiken, zijn tijdens deze herstartperiode niet beschikbaar voor eindgebruikers.

    ![Het exemplaar qnAMaker App-service opnieuw starten](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>QnA Maker configureren om verschillende cognitive search-bron te gebruiken

Als u een QnA-service en de afhankelijkheden ervan (zoals Zoeken) maakt via de portal, wordt er een zoekservice voor u gemaakt en gekoppeld aan de QnA Maker-service. Nadat deze bronnen zijn gemaakt, u de instelling App-service bijwerken om een eerder bestaande zoekservice te gebruiken en de service die u zojuist hebt gemaakt verwijderen.

De **App Service-bron** van QnA Maker maakt gebruik van de bron Cognitive Search. Als u de resource Cognitief zoeken wilt wijzigen die door QnA Maker wordt gebruikt, moet u de instelling in de Azure-portal wijzigen.

1. Download de **beheersleutel** en **de naam** van de resource Cognitief zoeken die u wilt dat QnA Maker gebruikt.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en zoek de **App-service** die is gekoppeld aan uw QnA Maker-bron. Beide hebben dezelfde naam.

1. Selecteer **Instellingen**, dan **Configuratie**. Hiermee worden alle bestaande instellingen voor de App Service van de QnA Maker weergegeven.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van Azure-portal met configuratie-instellingen voor App Service](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Wijzig de waarden voor de volgende toetsen:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Als u de nieuwe instellingen wilt gebruiken, moet u de app-service opnieuw starten. Selecteer **Overzicht**en selecteer **Opnieuw starten**.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van Azure-portal die App Service opnieuw opstart nadat de configuratie-instellingen zijn gewijzigd](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Als u een QnA-service maakt via Azure Resource Manager-sjablonen, u alle resources maken en de app-service maken om een bestaande zoekservice te gebruiken beheren.

Meer informatie over het configureren van de instellingen van de App [Service-toepassing](../../../app-service/configure-common.md#configure-app-settings).

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Inactieve app-service configureren om een time-out te voorkomen

De app-service, die de QnA Maker-voorspellingsruntime voor een gepubliceerde kennisbank bedient, heeft een inactieve time-outconfiguratie, die standaard automatisch een time-out kan krijgen als de service niet actief is. Voor QnA Maker betekent dit dat uw voorspelling runtime genererenAnswer API af en toe een time-out na perioden van geen verkeer.

Om de voorspellingendpunt-app te laden, zelfs als er geen verkeer is, stelt u de idle in op altijd ingeschakeld.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar en selecteer de app-service van uw QnA Maker-bron. Het zal dezelfde naam hebben als de QnA Maker bron, maar het zal een ander **type** app-service hebben.
1. Selecteer **Instellingen** en selecteer **Configuratie**.
1. Selecteer in het deelvenster Configuratie de optie **Algemene instellingen**, zoek **vervolgens Altijd aan**en selecteer **Aan** als waarde.

    > [!div class="mx-imgBorder"]
    > ![Selecteer in het deelvenster Configuratie **Algemene instellingen**, zoek **Altijd aan**en selecteer **Aan** als waarde.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Selecteer **Opslaan** om de configuratie op te slaan.
1. U wordt gevraagd of u de app opnieuw wilt starten om de nieuwe instelling te gebruiken. Selecteer **Doorgaan**.

Meer informatie over het configureren van de [algemene instellingen](../../../app-service/configure-common.md#configure-general-settings)van de app-service .

## <a name="delete-azure-resources"></a>Azure-resources verwijderen

Als u een van de Azure-bronnen verwijdert die worden gebruikt voor uw QnA Maker-kennisbases, werken de kennisbanken niet meer. Voordat u resources verwijderd, moet u ervoor zorgen dat u uw kennisbases exporteert via de pagina **Instellingen.**

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [app-](../../../app-service/index.yml) en [zoekservice](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Een knowledge base maken](../Quickstarts/create-publish-knowledge-base.md)