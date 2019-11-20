---
title: DNS-instellingen in azure VNet-configuratie bestand opgeven
description: DNS-server instellingen wijzigen in een virtueel netwerk met behulp van een configuratie bestand voor een virtueel netwerk in het klassieke implementatie model
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: db8b1817bb14d293632d16fe02792dbb85766559
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196644"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>DNS-instellingen opgeven in een virtueel-netwerk configuratie bestand
Een netwerk configuratie bestand heeft twee elementen die u kunt gebruiken om Domain Name System (DNS)-instellingen op te geven: **DnsServers** en **DnsServerRef**. U kunt een lijst met DNS-servers toevoegen door hun IP-adressen en referentie namen op te geven in het **DnsServers** -element. U kunt vervolgens een **DnsServerRef** -element gebruiken om op te geven welke DNS-server vermeldingen van het DnsServers-element worden gebruikt voor verschillende netwerk sites in uw virtuele netwerk.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Dit artikel is van toepassing op het klassieke implementatiemodel.

Het netwerk configuratie bestand bevat mogelijk de volgende elementen. De titel van elk element is gekoppeld aan een pagina met aanvullende informatie over de waarde-instellingen van het element.

> [!IMPORTANT]
> Voor informatie over het configureren van het netwerk configuratie bestand, Zie [een Virtual Network configureren met behulp van een netwerk configuratie bestand](virtual-networks-using-network-configuration-file.md). Voor informatie over elk element in het netwerk configuratie bestand, Zie [Azure Virtual Network-configuratie schema](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[DNS-element](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> Het kenmerk **name** in het element **DnsServer** wordt alleen gebruikt als verwijzing voor het element **DnsServerRef** . De naam voor de DNS-server komt niet overeen met de hostnaam. Elke waarde van het **DnsServer** -kenmerk moet uniek zijn binnen het gehele Microsoft Azure-abonnement
> 
> 

[Element Virtual Network sites](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Als u deze instelling voor het element Virtual Network sites wilt opgeven, moet dit eerst worden gedefinieerd in het DNS-element. De *naam* van de DnsServerRef in het element Virtual Network sites moet verwijzen naar een naam waarde die is opgegeven in het element DNS voor de *naam*DnsServer.
> 
> 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [Azure Virtual Network-configuratie schema](https://go.microsoft.com/fwlink/?LinkId=248093).
* Meer informatie over het [Configuratie schema](https://msdn.microsoft.com/library/windowsazure/ee758710)van de Azure-service.
* [Configureer een virtueel netwerk met behulp van netwerk configuratie bestanden](virtual-networks-using-network-configuration-file.md).

