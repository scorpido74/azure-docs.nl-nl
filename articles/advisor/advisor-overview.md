---
title: Inleiding tot Azure Advisor | Microsoft Docs
description: Gebruik Azure Advisor om uw Azure-implementaties te optimaliseren.
services: advisor
documentationcenter: NA
author: sagupt
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: sagupt
ms.openlocfilehash: 1a1ed13697d838041d1ac98b966695e24c06edbe
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74145372"
---
# <a name="introduction-to-azure-advisor"></a>Inleiding tot Azure Advisor

Meer informatie over de belangrijkste mogelijkheden van Azure Advisor en antwoorden op veelgestelde vragen.

## <a name="what-is-advisor"></a>Wat is Advisor?
Advisor is een persoonlijke Cloud consultant die u helpt bij het volgen van de aanbevolen procedures voor het optimaliseren van uw Azure-implementaties. Het analyseert uw resourceconfiguratie en gebruikstelemetrie, en raadt vervolgens oplossingen aan die u kunnen helpen de kosteneffectiviteit, prestaties, hoge beschikbaarheid en beveiliging van uw Azure-resources te verbeteren.

Met Advisor kunt u het volgende doen:
* Krijg aanbevelingen voor proactieve, actie bare en aangepaste best practices. 
* Verbeter de prestaties, beveiliging en hoge Beschik baarheid van uw resources, naarmate u uw verkoop kansen identificeert om uw totale Azure-uitgaven te verminderen.
* Krijg aanbevelingen met voorgestelde acties inline.

U hebt toegang tot Advisor via de [Azure Portal](https://aka.ms/azureadvisordashboard). Meld u aan bij de [Portal](https://portal.azure.com), zoek **Advisor** in het navigatie menu of zoek ernaar in het menu **alle services** .

In het Advisor-dash board worden persoonlijke aanbevelingen voor al uw abonnementen weer gegeven.  U kunt filters toep assen om aanbevelingen voor specifieke abonnementen en resource typen weer te geven.  De aanbevelingen zijn onderverdeeld in vier categorieën: 

* **Hoge Beschik baarheid**: om de continuïteit van uw bedrijfs kritieke toepassingen te garanderen en te verbeteren. Zie [aanbevelingen voor hoge Beschik baarheid voor Advisor](advisor-high-availability-recommendations.md)voor meer informatie.
* **Beveiliging**: voor het detecteren van bedreigingen en beveiligings problemen die kunnen leiden tot inbreuk op de beveiliging. Zie [Advisor Security aanbevelingen](advisor-security-recommendations.md)voor meer informatie.
* **Prestaties**: om de snelheid van uw toepassingen te verbeteren. Zie aanbevelingen voor Advisor- [prestaties](advisor-performance-recommendations.md)voor meer informatie.
* **Kosten**: om uw totale Azure-uitgaven te optimaliseren en te verlagen. Zie aanbevelingen voor Advisor- [kosten](advisor-cost-recommendations.md)voor meer informatie.
* **Operationele uitmuntendheid**: om u te helpen proces-en werk stroom efficiëntie, beheer baarheid en aanbevolen procedures voor implementatie te behaalt. . Zie [aanbevelingen voor operationele uitmuntendheid van Advisor](advisor-operational-excellence-recommendations.md)voor meer informatie.

  ![Aanbevelingen van Advisor](./media/advisor-overview/advisor-dashboard.png)

U kunt op een categorie klikken om de lijst met aanbevelingen in die categorie weer te geven en een aanbeveling selecteren voor meer informatie hierover.  U kunt ook meer te weten komen over acties die u kunt uitvoeren om gebruik te maken van een verkoop kans of om een probleem op te lossen.

![Advies categorie adviseur](./media/advisor-overview/advisor-ha-category-example.png) 

Selecteer de aanbevolen actie voor een aanbeveling om de aanbeveling te implementeren.  Er wordt een eenvoudige interface geopend waarmee u de aanbeveling kunt implementeren of kunt verwijzen naar documentatie die u helpt bij de implementatie.  Zodra u een aanbeveling hebt geïmplementeerd, kan het tot een dag duren voordat Advisor dit herkent.

Als u niet van plan bent om onmiddellijk actie te ondernemen volgens een aanbeveling, kunt u deze uitstellen gedurende een bepaalde periode of deze sluiten.  Als u geen aanbevelingen voor een specifiek abonnement of een specifieke resource groep wilt ontvangen, kunt u Advisor configureren zodat alleen aanbevelingen voor opgegeven abonnementen en resource groepen worden gegenereerd.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-do-i-access-advisor"></a>Hoe kan ik Advisor openen?
U hebt toegang tot Advisor via de [Azure Portal](https://aka.ms/azureadvisordashboard). Meld u aan bij de [Portal](https://portal.azure.com), zoek **Advisor** in het navigatie menu of zoek ernaar in het menu **alle services** .

U kunt aanbevelingen van Advisor ook weer geven via de resource interface van de virtuele machine. Kies een virtuele machine en schuif vervolgens naar Advisor aanbevelingen in het menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Welke machtigingen heb ik nodig om Advisor te openen?
 
U kunt aanbevelingen van Advisor als *eigenaar*, *bijdrager*of *lezer* van een abonnement benaderen.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Welke resources biedt Advisor aanbevelingen voor?

Advisor biedt aanbevelingen voor Application Gateway, App Services, beschikbaarheids sets, Azure cache, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure ExpressRoute, Azure Cosmos DB, Azure openbaar IP-adressen, SQL Data Warehouse, SQL-servers, opslag accounts, Traffic Manager profielen en virtuele machines.

Azure Advisor bevat ook uw aanbevelingen van [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) die aanbevelingen kunnen bevatten voor aanvullende resource typen.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Kan ik een aanbeveling uitstellen of negeren?

Als u een aanbeveling wilt uitstellen of verwijderen, klikt u op de koppeling **uitstellen** . U kunt een uitstel periode opgeven of **nooit** selecteren om de aanbeveling te negeren.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:

* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor hoge Beschik baarheid van Advisor](advisor-high-availability-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
* [Aanbevelingen voor Advisor-kosten](advisor-cost-recommendations.md)
