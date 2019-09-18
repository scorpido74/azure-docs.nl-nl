---
title: DNS-instellingen opgeven in een service configuratie bestand | Microsoft Docs
description: aangepaste DNS-instellingen opgeven met het service configuratie bestand voor het virtuele netwerk
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: f27802d76a8b94a0d5f1eb0c35fd55c93712e557
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059067"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>DNS-instellingen in een service configuratie bestand opgeven
## <a name="dns-elements"></a>DNS-elementen
Een service configuratie bestand kan een DnsServers-element bevatten met een lijst met IPv4-adressen voor de Domain Name System (DNS)-servers die door de service worden gebruikt. Instellingen in het service configuratie bestand hebben voor rang op instellingen in het netwerk configuratie bestand. Zie [Azure service configuration schema (cscfg-bestand)](https://msdn.microsoft.com/library/azure/ee758710.aspx)voor meer informatie.

**NetworkConfiguration-element**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> Het kenmerk **name** in het element **DnsServer** wordt alleen gebruikt als verwijzings naam. De naam voor de DNS-server komt niet overeen met de hostnaam. Elke waarde van het **DnsServer** -kenmerk moet uniek zijn binnen het gehele Microsoft Azure-abonnement.
> 
> 

## <a name="see-also"></a>Zie ook
[Configuratie schema van Azure-service (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Configuratie schema voor Azure Virtual Network](https://go.microsoft.com/fwlink/?LinkId=248093)

[Een Virtual Network configureren met behulp van netwerk configuratie bestanden](https://go.microsoft.com/fwlink/?LinkId=248094)

[Over Virtual Network instellingen in de Beheerportal](https://go.microsoft.com/fwlink/?LinkId=248092)

