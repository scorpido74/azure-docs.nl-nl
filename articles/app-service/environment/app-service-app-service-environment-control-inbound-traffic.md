---
title: Beheer van inkomend verkeer v1
description: Meer informatie over het beheren van inkomend verkeer naar een App Service Environment. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 857b2b00aadced567bc8ac191cdd9908f7bea7a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804398"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Inkomend verkeer naar een App Service Environment beheren
## <a name="overview"></a>Overzicht
Een App Service Environment kan worden gemaakt in een Azure Resource Manager virtueel netwerk **of** een [virtueel netwerk][virtualnetwork] **in het klassieke** implementatie model.  Er kan een nieuw virtueel netwerk en een nieuw subnet worden gedefinieerd op het moment dat een App Service Environment wordt gemaakt.  U kunt ook een App Service Environment maken in een bestaand virtueel netwerk en al het bestaande subnet.  Als er een wijziging is aangebracht in juni 2016, kan as ook worden geïmplementeerd in virtuele netwerken die gebruikmaken van open bare adresbereiken of RFC1918-adres ruimten (dat wil zeggen particuliere adressen).  Zie [een app service Environment maken][HowToCreateAnAppServiceEnvironment]voor meer informatie over het maken van een app service environment.

Een App Service Environment moet altijd worden gemaakt binnen een subnet omdat een subnet een netwerk grens biedt die kan worden gebruikt voor het vergren delen van inkomend verkeer achter upstream-apparaten en-services, zodat HTTP-en HTTPS-verkeer alleen wordt geaccepteerd van specifieke upstream-IP-adressen.

Binnenkomend en uitgaand netwerk verkeer op een subnet wordt beheerd met behulp van een [netwerk beveiligings groep][NetworkSecurityGroups]. Voor het beheren van binnenkomend verkeer moet u netwerk beveiligings regels maken in een netwerk beveiligings groep en vervolgens de netwerk beveiligings groep toewijzen aan het subnet met de App Service Environment.

Zodra een netwerk beveiligings groep is toegewezen aan een subnet, wordt binnenkomend verkeer naar apps in de App Service Environment toegestaan/geblokkeerd op basis van de regels voor toestaan en weigeren die zijn gedefinieerd in de netwerk beveiligings groep.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Binnenkomende netwerk poorten die worden gebruikt in een App Service Environment
Voordat u binnenkomend netwerk verkeer met een netwerk beveiligings groep vergrendelt, is het belang rijk dat u weet wat de vereiste en optionele netwerk poorten zijn die worden gebruikt door een App Service Environment.  Het per ongeluk sluiten van verkeer naar sommige poorten kan leiden tot verlies van functionaliteit in een App Service Environment.

Hier volgt een lijst met poorten die worden gebruikt door een App Service Environment. Alle poorten zijn **TCP**, tenzij anders duidelijk vermeld:

* 454: de **vereiste poort** die wordt gebruikt door de Azure-infra structuur voor het beheren en onderhouden van app service omgevingen via TLS.  Verkeer naar deze poort niet blok keren.  Deze poort is altijd gebonden aan het open bare VIP van een ASE.
* 455: de **vereiste poort** die wordt gebruikt door de Azure-infra structuur voor het beheren en onderhouden van app service omgevingen via TLS.  Verkeer naar deze poort niet blok keren.  Deze poort is altijd gebonden aan het open bare VIP van een ASE.
* 80: standaard poort voor binnenkomend HTTP-verkeer naar apps die worden uitgevoerd in App Service plannen in een App Service Environment.  Op een ILB-ASE is deze poort gebonden aan het ILB-adres van de ASE.
* 443: standaard poort voor binnenkomend TLS-verkeer naar apps die worden uitgevoerd in App Service plannen in een App Service Environment.  Op een ILB-ASE is deze poort gebonden aan het ILB-adres van de ASE.
* 21: besturings kanaal voor FTP.  Deze poort kan veilig worden geblokkeerd als FTP niet wordt gebruikt.  Op een ILB-ASE kan deze poort worden gebonden aan het ILB-adres voor een ASE.
* 990: besturings kanaal voor FTPS.  Deze poort kan veilig worden geblokkeerd als FTPS niet wordt gebruikt.  Op een ILB-ASE kan deze poort worden gebonden aan het ILB-adres voor een ASE.
* 10001-10020: gegevens kanalen voor FTP.  Net als bij het besturings kanaal kunnen deze poorten veilig worden geblokkeerd als FTP niet wordt gebruikt.  Op een ILB-ASE kan deze poort worden gebonden aan het ILB-adres van de ASE.
* 4016: wordt gebruikt voor fout opsporing op afstand met Visual Studio 2012.  Deze poort kan veilig worden geblokkeerd als de functie niet wordt gebruikt.  Op een ILB-ASE is deze poort gebonden aan het ILB-adres van de ASE.
* 4018: wordt gebruikt voor fout opsporing op afstand met Visual Studio 2013.  Deze poort kan veilig worden geblokkeerd als de functie niet wordt gebruikt.  Op een ILB-ASE is deze poort gebonden aan het ILB-adres van de ASE.
* 4020: wordt gebruikt voor fout opsporing op afstand met Visual Studio 2015.  Deze poort kan veilig worden geblokkeerd als de functie niet wordt gebruikt.  Op een ILB-ASE is deze poort gebonden aan het ILB-adres van de ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Vereisten voor uitgaande verbindingen en DNS
Voor een goede werking van App Service Environment is ook uitgaande toegang tot verschillende eind punten vereist. Een volledige lijst van de externe eind punten die worden gebruikt door een ASE vindt u in de sectie ' vereiste netwerk verbinding ' van het artikel [netwerk configuratie voor ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service omgevingen vereisen een geldige DNS-infra structuur die is geconfigureerd voor het virtuele netwerk.  Als de DNS-configuratie om welke reden dan ook wordt gewijzigd nadat een App Service Environment is gemaakt, kunnen ontwikkel aars een App Service Environment afdwingen om de nieuwe DNS-configuratie op te halen.  Het activeren van een rolling omgeving opnieuw opstarten met het pictogram ' opnieuw opstarten ' boven aan de Blade App Service Environment-beheer in de [Azure Portal][NewPortal] zorgt ervoor dat de omgeving de nieuwe DNS-configuratie ophaalt.

Het is ook raadzaam dat alle aangepaste DNS-servers op het vnet vooraf worden ingesteld voordat een App Service Environment wordt gemaakt.  Als de DNS-configuratie van een virtueel netwerk wordt gewijzigd terwijl er een App Service Environment wordt gemaakt, leidt dit ertoe dat het proces voor het maken van App Service Environment mislukt.  Als er in een vergelijk bare Vein een aangepaste DNS-server bestaat aan het andere uiteinde van een VPN-gateway en de DNS-server onbereikbaar is of niet beschikbaar is, mislukt het proces voor het maken van de App Service Environment ook.

## <a name="creating-a-network-security-group"></a>Een netwerk beveiligings groep maken
Raadpleeg de volgende [informatie][NetworkSecurityGroups]voor meer informatie over de werking van netwerk beveiligings groepen.  Het Azure Service Management-voor beeld hieronder is gericht op hooglichten van netwerk beveiligings groepen, met de nadruk op het configureren en Toep assen van een netwerk beveiligings groep op een subnet dat een App Service Environment bevat.

**Opmerking:** Netwerk beveiligings groepen kunnen grafisch worden geconfigureerd met behulp van [Azure Portal](https://portal.azure.com) of via Azure PowerShell.

Netwerk beveiligings groepen worden eerst gemaakt als een zelfstandige entiteit die is gekoppeld aan een abonnement. Aangezien netwerk beveiligings groepen worden gemaakt in een Azure-regio, moet u ervoor zorgen dat de netwerk beveiligings groep is gemaakt in dezelfde regio als de App Service Environment.

Hieronder ziet u een voor beeld van het maken van een netwerk beveiligings groep:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Zodra een netwerk beveiligings groep is gemaakt, worden er een of meer regels voor netwerk beveiliging aan toegevoegd.  Omdat de set regels in de loop van de tijd kan veranderen, is het raadzaam om het Nummerings schema dat wordt gebruikt voor regel prioriteiten uit te voeren, zodat u gemakkelijk extra regels kunt invoegen in de loop van de tijd.

In het onderstaande voor beeld ziet u een regel waarmee expliciet toegang wordt verleend tot de beheer poorten die nodig zijn voor de Azure-infra structuur voor het beheren en onderhouden van een App Service Environment.  Houd er rekening mee dat alle beheer verkeer stroomt via TLS en wordt beveiligd door client certificaten, dus zelfs als de poorten zijn geopend, zijn ze niet toegankelijk voor andere entiteiten dan de Azure-beheer infrastructuur.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP


Wanneer u de toegang tot poort 80 en 443 wilt vergren delen voor het verbergen van een App Service Environment achter upstream-apparaten of-services, moet u het IP-adres van de upstream weten.  Als u bijvoorbeeld een Web Application Firewall (WAF) gebruikt, heeft de WAF een eigen IP-adres (of adressen) dat wordt gebruikt voor het door lopen van verkeer naar een downstream App Service Environment.  U moet dit IP-adres gebruiken in de para meter *SourceAddressPrefix* van een netwerk beveiligings regel.

In het onderstaande voor beeld is inkomend verkeer van een specifiek upstream-IP-adres expliciet toegestaan.  Het adres *1.2.3.4* wordt gebruikt als tijdelijke aanduiding voor het IP-adres van een upstream-WAF.  Wijzig de waarde zodat deze overeenkomt met het adres dat wordt gebruikt door uw upstream-apparaat of-service.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Als FTP-ondersteuning gewenst is, kunnen de volgende regels worden gebruikt als een sjabloon om toegang te verlenen tot de FTP-besturings poort en gegevens kanaal poorten.  Aangezien FTP een stateful protocol is, is het wellicht niet mogelijk om FTP-verkeer via een traditioneel HTTP/HTTPS-firewall of proxy apparaat te routeren.  In dit geval moet u de *SourceAddressPrefix* instellen op een andere waarde, bijvoorbeeld het IP-adres bereik van ontwikkel aars-of implementatie computers waarop FTP-clients worden uitgevoerd. 

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP

(**Opmerking:** het poort bereik van het gegevens kanaal kan tijdens de preview-periode veranderen.)

Als externe fout opsporing met Visual Studio wordt gebruikt, demonstreert de volgende regels hoe u toegang kunt verlenen.  Er is een afzonderlijke regel voor elke ondersteunde versie van Visual Studio, aangezien elke versie een andere poort gebruikt voor fout opsporing op afstand.  Net als bij FTP-toegang kan het verkeer voor externe fout opsporing niet goed stromen via een traditioneel WAF of proxy apparaat.  De *SourceAddressPrefix* kan in plaats daarvan worden ingesteld op het IP-adres bereik van ontwikkel machines met Visual Studio.

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Een netwerk beveiligings groep toewijzen aan een subnet
Een netwerk beveiligings groep heeft een standaard beveiligings regel waarmee de toegang tot alle externe verkeer wordt geweigerd.  Het resultaat van het combi neren van de netwerk beveiligings regels die hierboven worden beschreven en de standaard beveiligings regel die binnenkomend verkeer blokkeert, is dat alleen verkeer van bronbereiken die zijn gekoppeld aan een actie *toestaan* , verkeer kan verzenden naar apps die worden uitgevoerd in een app service environment.

Nadat een netwerk beveiligings groep is gevuld met beveiligings regels, moet deze worden toegewezen aan het subnet met de App Service Environment.  De toewijzings opdracht verwijst naar zowel de naam van het virtuele netwerk waar de App Service Environment zich bevindt, evenals de naam van het subnet waarin de App Service Environment is gemaakt.  

In het onderstaande voor beeld ziet u een netwerk beveiligings groep die wordt toegewezen aan een subnet en een virtueel netwerk:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

Zodra de toewijzing van de netwerk beveiligings groep is geslaagd (de toewijzing is een langlopende bewerking en kan een paar minuten duren), worden alleen de regels voor binnenkomend verkeer dat overeenkomt met *toestaan* , apps in het app service Environment bereikt.

Voor volledigheid ziet u in het volgende voor beeld hoe u de netwerk beveiligings groep verwijdert en dus DIS koppelt aan het subnet:

    Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'

## <a name="special-considerations-for-explicit-ip-ssl"></a>Speciale overwegingen voor expliciete IP-SSL
Als een app is geconfigureerd met een expliciet IP-SSL-adres ( *alleen* van toepassing op as met een openbaar VIP), in plaats van het standaard IP-adres van de app service Environment, worden zowel http-als HTTPS-verkeer naar het subnet stromen via een andere set andere poorten dan de poorten 80 en 443.

Het afzonderlijke paar poorten dat door elk IP-SSL-adres wordt gebruikt, kunt u vinden in de gebruikers interface van de portal op de Blade Details van de App Service Environment UX.  Selecteer alle instellingen--> IP-adressen.  De Blade IP-adressen bevat een tabel met alle expliciet geconfigureerde IP-SSL-adressen voor de App Service Environment, samen met het speciale poort paar dat wordt gebruikt voor het routeren van HTTP-en HTTPS-verkeer dat is gekoppeld aan elk IP-SSL-adres.  Het is dit poort paar dat moet worden gebruikt voor de DestinationPortRange-para meters bij het configureren van regels in een netwerk beveiligings groep.

Wanneer een app op een ASE is geconfigureerd voor het gebruik van IP-SSL, zullen externe klanten geen zorgen hoeven te maken over de speciale poort paar toewijzing.  Verkeer naar de apps loopt normaal naar het geconfigureerde IP-SSL-adres.  De vertaling naar het speciale poort paar wordt automatisch intern uitgevoerd tijdens de laatste fase van het routeren van verkeer naar het subnet met de ASE. 

## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot app service Environment][IntroToAppServiceEnvironment] om aan de slag te gaan met app service omgevingen

Zie voor meer informatie over apps die veilig verbinding maken met de back-end-bron van een App Service Environment [veilig verbinding maken met back-end-bronnen vanuit een app service Environment][SecurelyConnecttoBackend]

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->

