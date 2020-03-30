---
title: Een web-app maken in ASE v1
description: Meer informatie over het maken van web-apps in een App Service Environment v1. Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5c947617f0c27708e72f9bff92e2b0041473cd92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266193"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Een web-app maken in een App-serviceomgeving v1

> [!NOTE]
> Dit artikel gaat over de App Service Environment v1.  Er is een nieuwere versie van de App Service-omgeving die gemakkelijker te gebruiken is en draait op een krachtigere infrastructuur. Voor meer informatie over de nieuwe versie start u met de [inleiding tot de app-serviceomgeving.](intro.md)
> 

## <a name="overview"></a>Overzicht
In deze zelfstudie ziet u hoe u web-apps en App-serviceplannen maakt in een [App Service Environment v1](app-service-app-service-environment-intro.md) (ASE). 

> [!NOTE]
> Zie [Een .NET-webapp](../app-service-web-get-started-dotnet.md) maken of een van de gerelateerde zelfstudies voor andere talen en frameworks als u wilt leren hoe u een web-app maakt, maar dit niet hoeft te doen in een App-serviceomgeving.
> 
> 

## <a name="prerequisites"></a>Vereisten
In deze zelfstudie wordt ervan uitgegaan dat u een App Service-omgeving hebt gemaakt. Zie [Een app-serviceomgeving maken](app-service-web-how-to-create-an-app-service-environment.md)als u dat nog niet hebt gedaan. 

## <a name="create-a-web-app"></a>Een webtoepassing maken
1. Klik in de [Azure Portal](https://portal.azure.com/)op Een bron maken > Web + Mobile > **Web App**. 
   
    ![][1]
2. Selecteer uw abonnement.  
   
    Als u meerdere abonnementen hebt, moet u hetzelfde abonnement gebruiken als u hebt gebruikt bij het maken van de omgeving om een app in uw app-serviceomgeving te maken. 
3. Selecteer of maak een resourcegroep.
   
    *Met resourcegroepen* u gerelateerde Azure-resources beheren als een eenheid en zijn ze handig bij het opstellen van *RBAC-regels (Role-based access control)* voor uw apps. Zie voor meer informatie [Overzicht van Azure Resource Manager][ResourceGroups]. 
4. Selecteer of maak een App Service-plan.
   
    *App Service-abonnementen* zijn beheerde sets van web-apps.  Normaal gesproken wordt wanneer u prijzen selecteert, de in rekening gebrachte prijs toegepast op het App Service-abonnement in plaats van op de afzonderlijke apps. In een ASE betaalt u voor de compute-exemplaren die aan de ASE zijn toegewezen in plaats van wat u bij uw ASP hebt vermeld.  Als u het aantal exemplaren van een web-app wilt opschalen, schaalt u de exemplaren van uw App Service-abonnement op en heeft dit gevolgen voor alle web-apps in dat abonnement.  Sommige functies, zoals siteslots of VNET-integratie, bevatten ook kwantitatieve beperkingen in het plan.  Zie overzicht van [Azure App Service-abonnementen voor](../overview-hosting-plans.md) meer informatie
   
    U de appserviceplannen in uw ASE identificeren door te kijken naar de locatie die wordt vermeld onder de naam van het abonnement.  
   
    ![][5]
   
    Als u een App Service-abonnement wilt gebruiken dat al bestaat in uw App-serviceomgeving, selecteert u dat abonnement. Als u een nieuw App Service-abonnement wilt maken, raadpleegt u het volgende gedeelte van deze zelfstudie, [Een App Service-abonnement maken in een App-serviceomgeving](#createplan).
5. Voer de naam voor uw web-app in en klik op **Maken**. 
   
    Als uw ASE een externe VIP gebruikt, is de URL van een app in een ASE: [*sitename*]. [*naam van uw App Service Environment*].p.azurewebsites.net in plaats van [*sitename*].azurewebsites.net
   
    Als uw ASE een interne VIP gebruikt, is de URL van een app in die ASE: [*sitename*]. [*subdomein opgegeven tijdens ASE-creatie*]   
    Nadat u uw ASP hebt geselecteerd tijdens het maken van ASE, ziet u de subdomeinupdate hieronder **Naam**

## <a name="create-an-app-service-plan"></a><a name="createplan"></a>Een App-serviceplan maken
Wanneer u een App Service-abonnement maakt in een App-serviceomgeving, zijn de werknemerskeuzes anders omdat er geen gedeelde werknemers in een ASE zijn.  De werknemers die u moet gebruiken zijn degenen die zijn toegewezen aan de ASE door de beheerder.  Dit betekent dat u voor het maken van een nieuw plan meer werknemers aan uw ASE-werknemersgroep moet toegewezen hebben dan het totale aantal exemplaren voor al uw plannen dat al in die werknemersgroep is.  Als u niet genoeg werknemers in uw ASE-werknemersgroep hebt om uw plan te maken, moet u samenwerken met uw ASE-beheerder om ze te laten toevoegen.

Een ander verschil met App Service-abonnementen die worden gehost door een App Service-omgeving is het gebrek aan prijsselectie.  Wanneer u een App-serviceomgeving hebt, betaalt u voor rekenresources die door het systeem worden gebruikt en hebt u geen kosten toegevoegd voor de plannen in die omgeving.  Normaal gesproken selecteert u bij het maken van een App Service-abonnement een prijsplan dat uw facturering bepaalt.  Een App Service Omgeving is in wezen een privélocatie waar u inhoud maken.  U betaalt voor het milieu en niet om uw inhoud te hosten.

In de volgende instructies ziet u hoe u een App Service-abonnement maakt terwijl u een web-app maakt, zoals uitgelegd in het vorige gedeelte van de zelfstudie.

1. Klik **op Nieuw maken** in de gebruikersinterface voor het selecteren van het abonnement en geef een naam op voor uw abonnement, net zoals u dat normaal gesproken buiten een ASE zou doen.
2. Selecteer de ASE die u wilt gebruiken in uw locatiekiezer.
   
    Omdat een App Service-omgeving in wezen een locatie voor privé-implementatie is, wordt deze weergegeven onder Locatie. 
   
    ![][2]
   
    Na selectie van een ASE in de locatiekiezer maakt het App Service-abonnement ui-updates.  De locatie toont nu de naam van het ASE-systeem en de regio waarin het zich bevindt en de prijsplankiezer wordt vervangen door een werknemerspoolkiezer.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Een werknemersgroep selecteren
Normaal gesproken zijn er in Azure App Service en buiten een App Service-omgeving 3 rekengroottes beschikbaar met de selectie van een speciaal prijsplan.  Op een vergelijkbare manier u voor een ASE maximaal 3 groepen werknemers definiëren en de rekengrootte opgeven die voor die werknemersgroep wordt gebruikt.  Wat dat betekent voor huurders van de ASE is dat in plaats van het selecteren van een prijsplan met rekengrootte voor uw App Service-abonnement, u een zogenaamde *werknemersgroep*selecteert.  

De gebruikersinterface voor de selectie van de groep werknemers toont de rekengrootte die wordt gebruikt voor die groep werknemers onder de naam.  De beschikbare hoeveelheid verwijst naar het aantal rekeninstanties dat beschikbaar is voor gebruik in die groep.  De totale pool kan eigenlijk meer exemplaren dan dit aantal hebben, maar deze waarde verwijst naar eenvoudig hoeveel er niet in gebruik zijn.  Als u uw app-serviceomgeving moet aanpassen om meer rekenbronnen toe te voegen, raadpleegt u [Uw app-serviceomgeving configureren.](app-service-web-configure-an-app-service-environment.md)

![][4]

In dit voorbeeld ziet u slechts twee werknemersgroepen beschikbaar. Dat komt omdat de ASE-beheerder alleen hosts heeft toegewezen aan deze twee werknemerspools.  De derde zou verschijnen wanneer er VM's in zijn toegewezen.  

## <a name="after-web-app-creation"></a>Na het maken van web-apps
Er zijn een paar overwegingen voor het uitvoeren van web-apps en het beheren van App Service-abonnementen in een ASE waarmee rekening moet worden gehouden.  

Zoals eerder opgemerkt, is de eigenaar van de ASE verantwoordelijk voor de grootte van het systeem en als gevolg daarvan zijn zij ook verantwoordelijk voor het waarborgen dat er voldoende capaciteit is om de gewenste App Service-plannen te hosten. Als er geen beschikbare werknemers zijn, u uw App Service-abonnement niet maken.  Dit geldt ook voor het opschalen van uw web-app.  Als u meer exemplaren nodig hebt, moet u uw beheerder van de app-serviceomgeving ertoe brengen meer werknemers toe te voegen.

Na het maken van uw web app en App Service plan is het een goed idee om het op te schalen.  In een ASE moet u altijd ten minste 2 exemplaren van uw App Service-abonnement hebben om fouttolerantie voor uw apps te bieden.  Het schalen van een App Service-abonnement in een ASE is hetzelfde als normaal via de gebruikersinterface van het App-serviceplan.  Informatie over schalen, [Een web-app schalen in een app-serviceomgeving](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
