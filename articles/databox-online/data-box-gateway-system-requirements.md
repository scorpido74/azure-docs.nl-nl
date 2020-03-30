---
title: Systeemvereisten voor Microsoft Azure Data Box Gateway| Microsoft Documenten
description: Meer informatie over de software- en netwerkvereisten voor uw Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 05/08/2019
ms.author: alkohli
ms.openlocfilehash: e8932097bcdef782b1a551d386c2872e02d8abfd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260187"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Systeemvereisten voor Azure Data Box Gateway

In dit artikel worden de belangrijke systeemvereisten beschreven voor uw Microsoft Azure Data Box Gateway-oplossing en voor de clients die verbinding maken met Azure Data Box Gateway. We raden u aan de informatie zorgvuldig te controleren voordat u uw Data Box Gateway implementeert en deze vervolgens terug te verwijzen als dat nodig is tijdens de implementatie en de daaropvolgende bewerking.

De systeemvereisten voor het virtuele apparaat Van de Data Box Gateway omvatten:

- **Softwarevereisten voor hosts** - beschrijft de ondersteunde platforms, browsers voor de lokale configuratie-gebruikersinterface, SMB-clients en eventuele aanvullende vereisten voor de hosts die verbinding maken met het apparaat.
- **Netwerkvereisten voor het apparaat** - biedt informatie over eventuele netwerkvereisten voor de werking van het virtuele apparaat.


## <a name="specifications-for-the-virtual-device"></a>Specificaties voor het virtuele apparaat

Het onderliggende hostsysteem voor de Data Box Gateway kan de volgende bronnen besteden aan het inrichten van uw virtuele apparaat:

| Specificaties                                          | Beschrijving              |
|---------------------------------------------------------|--------------------------|
| Virtuele processors (kernen)   | Minimaal 4 |
| Geheugen  | Minimaal 8 GB|
| Beschikbaarheid|Eén knooppunt|
| Disks| Besturingssysteemschijf: 250 GB <br> Gegevensschijf: minimaal 2 TB, thin-provisioned en moet worden ondersteund door SSD's|
| Netwerkinterfaces|1 of meer virtuele netwerkinterfaces|


## <a name="supported-os-for-clients-connected-to-device"></a>Ondersteund besturingssysteem voor clients die zijn verbonden met het apparaat

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Ondersteunde protocollen voor clients die toegang hebben tot het apparaat

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Ondersteunde virtualisatieplatforms voor apparaten

| **Besturingssysteem/platform**  |**Versies**   |**Opmerkingen**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |VMware-tools worden niet ondersteund.         |


## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Ondersteunde opslagtypen

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Ondersteunde browsers voor lokale webgebruikersinterface

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Vereisten voor netwerkpoorten

In de volgende tabel worden de poorten weergegeven die in uw firewall moeten worden geopend om smb-, cloud- of beheerverkeer mogelijk te maken. In deze tabel verwijst *in* of *inbound* naar de richting van waaruit binnenkomende client toegang tot uw apparaat vraagt. *Out-* of *outbound* verwijst naar de richting waarin uw Data Box Gateway-apparaat gegevens extern verzendt, buiten de implementatie: bijvoorbeeld uitgaand naar het internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>URL-patronen voor firewallregels

Netwerkbeheerders kunnen vaak geavanceerde firewallregels configureren op basis van de URL-patronen om het binnenkomende en het uitgaande verkeer te filteren. Uw Data Box Gateway-apparaat en de Data Box Gateway-service zijn afhankelijk van andere Microsoft-toepassingen, zoals Azure Service Bus, Azure Active Directory Access Control, opslagaccounts en Microsoft Update-servers. De URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen worden gebruikt om firewallregels te configureren. Het is belangrijk om te begrijpen dat de URL-patronen die aan deze toepassingen zijn gekoppeld, kunnen veranderen. Dit vereist op zijn beurt dat de netwerkbeheerder firewallregels voor uw Data Box Gateway controleert en bijwerkt wanneer dat nodig is.

We raden u aan uw firewallregels voor uitgaand verkeer in te stellen op basis van vaste IP-adressen van Data Box Gateway, in de meeste gevallen. U de onderstaande informatie echter gebruiken om geavanceerde firewallregels in te stellen die nodig zijn om beveiligde omgevingen te maken.

> [!NOTE]
> - De IP's van het apparaat (bron) moeten altijd worden ingesteld op alle netwerkinterfaces met cloudvoorziening.
> - De ip-bereiken van de bestemming moeten worden ingesteld op [IP-bereiken voor Azure-datacenters.](https://www.microsoft.com/download/confirmation.aspx?id=41653)

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>URL-patronen voor Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Internetbandbreedte

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Volgende stap

* [Uw Azure Data Box Gateway implementeren](data-box-gateway-deploy-prep.md)

