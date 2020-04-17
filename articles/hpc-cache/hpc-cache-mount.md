---
title: Een Azure HPC-cache monteren
description: Clients verbinden met een Azure HPC-cacheservice
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a44232f06b455e20530271723e816c2117b339a0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458351"
---
# <a name="mount-the-azure-hpc-cache"></a>De Azure HPC-cache monteren

Nadat de cache is gemaakt, kunnen NFS-clients er toegang toe krijgen met een eenvoudige `mount` opdracht. De opdracht verbindt een specifiek opslagdoelpad op de Azure HPC-cache met een lokale map op de clientmachine.

De mount opdracht bestaat uit deze elementen:

* Een van de bevestigingsadressen van de cache (vermeld op de pagina cacheoverzicht)
* Het virtuele naamruimtepad dat u instelt toen u het opslagdoel hebt gemaakt
* Het lokale pad dat u op de client wilt gebruiken
* Opdrachtparameters die het succes van dit soort NFS-mount optimaliseren

Op de pagina **Instructies voor uw** cache instellen verzamelt u de informatie en de aanbevolen opties en wordt een opdracht voor het monteren van een prototype gemaakt die u kopiëren. Lees [Gebruik de mount instructies hulpprogramma](#use-the-mount-instructions-utility) voor meer informatie.

## <a name="prepare-clients"></a>Klanten voorbereiden

Zorg ervoor dat uw clients de Azure HPC-cache kunnen monteren door de richtlijnen in deze sectie te volgen.

### <a name="provide-network-access"></a>Netwerktoegang bieden

De clientmachines moeten toegang hebben tot het virtuele netwerk en het privésubnet van de cache.

Maak bijvoorbeeld client-VM's binnen hetzelfde virtuele netwerk of gebruik een eindpunt, gateway of andere oplossing in het virtuele netwerk voor toegang van buitenaf. (Vergeet niet dat niets anders dan de cache zelf moet worden gehost in het subnet van de cache.)

### <a name="install-utilities"></a>Hulpprogramma's installeren

Installeer de juiste Linux-hulpprogrammasoftware ter ondersteuning van de opdracht NFS-montage:

* Voor Red Hat Enterprise Linux of SuSE:`sudo yum install -y nfs-utils`
* Voor Ubuntu of Debian:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Een lokaal pad maken

Maak een lokaal mappad voor elke client om verbinding te maken met de cache. Maak een pad voor elk naamruimtepad dat u wilt monteren.

Voorbeeld: `sudo mkdir -p /mnt/hpc-cache-1/target3`

De pagina [Instructies monteren](#use-the-mount-instructions-utility) in de Azure-portal bevat een prototypeopdracht die u kopiëren.

Wanneer u de clientmachine met de cache verbindt, koppelt u dit pad aan een virtueel naamruimtepad dat een exportvan opslagdoel vertegenwoordigt. Maak mappen voor elk van de virtuele naamruimtepaden die de client zal gebruiken.

## <a name="use-the-mount-instructions-utility"></a>Gebruik het hulpprogramma voor bevestigingsinstructies

U de pagina **Instructies monteren** in de Azure-portal gebruiken om een kopieerbare houderopdracht te maken. Open de pagina in het gedeelte **Configureren** van de cacheweergave in de portal.

Voordat u de opdracht op een client gebruikt, controleert u of de client `mount` voldoet aan de vereisten en de software heeft die nodig is om de NFS-opdracht te gebruiken zoals hierboven beschreven in [Clients voorbereiden.](#prepare-clients)

![schermafbeelding van een Azure HPC-cache-instantie in de portal, waarbij de pagina Instructies configureren > instellen is geladen](media/mount-instructions.png)

Volg deze procedure om de opdracht monteren te maken.

1. Pas het veld **Clientpad** aan. Dit veld geeft een voorbeeldopdracht die u gebruiken om een lokaal pad op de client te maken. De client heeft lokaal toegang tot de inhoud vanuit de Azure HPC-cache in deze map.

   Klik op het veld en bewerk de opdracht om de gewenste mapnaam te bevatten. De naam wordt aan het einde van de tekenreeks weergegeven na`sudo mkdir -p`

   ![schermafbeelding van het veld clientpad met cursor aan het einde](media/mount-edit-client.png)

   Nadat u het veld hebt bewerkt, wordt de opdracht onder aan de pagina bijgewerkt met het nieuwe clientpad.

1. Kies het **bevestigingsadres cache** in de lijst. In dit menu worden alle [clientbevestigingspunten](#find-mount-command-components)van de cache weergegeven.

   Balanceer de belasting van de client op alle beschikbare bevestigingsadressen voor betere cacheprestaties.

   ![schermafbeelding van het adresveld cachemount, waarbij kiezer drie IP-adressen weergeeft om uit te kiezen](media/mount-select-ip.png)

1. Kies het **pad Voor virtuele naamruimte** dat u voor de client wilt gebruiken. Deze paden koppelen aan export op het back-end opslagsysteem.

   ![schermafbeelding van het veld naamruimtepaden, waarbij kiezer is geopend](media/mount-select-target.png)

   U virtuele naamruimtepaden weergeven en wijzigen op de portalpagina Opslagdoelen. Lees [Opslagdoelen toevoegen](hpc-cache-add-storage.md) om te zien hoe.

   Lees [De samengevoegde naamruimte plannen](hpc-cache-namespace.md)voor meer informatie over de geaggregeerde naamruimtefunctie van Azure HPC Cache.

1. Het **opdrachtveld Monteren** in stap drie wordt automatisch gevuld met een aangepaste mountopdracht die gebruikmaakt van het bevestigingsadres, het virtuele naamruimtepad en het clientpad dat u in de vorige velden hebt ingesteld.

   Klik op het kopieersymbool aan de rechterkant van het veld om het automatisch naar het klembord te kopiëren.

   ![schermafbeelding van het veld naamruimtepaden, waarbij kiezer is geopend](media/mount-command-copy.png)

1. Gebruik de gekopieerde houder op de clientmachine om deze aan te sluiten op de Azure HPC-cache. U de opdracht rechtstreeks vanaf de opdrachtregel van de client uitvoeren of de opdracht voor het instellen van een client opnemen in een script of sjabloon voor het instellen van de client.

## <a name="understand-mount-command-syntax"></a>De syntaxis van de opdracht voor het monteren begrijpen

De opdracht monteren heeft het volgende formulier:

> sudo mount {*opties*} *cache_mount_address*:/*namespace_path* *local_path*

Voorbeeld:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

Nadat deze opdracht is geslaagd, is de inhoud ``hpccache`` van de opslagexport zichtbaar in de map op de client.

### <a name="mount-command-options"></a>Opdrachtopties instellen

Voor een robuuste clienthouder geeft u deze instellingen en argumenten door in de opdracht monteren:

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Aanbevolen opdrachtinstellingen voor het monteren | |
--- | ---
``hard`` | Soft mounts naar Azure HPC Cache zijn gekoppeld aan toepassingsfouten en mogelijk gegevensverlies.
``proto=tcp`` | Deze optie ondersteunt de juiste afhandeling van NFS-netwerkfouten.
``mountproto=tcp`` | Deze optie ondersteunt de juiste afhandeling van netwerkfouten voor montagebewerkingen.
``retry=<value>`` | Stel ``retry=30`` in om tijdelijke mountfouten te voorkomen. (Een andere waarde wordt aanbevolen in voorgrondmounts.)

### <a name="find-mount-command-components"></a>Opdrachtcomponenten zoeken

Als u een mountopdracht wilt maken zonder de pagina **Instructies voor de montage** te gebruiken, u de bevestigingsadressen vinden op de pagina **CacheOverzicht** en de virtuele naamruimtepaden op de **doelpagina Opslag.**

![schermafbeelding van de overzichtspagina van Azure HPC-cacheinstantie, met een hooglichtvak rond de lijst met bevestigingsadressen rechtsonder](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> De cachemountadressen komen overeen met netwerkinterfaces in het subnet van de cache. In een resourcegroep worden deze NIC's `-cluster-nic-` weergegeven met namen die eindigen op een getal. Wijzig of verwijder deze interfaces niet, anders wordt de cache niet meer beschikbaar.

De virtuele naamruimtepaden worden weergegeven op de detailpagina van elk opslagdoel. Klik op een afzonderlijke naam van het opslagdoel om de details te bekijken, inclusief geaggregeerde naamruimtepaden die eraan zijn gekoppeld.

![schermafbeelding van de detailpagina van een opslagdoel (koptekst 'Opslagdoel bijwerken'). Er is een hooglichtvak rond een vermelding in de kolom Virtuele naamruimtevan de tabel](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Volgende stappen

* Als u gegevens wilt verplaatsen naar de opslagdoelen van de cache, leest u [Nieuwe Azure Blob-opslag invullen](hpc-cache-ingest.md).
