---
title: Veelgestelde vragen over Azure API-beheer | Microsoft Documenten
description: Lees de antwoorden op veelgestelde vragen (FAQ), patronen en aanbevolen procedures in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: c32fdc67c74e100e0e31dad3afde128c05c356d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335974"
---
# <a name="azure-api-management-faqs"></a>Veelgestelde vragen over Azure API-beheer
Krijg de antwoorden op veelgestelde vragen, patronen en aanbevolen procedures voor Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Contact opnemen
* [Hoe kan ik het Microsoft Azure API Management-team een vraag stellen?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
* [Wat betekent het als een functie in preview is?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Hoe kan ik de verbinding tussen de API Management-gateway en mijn back-endservices beveiligen?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [How do I copy my API Management service instance to a new instance?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance) (Hoe kopieer ik mijn exemplaar van de API Management service naar een nieuw exemplaar?)
* [Kan ik mijn API Management-instantie programmatisch beheren?](#can-i-manage-my-api-management-instance-programmatically)
* [Hoe voeg ik een gebruiker toe aan de groep Beheerders?](#how-do-i-add-a-user-to-the-administrators-group)
* [Waarom is het beleid dat ik wil toevoegen niet beschikbaar in de beleidseditor?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Hoe stel ik meerdere omgevingen in één API in?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Kan ik SOAP gebruiken met API Management?](#can-i-use-soap-with-api-management)
* [Kan ik een OAuth 2.0-autorisatieserver configureren met AD FS-beveiliging?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Welke routeringsmethode gebruikt API Management in implementaties naar meerdere geografische locaties?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Kan ik een Azure Resource Manager-sjabloon gebruiken om een API Management-serviceinstantie te maken?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Kan ik een zelfondertekend TLS/SSL-certificaat gebruiken voor een back-end?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Waarom krijg ik een verificatiefout wanneer ik een GIT-repository probeer te klonen?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Werkt API Management met Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Waarom hebben we een speciaal subnet nodig in VNET's in Resource Manager-stijl wanneer API-beheer erin wordt geïmplementeerd?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Wat is de minimale subnetgrootte die nodig is bij het implementeren van API Management in een VNET?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Can I move an API Management service from one subscription to another?](#can-i-move-an-api-management-service-from-one-subscription-to-another) (Kan ik een API Management-service overzetten naar een ander abonnement?)
* [Zijn er beperkingen op of bekende problemen met het importeren van mijn API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Hoe kan ik het Microsoft Azure API Management-team een vraag stellen?
U contact met ons opnemen via een van de volgende opties:

* Plaats uw vragen in ons [API Management MSDN forum.](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)
* Stuur een e-mail naar <mailto:apimgmt@microsoft.com>.
* Stuur ons een functieaanvraag in het [Azure-feedbackforum.](https://feedback.azure.com/forums/248703-api-management)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Wat betekent het als een functie in preview is?
Wanneer een functie in preview is, betekent dit dat we actief op zoek zijn naar feedback over hoe de functie voor u werkt. Een functie in preview is functioneel compleet, maar het is mogelijk dat we een laatste wijziging aanbrengen in reactie op feedback van klanten. We raden u aan niet afhankelijk te zijn van een functie die in uw productieomgeving is opgenomen. Als u feedback hebt over preview-functies, laat het ons dan weten via een van de contactopties in [Hoe kan ik het Microsoft Azure API Management-team een vraag stellen?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Hoe kan ik de verbinding tussen de API Management-gateway en mijn back-endservices beveiligen?
U hebt verschillende opties om de verbinding tussen de API Management-gateway en uw back-endservices te beveiligen. U kunt:

* Gebruik HTTP-basisverificatie. Zie [Uw eerste API importeren en publiceren](import-and-publish.md)voor meer informatie.
* Gebruik wederzijdse verificatie van TLS zoals beschreven in [Hoe back-endservices te beveiligen met behulp van clientcertificaatverificatie in Azure API Management.](api-management-howto-mutual-certificates.md)
* Gebruik IP-whitelisting op uw back-endservice. In alle niveaus van API-beheer, met uitzondering van de verbruikslaag, blijft het IP-adres van de gateway constant, met enkele kanttekeningen beschreven in [het IP-documentatieartikel.](api-management-howto-ip-addresses.md)
* Verbind uw API-beheerexemplaar met een Azure Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>How do I copy my API Management service instance to a new instance? (Hoe kopieer ik mijn exemplaar van de API Management service naar een nieuw exemplaar?)
U hebt verschillende opties als u een API-beheerexemplaar naar een nieuw exemplaar wilt kopiëren. U kunt:

* Gebruik de back-up- en herstelfunctie in API-beheer. Zie [Herstel na noodgevallen implementeren door serviceback-up en herstel te gebruiken in Azure API Management](api-management-howto-disaster-recovery-backup-restore.md)voor meer informatie.
* Maak uw eigen back-up- en herstelfunctie met behulp van de [API Management REST API.](/rest/api/apimanagement/) Gebruik de REST-API om de entiteiten op te slaan en te herstellen van de gewenste serviceinstantie.
* Download de serviceconfiguratie met Git en upload deze naar een nieuw exemplaar. Zie [Uw API Management-serviceconfiguratie opslaan en configureren met Git](api-management-configuration-repository-git.md)voor meer informatie.

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Kan ik mijn API Management-instantie programmatisch beheren?
Ja, u API Management programmatisch beheren met behulp van:

* De [API Management REST API](/rest/api/apimanagement/).
* De [Microsoft Azure ApiManagement Service Management Library SDK](https://aka.ms/apimsdk).
* De PowerShell-cmdlets [service-implementatie](https://docs.microsoft.com/powershell/module/wds) en [servicebeheer.](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Hoe voeg ik een gebruiker toe aan de groep Beheerders?
U als u een gebruiker toevoegen aan de groep Administrators:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga naar de resourcegroep met de API-beheerinstantie die u wilt bijwerken.
3. Wijs in API-beheer de rol **api managementservicebijdrager toe** aan de gebruiker.

Nu kan de nieuw toegevoegde bijdrager Azure [PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure/overview)gebruiken. U zich als beheerder als beheerder aanmelden:

1. Gebruik `Connect-AzAccount` de cmdlet om in te loggen.
2. Stel de context in op het `Set-AzContext -SubscriptionID <subscriptionGUID>`abonnement waarop de service wordt gebruikt .
3. Download één url met een `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`aanmelding via .
4. Gebruik de URL om toegang te krijgen tot de beheerdersportal.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Waarom is het beleid dat ik wil toevoegen niet beschikbaar in de beleidseditor?
Als het beleid dat u wilt toevoegen, wordt gedimd of gearceerd weergegeven in de beleidseditor, moet u ervoor zorgen dat u zich in het juiste bereik voor het beleid bevindt. Elke beleidsverklaring is ontworpen voor u om te gebruiken in specifieke scopes en beleidssecties. Zie de sectie Gebruik van het beleid in [API-beheerbeleid](/azure/api-management/api-management-policies)om de beleidsonderdelen en -scopes voor een beleid te bekijken.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Hoe stel ik meerdere omgevingen in één API in?
Als u meerdere omgevingen wilt instellen, bijvoorbeeld een testomgeving en een productieomgeving, hebt u in één API twee opties. U kunt:

* Host verschillende API's op dezelfde tenant.
* Host dezelfde API's op verschillende tenants.

### <a name="can-i-use-soap-with-api-management"></a>Kan ik SOAP gebruiken met API Management?
[SOAP pass-through](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) ondersteuning is nu beschikbaar. Beheerders kunnen de WSDL van hun SOAP-service importeren en Azure API Management maakt een SOAP-front-end. Documentatie van ontwikkelaarsportalen, testconsole, beleidsregels en analyses zijn allemaal beschikbaar voor SOAP-services.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Kan ik een OAuth 2.0-autorisatieserver configureren met AD FS-beveiliging?
Zie ADFS gebruiken [in API-beheer](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/)voor meer informatie over het configureren van een OAuth 2.0-autorisatieserver met AD FS-beveiliging (Active Directory Federation Services).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Welke routeringsmethode gebruikt API Management in implementaties naar meerdere geografische locaties?
API Management gebruikt de [methode voor het routeren van prestatieverkeer](../traffic-manager/traffic-manager-routing-methods.md#performance) in implementaties naar meerdere geografische locaties. Binnenkomend verkeer wordt doorgestuurd naar de dichtstbijzijnde API-gateway. Als één regio offline gaat, wordt binnenkomend verkeer automatisch doorgestuurd naar de dichtstbijzijnde gateway. Meer informatie over routeringsmethoden in [routeringsmethoden voor verkeersbeheer](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Kan ik een Azure Resource Manager-sjabloon gebruiken om een API Management-serviceinstantie te maken?
Ja. Zie de [quickstartsjablonen van azure API Management Service.](https://aka.ms/apimtemplate)

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Kan ik een zelfondertekend TLS/SSL-certificaat gebruiken voor een back-end?
Ja. Dit kan via PowerShell of door rechtstreeks in te dienen bij de API. Hierdoor wordt de validatie van de certificaatketen uitgeschakeld en u zelfondertekende of privéondertekende certificaten gebruiken bij het communiceren van API-beheer naar de back-endservices.

#### <a name="powershell-method"></a>Powershell-methode ####
Gebruik [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) de PowerShell-cmdlets (voor nieuwe back-end) of [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) `-SkipCertificateChainValidation` (voor `True`bestaande back-end) PowerShell-cmdlets en stel de parameter in op .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Methode voor directe API-update ####
1. Maak een [backend-entiteit](/rest/api/apimanagement/) met API-beheer.     
2. Stel de eigenschap **skipCertificateChainValidation** in **op true**.     
3. Als u niet langer zelfondertekende certificaten wilt toestaan, verwijdert u de entiteit Backend of stelt u de eigenschap **skipCertificateChainValidation** in op **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Waarom krijg ik een verificatiefout wanneer ik probeer om een Git-opslagplaats te klonen?
Als u Git Credential Manager gebruikt of als u een Git-repository probeert te klonen met Visual Studio, loopt u mogelijk een bekend probleem tegen het dialoogvenster Windows-referenties aan. Het dialoogvenster beperkt de wachtwoordlengte tot 127 tekens en het wordt het door Microsoft gegenereerde wachtwoord afgekapt. We werken aan het verkorten van het wachtwoord. Gebruik git Bash voor nu om je Git repository te klonen.

### <a name="does-api-management-work-with-azure-expressroute"></a>Werkt API Management met Azure ExpressRoute?
Ja. API Management werkt met Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Waarom hebben we een speciaal subnet nodig in VNET's in Resource Manager-stijl wanneer API-beheer erin wordt geïmplementeerd?
De speciale subnet vereiste voor API Management komt uit het feit, dat het is gebouwd op Classic (PAAS V1 laag) implementatie model. Hoewel we kunnen implementeren in een Resource Manager VNET (V2-laag), zijn daar consequenties aan verbonden. Het klassieke implementatiemodel in Azure is niet nauw gekoppeld aan het Resource Manager-model en dus als u een resource in V2-laag maakt, weet de V1-laag er niets van en kunnen er problemen optreden, zoals API-beheer dat een IP probeert te gebruiken dat al is toegewezen aan een NIC (gebouwd op V2).
Voor meer informatie over het verschil tussen Klassieke en Resource Manager-modellen in Azure verwijst u naar [het verschil in implementatiemodellen.](../azure-resource-manager/management/deployment-models.md)

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Wat is de minimale subnetgrootte die nodig is bij het implementeren van API Management in een VNET?
De minimale subnetgrootte die nodig is om API-beheer te implementeren is [/29](../virtual-network/virtual-networks-faq.md#configuration), wat de minimale subnetgrootte is die Azure ondersteunt.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Can I move an API Management service from one subscription to another? (Kan ik een API Management-service overzetten naar een ander abonnement?)
Ja. Zie [Resources verplaatsen naar een nieuwe resourcegroep of -abonnement voor](../azure-resource-manager/management/move-resource-group-and-subscription.md)meer informatie.

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Zijn er beperkingen op of bekende problemen met het importeren van mijn API?
[Bekende problemen en beperkingen](api-management-api-import-restrictions.md) voor Open API(Swagger), WSDL en WADL formaten.
