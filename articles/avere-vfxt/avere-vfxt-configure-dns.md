---
title: Avere vFXT DNS - Azure
description: Een DNS-server configureren voor round-robin load balancing met Avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153782"
---
# <a name="avere-cluster-dns-configuration"></a>DNS-configuratie voor Avere-cluster

In dit gedeelte worden de basisprincipes van het configureren van een DNS-systeem voor het balanceren van uw Avere vFXT-cluster uitgelegd.

Dit document *bevat geen* instructies voor het instellen en beheren van een DNS-server in de Azure-omgeving.

In plaats van round-robin DNS te gebruiken om een vFXT-cluster in Azure te laden, u overwegen om handmatige methoden te gebruiken om IP-adressen gelijkmatig toe te wijzen aan clients wanneer deze zijn gemonteerd. Verschillende methoden worden beschreven in [Mount de Avere cluster](avere-vfxt-mount-clients.md).

Houd rekening met deze dingen bij de beslissing om al dan niet een DNS-server te gebruiken:

* Als uw systeem alleen toegankelijk is voor NFS-clients, is het gebruik van DNS niet vereist - het is mogelijk om alle netwerkadressen op te geven met behulp van numerieke IP-adressen.

* Als uw systeem SMB-toegang (CIFS) ondersteunt, is DNS vereist, omdat u een DNS-domein moet opgeven voor de Active Directory-server.

* DNS is vereist als u Kerberos-verificatie wilt gebruiken.

## <a name="load-balancing"></a>Taakverdeling

Als u de algehele belasting wilt distribueren, configureert u uw DNS-domein om de round-robin-belastingdistributie te gebruiken voor ip-adressen die naar client gericht zijn.

## <a name="configuration-details"></a>Configuratiegegevens

Wanneer clients toegang krijgen tot het cluster, balanceert RRDNS hun aanvragen automatisch tussen alle beschikbare interfaces.

Configureer uw DNS-server voor optimale prestaties om clusteradressen die naar client gericht zijn te verwerken, zoals in het volgende diagram wordt weergegeven.

Een clustervserver wordt links weergegeven en IP-adressen worden in het midden en aan de rechterkant weergegeven. Configureer elk toegangspunt voor de client met A-records en aanwijzers zoals geïllustreerd.

![Avere cluster round-robin DNS diagram](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Elk IP-adres dat naar de client gericht is, moet een unieke naam hebben voor intern gebruik door het cluster. (In dit diagram worden de IP's van de client vs1-client-IP-* genoemd voor duidelijkheid, maar in de productie moet u waarschijnlijk iets beknopter gebruiken, zoals client*.)

Clients monteren het cluster met de vservernaam als het argument server.

Wijzig het bestand ``named.conf`` van uw DNS-server om een cyclische volgorde voor query's in te stellen op uw vserver. Deze optie zorgt ervoor dat alle beschikbare waarden worden doorgefietst. Voeg een instructie als volgt toe:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

De ``nsupdate`` volgende opdrachten geven een voorbeeld van het correct configureren van DNS:

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

## <a name="cluster-dns-settings"></a>DNS-instellingen voor cluster

Geef de DNS-server op die het vFXT-cluster gebruikt op de pagina > **Clusterbeheernetwerkinstellingen.** **Cluster** Instellingen op die pagina zijn onder andere:

* DNS-serveradres
* DNS-domeinnaam
* DNS-zoekdomeinen

Lees [DNS-instellingen](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) in de Avere Cluster Configuration Guide voor meer informatie over het gebruik van deze pagina.
