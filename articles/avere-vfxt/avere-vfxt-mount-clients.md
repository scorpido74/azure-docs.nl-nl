---
title: Monteer de Avere vFXT - Azure
description: Clients monteren met Avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: b8486b5a33226b1faa5e3874144129dbe7a1a2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153408"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Het Avere vFXT-cluster koppelen

Volg deze stappen om clientmachines aan te sluiten op uw vFXT-cluster.

1. Bepaal hoe u clientverkeer in balans brengt tussen uw clusterknooppunten. Lees [Balance client load](#balance-client-load), hieronder, voor meer informatie.
1. Identificeer het IP-adres en het verbindingspad dat moet worden gemonteerd.
1. Geef de [opdracht monteren](#mount-command-arguments)uit met de juiste argumenten.

## <a name="balance-client-load"></a>Klantbelasting in evenwicht brengen

Als u clientaanvragen wilt balanceren tussen alle knooppunten in het cluster, moet u clients aan het volledige scala van ip-adressen met clientgerichtheid monteren. Er zijn verschillende eenvoudige manieren om deze taak te automatiseren.

> [!TIP]
> Andere taakverdelingsmethoden kunnen geschikt zijn voor grote of gecompliceerde systemen; [open een ondersteuningsticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) voor hulp.)
>
> Als u liever een DNS-server gebruikt voor automatische taakverdeling aan de serverzijde, moet u uw eigen DNS-server binnen Azure instellen en beheren. In dat geval u round-robin DNS configureren voor het vFXT-cluster volgens dit document: [Avere cluster DNS-configuratie.](avere-vfxt-configure-dns.md)

### <a name="sample-balanced-client-mounting-script"></a>Voorbeeld van een gebalanceerd clientmontagescript

In dit codevoorbeeld worden IP-adressen van clients gebruikt als een randomiserend element om clients te distribueren naar alle beschikbare IP-adressen van het vFXT-cluster.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi
}
```

De functie hierboven maakt deel uit van het batchvoorbeeld dat beschikbaar is in de [Avere vFXT-voorbeeldensite.](https://github.com/Azure/Avere#tutorials)

## <a name="create-the-mount-command"></a>De opdracht monteren maken

> [!NOTE]
> Als u geen nieuwe Blob-container hebt gemaakt bij het maken van uw Avere vFXT-cluster, voegt u opslagsystemen toe zoals beschreven in [Opslag configureren](avere-vfxt-add-storage.md) voordat u clients probeert te monteren.

Vanuit uw client ``mount`` brengt de opdracht de virtuele server (vserver) op het vFXT-cluster in kaart aan een pad op het lokale bestandssysteem. Het formaat is``mount <vFXT path> <local path> {options}``

De opdracht monteren bestaat uit drie elementen:

* vFXT-pad - een combinatie van een IP-adres en knooppuntpad naamruimte op het onderstaande cluster 9)
* lokaal pad - het pad op de client
* opdrachtopties voor opdrachten instellen - weergegeven in [opdrachtargumenten instellen](#mount-command-arguments)

### <a name="junction-and-ip"></a>Knooppunt en IP

Het vserverpad is een combinatie van het *IP-adres* plus het pad naar een *naamruimteknooppunt.* De naamruimteverbinding is een virtueel pad dat is gedefinieerd toen het opslagsysteem werd toegevoegd.

Als uw cluster is gemaakt met Blob-opslag, wordt het naamruimtepad naar die container`/msazure`

Voorbeeld: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Als u opslag hebt toegevoegd na het maken van het cluster, is het knooppuntpad voor naamruimte de waarde die u in **het pad Voor naamruimte** instelt bij het maken van de verbinding. Als u bijvoorbeeld ``/avere/files`` als naamruimtepad wordt gebruikt, worden IP_address:/avere/bestanden op hun lokale bevestigingspunt gemonteerd. *IP_address*

![Dialoogvenster 'Nieuwe verbinding toevoegen' met /avere/bestanden in het veld naamruimtepad](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

Het IP-adres is een van de clientgerichte IP-adressen die voor de vserver zijn gedefinieerd. U vindt het bereik van client-facing IP's op twee plaatsen in het Avere Configuratiescherm:

* **Tabel VServers** (tabblad Dashboard) -

  ![Tabblad Dashboard van het Configuratiescherm van Avere met het tabblad VServer geselecteerd in de gegevenstabel onder de grafiek en de sectie IP-adres omcirkeld](media/avere-vfxt-ip-addresses-dashboard.png)

* **Pagina Netwerkinstellingen voor clientgerichte** netwerken -

  ![Instellingen > VServer-> configuratiepagina van het clientfacing-netwerk met een cirkel rond het gedeelte Adresbereik van de tabel voor een bepaalde vserver](media/avere-vfxt-ip-addresses-settings.png)

Voeg naast de paden ook de onderstaande [opdrachtargumenten op](#mount-command-arguments) die hieronder worden beschreven bij het monteren van elke client.

### <a name="mount-command-arguments"></a>Opdrachtargumenten monteren

Als u wilt zorgen voor een naadloze clientbevestiging, geeft u deze instellingen en argumenten door in de opdracht monteren:

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Vereiste instellingen | |
--- | ---
``hard`` | Soft mounts aan het vFXT-cluster worden geassocieerd met toepassingsfouten en mogelijk gegevensverlies.
``proto=netid`` | Deze optie ondersteunt de juiste afhandeling van NFS-netwerkfouten.
``mountproto=netid`` | Deze optie ondersteunt de juiste afhandeling van netwerkfouten voor montagebewerkingen.
``retry=n`` | Stel ``retry=30`` in om tijdelijke mountfouten te voorkomen. (Een andere waarde wordt aanbevolen in voorgrondmounts.)

## <a name="next-steps"></a>Volgende stappen

Nadat u clients hebt gemonteerd, u deze gebruiken om gegevens te kopiëren naar een nieuwe Blob-opslagcontainer op uw cluster. Als u geen nieuwe opslag hoeft in te vullen, leest u de andere koppelingen om meer te weten te komen over extra installatietaken:

* [Gegevens verplaatsen naar een clustercore-filer](avere-vfxt-data-ingest.md) - Meerdere clients en threads gebruiken om uw gegevens efficiënt te uploaden naar een nieuwe kernfiler
* [Clusterafstemming aanpassen](avere-vfxt-tuning.md) - De clusterinstellingen aanpassen aan uw werkbelasting
* [Het cluster beheren](avere-vfxt-manage-cluster.md) - Het cluster starten of stoppen en knooppunten beheren
