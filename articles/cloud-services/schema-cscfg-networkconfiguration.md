---
title: Azure Cloud Services NetworkConfiguration-schema | Microsoft Docs
description: Meer informatie over de onderliggende elementen van het element NetworkConfiguration van het service configuratie bestand, waarmee Virtual Network-en DNS-waarden worden opgegeven.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: 695ba3acfd5af8797de6e6f7454e493d7863627c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79529285"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services config NetworkConfiguration-schema

Het `NetworkConfiguration` element van het service configuratie bestand bevat Virtual Network-en DNS-waarden. Deze instellingen zijn optioneel voor Cloud Services.

U kunt de volgende resource gebruiken voor meer informatie over virtuele netwerken en de bijbehorende schema's:

- [Configuratie schema van Cloud service (klassiek)](schema-cscfg-file.md)
- [Schema voor Cloud service (klassiek)](schema-csdef-file.md)
- [Een Virtual Network maken (klassiek)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration-element
In het volgende voor beeld `NetworkConfiguration` ziet u het element en de onderliggende elementen.

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

In de volgende tabel worden de onderliggende elementen van `NetworkConfiguration` het element beschreven.

| Element       | Beschrijving |
| ------------- | ----------- |
| AccessControl | Optioneel. Hiermee geeft u de regels voor toegang tot eind punten in een Cloud service. De naam van het toegangs beheer wordt gedefinieerd door een `name` teken reeks voor het kenmerk. Het `AccessControl` element bevat een of meer `Rule` elementen. Er kan meer `AccessControl` dan één element worden gedefinieerd.|
| Regel | Optioneel. Hiermee geeft u de actie op die moet worden uitgevoerd voor een opgegeven subnet-IP-adres bereik. De volg orde van de regel wordt gedefinieerd door een teken reeks waarde `order` voor het kenmerk. Hoe lager de regel, hoe hoger de prioriteit. U kunt bijvoorbeeld regels opgeven met order nummers van 100, 200 en 300. De regel met het order nummer 100 heeft voor rang op de regel met een bestelling van 200.<br /><br /> De actie voor de regel wordt gedefinieerd door een teken reeks voor `action` het kenmerk. Mogelijke waarden zijn:<br /><br /> -   `permit`-Geeft aan dat alleen pakketten van het opgegeven subnet-bereik kunnen communiceren met het eind punt.<br />-   `deny`-Geeft aan dat de toegang wordt geweigerd tot de eind punten in het opgegeven subnet-bereik.<br /><br /> Het subnetten van IP-adressen die worden beïnvloed door de regel, worden gedefinieerd door een teken `remoteSubnet` reeks voor het kenmerk. De beschrijving voor de regel wordt gedefinieerd door een teken reeks voor `description` het kenmerk.|
| EndpointAcl | Optioneel. Hiermee geeft u de toewijzing van regels voor toegangs beheer aan een eind punt op. De naam van de rol die het eind punt bevat, wordt gedefinieerd door een teken `role` reeks voor het kenmerk. De naam van het eind punt wordt gedefinieerd door een teken reeks `endpoint` voor het kenmerk. De naam van de set `AccessControl` regels die op het eind punt moet worden toegepast, wordt gedefinieerd in een teken reeks `accessControl` voor het kenmerk. Er `EndpointAcl` kunnen meerdere elementen worden gedefinieerd.|
| DnsServer | Optioneel. Hiermee geeft u de instellingen voor een DNS-server. U kunt instellingen opgeven voor DNS-servers zonder een Virtual Network. De naam van de DNS-server wordt gedefinieerd door een teken reeks `name` voor het kenmerk. Het IP-adres van de DNS-server wordt gedefinieerd door een teken `IPAddress` reeks voor het kenmerk. Het IP-adres moet een geldig IPv4-adres zijn.|
| VirtualNetworkSite | Optioneel. Hiermee geeft u de naam op van de Virtual Network site waarin u uw Cloud service wilt implementeren. Met deze instelling wordt geen Virtual Network-site gemaakt. Er wordt verwezen naar een site die eerder in het netwerk bestand voor uw Virtual Network is gedefinieerd. Een Cloud service kan alleen lid zijn van een Virtual Network. Als u deze instelling niet opgeeft, wordt de Cloud service niet geïmplementeerd op een Virtual Network. De naam van de Virtual Network site wordt gedefinieerd door een teken reeks voor `name` het kenmerk.|
| InstanceAddress | Optioneel. Hiermee geeft u de koppeling van een rol aan een subnet of een set subnetten in de Virtual Network. Wanneer u een rolnaam aan een exemplaar adres koppelt, kunt u de subnetten opgeven waaraan u deze rol wilt koppelen. Het `InstanceAddress` element bevat een subnets. De naam van de rol die aan het subnet of de subnetten is gekoppeld, wordt gedefinieerd door een teken `roleName` reeks voor het kenmerk.|
| Subnet | Optioneel. Hiermee geeft u het subnet op dat overeenkomt met de naam van het subnet in het netwerk configuratie bestand. De naam van het subnet wordt gedefinieerd door een teken reeks voor `name` het kenmerk.|
| ReservedIP | Optioneel. Hiermee geeft u het gereserveerde IP-adres op dat moet worden gekoppeld aan de implementatie. U moet Gereserveerd IP adres maken gebruiken om het gereserveerde IP-adres te maken. Elke implementatie in een Cloud service kan worden gekoppeld aan één gereserveerd IP-adres. De naam van het gereserveerde IP-adres wordt gedefinieerd door een teken `name` reeks voor het kenmerk.|

## <a name="see-also"></a>Zie ook
[Configuratie schema van Cloud service (klassiek)](schema-cscfg-file.md)
