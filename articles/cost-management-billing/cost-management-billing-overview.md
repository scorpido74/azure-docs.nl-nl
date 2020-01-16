---
title: Overzicht van Azure Cost Management en facturering | Microsoft Docs
description: U gebruikt Azure Cost Management en facturerings functies voor het uitvoeren van facturerings beheer taken en het beheren van facturerings toegang tot de kosten. U kunt ook de functie gebruiken om Azure-uitgaven te controleren en te beheren en Azure-resource gebruik te optimaliseren.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/20/2019
ms.topic: overview
ms.service: cost-management-billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: cadff1d83a8b47a540efe9b74ffaf6de171138b3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987514"
---
# <a name="what-is-azure-cost-management-and-billing"></a>Wat is Azure Cost Management en facturering?

Met Azure-producten en-services betaalt u alleen voor wat u gebruikt. Bij het maken en gebruiken van Azure-resources worden er kosten in rekening gebracht voor de resources. U gebruikt Azure Cost Management en facturerings functies voor het uitvoeren van facturerings beheer taken en het beheren van facturerings toegang tot de kosten. U hebt ook de functies voor het bewaken en beheren van Azure-uitgaven en het optimaliseren van Azure-resource gebruik.

## <a name="understand-azure-billing"></a>Meer informatie over Azure-facturering

De facturerings functies van Azure worden gebruikt om de gefactureerde kosten te controleren en de toegang tot facturerings gegevens te beheren. In grotere organisaties voeren inkopende en financiële teams doorgaans facturerings taken uit.

Er wordt een factureringsaccount gemaakt wanneer u zich aanmeldt om Azure te gebruiken. U gebruikt uw factureringsaccount om uw facturen en betalingen te beheren en kosten bij te houden. U kunt toegang hebben tot meerdere factureringsaccounts. Stel dat u zich voor uw persoonlijke projecten hebt geregistreerd voor Azure. U hebt mogelijk een individueel Azure-abonnement met een facturerings account. U kunt ook toegang hebben via de Enterprise-overeenkomst van uw organisatie of de Microsoft-klantovereenkomst. Voor elk scenario zou u een afzonderlijk facturerings account hebben.

### <a name="billing-accounts"></a>Facturerings accounts

De Azure Portal ondersteunt momenteel de volgende typen facturerings accounts:

- **Micro soft Online Services-programma**: er wordt een afzonderlijk facturerings account voor een micro soft Online Services-programma gemaakt wanneer u zich aanmeldt voor Azure via de Azure-website. Bijvoorbeeld toen u zich aanmeldde voor een [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/), een [account met tarieven voor betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) of een [Visual Studio-abonnement](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Enterprise Agreement**: er wordt een facturerings account voor een Enterprise Agreement gemaakt wanneer uw organisatie een [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) ondertekent om Azure te gebruiken.

- **Micro soft-klant overeenkomst**: een facturerings account voor een micro soft-klant overeenkomst wordt gemaakt wanneer uw organisatie samenwerkt met een micro soft-vertegenwoordiger om een micro soft-klant overeenkomst te ondertekenen. Sommige klanten in bepaalde regio's, die zich via de Azure-website aanmelden voor een [account met tarieven op gebruiksbasis](https://azure.microsoft.com/offers/ms-azr-0003p/) of die hun [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/) upgraden, hebben mogelijk ook een factureringsaccount voor een Microsoft-klantovereenkomst. Voor meer informatie, zie [Aan de slag met uw factureringsaccount voor een Microsoft-klantovereenkomst](./understand/mca-overview.md).

### <a name="scopes-for-billing-accounts"></a>Bereiken voor factureringsaccounts
Een bereik is een knoop punt in een facturerings account dat u gebruikt om facturering te bekijken en te beheren. U beheert facturerings gegevens, betalingen, facturen en een algemeen account beheer.

#### <a name="microsoft-online-services-program"></a>Microsoft Online Services Program

|Scope  |Definitie  |
|---------|---------|
|Factureringsaccount     | Verwijst naar een enkele eigenaar (accountbeheerder) voor één of meer Azure abonnementen. Een accountbeheerder is gemachtigd om verschillende factureringstaken uit te voeren, zoals abonnementen maken, facturen inzien of de facturering voor abonnementen wijzigen.  |
|Abonnement     |  Verwijst naar een groepering van Azure-resources. Er wordt een factuur gegenereerd bij het abonnements bereik. Het heeft zijn eigen betalingswijzen die worden gebruikt om de factuur te betalen.|


#### <a name="enterprise-agreement"></a>Enterprise Agreement

|Scope  |Definitie  |
|---------|---------|
|Factureringsaccount    | Verwijst naar de registratie van een Enterprise-overeenkomst. De factuur wordt gegenereerd bij het bereik van het facturerings account. Het is gestructureerd met afdelingen en inschrijvings accounts.  |
|Afdeling     |  Optionele groepering van inschrijvingsaccounts.      |
|Inschrijvingsaccount     |  Verwijst naar een enkele accounteigenaar. Azure-abonnementen worden gemaakt onder het bereik van het inschrijvings account.  |


#### <a name="microsoft-customer-agreement"></a>Microsoft-klantovereenkomst

|Scope  |Taken  |
|---------|---------|
|Factureringsaccount     |   Verwijst naar een klantenovereenkomst voor meerdere Microsoft-producten en -services. Het facturerings account is gestructureerd met behulp van facturerings profielen en factuur secties.   |
|Factureringsprofiel     |  Verwijst naar een factuur en de bijbehorende betalingswijzen. De factuur wordt gegenereerd in dit bereik. Het facturerings profiel kan meerdere factuur secties bevatten.      |
|Factuursectie     |   Verwijst naar een kostengroep op een factuur. Abonnementen en andere aankopen zijn gekoppeld aan het gedeelte bereik van de factuur sectie.    |


## <a name="understand-azure-cost-management"></a>Azure Cost Management begrijpen
Kostenbeheer is het proces waarmee u effectief de kosten voor uw bedrijf kunt plannen en beheren. Kostenbeheertaken worden normaal gesproken uitgevoerd door de financiële, management- en app-teams. Azure Cost Management en facturering helpt organisaties bij het plannen van kosten. Het helpt ook kosten effectief te analyseren en actie te ondernemen om Cloud uitgaven te optimaliseren. Lees het artikel [Azure Cost Management best practices](./costs/cost-mgt-best-practices.md) voor meer informatie over kostenbeheer in een bedrijf.

Bekijk de [overzichts video van Azure Cost Management](https://www.youtube.com/watch?v=el4yN5cHsJ0) voor een snel overzicht van hoe Azure Cost Management u kan helpen geld te besparen in Azure.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Facturering is wel gerelateerd, maar verschilt van kostenbeheer. Facturering is het proces van facturen versturen naar klanten voor geleverde goederen of diensten en de commerciële relatie beheren.

Cost Management toont organisatorische kosten en gebruikspatronen aan met geavanceerde analyses. Rapporten in Cost Management de verbruiks kosten weer geven die worden verbruikt door Azure-Services en Marketplace-aanbiedingen van derden. De kosten zijn gebaseerd op de overeengekomen prijzen en de factor in reserverings-en Azure Hybrid Benefit kortingen. Gezamenlijk tonen de rapporten uw intern en externe kosten voor gebruik en kosten voor Azure Marketplace. Andere kosten, zoals reserveringskosten, ondersteuning en belasting worden nog niet in rapporten getoond. De rapporten helpen u grip te krijgen op uw uitgaven en resource-gebruik en kunnen helpen afwijkingen te vinden in uitgaven. Voorspellende analyses zijn ook beschikbaar. Cost Management gebruikt Azure managementgroepen, budgetten en aanbevelingen om duidelijk aan te tonen hoe uw uitgaven zijn ingedeeld en hoe u kosten kunt reduceren.

U kunt de Azure Portal of verschillende API’s gebruiken voor geautomatiseerde export om kostengegevens te integreren met externe systemen en processen. Geautomatiseerde factureringsgegevensexport en geplande rapporten zijn ook beschikbaar.

### <a name="plan-and-control-expenses"></a>Uitgaven plannen en beheren

Op de volgende manieren Cost Management u de kosten voor het plannen en beheren van uw uitgaven: kosten analyse, budgetten, aanbevelingen en kosten beheer gegevens exporteren.

U gebruikt kostenanalyse om de kosten van Azure voor uw bedrijf te verkennen en te analyseren. U kunt de totale kosten per bedrijf weergeven, zodat u begrijpt waar de kosten worden opgebouwd en uitgavenpatronen kunt identificeren. En u kunt de totale kosten bekijken om geschatte kostentrends per maand, per kwartaal en zelfs per jaar naast een budget te leggen.

Budgetten helpen u met plannen voor en voldoen aan financiële aansprakelijkheid in uw bedrijf. Ze helpen voorkomen dat kostendrempels of -limieten worden overschreden. Budgetten kunnen u ook helpen anderen te informeren over hun uitgaven om proactief kosten te beheren. En u kunt ermee zien hoe kosten zich ontwikkelen in de loop der tijd.

Aanbevelingen tonen hoe u de efficiëntie kunt optimaliseren en verbeteren door inactieve en onderbenutte resources te identificeren. Of ze kunnen goedkopere resource-opties tonen. Als u de aanbevelingen volgt, wijzigt u de manier waarop u uw resources gebruikt om geld te besparen. Om dit te doen, bekijkt u eerst de optimalisatieaanbevelingen om mogelijk inefficiënt gebruik te zien. Daarna volgt u de aanbeveling om uw Azure-resourcegebruik te wijzigen in een meer kosteneffectieve optie. Dan verifieert u de actie om ervoor te zorgen dat de wijziging die u hebt aangebracht ook is geslaagd.

Als u externe systemen gebruikt om kostenbeheergegevens te zien of te controleren, kunt u de gegevens van Azure eenvoudig exporteren. En u kunt een dagelijks geplande export in CSV-indeling instellen en de gegevensbestanden opslaan in Azure-opslag. Zo kunt u de gegevens vanaf uw externe systeem benaderen.

### <a name="consider-cloudyn"></a>Overweeg het gebruik van Cloudyn

[Cloudyn](./cloudyn/overview.md) is een Azure-service gerelateerd aan Cost Management. Met Cloudyn kunt u het cloudgebruik en uitgaven voor uw Azure-resources volgen. Ook andere cloudproviders, inclusief AWS en Google worden hierdoor ondersteund. Aan de hand van eenvoudige dashboard-rapporten krijgt u inzicht in de toewijzing van kosten en tevens factureringen en teruggaven. Momenteel ondersteunt Cost Management geen facturering/terugstorting of andere cloudserviceproviders. Maar Cloudyn is een optie die dat _wel_ ondersteunt. Momenteel ondersteunt Cost Management geen CSP-accounts (Microsoft Cloud service provider) maar Cloudyn wel. Als u CSP-accounts hebt of als u show back/terugstorting wilt gebruiken, kunt u Cloudyn gebruiken om uw kosten te beheren.

Bekijk de [video over Azure Cost Management en Cloudyn](https://www.youtube.com/watch?v=PmwFWwSluh8) om aanbevelingen te bekijken wanneer u een Azure Cost Management of Cloudyn moet gebruiken, op basis van uw bedrijfs behoeften.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

### <a name="additional-azure-tools"></a>Aanvullende Azure-hulpprogramma’s

Azure heeft andere hulpprogram ma's die geen deel uitmaken van de functie voor Azure Cost Management en facturering. Ze spelen echter een belangrijke rol in het kostenbeheerproces. Zie de volgende koppelingen voor meer informatie over deze hulpprogramma’s.

- [Azure Prijscalculator](https://azure.microsoft.com/pricing/calculator/), gebruik dit hulpprogramma om de initiële cloudkosten in te schatten.
- [Azure Migrate](../migrate/migrate-overview.md), beoordeel de werkbelasting van uw huidige gegevenscentrum voor inzicht in wat er nodig is van een Azure-vervangingsoplossing.
- [Azure Advisor](../advisor/advisor-overview.md), identificeer ongebruikte VM’s en ontvang aanbevelingen over aankopen van Azure gereserveerde instanties.
- [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), gebruik uw huidige on-premises Windows Server- of SQL Server-licenties voor VM’s in Azure om te besparen.


## <a name="next-steps"></a>Volgende stappen

Nu u bekend bent met Cost Management en facturering, is de volgende stap het starten van de service.

- Begin Azure Cost Management te gebruiken om [kosten te analyseren](./costs/quick-acm-cost-analysis.md).
- U kunt ook meer informatie lezen over [Best practices voor Azure Cost Management](./costs/cost-mgt-best-practices.md).
