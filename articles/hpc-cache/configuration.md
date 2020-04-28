---
title: Instellingen voor de Azure HPC-cache configureren
description: In dit artikel wordt uitgelegd hoe u aanvullende instellingen voor de cache configureert, zoals MTU en no-root-Squash, en hoe u toegang krijgt tot de Express-moment opnamen van Azure Blob-opslag doelen.
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: v-erkel
ms.openlocfilehash: 7938fcc0819fc3e5e0762cc8c3c2931594ed1c68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195057"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Aanvullende instellingen voor de Azure HPC-cache configureren

De **configuratie** pagina in de Azure Portal bevat opties voor het aanpassen van verschillende instellingen. De meeste gebruikers hoeven deze instellingen niet te wijzigen van hun standaard waarden.

In dit artikel wordt ook beschreven hoe u de functie snap shot gebruikt voor Azure Blob Storage-doelen. De momentopname functie heeft geen Configureer bare instellingen.

Als u de instellingen wilt zien, opent u de pagina **configuratie** van de cache in de Azure Portal.

![scherm afbeelding van de configuratie pagina in Azure Portal](media/configuration.png)

## <a name="adjust-mtu-value"></a>MTU-waarde aanpassen
<!-- linked from troubleshoot-nas article -->

U kunt de maximale grootte van de verzend eenheid voor de cache selecteren met behulp van de vervolg keuzelijst met de naam **MTU-grootte**.

De standaard waarde is 1500 bytes, maar u kunt deze wijzigen in 1400.

> [!NOTE]
> Als u de MTU-grootte van de cache verlaagt, moet u ervoor zorgen dat de clients en opslag systemen die met de cache communiceren dezelfde MTU-instelling of een lagere waarde hebben.

Het verlagen van de cache-MTU-waarde kan u helpen de beperkingen voor pakket grootte te omzeilen in de rest van het netwerk van de cache. Sommige Vpn's kunnen bijvoorbeeld geen 1500-byte-pakketten van de volledige grootte verzenden. Het verminderen van de grootte van pakketten die via de VPN-verbinding worden verzonden, kan dat probleem mogelijk niet oplossen. Houd er echter rekening mee dat een lagere MTU-instelling voor de cache betekent dat elk ander onderdeel dat communiceert met de cache-inclusief-clients en-opslag systemen, ook een lagere MTU-instelling moet hebben om communicatie problemen te voor komen.

Als u de MTU-instellingen op andere systeem onderdelen niet wilt wijzigen, moet u de MTU-instelling van de cache niet verlagen. Er zijn andere oplossingen om beperkingen voor de grootte van VPN-pakketten te omzeilen. Lees de beperkingen voor de [grootte van VPN-pakketten aanpassen](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) in het artikel over het oplossen van problemen met NAS voor meer informatie over het diagnosticeren en oplossen van dit probleem.

Lees meer over MTU-instellingen in virtuele netwerken van Azure door het [afstemmen van TCP/IP-prestaties voor Azure-vm's](../virtual-network/virtual-network-tcpip-performance-tuning.md)te lezen.

## <a name="configure-root-squash"></a>Basis-Squash configureren
<!-- linked from troubleshoot -->

De instelling **basis Squash inschakelen** bepaalt hoe de Azure HPC-cache toegang tot het hoofd niveau toestaat. Met root Squash kunt u toegang op toegangs niveau voor komen van niet-geautoriseerde clients.

Met deze instelling kunnen gebruikers de toegang tot de hoofdmap beheren op het niveau van de cache, waarmee de ``no_root_squash`` vereiste instelling voor NAS-systemen die worden gebruikt als opslag doelen kan worden gecompenseerd. (Lees meer over de [vereisten voor NFS-opslag doel](hpc-cache-prereqs.md#nfs-storage-requirements).) Het kan ook de beveiliging verbeteren wanneer deze wordt gebruikt met Azure Blob Storage-doelen.

De standaard instelling is **Ja**. (Caches die vóór april 2020 zijn gemaakt, kunnen de standaard instelling **Nee**hebben.)

Als deze functie is ingeschakeld, wordt het gebruik van de set-UID-machtigings bits in client aanvragen voor de cache ook voor komen.

## <a name="view-snapshots-for-blob-storage-targets"></a>Moment opnamen voor Blob-opslag doelen weer geven

Met Azure HPC cache worden opslag momentopnamen voor Azure Blob-opslag doelen automatisch opgeslagen. Moment opnamen bieden een snel referentie punt voor de inhoud van de back-end-opslag container.

Moment opnamen zijn geen vervanging voor back-ups van gegevens en ze bevatten geen informatie over de status van gegevens in de cache.

> [!NOTE]
> Deze functie voor moment opnamen wijkt af van de momentopname functie die is opgenomen in de opslag software van NetApp of Isilon. Deze moment opname-implementaties nemen wijzigingen van de cache over naar het back-end-opslag systeem voordat de moment opname wordt gemaakt.
>
> Voor efficiëntie worden de wijzigingen in de Azure HPC-cache momentopname niet eerst leeg gemaakt en worden alleen gegevens vastgelegd die naar de BLOB-container zijn geschreven. Deze moment opname vertegenwoordigt niet de status van de gegevens in de cache, waardoor er mogelijk geen recente wijzigingen zijn.

Deze functie is alleen beschikbaar voor Azure Blob-opslag doelen en de configuratie kan niet worden gewijzigd.

Moment opnamen worden elke acht uur genomen, op UTC 0:00, 08:00 en 16:00.

Met de Azure HPC-cache worden dagelijkse, wekelijkse en maandelijkse moment opnamen opgeslagen, totdat deze worden vervangen door nieuwe sjablonen. De limieten zijn:

* Maxi maal 20 dagelijkse moment opnamen
* Maxi maal 8 wekelijkse moment opnamen
* Maxi maal 3 maandelijkse moment opnamen

Open de moment opnamen van de `.snapshot` map in uw Blob Storage-doel naam ruimte.
