---
title: Gelaagde beveiliging v1
description: Meer informatie over het implementeren van een gelaagde beveiligingsarchitectuur in uw App Service-omgeving. Dit document is alleen bedoeld voor klanten die de verouderde v1 ASE gebruiken.
author: stefsch
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: a8920e97d315dc7bfd0ba22386b8b637afb7c05e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688787"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Een gelaagde beveiligingsarchitectuur implementeren met app-serviceomgevingen
Aangezien App-serviceomgevingen een geïsoleerde runtime-omgeving bieden die in een virtueel netwerk wordt geïmplementeerd, kunnen ontwikkelaars een gelaagde beveiligingsarchitectuur maken die verschillende niveaus van netwerktoegang biedt voor elke fysieke toepassingslaag.

Een veel voorkomende wens is om API back-ends te verbergen voor algemene internettoegang, en alleen API's toe te staan om te worden aangeroepen door upstream web apps.  [Netwerkbeveiligingsgroepen (NSG's)][NetworkSecurityGroups] kunnen worden gebruikt op subnetten die App-serviceomgevingen bevatten om de toegang van het publiek tot API-toepassingen te beperken.

Het onderstaande diagram toont een voorbeeldarchitectuur met een WebAPI-gebaseerde app die is geïmplementeerd op een App-serviceomgeving.  Drie afzonderlijke web-app-exemplaren, geïmplementeerd in drie afzonderlijke App-serviceomgevingen, voeren back-endgesprekken naar dezelfde WebAPI-app.

![Conceptuele architectuur][ConceptualArchitecture] 

De groene plus borden geven aan dat het netwerk beveiligingsgroep op het subnet met "apiase" maakt inkomende gesprekken van de upstream web apps, evenals oproepen van zichzelf.  Dezelfde netwerkbeveiligingsgroep weigert echter expliciet toegang tot algemeen binnenkomend verkeer vanaf het internet. 

De rest van dit artikel loopt door de stappen die nodig zijn om de netwerkbeveiligingsgroep te configureren op het subnet met 'apiase'.

## <a name="determining-the-network-behavior"></a>Het netwerkgedrag bepalen
Om te weten welke netwerkbeveiligingsregels nodig zijn, moet u bepalen welke netwerkclients de App-serviceomgeving met de API-app mogen bereiken en welke clients worden geblokkeerd.

Aangezien [netwerkbeveiligingsgroepen (NSG's)][NetworkSecurityGroups] worden toegepast op subnetten en App-serviceomgevingen worden geïmplementeerd in subnetten, zijn de regels in een NSG van toepassing op **alle** apps die worden uitgevoerd op een App Service-omgeving.  Met behulp van de voorbeeldarchitectuur voor dit artikel worden alle apps die op de 'apiase' worden uitgevoerd, beschermd door dezelfde set beveiligingsregels zodra een netwerkbeveiligingsgroep wordt toegepast op het subnet dat 'apiase' bevat. 

* **Bepaal het uitgaande IP-adres van upstream-bellers:**  Wat is het IP-adres of de adressen van de upstream bellers?  Deze adressen moeten expliciet toegang krijgen tot de NSG.  Aangezien oproepen tussen App-serviceomgevingen worden beschouwd als "internet"-oproepen, moet het uitgaande IP-adres dat is toegewezen aan elk van de drie upstream App Service-omgevingen toegang krijgen in de NSG voor het subnet "apiase".   Zie het artikel Over het overzicht van het [netwerkarchitectuuroverzicht][NetworkArchitecture] voor meer informatie over het bepalen van het uitgaande IP-adres voor apps die in een App-serviceomgeving worden uitgevoerd.
* **Moet de back-end API-app zichzelf aanroepen?**  Een soms over het hoofd gezien en subtiel punt is het scenario waarin de back-end applicatie zichzelf moet noemen.  Als een back-end API-toepassing op een App Service-omgeving zichzelf moet aanroepen, wordt deze ook behandeld als een "internet"-gesprek.  In de voorbeeldarchitectuur moet ook toegang worden toegestaan vanaf het uitgaande IP-adres van de "apiase" App Service-omgeving.

## <a name="setting-up-the-network-security-group"></a>De netwerkbeveiligingsgroep instellen
Zodra de set uitgaande IP-adressen bekend is, is de volgende stap het bouwen van een netwerkbeveiligingsgroep.  Netwerkbeveiligingsgroepen kunnen worden gemaakt voor zowel op Resource Manager gebaseerde virtuele netwerken als voor klassieke virtuele netwerken.  De onderstaande voorbeelden tonen het maken en configureren van een NSG op een klassiek virtueel netwerk met Powershell.

Voor de voorbeeldarchitectuur bevinden de omgevingen zich in South Central US, zodat er in die regio een lege NSG wordt gemaakt:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Eerst wordt een expliciete regel voor toestaan toegevoegd voor de Azure-beheerinfrastructuur, zoals vermeld in het artikel over [binnenkomend verkeer][InboundTraffic] voor App-serviceomgevingen.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

Vervolgens worden twee regels toegevoegd om HTTP- en HTTPS-oproepen toe te staan vanaf de eerste upstream-appserviceomgeving ('fe1ase').

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Spoelen en herhalen voor de tweede en derde upstream App Service Environments ("fe2ase" en "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Ten slotte u toegang verlenen tot het uitgaande IP-adres van de App Service-omgeving van de back-end API, zodat deze weer in zichzelf kan terugbellen.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Er zijn geen andere netwerkbeveiligingsregels vereist, omdat elke NSG standaardregels heeft die binnenkomende toegang vanaf het internet standaard blokkeren.

De volledige lijst met regels in de netwerkbeveiligingsgroep wordt hieronder weergegeven.  Houd er rekening mee hoe de laatste regel, die is gemarkeerd, binnenkomende toegang blokkeert van alle bellers, met uitzondering van bellers die expliciet toegang hebben gekregen.

![NSG-configuratie][NSGConfiguration] 

De laatste stap is om de NSG toe te passen op het subnet dat de "apiase" App Service Environment bevat.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Met de NSG toegepast op het subnet, alleen de drie upstream App Service Environments, en de App Service Environment met de API back-end, mogen bellen in de "apiase" omgeving.

## <a name="additional-links-and-information"></a>Aanvullende koppelingen en -informatie
Informatie over [netwerkbeveiligingsgroepen](../../virtual-network/security-overview.md).

Inzicht in [uitgaande IP-adressen][NetworkArchitecture] en App-serviceomgevingen.

[Netwerkpoorten][InboundTraffic] die worden gebruikt door App-serviceomgevingen.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
