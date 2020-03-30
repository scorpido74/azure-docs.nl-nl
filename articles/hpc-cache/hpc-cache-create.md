---
title: Een Azure HPC-cache maken
description: Een Azure HPC-cache-exemplaar maken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: c6090d19ce530829b79dca69636c2123e2519961
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129554"
---
# <a name="create-an-azure-hpc-cache"></a>Een Azure HPC-cache maken

Gebruik de Azure-portal om uw cache te maken.

![schermafbeelding van cacheoverzicht in Azure-portal, met knop maken onderaan](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Basisdetails definiëren

![schermafbeelding van de pagina projectdetails in Azure-portal](media/hpc-cache-create-basics.png)

Selecteer **in Projectdetails**de abonnements- en brongroep die de cache host. Zorg ervoor dat het abonnement op de [toegangslijst](hpc-cache-prereqs.md#azure-subscription) staat.

Stel **in Servicegegevens**de cachenaam en deze andere kenmerken in:

* Locatie - Selecteer een van de [ondersteunde regio's](hpc-cache-overview.md#region-availability).
* Virtueel netwerk - U een bestaand netwerk selecteren of een nieuw virtueel netwerk maken.
* Subnet - Kies of maak een subnet met ten minste 64 IP-adressen (/24) die alleen worden gebruikt voor dit Azure HPC-cacheexemplaar.

## <a name="set-cache-capacity"></a>Cachecapaciteit instellen
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Op de pagina **Cache** moet u de capaciteit van uw cache instellen. De waarden die hier zijn ingesteld, bepalen hoeveel gegevens uw cache kan bevatten en hoe snel de clientaanvragen kunnen worden uitgevoerd.

Capaciteit heeft ook invloed op de kosten van de cache.

Kies de capaciteit door deze twee waarden in te stellen:

* De maximale gegevensoverdrachtssnelheid voor de cache (doorvoer), in GB/seconde
* De hoeveelheid opslagruimte die is toegewezen voor gegevens in de cache, in TB

Kies een van de beschikbare doorvoerwaarden en cacheopslagformaten.

Houd er rekening mee dat de werkelijke gegevensoverdrachtssnelheid afhankelijk is van de werkbelasting, netwerksnelheden en het type opslagdoelen. De waarden die u kiest, stellen de maximale doorvoer in voor het hele cachesysteem, maar een deel daarvan wordt gebruikt voor overheadtaken. Als een client bijvoorbeeld een bestand aanvraagt dat nog niet in de cache is opgeslagen of als het bestand als verouderd is gemarkeerd, gebruikt uw cache een deel van de doorvoer om het bestand uit backend-opslag te halen.

Azure HPC Cache beheert welke bestanden in de cache worden opgeslagen en vooraf zijn geladen om de hitsnelheden in de cache te maximaliseren. De inhoud van de cache wordt continu beoordeeld en bestanden worden verplaatst naar langdurige opslag wanneer ze minder vaak worden geopend. Kies een cacheopslaggrootte die de actieve set werkbestanden comfortabel kan vasthouden met extra ruimte voor metagegevens en andere overheadgegevens.

![schermafbeelding van de pagina cachegrootte](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Resourcetags toevoegen (optioneel)

Op de pagina **Labels** u [brontags](https://go.microsoft.com/fwlink/?linkid=873112) toevoegen aan uw Azure HPC-cache-exemplaar.

## <a name="finish-creating-the-cache"></a>De cache maken

Nadat u de nieuwe cache hebt geconfigureerd, klikt u op het tabblad **Controleren + maken.** De portal valideert uw selecties en laat u uw keuzes bekijken. Als alles juist is, klikt u op **Maken**.

Het maken van caches duurt ongeveer 10 minuten. U de voortgang bijhouden in het meldingenpaneel van de Azure-portal.

![schermafbeelding van pagina's voor het maken van cache 'deployment underway' en 'notifications' in portal](media/hpc-cache-deploy-status.png)

Wanneer het maken is voltooid, wordt een melding weergegeven met een koppeling naar het nieuwe exemplaar Azure HPC-cache en wordt de cache weergegeven in de lijst **Resources** van uw abonnement.
<!-- double check on notification -->

![schermafbeelding van azure HPC-cache-instantie in Azure-portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Volgende stappen

Nadat uw cache in de lijst **Resources** is weergegeven, definieert u opslagdoelen om uw cache toegang te geven tot uw gegevensbronnen.

* [Opslagdoelen toevoegen](hpc-cache-add-storage.md)
