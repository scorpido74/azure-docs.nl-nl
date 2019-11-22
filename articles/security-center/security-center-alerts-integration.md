---
title: Integratie van Azure-beveiligings producten met Azure Security Center
description: Dit onderwerp bevat Azure-beveiligings producten die zijn geïntegreerd met Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 693e7d35a0bb4c7dfbb3e033690a5e86e2c398a8
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278336"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Integratie van Azure-beveiligings producten in Azure Security Center

Azure Security Center biedt u aanvullende micro soft-licenties voor het werken met de volgende beveiligings producten:

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure Application Gateway biedt de functie Web Application Firewall (WAF) voor de gecentraliseerde beveiliging van uw webtoepassingen tegen bekende aanvallen en beveiligingsproblemen.

Webtoepassingen worden steeds gericht op kwaad aardige aanvallen die veelvoorkomende beveiligings problemen misbruiken. De Application Gateway WAF is gebaseerd op de core Rule set 3,0 of 2.2.9 van het open Web Application Security-project. De WAF wordt automatisch bijgewerkt om te beschermen tegen nieuwe beveiligings problemen, zonder dat er aanvullende configuratie nodig is. WAF-waarschuwingen worden gestreamd naar Security Center. Zie voor meer informatie over de waarschuwingen die door WAF worden gegenereerd, [CRS-regel groepen en-regels voor Web Application firewall](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).

## Azure DDoS Protection<a name="azure-ddos"></a>

DDoS-aanvallen (Distributed Denial of service) zijn bekend om eenvoudig te worden uitgevoerd. Ze zijn een geweldig beveiligings probleem, met name als u uw toepassingen naar de Cloud verplaatst. 

Met een DDoS-aanval wordt geprobeerd de resources van een toepassing uit te putten, waardoor de toepassing niet meer beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eind punt dat kan worden bereikt via internet.

Azure DDoS Protection, gecombineerd met aanbevolen procedures voor het ontwerpen van toepassingen, bieden een verdediging tegen DDoS-aanvallen. DDoS Protection biedt verschillende service lagen. Zie [Azure DDoS Protection Overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)voor meer informatie.

DDoS Protection Standard kan de volgende typen aanvallen beperken:

> [!div class="mx-tableFixed"]

|Waarschuwing|Beschrijving|
|---|---|
|**Volumetrische aanval gedetecteerd**|Het doel van deze aanval is om de netwerklaag te laten overstappen op een aanzienlijke hoeveelheid getrouwd verkeer. Dit omvat UDP-flooden, versterking van stromen en andere vervalste pakket stromen. DDoS Protection Standard verkleint deze potentiële multi-Gigabyte-aanvallen door ze te absorberen en te reinigen, waarbij de wereld wijde schaal van het netwerk automatisch wordt aangepast.|
|**Protocol aanval gedetecteerd**|Deze aanvallen genereren een doel dat niet toegankelijk is door een zwakte te misbruiken in de laag 3-en laag 4-Protocol stacks. Dit omvat SYN-flood-aanvallen, reflectie-aanvallen en andere protocol aanvallen. DDoS Protection Standard verkleint deze aanvallen, onderscheidt zich van schadelijk en betrouwbaar verkeer door interactie met de client en het blok keren van schadelijk verkeer.|
|**Aanval van resource (toepassing)-laag gedetecteerd**|Deze aanvallen richten op webtoepassingen om de overdracht van gegevens tussen hosts te verstoren. De aanvallen omvatten schendingen van het HTTP-protocol, SQL-injectie, cross-site scripting en andere Layer 7-aanvallen. Gebruik de Azure-toepassing gateway WAF, met DDoS Protection Standard, om deze aanvallen te verdedigen. Er zijn ook WAF-aanbiedingen van derden beschikbaar in azure Marketplace.|
