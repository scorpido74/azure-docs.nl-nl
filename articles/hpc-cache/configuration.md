---
title: Instellingen voor Azure HPC-cache configureren
description: Hier wordt uitgelegd hoe u aanvullende instellingen voor de cache configureren, zoals MTU en no-root-squash, en hoe u toegang krijgt tot de expresmomentopnamen van Azure Blob-opslagdoelen.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 137e41a3f7d346bb612605660d7798ac2fc297d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538815"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Aanvullende Azure HPC-cache-instellingen configureren

De **configuratiepagina** in de Azure-portal heeft opties voor het aanpassen van verschillende instellingen. De meeste gebruikers hoeven deze niet te wijzigen vanaf de standaardwaarden.

In dit artikel wordt ook beschreven hoe u de momentopnamefunctie voor Azure Blob-opslagdoelen gebruiken. De snapshot-functie heeft geen configureerbare instellingen.

Als u de instellingen wilt zien, opent u de **configuratiepagina van** de cache in de Azure-portal.

![schermafbeelding van configuratiepagina in Azure-portal](media/configuration.png)

## <a name="adjust-mtu-value"></a>MTU-waarde aanpassen
<!-- linked from troubleshoot-nas article -->

U de maximale grootte van de transmissie-eenheid voor de cache selecteren met behulp van het vervolgkeuzemenu met de **mtu-grootte**.

De standaardwaarde is 1500 bytes, maar u deze wijzigen in 1400.

> [!NOTE]
> Als u de MTU-grootte van de cache verlaagt, moet u ervoor zorgen dat de clients en opslagsystemen die met de cache communiceren, dezelfde MTU-instelling of een lagere waarde hebben.

Als u de MTU-waarde in de cache verlaagt, u in de rest van het netwerk van de cache problemen met de grootte van het pakket omzeilen. Sommige VPN's kunnen bijvoorbeeld 1500 bytepakketten op ware grootte niet succesvol verzenden. Het verminderen van de grootte van pakketten die via de VPN worden verzonden, kan dat probleem elimineren. Houd er echter rekening mee dat een lagere mtu-instelling in de cache betekent dat elk ander onderdeel dat met de cache communiceert - inclusief clients en opslagsystemen - ook een lagere instelling moet hebben om communicatieproblemen met de cache te voorkomen.

Als u de MTU-instellingen op andere systeemonderdelen niet wilt wijzigen, moet u de MTU-instelling van de cache niet verlagen. Er zijn andere oplossingen om vpn-pakketgroottebeperkingen te omzeilen. Lees [Beperkingen voor vpn-pakketgrootte](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) aanpassen in het NAS-probleemoplossingsartikel voor meer informatie over het diagnosticeren en aanpakken van dit probleem.

Meer informatie over MTU-instellingen in virtuele Azure-netwerken door [TCP/IP-prestatieafstemming voor Azure VM's te](../virtual-network/virtual-network-tcpip-performance-tuning.md)lezen.

## <a name="configure-root-squash"></a>Rootsquash configureren
<!-- linked from troubleshoot -->

Met de instelling **Rootsquash inschakelen** bepaalt u hoe de Azure HPC-cache roottoegang toestaat. Root squash helpt om root-level toegang van onbevoegde clients te voorkomen.

Met deze instelling kunnen gebruikers roottoegang beheren op cacheniveau, wat kan helpen compenseren voor de vereiste ``no_root_squash`` instelling voor NAS-systemen die als opslagdoel worden gebruikt. (Lees meer over [nfs-opslagdoelvereisten](hpc-cache-prereqs.md#nfs-storage-requirements).) Het kan ook de beveiliging verbeteren wanneer deze wordt gebruikt met Azure Blob-opslagdoelen.

De standaardinstelling is **Ja**. (Caches die vóór april 2020 zijn gemaakt, hebben mogelijk de standaardinstelling **Nr.)** Wanneer deze functie is ingeschakeld, voorkomt u ook het gebruik van set-UID-machtigingsbits in clientaanvragen naar de cache.

## <a name="view-snapshots-for-blob-storage-targets"></a>Momentopnamen weergeven voor blobopslagdoelen

Azure HPC Cache slaat automatisch opslagmomentopnamen op voor Azure Blob-opslagdoelen. Momentopnamen bieden een snel referentiepunt voor de inhoud van de back-endopslagcontainer. Momentopnamen zijn geen vervanging voor back-ups van gegevens en bevatten geen informatie over de status van gegevens in de cache.

> [!NOTE]
> Deze momentopnamefunctie verschilt van de momentopnamefunctie die is opgenomen in NetApp-, Isilon- of ZFS-opslagsoftware. Deze momentopnameimplementaties spoelen wijzigingen van de cache naar het back-endopslagsysteem voordat u de momentopname maakt.
>
> Voor de efficiëntie spoelt de momentopname Azure HPC-cache wijzigingen niet eerst door en registreert alleen gegevens die naar de Blob-container zijn geschreven. Deze momentopname vertegenwoordigt niet de status van gegevens in de cache, dus recente wijzigingen kunnen worden uitgesloten.

Deze functie is alleen beschikbaar voor Azure Blob-opslagdoelen en de configuratie kan niet worden gewijzigd.

Snapshots worden elke acht uur gemaakt, op UTC 0:00, 08:00 en 16:00 uur.

Azure HPC-cache slaat dagelijks, wekelijks e.d. en maandelijks momentopnamen op totdat ze worden vervangen door nieuwe. De limieten zijn:

* tot 20 dagelijkse snapshots
* tot 8 wekelijkse snapshots
* maximaal 3 maandelijkse momentopnamen

Toegang tot de `.snapshot` momentopnamen uit de map in de naamruimte van uw blob-opslagdoel.
