---
title: Azure AD-domeinservices voor cloudsolutionproviders | Microsoft Documenten
description: Meer informatie over het in- en beheren van beheerde azure Directory Domain Services-domeinen voor Azure Cloud Solution Providers
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: e7276dcfca6ba033942d62f347ac3a799524cac4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519093"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Implementatie en beheer van Azure Active Directory Domain Services voor Azure Cloud Solution Providers

Azure Cloud Solution Providers (CSP) is een programma voor Microsoft Partners en biedt een licentiekanaal voor verschillende Microsoft-cloudservices. Azure CSP stelt partners in staat om verkopen te beheren, de factureringsrelatie te bezitten, technische en factureringsondersteuning te bieden en het enige aanspreekpunt van de klant te zijn. Daarnaast biedt Azure CSP een volledige set tools, waaronder een selfserviceportal en bijbehorende API's. Met deze hulpprogramma's kunnen CSP-partners azure-resources eenvoudig inrichten en beheren en facturering bieden voor klanten en hun abonnementen.

De [Portal van het Partnercenter](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) is het toegangspunt voor alle Azure CSP-partners en biedt uitgebreide mogelijkheden voor klantbeheer, geautomatiseerde verwerking en meer. Azure CSP-partners kunnen de mogelijkheden van Partner Center gebruiken door een webgebaseerde gebruikersinterface te gebruiken of door PowerShell en verschillende API-aanroepen te gebruiken.

Het volgende diagram laat zien hoe het CSP-model op hoog niveau werkt. Hier heeft Contoso een Azure Active Directory-tenant (Azure AD). Ze hebben een partnerschap met een CSP, die resources implementeert en beheert in hun Azure CSP-abonnement. Contoso kan ook regelmatige (directe) Azure-abonnementen hebben, die rechtstreeks bij Contoso worden gefactureerd.

![Overzicht van het CSP-model](./media/csp/csp_model_overview.png)

De huurder van de CSP-partner heeft drie speciale agentgroepen : *Admin* agents, *Helpdesk* agents en *Sales* agents.

De groep *Beheerdersagents* is toegewezen aan de rol van tenantbeheerder in de Azure AD-tenant van Contoso. Als gevolg hiervan heeft een gebruiker die deel uitmaakt van de beheeragentsgroep van de CSP-partner tenantbeheerdersbevoegdheden in de Azure AD-tenant van Contoso.

Wanneer de CSP-partner een Azure CSP-abonnement voor Contoso voorziet, wordt de groep beheerdersagents toegewezen aan de eigenaarrol voor dat abonnement. Als gevolg hiervan hebben de beheerdersagents van de CSP-partner de vereiste bevoegdheden om Azure-bronnen in te richten, zoals virtuele machines, virtuele netwerken en Azure AD Domain Services namens Contoso.

Zie het Azure [CSP-overzicht](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview) voor meer informatie

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Voordelen van het gebruik van Azure AD DS in een Azure CSP-abonnement

Azure Active Directory Domain Services (Azure AD DS) biedt beheerde domeinservices zoals domeinjoin, groepsbeleid, LDAP, Kerberos/NTLM-verificatie die volledig compatibel is met Windows Server Active Directory Domain Services. In de loop der decennia zijn veel toepassingen gebouwd om tegen AD te werken met behulp van deze mogelijkheden. Veel onafhankelijke softwareleveranciers (ISV's) hebben applicaties bij hun klanten gebouwd en geïmplementeerd. Deze toepassingen zijn moeilijk te ondersteunen, omdat u vaak toegang nodig hebt tot de verschillende omgevingen waar de toepassingen worden geïmplementeerd. Met Azure CSP-abonnementen hebt u een eenvoudiger alternatief met de schaal en flexibiliteit van Azure.

Azure AD DS ondersteunt Azure CSP-abonnementen. U uw toepassing implementeren in een Azure CSP-abonnement dat is gekoppeld aan de Azure AD-tenant van uw klant. Als gevolg hiervan kunnen uw medewerkers (ondersteunend personeel) de VM's waarop uw toepassing is geïmplementeerd beheren, beheren en onderhouden met behulp van de bedrijfsreferenties van uw organisatie.

U ook een door Azure AD DS beheerd domein implementeren in de Azure AD-tenant van uw klant. Uw toepassing wordt vervolgens verbonden met het beheerde domein van uw klant. Mogelijkheden binnen uw toepassing die afhankelijk zijn van Kerberos / NTLM, LDAP of de [System.DirectoryServices API](/dotnet/api/system.directoryservices) werken naadloos tegen het domein van uw klant. Eindklanten profiteren van het gebruik van uw toepassing als een service, zonder zich zorgen te hoeven maken over het onderhoud van de infrastructuur waarop de toepassing wordt geïmplementeerd.

Alle facturering voor Azure-resources die u in dat abonnement gebruikt, inclusief Azure AD DS, wordt aan u in rekening gebracht. Je behoudt de volledige controle over de relatie met de klant als het gaat om verkoop, facturatie, technische ondersteuning etc. Met de flexibiliteit van het Azure CSP-platform kan een klein team van ondersteuningsmedewerkers veel van dergelijke klanten bedienen die instanties van uw toepassing hebben geïmplementeerd.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>CSP-implementatiemodellen voor Azure AD DS

Er zijn twee manieren waarop u Azure AD DS gebruiken met een Azure CSP-abonnement. Kies de juiste op basis van de veiligheids- en eenvoudoverwegingen die uw klanten hebben.

### <a name="direct-deployment-model"></a>Direct implementatiemodel

In dit implementatiemodel is Azure AD DS ingeschakeld binnen een virtueel netwerk dat deel uitmaakt van het Azure CSP-abonnement. De beheerdersagents van de CSP-partner hebben de volgende bevoegdheden:

* *Globale beheerdersbevoegdheden* in de Azure AD-tenant van de klant.
* *Bevoegdheden voor abonnementseigenaren* op het Azure CSP-abonnement.

![Direct implementatiemodel](./media/csp/csp_direct_deployment_model.png)

In dit implementatiemodel kunnen de beheerdersagents van de CSP-provider identiteiten voor de klant beheren. Deze beheerdersagents kunnen taken uitvoeren zoals het inrichten van nieuwe gebruikers of groepen of toepassingen toevoegen binnen de Azure AD-tenant van de klant.

Dit implementatiemodel kan geschikt zijn voor kleinere organisaties die geen speciale identiteitsbeheerder hebben of de voorkeur geven aan het beheer van identiteiten namens de CSP-partner.

### <a name="peered-deployment-model"></a>Peered-implementatiemodel

In dit implementatiemodel is Azure AD DS ingeschakeld binnen een virtueel netwerk van de klant - een rechtstreeks Azure-abonnement dat door de klant wordt betaald. De CSP-partner kan applicaties implementeren binnen een virtueel netwerk dat behoort tot het CSP-abonnement van de klant. De virtuele netwerken kunnen vervolgens worden verbonden met behulp van Azure virtual network peering.

Met deze implementatie kunnen de workloads of toepassingen die door de CSP-partner in het Azure CSP-abonnement worden geïmplementeerd, verbinding maken met het beheerde domein van de klant dat is ingericht in het directe Azure-abonnement van de klant.

![Peered-implementatiemodel](./media/csp/csp_peered_deployment_model.png)

Dit implementatiemodel biedt een scheiding van bevoegdheden en stelt de helpdeskmedewerkers van de CSP-partner in staat om het Azure-abonnement te beheren en resources daarin te implementeren en te beheren. De helpdeskmedewerkers van de CSP-partner hoeven echter geen algemene beheerdersbevoegdheden te hebben in de Azure AD-map van de klant. De identiteitsbeheerders van de klant kunnen identiteiten voor hun organisatie blijven beheren.

Dit implementatiemodel kan geschikt zijn voor scenario's waarin een ISV een gehoste versie van hun on-premises toepassing biedt, die ook verbinding moet maken met het Azure AD van de klant.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Azure AD DS beheren in CSP-abonnementen

De volgende belangrijke overwegingen zijn van toepassing bij het beheren van een beheerd domein in een Azure CSP-abonnement:

* **CSP-beheerders kunnen een beheerd domein inrichten met hun referenties:** Azure AD DS ondersteunt Azure CSP-abonnementen. Gebruikers die deel uitmaken van de groep beheerdersagents van een CSP-partner kunnen een nieuw door Azure AD DS beheerd domein inrichten.

* **CSP's kunnen het maken van nieuwe beheerde domeinen voor hun klanten scripten met PowerShell:** Bekijk [hoe u Azure AD DS inschakelt met PowerShell](powershell-create-instance.md) voor meer informatie.

* **CSP-beheerders kunnen geen doorlopende beheertaken uitvoeren op het beheerde domein met behulp van hun referenties:** CSP-beheerders kunnen geen routinematige beheertaken uitvoeren binnen het beheerde domein met behulp van hun referenties. Deze gebruikers zijn extern aan de Azure AD-tenant van de klant en hun referenties zijn niet beschikbaar binnen de Azure AD-tenant van de klant. Azure AD DS heeft geen toegang tot de wachtwoordhashes van Kerberos en NTLM voor deze gebruikers, zodat gebruikers niet kunnen worden geverifieerd op door Azure AD DS beheerde domeinen.

  > [!WARNING]
  > U moet een gebruikersaccount maken in de map van de klant om doorlopende beheertaken op het beheerde domein uit te voeren.
  >
  > U zich niet aanmelden bij het beheerde domein met de referenties van de CSP-beheerder. Gebruik hiervoor de referenties van een gebruikersaccount van de Azure AD-tenant van de klant. U hebt deze referenties nodig voor taken zoals het samenvoegen van VM's met het beheerde domein, het beheren van DNS of het beheren van groepsbeleid.

* **Het gebruikersaccount dat is gemaakt voor doorlopend beheer moet worden toegevoegd aan de groep *AAD DC-beheerders:* ** de groep *AAD DC-beheerders* heeft bevoegdheden om bepaalde gedelegeerde beheertaken op het beheerde domein uit te voeren. Deze taken omvatten het configureren van DNS, het maken van organisatie-eenheden en het beheren van groepsbeleid.
    
    Als een CSP-partner deze taken op een beheerd domein kan uitvoeren, moet een gebruikersaccount worden gemaakt binnen de Azure AD-tenant van de klant. De referenties voor dit account moeten worden gedeeld met de beheerdersagents van de CSP-partner. Dit gebruikersaccount moet ook worden toegevoegd aan de aad *dc-beheerdersgroep* om configuratietaken op het beheerde domein mogelijk te maken die met dit gebruikersaccount moeten worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Als u wilt beginnen, [schrijft u zich in voor het Azure CSP-programma.](/partner-center/enrolling-in-the-csp-program) Vervolgens u Azure AD Domain Services inschakelen via [de Azure-portal](tutorial-create-instance.md) of [Azure PowerShell.](powershell-create-instance.md)
