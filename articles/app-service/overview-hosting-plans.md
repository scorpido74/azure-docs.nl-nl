---
title: App Service-abonnementen
description: Meer informatie over de werking van App Service plannen in Azure App Service, hoe deze worden gefactureerd voor de klant en hoe u deze kunt schalen voor uw behoeften.
keywords: app service, Azure app service, schaal, schaalbaar, schaal baarheid, app service-plan, kosten van app service
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 11/09/2017
ms.custom: seodec18
ms.openlocfilehash: c0b28bd7a854d1b4a8b387e40fb045d47160768d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672289"
---
# <a name="azure-app-service-plan-overview"></a>Overzicht van Azure App Service plan

In App Service wordt een app uitgevoerd in een _App Service-plan_. Een App Service-plan definieert een set rekenresources waarmee een web-app kan worden uitgevoerd. Deze reken bronnen zijn vergelijkbaar met de [_server farm_](https://wikipedia.org/wiki/Server_farm) in conventionele webhosting. Een of meer apps kunnen worden geconfigureerd om te worden uitgevoerd op dezelfde computer bronnen (of in hetzelfde App Service-abonnement).

Wanneer u in een bepaalde regio een App Service plan maakt (bijvoorbeeld Europa-west), wordt er een set reken bronnen gemaakt voor dat plan in die regio. De apps die u in dit App Service plan plaatst, worden uitgevoerd op deze reken resources, zoals gedefinieerd in uw App Service plan. Elk App Service plan definieert:

- Regio (VS-West, VS-Oost, etc.)
- Aantal VM-exemplaren
- Grootte van VM-exemplaren (klein, normaal, groot)
- Prijs categorie (gratis, gedeeld, Basic, Standard, Premium, PremiumV2, geïsoleerd)

De _prijs categorie_ van een app service plan bepaalt welke app service functies u krijgt en hoeveel u betaalt voor het abonnement. Er zijn een aantal categorie prijs Categorieën:

- **Gedeelde Compute** **: de** twee basis **lagen, een**app, worden op dezelfde Azure-VM uitgevoerd als andere app service apps, inclusief apps van andere klanten. Deze lagen wijzen CPU-quota toe aan elke app die wordt uitgevoerd op de gedeelde bronnen, en de resources kunnen niet worden uitgeschaald.
- **Toegewezen reken kracht**: de lagen **Basic**, **Standard**, **Premium**en **PremiumV2** voeren apps uit op specifieke Azure-vm's. Alleen apps in hetzelfde App Service plan delen dezelfde reken resources. Hoe hoger de laag is, hoe meer VM-exemplaren er beschikbaar zijn voor uitschalen.
- **Geïsoleerd**: deze laag voert specifieke virtuele Azure-machines uit op specifieke Azure Virtual Networks. Het biedt netwerk isolatie boven op reken isolatie voor uw apps. Het biedt de maximale schaal mogelijkheden.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Elke laag biedt ook een specifieke subset van App Service-functies. Deze functies omvatten aangepaste domeinen en SSL-certificaten, automatisch schalen, implementatie sleuven, back-ups, Traffic Manager-integratie en meer. Hoe hoger de laag, hoe meer functies beschikbaar zijn. Zie [app service plan Details](https://azure.microsoft.com/pricing/details/app-service/plans/)als u wilt weten welke functies worden ondersteund in elke prijs categorie.

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> De nieuwe prijs categorie **PremiumV2** voorziet in [dv2-serie vm's](../virtual-machines/windows/sizes-general.md#dv2-series) met snellere processors, SSD-opslag en dubbele geheugen-naar-core-verhouding ten opzichte van de **Standard** -laag. **PremiumV2** biedt ook ondersteuning voor een hogere schaal via een verhoogd aantal instanties, terwijl alle geavanceerde mogelijkheden van het Standard-abonnement nog steeds beschikbaar zijn. Alle beschik bare functies in de bestaande **Premium** -laag zijn opgenomen in **PremiumV2**.
>
> Net als bij andere toegewezen lagen zijn er drie VM-grootten beschikbaar voor deze laag:
>
> - Klein (één CPU-kern, 3,5 GiB geheugen) 
> - Gemiddeld (twee CPU-kernen, 7 GiB geheugen) 
> - Groot (vier CPU-kernen, 14 GiB geheugen)  
>
> Zie [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/)voor **PremiumV2e** prijs informatie.
>
> Zie [PremiumV2 laag configureren voor app service](app-service-configure-premium-tier.md)om aan de slag te gaan met de nieuwe prijs categorie **PremiumV2** .

## <a name="how-does-my-app-run-and-scale"></a>Hoe wordt mijn app uitgevoerd en geschaald?

In de lagen **gratis** en **gedeeld** ontvangt een app CPU-minuten op een gedeeld VM-exemplaar en kan niet worden uitgeschaald. In andere lagen wordt een app als volgt uitgevoerd en geschaald.

Wanneer u een app in App Service maakt, wordt deze in een App Service plan geplaatst. Wanneer de app wordt uitgevoerd, wordt deze uitgevoerd op alle VM-exemplaren die in het App Service plan zijn geconfigureerd. Als meerdere apps zich in hetzelfde App Service plan bevinden, delen ze allemaal dezelfde VM-exemplaren. Als u meerdere implementatie sites voor een app hebt, worden alle implementatie sleuven ook uitgevoerd op dezelfde VM-exemplaren. Als u Diagnostische logboeken inschakelt, back-ups uitvoert of webjobs uitvoert, worden er ook CPU-cycli en geheugen gebruikt voor deze VM-exemplaren.

Op deze manier is het App Service plan de schaal eenheid van de App Service apps. Als het plan is geconfigureerd voor het uitvoeren van vijf VM-exemplaren, worden alle apps in het abonnement uitgevoerd op alle vijf de instanties. Als het plan is geconfigureerd voor automatisch schalen, worden alle apps in het plan samengebracht op basis van de instellingen voor automatisch schalen.

Zie [aantal exemplaren hand matig of automatisch schalen](../monitoring-and-diagnostics/insights-how-to-scale.md)voor meer informatie over het schalen van een app.

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Wat kost mijn App Service?

In deze sectie wordt beschreven hoe App Service-apps worden gefactureerd. Zie [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/)voor gedetailleerde, specifieke prijs informatie.

Met uitzonde ring van de laag **gratis** geldt een app service plan een uurtarief op basis van de reken resources die worden gebruikt.

- In de **gedeelde** laag ontvangt elke app een QUOTUM van CPU-minuten, zodat _elke app elk_ uur in rekening wordt gebracht voor het CPU-quotum.
- In de toegewezen reken lagen (**Basic**, **Standard**, **Premium**, **PremiumV2**) wordt met het app service plan het aantal VM-exemplaren gedefinieerd waarmee de apps worden geschaald, zodat _elk VM-exemplaar_ in het app service plan over een uurtarief in rekening wordt gebracht. Deze VM-exemplaren worden op dezelfde manier in rekening gebracht, ongeacht het aantal apps dat erop wordt uitgevoerd. Zie [een app service plan opschonen](app-service-plan-manage.md#delete)om onverwachte kosten te voor komen.
- In de **geïsoleerde** laag definieert het app service Environment het aantal geïsoleerde werk rollen waarop uw apps worden uitgevoerd, en _elke werk nemer_ wordt per uur in rekening gebracht. Daarnaast zijn er kosten per uur voor het uitvoeren van de App Service Environment zelf.

Er worden geen kosten in rekening gebracht voor het gebruik van de App Service functies die voor u beschikbaar zijn (het configureren van aangepaste domeinen, SSL-certificaten, implementatie sleuven, back-ups, enz.). De uitzonde ringen zijn:

- App Service domeinen: u betaalt wanneer u er een aanschaft in Azure en wanneer u het jaarlijks vernieuwt.
- App Service certificaten: u betaalt wanneer u er een aanschaft in Azure en wanneer u het jaarlijks vernieuwt.
- SSL-verbindingen op basis van IP: er worden per uur kosten in rekening gebracht voor elke SSL-verbinding op basis van IP, maar voor sommige **Standard** -laag of hoger hebt u één SSL-verbinding op basis van IP. SSL-verbindingen op basis van SNI zijn gratis.

> [!NOTE]
> Als u App Service integreert met een andere Azure-service, moet u mogelijk rekening houden met de kosten van deze andere services. Als u bijvoorbeeld Azure Traffic Manager gebruikt om uw app geografisch te schalen, worden er door Azure Traffic Manager ook kosten in rekening gebracht op basis van uw gebruik. Zie [prijs calculator](https://azure.microsoft.com/pricing/calculator/)voor informatie over het schatten van de kosten voor meerdere services in Azure. 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Wat moet ik doen als mijn app meer mogelijkheden of functies nodig heeft?

Uw App Service-abonnement kan op elk gewenst moment omhoog of omlaag worden geschaald. Het is net zo eenvoudig als het wijzigen van de prijs categorie van het plan. U kunt eerst een lagere prijs categorie kiezen en deze later omhoog schalen wanneer u meer App Service functies nodig hebt.

U kunt bijvoorbeeld beginnen met het testen van uw web-app in een **gratis** app service plan en niets betalen. Wanneer u uw [aangepaste DNS-naam](app-service-web-tutorial-custom-domain.md) wilt toevoegen aan de web-app, schaalt u uw plan naar een **gedeelde** laag. Als u later [een SSL-binding wilt maken](configure-ssl-bindings.md), schaalt u uw abonnement op de **Basic** -laag. Wanneer u [faserings omgevingen](deploy-staging-slots.md)wilt, schaalt u omhoog naar de laag **standaard** . Wanneer u meer kernen, geheugen of opslag ruimte nodig hebt, kunt u omhoog schalen naar een grotere VM-grootte in dezelfde laag.

Hetzelfde werkt in de omgekeerde volg orde. Wanneer u denkt dat u de mogelijkheden of functies van een hogere laag niet meer nodig hebt, kunt u omlaag schalen naar een lagere laag, waardoor u geld bespaart.

Zie [een app omhoog schalen in azure](manage-scale-up.md)voor meer informatie over het omhoog schalen van het app service plan.

Als uw app zich in hetzelfde App Service plan bevindt met andere apps, wilt u mogelijk de prestaties van de app verbeteren door de reken resources te isoleren. U kunt dit doen door de app te verplaatsen naar een afzonderlijk App Service plan. Zie [een app naar een andere app service plan verplaatsen](app-service-plan-manage.md#move)voor meer informatie.

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Moet ik een app in een nieuw abonnement of een bestaand abonnement plaatsen?

Omdat u betaalt voor de computer resources die uw App Service plan toewijst (zie hoeveel [kosten mijn app service plan?](#cost)), kunt u geld besparen door meerdere apps in één app service plan te plaatsen. U kunt apps blijven toevoegen aan een bestaand abonnement, zolang het plan voldoende bronnen heeft om de belasting te verwerken. Houd er echter rekening mee dat apps in hetzelfde App Service plan alle dezelfde reken resources delen. Als u wilt weten of de nieuwe app over de benodigde bronnen beschikt, moet u de capaciteit van het bestaande App Service plan begrijpen en de verwachte belasting voor de nieuwe app. Het overbelasting van een App Service plan kan leiden tot uitval tijd voor uw nieuwe en bestaande apps.

Isoleer uw app in een nieuw App Service plan wanneer:

- De app is resource-intensief.
- U wilt de app onafhankelijk van de andere apps in het bestaande abonnement schalen.
- De app heeft resources nodig in een andere geografische regio.

Op deze manier kunt u een nieuwe set resources toewijzen voor uw app en meer controle krijgen over uw apps.

## <a name="manage-an-app-service-plan"></a>Een App Service-abonnement beheren

> [!div class="nextstepaction"]
> [Een App Service-abonnement beheren](app-service-plan-manage.md)
