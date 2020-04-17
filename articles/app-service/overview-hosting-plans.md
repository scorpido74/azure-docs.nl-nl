---
title: App Service-abonnementen
description: Lees hoe App Service-abonnementen werken in Azure App Service, hoe ze worden gefactureerd aan de klant en hoe ze worden geschaald naar uw behoeften.
keywords: app-service, azure-app-service, schaal, schaalbaar, schaalbaarheid, app-serviceplan, app-servicekosten
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 11/09/2017
ms.custom: seodec18
ms.openlocfilehash: b1c44fb9f44eb75e6d2a766213c5db094ebe79b1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537641"
---
# <a name="azure-app-service-plan-overview"></a>Overzicht van Azure App Service-plan

In App Service wordt een app uitgevoerd in een _App Service-plan_. Een App Service-plan definieert een set rekenresources waarmee een web-app kan worden uitgevoerd. Deze compute resources zijn analoog aan de [_server farm_](https://wikipedia.org/wiki/Server_farm) in conventionele web hosting. Een of meer apps kunnen worden geconfigureerd om op dezelfde computerbronnen (of in hetzelfde App Service-abonnement) te worden uitgevoerd.

Wanneer u een App Service-abonnement maakt in een bepaalde regio (bijvoorbeeld West-Europa), wordt een set rekenresources gemaakt voor dat plan in die regio. Welke apps u ook in dit App Service-abonnement plaatst, worden uitgevoerd op deze rekenbronnen zoals gedefinieerd door uw App Service-abonnement. In elk App Service-plan wordt het volgende gedefinieerd:

- Regio (VS - west, VS - oost, enzovoort)
- Aantal VM-exemplaren
- Grootte van VM-exemplaren (klein, normaal of groot)
- Prijscategorie (gratis, gedeeld, basic, standaard, premium, premium, geïsoleerd)

De _prijscategorie_ van een App Service-abonnement bepaalt welke appservicefuncties u krijgt en hoeveel u voor het abonnement betaalt. Er zijn verschillende prijscategorieën:

- **Gedeelde compute:** **Gratis** en **gedeeld**, de twee basislagen, voert een app uit op dezelfde Azure VM als andere App Service-apps, inclusief apps van andere klanten. Hierbij worden CPU-quota toegewezen aan elke app die op de gedeelde resources wordt uitgevoerd en kunnen de resources niet worden opgeschaald.
- **Dedicated compute**: De **lagen Basic,** **Standard,** **Premium**en **PremiumV2** draaien apps op speciale Azure VM's. Alleen apps in hetzelfde App Service-plan maken gedeeld gebruik van dezelfde rekenresources. Hoe hoger het niveau, hoe meer VM-exemplaren u kunt gebruiken voor uitschalen.
- **Geïsoleerd:** met deze laag worden speciale Azure VM's uitgevoerd op dedicated Azure Virtual Networks. Het biedt netwerkisolatie bovenop compute isolation aan uw apps. Dit niveau biedt de meeste mogelijkheden voor uitschalen.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Elke laag biedt ook een specifieke subset van App Service-functies. Deze functies omvatten aangepaste domeinen en TLS/SSL-certificaten, automatisch schalen, implementatiesleuven, back-ups, Traffic Manager-integratie en meer. Hoe hoger de laag, hoe meer functies beschikbaar zijn. Zie [App Service-abonnement details](https://azure.microsoft.com/pricing/details/app-service/plans/)voor meer informatie over welke functies in elke prijscategorie worden ondersteund.

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> De nieuwe **PremiumV2-prijscategorie** biedt [VM's uit de Dv2-serie](../virtual-machines/dv2-dsv2-series.md) met snellere processors, SSD-opslag en een dubbele verhouding tussen geheugen en kern in vergelijking met **de standaardlaag.** **PremiumV2** ondersteunt ook een hogere schaal via een verhoogd aantal instance's, terwijl nog steeds alle geavanceerde mogelijkheden in het Standaard-plan worden geleverd. Alle functies die beschikbaar zijn in de bestaande **Premium-laag** zijn opgenomen in **PremiumV2.**
>
> Net als bij andere speciale lagen zijn er drie VM-formaten beschikbaar voor deze laag:
>
> - Klein (één CPU-kern, 3,5 GiB geheugen) 
> - Medium (twee CPU-cores, 7 GiB geheugen) 
> - Groot (vier CPU-cores, 14 GiB geheugen)  
>
> Zie [App Service Pricing](https://azure.microsoft.com/pricing/details/app-service/)voor **premiumv2-prijsinformatie.**
>
> Zie [PremiumV2-laag configureren voor App-service](app-service-configure-premium-tier.md)om aan de slag te gaan met de nieuwe **premiumv2-prijscategorie.**

## <a name="how-does-my-app-run-and-scale"></a>Hoe wordt mijn app uitgevoerd en geschaald?

In de lagen **Gratis** en **gedeeld** ontvangt een app CPU-minuten in een gedeelde VM-instantie en kan deze niet worden uitgeschaald. In andere lagen wordt een app als volgt uitgevoerd en geschaald.

Wanneer u een app maakt in App-service, wordt deze in een App Service-abonnement geplaatst. Wanneer de app wordt uitgevoerd, wordt deze uitgevoerd op alle VM-exemplaren die zijn geconfigureerd in het App-serviceplan. Als meerdere apps in hetzelfde App Service-abonnement zitten, delen ze allemaal dezelfde VM-exemplaren. Als u meerdere implementatiesleuven voor een app hebt, worden alle implementatiesleuven ook uitgevoerd op dezelfde VM-exemplaren. Als u diagnostische logboeken inschakelt, back-ups uitvoert of WebJobs uitvoert, gebruiken ze ook CPU-cycli en geheugen op deze VM-exemplaren.

Op deze manier is het App Service-abonnement de schaaleenheid van de App Service-apps. Als het plan is geconfigureerd om vijf VM-exemplaren uit te voeren, worden alle apps in het plan uitgevoerd op alle vijf exemplaren. Als het abonnement is geconfigureerd voor automatisch schalen, worden alle apps in het abonnement samen geschaald op basis van de instellingen voor automatisch schalen.

Zie [Het aantal instance per e-app handmatig of automatisch schalen](../monitoring-and-diagnostics/insights-how-to-scale.md)voor informatie over het uitschalen van een app.

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Hoeveel kost mijn App Service-abonnement?

In dit gedeelte wordt beschreven hoe app-apps worden gefactureerd. Zie [App Service Pricing](https://azure.microsoft.com/pricing/details/app-service/)voor gedetailleerde, regiospecifieke prijsinformatie.

Behalve voor **De Vrije** laag, brengt een app-serviceplan een uurvergoeding met zich mee op de compute resources die het gebruikt.

- In de **categorie Gedeeld** ontvangt elke app een quotum met CPU-minuten, zodat _elke app_ per uur in rekening wordt gebracht voor het CPU-quotum.
- In de speciale compute-lagen (**Basic**, **Standard**, **Premium**, **PremiumV2**) definieert het App Service-plan het aantal VM-exemplaren waarnaar de apps worden geschaald, zodat _elke VM-instantie_ in het App Service-abonnement een uurvergoeding heeft. Deze VM-exemplaren worden hetzelfde in rekening gebracht, ongeacht het aantal apps dat erop wordt uitgevoerd. Zie [Een App-serviceplan opschonen](app-service-plan-manage.md#delete)om onverwachte kosten te voorkomen.
- In de **laag Geïsoleerd** bepaalt de app-serviceomgeving het aantal geïsoleerde werknemers dat uw apps uitvoert en _elke werknemer_ wordt per uur in rekening gebracht. Daarnaast is er een basisvergoeding per uur voor het uitvoeren van de App Service Environment zelf.

Er worden geen kosten in rekening gebracht voor het gebruik van de App Service-functies die voor u beschikbaar zijn (het configureren van aangepaste domeinen, TLS/SSL-certificaten, implementatiesleuven, back-ups, enz.). De uitzonderingen zijn:

- App Service-domeinen - u betaalt wanneer u er een koopt in Azure en wanneer u deze elk jaar verlengt.
- App Service-certificaten - u betaalt wanneer u er een koopt in Azure en wanneer u deze elk jaar verlengt.
- IP-gebaseerde TLS-verbindingen - Er zijn een uurvergoeding voor elke IP-gebaseerde TLS-verbinding, maar sommige **standaardlagen** of hoger bieden u gratis één IP-gebaseerde TLS-verbinding. SNI-gebaseerde TLS-verbindingen zijn gratis.

> [!NOTE]
> Als u App Service integreert met een andere Azure-service, moet u mogelijk rekening houden met kosten van deze andere services. Als u bijvoorbeeld Azure Traffic Manager gebruikt om uw app geografisch te schalen, brengt Azure Traffic Manager ook kosten in rekening op basis van uw gebruik. Zie [Prijscalculator](https://azure.microsoft.com/pricing/calculator/)als u uw kosten voor kruisservices in Azure wilt schatten. 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Wat moet ik doen als mijn app meer mogelijkheden of functies nodig heeft?

Uw App Service-plan kan op elk gewenst moment omhoog of omlaag worden geschaald. Het is zo eenvoudig als het wijzigen van de prijscategorie van het plan. U kunt eerst een lagere prijscategorie kiezen en later opschalen als u meer App Service-functies nodig hebt.

U bijvoorbeeld beginnen met het testen van uw web-app in een **Gratis** App Service-abonnement en niets betalen. Wanneer u uw [aangepaste DNS-naam](app-service-web-tutorial-custom-domain.md) aan de web-app wilt toevoegen, schaalt u uw abonnement op **naar gedeelde** laag. Later, wanneer u [een TLS-binding](configure-ssl-bindings.md)wilt maken, schaalt u uw abonnement op naar **de basislaag.** Wanneer u [tijdelijke omgevingen](deploy-staging-slots.md)wilt hebben, schaaldan op naar **de standaardlaag.** Wanneer u meer cores, geheugen of opslag nodig hebt, schaal dan op naar een grotere VM-grootte in dezelfde laag.

Hetzelfde werkt in het omgekeerde. Wanneer u denkt dat u de mogelijkheden of functies van een hogere laag niet meer nodig hebt, u naar een lagere laag schalen, wat u geld bespaart.

Zie [Een app opschalen in Azure](manage-scale-up.md)voor informatie over het opschalen van het App Service-abonnement.

Als uw app in hetzelfde App-serviceplan zit met andere apps, u de prestaties van de app verbeteren door de rekenbronnen te isoleren. U dit doen door de app te verplaatsen naar een apart App Service-abonnement. Zie [Een app verplaatsen naar een ander App-serviceabonnement](app-service-plan-manage.md#move)voor meer informatie.

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Moet ik een app in een nieuw abonnement of een bestaand abonnement plaatsen?

Aangezien u betaalt voor de computerbronnen die uw App Service-abonnement toewijst (zie [Hoeveel kost mijn App Service-abonnement)](#cost)), u mogelijk geld besparen door meerdere apps in één App Service-abonnement te plaatsen. U apps blijven toevoegen aan een bestaand abonnement, zolang het plan voldoende resources heeft om de belasting te verwerken. Houd er echter rekening mee dat apps in hetzelfde App Service-abonnement allemaal dezelfde rekenbronnen hebben. Om te bepalen of de benodigde resources voor de nieuwe app aanwezig zijn, moet u de capaciteit van het bestaande App Service-plan en de verwachte belasting voor de nieuwe app weten. Een overbelast App Service-plan kan leiden tot uitvaltijd voor uw nieuwe en bestaande apps.

Isoleer uw app in een nieuw App Service-plan in de volgende gevallen:

- De app is resource-intensief.
- U wilt de app onafhankelijk van de andere apps in het bestaande abonnement schalen.
- De app heeft een resource nodig in een andere geografische regio.

Op deze manier u een nieuwe set resources toewijzen voor uw app en meer controle krijgen over uw apps.

## <a name="manage-an-app-service-plan"></a>Een App-serviceplan beheren

> [!div class="nextstepaction"]
> [Een App-serviceplan beheren](app-service-plan-manage.md)
