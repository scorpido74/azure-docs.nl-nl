---
title: Een HPC-cache van Azure maken
description: Een Azure HPC-cache-exemplaar maken
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: rohogue
ms.openlocfilehash: 07aba1b1536635e414fc5fab4ece148683909188
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168545"
---
# <a name="create-an-azure-hpc-cache"></a>Een HPC-cache van Azure maken

Gebruik de Azure Portal om uw cache te maken.

![scherm opname van het cache-overzicht in Azure Portal, met de knop maken onderaan](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Basis Details definiëren

![scherm afbeelding van de pagina project details in Azure Portal](media/hpc-cache-create-basics.png)

Selecteer in **Project Details**het abonnement en de resource groep die als host moet fungeren voor de cache. Zorg ervoor dat het abonnement op de [toegangs](hpc-cache-prereqs.md#azure-subscription) lijst staat.

Stel in **service Details**de naam van de cache en de andere kenmerken in:

* Locatie: Selecteer een van de [ondersteunde regio's](hpc-cache-overview.md#region-availability).
* Virtueel netwerk: u kunt een bestaande selecteren of een nieuw virtueel netwerk maken.
* Subnet: Kies of maak een subnet met ten minste 64 IP-adressen (/24) die alleen worden gebruikt voor deze Azure HPC-cache-instantie.

## <a name="set-cache-capacity"></a>Cache capaciteit instellen
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Op de pagina **cache** moet u de capaciteit van uw cache instellen. De waarden die u hier instelt, bepalen hoeveel gegevens uw cache kan bevatten en hoe snel deze client aanvragen kunnen worden verwerkt.

Na de open bare preview-periode is de capaciteit ook van invloed op de kosten van de cache.

Kies de capaciteit door deze twee waarden in te stellen:

* De maximale snelheid van gegevens overdracht voor de cache (door Voer), in GB/seconde
* De hoeveelheid opslag ruimte die is toegewezen voor gegevens in de cache, in TB

Kies een van de beschik bare doorvoer waarden en cache opslag grootten.

Houd er wel voor dat de werkelijke gegevens overdrachts snelheid afhankelijk is van de werk belasting, de netwerk snelheid en het type opslag doelen. Met de waarden die u kiest, stelt u de maximale door Voer voor het hele cache systeem in, maar een aantal dat wordt gebruikt voor overhead taken. Als een client bijvoorbeeld een bestand aanvraagt dat niet al in de cache is opgeslagen, of als het bestand is gemarkeerd als verouderd, gebruikt de cache een deel van de door Voer om het op te halen van back-end-opslag.

Met Azure HPC cache kunt u beheren welke bestanden in de cache worden opgeslagen en vooraf worden geladen om het aantal cache treffers te maximaliseren. De cache-inhoud wordt voortdurend beoordeeld en bestanden worden verplaatst naar lange termijn opslag wanneer ze minder vaak worden gebruikt. Kies een cache opslag grootte die kan worden ingesteld op de actieve set met werk bestanden, met extra ruimte voor meta gegevens en andere overhead.

![scherm afbeelding van de pagina cache grootte](media/hpc-cache-create-capacity.png)

## <a name="add-resource-tags-optional"></a>Resource Tags toevoegen (optioneel)

Op de pagina **Tags** kunt u [resource Tags](https://go.microsoft.com/fwlink/?linkid=873112) toevoegen aan uw Azure HPC-cache-exemplaar.

## <a name="finish-creating-the-cache"></a>Het maken van de cache volt ooien

Nadat u de nieuwe cache hebt geconfigureerd, klikt u op het tabblad **controleren + maken** . In de portal worden uw selecties gevalideerd en kunt u uw keuzes controleren. Als alles correct is, klikt u op **maken**.

Het maken van de cache duurt ongeveer 10 minuten. U kunt de voortgang volgen in het deel venster meldingen van de Azure Portal.

![scherm opname van pagina's voor het maken van de implementatie van de cache en meldingen in de portal](media/hpc-cache-deploy-status.png)

Wanneer het maken is voltooid, wordt er een melding weer gegeven met een koppeling naar het nieuwe Azure HPC-cache-exemplaar. de cache wordt weer gegeven in de lijst met **resources** van uw abonnement.
<!-- double check on notification -->

![scherm opname van het Azure HPC-cache-exemplaar in Azure Portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Volgende stappen

Nadat uw cache is weer gegeven in de lijst met **resources** , definieert u de opslag doelen om uw cache toegang te geven tot uw gegevens bronnen.

* [Opslag doelen toevoegen](hpc-cache-add-storage.md)
