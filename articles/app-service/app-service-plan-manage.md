---
title: App-serviceplan beheren
description: Meer informatie over het uitvoeren van verschillende taken om een App Service-abonnement te beheren, zoals maken, verplaatsen, schalen en verwijderen.
keywords: app-service, azure-app-service, schaal, app-serviceplan, wijzigen, maken, beheren, beheren
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d40f5db65ce9ca90ae978bac4491bdebccc2a328
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811707"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Een App-serviceplan beheren in Azure

Een [Azure App Service-abonnement](overview-hosting-plans.md) biedt de resources die een App Service-app moet uitvoeren. In deze handleiding ziet u hoe u een App Service-abonnement beheert.

## <a name="create-an-app-service-plan"></a>Een App Service-plan maken

> [!TIP]
> Zie [Een App-serviceplan maken in een App-serviceomgeving](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)als u een App-serviceomgeving hebt.

U een leeg App Service-abonnement maken of u een abonnement maken als onderdeel van het maken van apps.

1. Selecteer in de [Azure-portal](https://portal.azure.com)de optie **Een resource maken**.

   ![Maak een resource in de Azure-portal.][createResource] 

1. Selecteer **Nieuwe** > **web-app** of een ander soort App-service-app.

   ![Maak een app in de Azure-portal.][createWebApp] 

2. Configureer de sectie **Instantiedetails** voordat u het App-serviceplan configureert. Instellingen zoals **Publiceren** en **Besturingssystemen** kunnen de beschikbare prijsniveaus voor uw App Service-abonnement wijzigen. **Regio** bepaalt waar uw App Service-abonnement wordt gemaakt. 
   
3. Selecteer in de sectie **App-serviceplan** een bestaand abonnement of maak een plan door **Nieuw maken te**selecteren.

   ![Maak een App Service-abonnement.][createASP] 

4. Wanneer u een abonnement maakt, u de prijscategorie van het nieuwe abonnement selecteren. Selecteer in **Sku en grootte** **De optie Grootte wijzigen** om de prijslaag te wijzigen. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Een app verplaatsen naar een ander App-serviceabonnement

U een app verplaatsen naar een ander App Service-abonnement, zolang het bronplan en het doelplan zich in _dezelfde resourcegroep en geografische regio bevinden._

> [!NOTE]
> Azure implementeert elk nieuw App Service-abonnement in een implementatie-eenheid, intern een webspace genoemd. Elke regio kan veel webruimten hebben, maar uw app kan alleen worden verplaatst tussen plannen die in dezelfde webruimte zijn gemaakt. Een App Service-omgeving is een geïsoleerde webspace, zodat apps kunnen worden verplaatst tussen plannen in dezelfde App-serviceomgeving, maar niet tussen plannen in verschillende App-serviceomgevingen.
>
> U niet de webruimte opgeven die u wilt bij het maken van een plan, maar het is mogelijk om ervoor te zorgen dat een plan wordt gemaakt in dezelfde webruimte als een bestaand plan. Kortom, alle plannen die zijn gemaakt met dezelfde resourcegroep en regiocombinatie worden geïmplementeerd in dezelfde webruimte. Als u bijvoorbeeld een plan hebt gemaakt in resourcegroep A en regio B, wordt elk plan dat u vervolgens maakt in resourcegroep A en regio B geïmplementeerd in dezelfde webruimte. Houd er rekening mee dat plannen webruimten niet kunnen verplaatsen nadat ze zijn gemaakt, zodat u een abonnement niet naar 'dezelfde webruimte' verplaatsen als een ander plan door het naar een andere resourcegroep te verplaatsen.
> 

1. Zoek en selecteer in de [Azure-portal](https://portal.azure.com) **app-services** en selecteer de app die u wilt verplaatsen.

2. Selecteer App **Service-abonnement wijzigen**in het linkermenu .

3. Selecteer in de vervolgkeuzelijst **App-service-abonnement** een bestaand abonnement om de app naar te verplaatsen. In de vervolgkeuzelijst worden alleen plannen weergegeven die zich in dezelfde resourcegroep en het geografische gebied bevinden als het huidige App Service-abonnement. Als een dergelijk abonnement niet bestaat, u standaard een abonnement maken. U ook handmatig een nieuw plan maken door **Nieuw maken**te selecteren.

4. Als u een abonnement maakt, u de prijscategorie van het nieuwe abonnement selecteren. Selecteer **in prijscategorie**de bestaande laag om deze te wijzigen. 
   
   > [!IMPORTANT]
   > Als u een app verplaatst van een abonnement met een hogere laag naar een abonnement met een lagere laag, zoals van **D1** naar **F1,** kan de app bepaalde mogelijkheden in het doelplan verliezen. Als uw app bijvoorbeeld TLS/SSL-certificaten gebruikt, ziet u mogelijk dit foutbericht:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Selecteer **OK** als u klaar bent.
   
   ![App Service-abonnementkiezer.][change] 

## <a name="move-an-app-to-a-different-region"></a>Een app verplaatsen naar een andere regio

De regio waarin uw app wordt uitgevoerd, is de regio van het App Service-abonnement waarin deze zich bevindt. U de regio van een App-serviceplan echter niet wijzigen. Als u uw app in een andere regio wilt uitvoeren, is een alternatief app klonen. Klonen maakt een kopie van uw app in een nieuw of bestaand App Service-abonnement in elke regio.

Je **Clone App** vinden in het gedeelte **Ontwikkeltools** van het menu.

> [!IMPORTANT]
> Klonen heeft een aantal beperkingen. U erover lezen in [het klonen van Azure App Service App.](app-service-web-app-cloning.md)

## <a name="scale-an-app-service-plan"></a>Een App-serviceplan schalen

Zie [Een app opschalen in Azure](manage-scale-up.md)als u de prijscategorie van een App Service-abonnement wilt opschalen.

Zie Het [aantal instance's van](../monitoring-and-diagnostics/insights-how-to-scale.md)een app handmatig of automatisch schalen als u het aantal instance's van een app wilt schalen.

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Een App-serviceabonnement verwijderen

Om onverwachte kosten te voorkomen, verwijdert App Service het abonnement standaard wanneer u de laatste app in een App Service-abonnement verwijdert. Als u ervoor kiest om het abonnement te behouden, moet u het abonnement wijzigen in **de rij Gratis,** zodat er geen kosten in rekening worden gebracht.

> [!IMPORTANT]
> App Service-abonnementen waaraan geen apps zijn gekoppeld, worden nog steeds in rekening gebracht omdat ze de geconfigureerde VM-exemplaren blijven reserveren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een app omhoog schalen in Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
