---
title: Overzicht van de servicestatus | Microsoft Documenten
description: Gepersonaliseerde informatie over hoe uw Azure-apps worden beïnvloed door huidige en toekomstige Azure-serviceproblemen en -onderhoud.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: a1c6a10ab48125c030c9a35864f829438404ad97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898441"
---
# <a name="service-health-overview"></a>Overzicht van servicestatus

Servicestatus biedt u een aanpasbaar dashboard dat de status van uw Azure-services bijhoudt in de regio's waar u ze gebruikt. In dit dashboard u actieve gebeurtenissen bijhouden, zoals doorlopende serviceproblemen, gepland onderhoud of relevante gezondheidsadviezen. Wanneer gebeurtenissen inactief worden, worden ze maximaal 90 dagen in uw gezondheidsgeschiedenis geplaatst. Ten slotte u het dashboard Servicestatus gebruiken om servicestatuswaarschuwingen te maken en te beheren die u proactief op de hoogte stellen wanneer serviceproblemen u beïnvloeden.

## <a name="service-health-events"></a>Service status gebeurtenissen

Service health houdt vier soorten gezondheidsgebeurtenissen bij die van invloed kunnen zijn op uw resources:

1. **Serviceproblemen** - Problemen in de Azure-services die u op dit moment raken. 
2. **Gepland onderhoud** - Toekomstig onderhoud dat van invloed kan zijn op de beschikbaarheid van uw services in de toekomst.  
3. **Gezondheidsadviezen** - Wijzigingen in Azure-services die uw aandacht vereisen. Voorbeelden hiervan zijn wanneer Azure-functies worden afgeschaft of als u een gebruiksquotum overschrijdt.
4. **Beveiligingsadviezen (preview)** - Beveiligingsgerelateerde meldingen die van invloed kunnen zijn op de beschikbaarheid van uw Azure-services.

> [!NOTE]
> Als u servicestatusgebeurtenissen wilt bekijken, moeten gebruikers [de leesrol](../role-based-access-control/role-assignments-portal.md) bij een abonnement krijgen.

## <a name="get-started-with-service-health"></a>Aan de slag met Service Health

Als u het dashboard Servicestatus wilt starten, selecteert u de tegel Servicestatus op het portaldashboard. If you have previously removed the tile or you're using custom dashboard, search for Service Health service in "More services" (bottom left on your dashboard).

![Aan de slag met Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Bekijk actuele problemen die van invloed zijn op uw services

In de weergave **Serviceproblemen** worden alle aanhoudende problemen in Azure-services weergegeven die van invloed zijn op uw resources. U begrijpen wanneer het probleem is begonnen en welke services en regio's worden beïnvloed. U ook de meest recente update lezen om te begrijpen wat Azure doet om het probleem op te lossen. 

![Serviceprobleem beheren](./media/service-health-overview/azure-service-health-overview-2.png)

Kies het tabblad **Potentiële impact** om de specifieke lijst met resources te bekijken waarvan u eigenaar bent en die mogelijk door het probleem worden beïnvloed. U een CSV-lijst met deze bronnen downloaden om met uw team te delen.

![Serviceprobleem beheren - Impact](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Links en downloadbare uitleg downloaden 

U een koppeling voor het probleem te gebruiken in uw probleem management systeem. U PDF- en soms CSV-bestanden downloaden om te delen met mensen die geen toegang hebben tot de Azure-portal.   

![Serviceprobleem beheren - Probleembeheer](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Ondersteuning krijgen van Microsoft

Neem contact op met de ondersteuning als uw resource in een slechte staat is achtergelaten, zelfs nadat het probleem is opgelost.  Gebruik de ondersteuningskoppelingen rechts van de pagina.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Een gepersonaliseerde gezondheidskaart vastmaken aan uw dashboard

Filter servicestatus om uw bedrijfskritieke abonnementen, regio's en resourcetypen weer te geven. Sla het filter op en maak een aangepaste wereldkaart met statusmeldingen vast aan uw portaldashboard. 

![Gepersonaliseerde gezondheidskaart filteren](./media/service-health-overview/azure-service-health-overview-6a.png)

![Een gepersonaliseerde gezondheidskaart vastmaken](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Waarschuwingen voor servicestatus configureren

Servicestatus integreert met Azure Monitor om u te waarschuwen via e-mails, sms-berichten en webhookmeldingen wanneer uw bedrijfskritieke bronnen worden beïnvloed. Stel een waarschuwing voor het activiteitenlogboek in voor de juiste servicestatusgebeurtenis. Route er die waarschuwing naar de juiste mensen in uw organisatie met behulp van actiegroepen. Zie [Waarschuwingen configureren voor servicestatus voor](../azure-monitor/platform/alerts-activity-log-service-notifications.md) meer informatie

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Volgende stappen

Stel waarschuwingen in zodat u op de hoogte wordt gesteld van gezondheidsproblemen. Zie [Aanbevolen procedures voor het instellen van Azure Service Health Alerts](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s)voor meer informatie. 
