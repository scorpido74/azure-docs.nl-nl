---
title: App Services beveiligen in Azure Security Center | Microsoft Docs
description: Dit artikel helpt u bij het beveiligen van uw App Services in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 68f7c47f0a0f56085d632f1c1741318f440b41ee
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202481"
---
# <a name="protect-app-service-with-azure-security-center"></a>App Service beveiligen met Azure Security Center
Dit artikel helpt u bij het gebruik van Azure Security Center voor het bewaken en beveiligen van uw toepassingen die boven op App Service worden uitgevoerd.

Met App Service kunt u webtoepassingen bouwen en hosten in de programmeer taal van uw keuze zonder dat u infra structuur hoeft te beheren. App Service biedt automatisch schalen en hoge Beschik baarheid, biedt ondersteuning voor zowel Windows als Linux, maar ook voor automatische implementaties van GitHub, Azure DevOps of een Git-opslag plaats. 

Beveiligings problemen in webtoepassingen worden vaak misbruikt door aanvallers, omdat ze een gemeen schappelijke en dynamische interface hebben voor vrijwel elke organisatie op internet. Aanvragen voor toepassingen die worden uitgevoerd op App Service, passeren verschillende gateways die zijn geïmplementeerd in azure-data centers over de hele wereld, die verantwoordelijk zijn voor de route ring van elke aanvraag naar de bijbehorende toepassing. 

Azure Security Center kunt evaluaties en aanbevelingen uitvoeren voor uw toepassingen die worden uitgevoerd in App Service in de sandboxes in uw VM-of on-demand-exemplaren. Door gebruik te maken van de zicht baarheid die Azure als Cloud provider heeft Security Center worden uw App Service interne logboeken geanalyseerd om te controleren op veelvoorkomende web app-aanvallen die vaak op meerdere doelen worden uitgevoerd.

Security Center maakt gebruik van de schaal van de cloud om aanvallen op uw App Service toepassingen te identificeren en zich te richten op opkomende aanvallen, terwijl aanvallers zich in de Reconnaissance-fase bevinden, scannen op beveiligings problemen op meerdere websites, gehost op Azure. Security Center maakt gebruik van analyse-en machine learning modellen om alle interfaces te behandelen, zodat klanten kunnen communiceren met hun toepassingen, hetzij via HTTP of via een beheer methode. Als een service van de eerste partij in Azure is Security Center bovendien ook in een unieke positie voor het bieden van op een host gebaseerde beveiligings analyse voor de onderliggende reken knooppunten voor deze PaaS, waardoor Security Center voor het detecteren van aanvallen op webtoepassingen die al misbruikt.

## <a name="prerequisites"></a>Vereisten

Als u uw app-service wilt controleren en beveiligen, moet u een App Service-plan hebben dat is gekoppeld aan toegewezen machines. Deze plannen komen in aanmerking: Basic, Standard, Premium, Isolated of Linux. Azure Security Center biedt geen ondersteuning voor de plannen Gratis, Gedeeld of Verbruik. Zie [app service-abonnementen](https://azure.microsoft.com/pricing/details/app-service/plans/)voor meer informatie.

## <a name="security-center-protection"></a>Security Center beveiliging

Azure Security Center beschermt het VM-exemplaar waarin uw App Service wordt uitgevoerd en de beheer interface. Er worden ook aanvragen en antwoorden gecontroleerd die worden verzonden naar en van uw apps die worden uitgevoerd in App Service.

Security Center is systeem eigen geïntegreerd met App Service, waardoor het niet nodig is om de implementatie en het onboarding te elimineren: de integratie is volledig transparant.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Bewaking en beveiliging van App Service inschakelen

1. Kies in azure Security Center.
2. Ga naar **prijs & instellingen** en kies een abonnement.
3. Schakel onder **prijs categorie**in de rij **app service** het abonnement in op **ingeschakeld**.

![scha kelen tussen apps in app service](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Het aantal exemplaren dat voor uw resource hoeveelheid wordt vermeld, vertegenwoordigt het aantal relevante exemplaren van de app service dat actief is op het moment dat u de Blade prijs categorie hebt geopend. Omdat dit nummer kan worden gewijzigd op basis van de gekozen schaal opties, wordt het aantal exemplaren waarvoor u in rekening wordt gebracht, dienovereenkomstig gewijzigd.

Als u controle en aanbevelingen voor uw App Service wilt uitschakelen, herhaalt u dit proces en schakelt u het **app service** plan in op **uitgeschakeld**.



## <a name="see-also"></a>Zie ook
In dit artikel hebt u kunnen lezen hoe u de bewakingsmogelijkheden in Azure Security Center gebruikt. Zie de volgende onderwerpen voor meer informatie over het Azure Beveiligingscentrum:

* [Beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md): Meer informatie over het configureren van beveiligings instellingen in Azure Security Center.
* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md): Informatie over het beheren van en reageren op beveiligingswaarschuwingen.
* [App-Services](security-center-virtual-machine-protection.md#app-services):  Bekijk een lijst met uw app service-omgevingen met status overzichten.
* [Partner oplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): Meer informatie over het bewaken van de status van uw partneroplossingen.
* [Azure Security Center Veelgestelde vragen](security-center-faq.md): Raadpleeg de veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligings blog](https://blogs.msdn.com/b/azuresecurity/): Raadpleeg de blogberichten over beveiliging en naleving in Azure.
