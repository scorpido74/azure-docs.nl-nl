---
title: Inleiding tot Azure Advisor
description: Gebruik Azure Advisor om uw Azure-implementaties te optimaliseren.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443082"
---
# <a name="introduction-to-azure-advisor"></a>Inleiding tot Azure Advisor

Meer informatie over de belangrijkste mogelijkheden van Azure Advisor en krijg antwoorden op veelgestelde vragen.

## <a name="what-is-advisor"></a>Wat is Advisor?
Advisor is een gepersonaliseerde cloudconsultant die u helpt bij het volgen van best practices om uw Azure-implementaties te optimaliseren. Het analyseert uw resourceconfiguratie en gebruikstelemetrie, en raadt vervolgens oplossingen aan die u kunnen helpen de kosteneffectiviteit, prestaties, hoge beschikbaarheid en beveiliging van uw Azure-resources te verbeteren.

Met Advisor u:
* Proactieve, bruikbare en aan uw voorkeuren aangepaste aanbevelingen voor best practice ontvangen. 
* Verbeter de prestaties, beveiliging en hoge beschikbaarheid van uw resources, terwijl u mogelijkheden identificeert om uw totale Azure-uitgaven te verminderen.
* Aanbevelingen ontvangen met voorgestelde acties inline.

U hebt toegang tot Advisor via de [Azure-portal.](https://aka.ms/azureadvisordashboard) Meld u aan bij de [portal,](https://portal.azure.com)zoek **Advisor** in het navigatiemenu of zoek ernaar in het menu **Alle services.**

Het Dashboard Advisor bevat gepersonaliseerde aanbevelingen voor al uw abonnementen.  U filters toepassen om aanbevelingen weer te geven voor specifieke abonnementen en resourcetypen.  De aanbevelingen zijn onderverdeeld in vier categorieën: 

* **Hoge beschikbaarheid**: Om de continuïteit van uw bedrijfskritische applicaties te waarborgen en te verbeteren. Zie [Aanbevelingen voor hoge beschikbaarheid van adviseur voor](advisor-high-availability-recommendations.md)meer informatie .
* **Beveiliging:** Om bedreigingen en kwetsbaarheden op te sporen die kunnen leiden tot inbreuken op de beveiliging. Zie [Aanbevelingen voor beveiliging van adviseur voor](advisor-security-recommendations.md)meer informatie.
* **Prestaties:** Om de snelheid van uw toepassingen te verbeteren. Zie [Aanbevelingen voor prestaties van advisor voor](advisor-performance-recommendations.md)meer informatie .
* **Kosten:** Om uw totale Azure-uitgaven te optimaliseren en te verminderen. Zie [Aanbevelingen voor Advisor Cost voor](advisor-cost-recommendations.md)meer informatie.
* **Operational Excellence:** Om u te helpen bij het bereiken van proces- en workflow-efficiëntie, resourcebeheerbaarheid en best practices voor implementatie. . Zie [aanbevelingen voor Advisor Operational Excellence voor](advisor-operational-excellence-recommendations.md)meer informatie.

  ![Aanbevelingstypen van adviseurs](./media/advisor-overview/advisor-dashboard.png)

U op een categorie klikken om de lijst met aanbevelingen in die categorie weer te geven en een aanbeveling selecteren om er meer over te weten te komen.  U ook meer te weten komen over acties die u uitvoeren om gebruik te maken van een verkoopkans of een probleem op te lossen.

![Adviseur aanbevelingcategorie](./media/advisor-overview/advisor-ha-category-example.png) 

Selecteer de aanbevolen actie voor een aanbeveling om de aanbeveling uit te voeren.  Er wordt een eenvoudige interface geopend waarmee u de aanbeveling implementeren of u doorverwijzen naar documentatie die u helpt bij de implementatie.  Zodra u een aanbeveling implementeert, kan het tot een dag duren voordat Advisor dat erkent.

Als u niet van plan bent om onmiddellijk actie te ondernemen op een aanbeveling, u deze voor een bepaalde periode uitstellen of verwijderen.  Als u geen aanbevelingen wilt ontvangen voor een specifiek abonnement of resourcegroep, u Advisor configureren om alleen aanbevelingen voor opgegeven abonnementen en resourcegroepen te genereren.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="how-do-i-access-advisor"></a>Hoe krijg ik toegang tot Advisor?
U hebt toegang tot Advisor via de [Azure-portal.](https://aka.ms/azureadvisordashboard) Meld u aan bij de [portal,](https://portal.azure.com)zoek **Advisor** in het navigatiemenu of zoek ernaar in het menu **Alle services.**

U advisor-aanbevelingen ook bekijken via de resourceinterface voor virtuele machines. Kies een virtuele machine en scrol naar Aanbevelingen van Advisor in het menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Welke machtigingen heb ik nodig om toegang te krijgen tot Advisor?
 
U hebt toegang tot aanbevelingen van Advisor als *eigenaar,* *inzender*of *lezer* van een abonnement.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Voor welke middelen geeft Advisor aanbevelingen?

Advisor geeft aanbevelingen voor Application Gateway, App Services, beschikbaarheidssets, Azure Cache, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure ExpressRoute, Azure Cosmos DB, Azure public IP-adressen, SQL Data Warehouse, SQL-servers, opslagaccounts, Traffic Manager-profielen en virtuele machines.

Azure Advisor bevat ook uw aanbevelingen van [Azure Security Center,](https://docs.microsoft.com/azure/security-center/security-center-recommendations) die aanbevelingen voor aanvullende brontypen kunnen bevatten.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Kan ik een aanbeveling uitstellen of afwijzen?

Als u een aanbeveling wilt uitstellen of afwijzen, klikt u op de koppeling **Uitstellen.** U een uitstelperiode opgeven of De aanbeveling **nooit** verwijderen selecteren.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over aanbevelingen van Advisor:

* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor hoge beschikbaarheid](advisor-high-availability-recommendations.md)
* [Aanbevelingen voor beveiliging van adviseur](advisor-security-recommendations.md)
* [Aanbevelingen voor prestaties van adviseur](advisor-performance-recommendations.md)
* [Aanbevelingen voor kosten van adviseur](advisor-cost-recommendations.md)
