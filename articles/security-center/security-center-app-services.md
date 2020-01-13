---
title: Uw Azure App Service web-apps en Api's beveiligen
description: Dit artikel helpt u bij het beveiligen van uw Azure App Service web apps en Api's in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 81ef598c846d98548be2d3e7647166d655398921
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75912816"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Uw Azure App Service web-apps en Api's beveiligen

Azure App Service is een volledig beheerd platform voor het bouwen en hosten van uw web-apps en Api's zonder dat u zich zorgen hoeft te maken over het beheer van de infra structuur. Het biedt beheer, bewaking en operationele inzichten om te voldoen aan de vereisten voor prestaties, beveiliging en naleving van de bedrijfs kwaliteit. Zie [Azure app service](https://azure.microsoft.com/services/app-service/)voor meer informatie.

Als u geavanceerde beveiliging tegen bedreigingen wilt inschakelen voor uw Azure App Service-abonnement, moet u het volgende doen:

* Abonneren op de prijs categorie Standard van Azure Security Center
* Schakel het App Service plan in, zoals hieronder wordt weer gegeven. Security Center is systeem eigen geïntegreerd met App Service, waardoor implementatie en onboarding niet meer nodig is. de integratie is dus transparant.
* Een App Service plan hebben dat is gekoppeld aan toegewezen machines. Ondersteunde abonnementen zijn: Basic, Standard, Premium, geïsoleerd of Linux. Security Center biedt geen ondersteuning voor de gratis, gedeelde of verbruikte abonnementen. Zie [app service-abonnementen](https://azure.microsoft.com/pricing/details/app-service/plans/)voor meer informatie.

Wanneer het App Service-abonnement is ingeschakeld, bepaalt Security Center de resources die door uw App Service plan worden gedekt en worden beveiligings aanbevelingen gegenereerd op basis van de bevindingen. Security Center beschermt het VM-exemplaar waarin uw App Service wordt uitgevoerd en de beheer interface. Er worden ook aanvragen en antwoorden gecontroleerd die worden verzonden naar en van uw apps die worden uitgevoerd in App Service.

Security Center maakt gebruik van de schaal van de Cloud en de zicht baarheid die Azure als een Cloud provider heeft, om te controleren op veelvoorkomende web app-aanvallen. Security Center kunt aanvallen op uw toepassingen detecteren en opkomende aanvallen identificeren, zelfs wanneer een aanvaller zich in de Reconnaissance-fase bevindt, scans voor het identificeren van beveiligings problemen in meerdere door Azure gehoste toepassingen. Als Azure-systeem eigen service is Security Center ook in een unieke positie voor het bieden van op de host gebaseerde beveiligings analyse voor de onderliggende reken knooppunten voor deze PaaS, Security Center waardoor het mogelijk is om aanvallen te detecteren op webtoepassingen die al zijn misbruikt. Zie voor meer informatie over het detecteren van bedreigingen van de Security Center van Azure App Service [bedreigingen detectie voor native computing](security-center-alerts-compute.md#azure-app-service-)in de Cloud.


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Bewaking en beveiliging van App Service inschakelen

1. Kies in het Azure Portal Security Center.
2. Ga naar **prijs & instellingen** en kies een abonnement.
3. Schakel onder **prijs categorie**in de rij **app service** het abonnement in op **ingeschakeld**.

    [![app-Services in te scha kelen in uw abonnement op de Standard-laag](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Het aantal exemplaren dat wordt weer gegeven voor de **resource hoeveelheid** vertegenwoordigt het totale aantal reken instanties, in alle app service plannen voor dit abonnement, dat wordt uitgevoerd op het moment dat u de Blade prijs categorie hebt geopend.
>
> Azure App Service biedt diverse plannen. Uw App Service-abonnement definieert de set reken resources voor het uitvoeren van een web-app. Deze zijn gelijk aan server farms in conventionele webhosting. Een of meer apps kunnen worden geconfigureerd om te worden uitgevoerd op dezelfde computer bronnen (of in hetzelfde App Service-abonnement).
>
>Voor het valideren van het aantal, het hoofd naar ' App Service plannen ' in de Azure-Portal, waar u het aantal reken instanties kunt zien dat door elk abonnement wordt gebruikt. 






Als u controle en aanbevelingen voor uw App Service wilt uitschakelen, herhaalt u dit proces en schakelt u het **app service** plan in op **uitgeschakeld**.



## <a name="see-also"></a>Zie ook
In dit artikel hebt u kunnen lezen hoe u de bewakingsmogelijkheden in Azure Security Center gebruikt. Raadpleeg de volgende artikelen voor meer informatie over Azure Security Center:

* [Beveiligingsbeleid instellen in Azure Security Center](tutorial-security-policy.md): leer hoe u beveiligingsinstellingen configureert in Azure Security Center.
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [App Services](security-center-virtual-machine-protection.md#app-services): Bekijk een lijst van uw app service-omgevingen met status overzichten.
* [Partneroplossingen controleren met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt controleren.
* [Azure Security Center FAQ](security-center-faq.md) (Veelgestelde vragen over Azure Security Center): raadpleeg veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligingsblog](https://blogs.msdn.com/b/azuresecurity/): lees blogberichten over de beveiliging en naleving van Azure.
