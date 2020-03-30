---
title: Samenwerken met Azure DDoS Protection Standard
description: Inzicht in partnermogelijkheden die zijn ingeschakeld door Azure DDoS Protection Standard.
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849680"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Samenwerken met Azure DDoS Protection Standard
In dit artikel worden partnermogelijkheden beschreven die zijn ingeschakeld door de Azure DDoS Protection Standard. Dit artikel is ontworpen om productmanagers en business development-rollen te helpen de beleggingspaden te begrijpen en inzicht te geven in de partnerwaardeproposities.

## <a name="background"></a>Achtergrond
DDoS-aanvallen (Distributed denial of service) zijn een van de belangrijkste beschikbaarheids- en beveiligingsproblemen die worden geuit door klanten die hun toepassingen naar de cloud verplaatsen. Met afpersing en hacktivisme wordt de gemeenschappelijke motivaties achter DDoS-aanvallen, ze zijn consequent toe te nemen in type, schaal, en frequentie van het optreden als ze zijn relatief eenvoudig en goedkoop te lanceren.

Azure DDoS Protection biedt tegenmaatregelen tegen de meest geavanceerde DDoS-bedreigingen, waarbij gebruik wordt gemaakt van de wereldwijde schaal van Azure-netwerken. De service biedt verbeterde DDoS-mitigatiemogelijkheden voor toepassingen en resources die zijn geïmplementeerd in virtuele netwerken.

Technologiepartners kunnen de bronnen van hun klanten in eigen land beschermen met Azure DDoS Protection Standard om de beschikbaarheids- en betrouwbaarheidsproblemen als gevolg van DDoS-aanvallen aan te pakken.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Inleiding tot Azure DDoS Protection Standard
Azure DDoS Protection Standard biedt verbeterde DDoS-mitigatiemogelijkheden tegen Layer 3- en Layer 4 DDoS-aanvallen. Hieronder volgen de belangrijkste functies van de DDoS Protection Standard-service.

### <a name="adaptive-real-time-tuning"></a>Adaptieve real-time tuning
Voor elke beveiligde toepassing stemt Azure DDoS Protection Standard automatisch de ddos-mitigatiebeleidsdrempels af op basis van de verkeersprofielpatronen van de toepassing. De service bereikt deze aanpassing door gebruik te maken van twee inzichten:

- Automatisch leren van verkeerspatronen per klant (per IP) voor Laag 3 en 4.
- Het minimaliseren van false positives, gezien het feit dat de schaal van Azure het mogelijk maakt om een aanzienlijke hoeveelheid verkeer te absorberen.

![Adaptieve real-time tuning](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Aanvalsanalyses, telemetrie, bewaking en waarschuwingen
Azure DDoS Protection identificeert en vermindert DDoS-aanvallen zonder tussenkomst van de gebruiker.

- Als de beveiligde bron zich bevindt in het abonnement dat onder Azure Security Center valt, stuurt DDoS Protection Standard automatisch een waarschuwing naar Security Center wanneer een DDoS-aanval wordt gedetecteerd en beperkt tegen de beveiligde toepassing.
- U ook [een waarschuwing configureren](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics) voor de metrische DDoS-aanval onder DDoS.
- U er bovendien voor kiezen om waarschuwingen voor de andere DDoS-statistieken te maken en [aanvalsanalyses](manage-ddos-protection.md#configure-ddos-attack-analytics) te configureren om inzicht te krijgen in de schaal van de aanval, verkeer dat wordt verwijderd, aanvalsvectoren, topbijdragers en andere details.

![DDoS-statistieken](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS snelle reactie (DRR)
DDoS Protection Standard-klanten hebben toegang tot [het Rapid Response-team](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/) tijdens een actieve aanval. DRR kan helpen met aanvalsonderzoek, aangepaste oplossingen tijdens een aanval en analyse na de aanval.

### <a name="sla-guarantee-and-cost-protection"></a>SLA-garantie en kostenbescherming
DDoS Protection Standard-service wordt gedekt door een SLA van 99,99% en kostenbescherming biedt resourcecredits voor schaalvoordelen tijdens een gedocumenteerde aanval. Zie [SLA voor Azure DDoS Protection voor](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)meer informatie.

## <a name="featured-partner-scenarios"></a>Aanbevolen partnerscenario's
De volgende zijn de belangrijkste voordelen die u behalen door te integreren met de Azure DDoS Protection Standard:

- De aangeboden services van partners (load balancer, webapplication firewall, firewall, enz.) aan hun klanten worden automatisch beveiligd (white labeled) door Azure DDoS Protection Standard in de back-end.
- Partners hebben toegang tot Azure DDoS Protection Standard-aanvalsanalyses en telemetrie die ze kunnen integreren met hun eigen producten en bieden een uniforme klantervaring.  
- Partners hebben toegang tot DDoS-ondersteuning voor snelle respons, zelfs als er geen snelle reactie van Azure is, voor DDoS-gerelateerde problemen.
- De beveiligde toepassingen van partners worden ondersteund door een DDoS SLA-garantie en kostenbescherming in het geval van DDoS-aanvallen.

## <a name="technical-integration-overview"></a>Overzicht van technische integratie
Azure DDoS Protection Standard partnermogelijkheden worden beschikbaar gesteld via Azure portal, API's en CLI/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integreren met DDoS Protection Standard
De volgende stappen zijn vereist voor partners om integratie met Azure DDoS Protection Standard te configureren:
1. Maak een DDoS Protection Plan in uw gewenste (partner)abonnement. Zie [Een DDoS-standaardbeveiligingsplan maken](manage-ddos-protection.md#create-a-ddos-protection-plan)voor stapsgewijze instructies.
   > [!NOTE]
   > Er hoeft slechts 1 DDoS Protection Plan te worden gemaakt voor een bepaalde tenant. 
2. Implementeer een service met openbaar eindpunt in uw (partner)abonnementen, zoals load balancer, firewalls en firewall voor webtoepassingen. 
3. Azure DDoS Protection Standard inschakelen op het virtuele netwerk van de service met openbare eindpunten met DDoS Protection Plan dat in de eerste stap is gemaakt. Zie [DDoS-standaardbeschermingsplan inschakelen](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network) voor stpe-by-step-instructies
   > [!IMPORTANT] 
   > Nadat Azure DDoS Protection Standard is ingeschakeld in een virtueel netwerk, worden alle openbare IP's binnen dat virtuele netwerk automatisch beveiligd. De oorsprong van deze openbare IP's kan zijn binnen Azure (clientabonnement) of buiten Azure. 
4. Mogelijk integreer je azure DDoS Protection Standard-telemetrie en aanvalsanalyses in uw toepassingsspecifieke klantgerichte dashboard. Zie [DDoS Protection telemetrie gebruiken](manage-ddos-protection.md#use-ddos-protection-telemetry)voor meer informatie over het gebruik van telemetrie. Zie [DDoS-aanvalsanalyses configureren](manage-ddos-protection.md#configure-ddos-attack-analytics) voor meer informatie over het configureren van aanvalsanalyses

### <a name="onboarding-guides-and-technical-documentation"></a>Onboarding gidsen en technische documentatie

- [Productpagina Azure DDoS Protection](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS Protection-documentatie](ddos-protection-overview.md)
- [Azure DDoS Protection API-verwijzing](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure virtual network API-verwijzing](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Help opvragen

- Als u vragen hebt over toepassings-, service- of productintegraties met Azure DDoS Protection Standard, neemt u contact op met de [Azure-beveiligingscommunity.](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)
- Volg discussies over [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Kom op de markt

- Het primaire programma voor het samenwerken met Microsoft is het [Microsoft Partner Network.](https://partner.microsoft.com/) – Microsoft Graph Security-integraties vallen in het [ISV-track (MPN Independent Software Vendor).](https://partner.microsoft.com/saas-solution-guide)
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) is het programma speciaal voor Microsoft Security Partners om uw beveiligingsproducten te verrijken en de vindbaarheid van uw integraties met Microsoft Security-producten te verbeteren.

## <a name="next-steps"></a>Volgende stappen
Bekijk bestaande partnerintegraties:

- [Barracuda WAF-as-a-service](https://www.barracuda.com/waf-as-a-service)
- [Azure Cloud WAF van Radware](https://www.radware.com/resources/microsoft-azure/)
