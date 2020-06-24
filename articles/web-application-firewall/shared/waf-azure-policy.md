---
title: Azure Web Application firewall en Azure Policy
description: Azure Web Application firewall (WAF) in combi natie met Azure Policy kan u helpen organisatie standaarden af te dwingen en op schaal naleving te beoordelen voor WAF-resources
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: rimansdo
ms.openlocfilehash: 4c1fd53eb6ebf1f1aebdfba99b736e26bd6cff2b
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85306894"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure Web Application firewall en Azure Policy

Azure Web Application firewall (WAF) in combi natie met Azure Policy kan u helpen organisatie standaarden af te dwingen en de naleving op schaal te beoordelen voor WAF-resources. Azure Policy is een beheer programma dat een geaggregeerde weer gave biedt om de algehele status van de omgeving te evalueren, met de mogelijkheid om in te zoomen op de nauw keurigheid per bron per beleid. Azure Policy helpt u ook om uw resources te laten voldoen aan de naleving van bulksgewijs herstel voor bestaande resources en automatisch herstel voor nieuwe resources.

## <a name="azure-policies-for-web-application-firewall"></a>Azure-beleid voor Web Application firewall

Er zijn verschillende ingebouwde Azure-beleids regels voor het beheren van WAF-resources. Een uitsplitsing van de beleids regels en hun functionaliteit is als volgt:

1. De **Web Application firewall moet zijn ingeschakeld voor de Azure front-deur service of Application Gateway**: Azure front-deur Services en toepassings gateways worden geëvalueerd op als er een WAF aanwezig is bij het maken van resources. Het beleid heeft drie effecten: controleren, weigeren en uitschakelen. Controle nummers wanneer een Azure front-deur service of Application Gateway geen WAF heeft en gebruikers kunnen zien welke Azure front-deur service of Application Gateway momenteel niet aan de eisen voldoet. Weigeren voor komt dat een Azure front-deur service of Application Gateway worden gemaakt als er geen WAF is gekoppeld. Als u dit beleid uitschakelt, wordt deze instelling uitgeschakeld.

2. **Web Application firewall moet een ingestelde modus zijn voor Application Gateway en de Azure front-deur service**: Web Application firewall wordt geëvalueerd op de modus waarin deze zich bevindt of die wordt gedetecteerd. Het beleid garandeert consistentie van de modus tussen Web Application firewalls. Het beleid heeft drie effecten: controleren, weigeren en uitschakelen. Controle sporen wanneer een WAF niet voldoet aan de opgegeven modus. Als deze optie niet is ingesteld op de juiste modus, voor komt u dat een WAF wordt gemaakt. Als u dit beleid uitschakelt, wordt deze instelling uitgeschakeld.


## <a name="launch-an-azure-policy"></a>Een Azure Policy starten


1.  Op de start pagina van Azure typt u beleid in de zoek balk en klikt u op het pictogram Azure Policy

2.  Selecteer in de Azure Policy-service onder **ontwerpen**de optie **toewijzingen**.

![Azure Web Application Firewall](../media/waf-azure-policy/policy-home.png)

3.  Selecteer op de pagina Toewijzingen het pictogram **beleid** aan de bovenkant.

![Azure Web Application Firewall](../media/waf-azure-policy/assign-policy.png)

4.  Werk op het tabblad basis beginselen van beleid toewijzen de volgende velden bij:
    1.  **Bereik**: Selecteer wat Azure-abonnementen en-resource groepen moeten worden beïnvloed door de Azure Policy.
    2.  **Uitsluitingen**: Selecteer alle resources uit het bereik die u wilt uitsluiten van het beleid 
    3.  **Beleids definitie**: selecteer de Azure Policy die u wilt Toep assen op het bereik met uitsluitingen. Typ ' Web Application Firewall ' in de zoek balk om de relevante Web Application firewall-Azure Policy te kiezen.

![Azure Web Application Firewall](../media/waf-azure-policy/policy-listings.png)


5.  Selecteer het tabblad **para meters** en werk de beleids parameters bij. Als u verder wilt verduidelijken wat de para meter doet, houdt u de muis aanwijzer boven het info pictogram naast de parameter naam voor verdere uitleg.

6.  Selecteer **beoordeling + maken** om uw Azure-beleid te volt ooien. Het Azure-beleid neemt ongeveer 15 minuten in beslag, totdat het actief is voor nieuwe resources.
