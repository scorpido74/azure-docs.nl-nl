---
title: De avere vFXT-Azure koppelen
description: Clients koppelen met avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: e8850162847f2dd416b0951a797e2eb0cd7d55d2
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229564"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Het Avere vFXT-cluster koppelen

Volg deze stappen om client computers te verbinden met uw vFXT-cluster.

1. Bepaal hoe u het client verkeer wilt verdelen over de cluster knooppunten. Lees [saldo client belasting](#balance-client-load)hieronder voor meer informatie.
1. Identificeer het IP-adres en het pad voor de koppeling om te koppelen.
1. Geef de [koppelings opdracht](#mount-command-arguments)met de juiste argumenten op.

## <a name="balance-client-load"></a>Taak verdeling van client

Als u client aanvragen wilt verdelen over alle knoop punten in het cluster, moet u clients koppelen aan het volledige bereik aan client gerichte IP-adressen. Er zijn verschillende manieren om deze taak te automatiseren.

> [!TIP]
> Andere taakverdelings methoden zijn mogelijk geschikt voor grote of complexe systemen. [Open een ondersteunings ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) voor hulp.)
>
> Als u liever een DNS-server gebruikt voor de automatische taak verdeling aan de server zijde, moet u uw eigen DNS-server in azure instellen en beheren. In dat geval kunt u round-robin DNS configureren voor het vFXT-cluster op basis van dit document: [avere cluster DNS-configuratie](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Voor beeld van een evenwichtige client koppelings script

Dit code voorbeeld maakt gebruik van client-IP-adressen als een wille keurig element om clients te distribueren naar alle beschik bare IP-adressen van het vFXT-cluster.

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

De bovenstaande functie maakt deel uit van het batch-voor beeld dat beschikbaar is op de site met [avere vFXT-voor beelden](https://github.com/Azure/Avere#tutorials) .

## <a name="create-the-mount-command"></a>De koppeling maken opdracht

> [!NOTE]
> Als u geen nieuwe BLOB-container hebt gemaakt tijdens het maken van uw avere vFXT-cluster, voegt u opslag systemen toe zoals beschreven in [opslag ruimte configureren](avere-vfxt-add-storage.md) voordat u probeert clients te koppelen.

Vanaf uw client wijst de ``mount`` opdracht de virtuele server (vserver) toe aan het vFXT-cluster naar een pad op het lokale bestands systeem. De indeling is``mount <vFXT path> <local path> {options}``

De opdracht mount heeft drie elementen:

* pad naar vFXT: een combi natie van een IP-adres en pad voor naam ruimte koppeling op het cluster 9described hieronder)
* lokaal pad-het pad op de client
* opdracht Opties koppelen: wordt weer gegeven in de [opdracht argumenten koppelen](#mount-command-arguments)

### <a name="junction-and-ip"></a>Verbinding en IP

Het pad vserver is een combi natie van het *IP-adres* plus het pad naar een *naam ruimte koppeling*. De naam ruimte koppeling is een virtueel pad dat is gedefinieerd toen het opslag systeem werd toegevoegd.

Als uw cluster is gemaakt met Blob Storage, is het pad naar de naam ruimte naar die container`/msazure`

Voorbeeld: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Als u opslag hebt toegevoegd nadat u het cluster hebt gemaakt, is het pad van de naam ruimte koppeling de waarde die u hebt ingesteld in het **pad naar de naam ruimte** bij het maken van de verbinding. Als u bijvoorbeeld hebt gebruikt ``/avere/files`` als pad voor de naam ruimte, zouden uw clients *iP_address*:/avere/files te koppelen aan hun lokale koppel punt.

![Dialoog venster nieuwe verbinding toevoegen met/avere/files in het veld naam ruimte-pad](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

Het IP-adres is een van de client gerichte IP-adressen die zijn gedefinieerd voor de vserver. U kunt het bereik van client gerichte Ip's vinden op twee plaatsen in het configuratie scherm van AVERE:

* **VServers** -tabel (tabblad dash board)-

  ![Tabblad dash board van het configuratie scherm avere met het tabblad VServer geselecteerd in de gegevens tabel onder de grafiek, en de sectie IP-adres omcirkeld](media/avere-vfxt-ip-addresses-dashboard.png)

* Pagina netwerk instellingen voor **client** -

  ![Instellingen > de pagina VServer > client gerichte netwerk configuratie met een cirkel rond de sectie adres bereik van de tabel voor een bepaalde vserver](media/avere-vfxt-ip-addresses-settings.png)

Naast de paden moet u de volgende para [meters voor koppelen](#mount-command-arguments) gebruiken bij het koppelen van elke client.

### <a name="mount-command-arguments"></a>Opdracht argumenten koppelen

Om ervoor te zorgen dat een naadloze client koppelt, geeft u deze instellingen en argumenten door in de opdracht Mount:

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Vereiste instellingen | Beschrijving |
--- | ---
``hard`` | Zachte koppelingen naar het vFXT-cluster zijn gekoppeld aan toepassings fouten en mogelijke gegevens verlies.
``proto=netid`` | Met deze optie wordt de juiste verwerking van NFS-netwerk fouten ondersteund.
``mountproto=netid`` | Deze optie biedt ondersteuning voor de juiste verwerking van netwerk fouten voor koppelings bewerkingen.
``retry=n`` | Stel ``retry=30`` in om storingen van de tijdelijke koppeling te voor komen. (Een andere waarde wordt aanbevolen in voorgrond koppelingen.)

## <a name="next-steps"></a>Volgende stappen

Nadat u clients hebt gekoppeld, kunt u deze gebruiken om gegevens te kopiëren naar een nieuwe Blob Storage-container in uw cluster. Als u geen nieuwe opslag ruimte nodig hebt, leest u de andere koppelingen voor meer informatie over aanvullende installatie taken:

* [Gegevens verplaatsen naar een kern bestand](avere-vfxt-data-ingest.md) van het cluster: meerdere clients en threads gebruiken om uw gegevens efficiënt te uploaden naar een nieuwe kern bestand
* [Cluster aanpassing aanpassen](avere-vfxt-tuning.md) : de cluster instellingen op uw werk belasting passen
* [Beheer van het cluster](avere-vfxt-manage-cluster.md) -het starten of stoppen van het cluster en het beheren van knoop punten
