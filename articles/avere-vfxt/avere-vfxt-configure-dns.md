---
title: AVERE vFXT DNS-Azure
description: Een DNS-server configureren voor Round-Robin taak verdeling met avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: 11ff310dae3c4733283d965a518df42a0711ce01
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75416045"
---
# <a name="avere-cluster-dns-configuration"></a>DNS-configuratie voor Avere-cluster

In deze sectie worden de basis beginselen uitgelegd van het configureren van een DNS-systeem voor taak verdeling van uw avere vFXT-cluster.

Dit document bevat *geen* instructies voor het instellen en beheren van een DNS-server in de Azure-omgeving.

In plaats van Round-Robin DNS te gebruiken om een vFXT-cluster in azure te verdelen, kunt u hand matige methoden gebruiken om IP-adressen gelijkmatig toe te wijzen aan clients wanneer deze zijn gekoppeld. Er worden verschillende methoden beschreven in [Koppel het avere-cluster](avere-vfxt-mount-clients.md).

Houd bij het bepalen van het gebruik van een DNS-server het volgende in de hand:

* Als uw systeem alleen door NFS-clients wordt gebruikt, is het gebruik van DNS niet vereist. het is mogelijk om alle netwerk adressen op te geven met behulp van numerieke IP-adressen.

* Als uw systeem ondersteuning biedt voor SMB (CIFS), is DNS vereist, omdat u een DNS-domein moet opgeven voor de Active Directory-server.

* DNS is vereist als u Kerberos-verificatie wilt gebruiken.

## <a name="load-balancing"></a>Taakverdeling

Als u de algehele belasting wilt distribueren, moet u uw DNS-domein configureren voor het gebruik van Round-Robin load distributie voor client gerichte IP-adressen.

## <a name="configuration-details"></a>Configuratiegegevens

Wanneer clients toegang hebben tot het cluster, balanceert RRDNS automatisch hun aanvragen over alle beschik bare interfaces.

Voor optimale prestaties kunt u uw DNS-server configureren voor het afhandelen van cluster adressen op de client, zoals wordt weer gegeven in het volgende diagram.

Er wordt aan de linkerkant een cluster-vserver weer gegeven en IP-adressen worden weer gegeven in het midden en aan de rechter kant. Configureer elk client toegangs punt met een record en pointers zoals geïllustreerd.

![avere Cluster Round-Robin DNS-diagram](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Elk IP-adres dat aan de client is gericht, moet een unieke naam hebben voor intern gebruik door het cluster. (In dit diagram worden de IP-adressen van de client de naam VS1-client-IP-* voor duidelijkheid, maar in productie moet u waarschijnlijk een beknoptere, zoals client *) gebruiken.

Clients koppelen het cluster met de naam vserver als server argument.

Wijzig het ``named.conf``-bestand van uw DNS-server om de cyclische volg orde voor query's naar uw vserver in te stellen. Deze optie zorgt ervoor dat alle beschik bare waarden worden gerecycled. Voeg een instructie toe zoals de volgende:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

De volgende ``nsupdate`` opdrachten bieden een voor beeld van het correct configureren van DNS:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>DNS-instellingen van cluster

Geef de DNS-server op die het vFXT-cluster gebruikt op de pagina **cluster** > netwerk instellingen voor **beheer** . De instellingen op die pagina zijn onder andere:

* DNS-server adres
* DNS-domein naam
* DNS-Zoek domeinen

Lees de [DNS-instellingen](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) in de avere-cluster configuratie handleiding voor meer informatie over het gebruik van deze pagina.
