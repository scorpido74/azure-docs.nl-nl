---
title: Clients monteren op het Microsoft Azure FXT Edge Filer-cluster
description: Hoe NFS-clientmachines de hybride opslagcache van Azure FXT Edge Filer kunnen monteren
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 43223db298e4ad170ea6d0687a342b3aee35500e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130772"
---
# <a name="tutorial-mount-the-cluster"></a>Zelfstudie: Het cluster monteren

In deze zelfstudie leert u hoe u NFS-clients monteren op het Azure FXT Edge Filer-cluster. Clients monteren de virtuele naamruimtepaden die u hebt toegewezen toen u back-endopslag hebt toegevoegd.

Deze tutorial leert:

> [!div class="checklist"]
> * Strategieën voor het balanceren van clients in het bereik van clientgerichte IP-adressen
> * Een bevestigingspad maken vanaf een IP-adres met clienten en een knooppunt voor naamruimte
> * Welke argumenten u moet gebruiken in een houderopdracht

Deze tutorial duurt ongeveer 45 minuten.

## <a name="steps-to-mount-the-cluster"></a>Stappen om het cluster te monteren

Volg deze stappen om clientmachines aan te sluiten op uw Azure FXT Edge Filer-cluster.

1. Bepaal hoe u clientverkeer in balans brengt tussen uw clusterknooppunten. Lees [Balance client load](#balance-client-load), hieronder, voor meer informatie.
1. Identificeer het cluster-IP-adres en het verbindingspad dat moet worden gemonteerd.
1. Bepaal het clientgerichte pad voor de berg.
1. Geef de [opdracht monteren](#use-recommended-mount-command-options)uit met de juiste argumenten.

## <a name="balance-client-load"></a>Klantbelasting in evenwicht brengen

Als u clientaanvragen wilt balanceren tussen alle knooppunten in het cluster, moet u clients aan het volledige scala van ip-adressen met clientgerichtheid monteren. Er zijn verschillende manieren om deze taak te automatiseren.

Lees [DNS configureren voor het Azure FXT Edge Filer-cluster voor](fxt-configure-network.md#configure-dns-for-load-balancing)meer informatie over round-robin DNS-taakverdeling voor het cluster. Als u deze methode wilt gebruiken, moet u een DNS-server onderhouden, wat niet wordt uitgelegd in deze artikelen.

Een eenvoudigere methode voor kleine installaties is het gebruik van een script om IP-adressen toe te wijzen in het hele bereik op client mount tijd.

Andere taakverdelingsmethoden kunnen geschikt zijn voor grote of gecompliceerde systemen. Raadpleeg uw Microsoft-vertegenwoordiger of open een [ondersteuningsverzoek](fxt-support-ticket.md) voor hulp. (Azure Load Balancer wordt momenteel *niet ondersteund* met Azure FXT Edge Filer.)

## <a name="create-the-mount-command"></a>De opdracht monteren maken

Vanuit uw client ``mount`` brengt de opdracht de virtuele server (vserver) op het Azure FXT Edge Filer-cluster in kaart aan een pad op het lokale bestandssysteem.

Het formaat is``mount <FXT cluster path> <local path> {options}``

Er zijn drie elementen aan de mount opdracht:

* clusterpad - een combinatie van IP-adres en knooppuntpad naamruimte hieronder beschreven
* lokaal pad - het pad op de client
* opdrachtopties voor het monteren - (weergegeven in aanbevolen [opdrachtopties gebruiken)](#use-recommended-mount-command-options)

### <a name="create-the-cluster-path"></a>Het clusterpad maken

Het clusterpad is een combinatie van het *vserver-IP-adres* plus het pad naar een *naamruimteknooppunt*. De naamruimteverbinding is een virtueel pad dat u hebt gedefinieerd toen u [het opslagsysteem hebt toegevoegd.](fxt-add-storage.md#create-a-junction)

Als u bijvoorbeeld ``/fxt/files`` als naamruimtepad wordt gebruikt, worden IP_address:/fxt/files naar hun lokale bevestigingspunt gemonteerd. *IP_address*

![Dialoogvenster 'Nieuwe verbinding toevoegen' met /avere/bestanden in het veld naamruimtepad](media/fxt-mount/fxt-junction-example.png)

Het IP-adres is een van de clientgerichte IP-adressen die voor de vserver zijn gedefinieerd. U het bereik van ip-gebaseerde clientoplaten op twee plaatsen vinden in het configuratiescherm van het cluster:

* **Tabel VServers** (tabblad Dashboard) -

  ![Tabblad Dashboard van het Configuratiescherm met het tabblad VServer geselecteerd in de gegevenstabel onder de grafiek en de sectie IP-adres omcirkeld](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Pagina Netwerkinstellingen voor clientgerichte** netwerken -

  ![Instellingen > VServer-> configuratiepagina van het clientfacing-netwerk met een cirkel rond het gedeelte Adresbereik van de tabel voor een bepaalde vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Combineer het IP-adres en het naamruimtepad om het clusterpad voor de opdracht monteren te vormen.

Opdracht Voorbeeld clientmount:``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Het lokale pad maken

Het lokale pad voor de opdracht mount is aan jou. U elke gewenste padstructuur instellen als onderdeel van de virtuele naamruimte. Ontwerp een naamruimte en lokaal pad dat handig is voor uw clientworkflow.

Lees het overzicht van de [naamruimte](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)van de clusterconfiguratiegids voor meer informatie over de naamruimte die naar de client gericht is.

Naast de paden, omvatten de [mount command opties](#use-recommended-mount-command-options) hieronder beschreven bij het monteren van elke client.

### <a name="use-recommended-mount-command-options"></a>Aanbevolen opdrachtopties voor het instellen gebruiken

Als u wilt zorgen voor een naadloze clientbevestiging, geeft u deze instellingen en argumenten door in de opdracht monteren:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Vereiste instellingen | |
--- | ---
``hard`` | Zachte bevestigingen aan het Azure FXT Edge Filer-cluster zijn gekoppeld aan toepassingsfouten en mogelijk gegevensverlies.
``proto=netid`` | Deze optie ondersteunt de juiste afhandeling van NFS-netwerkfouten.
``mountproto=netid`` | Deze optie ondersteunt de juiste afhandeling van netwerkfouten voor montagebewerkingen.
``retry=n`` | Stel ``retry=30`` in om tijdelijke mountfouten te voorkomen. (Een andere waarde wordt aanbevolen in voorgrondmounts.)

| Voorkeursinstellingen  | |
--- | ---
``nointr``            | Als uw clients oudere OS-kernels gebruiken (vóór april 2008) die deze optie ondersteunen, gebruikt u deze optie. De optie "intr" is de standaardinstelling.

## <a name="next-steps"></a>Volgende stappen

Nadat u clients hebt gemonteerd, u uw workflow testen en aan de slag gaan met uw cluster.

Als u gegevens naar een nieuwe cloudcore-filer wilt verplaatsen, profiteert u van de cachestructuur door parallelle gegevensopname te gebruiken. Sommige strategieën worden beschreven in [Het verplaatsen van gegevens naar een vFXT-cluster.](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest) (Avere vFXT voor Azure is een cloudgebaseerd product dat caching-technologie gebruikt die sterk lijkt op de Azure FXT Edge Filer.)

Lees [de hardwarestatus monitor Azure FXT Edge Filer](fxt-monitor.md) als u hardwareproblemen moet oplossen.
