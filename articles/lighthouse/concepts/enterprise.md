---
title: Azure Lighthouse in zakelijke scenario's
description: De mogelijkheden van Azure Lighthouse kunnen worden gebruikt om het beheer van meerdere tenants te vereenvoudigen binnen een onderneming die meerdere Azure AD-tenants gebruikt.
ms.date: 09/25/2019
ms.topic: conceptual
ms.openlocfilehash: 91089a6fb1a965191489e87027ef508c7ebe2aa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749204"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse in zakelijke scenario's

Het meest voorkomende scenario voor [Azure Lighthouse](../overview.md) is een serviceprovider die resources beheert in de Azure Active Directory-tenants (Azure AD) van zijn klanten. De mogelijkheden van Azure Lighthouse kunnen echter ook worden gebruikt om het beheer van meerdere tenants te vereenvoudigen binnen een onderneming die meerdere Azure AD-tenants gebruikt.

## <a name="single-vs-multiple-tenants"></a>Enkele versus meerdere huurders

Voor de meeste organisaties is beheer eenvoudiger met één Azure AD-tenant. Als u alle resources binnen één tenant hebt, u beheertaken centraliseren door aangewezen gebruikers, gebruikersgroepen of serviceprincipals binnen die tenant. We raden u aan waar mogelijk één tenant voor uw organisatie te gebruiken.

Tegelijkertijd zijn er situaties waarvoor een organisatie mogelijk meerdere Azure AD-tenants moet onderhouden. In sommige gevallen kan dit een tijdelijke situatie zijn, zoals wanneer acquisities hebben plaatsgevonden en een langetermijnstrategie voor tenantconsolidatie enige tijd zal duren om te definiëren. Een organisatie kan ook nodig zijn om meerdere huurders te handhaven op een permanente basis (als gevolg van geheel onafhankelijke dochterondernemingen, geografische of wettelijke vereisten, enzovoort). In gevallen waarin een multi-tenantarchitectuur vereist is, kan Azure gedelegeerd resourcebeheer worden gebruikt om beheerbewerkingen te centraliseren en te stroomlijnen. Abonnementen van meerdere tenants kunnen worden ingeschakeld voor [azure-gedelegeerd resourcebeheer,](azure-delegated-resource-management.md)waardoor aangewezen gebruikers in een beheertenant op een gecentraliseerde en schaalbare manier [cross-tenantbeheerfuncties](cross-tenant-management-experience.md) kunnen uitvoeren.

## <a name="tenant-management-architecture"></a>Tenantmanagementarchitectuur

Wanneer u beheerbewerkingen centraliseert voor meerdere tenants, moet u bepalen welke tenant de gebruikers omvat die beheerbewerkingen uitvoeren voor de andere tenants. Met andere woorden, u moet bepalen welke huurder de beheerder van andere huurders zal zijn.

Stel dat uw organisatie één tenant heeft die we *Tenant A*noemen. Uw organisatie verwerft dan twee extra huurders, *Huurder B* en *Huurder C,* en u hebt zakelijke redenen die vereisen dat u ze als afzonderlijke huurders onderhoudt.

Uw organisatie wil dezelfde beleidsdefinities, back-upprocedures en beveiligingsprocessen gebruiken voor alle tenants. Aangezien u al gebruikers (inclusief gebruikersgroepen en serviceprincipals) hebt die verantwoordelijk zijn voor het uitvoeren van deze taken binnen Tenant A, u alle abonnementen binnen Tenant B en Tenant C aan boord nemen, zodat dezelfde gebruikers in Tenant A deze kunnen uitvoeren Taken.

![Gebruikers in Tenant A beheren resources in Tenant B en Tenant C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Veiligheids- en toegangsoverwegingen

In de meeste bedrijfsscenario's wilt u een volledig abonnement voor Azure-gedelegeerd resourcebeheer delegeren, hoewel u ook alleen specifieke resourcegroepen binnen een abonnement delegeren.

Hoe dan ook, zorg ervoor dat u het principe van de minste bevoegdheden te [volgen bij het definiëren van welke gebruikers toegang hebben tot middelen](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Als u dit doet, u ervoor zorgen dat gebruikers alleen over de machtigingen beschikken die nodig zijn om de vereiste taken uit te voeren en vermindert het de kans op onbedoelde fouten.

Azure Lighthouse en Azure gedelegeerd resourcebeheer bieden alleen logische koppelingen tussen een beheertenant en beheerde tenants, in plaats van gegevens of resources fysiek te verplaatsen. Bovendien gaat de toegang altijd in slechts één richting, van de beherende huurder tot de beheerde huurders.  Gebruikers en groepen in de beheertenant moeten multi-factor authenticatie blijven gebruiken bij het uitvoeren van beheerbewerkingen op beheerde tenantresources.

Ondernemingen met interne of externe governance- en compliancevangrails kunnen [Azure Activity-logboeken](../../azure-monitor/platform/platform-logs-overview.md) gebruiken om te voldoen aan hun transparantievereisten. Wanneer bedrijfshuurders beheer- en beheerde tenantrelaties hebben ingesteld, kunnen gebruikers in elke tenant controleren en inzicht krijgen in acties die door de gebruikers in de andere tenant worden uitgevoerd door geregistreerde activiteit weer te geven.

## <a name="onboarding-process-considerations"></a>Overwegingen voor onboarding-processen

Abonnementen (of resourcegroepen binnen een abonnement) kunnen worden aangesloten op Azure-gedelegeerd bronbeheer door Azure Resource Manager-sjablonen te implementeren of via Managed Services-aanbiedingen die privé of privé zijn gepubliceerd op Azure Marketplace Publiekelijk.

Aangezien zakelijke gebruikers normaal gesproken rechtstreeks toegang kunnen krijgen tot de tenants van de onderneming en er geen noodzaak is om een beheeraanbod op de markt te brengen of te promoten, is het over het algemeen sneller en eenvoudiger om rechtstreeks te implementeren met Azure Resource Manager-sjablonen. Terwijl we verwijzen naar dienstverleners en klanten in de [onboarding begeleiding,](../how-to/onboard-customer.md)kunnen ondernemingen gebruik maken van dezelfde processen.

Huurders binnen een onderneming kunnen destijds worden aangesloten door [een Managed Services-aanbieding te publiceren naar Azure Marketplace.](../how-to/publish-managed-services-offers.md) Als u ervoor wilt zorgen dat de aanbieding alleen beschikbaar is voor de juiste huurders, moet u ervoor zorgen dat uw plannen als privé zijn gemarkeerd. Met een privé-abonnement u de abonnements-id's voor elke tenant die u van plan bent aan boord te verstrekken, en niemand anders zal in staat zijn om uw aanbieding te krijgen.

## <a name="terminology-notes"></a>Terminologienotities

Voor beheer van meerdere tenants binnen de onderneming kan worden begrepen dat verwijzingen naar serviceproviders in de Azure Lighthouse-documentatie van toepassing zijn op de beheertenant binnen een onderneming, dat wil zeggen de tenant die de gebruikers bevat die resources beheren in andere tenants via Azure gedelegeerd resourcebeheer. Op dezelfde manier kunnen verwijzingen naar klanten worden opgevat als van toepassing op de tenants die resources delegeren die moeten worden beheerd via gebruikers in de beheertenant.

In het hierboven beschreven voorbeeld kan huurder A bijvoorbeeld worden gezien als de huurder van de serviceprovider (de beheertenant) en huurder B en Huurder C kunnen worden gezien als de huurders van de klant.

In dat voorbeeld kunnen Tenant A-gebruikers met de juiste machtigingen [gedelegeerde resources weergeven en beheren](../how-to/view-manage-customers.md) op de pagina **Mijn klanten** van de Azure-portal. Evenzo kunnen Tenant B- en Tenant C-gebruikers met de juiste machtigingen [de resources bekijken en beheren die zijn gedelegeerd](../how-to/view-manage-service-providers.md) aan Tenant A op de pagina **Serviceproviders** van de Azure-portal.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [cross-tenant management ervaringen](cross-tenant-management-experience.md).
- Meer informatie over [gedelegeerd Azure-resourcebeheer](azure-delegated-resource-management.md).