---
title: Azure Cloud Services NetworkConfiguration Schema | Microsoft Documenten
description: Meer informatie over de onderliggende elementen van het element NetworkConfiguration van het serviceconfiguratiebestand, waarin virtuele netwerk- en DNS-waarden worden opgegeven.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: 695ba3acfd5af8797de6e6f7454e493d7863627c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529285"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services Config NetworkConfiguration Schema

Het `NetworkConfiguration` element van het serviceconfiguratiebestand geeft virtuele netwerk- en DNS-waarden op. Deze instellingen zijn optioneel voor cloudservices.

U de volgende bron gebruiken voor meer informatie over virtuele netwerken en de bijbehorende schema's:

- [Cloud Service (klassiek) configuratieschema](schema-cscfg-file.md)
- [Cloud Service (klassiek) definitieschema](schema-csdef-file.md)
- [Een virtueel netwerk maken (klassiek)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>Element netwerkconfiguratie
In het volgende `NetworkConfiguration` voorbeeld worden het element en de onderliggende elementen weergegeven.

```xml
<ServiceConfiguration>
  <NetworkConfiguration>
    <AccessControls>
      <AccessControl name="aclName1">
        <Rule order="<rule-order>" action="<rule-action>" remoteSubnet="<subnet-address>" description="rule-description"/>
      </AccessControl>
    </AccessControls>
    <EndpointAcls>
      <EndpointAcl role="<role-name>" endpoint="<endpoint-name>" accessControl="<acl-name>"/>
    </EndpointAcls>
    <Dns>
      <DnsServers>
        <DnsServer name="<server-name>" IPAddress="<server-address>" />
      </DnsServers>
    </Dns>
    <VirtualNetworkSite name="Group <RG-VNet> <VNet-name>"/>
    <AddressAssignments>
      <InstanceAddress roleName="<role-name>">
        <Subnets>
          <Subnet name="<subnet-name>"/>
        </Subnets>
      </InstanceAddress>
      <ReservedIPs>
        <ReservedIP name="<reserved-ip-name>"/>
      </ReservedIPs>
    </AddressAssignments>
  </NetworkConfiguration>
</ServiceConfiguration>
```

In de volgende tabel worden `NetworkConfiguration` de onderliggende elementen van het element beschreven.

| Element       | Beschrijving |
| ------------- | ----------- |
| AccessControl | Optioneel. Hiermee geeft u de regels op voor toegang tot eindpunten in een cloudservice. De naam van het toegangsbeheer `name` wordt gedefinieerd door een tekenreeks voor het kenmerk. Het `AccessControl` element bevat `Rule` een of meer elementen. Er kan `AccessControl` meer dan één element worden gedefinieerd.|
| Regel | Optioneel. Hiermee geeft u de actie op die moet worden uitgevoerd voor een bepaald subnetbereik van IP-adressen. De volgorde van de regel wordt gedefinieerd `order` door een tekenreekswaarde voor het kenmerk. Hoe lager het regelnummer, hoe hoger de prioriteit. Regels kunnen bijvoorbeeld worden opgegeven met ordernummers van 100, 200 en 300. De regel met het ordernummer van 100 heeft voorrang op de regel die een order van 200 heeft.<br /><br /> De actie voor de regel wordt gedefinieerd `action` door een tekenreeks voor het kenmerk. Mogelijke waarden zijn:<br /><br /> -   `permit`– Hiermee geeft u op dat alleen pakketten uit het opgegeven subnetbereik met het eindpunt kunnen communiceren.<br />-   `deny`– Hiermee geeft u op dat de toegang wordt geweigerd tot de eindpunten in het opgegeven subnetbereik.<br /><br /> Het subnetbereik van IP-adressen dat wordt beïnvloed door de `remoteSubnet` regel, wordt gedefinieerd door een tekenreeks voor het kenmerk. De beschrijving voor de regel wordt gedefinieerd `description` door een tekenreeks voor het kenmerk.|
| EindpuntAcl | Optioneel. Hiermee geeft u de toewijzing van toegangscontroleregels op naar een eindpunt. De naam van de rol die het eindpunt bevat, `role` wordt gedefinieerd door een tekenreeks voor het kenmerk. De naam van het eindpunt wordt gedefinieerd `endpoint` door een tekenreeks voor het kenmerk. De naam van `AccessControl` de set regels die op het eindpunt moet worden `accessControl` toegepast, wordt gedefinieerd in een tekenreeks voor het kenmerk. Er kunnen `EndpointAcl` meer dan één elementen worden gedefinieerd.|
| DnsServer | Optioneel. Hiermee geeft u de instellingen voor een DNS-server op. U instellingen opgeven voor DNS-servers zonder virtueel netwerk. De naam van de DNS-server wordt `name` gedefinieerd door een tekenreeks voor het kenmerk. Het IP-adres van de DNS-server wordt `IPAddress` gedefinieerd door een tekenreeks voor het kenmerk. Het IP-adres moet een geldig IPv4-adres zijn.|
| VirtualNetworkSite VirtualNetworkSite | Optioneel. Hiermee geeft u de naam op van de site voor virtueel netwerk waarin u uw cloudservice wilt implementeren. Met deze instelling wordt geen virtuele netwerksite gemaakt. Het verwijst naar een site die eerder is gedefinieerd in het netwerkbestand voor uw virtuele netwerk. Een cloudservice kan alleen lid zijn van één virtueel netwerk. Als u deze instelling niet opgeeft, wordt de cloudservice niet geïmplementeerd in een virtueel netwerk. De naam van de virtuele netwerksite wordt `name` gedefinieerd door een tekenreeks voor het kenmerk.|
| InstanceAdres | Optioneel. Hiermee geeft u de koppeling van een rol op met een subnet of een set subnetten in het virtuele netwerk. Wanneer u een rolnaam koppelt aan een instantieadres, u de subnetten opgeven waaraan deze rol moet worden gekoppeld. Het `InstanceAddress` bevat een subnets-element. De naam van de rol die is gekoppeld aan het subnet of `roleName` subnetten wordt gedefinieerd door een tekenreeks voor het kenmerk.|
| Subnet | Optioneel. Hiermee geeft u het subnet op dat overeenkomt met de subnetnaam in het netwerkconfiguratiebestand. De naam van het subnet wordt gedefinieerd `name` door een tekenreeks voor het kenmerk.|
| Gereserveerde IP | Optioneel. Hiermee geeft u het gereserveerde IP-adres op dat aan de implementatie moet worden gekoppeld. U moet Gereserveerd IP-adres maken gebruiken om het gereserveerde IP-adres te maken. Elke implementatie in een cloudservice kan worden gekoppeld aan één gereserveerd IP-adres. De naam van het gereserveerde IP-adres `name` wordt gedefinieerd door een tekenreeks voor het kenmerk.|

## <a name="see-also"></a>Zie ook
[Cloud Service (klassiek) configuratieschema](schema-cscfg-file.md)
