---
title: Clients koppelen aan het FXT Edge-cluster van Microsoft Azure
description: Hoe NFS-client computers de Azure FXT Edge filer hybride opslag cache kan koppelen
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 43223db298e4ad170ea6d0687a342b3aee35500e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80130772"
---
# <a name="tutorial-mount-the-cluster"></a>Zelf studie: het cluster koppelen

In deze zelf studie leert u hoe u NFS-clients kunt koppelen aan het Azure FXT Edge-bestands cluster. Clients koppelen de paden van de virtuele naam ruimte die u hebt toegewezen bij het toevoegen van back-end-opslag.

In deze zelf studie leert u het volgende:

> [!div class="checklist"]
> * Strategieën voor Load Balancing-clients over het bereik van client gerichte IP-adressen
> * Een koppelingspad maken op basis van een client gericht IP-adres en naam ruimte koppeling
> * Welke argumenten moeten worden gebruikt in een koppelings opdracht

Het volt ooien van deze zelf studie duurt ongeveer 45 minuten.

## <a name="steps-to-mount-the-cluster"></a>Stappen voor het koppelen van het cluster

Volg deze stappen om client computers te verbinden met uw Azure FXT Edge-bestands cluster.

1. Bepaal hoe u het client verkeer wilt verdelen over de cluster knooppunten. Lees [saldo client belasting](#balance-client-load)hieronder voor meer informatie.
1. Identificeer het IP-adres en het pad van de cluster om te koppelen.
1. Bepaal het op de client gerichte pad voor de koppeling.
1. Geef de [koppelings opdracht](#use-recommended-mount-command-options)met de juiste argumenten op.

## <a name="balance-client-load"></a>Taak verdeling van client

Als u client aanvragen wilt verdelen over alle knoop punten in het cluster, moet u clients koppelen aan het volledige bereik aan client gerichte IP-adressen. Er zijn verschillende manieren om deze taak te automatiseren.

Voor meer informatie over Round Robin DNS-taak verdeling voor het cluster, Lees [DNS configureren voor het Azure FXT Edge-bestands cluster](fxt-configure-network.md#configure-dns-for-load-balancing). Als u deze methode wilt gebruiken, moet u een DNS-server onderhouden, die niet wordt uitgelegd in deze artikelen.

Een eenvoudigere methode voor kleine installaties is het gebruik van een script om IP-adressen toe te wijzen binnen het bereik van de client koppelings tijd.

Andere methoden voor taak verdeling kunnen geschikt zijn voor grote of gecompliceerde systemen. Neem contact op met uw micro soft-vertegenwoordiger of open een [ondersteunings aanvraag](fxt-support-ticket.md) voor hulp. (Azure Load Balancer wordt momenteel *niet ondersteund* met Azure FXT Edge-bestand.)

## <a name="create-the-mount-command"></a>De koppeling maken opdracht

Vanaf uw client wijst de ``mount`` opdracht de virtuele server (vserver) toe aan het Azure FXT Edge-bestands cluster naar een pad op het lokale bestands systeem.

De indeling is``mount <FXT cluster path> <local path> {options}``

Er zijn drie elementen voor de koppelings opdracht:

* pad naar cluster: een combi natie van het pad voor IP-adres en naam ruimte koppeling dat hieronder wordt beschreven
* lokaal pad-het pad op de client
* opdracht Opties voor koppelen: (vermeld in [Aanbevolen opdracht Opties voor koppelen gebruiken](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Het pad van het cluster maken

Het pad van het cluster is een combi natie van het vserver *IP-adres* plus het pad naar een *naam ruimte koppeling*. De naam ruimte koppeling is een virtueel pad dat u hebt gedefinieerd tijdens [het toevoegen van het opslag systeem](fxt-add-storage.md#create-a-junction).

Als u bijvoorbeeld hebt gebruikt ``/fxt/files`` als pad voor de naam ruimte, zouden uw clients *iP_address*:/FXT/files te koppelen aan hun lokale koppel punt.

![Dialoog venster nieuwe verbinding toevoegen met/avere/files in het veld naam ruimte-pad](media/fxt-mount/fxt-junction-example.png)

Het IP-adres is een van de client gerichte IP-adressen die zijn gedefinieerd voor de vserver. U kunt het bereik van client gerichte Ip's vinden op twee plaatsen in het configuratie scherm van het cluster:

* **VServers** -tabel (tabblad dash board)-

  ![Tabblad dash board van het configuratie scherm met het tabblad VServer geselecteerd in de gegevens tabel onder de grafiek, en de sectie IP-adres omcirkeld](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* Pagina netwerk instellingen voor **client** -

  ![Instellingen > de pagina VServer > client gerichte netwerk configuratie met een cirkel rond de sectie adres bereik van de tabel voor een bepaalde vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Combi neer het IP-adres en het pad van de naam ruimte om het pad van het cluster voor de opdracht Mount te maken.

Voor beeld van client koppelings opdracht:``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Het lokale pad maken

Het lokale pad voor de koppelings opdracht is voor u. U kunt elke gewenste pad-structuur instellen als onderdeel van de virtuele naam ruimte. Ontwerp een naam ruimte en een lokaal pad dat handig is voor uw client werk stroom.

Lees het overzicht van de [naam ruimte](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)van de cluster configuratie handleiding voor meer informatie over de client gerichte naam ruimte.

Naast de paden moet u de [opdracht Opties voor koppelen](#use-recommended-mount-command-options) gebruiken die hieronder worden beschreven bij het koppelen van elke client.

### <a name="use-recommended-mount-command-options"></a>Aanbevolen opdracht Opties voor koppelen gebruiken

Om ervoor te zorgen dat een naadloze client koppelt, geeft u deze instellingen en argumenten door in de opdracht Mount:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Vereiste instellingen | |
--- | ---
``hard`` | Zachte koppelingen naar het Azure FXT Edge-bestands cluster zijn gekoppeld aan toepassings fouten en mogelijke gegevens verlies.
``proto=netid`` | Met deze optie wordt de juiste verwerking van NFS-netwerk fouten ondersteund.
``mountproto=netid`` | Deze optie biedt ondersteuning voor de juiste verwerking van netwerk fouten voor koppelings bewerkingen.
``retry=n`` | Stel ``retry=30`` in om storingen van de tijdelijke koppeling te voor komen. (Een andere waarde wordt aanbevolen in voorgrond koppelingen.)

| Voorkeurs instellingen  | |
--- | ---
``nointr``            | Als uw clients oudere OS-kernels gebruiken (vóór april 2008) die deze optie ondersteunen, gebruikt u deze. De optie ' intr ' is de standaard instelling.

## <a name="next-steps"></a>Volgende stappen

Nadat u-clients hebt gekoppeld, kunt u uw werk stroom testen en aan de slag met uw cluster.

Als u gegevens naar een nieuwe Cloud core-bestandsr wilt verplaatsen, kunt u profiteren van de cache structuur door gebruik te maken van parallelle gegevens opname. Sommige strategieën worden beschreven in [gegevens verplaatsen naar een vFXT-cluster](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). (Avere vFXT voor Azure is een Cloud product dat gebruikmaakt van cache technologie die zeer vergelijkbaar is met de Azure FXT Edge-bestand.)

Lees de hardware-status van de [Azure FXT Edge-bestandsr controleren](fxt-monitor.md) als u hardwareproblemen moet oplossen.
