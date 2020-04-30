---
title: Vereisten per vermeldings type | Azure
description: In dit artikel worden de criteria voor geschiktheid en de publicatie vereisten beschreven die willen weten hoe u apps publiceert op de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: dsindona
ms.openlocfilehash: c9936e9c406e262c06d9016b88f8999e46dcb917
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684358"
---
# <a name="requirements-by-listing-type"></a>Vereisten per type vermelding  
De technische en marketing vereisten zijn afhankelijk van de winkel, het aanbiedings type en het vermeldings type. Bekijk de volgende specificaties om uw naleving te controleren.  
1. Vereisten voor de winkel:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Vermeldings type en vereisten voor het aanbiedings type:  
    *   Ga voor meer informatie over de aanbiedings typen en aanbiedings typen naar het type vermelding voor uw oplossings pagina op [docs.Microsoft.com/azure/Marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Vereisten voor de winkel: AppSource  
In de volgende tabel worden de vereisten voor het publiceren op AppSource beschreven.  

| Vereiste | Details | Vereist of aanbevolen |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Uw app moet Azure Active Directory federatieve eenmalige aanmelding (Azure AD Federated SSO) toestaan waarvoor toestemming is ingeschakeld.<ul> <li>Ga voor meer informatie over het inschakelen van Azure AD Federated SSO naar het configureren van eenmalige aanmelding voor toepassingen die zich niet op de pagina Azure Active Directory toepassings galerie bevinden op [docs.Microsoft.com/azure/Active-Directory/Active-Directory-SaaS-Custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Vereist |   
| ***Integratie met Microsoft Cloud Services*** | Uw app moet worden geïntegreerd met andere Microsoft Cloud-Services, zoals micro soft Power BI, Cortana Intelligence of Microsoft Azure Services.<ul> <li>Een voor beeld van een Microsoft Cloud-service is Internet of Things.</li> </ul> | Aanbevolen |  
| ***Doelgroep*** | Uw app moet voor line-of-Business-gebruikers en zakelijke eigen aren zijn. | Vereist | 
| ***SaaS-app (Software as a Service) voor bedrijven*** | Uw app moet aan de volgende vereisten voldoen.<ul> <li>Een line-of-Business SaaS-app</li> <li>Bedrijfs processen gericht</li> <li>Gericht op zakelijke klanten</li> <li>Gebruikers in staat stellen hun werk referenties te gebruiken om zich aan te melden, zoals gebruikers naam en wacht woord</li> </ul> | Vereist |  
| ***Gratis proef periode en proef ervaring*** | Uw app moet een van de volgende opties bevatten om ervoor te zorgen dat een klant uw app gedurende een beperkte tijd gratis kan gebruiken.<ul> <li>Geef een `try` methode op, zodat klanten een proef versie van uw app kunnen starten binnen AppSource</li> <li>Geef een `request trial` optie op in het AppSource. klanten kunnen daarom een proef versie van uw app aanvragen</li> </ul>De gratis proef versie die u opgeeft, moet de klant een vooraf ingestelde hoeveelheid tijd bieden om uw app zonder extra kosten te proberen. | Vereist |  
| ***Eenvoudig Configureer bare, kant-en-klare oplossing*** | Uw app moet eenvoudig zijn en snel worden geconfigureerd en ingesteld zonder aanpassing vereist. | Vereist |  
| ***Leadbeheer*** | Zorg ervoor dat uw CRM lead gegevens accepteert voordat u leads van de winkel ontvangt.<ul> <li>Voor beelden van CRMs zijn Marketo, micro soft Dynamics of Sales Force</li> </ul> | Vereist |  
| ***Privacybeleid en gebruiks voorwaarden*** | Uw app moet een koppeling naar de pagina met uw privacybeleid opgeven met behulp van een open bare URL. De gebruiks voorwaarden moeten worden vermeld tijdens het publiceren als tekst. | Vereist |  
| ***Ondersteuning*** | Uw app moet een koppeling naar de ondersteunings pagina van uw klant bieden met behulp van een open bare URL. Als uw app een proef versie is, moet u tijdens de proef periode zonder extra kosten ondersteunen. | Vereist |  

## <a name="storefront-requirements-azure-marketplace"></a>Vereisten voor de winkel: Azure Marketplace  
Hier volgen de vereisten voor het typen van vermeldingen in azure Marketplace.  

| Vereiste | Details | Type vermelding |  
|:--- |:--- |:--- |  
| ***Deelname beleid*** | Uw app moet voldoen aan het deelname beleid voor Azure Marketplace.<ul> <li>Ga voor meer informatie over het deelname beleid naar de pagina deelname beleid voor Azure Marketplace op [Azure.Microsoft.com/support/legal/Marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />trans<br />trial |  
| ***Integratie met micro soft*** | Uw aanbieding moet Microsoft Azure service typen, zoals compute, Networking of Storage, gebruiken of uitbreiden. Uw aanbieding moet worden uitgelijnd op een bestaande Azure Marketplace-categorie, zoals data bases, beveiliging of netwerken.<ul> <li>Ga naar de pagina Marketplace-apps op [azuremarketplace.Microsoft.com/Marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps)voor meer informatie over Marketplace-aanbiedingen.</li> </ul> | list<br />trans<br />trial |  
| ***Doelgroep*** | Uw aanbieding moet voor IT-professionals, Cloud ontwikkelaars of andere technische klant rollen zijn. | list<br />trans<br />trial |  
| ***Leadbeheer*** | Schakel uw CRM (Marketo, micro soft Dynamics of Sales Force) in om lead gegevens te accepteren voordat u leads van de winkel ontvangt. | list<br />trans<br />trial |  
| ***Privacybeleid en gebruiks voorwaarden*** | Uw app moet een koppeling naar de pagina met uw privacybeleid opgeven met behulp van een open bare URL. De gebruiks voorwaarden moeten worden vermeld tijdens het publiceren als tekst. | list<br />trans<br />trial |  
| ***Ondersteuning*** | Uw aanbieding moet een koppeling naar de ondersteunings pagina van de klant bieden met behulp van een open bare URL. Als uw aanbieding een proef versie is, moet u tijdens de proef periode zonder extra kosten ondersteunen. | trans<br />trial |    

## <a name="non-transact-listings"></a>Niet-Transact-vermeldingen  
In deze sectie worden alle aanbiedings typen beschreven die niet gebruikmaken van het type van de Transact-vermelding. 

### <a name="list"></a>Lijst  
Het lijst vermeldings type bevat de volgende aanbiedings typen op de winkel in de Marketplace.  

| Type aanbieding | Webwinkel | Details |  
|:---        |:---        |:---     |  
| Adviesservices | AppSource | Vereisten: AppSource: List: Consulting Services |  
| Adviesservices | Azure Marketplace | Vereisten: Azure Marketplace: lijst: advies Services |  
| Contact opnemen | AppSource | [](#) |  
| Contact opnemen | Azure Marketplace | Vereisten: AppSource: List: contact met mij opnemen |  

#### <a name="requirements-appsource-list-consulting-service"></a>Vereisten: AppSource: List: Consulting Service  

| Vereisten | Details |  
|:--- |:--- |  
| Kenmerken van service aanbod | Uw advies service moet voldoen aan de volgende criteria.<ul> <li>Een vaste prijs-of vrije duur-afspraak (of gratis) voor vaste prijzen bieden.</li> <li>In de eerste plaats voor verkoop.</li> <li>Maxi maal één klant.</li> <li>Uitvoeren op site.</li> </ul> |  
| Partner vereisten voor advies Services | U voldoet aan de criteria in het relevante gebied voor uw service.<table><tr><th>Oplossings gebied</th><th>Criteria</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Beschikken over een competentie van Silver of Gold Customer Relationship Management.</td></tr><tr><td>Dynamics 365 voor Financiën en bewerkingen, Enter prise Edition</td><td>De vaardig heden en omzet van uw Cloud bewerkingen met Silver of Gold Enter prise resource planning in de laatste 12 maanden van $25.000 of meer.</td></tr><tr><td>Dynamics 365 voor Financiën en operationele activiteiten, Business Edition</td><td>Fungeren als Cloud Services provider (CSP) of digitale partner of record (DPOR) voor een of meer klanten.</td></tr><tr><td>Power BI</td><td>Voldoen aan de partner criteria van de oplossing.</td></tr><tr><td>PowerApps</td><td>Een partner show case-oplossing hebben.</td></tr></table><ul> <li>Ga voor meer informatie over Customer Relationship Management naar de pagina Cloud Customer Relationship Management bevindt zich op [partner.Microsoft.com/Membership/Cloud-Customer-Relationship-Management-Competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency).</li> <li>Ga voor meer informatie over resource planning naar de pagina Enter prise resource planning op [partner.Microsoft.com/Membership/Enterprise-resource-planning-Competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Ga naar de Cloud Services provider pagina op [partner.Microsoft.com/Cloud-Solution-Provider](https://partner.microsoft.com/cloud-solution-provider)voor meer informatie over CSP.</li> <li>Ga voor meer informatie over DPOR naar de pagina digitale partner van record en partner Association op [partner.Microsoft.com/Membership/Digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record).</li> <li>Voor meer informatie over Solution Partner criteria gaat u naar het overzicht van de oplossings partner en [https://partner.microsoft.com/en-us/membership/partner-incentives](https://partner.microsoft.com/en-us/membership/partner-incentives)het document prikkels op.</li> <li>Voor meer informatie over partner show case gaat u naar de pagina met de partner show case op [powerapps.Microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase).</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Vereisten: Azure Marketplace: lijst: Consulting Service  

| Vereisten | Details |  
|:--- |:--- |  
| Kenmerken van service aanbod | Uw advies service moet voldoen aan de volgende criteria.<ul> <li>Een vaste prijs-of vrije duur-afspraak (of gratis) voor vaste prijzen bieden.</li> <li>In de eerste plaats voor verkoop.</li> <li>Maxi maal één klant.</li> <li>Uitvoeren op site.</li> </ul> |  
| Partner vereisten voor advies Services | U moet zilver of goud hebben in een van de volgende competenties in het relevante gebied voor uw service. <table><tr><th>Oplossings gebied</th><th>Competentie</th></tr><td>Cloud platform en-infra structuur</td><td>Cloud platform<br />Datacentrum</td><tr><td>Toepassings ontwikkeling en ISV</td><td>Ontwikkeling van toepassingen<br />Integratie van toepassingen<br />DevOps</td></tr><tr><td>Gegevensbeheer en analyse</td><td>Data Analytics<br />Gegevens platform</td></tr></table><ul> <li>Ga voor meer informatie over competenties naar de pagina competenties via Microsoft Partner Network op [partner.Microsoft.com/Membership/competencies](https://partner.microsoft.com/membership/competencies).</li> <li>Ga naar de Azure Marketplace Consulting Services-pagina op [docs.Microsoft.com/azure/Marketplace/Consulting-Services](https://docs.microsoft.com/azure/marketplace/consulting-services)voor meer informatie over de aanbieding.</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Proefversie  

| Type aanbieding | Webwinkel | Details |  
|:---        |:---        |:---     |  
| Gratis/SaaS-proef versie | AppSource | Vereisten voor type vermelding: proef versie |  
| Gratis/SaaS-proef versie | Azure Marketplace | Vereisten: Azure Marketplace: proef versie: gratis proef versie/SaaS-proef versie |  
| Interactieve demo | AppSource | Vereisten voor type vermelding: proef versie |  
| Interactieve demo | Azure Marketplace | [Vereisten: Azure Marketplace: proef versie: interactieve demo](#requirements-azure-marketplace-trial-interactive-demo) |  
| Station testen | AppSource | Vereisten voor type vermelding: proef versie |  
| Station testen | Azure Marketplace | [Vereisten: Azure Marketplace: proef versie: test drive](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Vereisten: Azure Marketplace: proef versie  

| Vereiste | Details |  
|:--- |:--- |  
| Gratis proef periode en proef ervaring | Uw klant kan uw app gedurende een beperkte periode gratis gebruiken.<br /><br />Uw klant hoeft geen licentie of abonnements kosten te betalen voor uw aanbieding of app. Uw klant hoeft niet te betalen voor het onderliggende product of de service van micro soft van derden. Alle opties voor de proef versie worden geïmplementeerd in uw Azure-abonnement. U hebt uitsluitend controle over de kosten optimalisatie en het beheer.<br /><br />U kunt een gratis proef versie, een interactieve demo of een test drive kiezen. Wat u ook kiest, de gratis proef versie moet de klant vooraf ingestelde tijds duur bieden om de app zonder extra kosten te proberen.<ul> <li>Als u het proces van het maken van een test drive wilt starten, [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com)stuurt u een e-mail naar.</li> </ul>Opmerking: Azure Marketplace SaaS-proef ervaringen moeten klanten kunnen gebruiken om zich aan te melden.<ul> <li>Ga voor meer informatie naar de sectie AppSource trial experiences (Engelstalig) op [docs.Microsoft.com/azure/Active-Directory/develop/Active-Directory-devhowto-appsource-Certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences).</li> </ul> |  
| Eenvoudig Configureer bare, kant-en-klare oplossing | Uw app moet eenvoudig zijn en snel worden geconfigureerd en ingesteld. |  
| Beschik baarheid/uptime | Uw SaaS-app of-platform moet een uptime van ten minste 99,9% hebben. |  
| Azure Active Directory | Uw aanbieding moet Azure Active Directory (Azure AD) federatieve eenmalige aanmelding (SSO) (eenmalige aanmelding) (Azure AD Federated SSO) toestaan waarvoor toestemming is ingeschakeld. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Vereisten: Azure Marketplace: proef versie: gratis proef versie/SaaS-proef versie  

| Voordeel | Vereiste |  
|:--- |:--- |  
| Hiermee kan een klant uw product uitproberen voordat het wordt gekocht met een geautomatiseerde methode om te converteren naar betaald gebruik. Maakt ook het concept van de klant en de gemeen schappelijke betrokkenheid met micro soft-verkoop teams mogelijk. | Uw oplossing is een virtuele machine of oplossings sjabloon.<br /><br />Uw oplossing is een SaaS-aanbieding en u kunt een multi tenant-SaaS-product aanbieden.<br /><br />U hebt een eerste ervaring om snel aan de slag te gaan met het uitvoeren van een klant.<br /><br />U hebt één Tenant maar voegt klanten toe als gast gebruikers. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Vereisten: Azure Marketplace: proef versie: interactieve demo  

| Voordeel | Vereiste |  
|:--- |:--- |  
| Hiermee kunnen uw klanten uw oplossing in actie zien zonder de complexiteit van de instelling. | Uw oplossing vereist complexe instellingen die in de proef periode lastig zijn. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Vereisten: Azure Marketplace: proef versie: test drive  

| Voordeel | Vereiste |  
|:--- |:--- |  
| Hiermee kan een klant het product proberen voordat ze worden gekocht.<br /><br />Biedt een begeleide ervaring voor uw oplossing met behulp van vooraf geconfigureerde instellingen.<br /><br />Hieronder vindt u meer voor delen van het gebruik van een test drive.<ul> <li>27% van de zoek acties van gebruikers op de Marketplace worden verfijnd door gebruikers om aanbiedingen met een test station weer te geven.</li> <li>Aanbiedingen met test stations genereren 38% meer leads dan aanbiedingen zonder.</li> <li>36% van de nieuwe klant aankopen op Marketplace zijn afkomstig van klanten die een test drive hebben genomen.</li> <li>Met test drives kunnen micro soft-veld verkopers uw product beter begrijpen voor het verkopen van mede werkers.</li> </ul> | Uw oplossing is een virtuele machine, oplossings sjabloon of SaaS-app met één Tenant of is gecompliceerd om in te richten. <br /><br />U hebt geen methode om uw proef abonnement om te zetten naar een betaalde aanbieding. |  

---

## <a name="transact-specific-listings"></a>Specifieke vermeldingen voor de Transact

### <a name="transact"></a>Trans  

| Type aanbieding | Webwinkel | Details |   
|:---        |:---        | :--- |  
| Azure-apps: beheerde app | Azure Marketplace | Vereisten: Azure Marketplace: Transact: Azure apps: beheerde app |  
| Azure-apps: oplossings sjabloon | Azure Marketplace | Vereisten: Azure Marketplace: Transact: Azure apps: oplossings sjabloon |  
| Containers | Azure Marketplace | [Vereisten: Azure Marketplace: Transact: container](#requirements-azure-marketplace-transact-container) |  
| SaaS-app  | Azure Marketplace | [Vereisten: Azure Marketplace: Transact: SaaS-app](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuele machine | Azure Marketplace | [Vereisten: Azure Marketplace: Transact: virtual machine](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Vereisten: Azure Marketplace: Transact: container  

| Vereiste | Details |  
|:--- |:--- |  
| Facturering en meting | Ondersteuning voor het gratis of BYOL facturerings model. |  
| Op docker gebaseerde installatie kopie | De container installatie kopie moet worden gebaseerd op de indeling van de docker-installatie kopie en moet worden opgehaald uit Azure-container registers. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Vereisten: Azure Marketplace: Transact: SaaS-app  

| Vereiste | Details |  
|:--- |:--- |  
| Facturering en meting | Voor uw aanbieding geldt een maandelijks vast bedrag. Op gebruik gebaseerde prijs-en op gebruik gebaseerde, op dit moment ingestelde opties voor het op basis van *een werkelijke waarde* worden niet ondersteund. |  
| Opzegging | Uw aanbieding wordt op elk gewenst moment geannuleerd door de klant. |  
| Pagina transactie overloop | Host een landings pagina van een Azure co-branding-trans actie. Uw landings pagina stelt uw klanten in staat om uw SaaS-service account te maken en te beheren. |  
| API voor SaaS-abonnementen | Geef een service op die samenwerkt met het SaaS-abonnement om een gebruikers account en een service plan te maken, bij te werken en te verwijderen. Alle kritieke API-wijzigingen moeten binnen 24 uur worden ondersteund. Eventuele wijzigingen in de niet-kritieke API worden regel matig bijgewerkt. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Vereisten: Azure Marketplace: Transact: virtual machine  

| Vereiste | Details |  
|:--- |:--- | 
| Facturering en meting | Uw virtuele machine moet de facturering per maand BYOL of betalen naar gebruik ondersteunen. |  
| Azure-compatibele virtuele harde schijf (VHD) | Vm's moeten zijn gebouwd op Windows of Linux.<ul> <li>Zie [Linux-distributies die zijn goedgekeurd op Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)voor meer informatie over het maken van een virtuele Linux-harde schijf.</li> <li>Zie [een virtuele harde schijf maken die compatibel is met Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)voor meer informatie over het maken van een Windows VHD.</li> </ul> |  

## <a name="next-steps"></a>Volgende stappen
*   Ga naar de pagina [met Publisher-hand leiding voor Azure Marketplace en AppSource](./marketplace-publishers-guide.md) .  

