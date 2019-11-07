---
title: Een HPC-cache van Azure koppelen
description: Clients verbinden met een Azure HPC cache-service
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582216"
---
# <a name="mount-the-azure-hpc-cache"></a>De Azure HPC-cache koppelen

Nadat de cache is gemaakt, hebben NFS-clients toegang tot de server met een eenvoudige koppelings opdracht.

De koppelings opdracht bestaat uit twee elementen:

* Een van de koppel adressen van de cache (vermeld op de pagina overzicht van cache)
* Het pad van de virtuele naam ruimte dat u instelt tijdens het maken van het opslag doel

![scherm afbeelding van de overzichts pagina van het Azure HPC-cache-exemplaar, met een markeer stift rond de lijst met koppelings adressen op de rechter benedenhoek](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> De cache koppel adressen komen overeen met netwerk interfaces in het subnet van de cache. In een resource groep worden deze Nic's vermeld met namen die eindigen op `-cluster-nic-` en een getal. Wijzig of verwijder deze interfaces niet, of de cache is niet meer beschikbaar.

De virtuele naam ruimte paden worden weer gegeven op de pagina **opslag doelen** . Klik op een individuele opslag doel naam om de details ervan weer te geven, inclusief geaggregeerde naam ruimte-paden die eraan zijn gekoppeld.

![scherm afbeelding van het opslag doel paneel van de cache, met een markeer stift rond een vermelding in de kolom pad van de tabel](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Syntaxis van koppelings opdracht

Gebruik een koppelings opdracht zoals de volgende:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*Opties*}

Voorbeeld:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Wanneer deze opdracht is geslaagd, moet de inhoud van de opslag export zichtbaar zijn in de map ``hpccache`` op de client.

> [!NOTE] 
> Uw clients moeten toegang hebben tot het virtuele netwerk en het subnet dat uw cache heeft. Maak bijvoorbeeld client-Vm's binnen hetzelfde virtuele netwerk, of gebruik een eind punt, gateway of andere oplossing in het virtuele netwerk voor toegang vanaf buiten. Houd er rekening mee dat niets anders kan worden gehost in het subnet van de cache.

### <a name="mount-command-options"></a>Opdracht Opties koppelen

Voor een robuuste client koppeling geeft u deze instellingen en argumenten door in de opdracht Mount: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Aanbevolen opdracht instellingen voor koppelen | |
--- | --- 
``hard`` | Zachte koppelingen naar de Azure HPC-cache zijn gekoppeld aan toepassings fouten en mogelijke gegevens verlies. 
``proto=netid`` | Met deze optie wordt de juiste verwerking van NFS-netwerk fouten ondersteund.
``mountproto=netid`` | Deze optie biedt ondersteuning voor de juiste verwerking van netwerk fouten voor koppelings bewerkingen.
``retry=n`` | Stel ``retry=30`` in om storingen van de tijdelijke koppeling te voor komen. (Een andere waarde wordt aanbevolen in voorgrond koppelingen.)

## <a name="next-steps"></a>Volgende stappen

* Als u gegevens naar de opslag doelen van de cache wilt verplaatsen, leest u [nieuwe Azure Blob-opslag vullen](hpc-cache-ingest.md).
