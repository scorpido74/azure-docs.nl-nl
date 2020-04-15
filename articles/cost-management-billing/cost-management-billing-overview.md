---
title: Overzicht van Azure Kostenbeheer en facturering | Microsoft Docs
description: U gebruikt functies van Azure Kostenbeheer en facturering voor het uitvoeren van administratieve taken en het beheren van factureringstoegang tot kosten. Daarnaast zijn er ook functies om Azure-uitgaven te controleren en om het gebruik van Azure-resources te optimaliseren.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: overview
ms.service: cost-management-billing
ms.custom: ''
ms.openlocfilehash: 2f96208ff3f9664d82bfc1d9ddf9bc5b9aec37c3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879085"
---
# <a name="what-is-azure-cost-management-and-billing"></a>Wat is Azure Kostenbeheer en facturering?

Bij producten en services van Azure is het zo dat u alleen betaalt voor wat u gebruikt. Als u Azure-resources maakt en gebruikt, worden er kosten in rekening gebracht voor de resources. U gebruikt functies van Azure Kostenbeheer en facturering voor het uitvoeren van administratieve taken en het beheren van factureringstoegang tot kosten. U kunt ook functies gebruiken voor het bewaken en controleren van Azure-uitgaven en voor het optimaliseren van het resourcegebruik van Azure.

## <a name="understand-azure-billing"></a>Informatie over Azure-facturering

De factureringsfuncties van Azure worden gebruikt om de gefactureerde kosten te controleren en de toegang tot factureringsgegevens te beheren. In grotere organisaties worden factureringstaken normaal gesproken uitgevoerd door speciale teams.

Er wordt een factureringsaccount gemaakt wanneer u zich aanmeldt om Azure te gebruiken. U gebruikt uw factureringsaccount om uw facturen en betalingen te beheren en kosten bij te houden. U kunt toegang hebben tot meerdere factureringsaccounts. Stel dat u zich voor uw persoonlijke projecten hebt geregistreerd voor Azure. U hebt dus misschien een individueel Azure-abonnement met daaraan een factureringsaccount gekoppeld. U kunt ook toegang hebben via de Enterprise-overeenkomst van uw organisatie of de Microsoft-klantovereenkomst. Voor elk scenario hebt u een afzonderlijk factureringsaccount.

### <a name="billing-accounts"></a>Factureringsaccounts

De Azure-portal ondersteunt momenteel de volgende typen factureringsaccounts:

- **Microsoft Online Services-programma**: Er wordt een individueel factureringsaccount voor een Microsoft Online Services-programma gemaakt wanneer u zich bij de Azure-website aanmeldt om Azure te gebruiken. Bijvoorbeeld als u zich aanmeldt voor een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), een [account met tarieven op gebruiksbasis](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [Visual Studio-abonnement](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise Overeenkomst**: Er wordt een factureringsaccount voor een Enterprise-overeenkomst gemaakt wanneer uw organisatie een [Enterprise-overeenkomst (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ondertekent om Azure te gebruiken.

- **Microsoft-klantovereenkomst**: Er wordt een factureringsaccount voor een Microsoft-klantovereenkomst aangemaakt, wanneer uw organisatie samenwerkt met een Microsoft-vertegenwoordiger om een Microsoft-klantovereenkomst te ondertekenen. Sommige klanten in bepaalde regio's, die zich via de Azure-website aanmelden voor een [account met tarieven op gebruiksbasis](https://azure.microsoft.com/offers/ms-azr-0003p/) of die hun [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/) upgraden, hebben mogelijk ook een factureringsaccount voor een Microsoft-klantovereenkomst. Voor meer informatie, zie [Aan de slag met uw factureringsaccount voor een Microsoft-klantovereenkomst](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>Bereiken voor factureringsaccounts
Een bereik is een knooppunt in een factureringsaccount waar u facturen kunt weergeven en beheren. Dit de plek waar u factureringsgegevens, betalingen en facturen beheert en algemeen accountbeheer uitvoert.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services-programma

|Bereik  |Definitie  |
|---------|---------|
|Factureringsaccount     | Verwijst naar een enkele eigenaar (accountbeheerder) voor één of meer Azure abonnementen. Een accountbeheerder is gemachtigd om verschillende factureringstaken uit te voeren, zoals abonnementen maken, facturen inzien of de facturering voor abonnementen wijzigen.  |
|Abonnement     |  Verwijst naar een groepering van Azure-resources. Er wordt een factuur gegenereerd voor het abonnementsbereik. Het heeft zijn eigen betalingswijzen die worden gebruikt om de factuur te betalen.|


#### <a name="enterprise-agreement"></a>Enterprise Agreement

|Bereik  |Definitie  |
|---------|---------|
|Factureringsaccount    | Verwijst naar de registratie van een Enterprise-overeenkomst. Er wordt een factuur gegenereerd voor het bereik van het factureringsaccount. Het is gestructureerd op basis van afdelingen en inschrijvingsaccounts.  |
|Afdeling     |  Optionele groepering van inschrijvingsaccounts.      |
|Inschrijvingsaccount     |  Verwijst naar een enkele accounteigenaar. Azure-abonnementen worden gemaakt voor het bereik van het inschrijvingsaccount.  |


#### <a name="microsoft-customer-agreement"></a>Microsoft-klantovereenkomst

|Bereik  |Taken  |
|---------|---------|
|Factureringsaccount     |   Verwijst naar een klantenovereenkomst voor meerdere Microsoft-producten en -services. Het factureringsaccount is gestructureerd op basis van factureringsprofielen en factuursecties.   |
|Factureringsprofiel     |  Verwijst naar een factuur en de bijbehorende betalingswijzen. De factuur wordt gegenereerd in dit bereik. Het factureringsprofiel kan meerdere factuursecties hebben.      |
|Factuursectie     |   Verwijst naar een kostengroep op een factuur. Abonnementen en andere aankopen worden gekoppeld aan de factuursectie.    |


## <a name="understand-azure-cost-management"></a>Inzicht in het werken met Azure Kostenbeheer
Kostenbeheer is het proces waarmee u effectief de kosten voor uw bedrijf kunt plannen en beheren. Kostenbeheertaken worden normaal gesproken uitgevoerd door de financiële, management- en app-teams. Azure Kostenbeheer en facturering helpt organisaties bij het plannen door rekening te houden met kosten. Het helpt ook effectief kosten te analyseren en actie te ondernemen om clouduitgaven te optimaliseren. Lees het artikel [Azure Cost Management best practices](./costs/cost-mgt-best-practices.md) voor meer informatie over kostenbeheer in een bedrijf.

Bekijk de video [met een overzicht van Azure Cost Management](https://www.youtube.com/watch?v=el4yN5cHsJ0) voor een beknopt overzicht van hoe Azure Cost Management u kan helpen geld te besparen in Azure. Als u andere video’s wilt bekijken, gaat u naar het [YouTube-kanaal voor Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Facturering is wel gerelateerd, maar verschilt van kostenbeheer. Facturering is het proces van facturen versturen naar klanten voor geleverde goederen of diensten en de commerciële relatie beheren.

Cost Management toont organisatorische kosten en gebruikspatronen aan met geavanceerde analyses. Rapporten in Cost Management tonen de verbruikskosten die worden toegerekend aan Azure-services en aanbiedingen op Marketplace van derden. De kosten zijn gebaseerd op de overeengekomen prijzen en er wordt rekening gehouden met kortingen voor reserveringen en Azure Hybrid Benefit. Gezamenlijk tonen de rapporten uw intern en externe kosten voor gebruik en kosten voor Azure Marketplace. Andere kosten, zoals reserveringskosten, ondersteuning en belasting worden nog niet in rapporten getoond. De rapporten helpen u grip te krijgen op uw uitgaven en resource-gebruik en kunnen helpen afwijkingen te vinden in uitgaven. Voorspellende analyses zijn ook beschikbaar. Cost Management gebruikt Azure managementgroepen, budgetten en aanbevelingen om duidelijk aan te tonen hoe uw uitgaven zijn ingedeeld en hoe u kosten kunt reduceren.

U kunt de Azure Portal of verschillende API’s gebruiken voor geautomatiseerde export om kostengegevens te integreren met externe systemen en processen. Geautomatiseerde factureringsgegevensexport en geplande rapporten zijn ook beschikbaar.

### <a name="plan-and-control-expenses"></a>Uitgaven plannen en beheren

Verschillende manieren waarop Cost Management u kan helpen bij het plannen en beheren van uw kosten: Kosten analyseren, budgetten, aanbevelingen en kostenbeheergegevens exporteren.

U gebruikt kostenanalyse om de kosten van Azure voor uw bedrijf te verkennen en te analyseren. U kunt de totale kosten per bedrijf weergeven, zodat u begrijpt waar de kosten worden opgebouwd en uitgavenpatronen kunt identificeren. En u kunt de totale kosten bekijken om geschatte kostentrends per maand, per kwartaal en zelfs per jaar naast een budget te leggen.

Budgetten helpen u met plannen voor en voldoen aan financiële aansprakelijkheid in uw bedrijf. Ze helpen voorkomen dat kostendrempels of -limieten worden overschreden. Budgetten kunnen u ook helpen anderen te informeren over hun uitgaven om proactief kosten te beheren. En u kunt ermee zien hoe kosten zich ontwikkelen in de loop der tijd.

Aanbevelingen tonen hoe u de efficiëntie kunt optimaliseren en verbeteren door inactieve en onderbenutte resources te identificeren. Of ze kunnen goedkopere resource-opties tonen. Als u de aanbevelingen volgt, wijzigt u de manier waarop u uw resources gebruikt om geld te besparen. Om dit te doen, bekijkt u eerst de optimalisatieaanbevelingen om mogelijk inefficiënt gebruik te zien. Daarna volgt u de aanbeveling om uw Azure-resourcegebruik te wijzigen in een meer kosteneffectieve optie. Dan verifieert u de actie om ervoor te zorgen dat de wijziging die u hebt aangebracht ook is geslaagd.

Als u externe systemen gebruikt om kostenbeheergegevens te zien of te controleren, kunt u de gegevens van Azure eenvoudig exporteren. En u kunt een dagelijks geplande export in CSV-indeling instellen en de gegevensbestanden opslaan in Azure-opslag. Zo kunt u de gegevens vanaf uw externe systeem benaderen.

### <a name="cloudyn-deprecation"></a>Afschaffing van Cloudyn

[Cloudyn](./cloudyn/overview.md) is een Azure-service met betrekking tot Cost Management, die eind 2020 wordt afgeschaft. Waar mogelijk worden bestaande Cloudyn-functies rechtstreeks geïntegreerd in de Azure-portal. Er wordt op dit moment geen onboarding uitgevoerd voor nieuwe klanten, maar de ondersteuning voor het product blijft bestaan totdat dit volledig is afgeschaft.
 
Bekijk de [video over Azure Cost Management en Cloudyn](https://www.youtube.com/watch?v=15DzKPMBRxM) voor meer informatie over het gebruik van Azure Cost Management of Cloudyn, op basis van uw bedrijfsbehoeften. Als u andere video’s wilt bekijken, gaat u naar het [YouTube-kanaal voor Cost Management](https://www.youtube.com/c/AzureCostManagement).
 
>[!VIDEO https://www.youtube.com/embed/15DzKPMBRxM]

### <a name="additional-azure-tools"></a>Aanvullende Azure-hulpprogramma’s

Azure heeft andere hulpprogramma’s die geen onderdeel uitmaken van de functieset van Azure Kostenbeheer en facturering. Ze spelen echter een belangrijke rol in het kostenbeheerproces. Zie de volgende koppelingen voor meer informatie over deze hulpprogramma’s.

- [Azure Prijscalculator](https://azure.microsoft.com/pricing/calculator/), gebruik dit hulpprogramma om de initiële cloudkosten in te schatten.
- [Azure Migrate](../migrate/migrate-overview.md), beoordeel de werkbelasting van uw huidige gegevenscentrum voor inzicht in wat er nodig is van een Azure-vervangingsoplossing.
- [Azure Advisor](../advisor/advisor-overview.md), identificeer ongebruikte VM’s en ontvang aanbevelingen over aankopen van Azure gereserveerde instanties.
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), gebruik uw huidige on-premises Windows Server- of SQL Server-licenties voor VM’s in Azure om te besparen.


## <a name="next-steps"></a>Volgende stappen

Nu u weet wat Kostenbeheer en facturering is, is de volgende stap beginnen met het gebruik ervan.

- Begin Azure Cost Management te gebruiken om [kosten te analyseren](./costs/quick-acm-cost-analysis.md).
- U kunt ook meer informatie lezen over [Best practices voor Azure Cost Management](./costs/cost-mgt-best-practices.md).
