---
title: Detectie van bedreigingen voor Azure-beveiligings producten in Azure Security Center
description: Dit onderwerp bevat de Azure-beveiligings producten waarvoor Azure Security Center detectie van bedreigingen kan bieden
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 1ea207f0ba09e0637a08632d5c56591fd1335b22
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75665723"
---
# <a name="threat-detection-for-azure-waf-and-azure-ddos-protection"></a>Detectie van bedreigingen voor Azure WAF en Azure DDoS Protection

Azure Security Center kunt detectie van bedreigingen bieden voor de volgende Azure-beveiligings producten (een afzonderlijke licentie voor elk product is vereist):

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure Application Gateway biedt de functie Web Application Firewall (WAF) voor de gecentraliseerde beveiliging van uw webtoepassingen tegen bekende aanvallen en beveiligingsproblemen.

Webtoepassingen worden steeds gericht op kwaad aardige aanvallen die veelvoorkomende beveiligings problemen misbruiken. De Application Gateway WAF is gebaseerd op de core Rule set 3,0 of 2.2.9 van het open Web Application Security-project. De WAF wordt automatisch bijgewerkt om te beschermen tegen nieuwe beveiligings problemen, zonder dat er aanvullende configuratie nodig is. WAF-waarschuwingen worden gestreamd naar Security Center. Zie voor meer informatie over de waarschuwingen die door WAF worden gegenereerd, [CRS-regel groepen en-regels voor Web Application firewall](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).

## Azure DDoS Protection<a name="azure-ddos"></a>

DDoS-aanvallen (Distributed Denial of service) zijn bekend om eenvoudig te worden uitgevoerd. Ze zijn een geweldig beveiligings probleem, met name als u uw toepassingen naar de Cloud verplaatst. 

Met een DDoS-aanval wordt geprobeerd de resources van een toepassing uit te putten, waardoor de toepassing niet meer beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eind punt dat kan worden bereikt via internet.

Azure DDoS Protection, gecombineerd met aanbevolen procedures voor het ontwerpen van toepassingen, bieden een verdediging tegen DDoS-aanvallen. DDoS Protection biedt verschillende service lagen. Zie [Azure DDoS Protection Overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)voor meer informatie.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureddos)voor een overzicht van de waarschuwingen voor Azure DDoS Protection.