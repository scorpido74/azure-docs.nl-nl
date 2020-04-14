---
title: Veelgestelde vragen en terminologie voor de commerciële marktplaatsanalyse in Partner Center
description: Meer informatie over het beantwoorden van veelgestelde vragen over commerciële marktplaatsanalyses. Bevat een gegevenswoordenboek voor analyseterminologie.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: b7367e58de818c20723c02a6763b1bf1e3b18f24
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81251823"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>Veelgestelde vragen en terminologie voor commerciële marktplaatsanalyse

In dit artikel worden veelgestelde vragen over analyseberichten in partnercentrum beantwoord en wordt ook een woordenboek met terminologie voor analyses weergegeven.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

In deze sectie vindt u antwoorden op veelgestelde vragen over nog **geen** beschikbare berichten in het Partnercentrum.

**Ik kan mijn analysegegevens niet bekijken in partnercentrum. Als ik deze pagina's bekijk, zie ik het bericht hieronder. Waarom is dit?**

![Nog geen gegevens voor uw aanbiedingen](./media/analytics-faq-no-data.png)

Waarom u dit bericht krijgt:

- Er bestaan momenteel geen acquisities voor uw gepubliceerde aanbiedingen in marketplace. Dit kan betekenen dat uw aanbiedingen live zijn in de markt en het verkrijgen van weergaven van klanten in de productdisplaypagina's, maar klanten hebben nog geen actie ondernomen om ze te kopen en te implementeren.
- Het publiceren van uw aanbieding is mogelijk in behandeling en is nog niet live. Alleen live aanbiedingen kunnen worden overgenomen door klanten. Zie Overzicht in het [dashboard Analyseren](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)om de status van uw aanbiedingen te controleren. Zie [Overzichtsdashboard in commerciële marktplaatsanalyses](./summary-dashboard.md)voor meer informatie.
- Uw aanbiedingen kunnen worden vermeld als **Contact mij,** die zijn alleen op de lijst aanbiedingen en kan niet worden gekocht door klanten in de markt. Hoewel deze aanbiedingen leads genereren en met u worden gedeeld, worden bestellingen niet gemaakt voor deze aanbiedingen omdat ze niet kunnen worden gekocht. Als u het type aanbiedingsvermelding wilt controleren, gaat u naar de installatiepagina.

**Ik weet dat ik analysegegevens heb, maar het onderstaande bericht verschijnt:**

![Geen gegevens voor een bepaald datumbereik](./media/analytics-faq-data-range.png)

Als u dit bericht ontvangt, betekent dit dat u analysegegevens hebt, maar dat er geen gegevens zijn voor het datumbereik dat u hebt geselecteerd. Selecteer een ander datumbereik of aangepast datumbereik om gegevens sinds 2010 weer te geven. Zie voor meer informatie het gedeelte Datumbereik van [Overzichtsdashboard in commerciële marktplaatsanalyses](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Woordenboek van gegevenstermen

| Kenmerknaam | Rapporten | Definitie|
|---|---|---|
| Azure-licentietype | Klant, Bestelling | Het type licentieovereenkomst dat door klanten wordt gebruikt om Azure te kopen. Ook bekend als Channel |
| Azure-licentietype: cloudoplossingsprovider | Klant, Bestelling | De eindklant koopt Azure en uw Marketplace-aanbod via hun Cloud Solution Provider, die optreedt als uw reseller.|
| Azure-licentietype: onderneming | Klant, Bestelling | De eindklant koopt Azure en uw Marketplace-aanbieding via een Enterprise Agreement, die rechtstreeks met Microsoft is ondertekend.|
| Azure-licentietype: onderneming via reseller  | Klant, Bestelling | De eindklant koopt Azure en uw Marketplace-aanbieding via een reseller die zijn Ondernemingsovereenkomst met Microsoft faciliteert.|  |
| Azure-licentietype: betalen als u onderweg bent| Klant, Bestelling | De eindklant koopt Azure en uw Marketplace-aanbieding via een 'Pay as You Go'-overeenkomst, die rechtstreeks met Microsoft is ondertekend.||
| Naam van cloud-instantie| Bestellen| De Microsoft Cloud waarin een VM-implementatie is opgetreden.||
| Naam van cloudinstantie: Azure Global| Bestellen| De openbare wereldwijde Microsoft-cloud.|| |
| Naam van cloudinstantie: Azure-overheid | Bestellen| Overheidsspecifieke Microsoft-clouds voor een van de volgende regeringen: China, Duitsland of De Verenigde Staten van Amerika.| |
| Klantstad| Klant| De naam van de stad die door de klant wordt verstrekt. De stad kan anders zijn dan de stad in het Azure-abonnement van een klant.||
| Taal voor klantcommunicatie  | Klant| De taal die de klant voor communicatie prefereerde.||
| Bedrijfsnaam van de klant | Klant, Bestelling | De bedrijfsnaam die door de klant wordt verstrekt. De naam kan anders zijn dan de plaats in het Azure-abonnement van een klant.|  |
| Land van klant | Klant, Bestelling | De landnaam die door de klant wordt opgegeven. Land kan anders zijn dan het land in het Azure-abonnement van een klant.|  |
| E-mail van de klant| Klant| Het e-mailadres van de eindklant. E-mail kan anders zijn dan het e-mailadres in het Azure-abonnement van een klant.||
| Voornaam klant| Klant| De naam die door de klant wordt verstrekt. De naam kan anders zijn dan de naam die wordt opgegeven in het Azure-abonnement van een klant.| |
| Klant-id | Klant, Bestelling | De unieke id die aan een klant is toegewezen. Een klant heeft mogelijk nul of meer Azure Marketplace-abonnementen.|  |
| Postcode van de klant  | Klant| De postcode van de klant. Code kan anders zijn dan de postcode die in het Azure-abonnement van een klant wordt opgegeven.| |
| Klantstatus| Klant| De staat (adres) verstrekt door de klant. De status kan anders zijn dan de status die wordt opgegeven in het Azure-abonnement van een klant.| |
| Verkregen datum| Klant| De eerste datum waarop de klant een door u gepubliceerd aanbod heeft gekocht.| |
| Datum verloren| Klant| De laatste datum waarop de klant de laatste van alle eerder gekochte aanbiedingen heeft geannuleerd.||
| Is nieuwe klant  | Bestellen| De waarde identificeert een nieuwe klant die voor het eerst een of meer van uw aanbiedingen verkrijgt (of niet). Waarde is "Ja" als binnen dezelfde kalendermaand voor "Verkregen datum". De waarde is 'Nee' als de klant een van uw aanbiedingen heeft gekocht voordat de gerapporteerde kalendermaand wordt gerapporteerd. |
| Is Preview SKU| Bestellen| De waarde laat je weten als je de SKU hebt getagd als 'voorbeeld'. Waarde is 'Ja' als de SKU dienovereenkomstig is getagd en alleen Azure-abonnementen die door u zijn geautoriseerd, deze afbeelding kunnen implementeren en gebruiken. Waarde is 'Nee' als de SKU niet is geïdentificeerd als "voorbeeld".  |
| Is Promotionele contact Opt-in| Klant| De waarde laat u weten of de klant proactief heeft gekozen voor promotioneel contact van uitgevers. Op dit moment presenteren we de optie niet aan klanten, dus hebben we over de hele linie "Nee" aangegeven. Zodra deze functie is geïmplementeerd, zullen we beginnen met het bijwerken dienovereenkomstig.|
| Marketplace-licentietype| Bestellen| De factureringsmethode van de Marketplace-aanbieding.||
| Marketplace-licentietype: gefactureerd via Azure| Bestellen| Microsoft is uw agent voor deze Marketplace-aanbieding en factureert klanten namens u. (PAYG-creditcard of ondernemingsfactuur)||
| Marketplace-licentietype: breng uw eigen licentie mee | Bestellen| De VM vereist een licentiesleutel die door de klant wordt geleverd om te implementeren. Microsoft factureert geen klanten voor het aanbieden van hun aanbiedingen op deze manier via de marktplaats.||
| Marketplace-licentietype: gratis| Bestellen| De aanbieding is geconfigureerd om gratis te zijn voor alle gebruikers. Microsoft factureert geen klanten voor hun gebruik van deze aanbieding.||
| Marketplace-licentietype: Microsoft als reseller  | Bestellen| Microsoft is uw reseller voor deze Marketplace-aanbieding.|  |
| Marketplace-abonnements-id | Klant, Bestelling | De unieke id die is gekoppeld aan het Azure-abonnement dat de klant heeft gebruikt om uw Marketplace-aanbieding te kopen. ID was voorheen de Azure Subscription GUID.||
| Naam van aanbieding  | Bestellen| De naam van het Marketplace-aanbod.|| |
| Aanbiedingstype  | Bestellen| Het type Microsoft Marketplace-aanbod.|||
| Aanbiedingstype: beheerde toepassing  | Volgorde | Gebruik de Azure-app: beheerde app-aanbiedingstype wanneer de volgende voorwaarden vereist zijn: u implementeert een oplossing op basis van een abonnement voor uw klant met behulp van een VM of een volledige IaaS-oplossing. U of uw klant eist dat de oplossing wordt beheerd door een partner. |
| Aanbiedingstype: Azure-toepassing| Volgorde | Gebruik het aanbiedingstype azure-toepassingssjabloon wanneer uw oplossing extra implementatie- en configuratieautomatisering vereist dan een eenvoudige vm.||
| Aanbiedingstype: Consulting Service| Bestellen| Consulting Services in Azure Marketplace helpen om klanten in contact te brengen met services om hun gebruik van Azure te ondersteunen en uit te breiden.| |
| Aanbiedingstype: container | Bestellen| Gebruik het type containeraanbieding wanneer uw oplossing een Docker-containerafbeelding is die is ingericht als een Op Kubernetes gebaseerde Azure-containerservice.||
| Aanbiedingstype: Dynamics 365 Business Central| Bestellen| Gebruik dit aanbiedingstype wanneer uw oplossing is geïntegreerd met Dynamics 365 voor Financiën en Bedrijfsvoering| |
| Aanbiedingstype: Dynamics 365 voor klantbetrokkenheid | Bestellen| Gebruik dit aanbiedingstype wanneer uw oplossing is geïntegreerd met Dynamics 365 voor klantbetrokkenheid.||
| Aanbiedingstype: IoT Edge-module | Bestellen| Azure IoT Edge-modules zijn de kleinste rekeneenheden die worden beheerd door IoT Edge en kunnen Microsoft-services bevatten (zoals Azure Stream Analytics), services van derden of uw eigen oplossingsspecifieke code. |
| Aanbiedingstype: Power BI-toepassing | Bestellen| Gebruik het aanbiedingstype Power BI-toepassing wanneer u een toepassing implementeert die is geïntegreerd met Power BI.|  |
| Aanbiedingstype: SaaS-toepassing| Bestellen| Gebruik het aanbiedingstype van de SaaS-app om uw klant in staat te stellen uw saas-gebaseerde, technische oplossing als abonnement te kopen.||
| Aanbiedingstype: virtuele machine | Bestellen| Gebruik het type virtuele machineaanbieding wanneer u een virtueel toestel implementeert op het abonnement dat aan uw klant is gekoppeld.||
| Aanbiedingstype: Visual Studio Marketplace-extensie  | Bestellen| Aanbiedingstype dat voorheen beschikbaar was voor azure DevOps-extensieontwikkelaars. In de toekomst kunnen azure DevOps-extensieontwikkelaars hun extensie rechtstreeks aan klanten verkopen. Uitbreidingsaanbiedingen kunnen worden geconfigureerd als betaald of inclusief een proefversie. |
| Datum annuleren bestellen| Bestellen| De datum waarop de Marketplace-bestelling is geannuleerd.||
| Order-id| Bestellen| De unieke id van de klantorder voor uw Marketplace-service. Aanbiedingen op basis van virtuele machines zijn niet gekoppeld aan een bestelling.| |
| Aankoopdatum voor bestelling| Bestellen| De datum waarop de Marketplace-order is gemaakt.|||
| Orderstatus| Bestellen| De status van een Marketplace-bestelling op het moment dat de gegevens voor het laatst zijn vernieuwd.|     |
| Bestelstatus: actief  | Bestellen| De klant heeft een bestelling gekocht en heeft zijn bestelling niet geannuleerd.|         |
| Bestelstatus: geannuleerd | Bestellen| De klant heeft eerder een bestelling gekocht en vervolgens zijn bestelling geannuleerd.||
| Providere-mail| Klant| Het e-mailadres van de provider die betrokken is bij de relatie tussen Microsoft en de eindklant. Als de klant een Enterprise via Reseller is, is dit de reseller. Als het om een Cloud Solution Provider (CSP) gaat, is dit de CSP.|
| Naam van provider| Klant| De naam van de provider die betrokken is bij de relatie tussen Microsoft en de eindklant. Als de klant een Enterprise via Reseller is, is dit de reseller. Als het om een Cloud Solution Provider (CSP) gaat, is dit de CSP.|
| SKU| Bestellen| SKU-naam zoals gedefinieerd tijdens het publiceren. Een aanbieding kan veel SKU's hebben, maar een SKU kan slechts aan één aanbieding worden gekoppeld.||
| Einddatum proef| Bestellen| De datum waarop de proefperiode voor deze bestelling wordt beëindigd of is beëindigd.||

## <a name="next-steps"></a>Volgende stappen

- Zie [Analytics voor de commerciële marktplaats voor](./analytics.md)de commerciële marktplaats voor een overzicht van analyserapporten die beschikbaar zijn in de commerciële marktplaats voor het Partnercentrum.
- Zie [Overzichtsdashboard in commerciële marktplaatsanalyses](./summary-dashboard.md)voor grafieken, trends en waarden van geaggregeerde gegevens die de activiteit op de markt voor uw aanbieding samenvatten.
- Zie [Orders Dashboard in commerciële marktplaatsanalyses](./orders-dashboard.md)voor informatie over uw bestellingen in een grafische en downloadbare indeling.
- Zie [Gebruiksdashboard in commerciële marktplaatsanalyses](./usage-dashboard.md)voor virtuele machine (VM) voor gebruiks- en factureringsstatistieken met een datameter.
- Zie [Customer Dashboard in commerciële marktplaatsanalyses](./customer-dashboard.md)voor gedetailleerde informatie over uw klanten, inclusief groeitrends.
- Zie [Downloads Dashboard in commerciële marktplaatsanalyses](./downloads-dashboard.md)voor een lijst met uw downloadaanvragen van de afgelopen 30 dagen.
- Zie [Dashboard Beoordelingen en beoordelingen in commerciële marktplaatsanalyses](./ratings-reviews.md)voor ziet een geconsolideerde weergave van feedback van klanten voor aanbiedingen op Azure Marketplace en AppSource.
