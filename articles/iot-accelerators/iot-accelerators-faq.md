---
title: Veelgestelde vragen over IoT Solution Accelerators-Azure | Microsoft Docs
description: In dit artikel worden de veelgestelde vragen voor IoT-oplossings Accelerators beantwoord. Het bevat koppelingen naar de GitHub-opslag plaatsen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 7a2b167f90b4ec79f5fa515ded1c676ca7085e72
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75647710"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Veelgestelde vragen over IoT-oplossingsversnellers

Zie ook de [verbonden specifieke, vooraf](iot-accelerators-faq-cf.md) gevraagde en [specifieke veelgestelde vragen](iot-accelerators-faq-rm-v2.md) .

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Waar vind ik de bron code voor de oplossings Accelerators?

De bron code wordt opgeslagen in de volgende GitHub-opslag plaatsen:

* [Oplossings versneller voor externe controle (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Oplossings versneller voor externe controle (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Oplossings versneller voor voorspellend onderhoud](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Oplossings versneller Connected Factory](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Welke Sdk's kan ik gebruiken om apparaatclients te ontwikkelen voor de oplossings Accelerators?

U vindt koppelingen naar de verschillende talen (C, .NET, Java, Node.js, python) IoT-apparaat-Sdk's in de [Microsoft Azure IOT sdk's](https://github.com/Azure/azure-iot-sdks) github-opslag plaatsen.

Als u het DevKit-apparaat gebruikt, kunt u bronnen en voor beelden vinden in de DevKit-opslag plaats van [IOT](https://github.com/Microsoft/devkit-sdk) github.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-solution-accelerators"></a>Is de nieuwe architectuur voor micro services beschikbaar voor alle drie oplossings Accelerators?

Op dit moment gebruikt alleen de oplossing voor externe controle de architectuur van micro Services, aangezien deze het breedste scenario omvat.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>Welke voor delen biedt de nieuwe open source-architectuur op basis van micro Services in de nieuwe update?

In de afgelopen twee jaar is de Cloud architectuur aanzienlijk ontwikkeld. Micro Services zijn opgegroeid als een geweldig patroon voor schaal baarheid en flexibiliteit, zonder dat dit tot een betere ontwikkelings snelheid kan leiden. Dit architectuur patroon wordt intern in verschillende micro soft-Services gebruikt met uitstekende betrouw baarheid en schaal baarheid. Micro soft zet deze informatie in de praktijk in de oplossings accelerators, zodat klanten hiervan profiteren.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Ik ben een service beheerder en ik wil de adreslijst toewijzing wijzigen tussen mijn abonnement en een specifieke Azure AD-Tenant. Wilt u deze taak Hoe kan ik volt ooien?

Zie [een bestaand abonnement toevoegen aan uw Azure AD-adres lijst](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Ik wil een service beheerder of mede beheerder wijzigen wanneer hij is aangemeld met een organisatie account

Zie de ondersteunings artikelen [service beheerder en mede beheerder wijzigen wanneer u bent aangemeld met een organisatie account](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Waarom wordt deze fout weer gegeven? "Uw account beschikt niet over de juiste machtigingen om een oplossing te maken. Neem contact op met uw account beheerder of probeer met een ander account.

Bekijk het volgende diagram voor hulp:

![Toestemmings stroomdiagram](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Als u de fout blijft zien nadat u een globale beheerder bent van de Azure AD-Tenant en een mede beheerder van het abonnement, moet uw account beheerder de gebruiker verwijderen en de benodigde machtigingen opnieuw toewijzen in deze volg orde. Voeg eerst de gebruiker toe als globale beheerder en voeg vervolgens een gebruiker toe als co-beheerder van het Azure-abonnement. Neem contact op met de [ondersteuning van hulp &](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)als er problemen blijven optreden.

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Waarom wordt deze fout weer gegeven wanneer ik een Azure-abonnement heb? "Een Azure-abonnement is vereist voor het maken van vooraf geconfigureerde oplossingen. U kunt in slechts een paar minuten een gratis proef account maken. "

Als u zeker weet dat u een Azure-abonnement hebt, valideert u de Tenant toewijzing voor uw abonnement en controleert u of de juiste Tenant is geselecteerd in de vervolg keuzelijst. Als u de Tenant juist hebt gevalideerd, volgt u het voor gaande diagram en valideert u de toewijzing van uw abonnement en deze Azure AD-Tenant.

### <a name="where-can-i-find-information-about-the-previous-version-of-the-remote-monitoring-solution"></a>Waar vind ik informatie over de vorige versie van de oplossing voor bewaking op afstand?

De vorige versie van de oplossing voor controle op afstand van externe bewaking heette de IoT Suite vooraf geconfigureerde oplossing voor externe controle. U kunt de gearchiveerde documentatie vinden op [https://docs.microsoft.com/previous-versions/azure/iot-suite/](https://docs.microsoft.com/previous-versions/azure/iot-suite/) .

### <a name="is-the-new-solution-accelerator-available-in-the-same-geographic-region-as-the-existing-solution"></a>Is de nieuwe oplossings versneller beschikbaar in dezelfde geografische regio als de bestaande oplossing?

Ja, de nieuwe controle op afstand is beschikbaar in dezelfde geografische regio's.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Wat is het verschil tussen het verwijderen van een resource groep in de Azure Portal en het klikken op verwijderen in een oplossings versneller in azureiotsolutions.com?

* Als u de oplossings versneller in [azureiotsolutions.com](https://www.azureiotsolutions.com/)verwijdert, verwijdert u alle resources die zijn geïmplementeerd tijdens het maken van de oplossings versneller. Als u extra resources aan de resource groep hebt toegevoegd, worden deze resources ook verwijderd.
* Als u de resource groep in de [Azure Portal](https://portal.azure.com)verwijdert, verwijdert u alleen de resources in die resource groep. U moet ook de Azure Active Directory toepassing verwijderen die aan de oplossings versneller is gekoppeld.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kan ik mijn bestaande investeringen in azure IoT-oplossings Accelerators blijven gebruiken?

Ja. Alle oplossingen die nu bestaan, blijven werken in uw Azure-abonnement en de bron code blijft beschikbaar in GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hoeveel IoT Hub exemplaren kan ik inrichten in een abonnement?

U kunt standaard [10 IOT-hubs inrichten per abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). U kunt een [ondersteunings ticket voor Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) maken om deze limiet te verhogen. Omdat elke oplossings versneller een nieuw IoT Hub heeft, kunt u Maxi maal 10 oplossings Accelerators inrichten in een bepaald abonnement.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hoeveel Azure Cosmos DB exemplaren kan ik inrichten in een abonnement?

50. U kunt een [ondersteunings ticket voor Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) maken om deze limiet te verhogen, maar standaard kunt u maxi maal 50 exemplaren van Cosmos DB per abonnement inrichten.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Hoeveel gratis Bing Kaarten-API's kan ik inrichten met een abonnement?

Twee. U kunt slechts twee interne trans actie Level 1 Bing Kaarten maken voor Enter prise-abonnementen in een Azure-abonnement. De oplossing voor controle op afstand wordt standaard ingericht met het abonnement voor interne trans acties niveau 1. Als gevolg hiervan kunt u Maxi maal twee externe bewakings oplossingen inrichten in een abonnement zonder wijzigingen.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Kan ik een oplossings versneller maken als ik Microsoft Azure heb voor DreamSpark?

> [!NOTE]
> Microsoft Azure voor DreamSpark is nu bekend als Microsoft Imagine voor studenten.

Op dit moment kunt u geen oplossings versneller maken met een [Microsoft Azure voor DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) -account. U kunt echter in slechts een paar minuten een [gratis proef account voor Azure](https://azure.microsoft.com/free/) maken, waardoor u een oplossings versneller maakt.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hoe kan ik een Azure AD-Tenant verwijderen?

Zie het blog bericht over het [verwijderen van een Azure AD-Tenant met](https://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx)de bespreking van het weblog boek van Eric golpe.

### <a name="next-steps"></a>Volgende stappen

U kunt ook enkele van de andere functies en mogelijkheden van de IoT-oplossingsversnellers bekijken:

* [Bekijk de mogelijkheden van de oplossings versneller voor externe controle](quickstart-remote-monitoring-deploy.md)
* [Overzicht van de oplossingsversneller Voorspeld onderhoud](iot-accelerators-predictive-overview.md)
* [Connected Factory Solution Accelerator implementeren](quickstart-connected-factory-deploy.md)
* [Fundamentele IoT-beveiliging](/azure/iot-fundamentals/iot-security-ground-up)
