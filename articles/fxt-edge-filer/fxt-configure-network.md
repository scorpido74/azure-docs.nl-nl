---
title: 'Zelfstudie: Netwerk configureren in een Azure FXT Edge Filer-cluster'
description: Netwerkinstellingen aanpassen na het maken van het Azure FXT Edge Filer-cluster
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 9b0154889544e0054e309cc5f43851b73b4396b4
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754691"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Zelfstudie: De netwerkinstellingen van het cluster configureren

Voordat u een nieuw gemaakt Azure FXT Edge Filer-cluster gebruikt, moet u verschillende netwerkinstellingen voor uw werkstroom controleren en aanpassen. 

In deze zelfstudie worden de netwerkinstellingen uitgelegd die u mogelijk moet aanpassen voor een nieuw cluster. 

U leert: 

> [!div class="checklist"]
> * Welke netwerkinstellingen moeten mogelijk worden bijgewerkt na het maken van een cluster
> * Welke gebruiksgevallen van Azure FXT Edge Filer vereisen een AD-server of een DNS-server 
> * Round-robin DNS (RRDNS) configureren om clientaanvragen automatisch te laden naar het FXT-cluster

De hoeveelheid tijd die nodig is om deze stappen uit te voeren, is afhankelijk van het aantal configuratiewijzigingen dat nodig is in uw systeem:

* Als u alleen hoeft te lezen door de tutorial en controleer een paar instellingen, moet het 10 tot 15 minuten duren. 
* Als u round-robin DNS moet configureren, kan die taak een uur of langer duren.

## <a name="adjust-network-settings"></a>Netwerkinstellingen aanpassen

Verschillende netwerkgerelateerde taken maken deel uit van het opzetten van een nieuw Azure FXT Edge Filer-cluster. Controleer deze lijst en beslissen welke van toepassing zijn op uw systeem.

Lees [Netwerkservices configureren](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) in de clusterconfiguratiehandleiding voor meer informatie over netwerkinstellingen voor het cluster.

* Round-robin DNS configureren voor het clientgerichte netwerk (optioneel)

  Load balance clusterverkeer door het DNS-systeem te configureren zoals beschreven in [DNS configureren voor het FXT Edge Filer-cluster.](#configure-dns-for-load-balancing)

* NTP-instellingen verifiëren

* Active Directory- en gebruikersnaam-/groepsnaamdownloads configureren (indien nodig)

  Als uw netwerkhosts Active Directory of een ander soort externe directoryservice gebruiken, moet u de directoryservicesconfiguratie van het cluster wijzigen om in te stellen hoe de gebruikersnamen en groepsgegevens van het cluster worden gedownload. Lees > **Clusterdirectoryservices** in de clusterconfiguratiehandleiding voor meer informatie. **Cluster**

  Er is een AD-server vereist als u SMB-ondersteuning wilt. Configureer AD voordat u SMB instelt.

* VLAN's definiëren (optioneel)
  
  Configureer eventuele extra VLAN's die nodig zijn voordat u de vservers en de algemene naamruimte van uw cluster definieert. Lees [Werken met VLAN's](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) in de clusterconfiguratiehandleiding voor meer informatie.

* Proxyservers configureren (indien nodig)

  Als uw cluster een proxyserver gebruikt om externe adressen te bereiken, voert u de volgende stappen uit om het in te stellen:

  1. De proxyserver definiëren op de pagina **Proxyconfiguratie-instellingen**
  1. Pas de configuratie van de proxyserver toe op de pagina **Clusteralgemene** > **installatie** of de pagina Details van de **Core Filer.**
  
  Lees [Webproxy's gebruiken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) in de clusterconfiguratiehandleiding voor meer informatie.

* [Versleutelingscertificaten](#encryption-certificates) uploaden voor het te gebruiken cluster (optioneel)

### <a name="encryption-certificates"></a>Versleutelingscertificaten

Het FXT Edge Filer-cluster gebruikt X.509-certificaten voor deze functies:

* Clusterbeheerverkeer versleutelen

* Verifiëren namens een client naar KMIP-servers van derden

* Voor het verifiëren van servercertificaten van cloudproviders

Als u certificaten naar het cluster wilt uploaden, gebruikt u de pagina > **Clustercertificaten-instellingen.** **Cluster** Details staan op de pagina [Cluster > certificaten](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) van de clusterconfiguratiehandleiding.

Als u de communicatie met clusterbeheer wilt versleutelen, gebruikt u de pagina Instellingen voor algemene instellingen **clusterinstellingen** > **General Setup** om te selecteren welk certificaat moet worden gebruikt voor tls voor beheer.

> [!Note] 
> Toegangssleutels voor cloudservices worden opgeslagen met behulp van de configuratiepagina **CloudReferenties.** In de sectie [Een kernfiler](fxt-add-storage.md#add-a-core-filer) toevoegen hierboven wordt een voorbeeld weergegeven. lees de sectie [Clusterconfiguratiehandleiding Cloudreferenties](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) voor meer informatie. 

## <a name="configure-dns-for-load-balancing"></a>DNS configureren voor taakverdeling

In deze sectie worden de basisprincipes van het configureren van een round-robin DNS (RRDNS)-systeem uitgelegd om clientbelasting te verdelen over alle clientgerichte IP-adressen in uw FXT Edge Filer-cluster. 

### <a name="decide-whether-or-not-to-use-dns"></a>Beslissen of dns al dan niet wordt gebruikt

Load balancing wordt altijd aanbevolen, maar je hoeft niet altijd DNS te gebruiken. Bij sommige typen clientwerkstromen is het bijvoorbeeld logischer om een script te gebruiken om cluster-IP-adressen gelijkmatig toe te wijzen aan clients wanneer ze het cluster monteren. Sommige methoden worden beschreven in [Het cluster monteren.](fxt-mount-clients.md) 

Houd rekening met deze dingen bij de beslissing om al dan niet een DNS-server te gebruiken: 

* Als uw systeem alleen toegankelijk is voor NFS-clients, is DNS niet vereist. Het is mogelijk om alle netwerkadressen op te geven met behulp van numerieke IP-adressen. 

* Als uw systeem SMB-toegang (CIFS) ondersteunt, is DNS vereist, omdat u een DNS-domein moet opgeven voor de Active Directory-server.

* DNS is vereist als u Kerberos-verificatie wilt gebruiken.

### <a name="round-robin-dns-configuration-details"></a>Round-robin DNS-configuratiegegevens

Wanneer clients toegang krijgen tot het cluster, balanceert RRDNS hun aanvragen automatisch tussen alle beschikbare interfaces.

Configureer uw DNS-server voor optimale prestaties om clusteradressen die naar client gericht zijn te verwerken, zoals in het volgende diagram wordt weergegeven.

Een clustervserver wordt links weergegeven en IP-adressen worden in het midden en aan de rechterkant weergegeven. Configureer elk toegangspunt voor de client met A-records en aanwijzers zoals geïllustreerd.

![Cluster round-robin DNS diagram - gedetailleerde](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
alt tekst link volgt afbeelding[gedetailleerde tekst beschrijving](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

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

### <a name="enable-dns-in-the-cluster"></a>DNS in het cluster inschakelen 

Geef de DNS-server op die het cluster gebruikt op de pagina > **Clusterbeheernetwerkinstellingen.** **Cluster** Instellingen op die pagina zijn onder andere:

* DNS-serveradres
* DNS-domeinnaam
* DNS-zoekdomeinen

Lees voor meer informatie [DNS-instellingen](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) in de clusterconfiguratiehandleiding.

## <a name="next-steps"></a>Volgende stappen

Dit is de laatste basisconfiguratiestap voor het Azure FXT Edge Filer-cluster. 

* Meer informatie over de LED's en andere indicatoren van het systeem in [de hardwarestatus Monitor](fxt-monitor.md).
* Meer informatie over hoe clients het FXT Edge Filer-cluster moeten monteren in [Het cluster monteren.](fxt-mount-clients.md) 
* Zie de [clusterconfiguratiehandleiding](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)voor meer informatie over het bedienen en beheren van een FXT Edge Filer-cluster. 