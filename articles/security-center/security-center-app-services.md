---
title: Uw Azure App Service-webapps en API's beveiligen
description: Met dit artikel u aan de slag met het beveiligen van uw Azure App Service-webapps en API's in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 2d81e1a1218add504e1e35015276b6924da0e3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616472"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Uw Azure App Service-webapps en API's beveiligen

Azure App Service is een volledig beheerd platform voor het bouwen en hosten van uw web-apps en API's zonder dat u zich zorgen hoeft te maken over het moeten beheren van de infrastructuur. Het biedt management-, monitoring- en operationele inzichten om te voldoen aan de vereisten op het gebied van prestaties, beveiliging en naleving op bedrijfsniveau. Zie [Azure App Service](https://azure.microsoft.com/services/app-service/)voor meer informatie .

Als u geavanceerde bedreigingsbeveiliging voor uw Azure App Service-abonnement wilt inschakelen, moet u het volgende doen:

* U abonneren op de standaardprijslaag van Azure Security Center
* Schakel het app-serviceplan in zoals hieronder wordt weergegeven. Security Center is native geïntegreerd met App Service, waardoor de noodzaak voor implementatie en onboarding elimineert - de integratie is transparant.
* Een App Service-abonnement hebben dat is gekoppeld aan speciale machines. Ondersteunde plannen zijn: Basic, Standard, Premium, Isolated of Linux. Security Center biedt geen ondersteuning voor de plannen Gratis, Gedeeld of Verbruik. Zie [App-serviceplannen](https://azure.microsoft.com/pricing/details/app-service/plans/)voor meer informatie .

Als het App Service-abonnement is ingeschakeld, beoordeelt Security Center de bronnen die onder uw App Service-abonnement vallen en genereert het beveiligingsaanbevelingen op basis van de bevindingen. Security Center beschermt het VM-exemplaar waarin uw App Service wordt uitgevoerd en de beheerinterface. Het controleert ook verzoeken en antwoorden die worden verzonden naar en van uw apps die worden uitgevoerd in App Service.

Security Center maakt gebruik van de schaal van de cloud en de zichtbaarheid die Azure heeft als cloudprovider om te controleren op veelvoorkomende web-app-aanvallen. Security Center kan aanvallen op uw toepassingen ontdekken en opkomende aanvallen identificeren - zelfs wanneer aanvallers zich in de verkenningsfase bevinden, scannen om kwetsbaarheden in meerdere Azure-gehoste toepassingen te identificeren. Als Azure-native service bevindt Security Center zich ook in een unieke positie om hostgebaseerde beveiligingsanalyses te bieden die de onderliggende compute-knooppunten voor deze PaaS bestrijken, waardoor Security Center aanvallen kan detecteren op webtoepassingen die al zijn misbruikt. Zie [Bedreigingsbeveiliging voor Azure App Service voor](threat-protection.md#app-services)meer informatie.


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Controle en beveiliging van app-service mogelijk maken

1. Kies beveiligingscentrum in de Azure-portal.
2. Ga naar **& instellingen prijzen** en kies een abonnement.
3. Schakel **onder De laag Prijzen**in de rij **App-service** uw abonnement in op **Ingeschakeld**.

    [![App-services inschakelen in uw abonnement op de standaardlaag](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Het aantal exemplaren dat wordt vermeld voor uw **resourcehoeveelheid** vertegenwoordigt het totale aantal rekeninstanties in alle App Service-abonnementen op dit abonnement, dat wordt uitgevoerd op het moment dat u het prijsmodel hebt geopend.
>
> Azure App Service biedt verschillende abonnementen. Uw App-serviceplan definieert de set rekenbronnen die een web-app moet uitvoeren. Deze zijn gelijk aan server farms in conventionele webhosting. Een of meer apps kunnen worden geconfigureerd om op dezelfde computerbronnen (of in hetzelfde App Service-abonnement) te worden uitgevoerd.
>
>Als u het aantal wilt valideren, gaat u naar 'App Service-plannen' in de Azure Portal, waar u het aantal rekeninstanties zien dat door elk abonnement wordt gebruikt. 






Als u de controle en aanbevelingen voor uw app-service wilt uitschakelen, herhaalt u dit proces en schakelt u uw **App Service-abonnement** in op **Uitgeschakeld.**



## <a name="see-also"></a>Zie ook
In dit artikel hebt u kunnen lezen hoe u de bewakingsmogelijkheden in Azure Security Center gebruikt. Zie de volgende artikelen voor meer informatie over Azure Security Center:

* [Beveiligingsbeleid instellen in Azure Security Center](tutorial-security-policy.md): leer hoe u beveiligingsinstellingen configureert in Azure Security Center.
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [App-services:](security-center-virtual-machine-protection.md#app-services)bekijk een lijst met uw App-serviceomgevingen met overzichten in de status.
* [Partneroplossingen controleren met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt controleren.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/): Zoek blogberichten over Azure-beveiliging en -naleving.
