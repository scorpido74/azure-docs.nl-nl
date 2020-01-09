---
title: Azure Cloud Services NetworkConfiguration-schema | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 28
author: tgore03
ms.author: tagore
ms.openlocfilehash: cacc8b1f2909965594fdf0d841963e792acf648c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385421"
---
# <a name="azure-cloud-services-config-networkconfiguration-schema"></a>Azure Cloud Services Config NetworkConfiguration Schema

Het `NetworkConfiguration`-element van het configuratie bestand van de service specificeert Virtual Network-en DNS-waarden. Deze instellingen zijn optioneel voor Cloud Services.

U kunt de volgende resource gebruiken voor meer informatie over virtuele netwerken en de bijbehorende schema's:

- [Configuratie schema van Cloud service (klassiek)](schema-cscfg-file.md)
- [Schema voor Cloud service (klassiek)](schema-csdef-file.md)
- [Een Virtual Network maken (klassiek)](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)

## <a name="networkconfiguration-element"></a>NetworkConfiguration-element
In het volgende voor beeld ziet u het `NetworkConfiguration` element en de onderliggende elementen ervan.

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

In de volgende tabel worden de onderliggende elementen van het element `NetworkConfiguration` beschreven.

| Element       | Beschrijving |
| ------------- | ----------- |
| AccessControl | Optioneel. Hiermee geeft u de regels voor toegang tot eind punten in een Cloud service. De naam van het toegangs beheer wordt gedefinieerd door een teken reeks voor het kenmerk `name`. Het `AccessControl`-element bevat een of meer `Rule` elementen. Er kan meer dan één `AccessControl` element worden gedefinieerd.|
| Regel | Optioneel. Hiermee geeft u de actie op die moet worden uitgevoerd voor een opgegeven subnet-IP-adres bereik. De volg orde van de regel wordt gedefinieerd door een teken reeks waarde voor het kenmerk `order`. Hoe lager de regel, hoe hoger de prioriteit. U kunt bijvoorbeeld regels opgeven met order nummers van 100, 200 en 300. De regel met het order nummer 100 heeft voor rang op de regel met een bestelling van 200.<br /><br /> De actie voor de regel wordt gedefinieerd door een teken reeks voor het kenmerk `action`. Mogelijke waarden zijn:<br /><br /> -   `permit`: Hiermee geeft u op dat alleen pakketten van het opgegeven subnet-bereik kunnen communiceren met het eind punt.<br />-   `deny`: Hiermee geeft u op dat toegang wordt geweigerd tot de eind punten in het opgegeven subnet-bereik.<br /><br /> Het subnetten van IP-adressen die worden beïnvloed door de regel, worden gedefinieerd door een teken reeks voor het kenmerk `remoteSubnet`. De beschrijving voor de regel wordt gedefinieerd door een teken reeks voor het kenmerk `description`.|
| EndpointAcl | Optioneel. Hiermee geeft u de toewijzing van regels voor toegangs beheer aan een eind punt op. De naam van de rol die het eind punt bevat, wordt gedefinieerd door een teken reeks voor het kenmerk `role`. De naam van het eind punt wordt gedefinieerd door een teken reeks voor het kenmerk `endpoint`. De naam van de set `AccessControl` regels die op het eind punt moet worden toegepast, wordt gedefinieerd in een teken reeks voor het kenmerk `accessControl`. Er kunnen meerdere `EndpointAcl` elementen worden gedefinieerd.|
| DnsServer | Optioneel. Hiermee geeft u de instellingen voor een DNS-server. U kunt instellingen opgeven voor DNS-servers zonder een Virtual Network. De naam van de DNS-server wordt gedefinieerd door een teken reeks voor het kenmerk `name`. Het IP-adres van de DNS-server wordt gedefinieerd door een teken reeks voor het kenmerk `IPAddress`. Het IP-adres moet een geldig IPv4-adres zijn.|
| VirtualNetworkSite | Optioneel. Hiermee geeft u de naam op van de Virtual Network site waarin u uw Cloud service wilt implementeren. Met deze instelling wordt geen Virtual Network-site gemaakt. Er wordt verwezen naar een site die eerder in het netwerk bestand voor uw Virtual Network is gedefinieerd. Een Cloud service kan alleen lid zijn van een Virtual Network. Als u deze instelling niet opgeeft, wordt de Cloud service niet geïmplementeerd op een Virtual Network. De naam van de Virtual Network-site wordt gedefinieerd door een teken reeks voor het kenmerk `name`.|
| InstanceAddress | Optioneel. Hiermee geeft u de koppeling van een rol aan een subnet of een set subnetten in de Virtual Network. Wanneer u een rolnaam aan een exemplaar adres koppelt, kunt u de subnetten opgeven waaraan u deze rol wilt koppelen. Het `InstanceAddress` bevat een subnets-element. De naam van de rol die aan het subnet of de subnetten is gekoppeld, wordt gedefinieerd door een teken reeks voor het kenmerk `roleName`.|
| Subnet | Optioneel. Hiermee geeft u het subnet op dat overeenkomt met de naam van het subnet in het netwerk configuratie bestand. De naam van het subnet wordt gedefinieerd door een teken reeks voor het kenmerk `name`.|
| ReservedIP | Optioneel. Hiermee geeft u het gereserveerde IP-adres op dat moet worden gekoppeld aan de implementatie. U moet Gereserveerd IP adres maken gebruiken om het gereserveerde IP-adres te maken. Elke implementatie in een Cloud service kan worden gekoppeld aan één gereserveerd IP-adres. De naam van het gereserveerde IP-adres wordt gedefinieerd door een teken reeks voor het kenmerk `name`.|

## <a name="see-also"></a>Zie ook
[Configuratie schema van Cloud service (klassiek)](schema-cscfg-file.md)
