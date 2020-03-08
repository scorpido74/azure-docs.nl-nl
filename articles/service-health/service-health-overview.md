---
title: Overzicht van Service Health | Microsoft Docs
description: Persoonlijke informatie over hoe uw Azure-apps worden beïnvloed door de huidige en toekomstige problemen met de Azure-service en het onderhoud.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: a1c6a10ab48125c030c9a35864f829438404ad97
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898441"
---
# <a name="service-health-overview"></a>Overzicht van Service Health

Service Health biedt u een aanpasbaar dash board waarmee u de status van uw Azure-Services kunt bijhouden in de regio's waar u ze gebruikt. In dit dash board kunt u actieve gebeurtenissen volgen, zoals lopende Service problemen, gepland gepland onderhoud of relevante status adviezen. Wanneer gebeurtenissen inactief worden, worden deze tot 90 dagen in uw status geschiedenis geplaatst. Ten slotte kunt u het Service Health-dash board gebruiken om service status waarschuwingen te maken en te beheren die u proactief waarschuwen wanneer Service problemen van invloed zijn.

## <a name="service-health-events"></a>Service Health gebeurtenissen

Service Health houdt vier typen status gebeurtenissen bij die van invloed kunnen zijn op uw resources:

1. **Service problemen** : problemen in de Azure-Services die nu van invloed zijn op u. 
2. **Gepland onderhoud** : aanstaande onderhouds werkzaamheden die in de toekomst van invloed kunnen zijn op de beschik baarheid van uw services.  
3. **Status adviseur** -wijzigingen in Azure-Services die uw aandacht vereisen. Voor beelden zijn wanneer Azure-functies zijn afgeschaft of als u een gebruiks quotum overschrijdt.
4. **Beveiligings adviezen (preview)** : beveiligings waarschuwingen die van invloed kunnen zijn op de beschik baarheid van uw Azure-Services.

> [!NOTE]
> Voor het weer geven van Service Health gebeurtenissen moeten gebruikers [de rol van lezer krijgen](../role-based-access-control/role-assignments-portal.md) voor een abonnement.

## <a name="get-started-with-service-health"></a>Aan de slag met Service Health

Als u het dash board van Service Health wilt starten, selecteert u de tegel Service Health in het dash board van de portal. Als u de tegel eerder hebt verwijderd of als u aangepast dash board gebruikt, zoekt u naar Service Health-Service in "meer services" (linksonder op uw dash board).

![Aan de slag met Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Bekijk de huidige problemen die van invloed zijn op uw services

In de weer gave **Service problemen** ziet u lopende problemen in Azure-Services die van invloed zijn op uw resources. U kunt begrijpen wanneer het probleem is begonnen en welke services en regio's worden beïnvloed. U kunt ook de meest recente update lezen om te begrijpen wat Azure doet om het probleem op te lossen. 

![Service probleem beheren](./media/service-health-overview/azure-service-health-overview-2.png)

Kies het tabblad **mogelijke impact** om de specifieke lijst met resources te bekijken waarvan u de eigenaar bent die mogelijk van invloed is op het probleem. U kunt een CSV-lijst van deze resources downloaden om te delen met uw team.

![Service probleem beheren-impact](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Koppelingen en download bare uitleg ophalen 

U kunt een koppeling voor het probleem verkrijgen om te gebruiken in uw probleem beheersysteem. U kunt een PDF-bestand en soms CSV-bestanden downloaden om te delen met personen die geen toegang hebben tot de Azure Portal.   

![Service probleem beheren-probleem beheer](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Ontvang ondersteuning van micro soft

Neem contact op met de ondersteuning als uw resource in een slechte staat blijft, zelfs nadat het probleem is opgelost.  Gebruik de ondersteunings koppelingen aan de rechter kant van de pagina.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Een persoonlijke status toewijzing vastmaken aan uw dash board

Filter Service Health om uw bedrijfs kritieke abonnementen, regio's en resource typen weer te geven. Sla het filter op en maak een persoonlijke status in de praktijk aan uw portal-dash board. 

![Persoonlijke status toewijzing filteren](./media/service-health-overview/azure-service-health-overview-6a.png)

![Een persoonlijke status kaart vastmaken](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Service status waarschuwingen configureren

Service Health integreert met Azure Monitor om u te waarschuwen via e-mails, SMS-berichten en webhook-meldingen wanneer uw bedrijfs kritieke resources worden beïnvloed. Stel een waarschuwing voor een activiteiten logboek in voor de juiste service status gebeurtenis. Hiermee stuurt u een waarschuwing naar de juiste personen in uw organisatie met behulp van actie groepen. Zie [Configure alerts for service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md) voor meer informatie.

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Volgende stappen

Stel waarschuwingen in zodat u op de hoogte wordt gesteld van status problemen. Zie [Aanbevolen procedures voor het instellen van Azure service Health-waarschuwingen](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s)voor meer informatie. 
