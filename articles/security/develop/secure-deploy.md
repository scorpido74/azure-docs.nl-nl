---
title: Veilige toepassingen implementeren op Microsoft Azure
description: In dit artikel worden aanbevolen procedures besproken om rekening mee te houden tijdens de release- en reactiefasen van uw webtoepassingsproject.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934876"
---
# <a name="deploy-secure-applications-on-azure"></a>Veilige toepassingen implementeren op Azure
In dit artikel presenteren we beveiligingsactiviteiten en besturingselementen om rekening mee te houden wanneer u toepassingen voor de cloud implementeert. Beveiligingsvragen en concepten die moeten worden overwogen tijdens de release- en antwoordfasen van de Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) komen aan bod. Het doel is om u te helpen bij het definiëren van activiteiten en Azure-services die u gebruiken om een veiligere toepassing te implementeren.

De volgende SDL-fasen komen aan bod in dit artikel:

- Release
- Antwoord

## <a name="release"></a>Release
De focus van de release fase is het voorbereiden van een project voor publieke release.
Dit omvat het plannen van manieren om onderhoudstaken na de release effectief uit te voeren en beveiligingsproblemen aan te pakken die later kunnen optreden.

### <a name="check-your-applications-performance-before-you-launch"></a>Controleer de prestaties van uw toepassing voordat u start

Controleer de prestaties van uw toepassing voordat u deze start of implementeer updates voor de productie. Voer cloudgebaseerde [belastingstests](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) uit met Visual Studio om prestatieproblemen in uw toepassing te vinden, de implementatiekwaliteit te verbeteren, ervoor te zorgen dat uw toepassing altijd up- of beschikbaar is en dat uw toepassing verkeer voor uw lancering kan verwerken.

### <a name="install-a-web-application-firewall"></a>Een firewall voor webtoepassingen installeren

Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van veelvoorkomende bekende beveiligingsproblemen. Veel voorkomende onder deze exploits zijn SQL-injectie-aanvallen en cross-site scripting-aanvallen. Het voorkomen van deze aanvallen in toepassingscode kan een uitdaging zijn. Het kan rigoureus onderhoud, patchen en bewaking vereisen op vele lagen van de toepassingstopologie. Een gecentraliseerde WAF helpt het beveiligingsbeheer eenvoudiger te maken. Een WAF-oplossing kan ook reageren op een beveiligingsbedreiging door een bekend beveiligingslek op een centrale locatie te patchen versus elke afzonderlijke webapplicatie te beveiligen.

De [WAF van Azure Application Gateway](../../application-gateway/waf-overview.md) biedt gecentraliseerde bescherming van uw webtoepassingen tegen veelvoorkomende exploits en kwetsbaarheden. De WAF is gebaseerd op regels uit de [OWASP-kernregelsets](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 of 2.2.9.

### <a name="create-an-incident-response-plan"></a>Een reactieplan voor incidenten maken

Het opstellen van een incident response plan is van cruciaal belang om u te helpen nieuwe bedreigingen die kunnen ontstaan in de tijd aan te pakken. Het opstellen van een incidentresponseplan omvat het identificeren van geschikte beveiligingscontactpersonen voor noodgevallen en het opstellen van beveiligingsonderhoudsplannen voor code die is overgenomen van andere groepen in de organisatie en voor gelicentieerde code van derden.

### <a name="conduct-a-final-security-review"></a>Een laatste beveiligingsbeoordeling uitvoeren

Door alle beveiligingsactiviteiten die zijn uitgevoerd, bewust te controleren, u ervoor zorgen dat uw softwarerelease of -toepassing gereed is voor de release of toepassing van uw software. De uiteindelijke security review (FSR) omvat meestal het onderzoeken van bedreigingsmodellen, tools uitgangen, en prestaties tegen de kwaliteit poorten en bug bars die werden gedefinieerd in de vereisten fase.

### <a name="certify-release-and-archive"></a>Release en archiveren certificeren

Het certificeren van software voordat een release wordt uitgebracht, zorgt ervoor dat aan de beveiligings- en privacyvereisten wordt voldaan. Het archiveren van alle relevante gegevens is essentieel voor het uitvoeren van servicetaken na de release. Archivering helpt ook de langetermijnkosten in verband met duurzame softwareengineering te verlagen.

## <a name="response"></a>Antwoord
De reactie fase post-release centra op het ontwikkelingsteam in staat en beschikbaar om adequaat te reageren op eventuele meldingen van opkomende software bedreigingen en kwetsbaarheden.

### <a name="execute-the-incident-response-plan"></a>Het incidentresponsplan uitvoeren

In staat zijn om het incident response plan ingesteld in de release fase te implementeren is essentieel om klanten te helpen beschermen tegen software beveiliging of privacy kwetsbaarheden die ontstaan.

### <a name="monitor-application-performance"></a>Prestaties van toepassingen controleren

Doorlopende monitoring van uw toepassing nadat deze is geïmplementeerd, helpt u mogelijk om prestatieproblemen en beveiligingsproblemen op te sporen.
Azure-services die helpen bij het bewaken van toepassingen zijn:

  - Azure Application Insights
  - Azure Security Center

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) is een uitbreidbare Application Performance Management (APM) service voor webontwikkelaars op meerdere platforms. Hiermee kunt u uw livewebtoepassing controleren. Application Insights detecteert automatisch prestatieafwijkingen. Het bevat krachtige analysetools om u te helpen problemen te diagnosticeren en te begrijpen wat gebruikers daadwerkelijk met uw app doen. Het is bedoeld om u te helpen de prestaties en bruikbaarheid continu te verbeteren.

#### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-intro.md) helpt u bedreigingen te voorkomen, detecteren en erop te reageren met meer inzicht in (en controle over) de beveiliging van uw Azure-resources, waaronder webtoepassingen. Azure Security Center helpt bij het detecteren van bedreigingen die anders onopgemerkt zouden kunnen blijven. Het werkt met verschillende beveiligingsoplossingen.

De gratis laag van security center biedt beperkte beveiliging voor alleen uw Azure-bronnen. De [standaardlaag van het Beveiligingscentrum](../../security-center/security-center-onboarding.md) breidt deze mogelijkheden uit naar on-premises resources en andere clouds.
Security Center Standard helpt u:

  - Beveiligingsproblemen zoeken en oplossen.
  - Pas toegangs- en toepassingsbesturingselementen toe om schadelijke activiteiten te blokkeren.
  - Detecteer bedreigingen met behulp van analytics en intelligentie.
  - Reageer snel wanneer u wordt aangevallen.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen raden we beveiligingscontroles en -activiteiten aan die u kunnen helpen bij het ontwerpen en ontwikkelen van veilige toepassingen.

- [Veilige toepassingen ontwerpen](secure-design.md)
- [Veilige toepassingen ontwikkelen](secure-develop.md)
