---
title: App Service plan beheren-Azure | Microsoft Docs
description: Meer informatie over het uitvoeren van verschillende taken voor het beheren van een App Service plan.
keywords: app service, Azure app service, Scale, app service-plan, wijzigen, maken, beheren, beheer
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: a5e69209c30eae816837ce8f00a065231a5fd821
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067208"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Een App Service-abonnement beheren in azure

Een [Azure app service plan](overview-hosting-plans.md) bevat de resources die een app service app moet uitvoeren. In deze hand leiding wordt uitgelegd hoe u een App Service plan beheert.

## <a name="create-an-app-service-plan"></a>Een App Service-plan maken

> [!TIP]
> Als u een App Service Environment hebt, raadpleegt u [een app service plan maken in een app service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

U kunt een leeg App Service plan maken of u kunt een plan maken als onderdeel van het maken van een app.

1. Selecteer in de [Azure Portal](https://portal.azure.com) **nieuwe** > **Web en mobiel**en selecteer vervolgens web- **app** of een ander soort app service-app.

2. Selecteer een bestaand App Service plan of maak een plan voor de nieuwe app.

   ![Maak een app in de Azure Portal.][createWebApp]

   Een plan maken:

   a. Selecteer **[+] nieuwe maken**.

      ![Een App Service-abonnement maken.][createASP] 

   b. Voer de naam van het plan in bij **app service plan**.

   c. Selecteer bij **locatie**een geschikte locatie.

   d. Voor **prijs categorie**selecteert u een geschikte prijs categorie voor de service. Selecteer **alles weer geven** om meer prijs opties weer te geven, zoals **gratis** en **gedeeld**. Nadat u de prijs categorie hebt geselecteerd, klikt u op de knop **selecteren** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Een app verplaatsen naar een andere App Service plan

U kunt een app naar een andere App Service plan verplaatsen, zolang het bron plan en het doel abonnement zich in _dezelfde resource groep en geografische regio_bevinden.

> [!NOTE]
> Azure implementeert elk nieuw App Service plan in een implementatie-eenheid, intern een webruimte genoemd. Elke regio kan veel webruimten hebben, maar uw app kan alleen worden verplaatst tussen plannen die zijn gemaakt in dezelfde webruimte. Een App Service Environment is een geïsoleerde webruimte, zodat apps kunnen worden verplaatst tussen plannen in dezelfde App Service Environment, maar niet tussen plannen in verschillende App Service omgevingen.
>
> U kunt de gewenste webruimte niet opgeven wanneer u een plan maakt, maar het is mogelijk om ervoor te zorgen dat een plan wordt gemaakt in dezelfde webruimte als een bestaand abonnement. In het kort worden alle abonnementen die zijn gemaakt met dezelfde resource groep en regio combinatie, geïmplementeerd in dezelfde webruimte. Als u bijvoorbeeld een plan hebt gemaakt in resource groep A en regio B, wordt een plan dat u vervolgens in resource groep A en regio B maakt, geïmplementeerd in dezelfde webruimte. Houd er rekening mee dat het niet mogelijk is om webruimten te verplaatsen nadat ze zijn gemaakt. u kunt een plan niet verplaatsen naar dezelfde webruimte als een ander abonnement door het te verplaatsen naar een andere resource groep.
> 

1. Blader in het [Azure Portal](https://portal.azure.com)naar de app die u wilt verplaatsen.

1. Zoek in het menu naar de sectie **app service plan** .

1. Selecteer **wijziging app service plan** om de **app service plan** kiezer te openen.

   ![App Service plan kiezer.][change] 

1. Selecteer in de **app service plan** kiezer een bestaand plan om deze app naar te verplaatsen.   

Op de pagina **app service plan selecteren** worden alleen abonnementen weer gegeven die deel uitmaken van dezelfde resource groep en geografische regio als het app service plan van de huidige app.

Elk abonnement heeft een eigen prijs categorie. Als u bijvoorbeeld een site verplaatst van een **gratis** laag naar een **Standard** -laag, kunnen alle hieraan toegewezen apps de functies en resources van de laag **standaard** gebruiken. Het verplaatsen van een app van een abonnement met een hoger niveau naar een onderlaagd abonnement betekent echter dat u geen toegang meer hebt tot bepaalde functies. Als uw app gebruikmaakt van een functie die niet beschikbaar is in het doel abonnement, krijgt u een fout melding waarin wordt aangegeven welke functie wordt gebruikt die niet beschikbaar is. 

Als een van uw apps bijvoorbeeld SSL-certificaten gebruikt, ziet u mogelijk het volgende fout bericht:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

In dit geval moet u, voordat u de app naar het doel abonnement kunt verplaatsen, het volgende doen:
- Schaal de prijs categorie van het doel abonnement op **basis** of hoger.
- Verwijder alle SSL-verbindingen met uw app.

## <a name="move-an-app-to-a-different-region"></a>Een app verplaatsen naar een andere regio

De regio waarin uw app wordt uitgevoerd, is de regio van het App Service plan waarin deze zich bevindt. U kunt de regio van een App Service abonnement echter niet wijzigen. Als u uw app in een andere regio wilt uitvoeren, is een alternatief voor het klonen van apps. Bij het klonen wordt een kopie van uw app in een nieuw of bestaand App Service plan in elke regio gemaakt.

U vindt de **kloon-app** in de sectie **hulp middelen voor ontwikkel aars** van het menu.

> [!IMPORTANT]
> Voor klonen gelden enkele beperkingen. U kunt deze informatie lezen in [Azure app service](app-service-web-app-cloning.md)het klonen van apps.

## <a name="scale-an-app-service-plan"></a>Een App Service plan schalen

Zie [een app omhoog schalen in azure](manage-scale-up.md)om de prijs categorie van een app service plan omhoog te schalen.

Als u het aantal exemplaren van een app wilt uitschalen, raadpleegt u [aantal exemplaren hand matig of automatisch schalen](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Een App Service-abonnement verwijderen

Om onverwachte kosten te voor komen, wordt het plan ook standaard verwijderd wanneer u de laatste app in een App Service App Service plan verwijdert. Als u ervoor kiest om het plan te houden, moet u het abonnement wijzigen in de laag **gratis** , zodat u niet in rekening wordt gebracht.

> [!IMPORTANT]
> App Service plannen waaraan geen apps zijn gekoppeld, worden nog steeds kosten in rekening gebracht omdat ze de geconfigureerde VM-instanties blijven reserveren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een app omhoog schalen in azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
