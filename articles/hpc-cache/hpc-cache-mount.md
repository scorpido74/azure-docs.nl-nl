---
title: Een Azure HPC-cache monteren
description: Clients verbinden met een Azure HPC-cacheservice
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657356"
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

Maak een lokaal mappad voor elke client om verbinding te maken met de cache. Maak een pad voor elk opslagdoel dat u wilt monteren.

Voorbeeld: `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>Gebruik het hulpprogramma voor bevestigingsinstructies

Open de pagina **Instructies instellen** in het gedeelte **Configureren** van de cacheweergave in de Azure-portal.

![schermafbeelding van een Azure HPC-cache-instantie in de portal, waarbij de pagina Instructies configureren > instellen is geladen](media/mount-instructions.png)

De opdrachtpagina voor het monteren bevat informatie over het proces voor het monteren van de client en vereisten, plus velden die u gebruiken om een kopieerbare mountopdracht te maken.

Volg de volgende procedure om deze pagina te gebruiken:

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. Bekijk de vereisten voor clients en installeer de `mount` hulpprogramma's die nodig zijn om de NFS-opdracht te gebruiken zoals hierboven beschreven in [Clients voorbereiden.](#prepare-clients)

1. Stap één van Het opzetten van **uw bestandssysteem**<!-- label will change --> geeft een voorbeeldopdracht voor het maken van het lokale pad op de client. Dit is het pad dat de client gebruikt om toegang te krijgen tot de inhoud vanuit de Azure HPC-cache.

   Let op de padnaam, zodat u deze indien nodig wijzigen in de opdracht.

1. Selecteer in stap twee een van de beschikbare IP-adressen. Alle client mount [punten](#find-mount-command-components) van de cache zijn hier vermeld. Zorg ervoor dat u een systeem hebt om de belasting tussen alle IP-adressen in evenwicht te brengen.

1. Het veld in stap drie wordt automatisch gevuld met een prototype mount opdracht. Klik op het kopieersymbool aan de rechterkant van het veld om het automatisch naar het klembord te kopiëren.

   > [!NOTE]
   > Controleer de opdracht kopiëren voordat u deze gebruikt. Mogelijk moet u het pad voor clientbevestiging en het virtuele naamruimtepad voor opslagdoel aanpassen, die nog niet in deze interface kunnen worden geselecteerd. U moet ook de opties voor de opdracht voor het monteren bijwerken om de aanbevolen [opties](#mount-command-options) hieronder weer te geven. Lees [De syntaxis van de opdracht Begrijpen](#understand-mount-command-syntax) voor hulp.

1. Gebruik de gekopieerde houder (indien nodig bewerkingen) op de clientmachine om deze aan te sluiten op het opslagdoel in de Azure HPC-cache. U de opdracht rechtstreeks vanaf de opdrachtregel van de client uitvoeren of de opdracht voor het instellen van een client opnemen in een script of sjabloon voor het instellen van de client.

## <a name="understand-mount-command-syntax"></a>De syntaxis van de opdracht voor het monteren begrijpen

De opdracht monteren heeft het volgende formulier:

> sudo mount *cache_mount_address*:/*namespace_path* *local_path* {*opties*}

Voorbeeld:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
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

Als u een mountopdracht wilt maken zonder de pagina **Instructies voor de montage** te gebruiken, u de bevestigingsadressen vinden op de pagina **CacheOverzicht** en de virtuele naamruimtepaden op de pagina **Opslagdoelen.**

![schermafbeelding van de overzichtspagina van Azure HPC-cacheinstantie, met een hooglichtvak rond de lijst met bevestigingsadressen rechtsonder](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> De cachemountadressen komen overeen met netwerkinterfaces in het subnet van de cache. In een resourcegroep worden deze NIC's `-cluster-nic-` weergegeven met namen die eindigen op een getal. Wijzig of verwijder deze interfaces niet, anders wordt de cache niet meer beschikbaar.

De virtuele naamruimtepaden worden weergegeven op de pagina **Opslagdoelen.** Klik op een afzonderlijke naam van het opslagdoel om de details te bekijken, inclusief geaggregeerde naamruimtepaden die eraan zijn gekoppeld.

![schermafbeelding van het opslagdoelpaneel van de cache, met een hooglichtvak rond een vermelding in de kolom Pad van de tabel](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Volgende stappen

* Als u gegevens wilt verplaatsen naar de opslagdoelen van de cache, leest u [Nieuwe Azure Blob-opslag invullen](hpc-cache-ingest.md).
