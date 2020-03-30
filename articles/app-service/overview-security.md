---
title: Beveiliging
description: Meer informatie over hoe App Service uw app helpt beveiligen en hoe u uw app verder vergrendelen van bedreigingen.
keywords: azure app service, web app, mobile app, api app, function app, security, secure, secured, compliance, compliant, certificate, certificates, https, ftps, tls, trust, encryption, encrypt, encrypted, ip restriction, authentication, authorization, authn, autho, msi, managed service identity, managed identity, secrets, secret, patching, patch, patches, version, isolation, network isolation, ddos, mitm
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: e28935f73511e5ad973929517658cc626b5a6ea2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475371"
---
# <a name="security-in-azure-app-service"></a>Beveiliging in Azure App-service

In dit artikel ziet u hoe [Azure App Service](overview.md) uw web-app, back-end voor mobiele apps, API-app en [functie-app](/azure/azure-functions/)helpt beveiligen. Het laat ook zien hoe u uw app verder beveiligen met de ingebouwde App Service-functies.

De platformonderdelen van App Service, waaronder Azure VM's, opslag, netwerkverbindingen, webframeworks, beheer- en integratiefuncties, worden actief beveiligd en gehard. App Service gaat door middel van krachtige compliance controles op een continue basis om ervoor te zorgen dat:

- Uw app-resources zijn [beveiligd op](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) de Azure-bronnen van de andere klanten.
- [VM-exemplaren en runtime-software worden regelmatig bijgewerkt](overview-patch-os-runtime.md) om nieuw ontdekte kwetsbaarheden op te zoeken. 
- Communicatie van geheimen (zoals verbindingstekenreeksen) tussen uw app en andere [Azure-bronnen](https://azure.microsoft.com/services/sql-database/)(zoals SQL Database) blijft binnen Azure en overschrijdt geen netwerkgrenzen. Geheimen worden altijd versleuteld wanneer ze worden opgeslagen.
- Alle communicatie via de app-service-connectiviteitsfuncties, zoals [hybride verbinding,](app-service-hybrid-connections.md)wordt versleuteld. 
- Verbindingen met hulpprogramma's voor extern beheer zoals Azure PowerShell, Azure CLI, Azure SDKs, REST API's, zijn allemaal versleuteld.
- 24-uurs threat management beschermt de infrastructuur en het platform tegen malware, distributed denial-of-service (DDoS), man-in-the-middle (MITM) en andere bedreigingen.

Zie [Azure Trust Center](https://azure.microsoft.com/overview/trusted-cloud/)voor meer informatie over infrastructuur en platformbeveiliging in Azure.

In de volgende secties ziet u hoe u uw App Service-app verder beschermen tegen bedreigingen.

## <a name="https-and-certificates"></a>HTTPS en certificaten

Met App Service u uw apps beveiligen met [HTTPS.](https://wikipedia.org/wiki/HTTPS) Wanneer uw app wordt gemaakt,\<is de standaarddomeinnaam (app_name>.azurewebsites.net) al toegankelijk via HTTPS. Als u [een aangepast domein voor uw app configureert,](app-service-web-tutorial-custom-domain.md)moet u het ook beveiligen met een [SSL-certificaat,](configure-ssl-bindings.md) zodat clientbrowsers beveiligde HTTPS-verbindingen kunnen maken met uw aangepaste domein. Er zijn verschillende soorten certificaten die worden ondersteund door App Service:

- Gratis beheerd certificaat voor app-service
- App-servicecertificaat
- Certificaat van derden
- Certificaat geïmporteerd uit Azure Key Vault

Zie [Een SSL-certificaat toevoegen in Azure App Service](configure-ssl-certificate.md)voor meer informatie.

## <a name="insecure-protocols-http-tls-10-ftp"></a>Onveilige protocollen (HTTP, TLS 1.0, FTP)

Als u uw app wilt beveiligen tegen alle niet-versleutelde (HTTP)-verbindingen, biedt App Service een configuratie met één klik om HTTPS af te dwingen. Onbeveiligde aanvragen worden afgewezen voordat ze zelfs uw toepassingscode bereiken. Zie [HTTPS afdwingen](configure-ssl-bindings.md#enforce-https)voor meer informatie .

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 wordt niet langer als veilig beschouwd door industriestandaarden, zoals [PCI DSS.](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) Met App Service u verouderde protocollen uitschakelen door [TLS 1.1/1.2 af te dwingen.](configure-ssl-bindings.md#enforce-tls-versions)

App Service ondersteunt zowel FTP als FTPS voor het implementeren van uw bestanden. FtpS moet echter worden gebruikt in plaats van FTP, indien mogelijk. Wanneer een of beide protocollen niet in gebruik zijn, moet u [ze uitschakelen.](deploy-ftp.md#enforce-ftps)

## <a name="static-ip-restrictions"></a>Statische IP-beperkingen

Standaard accepteert uw App Service-app aanvragen van alle IP-adressen van het internet, maar u die toegang beperken tot een kleine subset van IP-adressen. Met App Service op Windows u een lijst met IP-adressen definiëren die toegang hebben tot uw app. De toegestane lijst kan afzonderlijke IP-adressen of een reeks IP-adressen bevatten die zijn gedefinieerd door een subnetmasker. Zie [Statische IP-beperkingen van Azure App Service](app-service-ip-restrictions.md)voor meer informatie .

Voor App Service op Windows u IP-adressen ook dynamisch beperken door de _web.config_te configureren. Zie [Dynamic IP Security \<dynamicIpSecurity>voor ](https://docs.microsoft.com/iis/configuration/system.webServer/security/dynamicIpSecurity/)meer informatie.

## <a name="client-authentication-and-authorization"></a>Clientverificatie en autorisatie

Azure App Service biedt turn-key authenticatie en autorisatie van gebruikers of client apps. Wanneer ingeschakeld, kan het inloggen gebruikers en client apps met weinig of geen applicatiecode. U uw eigen verificatie- en autorisatieoplossing implementeren of App Service toestaan deze voor u af te handelen. De verificatie- en autorisatiemodule verwerkt webaanvragen voordat ze worden overgedragen aan uw toepassingscode en weigert ongeautoriseerde verzoeken voordat ze uw code bereiken.

Verificatie en autorisatie van App Service ondersteunen meerdere verificatieproviders, waaronder Azure Active Directory, Microsoft-accounts, Facebook, Google en Twitter. Raadpleeg [Verificatie en autorisatie in Azure App Service](overview-authentication-authorization.md) voor meer informatie.

## <a name="service-to-service-authentication"></a>Verificatie van service-tot-service

Wanneer u zich afmeldt tegen een back-endservice, biedt App Service twee verschillende mechanismen, afhankelijk van uw behoefte:

- **Service-identiteit** - Meld u aan bij de externe bron met de identiteit van de app zelf. Met App Service u eenvoudig een [beheerde identiteit](overview-managed-identity.md)maken, die u gebruiken om te verifiëren met andere services, zoals [Azure SQL Database](/azure/sql-database/) of Azure Key [Vault.](/azure/key-vault/) Zie [Secure Azure SQL Database-verbinding van App Service met behulp van een beheerde identiteit](app-service-web-tutorial-connect-msi.md)voor een end-to-end zelfstudie van deze aanpak.
- **Namens (OBO)** - Maak gedelegeerde toegang tot externe bronnen namens de gebruiker. Met Azure Active Directory als verificatieprovider kan uw App Service-app gedelegeerde aanmelding uitvoeren bij een externe service, zoals [microsoft graph-api](../active-directory/develop/microsoft-graph-intro.md) of een externe API-app in App Service. Zie [Eindgebruikers end-to-end verifiëren en autoriseren in Azure App Service](app-service-web-tutorial-auth-aad.md)voor een end-to-end zelfstudie van deze aanpak.

## <a name="connectivity-to-remote-resources"></a>Connectiviteit met externe bronnen

Er zijn drie soorten externe bronnen die uw app mogelijk moet openen: 

- [Azure-resources](#azure-resources)
- [Bronnen binnen een Azure Virtual Network](#resources-inside-an-azure-virtual-network)
- [On-premises resources](#on-premises-resources)

In elk van deze gevallen biedt App Service een manier om beveiligde verbindingen te maken, maar u moet nog steeds de beste beveiligingsprocedures in acht nemen. Gebruik bijvoorbeeld altijd versleutelde verbindingen, zelfs als de back-endbron onversleutelde verbindingen toestaat. Zorg er bovendien voor dat uw back-end Azure-service de minimale set IP-adressen toestaat. U de uitgaande IP-adressen voor uw app vinden op [inkomende en uitgaande IP-adressen in Azure App Service.](overview-inbound-outbound-ips.md)

### <a name="azure-resources"></a>Azure-resources

Wanneer uw app verbinding maakt met [Azure-bronnen,](https://azure.microsoft.com/services/sql-database/) zoals SQL Database en [Azure Storage,](/azure/storage/)blijft de verbinding binnen Azure en overschrijdt deze geen netwerkgrenzen. De verbinding verloopt echter via de gedeelde netwerken in Azure, dus zorg er altijd voor dat uw verbinding is versleuteld. 

Als uw app wordt gehost in een [App Service-omgeving,](environment/intro.md)moet u [verbinding maken met ondersteunde Azure-services met behulp van eindpunten van de Service voor virtuele netwerken](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Bronnen binnen een Azure Virtual Network

Uw app heeft toegang tot bronnen in een [Azure Virtual Network](/azure/virtual-network/) via integratie van virtuele [netwerken.](web-sites-integrate-with-vnet.md) De integratie wordt tot stand gebracht met een virtueel netwerk met behulp van een point-to-site VPN. De app heeft vervolgens toegang tot de bronnen in het virtuele netwerk met behulp van hun privé-IP-adressen. De point-to-site-verbinding doorkruist echter nog steeds de gedeelde netwerken in Azure. 

Als u uw bronconnectiviteit volledig wilt isoleren van de gedeelde netwerken in Azure, maakt u uw app in een [App Service-omgeving.](environment/intro.md) Aangezien een App Service-omgeving altijd wordt geïmplementeerd in een speciaal virtueel netwerk, is de verbinding tussen uw app en resources binnen het virtuele netwerk volledig geïsoleerd. Zie [Netwerkisolatie](#network-isolation)voor andere aspecten van netwerkbeveiliging in een App-serviceomgeving.

### <a name="on-premises-resources"></a>On-premises resources

U op drie manieren veilig toegang krijgen tot on-premises bronnen, zoals databases: 

- [Hybride verbindingen](app-service-hybrid-connections.md) - Hiermee wordt een point-to-point-verbinding met uw externe bron gemaakt via een TCP-tunnel. De TCP-tunnel wordt ingesteld met TLS 1.2 met SAS-sleutels (Shared Access Signature).
- [Virtual Network integration](web-sites-integrate-with-vnet.md) with site-to-site VPN - As described in [Resources inside an Azure Virtual Network](#resources-inside-an-azure-virtual-network), but the Virtual Network can be connected to your on-premises network through a [site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). In deze netwerktopologie kan uw app verbinding maken met on-premises bronnen, zoals andere bronnen in het virtuele netwerk.
- [App Service-omgeving](environment/intro.md) met site-to-site VPN - Zoals beschreven in [Resources in een Azure Virtual Network,](#resources-inside-an-azure-virtual-network)maar het virtuele netwerk kan worden aangesloten op uw on-premises netwerk via een [site-to-site VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). In deze netwerktopologie kan uw app verbinding maken met on-premises bronnen, zoals andere bronnen in het virtuele netwerk.

## <a name="application-secrets"></a>Toepassingsgeheimen

Sla toepassingsgeheimen, zoals databasereferenties, API-tokens en privésleutels, niet op in uw code of configuratiebestanden. De algemeen aanvaarde aanpak is om toegang te krijgen tot hen als [omgevingsvariabelen](https://wikipedia.org/wiki/Environment_variable) met behulp van het standaardpatroon in uw taal van keuze. In App Service is de manier om omgevingsvariabelen te definiëren via [app-instellingen](configure-common.md#configure-app-settings) (en, met name voor .NET-toepassingen, [verbindingstekenreeksen).](configure-common.md#configure-connection-strings) App-instellingen en verbindingstekenreeksen worden versleuteld opgeslagen in Azure en worden alleen gedecodeerd voordat ze in het procesgeheugen van uw app worden geïnjecteerd wanneer de app wordt gestart. De encryptiesleutels worden regelmatig gedraaid.

U ook uw App Service-app integreren met [Azure Key Vault](/azure/key-vault/) voor geavanceerd geheimenbeheer. Door [toegang te krijgen tot de Key Vault met een beheerde identiteit,](../key-vault/tutorial-web-application-keyvault.md)heeft uw App Service-app veilig toegang tot de geheimen die u nodig hebt.

## <a name="network-isolation"></a>Netwerkisolatie

Behalve de **prijscategorie Geïsoleerd,** worden uw apps in alle lagen uitgevoerd op de gedeelde netwerkinfrastructuur in App Service. De openbare IP-adressen en front-end load balancers worden bijvoorbeeld gedeeld met andere tenants. De **laag Geïsoleerd en** is volledig geïsoleerd van het netwerk door uw apps uit te voeren in een speciale App [Service-omgeving.](environment/intro.md) Een App Service-omgeving wordt uitgevoerd in uw eigen exemplaar van [Azure Virtual Network.](/azure/virtual-network/) Hiermee u: 

- Serveer uw apps via een speciaal openbaar eindpunt, met speciale front-ends.
- Interne toepassing weergeven met behulp van een interne load balancer (ILB), die alleen toegang biedt vanuit uw Azure Virtual Network. De ILB heeft een IP-adres van uw privé subnet, dat totale isolatie van uw apps van het internet biedt.
- [Gebruik een ILB achter een webapplicatiefirewall (WAF).](environment/integrate-with-application-gateway.md) De WAF biedt bescherming op ondernemingsniveau voor uw openbare toepassingen, zoals DDoS-bescherming, URI-filtering en SQL-injectiepreventie.

Zie [Inleiding tot Azure App-serviceomgevingen](environment/intro.md)voor meer informatie. 
