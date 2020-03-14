---
title: 'Zelf studie: netwerk configureren in een Azure FXT Edge-bestands cluster'
description: Netwerk instellingen aanpassen na het maken van het Azure FXT Edge-bestands cluster
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: a40ff50dcb0934cbf1ea5222675bd75948ac1d03
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239796"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Zelf studie: de netwerk instellingen van het cluster configureren

Voordat u een nieuw gemaakt Azure FXT Edge-cluster gebruikt, moet u verschillende netwerk instellingen voor uw werk stroom controleren en aanpassen. 

In deze zelf studie worden de netwerk instellingen beschreven die u mogelijk moet aanpassen voor een nieuw cluster. 

U leert: 

> [!div class="checklist"]
> * Welke netwerk instellingen moeten mogelijk worden bijgewerkt na het maken van een cluster
> * Voor welke Azure FXT Edge-bestanden use-cases een AD-server of een DNS-server vereisen 
> * Round-Robin DNS (RRDNS) configureren om client aanvragen automatisch te verdelen over het FXT-cluster

De hoeveelheid tijd die nodig is om deze stappen uit te voeren, is afhankelijk van het aantal configuratie wijzigingen dat nodig is in uw systeem:

* Als u de zelf studie alleen wilt lezen en een paar instellingen wilt controleren, duurt het 10 tot 15 minuten. 
* Als u round-robin DNS moet configureren, kan deze taak een uur of langer duren.

## <a name="adjust-network-settings"></a>Netwerk instellingen aanpassen

Verschillende taken die betrekking hebben op het netwerk maken deel uit van het instellen van een nieuw Azure FXT Edge-bestands cluster. Controleer deze lijst en bepaal welke items van toepassing zijn op uw systeem.

Lees voor meer informatie over netwerk instellingen voor het cluster [netwerk services configureren](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) in de cluster configuratie handleiding.

* Round-Robin DNS voor het client gerichte netwerk configureren (optioneel)

  Taak verdeling van het cluster verkeer door het DNS-systeem te configureren, zoals beschreven in [DNS configureren voor het FXT Edge-bestands cluster](#configure-dns-for-load-balancing).

* NTP-instellingen verifiëren

* Down loads van Active Directory en naam/groeps naam configureren (indien nodig)

  Als uw netwerkhosts Active Directory of een ander type externe adreslijst service gebruiken, moet u de configuratie van de Directory Services van het cluster aanpassen om in te stellen hoe het cluster gebruikers naam en groeps informatie downloadt. Lees de **cluster** - > **Directory Services** in de cluster configuratie handleiding voor meer informatie.

  Een AD-server is vereist als u SMB-ondersteuning wilt. Configureer AD voordat u SMB gaat instellen.

* VLAN'S definiëren (optioneel)
  
  Configureer alle extra VLAN'S die nodig zijn voordat u de vservers en globale naam ruimte van uw cluster definieert. Lees meer over het [werken met vlan's](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) in de cluster configuratie handleiding voor meer informatie.

* Proxy servers configureren (indien nodig)

  Als uw cluster een proxy server gebruikt om externe adressen te bereiken, voert u de volgende stappen uit om deze in te stellen:

  1. Definieer de proxy server op de pagina **proxy configuratie** -instellingen
  1. Pas de configuratie van de proxy server toe met de pagina **Cluster** > **algemene installatie** of de pagina Details van de **kern bestanden** .
  
  Lees voor meer informatie [met behulp van web-proxy's](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) in de cluster configuratie handleiding.

* [Versleutelings certificaten](#encryption-certificates) uploaden voor het cluster dat moet worden gebruikt (optioneel)

### <a name="encryption-certificates"></a>Versleutelings certificaten

Het FXT Edge-bestands cluster maakt gebruik van X. 509-certificaten voor deze functies:

* Het cluster beheer verkeer versleutelen

* Verificatie namens een client aan KMIP-servers van derden

* Voor het controleren van server certificaten van cloud providers

Als u certificaten moet uploaden naar het cluster, gebruikt u de pagina **cluster** > -instellingen voor **certificaten** . Meer informatie vindt u op de pagina [cluster > certificaten](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) van de hand leiding voor cluster configuratie.

Als u de communicatie van Cluster beheer wilt versleutelen, gebruikt u de pagina **cluster** > **algemene installatie** -instellingen om te selecteren welk certificaat moet worden gebruikt voor beheer-SSL.

> [!Note] 
> Toegangs sleutels voor Cloud Services worden opgeslagen via de pagina configuratie van **Cloud referenties** . In het gedeelte [een kern bestand toevoegen](fxt-add-storage.md#add-a-core-filer) hierboven ziet u een voor beeld. Lees de sectie [Cloud referenties](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) voor cluster configuratie handleiding voor meer informatie. 

## <a name="configure-dns-for-load-balancing"></a>DNS configureren voor taak verdeling

In deze sectie worden de basis beginselen uitgelegd van het configureren van een Round-Robin DNS-systeem (RRDNS) voor het distribueren van client belasting tussen alle client gerichte IP-adressen in uw FXT Edge-bestands cluster. 

### <a name="decide-whether-or-not-to-use-dns"></a>Beslissen of u DNS wilt gebruiken

Taak verdeling wordt altijd aanbevolen, maar u hoeft niet altijd DNS te gebruiken. Met bijvoorbeeld een aantal typen client werk stromen kan het zinvol zijn om een script te gebruiken om IP-adressen van clusters gelijkmatig toe te wijzen aan clients wanneer ze het cluster koppelen. Een aantal methoden wordt beschreven in [het cluster koppelen](fxt-mount-clients.md). 

Houd bij het bepalen van het gebruik van een DNS-server het volgende in de hand: 

* Als uw systeem alleen door NFS-clients wordt gebruikt, is DNS niet vereist. Het is mogelijk om alle netwerk adressen op te geven met behulp van numerieke IP-adressen. 

* Als uw systeem ondersteuning biedt voor SMB (CIFS), is DNS vereist, omdat u een DNS-domein moet opgeven voor de Active Directory-server.

* DNS is vereist als u Kerberos-verificatie wilt gebruiken.

### <a name="round-robin-dns-configuration-details"></a>Details van Round Robin DNS-configuratie

Wanneer clients toegang hebben tot het cluster, balanceert RRDNS automatisch hun aanvragen over alle beschik bare interfaces.

Voor optimale prestaties kunt u uw DNS-server configureren voor het afhandelen van cluster adressen op de client, zoals wordt weer gegeven in het volgende diagram.

Er wordt aan de linkerkant een cluster-vserver weer gegeven en IP-adressen worden weer gegeven in het midden en aan de rechter kant. Configureer elk client toegangs punt met een record en pointers zoals geïllustreerd.

![Cluster Round-Robin DNS diagram-gedetailleerde ALT-tekst koppeling volgt de afbeelding](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
[gedetailleerde beschrijving](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html) van de tekst

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

### <a name="enable-dns-in-the-cluster"></a>DNS inschakelen in het cluster 

Geef de DNS-server op die door het cluster wordt gebruikt op de pagina **cluster** > netwerk instellingen voor **beheer** . De instellingen op die pagina zijn onder andere:

* DNS-server adres
* DNS-domein naam
* DNS-Zoek domeinen

Lees voor meer informatie [DNS-instellingen](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) in de cluster configuratie handleiding.

## <a name="next-steps"></a>Volgende stappen

Dit is de laatste basis configuratie stap voor het Azure FXT Edge-bestands cluster. 

* Meer informatie over de Led's en andere indica toren van het systeem in de [status van hardware controleren](fxt-monitor.md).
* Meer informatie over hoe clients het FXT Edge-cluster moeten koppelen in [de koppeling van het cluster](fxt-mount-clients.md). 
* Zie de [cluster configuratie handleiding](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)voor meer informatie over het werken en beheren van een FXT Edge-cluster. 