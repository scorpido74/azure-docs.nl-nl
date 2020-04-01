---
title: Aandachtspunten voor netwerken
description: Meer informatie over het ASE-netwerkverkeer en hoe u netwerkbeveiligingsgroepen en door de gebruiker gedefinieerde routes instelt met uw ASE.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 01/24/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4aec7fa78292f224952dd2ae929d2b8bfd97ab9b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477687"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Netwerkoverwegingen voor een App Service-omgeving #

## <a name="overview"></a>Overzicht ##

 Azure [App Service-omgeving][Intro] is een implementatie van Azure App Service in een subnet in uw Azure virtual network (VNet). Er zijn twee implementatietypen voor een App Service-omgeving (ASE):

- **Externe ASE**: Hiermee worden de door ASE gehoste apps op een IP-adres dat via internet wordt gehost, ontmaskerd. Zie [Een externe ASE maken][MakeExternalASE]voor meer informatie.
- **ILB ASE**: Onthult de ase-gehoste apps op een IP-adres in uw VNet. Het interne eindpunt is een interne load balancer (ILB), daarom heet het een ILB ASE. Zie [Een ILB ASE maken en gebruiken][MakeILBASE]voor meer informatie.

Alle ASEs, Extern en ILB, hebben een openbare VIP die wordt gebruikt voor inkomend beheerverkeer en als het van adres bij het bellen van de ASE naar het internet. De oproepen van een ASE die naar het internet gaan, verlaten het VNet via de VIP die is toegewezen aan de ASE. De openbare IP van deze VIP is de bron IP voor alle gesprekken van de ASE die naar het internet. Als de apps in uw ASE bellen naar bronnen in uw VNet of via een VPN, is het bron-IP een van de IP's in het subnet dat door uw ASE wordt gebruikt. Omdat de ASE zich binnen het VNet bevindt, kan het ook toegang krijgen tot bronnen binnen het VNet zonder extra configuratie. Als het VNet is verbonden met uw on-premises netwerk, hebben apps in uw ASE daar ook toegang tot bronnen zonder extra configuratie.

![Externe ASE][1] 

Als u een externe ASE hebt, is de openbare VIP ook het eindpunt dat uw ASE-apps oplossen voor:

* HTTP/S 
* FTP/S
* Webimplementatie
* Foutopsporing op afstand

![ILB ASE][2]

Als u een ILB ASE hebt, is het adres van het ILB-adres het eindpunt voor HTTP/S, FTP/S, webimplementatie en foutopsporing op afstand.

## <a name="ase-subnet-size"></a>ASE subnetgrootte ##

De grootte van het subnet dat wordt gebruikt om een ASE te hosten, kan niet worden gewijzigd nadat de ASE is geïmplementeerd.  De ASE gebruikt een adres voor elke infrastructuurrol en voor elk exemplaar van het app-serviceplan.  Daarnaast zijn er vijf adressen die door Azure Networking worden gebruikt voor elk subnet dat wordt gemaakt.  Een ASE zonder App Service-abonnementen gebruikt 12 adressen voordat u een app maakt.  Als het een ILB ASE is, gebruikt het 13 adressen voordat u een app in die ASE maakt. Terwijl u uw ASE uitschaalt, worden infrastructuurrollen toegevoegd elk veelvoud van 15 en 20 van uw App Service-planexemplaren.

   > [!NOTE]
   > Niets anders kan worden in het subnet, maar de ASE. Zorg ervoor dat u een adresruimte kiest die toekomstige groei mogelijk maakt. U deze instelling later niet wijzigen. Wij raden een `/24` grootte van met 256 adressen.

Wanneer u opschaalt of omlaag schaalt, worden nieuwe rollen van de juiste grootte toegevoegd en worden uw workloads gemigreerd van de huidige grootte naar de doelgrootte. De oorspronkelijke VM's worden pas verwijderd nadat de workloads zijn gemigreerd. Als u een ASE met 100 ASP-exemplaren had, zou er een periode zijn waarin u het dubbele van het aantal VM's nodig hebt.  Daarom raden we aan om een '/24' te gebruiken om eventuele wijzigingen aan te passen.  

## <a name="ase-dependencies"></a>ASE-afhankelijkheden ##

### <a name="ase-inbound-dependencies"></a>ASE-inkomende afhankelijkheden ###

Alleen voor de ASE om te werken, de ASE vereist dat de volgende poorten open zijn:

| Gebruiken | Van | Handeling |
|-----|------|----|
| Beheer | Adressen voor app-servicebeheer | ASE-subnet: 454, 455 |
|  Interne communicatie van ASE | ASE-subnet: Alle poorten | ASE-subnet: Alle poorten
|  Azure load balancer binnenkomen | Azure Load Balancer | ASE subnet: 16001

Er zijn 2 andere poorten die kunnen worden weergegeven als open op een poortscan, 7654 en 1221. Ze antwoorden met een IP-adres en niets meer. Ze kunnen desgewenst worden geblokkeerd. 

Het inkomende beheerverkeer biedt command en control van de ASE naast systeembewaking. De bronadressen voor dit verkeer worden vermeld in het [ASE-beheeradresdocument.][ASEManagement] De netwerkbeveiligingsconfiguratie moet toegang bieden vanaf de ASE-beheeradressen op poorten 454 en 455. Als u de toegang vanaf deze adressen blokkeert, wordt uw ASE ongezond en wordt deze opgeschort. Het TCP-verkeer dat binnenkomt op poorten 454 en 455 moet terug gaan van dezelfde VIP of u zult een asymmetrisch routeprobleem hebben. 

Binnen het ASE-subnet worden veel poorten gebruikt voor interne componentcommunicatie en kunnen ze veranderen. Dit vereist dat alle poorten in het ASE-subnet toegankelijk zijn via het ASE-subnet. 

Voor de communicatie tussen de Azure load balancer en het ASE-subnet zijn de minimale poorten die open moeten zijn 454, 455 en 16001. De 16001-poort wordt gebruikt voor het in leven houden van het verkeer tussen de load balancer en de ASE. Als u een ILB ASE gebruikt, u het verkeer vergrendelen naar alleen de 454, 455, 16001-poorten.  Als u een externe ASE gebruikt, moet u rekening houden met de normale toegangspoorten voor apps.  

De andere poorten waar u zich zorgen over moet maken zijn de toepassingspoorten:

| Gebruiken | Poorten |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Foutopsporing op externe services van Visual Studio  |  4020, 4022, 4024 |
|  Webdeploy-service | 8172 |

Als u de toepassingspoorten blokkeert, kan uw ASE nog steeds functioneren, maar uw app mogelijk niet.  Als u ip-adressen met een externe ASE met app-toegewezen gebruikt, moet u verkeer van de IP's die aan uw apps zijn toegewezen aan het ASE-subnet toestaan op de poorten die worden weergegeven in de PAGINA ASE-portal > IP-adressen.

### <a name="ase-outbound-dependencies"></a>ASE uitgaande afhankelijkheden ###

Voor uitgaande toegang is een ASE afhankelijk van meerdere externe systemen. Veel van deze systeemafhankelijkheden worden gedefinieerd met DNS-namen en worden niet toegewezen aan een vaste set IP-adressen. De ASE vereist dus uitgaande toegang van het ASE-subnet tot alle externe IP's in verschillende poorten. 

De ASE communiceert naar internettoegankelijke adressen op de volgende poorten:

| Gebruikt | Poorten |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL, Windows-updates, Linux-afhankelijkheden, Azure-services | 80/443 |
| Azure SQL | 1433 | 
| Bewaking | 12000 |

De uitgaande afhankelijkheden worden weergegeven in het document waarin [uitgaand verkeer van de app-serviceomgeving](./firewall-integration.md)wordt beschreven. Als de ASE geen toegang meer heeft tot zijn afhankelijkheden, werkt deze niet meer. Als dat lang genoeg gebeurt, wordt de ASE opgeschort. 

### <a name="customer-dns"></a>DNS-klant ###

Als de VNet is geconfigureerd met een door de klant gedefinieerde DNS-server, gebruiken de tenantworkloads deze. De ASE gebruikt Azure DNS voor beheerdoeleinden. Als de VNet is geconfigureerd met een door de klant geselecteerde DNS-server, moet de DNS-server bereikbaar zijn vanaf het subnet dat de ASE bevat.

Als u de DNS-resolutie vanuit uw web-app wilt testen, u de *naamresolver van*de consoleopdracht gebruiken. Ga naar het foutopsporingsvenster op uw scm-site voor uw app of ga naar de app in de portal en selecteer console. Vanuit de shell-prompt u de *opdrachtnaamresolver* samen met de DNS-naam die u wilt opzoeken, uitgeven. Het resultaat dat u terugkrijgt is hetzelfde als wat uw app zou krijgen tijdens het maken van dezelfde lookup. Als u nslookup gebruikt, wordt er een opzoeknaar gedaan met Azure DNS.

Als u de DNS-instelling van de VNet waarin uw ASE zich bevindt, wijzigt, moet u uw ASE opnieuw opstarten. Om te voorkomen dat uw ASE opnieuw wordt opgestart, is het ten zeerste aan te raden om uw DNS-instellingen voor uw VNet te configureren voordat u uw ASE maakt.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portal dependencies (Portal-afhankelijkheden) ##

Naast de ase functionele afhankelijkheden, zijn er een paar extra items met betrekking tot de portal ervaring. Sommige mogelijkheden in de Azure-portal zijn afhankelijk van directe toegang tot _de SCM-site._ Voor elke app in Azure App Service zijn er twee URL's. De eerste URL is om toegang te krijgen tot uw app. De tweede URL is om toegang te krijgen tot de SCM site, die ook wel de _Kudu console_. Functies die gebruik maken van de SCM-site zijn:

-   WebJobs
-   Functions
-   Logboekstreaming
-   Kudu
-   Extensies
-   Procesverkenner
-   Console

Wanneer u een ILB ASE gebruikt, is de SCM-site niet toegankelijk van buiten het VNet. Sommige mogelijkheden werken niet vanuit de app-portal omdat ze toegang nodig hebben tot de SCM-site van een app. U rechtstreeks verbinding maken met de SCM-site in plaats van de portal te gebruiken. 

Als uw ILB ASE de domeinnaam is *contoso.appserviceenvironment.net* en uw app-naam *testapp*is, wordt de app bereikt op *testapp.contoso.appserviceenvironment.net*. De SCM site die daarbij hoort is bereikt op *testapp.scm.contoso.appserviceenvironment.net.*

## <a name="ase-ip-addresses"></a>ASE IP-adressen ##

Een ASE heeft een paar IP-adressen om op de hoogte te zijn van. Dit zijn:

- **Openbaar inkomend IP-adres**: wordt gebruikt voor app-verkeer in een externe ASE en beheerverkeer in zowel een externe ASE als een ILB ASE.
- **Uitgaande openbare IP:** Gebruikt als de "van" IP voor uitgaande verbindingen van de ASE die de VNet verlaten, die niet worden gerouteerd naar beneden een VPN.
- **Ip-adres van ILB**: Het IP-adres van ILB bestaat alleen in een ILB ASE.
- **App-toegewezen IP-gebaseerde SSL-adressen:** Alleen mogelijk met een externe ASE en wanneer IP-gebaseerde SSL is geconfigureerd.

Al deze IP-adressen zijn zichtbaar in de Azure-portal vanuit de ASE-gebruikersinterface. Als u een ILB ASE hebt, wordt het IP voor de ILB vermeld.

   > [!NOTE]
   > Deze IP-adressen worden niet gewijzigd zolang uw ASE actief blijft.  Als uw ASE wordt opgeschort en hersteld, worden de adressen die door uw ASE worden gebruikt, gewijzigd. De normale reden voor een aas om te worden opgeschort is als u inkomende beheertoegang blokkeert of toegang tot een ASE-afhankelijkheid blokkeert. 

![IP-adressen][3]

### <a name="app-assigned-ip-addresses"></a>Aan app toegewezen IP-adressen ###

Met een externe ASE u IP-adressen toewijzen aan afzonderlijke apps. Dat kan niet met een ILB ASE. Zie [Een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in Azure App Service](../configure-ssl-bindings.md)voor meer informatie over het configureren van uw app op een eigen IP-adres.

Wanneer een app een eigen IP-adres heeft, reserveert de ASE twee poorten om aan dat IP-adres te toewijzen. De ene poort is voor HTTP-verkeer en de andere poort is voor HTTPS. Deze poorten worden vermeld in de ASE-gebruikersinterface in de sectie IP-adressen. Verkeer moet in staat zijn om deze poorten te bereiken vanuit de VIP of de apps zijn ontoegankelijk. Deze vereiste is belangrijk om te onthouden wanneer u Network Security Groups (NSG's) configureert.

## <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen ##

[Netwerkbeveiligingsgroepen][NSGs] bieden de mogelijkheid om de toegang tot het netwerk binnen een VNet te beheren. Wanneer u de portal gebruikt, is er een impliciete weigeringsregel met de laagste prioriteit om alles te ontkennen. Wat je bouwt zijn uw toestaan regels.

In een ASE hebt u geen toegang tot de VM's die worden gebruikt om de ASE zelf te hosten. Ze hebben een door Microsoft beheerd abonnement. Als u de toegang tot de apps op de ASE wilt beperken, stelt u NSG's in op het ASE-subnet. Let daarbij goed op de ASE-afhankelijkheden. Als u afhankelijkheden blokkeert, werkt de ASE niet meer.

NSG's kunnen worden geconfigureerd via de Azure-portal of via PowerShell. De informatie hier toont de Azure-portal. U maakt en beheert NSG's in de portal als een bron op het hoogste niveau onder **Netwerken.**

De vereiste vermeldingen in een NSG, om een ASE te laten functioneren, zijn om het verkeer toe te staan:

**Inkomend**
* van de IP-servicetag AppServiceManagement op poorten 454.455
* van de load balancer op poort 16001
* van het ASE-subnet naar het ASE-subnet op alle poorten

**Uitgaand**
* naar alle IP's op poort 123
* aan alle IP's op havens 80, 443
* naar de IP-servicetag AzureSQL op poorten 1433
* aan alle IP's op poort 12000
* naar het ASE-subnet op alle poorten

De DNS-poort hoeft niet te worden toegevoegd omdat het verkeer naar DNS niet wordt beïnvloed door NSG-regels. Deze poorten bevatten niet de poorten die uw apps nodig hebben voor succesvol gebruik. De normale app-toegangspoorten zijn:

| Gebruiken | Poorten |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP/FTPS    | 21, 990, 10001-10020 |
|  Foutopsporing op externe services van Visual Studio  |  4020, 4022, 4024 |
|  Webdeploy-service | 8172 |

Wanneer rekening wordt gehouden met de inkomende en uitgaande eisen, moeten de NSG's op elkaar lijken als de NSG's die in dit voorbeeld worden weergegeven. 

![Inkomende beveiligingsregels][4]

Met een standaardregel kunnen de IP's in het VNet met het ASE-subnet praten. Een andere standaardregel stelt de load balancer, ook wel bekend als de openbare VIP, in staat om met de ASE te communiceren. Als u de standaardregels wilt weergeven, selecteert u **Standaardregels** naast het pictogram **Toevoegen.** Als u een regel voor het weigeren van al het andere vóór de standaardregels plaatst, voorkomt u verkeer tussen de VIP en de ASE. Als u wilt voorkomen dat verkeer binnen het VNet komt, voegt u uw eigen regel toe om binnenkomend toe te staan. Gebruik een bron die gelijk is aan AzureLoadBalancer **\*** met een bestemming van **Elke** en een poortbereik van . Omdat de NSG-regel wordt toegepast op het ASE-subnet, hoeft u niet specifiek te zijn in de bestemming.

Als u een IP-adres aan uw app hebt toegewezen, moet u ervoor zorgen dat u de poorten open houdt. Als u de poorten wilt zien, selecteert u**IP-adressen** **van app-serviceomgeving** > .  

Alle items in de volgende uitgaande regels zijn nodig, behalve het laatste item. Ze maken netwerktoegang tot de ASE-afhankelijkheden mogelijk die eerder in dit artikel zijn opgemerkt. Als je een van hen blokkeert, werkt je ASE niet meer. Met het laatste item in de lijst kan uw ASE communiceren met andere bronnen in uw VNet.

![Uitgaande beveiligingsregels][5]

Nadat uw NSG's zijn gedefinieerd, wijst u ze toe aan het subnet waarop uw ASE is ingeschakeld. Als u het ASE VNet of subnet niet meer weet, u het zien op de ASE-portalpagina. Als u de NSG aan uw subnet wilt toewijzen, gaat u naar de subnetgebruikersinterface en selecteert u de NSG.

## <a name="routes"></a>Routes ##

Gedwongen tunneling is wanneer u routes in uw VNet, zodat de uitgaande verkeer gaat niet rechtstreeks naar het internet, maar ergens anders, zoals een ExpressRoute gateway of een virtueel toestel.  Als u uw ASE op een dergelijke manier moet configureren, leest u het document op [Het configureren van uw app-serviceomgeving met gedwongen tunneling.][forcedtunnel]  Dit document vertelt u de beschikbare opties om te werken met ExpressRoute en gedwongen tunneling.

Wanneer u een ASE maakt in de portal, maken we ook een reeks routetabellen op het subnet dat met de ASE is gemaakt.  Die routes zeggen gewoon om uitgaand verkeer rechtstreeks naar het internet te sturen.  
Voer de volgende stappen uit om dezelfde routes handmatig te maken:

1. Ga naar Azure Portal. Selecteer > **Netwerkroutetabellen**. **Networking**

2. Maak een nieuwe routetabel in dezelfde regio als uw VNet.

3. Selecteer Routes **Routes** > **toevoegen**vanuit de gebruikersinterface van uw routetabel .

4. Stel het **volgende hoptype** in op **internet** en het **adresvoorvoegsel** op **0.0.0.0/0**. Selecteer **Opslaan**.

    U ziet dan zoiets als het volgende:

    ![Functionele routes][6]

5. Nadat u de nieuwe routetabel hebt gemaakt, gaat u naar het subnet met uw ASE. Selecteer de routetabel in de lijst in de portal. Nadat u de wijziging hebt opgeslagen, ziet u de NSG's en routes die met uw subnet zijn genoteerd.

    ![NSGs en routes][7]

## <a name="service-endpoints"></a>Service-eindpunten ##

Met service-eindpunten kunt u de toegang tot multitenant-services beperken tot een reeks virtuele Azure-netwerken en subnetten. In de documentatie [Service-eindpunten voor virtuele netwerken][serviceendpoints] vindt u meer informatie over service-eindpunten. 

Wanneer u service-eindpunten voor een bron inschakelt, worden er routes gemaakt die een hogere prioriteit hebben dan alle andere routes. Als u Service-eindpunten gebruikt op een Azure-service, met een geforceerde getunnelde ASE, wordt het verkeer naar die services niet geforceerd getunneld. 

Als Service-eindpunten in een subnet met een Azure SQL-exemplaar is ingeschakeld, moet Service-eindpunten zijn ingeschakeld op alle Azure SQL-exemplaren waarmee vanuit dat subnet een verbinding wordt gemaakt. Als u vanuit hetzelfde subnet toegang wilt hebben tot meerdere Azure SQL-exemplaren, is het niet mogelijk om Service-eindpunten wel op het ene Azure SQL-exemplaar in te schakelen en niet op een ander. Geen enkele andere Azure-service gedraagt zich als Azure SQL met betrekking tot Service Endpoints. Wanneer u Service-eindpunten met Azure Storage inschakelt, kunt u de toegang tot die resource vanuit uw subnet vergrendelen en toegang behouden tot andere Azure Storage-accounts, zelfs als Service-eindpunten op die accounts niet is ingeschakeld.  

![Service-eindpunten][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/security-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
