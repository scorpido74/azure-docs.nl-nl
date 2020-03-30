---
title: Een Azure HPC-cache monteren
description: Clients verbinden met een Azure HPC-cacheservice
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582216"
---
# <a name="mount-the-azure-hpc-cache"></a>De Azure HPC-cache monteren

Nadat de cache is gemaakt, kunnen NFS-clients er toegang toe krijgen met een eenvoudige mount-opdracht.

De opdracht bevestiging bestaat uit twee elementen:

* Een van de bevestigingsadressen van de cache (vermeld op de pagina cacheoverzicht)
* Het virtuele naamruimtepad dat u instelt toen u het opslagdoel hebt gemaakt

![schermafbeelding van de overzichtspagina van Azure HPC-cacheinstantie, met een hooglichtvak rond de lijst met bevestigingsadressen rechtsonder](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> De cachemountadressen komen overeen met netwerkinterfaces in het subnet van de cache. In een resourcegroep worden deze NIC's `-cluster-nic-` weergegeven met namen die eindigen op een getal. Wijzig of verwijder deze interfaces niet, anders wordt de cache niet meer beschikbaar.

De virtuele naamruimtepaden worden weergegeven op de pagina **Opslagdoelen.** Klik op een afzonderlijke naam van het opslagdoel om de details te bekijken, inclusief geaggregeerde naamruimtepaden die eraan zijn gekoppeld.

![schermafbeelding van het opslagdoelpaneel van de cache, met een hooglichtvak rond een vermelding in de kolom Pad van de tabel](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Opdrachtsyntaxis monteren

Gebruik een opdracht voor het monteren als volgt:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*opties*}

Voorbeeld:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

Nadat deze opdracht is geslaagd, moet de inhoud ``hpccache`` van de opslagexport zichtbaar zijn in de map op de client.

> [!NOTE] 
> Uw klanten moeten toegang hebben tot het virtuele netwerk en subnet dat uw cache herbergt. Maak bijvoorbeeld client-VM's binnen hetzelfde virtuele netwerk of gebruik een eindpunt, gateway of andere oplossing in het virtuele netwerk voor toegang van buitenaf. Vergeet niet dat niets anders kan worden gehost in het subnet van de cache.

### <a name="mount-command-options"></a>Opdrachtopties instellen

Voor een robuuste clienthouder geeft u deze instellingen en argumenten door in de opdracht monteren: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Aanbevolen opdrachtinstellingen voor het monteren | |
--- | --- 
``hard`` | Soft mounts naar Azure HPC Cache zijn gekoppeld aan toepassingsfouten en mogelijk gegevensverlies. 
``proto=netid`` | Deze optie ondersteunt de juiste afhandeling van NFS-netwerkfouten.
``mountproto=netid`` | Deze optie ondersteunt de juiste afhandeling van netwerkfouten voor montagebewerkingen.
``retry=n`` | Stel ``retry=30`` in om tijdelijke mountfouten te voorkomen. (Een andere waarde wordt aanbevolen in voorgrondmounts.)

## <a name="next-steps"></a>Volgende stappen

* Als u gegevens wilt verplaatsen naar de opslagdoelen van de cache, leest u [Nieuwe Azure Blob-opslag invullen](hpc-cache-ingest.md).
