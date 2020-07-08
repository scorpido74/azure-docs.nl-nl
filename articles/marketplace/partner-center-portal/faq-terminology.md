---
title: Veelgestelde vragen over commerciële Marketplace Analytics & terminologie-partner centrum
description: Krijg antwoorden op veelgestelde vragen over de analyse van commerciële Marketplace in Partner Center. Dit artikel bevat een data dictionary voor analyse terminologie.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: eb91e498fa757c5cec7bd466c60aaf7e8758304c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83744564"
---
# <a name="commercial-marketplace-analytics-terminology-and-common-questions"></a>Commercial Marketplace Analytics terminologie en veelgestelde vragen

Dit artikel heeft betrekking op veelgestelde vragen over analyse berichten in het partner centrum en biedt ook een woorden lijst met analyse terminologie.

## <a name="common-questions"></a>Veelgestelde vragen

**Ik kan mijn Analytics-gegevens niet weer geven in het partner centrum. Wanneer ik deze pagina's open, zie ik het onderstaande bericht. Waarom is dit?**

![Er zijn nog geen gegevens voor uw aanbiedingen](./media/analytics-faq-no-data.png)

Waarom dit bericht wordt opgehaald:

- Er zijn momenteel geen acquisities voor uw gepubliceerde aanbiedingen op Marketplace. Dit kan betekenen dat uw aanbiedingen zich in Marketplace bevinden en de weer gaven van klanten kunnen verkrijgen op de product weergave pagina's, maar klanten hebben nog geen actie ondernomen om ze te kopen en te implementeren.
- Het publiceren van uw aanbieding is mogelijk in behandeling en is nog niet actief. Alleen live-aanbiedingen kunnen door klanten worden aangeschaft. Zie overzicht in het [dash board analyseren](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)om de status van uw aanbiedingen te controleren. Zie [Summary dash board in Commercial Marketplace Analytics](./summary-dashboard.md)(Engelstalig) voor meer informatie.
- Uw aanbiedingen kunnen worden vermeld als **contact persoon**, die alleen lijst aanbiedingen zijn en die niet door klanten op Marketplace kunnen worden aangeschaft. Hoewel deze aanbiedingen leads genereren en met u worden gedeeld, worden er geen orders gemaakt voor deze aanbiedingen omdat ze niet kunnen worden gekocht. Als u uw aanbiedings type wilt controleren, gaat u naar de pagina Setup.

**Ik weet dat ik Analytics-gegevens heb, maar het onderstaande bericht wordt weer gegeven:**

![Er zijn geen gegevens voor het opgegeven datum bereik](./media/analytics-faq-data-range.png)

Als u dit bericht ontvangt, betekent dit dat u Analytics-gegevens hebt, maar er zijn geen gegevens voor het datum bereik dat u hebt geselecteerd. Selecteer een ander datum bereik of een aangepast datum bereik om alle gegevens sinds 2010 weer te geven. Voor meer informatie, zie de sectie datum bereik van het [dash board samen vatting van de commerciële Marketplace-analyse](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Woorden lijst met gegevens termen

| Kenmerknaam | Rapporten | Definitie|
|---|---|---|
| Azure-licentie type | Klant, order | Het type licentie overeenkomst dat door klanten wordt gebruikt om Azure aan te schaffen. Ook wel bekend als Channel |
| Azure-licentie type: Cloud Solution Provider | Klant, order | De eind klant schaft Azure en uw Marketplace-aanbieding aan via hun Cloud solution provider, die fungeert als uw wederverkoper.|
| Azure-licentie type: Enter prise | Klant, order | De eind klant schaft Azure en uw Marketplace-aanbieding aan via een Enterprise Agreement, die rechtstreeks is ondertekend met micro soft.|
| Azure-licentie type: Enter prise through reseller  | Klant, order | De eind klant schaft Azure en uw Marketplace-aanbieding aan via een wederverkoper die hun Enterprise Agreement met micro soft vereenvoudigt.|  |
| Azure-licentie type: betalen naar gebruik| Klant, order | De eind klant aanschaft Azure en uw Marketplace-aanbieding via een overeenkomst voor betalen naar gebruik, die rechtstreeks is ondertekend met micro soft.||
| Naam van Cloud exemplaar| Bestellen| De Microsoft Cloud waarin een VM-implementatie is opgetreden.||
| Naam van Cloud exemplaar: Azure Global| Bestellen| De open bare wereld wijde micro soft-Cloud.|| |
| Naam van Cloud exemplaar: Azure Government | Bestellen| Government-specifieke micro soft-Clouds voor een van de volgende overheden: China, Duitsland of de Verenigde Staten van Amerika.| |
| Klant plaats| Klant| De naam van de plaats van de klant. De stad kan afwijken van de plaats in het Azure-abonnement van een klant.||
| Taal van klant communicatie  | Klant| De taal die door de klant wordt aanbevolen voor communicatie.||
| Bedrijfs naam van klant | Klant, order | De bedrijfs naam die door de klant is verschaft. De naam kan afwijken van de plaats in het Azure-abonnement van een klant.|  |
| Land/regio van klant | Klant, order | De land/regio naam die door de klant wordt verschaft. Land/regio kan afwijken van het land/de regio in het Azure-abonnement van een klant.|  |
| E-mail van klant| Klant| Het e-mail adres van de eind gebruiker. E-mail kan afwijken van het e-mail adres in het Azure-abonnement van een klant.||
| Voor naam van klant| Klant| De naam van de klant. De naam kan afwijken van de naam die is opgenomen in het Azure-abonnement van de klant.| |
| Klant-id | Klant, order | De unieke id die aan een klant is toegewezen. Een klant heeft mogelijk geen of meer Azure Marketplace-abonnementen.|  |
| Post code van de klant  | Klant| De post code die de klant heeft ontvangen. De code kan afwijken van de post code die is opgenomen in het Azure-abonnement van een klant.| |
| Klant status| Klant| De status (adres) van de klant. De status kan afwijken van de status die is opgenomen in het Azure-abonnement van de klant.| |
| Aanschaf datum| Klant| De eerste datum waarop de klant een door u gepubliceerde aanbieding heeft gekocht.| |
| Datum verloren| Klant| De laatste datum waarop de klant de laatste van alle eerder gekochte aanbiedingen heeft geannuleerd.||
| Is nieuwe klant  | Bestellen| Met deze waarde wordt een nieuwe klant geïdentificeerd die voor de eerste keer een of meer van uw aanbiedingen aanschaft (of niet). De waarde is ' ja ' in dezelfde kalender maand voor ' verworven datum '. De waarde is "nee" als de klant een van de aanbiedingen vóór de gemelde kalender maand heeft gekocht. |
| Is preview-SKU| Bestellen| De waarde laat u weten of u de SKU hebt gelabeld als ' preview '. De waarde is "ja" als de SKU dienovereenkomstig is gelabeld en alleen Azure-abonnementen die door u zijn geautoriseerd, kunnen deze installatie kopie implementeren en gebruiken. De waarde is ' nee ' als de SKU niet is geïdentificeerd als ' preview '.  |
| Is de contact persoon voor de reclame in| Klant| De waarde laat u weten of de klant proactief heeft gekozen voor een contact persoon van uitgevers. Op dit moment bieden we geen optie voor klanten, dus hebben we "no" aangegeven op het bord. Zodra deze functie is geïmplementeerd, gaan we dienovereenkomstig met bijwerken.|
| Marketplace-licentie type| Bestellen| De facturerings methode van de Marketplace-aanbieding.||
| Marketplace-licentie type: gefactureerd via Azure| Bestellen| Micro soft is uw agent voor deze Marketplace-aanbieding en factuurt klanten namens u. (PAYG credit card of ENTER prise-factuur)||
| Marketplace-licentie type: Neem uw eigen licentie mee | Bestellen| Voor de virtuele machine is een licentie sleutel van de klant vereist om te implementeren. Micro soft factureert klanten niet op deze manier met hun aanbiedingen via Marketplace.||
| Marketplace-licentie type: gratis| Bestellen| De aanbieding is zo geconfigureerd dat deze gratis is voor alle gebruikers. Micro soft houdt geen klanten in rekening voor het gebruik van deze aanbieding.||
| Marketplace-licentie type: micro soft als wederverkoper  | Bestellen| Micro soft is uw wederverkoper voor deze Marketplace-aanbieding.|  |
| Marketplace-abonnements-ID | Klant, order | De unieke id die is gekoppeld aan het Azure-abonnement dat de klant heeft gebruikt voor het kopen van uw Marketplace-aanbieding. De ID was voorheen de GUID van het Azure-abonnement.||
| Naam van aanbieding  | Bestellen| De naam van de Marketplace-aanbieding.|| |
| Aanbiedings type  | Bestellen| Het type Microsoft Marketplace aanbieding.|||
| Aanbiedings type: beheerde toepassing  | Ter | Gebruik het aanbiedings type Azure app: beheerde app wanneer de volgende voor waarden zijn vereist: u implementeert een op abonnementen gebaseerde oplossing voor uw klant met behulp van een virtuele machine of een volledige oplossing op basis van IaaS. U of uw klant vereist dat de oplossing wordt beheerd door een partner. |
| Aanbiedings type: Azure-toepassing| Ter | Gebruik het Azure-toepassing aanbieding van de oplossings sjabloon wanneer voor uw oplossing extra implementatie-en configuratie automatisering nodig is dan een eenvoudige virtuele machine.||
| Aanbiedings type: Consulting Service| Bestellen| Advies Services in azure Marketplace helpen klanten met services te verbinden en hun gebruik van Azure te ondersteunen en uit te breiden.| |
| Aanbiedings type: container | Bestellen| Gebruik het type container aanbod wanneer uw oplossing een docker-container installatie kopie is ingericht als een op Kubernetes gebaseerde Azure-container service.||
| Aanbiedings type: Dynamics 365 Business Central| Bestellen| Gebruik dit aanbiedings type wanneer uw oplossing is geïntegreerd met Dynamics 365 voor financiering en bewerkingen| |
| Aanbiedings type: Dynamics 365 voor klant betrokkenheid | Bestellen| Gebruik dit aanbiedings type wanneer uw oplossing is geïntegreerd met Dynamics 365 voor klant betrokkenheid.||
| Aanbiedings type: IoT Edge-module | Bestellen| Azure IoT Edge modules zijn de kleinste reken eenheden die worden beheerd door IoT Edge en die micro soft-Services (zoals Azure Stream Analytics), services van derden of uw eigen oplossing-specifieke code kunnen bevatten. |
| Aanbiedings type: Power BI-toepassing | Bestellen| Gebruik het Power BI type toepassings aanbieding wanneer u een toepassing implementeert die is geïntegreerd met Power BI.|  |
| Aanbiedings type: SaaS-toepassing| Bestellen| Gebruik het type SaaS-app-aanbieding om uw klant in staat te stellen uw SaaS-gebaseerde technische oplossing als een abonnement te kopen.||
| Aanbiedings type: virtuele machine | Bestellen| Gebruik het type virtuele machine-aanbieding wanneer u een virtueel apparaat implementeert op het abonnement dat is gekoppeld aan uw klant.||
| Aanbiedings type: Visual Studio Marketplace-extensie  | Bestellen| Het aanbiedings type is eerder beschikbaar voor ontwikkel aars van Azure DevOps extension. Ontwikkel aars van Azure DevOps extension kunnen hun verlenging rechtstreeks aan klanten verkopen. Extensie aanbiedingen kunnen worden geconfigureerd als betaald of met inbegrip van een proef versie. |
| Annulerings datum van bestelling| Bestellen| De datum waarop de Marketplace-bestelling is geannuleerd.||
| Order-id| Bestellen| De unieke id van de klant bestelling voor uw Marketplace-service. Aanbiedingen op basis van het gebruik van virtuele machines zijn niet gekoppeld aan een order.| |
| Aankoop datum van bestelling| Bestellen| De datum waarop de Marketplace-bestelling is gemaakt.|||
| Orderstatus| Bestellen| De status van een Marketplace-bestelling op het moment dat de gegevens voor het laatst zijn vernieuwd.|     |
| Status van bestelling: actief  | Bestellen| De klant heeft een bestelling gekocht en heeft zijn order niet geannuleerd.|         |
| Status van bestelling: geannuleerd | Bestellen| De klant heeft eerder een order aangeschaft en vervolgens de bestelling geannuleerd.||
| E-mail van provider| Klant| Het e-mail adres van de provider die deel uitmaakt van de relatie tussen micro soft en de eind klant. Als de klant een onderneming is via wederverkoper, is dit de wederverkoper. Als er een Cloud Solution Provider (CSP) betrokken is, wordt dit de CSP.|
| Provider naam| Klant| De naam van de provider die deel uitmaakt van de relatie tussen micro soft en de eind klant. Als de klant een onderneming is via wederverkoper, is dit de wederverkoper. Als er een Cloud Solution Provider (CSP) betrokken is, wordt dit de CSP.|
| SKU| Bestellen| SKU-naam zoals gedefinieerd tijdens het publiceren. Een aanbieding kan veel Sku's hebben, maar een SKU kan slechts worden gekoppeld aan één aanbieding.||
| Eind datum van proef versie| Bestellen| De datum waarop de proef periode voor deze order wordt beëindigd of beëindigd.||

## <a name="next-steps"></a>Volgende stappen

- Voor een overzicht van analyse rapporten die beschikbaar zijn in de commerciële Marketplace van partner Center raadpleegt u [Analytics voor de commerciële Marketplace in Partner Center](./analytics.md).
- Zie [Summary dash board in Commercial Marketplace Analytics](./summary-dashboard.md)voor grafieken, trends en waarden van statistische gegevens die de Marketplace-activiteiten voor uw aanbieding samenvatten.
- Voor informatie over uw orders in een grafische en download bare indeling raadpleegt u het [dash board orders in Commercial Marketplace Analytics](./orders-dashboard.md).
- Voor virtuele machine (VM) zijn metrische gegevens over gebruik en gefactureerde facturering, Zie [gebruiks dashboard in commerciële Marketplace-analyses](./usage-dashboard.md).
- Zie [klanten dashboard in Commercial Marketplace Analytics](./customer-dashboard.md)voor gedetailleerde informatie over uw klanten, waaronder groei trends.
- Zie [dash board downloaden in Commercial Marketplace Analytics](./downloads-dashboard.md)voor een lijst met Download aanvragen voor de afgelopen 30 dagen.
- Voor een geconsolideerde weer gave van feedback van klanten voor aanbiedingen op Azure Marketplace en AppSource raadpleegt u het [dash board beoordelingen en beoordelingen in Commercial Marketplace Analytics](./ratings-reviews.md).
