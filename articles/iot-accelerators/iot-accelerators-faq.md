---
title: Veelgestelde vragen over IoT-oplossing - Azure | Microsoft Documenten
description: Dit artikel beantwoordt de veelgestelde vragen voor IoT-oplossingsversnellers. Het bevat links naar de GitHub repositories.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 7a2b167f90b4ec79f5fa515ded1c676ca7085e72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647710"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Veelgestelde vragen over IoT-oplossingsversnellers

Zie ook de [Connected Factory-specifieke FAQ](iot-accelerators-faq-cf.md) en de [Remote Monitoring-specifieke FAQ.](iot-accelerators-faq-rm-v2.md)

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Waar vind ik de broncode voor de oplossingsversnellers?

De broncode wordt opgeslagen in de volgende GitHub-repositories:

* [Remote Monitoring oplossingsversneller (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Remote Monitoring oplossingversneller (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Predictive Maintenance oplossing versneller](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Connected Factory-oplossingsversneller](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Welke SDK's kan ik gebruiken om apparaatclients te ontwikkelen voor de oplossingsversnellers?

U koppelingen naar de verschillende taal (C, .NET, Java, Node.js, Python) IoT-apparaat SDKs vinden in de [Microsoft Azure IoT SDKs](https://github.com/Azure/azure-iot-sdks) GitHub-repositories.

Als u het DevKit-apparaat gebruikt, u bronnen en voorbeelden vinden in de [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub-opslagplaats.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Is de nieuwe microservices-architectuur beschikbaar voor alle drie de oplossingsversnellers?

Momenteel gebruikt alleen de oplossing voor externe bewaking de microservices-architectuur, omdat deze het breedste scenario dekt.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Welke voordelen biedt de nieuwe open-source microservices-gebaseerde architectuur in de nieuwe update?

In de afgelopen twee jaar is de cloudarchitectuur sterk geëvolueerd. Microservices zijn ontstaan als een geweldig patroon om schaal en flexibiliteit te bereiken, zonder in te boeten aan ontwikkelingssnelheid. Dit architecturale patroon wordt intern gebruikt in verschillende Microsoft-services met grote betrouwbaarheids- en schaalbaarheidsresultaten. Microsoft brengt deze learnings in de praktijk in de oplossingsversnellers, zodat klanten ervan profiteren.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Ik ben een servicebeheerder en ik wil de directorytoewijzing tussen mijn abonnement en een specifieke Azure AD-tenant wijzigen. Hoe voltooi ik deze taak?

Zie [Een bestaand abonnement toevoegen aan uw Azure AD-map](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Ik wil een servicebeheerder of co-beheerder wijzigen wanneer ik is aangemeld met een organisatieaccount

Zie het ondersteuningsartikel [Servicebeheerder en medebeheerder wijzigen wanneer u bent aangemeld met een organisatieaccount](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Waarom zie ik deze fout? "Uw account heeft niet de juiste machtigingen om een oplossing te maken. Neem contact op met uw accountbeheerder of probeer het met een ander account."

Bekijk het volgende diagram voor richtlijnen:

![Stroomdiagram machtigingen](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Als u de fout blijft zien nadat u valideert, bent u een globale beheerder van de Azure AD-tenant en een medebeheerder van het abonnement, laat uw accountbeheerder de gebruiker verwijderen en de benodigde machtigingen in deze volgorde opnieuw toewijzen. Voeg eerst de gebruiker toe als globale beheerder en voeg vervolgens de gebruiker toe als medebeheerder van het Azure-abonnement. Als er problemen blijven bestaan, neemt u contact op met [Help & Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Waarom zie ik deze fout als ik een Azure-abonnement heb? "Een Azure-abonnement is vereist om vooraf geconfigureerde oplossingen te maken. U in slechts een paar minuten een gratis proefaccount aanmaken.

Als u zeker weet dat u een Azure-abonnement hebt, valideert u de tenanttoewijzing voor uw abonnement en controleert u of de juiste tenant is geselecteerd in de vervolgkeuzelijst. Als u hebt gevalideerd dat de tenant juist is, volgt u het voorgaande diagram en valideert u de toewijzing van uw abonnement en deze Azure AD-tenant.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Waar kan ik informatie vinden over de vorige versie van de Remote Monitoring-oplossing?

De vorige versie van de Remote Monitoring oplossingsversneller stond bekend als de IoT Suite Remote Monitoring vooraf geconfigureerde oplossing. U vindt de gearchiveerde [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/)documentatie op .

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Is de nieuwe oplossingsversneller beschikbaar in dezelfde geografische regio als de bestaande oplossing?

Ja, de nieuwe externe bewaking is beschikbaar in dezelfde geografische regio's.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Wat is het verschil tussen het verwijderen van een brongroep in de Azure-portal en het klikken op verwijderen op een oplossingsversneller in azureiotsolutions.com?

* Als u de oplossingsversneller in [azureiotsolutions.com](https://www.azureiotsolutions.com/)verwijdert, verwijdert u alle bronnen die zijn geïmplementeerd toen u de oplossingsversneller hebt gemaakt. Als u extra resources aan de resourcegroep hebt toegevoegd, worden deze bronnen ook verwijderd.
* Als u de brongroep in de [Azure-portal](https://portal.azure.com)verwijdert, verwijdert u alleen de bronnen in die brongroep. U moet ook de Azure Active Directory-toepassing verwijderen die is gekoppeld aan het oplossingsversneller.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kan ik mijn bestaande investeringen in Azure IoT-oplossingsversnellers blijven gebruiken?

Ja. Elke oplossing die vandaag de dag bestaat, blijft werken in uw Azure-abonnement en de broncode blijft beschikbaar in GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hoeveel IoT Hub-exemplaren kan ik inrichten in een abonnement?

Standaard u [10 IoT-hubs per abonnement inrichten.](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits) U een [Azure-ondersteuningsticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) maken om deze limiet te verhogen. Aangezien elke oplossingsversneller een nieuwe IoT Hub inziet, u in een bepaald abonnement slechts tot 10 oplossingsversnellers inrichten.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hoeveel Azure Cosmos DB-exemplaren kan ik inrichten in een abonnement?

Vijftig. U een [Azure-ondersteuningsticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) maken om deze limiet te verhogen, maar standaard u alleen 50 Cosmos DB-exemplaren per abonnement inrichten.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hoeveel gratis Bing Kaarten-API's kan ik inrichten met een abonnement?

Twee. U slechts twee Bing Maps for Enterprise-abonnementen voor interne transacties maken in een Azure-abonnement. De oplossing voor externe bewaking is standaard ingericht met het plan intern transactieniveau 1. Als gevolg hiervan u maximaal twee Remote Monitoring-oplossingen in een abonnement inleveren zonder wijzigingen.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Kan ik een oplossingsversneller maken als ik Microsoft Azure voor DreamSpark heb?

> [!NOTE]
> Microsoft Azure voor DreamSpark staat nu bekend als Microsoft Imagine voor studenten.

Momenteel u geen oplossingsversneller maken met een [Microsoft Azure voor DreamSpark-account.](https://azure.microsoft.com/pricing/member-offers/imagine/) U echter in slechts een paar minuten een [gratis proefaccount voor Azure](https://azure.microsoft.com/free/) maken waarmee u een oplossingsversneller maken.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hoe verwijder ik een Azure AD-tenant?

Zie Eric Golpe's blogpost [Walkthrough of Deleting an Azure AD Tenant](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Volgende stappen

U kunt ook enkele van de andere functies en mogelijkheden van de IoT-oplossingsversnellers bekijken:

* [Ontdek de mogelijkheden van de remote monitoring oplossingsversneller](quickstart-remote-monitoring-deploy.md)
* [Een overzicht van de oplossingsversneller voor Predictief onderhoud](iot-accelerators-predictive-overview.md)
* [Connected Factory-oplossingsversneller implementeren](quickstart-connected-factory-deploy.md)
* [Fundamentele IoT-beveiliging](/azure/iot-fundamentals/iot-security-ground-up)
