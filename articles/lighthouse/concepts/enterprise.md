---
title: Azure Lighthouse in zakelijke scenario's
description: De mogelijkheden van Azure Lighthouse kunnen worden gebruikt voor het vereenvoudigen van cross-Tenant beheer binnen een onderneming die gebruikmaakt van meerdere Azure AD-tenants.
ms.date: 09/25/2019
ms.topic: overview
ms.openlocfilehash: ae2241048e6ae481e319154beedab74c23c91163
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132496"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse in zakelijke scenario's

Het meest voorkomende scenario voor [Azure Lighthouse](../overview.md) is een service provider die resources beheert in de Azure Active Directory (Azure AD)-tenants van klanten. De mogelijkheden van Azure Lighthouse kunnen echter ook worden gebruikt voor het vereenvoudigen van cross-Tenant beheer binnen een onderneming die gebruikmaakt van meerdere Azure AD-tenants.

## <a name="single-vs-multiple-tenants"></a>Enkele versus meerdere tenants

Voor de meeste organisaties is het beheer eenvoudiger met één Azure AD-Tenant. Met alle resources binnen één Tenant kunnen beheer taken worden gecentraliseerd door bepaalde gebruikers, gebruikers groepen of service-principals binnen die Tenant. We raden u aan om waar mogelijk één Tenant voor uw organisatie te gebruiken.

Op hetzelfde moment zijn er situaties waarin een organisatie mogelijk meerdere Azure AD-tenants moet onderhouden. In sommige gevallen kan dit een tijdelijke situatie zijn, alsof er verwervingen hebben plaatsgevonden en een strategie voor een langdurige Tenant consolidatie enige tijd in beslag neemt. Een organisatie moet mogelijk ook op doorlopende wijze meerdere tenants onderhouden (als gevolg van de volledige onafhankelijke dochter ondernemingen, geografische of juridische vereisten, enzovoort). In gevallen waarin een architectuur met meerdere tenants is vereist, kan Azure delegated resource management worden gebruikt om beheer bewerkingen te centraliseren en te stroom lijnen. Abonnementen van meerdere tenants kunnen worden voor bereid op het beheer van de [gedelegeerde resources van Azure](azure-delegated-resource-management.md), waardoor aangewezen gebruikers in een Tenant beheren voor het uitvoeren van [Cross-Tenant beheer functies](cross-tenant-management-experience.md) in een gecentraliseerde en schaal bare manier.

## <a name="tenant-management-architecture"></a>Architectuur van Tenant beheer

Wanneer u beheer bewerkingen verspreidt over meerdere tenants, moet u bepalen welke Tenant de gebruikers zijn die beheer bewerkingen uitvoeren voor de andere tenants. Met andere woorden, u moet bepalen welke Tenant de Tenant voor het beheren van andere tenants wordt.

Stel bijvoorbeeld dat uw organisatie een enkele Tenant heeft die *Tenant a*oproept. Uw organisatie verwerft vervolgens twee extra tenants, *Tenant B* en *Tenant C*, en u hebt zakelijke redenen die u moeten onderhouden als afzonderlijke tenants.

Uw organisatie wil dezelfde beleids definities, back-upprocedures en beveiligings processen voor alle tenants gebruiken. Omdat u al gebruikers (met inbegrip van gebruikers groepen en service-principals) hebt die verantwoordelijk zijn voor het uitvoeren van deze taken binnen Tenant A, kunt u alle abonnementen in Tenant B en Tenant C onboarden zodat dezelfde gebruikers in Tenant A deze kunnen uitvoeren. stappen.

![Gebruikers in tenants die een resources beheren in Tenant B en Tenant C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Aandachtspunten voor beveiliging en toegang

In de meeste bedrijfs scenario's wilt u een volledig abonnement voor Azure delegated resource management delegeren, maar u kunt ook alleen specifieke resource groepen binnen een abonnement delegeren.

In beide gevallen moet u [het principe van minimale bevoegdheid volgen om te definiëren welke gebruikers toegang hebben tot resources](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Dit zorgt ervoor dat gebruikers alleen over de benodigde machtigingen beschikken om de vereiste taken uit te voeren en de kans op onbedoelde fouten te verminderen.

Azure Lighthouse en Azure delegated resource management bieden alleen logische koppelingen tussen een beheer Tenant en beheerde tenants, in plaats van fysiek gegevens of bronnen te verplaatsen. Bovendien gaat de Access altijd in slechts één richting, van de Tenant beheren naar de beheerde tenants.  Gebruikers en groepen in de Tenant beheren moeten multi-factor Authentication blijven gebruiken bij het uitvoeren van beheer bewerkingen op beheerde Tenant bronnen.

Ondernemingen met interne of externe governance-en nalevings Guardrails kunnen [Azure-activiteiten logboeken](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) gebruiken om te voldoen aan hun transparantie vereisten. Wanneer ondernemings tenants beheer-en beheerde Tenant relaties hebben ingesteld, kunnen gebruikers in elke Tenant de acties die door de gebruikers in de andere Tenant worden uitgevoerd, bewaken en inzicht krijgen door de geregistreerde activiteit te bekijken.

## <a name="onboarding-process-considerations"></a>Overwegingen voor het onboarding-proces

Abonnementen (of resource groepen binnen een abonnement) kunnen worden gedistribueerd naar Azure delegated resource management door Azure Resource Manager sjablonen of via beheerde services aanbiedingen te implementeren die zijn gepubliceerd naar Azure Marketplace, hetzij privé, hetzij Pas.

Omdat zakelijke gebruikers normaal gesp roken direct toegang kunnen krijgen tot de tenants van de onderneming en er geen beheer aanbod hoeft te worden gemarket of bevorderd, is het over het algemeen sneller en eenvoudiger om rechtstreeks met Azure Resource Manager-sjablonen te implementeren. Hoewel we naar service providers en klanten verwijzen in de voorbereidings [richtlijnen](../how-to/onboard-customer.md), kunnen ondernemingen dezelfde processen gebruiken.

Indien gewenst kunnen tenants binnen een onderneming onboarding worden uitgevoerd door [een managed services-aanbod naar Azure Marketplace te publiceren](../how-to/publish-managed-services-offers.md). Om ervoor te zorgen dat de aanbieding alleen beschikbaar is voor de juiste tenants, moet u ervoor zorgen dat uw plannen als privé zijn gemarkeerd. Met een privé-abonnement kunt u de abonnements-Id's opgeven voor elke Tenant die u wilt voorbereiden en hoeft niemand anders uw aanbieding te ontvangen.

## <a name="terminology-notes"></a>Terminologie notities

Voor beheer van meerdere tenants binnen de onderneming kunnen verwijzingen naar service providers in de Azure Lighthouse-documentatie worden geïnterpreteerd voor het beheren van de Tenant binnen een onderneming, dat wil zeggen, de Tenant die de gebruikers bevat die resources gaan beheren in andere tenants met behulp van het beheer van gedelegeerde resources van Azure. Verwijzingen naar klanten kunnen op dezelfde manier worden toegepast op de tenants die resources overdragen die worden beheerd door gebruikers in de Tenant beheren.

In het bovenstaande voor beeld kan Tenant A bijvoorbeeld worden beschouwd als de Tenant van de service provider (de Tenant beheren) en Tenant B en Tenant C kunnen worden beschouwd als de tenants van de klant.

In dit voor beeld kunnen tenants van een gebruiker met de juiste machtigingen [gedelegeerde resources weer geven en beheren](../how-to/view-manage-customers.md) op de pagina **mijn klanten** van de Azure Portal. Daarnaast kunnen Tenant B-en Tenant C-gebruikers met de juiste machtigingen [de resources weer geven en beheren die zijn gedelegeerd](../how-to/view-manage-service-providers.md) aan Tenant A op de pagina **service providers** van de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ervaring op het beheer van cross-tenants](cross-tenant-management-experience.md).
- Meer informatie over [gedelegeerd Azure-resourcebeheer](azure-delegated-resource-management.md).