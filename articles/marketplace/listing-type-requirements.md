---
title: Vereisten per aanbiedingstype | Azure
description: In dit artikel worden de geschiktheidscriteria en publicatievereisten beschreven die partners proberen te begrijpen hoe ze apps naar de Azure Marketplace kunnen publiceren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: dsindona
ms.openlocfilehash: c9936e9c406e262c06d9016b88f8999e46dcb917
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684358"
---
# <a name="requirements-by-listing-type"></a>Vereisten per type vermelding  
De technische en marketingcontentvereisten verschillen per winkel, aanbiedingstype en vermeldingstype. Bekijk de volgende specificaties om uw naleving te controleren.  
1. Vereisten voor winkelpuien:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. Aanbiedingstype en aanbiedingstypevereisten:  
    *   Ga voor meer informatie over aanbiedingstypen en aanbiedingstypen naar de pagina Het aanbiedingstype voor uw oplossing bepalen op [docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md).  

## <a name="storefront-requirements-appsource"></a>Vereisten voor storefront: AppSource  
In de volgende tabel worden de vereiste vereisten voor publicatie op AppSource beschreven.  

| Vereiste | Details | Vereist of aanbevolen |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | Uw app moet Azure Active Directory met eenmalige aanmelding (Azure AD-federatie sso) toestaan met toestemming ingeschakeld.<ul> <li>Ga voor meer informatie over het inschakelen van Azure AD-federatiessso naar de galeriepagina Voor het configureren van eenmalige aanmelding voor toepassingen die zich niet bevinden op de galeriepagina van Azure Active Directory-toepassingen op [docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).</li> </ul> | Vereist |   
| ***Integratie met Microsoft Cloud Services*** | Uw app moet worden geïntegreerd met andere Microsoft Cloud-services, zoals Microsoft Power BI, Cortana Intelligence of Microsoft Azure-services.<ul> <li>Een voorbeeld van een Microsoft Cloud-service is Internet of Things.</li> </ul> | Aanbevolen |  
| ***Doelgroep*** | Uw app moet zijn bedoeld voor zakelijke gebruikers en bedrijfseigenaren. | Vereist | 
| ***Software as a service (SaaS) app voor bedrijven*** | Uw app moet aan de volgende vereisten voldoen.<ul> <li>Een line-of-business SaaS-app</li> <li>Bedrijfsproces gericht</li> <li>Gericht op zakelijke klanten</li> <li>Gebruikers in staat stellen hun werkreferenties te gebruiken om zich aan te melden, zoals gebruikersnaam en wachtwoord</li> </ul> | Vereist |  
| ***Gratis proefperiode en proefervaring*** | Uw app moet een van de volgende opties bevatten zodat een klant uw app tijdelijk gratis kan gebruiken.<ul> <li>Geef `try` een methode op, zodat klanten een proefversie van uw app kunnen starten in AppSource</li> <li>Een `request trial` optie bieden in de AppSource, zodat klanten een proefversie van uw app kunnen aanvragen</li> </ul>De gratis proefperiode die u verstrekt, moet de klant een vooraf ingestelde hoeveelheid tijd bieden om uw app uit te proberen zonder extra kosten. | Vereist |  
| ***Eenvoudig configureerbare, kant-en-klare oplossing*** | Uw app moet eenvoudig en snel te configureren en in te stellen zijn zonder dat u wilt aanpassen. | Vereist |  
| ***Leadbeheer*** | Stel uw CRM in staat om leadgegevens te accepteren voordat u leads van de storefront ontvangt.<ul> <li>Voorbeelden van CRM's zijn Marketo, Microsoft Dynamics of Salesforce</li> </ul> | Vereist |  
| ***Privacybeleid en gebruiksvoorwaarden*** | Uw app moet een koppeling naar uw privacybeleidspagina opgeven met behulp van een openbare URL. Uw gebruiksvoorwaarden moeten worden verstrekt tijdens het publiceren als tekst. | Vereist |  
| ***Ondersteuning*** | Uw app moet een koppeling naar uw pagina met klantenondersteuning bieden met behulp van een openbare URL. Als uw app een proefversie is, moet u tijdens de proefperiode zonder extra kosten ondersteuning bieden. | Vereist |  

## <a name="storefront-requirements-azure-marketplace"></a>Vereisten voor storefront: Azure Marketplace  
De volgende voorwaarden zijn vereiste vereisten voor aanbiedingstypen in Azure Marketplace.  

| Vereiste | Details | Aanbiedingstype |  
|:--- |:--- |:--- |  
| ***Participatiebeleid*** | Uw app moet het azure marketplace-deelnamebeleid volgen.<ul> <li>Ga voor meer informatie over het deelnamebeleid naar de pagina Azure Marketplace-deelnamebeleid op [azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies).</li></ul> | list<br />Transact<br />trial |  
| ***Integratie met Microsoft*** | Uw aanbieding moet Microsoft Azure-servicetypen zoals compute, networking of opslag gebruiken of uitbreiden. Uw aanbieding moet worden afgestemd op een bestaande Azure Marketplace-categorie, zoals databases, beveiliging of netwerken.<ul> <li>Ga voor meer informatie over Marketplace-aanbiedingen naar de pagina Marketplace-apps op [azuremarketplace.microsoft.com/marketplace/apps.](https://azuremarketplace.microsoft.com/marketplace/apps)</li> </ul> | list<br />Transact<br />trial |  
| ***Doelgroep*** | Uw aanbod moet zijn voor IT-professionals, cloudontwikkelaars of andere technische klantrollen. | list<br />Transact<br />trial |  
| ***Leadbeheer*** | Stel uw CRM (Marketo, Microsoft Dynamics of Salesforce) in om leadgegevens te accepteren voordat u leads van de storefront ontvangt. | list<br />Transact<br />trial |  
| ***Privacybeleid en gebruiksvoorwaarden*** | Uw app moet een koppeling naar uw privacybeleidspagina opgeven met behulp van een openbare URL. Uw gebruiksvoorwaarden moeten worden verstrekt tijdens het publiceren als tekst. | list<br />Transact<br />trial |  
| ***Ondersteuning*** | Uw aanbieding moet een link naar uw pagina met klantenondersteuning bieden met behulp van een openbare URL. Als uw aanbieding een proefperiode is, moet u tijdens de proefperiode zonder extra kosten ondersteuning bieden. | Transact<br />trial |    

## <a name="non-transact-listings"></a>Niet-transacties  
In deze sectie worden alle aanbiedingstypen beschreven die geen gebruik maken van het type transact-vermelding. 

### <a name="list"></a>Lijst  
Het type aanbiedingstype lijst bevat de volgende aanbiedingstypen op de winkelpuien in de marktplaats.  

| Type aanbieding | Storefront | Details |  
|:---        |:---        |:---     |  
| Adviesservices | AppSource | Vereisten: AppSource: Lijst: Consulting Services |  
| Adviesservices | Azure Marketplace | Vereisten: Azure Marketplace: Lijst: Consulting Services |  
| Neem contact met mij op | AppSource | [](#) |  
| Neem contact met mij op | Azure Marketplace | Vereisten: AppSource: Lijst: Neem contact met mij op |  

#### <a name="requirements-appsource-list-consulting-service"></a>Vereisten: AppSource: Lijst: Consulting service  

| Vereisten | Details |  
|:--- |:--- |  
| Kenmerken van het serviceaanbod | Uw adviesdienst moet aan de volgende criteria voldoen.<ul> <li>Lever een vaste scope, vaste duur, vaste prijs (of gratis) engagement.</li> <li>Oriënteer voornamelijk voor pre-sales.</li> <li>Beperk tot één klant.</li> <li>Gedrag ter plaatse.</li> </ul> |  
| Partnervereisten voor Consulting Services | U voldoet aan de criteria op het relevante gebied voor uw service.<table><tr><th>Oplossingsgebied</th><th>Criteria</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Hebben Silver of Gold Cloud Customer Relationship Management competentie.</td></tr><tr><td>Dynamics 365 voor Financiën en Operations, Enterprise editie</td><td>Hebben Silver of Gold Enterprise Resource Planning competentie en inkomsten uit uw cloud-activiteiten in de trailing 12 maanden van $ 25.000 of meer.</td></tr><tr><td>Dynamics 365 voor Financiën en Operations, Business edition</td><td>Fungeren als Cloud Services Provider (CSP) of Digital Partner of Record (DPOR) voor een of meer klanten.</td></tr><tr><td>Power BI</td><td>Voldoe aan de criteria van Solution Partner.</td></tr><tr><td>PowerApps</td><td>Heb een Partner Showcase-oplossing.</td></tr></table><ul> <li>Ga voor meer informatie over klantrelatiebeheer naar de pagina Cloud Customer Relationship Management op [partner.microsoft.com/membership/cloud-customer-relationship-management-competency.](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency)</li> <li>Ga voor meer informatie over resourceplanning naar de pagina Ondernemingsresourceplanning op [partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency).</li> <li>Ga voor meer informatie over CSP naar de pagina Cloud Services Provider op [partner.microsoft.com/cloud-solution-provider.](https://partner.microsoft.com/cloud-solution-provider)</li> <li>Ga voor meer informatie over DPOR naar de pagina Digital Partner of Record and Partner Association op [partner.microsoft.com/membership/digital-partner-of-record.](https://partner.microsoft.com/membership/digital-partner-of-record)</li> <li>Ga voor meer informatie over de criteria van solution partner [https://partner.microsoft.com/en-us/membership/partner-incentives](https://partner.microsoft.com/en-us/membership/partner-incentives)naar het oplossingspartneroverzicht en incentives-document op .</li> <li>Ga voor meer informatie over partnershowcase naar de partnershowcasepagina op [powerapps.microsoft.com/partner-showcase.](https://powerapps.microsoft.com/partner-showcase)</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>Vereisten: Azure Marketplace: Lijst: Consulting-service  

| Vereisten | Details |  
|:--- |:--- |  
| Kenmerken van het serviceaanbod | Uw adviesdienst moet aan de volgende criteria voldoen.<ul> <li>Lever een vaste scope, vaste duur, vaste prijs (of gratis) engagement.</li> <li>Oriënteer voornamelijk voor pre-sales.</li> <li>Beperk tot één klant.</li> <li>Gedrag ter plaatse.</li> </ul> |  
| Partnervereisten voor Consulting Services | U moet zilver of goud hebben in een van de volgende competenties op het desbetreffende gebied voor uw service. <table><tr><th>Oplossingsgebied</th><th>Competentie</th></tr><td>Cloudplatform en -infrastructuur</td><td>Cloudplatform<br />Datacentrum</td><tr><td>Applicatieontwikkeling en ISV</td><td>Ontwikkeling van toepassingen<br />Integratie van toepassingen<br />DevOps</td></tr><tr><td>Gegevensbeheer en -analyse</td><td>Data Analytics<br />Dataplatform</td></tr></table><ul> <li>Ga voor meer informatie over competenties naar de pagina Competenties via het Microsoft Partner Netwerk op [partner.microsoft.com/membership/competencies.](https://partner.microsoft.com/membership/competencies)</li> <li>Ga voor meer informatie over de vermelding naar de pagina Azure Marketplace Consulting Services op [docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services).</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>Proefversie  

| Type aanbieding | Storefront | Details |  
|:---        |:---        |:---     |  
| Gratis / SaaS trial | AppSource | Aanbiedingstypevereisten: proef |  
| Gratis / SaaS trial | Azure Marketplace | Vereisten: Azure Marketplace: proefversie: gratis proefversie / SaaS-proefversie |  
| Interactieve demo | AppSource | Aanbiedingstypevereisten: proef |  
| Interactieve demo | Azure Marketplace | [Vereisten: Azure Marketplace: Proefversie: interactieve demo](#requirements-azure-marketplace-trial-interactive-demo) |  
| Proefrit | AppSource | Aanbiedingstypevereisten: proef |  
| Proefrit | Azure Marketplace | [Vereisten: Azure Marketplace: proefversie: proefrit](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>Vereisten: Azure Marketplace: proefversie  

| Vereiste | Details |  
|:--- |:--- |  
| Gratis proefperiode en proefervaring | Uw klant kan uw app tijdelijk gratis gebruiken.<br /><br />Uw klant hoeft geen licentie- of abonnementskosten te betalen voor uw aanbieding of app. Uw klant hoeft niet te betalen voor het onderliggende Microsoft first-party product of -service. Alle testopties worden geïmplementeerd in uw Azure-abonnement. Je hebt alleen de controle over de kostenoptimalisatie en het beheer.<br /><br />U kiezen voor een gratis proefversie, interactieve demo of proefrit. Het maakt niet uit wat u kiest, uw gratis proefperiode moet de klant een vooraf ingestelde hoeveelheid tijd bieden om de app zonder extra kosten te proberen.<ul> <li>Als u wilt beginnen met het maken [amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com)van een testrit, stuurt u een e-mail naar .</li> </ul>Opmerking: Proefversies van Azure Marketplace SaaS moeten klanten in staat stellen werkreferenties te gebruiken om zich aan te melden.<ul> <li>Ga voor meer informatie naar de sectie AppSource-proefervaringen op [docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences)</li> </ul> |  
| Eenvoudig configureerbare, kant-en-klare oplossing | Uw app moet eenvoudig en snel te configureren en in te stellen zijn. |  
| Beschikbaarheid / uptime | Uw SaaS-app of -platform moet een uptime van ten minste 99,9% hebben. |  
| Azure Active Directory | Uw aanbieding moet Azure Active Directory (Azure AD) met eenmalige aanmelding (SSO) (Azure AD-federaties SSO) toestaan met toestemming ingeschakeld. |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>Vereisten: Azure Marketplace: proefversie: gratis proefversie / SaaS-proefversie  

| Voordeel | Vereiste |  
|:--- |:--- |  
| Hiermee kan een klant uw product uitproberen voordat hij koopt met een geautomatiseerde methode voor het converteren naar betaald gebruik. Maakt ook proofs of concept voor de klant en gezamenlijke betrokkenheid met Microsoft sales teams. | Uw oplossing is een virtuele machine of oplossingsjabloon.<br /><br />Uw oplossing is een SaaS-aanbod en u biedt een multitenant SaaS-product aan.<br /><br />Je hebt een first-run ervaring om een klant snel aan de slag te krijgen.<br /><br />U hebt één tenant, maar voegt klanten toe als gastgebruikers. |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>Vereisten: Azure Marketplace: Proefversie: interactieve demo  

| Voordeel | Vereiste |  
|:--- |:--- |  
| Stelt uw klanten in staat om uw oplossing in actie te zien zonder de complexiteit van het instellen. | Uw oplossing vereist complexe instellingen die moeilijk te bereiken zijn binnen de proefperiode. |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>Vereisten: Azure Marketplace: proefversie: proefrit  

| Voordeel | Vereiste |  
|:--- |:--- |  
| Hiermee kan een klant uw product proberen voordat hij of zij koopt.<br /><br />Biedt een begeleide ervaring van uw oplossing met vooraf geconfigureerde instellingen.<br /><br />De volgende zijn extra voordelen bij het gebruik van een proefrit.<ul> <li>27% van de zoekopdrachten van gebruikers op de marktplaats worden door gebruikers verfijnd om alleen aanbiedingen met teststations weer te geven.</li> <li>Aanbiedingen met testritten genereren 38% meer leads dan aanbiedingen zonder.</li> <li>36% van de nieuwe klantacquisities op de markt is afkomstig van klanten die een proefrit maakten.</li> <li>Teststations stellen verkopers in het veld van Microsoft in staat om uw product beter te begrijpen voor co-sell-inspanningen.</li> </ul> | Uw oplossing is een virtuele machine, oplossingssjabloon of SaaS-app met één tenant of is ingewikkeld om in te richten. <br /><br />Je hebt geen methode om je proefperiode om te zetten in een betaalde aanbieding. |  

---

## <a name="transact-specific-listings"></a>Transacties-specifieke aanbiedingen

### <a name="transact"></a>Transact  

| Type aanbieding | Storefront | Details |   
|:---        |:---        | :--- |  
| Azure-apps: beheerde app | Azure Marketplace | Vereisten: Azure Marketplace: Transact: Azure-apps: beheerde app |  
| Azure-apps: sjabloon oplossing | Azure Marketplace | Vereisten: Azure Marketplace: Transact: Azure-apps: sjabloon oplossing |  
| Containers | Azure Marketplace | [Vereisten: Azure Marketplace: Transact: Container](#requirements-azure-marketplace-transact-container) |  
| SaaS-app  | Azure Marketplace | [Vereisten: Azure Marketplace: Transact: SaaS-app](#requirements-azure-marketplace-transact-saas-app) |  
| Virtuele machine | Azure Marketplace | [Vereisten: Azure Marketplace: Transact: virtuele machine](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>Vereisten: Azure Marketplace: Transact: Container  

| Vereiste | Details |  
|:--- |:--- |  
| Facturering en meting | Ondersteuning voor het gratis of BYOL-factureringsmodel. |  
| Afbeelding op basis van docker | Uw containerafbeelding moet gebaseerd zijn op de docker-afbeeldingsindeling en moet worden opgehaald uit Azure Container-registers. |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>Vereisten: Azure Marketplace: Transact: SaaS-app  

| Vereiste | Details |  
|:--- |:--- |  
| Facturering en meting | Uw aanbieding is geprijsd tegen een maandelijks vast tarief. Op gebruik gebaseerde prijzen en op gebruik gebaseerde *true-upopties* worden op dit moment niet ondersteund. |  
| Opzegging | Uw aanbieding kan te koop worden op elk gewenst moment door de klant geannuleerd. |  
| Bestemmingspagina voor transacties | Host een bestemmingspagina voor transacties van Azure voor co-branded transacties. Met uw bestemmingspagina kunnen uw klanten uw SaaS-serviceaccount aanmaken en beheren. |  
| Api voor SaaS-abonnement | Bied een service die samenwerkt met het SaaS-abonnement om een gebruikersaccount en serviceplan te maken, bij te werken en te verwijderen. Alle kritieke API-wijzigingen moeten binnen 24 uur worden ondersteund. Niet-kritieke API-wijzigingen worden periodiek bijgewerkt. |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>Vereisten: Azure Marketplace: Transact: virtuele machine  

| Vereiste | Details |  
|:--- |:--- | 
| Facturering en meting | Uw vm moet byol of pay-as-you-go maandelijkse facturering ondersteunen. |  
| Azure-compatibele virtuele harde schijf (VHD) | VM's moeten worden gebouwd op Windows of Linux.<ul> <li>Zie [Linux-distributies die zijn goedgekeurd op Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)voor meer informatie over het maken van een Linux VHD.</li> <li>Zie Een VHD maken voor meer informatie over het maken van een Windows [VHD.](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)</li> </ul> |  

## <a name="next-steps"></a>Volgende stappen
*   Ga naar de pagina [Azure Marketplace en AppSource Publisher Guide.](./marketplace-publishers-guide.md)  

