---
title: Azure AD-domeinservices voor cloudsolutionproviders | Microsoft Documenten
description: Azure Active Directory Domain Services voor Azure Cloud Solution Providers.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: iainfou
ms.openlocfilehash: 1134c078ee36a146cb1e1cbf8ca46f6cd9f8d775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754442"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure Active Directory (AD) Domain Services voor Azure Cloud Solution Providers (CSP)
In dit artikel wordt uitgelegd hoe u Azure AD Domain Services gebruiken in een Azure CSP-abonnement.

## <a name="overview-of-azure-csp"></a>Overzicht van Azure CSP
Azure CSP is een programma voor Microsoft Partners en biedt een licentiekanaal voor verschillende Microsoft-cloudservices. Azure CSP stelt partners in staat om verkopen te beheren, de factureringsrelatie te bezitten, technische en factureringsondersteuning te bieden en het enige aanspreekpunt van de klant te zijn. Daarnaast biedt Azure CSP een volledige set tools, waaronder een selfserviceportal en bijbehorende API's. Met deze hulpprogramma's kunnen CSP-partners azure-resources eenvoudig inrichten en beheren en facturering bieden voor klanten en hun abonnementen.

De [Portal Van het Partnercentrum](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) fungeert als toegangspunt voor alle Azure CSP-partners. Het biedt uitgebreide mogelijkheden voor klantbeheer, geautomatiseerde verwerking en meer. Azure CSP-partners kunnen de mogelijkheden van Partner Center gebruiken door een webgebaseerde gebruikersinterface te gebruiken of door PowerShell en verschillende API-aanroepen te gebruiken.

Het volgende diagram laat zien hoe het CSP-model op hoog niveau werkt. Contoso heeft een Azure AD Active Directory. Ze hebben een partnerschap met een CSP, die resources implementeert en beheert in hun Azure CSP-abonnement. Contoso kan ook regelmatige (directe) Azure-abonnementen hebben, die rechtstreeks bij Contoso worden gefactureerd.

![Overzicht van het CSP-model](./media/csp/csp_model_overview.png)

De huurder van de CSP-partner heeft drie speciale agentgroepen : Admin agents, Helpdesk agents en Sales agents. De groep Beheerdersagents is toegewezen aan de rol van tenantbeheerder in de Azure AD-map van Contoso. Als gevolg hiervan heeft een gebruiker die deel uitmaakt van de beheeragentsgroep van de CSP-partner tenantbeheerdersbevoegdheden in de Azure AD-map van Contoso. Wanneer de CSP-partner een Azure CSP-abonnement voor Contoso voorziet, wordt de groep beheerdersagents toegewezen aan de eigenaarrol voor dat abonnement. Als gevolg hiervan hebben de beheerdersagents van de CSP-partner de vereiste bevoegdheden om Azure-bronnen in te richten, zoals virtuele machines, virtuele netwerken en Azure AD Domain Services namens Contoso.

Zie het Azure [CSP-overzicht](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview) voor meer informatie

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Voordelen van het gebruik van Azure AD Domain Services in een Azure CSP-abonnement
Azure AD Domain Services biedt Windows Server AD-compatibele services in Azure, zoals LDAP, Kerberos/NTLM-verificatie, domeinjoin, groepsbeleid en DNS. In de loop der decennia zijn veel toepassingen gebouwd om tegen AD te werken met behulp van deze mogelijkheden. Veel onafhankelijke softwareleveranciers (ISV's) hebben applicaties bij hun klanten gebouwd en geïmplementeerd. Deze toepassingen zijn lastig te ondersteunen, omdat dat vaak vereist toegang tot de verschillende omgevingen waarin deze toepassingen worden ingezet. Met Azure CSP-abonnementen hebt u een eenvoudiger alternatief met de schaal en flexibiliteit van Azure.

Azure AD Domain Services ondersteunt nu Azure CSP-abonnementen. U uw toepassing nu implementeren in een Azure CSP-abonnement dat is gekoppeld aan de Azure AD-map van uw klant. Als gevolg hiervan kunnen uw medewerkers (ondersteunend personeel) de virtuele machines waarop uw toepassing is geïmplementeerd beheren, beheren en onderhouden met behulp van de bedrijfsreferenties van uw organisatie. Verder u een beheerd Azure AD Domain Services-domein inrichten voor de Azure AD-directory van uw klant. Uw toepassing is verbonden met het beheerde domein van uw klant. Daarom werken mogelijkheden binnen uw toepassing die afhankelijk zijn van Kerberos/NTLM, LDAP of de [System.DirectoryServices API](/dotnet/api/system.directoryservices) naadloos tegen de directory van uw klant. Uw eindklanten profiteren enorm van het gebruik van uw toepassing als service, zonder zich zorgen te hoeven maken over het onderhoud van de infrastructuur waarop de toepassing wordt geïmplementeerd.

Alle facturering voor Azure-resources die u in dat abonnement gebruikt, inclusief Azure AD Domain Services, wordt aan u in rekening gebracht. Je behoudt de volledige controle over de relatie met de klant als het gaat om verkoop, facturatie, technische ondersteuning etc. Met de flexibiliteit van het Azure CSP-platform kan een klein team van ondersteuningsmedewerkers veel van dergelijke klanten bedienen die instanties van uw toepassing hebben geïmplementeerd.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>CSP-implementatiemodellen voor Azure AD-domeinservices
Er zijn twee manieren waarop u Azure AD Domain Services gebruiken met een Azure CSP-abonnement. Kies de juiste op basis van de veiligheids- en eenvoudoverwegingen die uw klanten hebben.

### <a name="direct-deployment-model"></a>Direct implementatiemodel
In dit implementatiemodel is Azure AD Domain Services ingeschakeld binnen een virtueel netwerk dat behoort tot het Azure CSP-abonnement. De beheerdersagents van de CSP-partner hebben de volgende bevoegdheden:
* Globale beheerdersrechten in de Azure AD-map van de klant.
* Bevoegdheden voor abonnementseigenaren op het Azure CSP-abonnement.

![Direct implementatiemodel](./media/csp/csp_direct_deployment_model.png)

In dit implementatiemodel kunnen de beheerdersagents van de CSP-provider identiteiten voor de klant beheren. Deze beheerdersagents hebben de mogelijkheid om nieuwe gebruikers, groepen, toepassingen toe te voegen binnen de Azure AD-map van de klant, enz. Dit implementatiemodel kan geschikt zijn voor kleinere organisaties die geen speciale identiteitsbeheerder hebben of de voorkeur geven aan het beheer van identiteiten namens de CSP-partner.


### <a name="peered-deployment-model"></a>Peered-implementatiemodel
In dit implementatiemodel is Azure AD Domain Services ingeschakeld binnen een virtueel netwerk van de klant, dat wil zeggen een direct Azure-abonnement dat door de klant wordt betaald. De CSP-partner kan vervolgens applicaties implementeren binnen een virtueel netwerk dat behoort tot het CSP-abonnement van de klant. De virtuele netwerken kunnen vervolgens worden verbonden met behulp van Azure virtual network peering. Als gevolg hiervan kunnen de workloads/toepassingen die door de CSP-partner in het Azure CSP-abonnement worden geïmplementeerd, verbinding maken met het beheerde domein van de klant dat is ingericht in het directe Azure-abonnement van de klant.

![Peered-implementatiemodel](./media/csp/csp_peered_deployment_model.png)

Dit implementatiemodel biedt een scheiding van bevoegdheden en stelt de helpdeskmedewerkers van de CSP-partner in staat om het Azure-abonnement te beheren en resources daarin te implementeren en te beheren. De helpdeskmedewerkers van de CSP-partner hoeven echter geen algemene beheerdersbevoegdheden te hebben in de Azure AD-map van de klant. De identiteitsbeheerders van de klant kunnen identiteiten voor hun organisatie blijven beheren.

Dit implementatiemodel kan geschikt zijn voor scenario's waarin een ISV (onafhankelijke softwareleverancier) een gehoste versie van hun on-premises toepassing biedt, die ook verbinding moet maken met het AD van de klant.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Beheer van beheer van beheerde azure AD Domain Services-domeinen in CSP-abonnementen
De volgende belangrijke overwegingen zijn van toepassing bij het beheren van een beheerd domein in een Azure CSP-abonnement:

* **CSP-beheerders kunnen een beheerd domein inrichten met hun referenties:** Azure AD Domain Services ondersteunt Azure CSP-abonnementen. Daarom kunnen gebruikers die deel uitmaken van de beheeragentsgroep van een CSP-partner een nieuw beheerd Azure AD Domain Services-domein inrichten.

* **CSP's kunnen het maken van nieuwe beheerde domeinen voor hun klanten scripten met PowerShell:** Zie [hoe u Azure AD Domain Services inschakelt met PowerShell](powershell-create-instance.md) voor meer informatie.

* **CSP-beheerders kunnen geen doorlopende beheertaken uitvoeren op het beheerde domein met behulp van hun referenties:** Gebruikers van CSP-beheerders kunnen geen routinematige beheertaken binnen het beheerde domein uitvoeren met behulp van hun referenties. Deze gebruikers zijn extern aan de Azure AD-map van de klant en hun referenties zijn niet beschikbaar in de Azure AD-map van de klant. Azure AD Domain Services heeft daarom geen toegang tot de wachtwoordhashes van Kerberos en NTLM voor deze gebruikers. Als gevolg hiervan kunnen dergelijke gebruikers niet worden geverifieerd op beheerde azure AD Domain Services-domeinen.

  > [!WARNING]
  > **U moet een gebruikersaccount maken in de map van de klant om doorlopende beheertaken op het beheerde domein uit te voeren.**
  > U zich niet aanmelden bij het beheerde domein met behulp van de referenties van een CSP-beheerder. Gebruik hiervoor de referenties van een gebruikersaccount dat behoort tot de Azure AD-map van de klant. U hebt deze referenties nodig voor taken zoals het samenvoegen van virtuele machines met het beheerde domein, het beheren van DNS, het beheren van groepsbeleid enz.
  >

* **Het gebruikersaccount dat is gemaakt voor doorlopend beheer moet worden toegevoegd aan de groep 'AAD DC Administrators':** De groep 'AAD DC-beheerders' heeft bevoegdheden om bepaalde gedelegeerde beheertaken op het beheerde domein uit te voeren. Deze taken omvatten het configureren van DNS, het maken van organisatie-eenheden, het beheren van groepsbeleid enz. Als een CSP-partner dergelijke taken op een beheerd domein kan uitvoeren, moet een gebruikersaccount worden gemaakt in de Azure AD-map van de klant. De referenties voor dit account moeten worden gedeeld met de beheerdersagents van de CSP-partner. Dit gebruikersaccount moet ook worden toegevoegd aan de groep 'AAD DC Administrators' om configuratietaken op het beheerde domein mogelijk te maken die met dit gebruikersaccount moeten worden uitgevoerd.


## <a name="next-steps"></a>Volgende stappen
* [Schrijf je in voor het Azure CSP-programma](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) en begin met het maken van bedrijven via Azure CSP.
* Bekijk de lijst met [Azure-services die beschikbaar zijn in Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Azure AD Domain Services inschakelen met behulp van PowerShell](powershell-create-instance.md)
* [Aan de slag met Azure AD Domain Services](tutorial-create-instance.md)
