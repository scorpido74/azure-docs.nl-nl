---
title: Inkomend verkeer v1 beheren
description: Meer informatie over het beheren van binnenkomend verkeer naar een App-serviceomgeving. Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 857b2b00aadced567bc8ac191cdd9908f7bea7a3
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804398"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Inkomend verkeer naar een app-serviceomgeving beheren
## <a name="overview"></a>Overzicht
Er kan **een** App-serviceomgeving worden gemaakt in een virtueel Azure Resource Manager-netwerk **of** in een virtueel netwerk voor een klassiek [implementatiemodel.][virtualnetwork]  Een nieuw virtueel netwerk en nieuw subnet kunnen worden gedefinieerd op het moment dat een App Service-omgeving wordt gemaakt.  U ook een App Service-omgeving maken in een reeds bestaand virtueel netwerk en een reeds bestaand subnet.  Met een wijziging in juni 2016 kunnen ASE's ook worden geïmplementeerd in virtuele netwerken die gebruikmaken van openbare adresbereiken of RFC1918-adresruimten (d.w.z. privéadressen).  Zie Hoe u [een app-serviceomgeving maakt][HowToCreateAnAppServiceEnvironment]voor meer informatie over het maken van een app-serviceomgeving.

Een App-serviceomgeving moet altijd worden gemaakt binnen een subnet, omdat een subnet een netwerkgrens biedt die kan worden gebruikt om binnenkomend verkeer achter upstream-apparaten en -services te vergrendelen, zodat HTTP- en HTTPS-verkeer alleen wordt geaccepteerd vanaf specifieke upstream IP-adressen.

Inkomend en uitgaand netwerkverkeer op een subnet wordt beheerd met behulp van een [netwerkbeveiligingsgroep.][NetworkSecurityGroups] Voor het beheren van binnenkomend verkeer moeten netwerkbeveiligingsregels worden opgesteld in een netwerkbeveiligingsgroep en moet de netwerkbeveiligingsgroep het subnet worden toegekoppeld dat de App-serviceomgeving bevat.

Zodra een netwerkbeveiligingsgroep is toegewezen aan een subnet, is binnenkomend verkeer naar apps in de App-serviceomgeving toegestaan/geblokkeerd op basis van de regels voor toestaan en weigeren die zijn gedefinieerd in de netwerkbeveiligingsgroep.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Binnenkomende netwerkpoorten die worden gebruikt in een app-serviceomgeving
Voordat het binnenkomende netwerkverkeer wordt geblokkeerd met een netwerkbeveiligingsgroep, is het belangrijk om de vereiste en optionele netwerkpoorten te kennen die worden gebruikt door een App-serviceomgeving.  Het per ongeluk afsluiten van verkeer naar sommige poorten kan leiden tot verlies van functionaliteit in een App Service-omgeving.

Hieronder volgt een lijst met poorten die worden gebruikt door een App Service-omgeving. Alle poorten zijn **TCP,** tenzij anders duidelijk vermeld:

* 454: **Vereiste poort** die wordt gebruikt door Azure-infrastructuur voor het beheren en onderhouden van App-serviceomgevingen via TLS.  Blokkeer het verkeer naar deze poort niet.  Deze haven is altijd gebonden aan de openbare VIP van een ASE.
* 455: **Vereiste poort** die wordt gebruikt door Azure-infrastructuur voor het beheren en onderhouden van App-serviceomgevingen via TLS.  Blokkeer het verkeer naar deze poort niet.  Deze haven is altijd gebonden aan de openbare VIP van een ASE.
* 80: Standaardpoort voor binnenkomend HTTP-verkeer naar apps die worden uitgevoerd in App-serviceplannen in een App-serviceomgeving.  Op een ASE met ILB-poort is deze poort gebonden aan het ILB-adres van de ASE.
* 443: Standaardpoort voor binnenkomend TLS-verkeer naar apps die worden uitgevoerd in App-serviceplannen in een App-serviceomgeving.  Op een ASE met ILB-poort is deze poort gebonden aan het ILB-adres van de ASE.
* 21: Controlekanaal voor FTP.  Deze poort kan veilig worden geblokkeerd als FTP niet wordt gebruikt.  Op een ASE met ILB-poort kan deze poort worden gekoppeld aan het ILB-adres voor een ASE.
* 990: Besturingskanaal voor FTPS.  Deze poort kan veilig worden geblokkeerd als FTPS niet wordt gebruikt.  Op een ASE met ILB-poort kan deze poort worden gekoppeld aan het ILB-adres voor een ASE.
* 10001-10020: Datakanalen voor FTP.  Net als bij het controlekanaal kunnen deze poorten veilig worden geblokkeerd als FTP niet wordt gebruikt.  Op een ASE met ILB-poort kan deze poort worden gekoppeld aan het ILB-adres van de ASE.
* 4016: Gebruikt voor het opsporen van externe debuggen met Visual Studio 2012.  Deze poort kan veilig worden geblokkeerd als de functie niet wordt gebruikt.  Op een ASE met ILB-poort is deze poort gebonden aan het ILB-adres van de ASE.
* 4018: Gebruikt voor het opsporen van externe debuggen met Visual Studio 2013.  Deze poort kan veilig worden geblokkeerd als de functie niet wordt gebruikt.  Op een ASE met ILB-poort is deze poort gebonden aan het ILB-adres van de ASE.
* 4020: Gebruikt voor het opsporen van externe debuggen met Visual Studio 2015.  Deze poort kan veilig worden geblokkeerd als de functie niet wordt gebruikt.  Op een ASE met ILB-poort is deze poort gebonden aan het ILB-adres van de ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Vereisten voor uitgaande verbindingen en DNS
Om een App Service-omgeving goed te laten functioneren, is ook uitgaande toegang tot verschillende eindpunten vereist. Een volledige lijst van de externe eindpunten die door een ASE worden gebruikt, bevindt zich in het gedeelte 'Vereiste netwerkconnectiviteit' van het artikel [Netwerkconfiguratie voor ExpressRoute.](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity)

App-serviceomgevingen vereisen een geldige DNS-infrastructuur die is geconfigureerd voor het virtuele netwerk.  Als de DNS-configuratie om welke reden dan ook wordt gewijzigd nadat een App Service-omgeving is gemaakt, kunnen ontwikkelaars een App Service-omgeving dwingen om de nieuwe DNS-configuratie op te halen.  Als u een herstart van de rolling omgeving activeert met het pictogram Opnieuw starten boven aan het beheerblad app-serviceomgeving in de [Azure-portal,][NewPortal] wordt de nieuwe DNS-configuratie opgehaald door de omgeving.

Het wordt ook aanbevolen dat aangepaste DNS-servers op het vnet van tevoren worden ingesteld voordat u een App Service-omgeving maakt.  Als de DNS-configuratie van een virtueel netwerk wordt gewijzigd terwijl een App-serviceomgeving wordt gemaakt, wordt het proces voor het maken van de app-serviceomgeving mislukt.  Als er aan de andere kant van een VPN-gateway een aangepaste DNS-server bestaat en de DNS-server onbereikbaar of niet beschikbaar is, mislukt ook het proces voor het maken van de app-serviceomgeving.

## <a name="creating-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken
Zie de volgende [informatie][NetworkSecurityGroups]voor meer informatie over de manier waarop netwerkbeveiligingsgroepen werken.  In het onderstaande voorbeeld Azure Service Management worden hoogtepunten van netwerkbeveiligingsgroepen behandeld, met een focus op het configureren en toepassen van een netwerkbeveiligingsgroep op een subnet dat een App Service-omgeving bevat.

**Let op:** Netwerkbeveiligingsgroepen kunnen grafisch worden geconfigureerd met behulp van de [Azure Portal](https://portal.azure.com) of via Azure PowerShell.

Netwerkbeveiligingsgroepen worden eerst gemaakt als een zelfstandige entiteit die is gekoppeld aan een abonnement. Aangezien netwerkbeveiligingsgroepen zijn gemaakt in een Azure-regio, moet u ervoor zorgen dat de netwerkbeveiligingsgroep wordt gemaakt in dezelfde regio als de App-serviceomgeving.

Het volgende toont het maken van een netwerkbeveiligingsgroep:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Zodra een netwerkbeveiligingsgroep is gemaakt, worden er een of meer netwerkbeveiligingsregels aan toegevoegd.  Aangezien de reeks regels in de loop van de tijd kan veranderen, wordt aanbevolen om ruimte uit de nummeringregeling die wordt gebruikt voor regelprioriteiten om het gemakkelijk te maken om in de loop van de tijd aanvullende regels in te voegen.

In het onderstaande voorbeeld ziet u een regel die expliciet toegang verleent tot de beheerpoorten die de Azure-infrastructuur nodig heeft om een App-serviceomgeving te beheren en te onderhouden.  Houd er rekening mee dat alle beheerverkeer via TLS stroomt en wordt beveiligd door clientcertificaten, dus ook al worden de poorten geopend, zijn ze ontoegankelijk voor een andere entiteit dan azure-beheerinfrastructuur.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Wanneer u de toegang tot poort 80 en 443 vergrendelt om een App Service-omgeving achter upstream-apparaten of -services te "verbergen", moet u het upstream IP-adres kennen.  Als u bijvoorbeeld een webtoepassingsfirewall (WAF) gebruikt, heeft de WAF een eigen IP-adres (of adressen) dat wordt gebruikt bij het proxyren van verkeer naar een downstream-App Service-omgeving.  U moet dit IP-adres gebruiken in de parameter *SourceAddressPrefix* van een netwerkbeveiligingsregel.

In het onderstaande voorbeeld is inkomend verkeer vanaf een specifiek upstream IP-adres expliciet toegestaan.  Het adres *1.2.3.4* wordt gebruikt als tijdelijke aanduiding voor het IP-adres van een upstream WAF.  Wijzig de waarde die overeenkomt met het adres dat wordt gebruikt door uw upstream-apparaat of -service.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Als FTP-ondersteuning gewenst is, kunnen de volgende regels worden gebruikt als sjabloon om toegang te verlenen tot de FTP-beheerpoort en de poorten van het gegevenskanaal.  Aangezien FTP een stateful protocol is, u ftp-verkeer mogelijk niet routeren via een traditionele HTTP/HTTPS-firewall of proxy-apparaat.  In dit geval moet u het *SourceAddressPrefix* instellen op een andere waarde, bijvoorbeeld het IP-adresbereik van ontwikkelaars- of implementatiemachines waarop FTP-clients worden uitgevoerd. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Opmerking:** het bereik van de datakanaalpoort kan tijdens de voorbeeldperiode veranderen.)

Als het foutopsporing op afstand met Visual Studio wordt gebruikt, geven de volgende regels aan hoe u toegang verleent.  Er is een aparte regel voor elke ondersteunde versie van Visual Studio, omdat elke versie een andere poort gebruikt voor het op afstand opsporen van foutopsporing.  Net als bij FTP-toegang kan rasfoutbuggingverkeer niet goed verlopen via een traditioneel WAF- of proxy-apparaat.  De *SourceAddressPrefix* kan in plaats daarvan worden ingesteld op het IP-adresbereik van ontwikkelaarsmachines met Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Een netwerkbeveiligingsgroep toewijzen aan een subnet
Een netwerkbeveiligingsgroep heeft een standaardbeveiligingsregel die de toegang tot al het externe verkeer ontzegt.  Het resultaat van het combineren van de hierboven beschreven netwerkbeveiligingsregels en de standaardbeveiligingsregel die binnenkomend verkeer blokkeert, is dat alleen verkeer van bronadresbereiken dat is gekoppeld aan een actie *Toestaan,* verkeer kan verzenden naar apps die worden uitgevoerd in een App-serviceomgeving.

Nadat een netwerkbeveiligingsgroep is gevuld met beveiligingsregels, moet deze worden toegewezen aan het subnet met de App-serviceomgeving.  De opdracht opdracht verwijst naar zowel de naam van het virtuele netwerk waar de App Service Omgeving zich bevindt, als de naam van het subnet waar de App Service Environment is gemaakt.  

In het onderstaande voorbeeld ziet u een netwerkbeveiligingsgroep die wordt toegewezen aan een subnet en virtueel netwerk:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Zodra de toewijzing van de netwerkbeveiligingsgroep is geslaagd (de toewijzing is een langlopende bewerking en kan het enkele minuten duren), bereiken alleen binnenkomende regels voor verkeer *die overeenkomen met Regels toestaan* met succes apps in de App-serviceomgeving.

Voor volledigheid geeft het volgende voorbeeld aan hoe u de netwerkbeveiligingsgroep van het subnet verwijderen en dus loskoppelen:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Speciale overwegingen voor expliciete IP-SSL
Als een app is geconfigureerd met een expliciet IP-SSL-adres *(alleen* van toepassing op AS's met een openbare VIP), in plaats van het standaard IP-adres van de App Service-omgeving te gebruiken, stroomt zowel HTTP- als HTTPS-verkeer in het subnet via een andere set poorten dan poorten 80 en 443.

Het afzonderlijke paar poorten dat door elk IP-SSL-adres wordt gebruikt, is te vinden in de gebruikersinterface van de portal via het UX-blade van de App Service Environment.  Selecteer 'Alle instellingen' > 'IP-adressen'.  Het blade 'IP-adressen' toont een tabel met alle expliciet geconfigureerde IP-SSL-adressen voor de App Service-omgeving, samen met het speciale poortpaar dat wordt gebruikt om HTTP- en HTTPS-verkeer te routeren dat is gekoppeld aan elk IP-SSL-adres.  Het is dit poortpaar dat moet worden gebruikt voor de Parameters DestinationPortRange bij het configureren van regels in een netwerkbeveiligingsgroep.

Wanneer een app op een ASE is geconfigureerd om IP-SSL te gebruiken, zien externe klanten niet en hoeven ze zich geen zorgen te maken over de toewijzing van speciale poortpaartoewijzingen.  Verkeer naar de apps wordt normaal gesproken naar het geconfigureerde IP-SSL-adres uitgevoerd.  De vertaling naar het speciale poortpaar gebeurt automatisch intern tijdens de laatste etappe van het routeringsverkeer naar het subnet met de ASE. 

## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot app-serviceomgeving][IntroToAppServiceEnvironment] om aan de slag te gaan met app-serviceomgeving

Zie [Veilig verbinding maken met Backend-bronnen vanuit een app-serviceomgeving][SecurelyConnecttoBackend] voor meer informatie over apps die veilig verbinding maken met backendbronnen vanuit een app-serviceomgeving.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

