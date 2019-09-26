---
title: Een HPC-cache van Azure maken (preview)
description: Een Azure HPC-cache-exemplaar maken
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 09/24/2019
ms.author: v-erkell
ms.openlocfilehash: a0590c14032595bea685c69962ef27dca14d1d69
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300022"
---
# <a name="create-an-azure-hpc-cache-preview"></a>Een HPC-cache van Azure maken (preview)

Gebruik de Azure Portal om uw cache te maken. 

![scherm opname van het cache-overzicht in Azure Portal, met de knop maken onderaan](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Basis Details definiëren

![scherm afbeelding van de pagina project details in Azure Portal](media/hpc-cache-create-basics.png)

Selecteer in **Project Details**het abonnement en de resource groep die als host moet fungeren voor de cache. Zorg ervoor dat het abonnement wordt weer gegeven in de lijst met [toegangs voorbeelden](hpc-cache-prereqs.md#azure-subscription) .

Stel in **service Details**de naam van de cache en de andere kenmerken in:

* Locatie: Selecteer een van de [ondersteunde regio's](hpc-cache-overview.md#region-availability).
* Virtueel netwerk: u kunt een bestaande selecteren of een nieuw virtueel netwerk maken.
* Subnet: Kies of maak een subnet met ten minste 64 IP-adressen (/24) die alleen worden gebruikt voor deze Azure HPC-cache-instantie.

## <a name="set-cache-capacity"></a>Cache capaciteit instellen
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Op de pagina **cache** moet u de capaciteit van uw cache instellen. Met deze waarde wordt bepaald hoeveel gegevens uw cache kan bevatten en hoe snel deze client aanvragen kunnen worden verwerkt. 

Na de open bare preview-periode is de capaciteit ook van invloed op de kosten van de cache.

De cache capaciteit wordt gemeten in i/o-bewerkingen per seconde (IOPS). Kies de capaciteit door deze twee waarden in te stellen:

* De maximale snelheid van gegevens overdracht voor de cache (door Voer), in GB/seconde
* De hoeveelheid opslag ruimte die is toegewezen voor gegevens in de cache, in TB

Kies een van de beschik bare doorvoer waarden en cache opslag grootten. De capaciteit van IOPS wordt berekend en weer gegeven onder de waarde-selectors.

Houd er wel voor dat de werkelijke gegevens overdrachts snelheid afhankelijk is van de werk belasting, de netwerk snelheid en het type opslag doelen. De waarde die u kiest, stelt de maximale door Voer voor de volledige cache in en niet alle voor client aanvragen. Als een client bijvoorbeeld een bestand aanvraagt dat niet al in de cache is opgeslagen, of als het bestand is gemarkeerd als verouderd, gebruikt de cache een deel van de door Voer om het op te halen van back-end-opslag.

Met Azure HPC cache kunt u beheren welke bestanden in de cache worden opgeslagen en vooraf worden geladen om het aantal cache treffers te maximaliseren. De cache-inhoud wordt voortdurend beoordeeld en bestanden worden verplaatst naar lange termijn opslag wanneer ze minder vaak worden gebruikt. Kies een cache opslag grootte die kan worden ingesteld op de actieve set met werk bestanden, met extra ruimte voor meta gegevens en andere overhead.

![scherm afbeelding van de pagina cache grootte](media/hpc-cache-create-iops.png)

## <a name="add-storage-targets"></a>Opslagdoelen toevoegen

Opslag doelen zijn de back-end-lange termijn opslag voor de inhoud van uw cache.

U kunt opslag doelen definiëren tijdens het maken van de cache, maar u kunt ze nader hand ook toevoegen met de koppeling in het gedeelte **configureren** van de pagina van de cache in de portal.

![scherm afbeelding van de pagina opslag doelen](media/hpc-cache-storage-targets-pop.png)

Klik op de **koppeling opslag doel toevoegen** om uw back-end-opslag systemen te definiëren. De opslag kan Azure Blob-containers of on-premises NFS-systemen zijn.

U kunt Maxi maal tien verschillende opslag doelen definiëren.

Stapsgewijze instructies voor het toevoegen van een opslag doel zijn opgenomen in [opslag doelen toevoegen](hpc-cache-add-storage.md). De procedure wijkt af van Blob Storage of voor NFS-exports.

Hier volgen enkele tips:

* Voor beide typen opslag moet u opgeven hoe het back-end-opslag systeem (een NFS-adres of een BLOB-container naam) en het pad naar de client gerichte naam ruimte worden gezocht.

* Wanneer u een Blob-opslag doel maakt, moet u ervoor zorgen dat de cache toegangs machtigingen heeft voor het opslag account, zoals beschreven in [de toegangs beheer rollen toevoegen](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account). Als u niet zeker weet dat de rolconfiguratie slaagt, maakt u eerst de cache en voegt u daarna de Blob-opslag toe.

* Geef bij het maken van een NFS-opslag doel een [gebruiks model](hpc-cache-add-storage.md#choose-a-usage-model)op. De instelling gebruiks model helpt de cache uw werk stroom te optimaliseren.

## <a name="add-resource-tags-optional"></a>Resource Tags toevoegen (optioneel)

Op de pagina **Tags** kunt u [resource Tags](https://go.microsoft.com/fwlink/?linkid=873112) toevoegen aan uw Azure HPC-cache-exemplaar.

## <a name="finish-creating-the-cache"></a>Het maken van de cache volt ooien

Nadat u de nieuwe cache hebt geconfigureerd, klikt u op het tabblad **controleren + maken** . In de portal worden uw selecties gevalideerd en kunt u uw keuzes controleren. Als alles correct is, klikt u op **maken**. 

Het maken van de cache duurt ongeveer 10 minuten. U kunt de voortgang volgen in het deel venster meldingen van de Azure Portal. 

![scherm opname van pagina's voor het maken van de implementatie van de cache en meldingen in de portal](media/hpc-cache-deploy-status.png)

Wanneer het maken is voltooid, wordt er een melding weer gegeven met een koppeling naar het nieuwe Azure HPC-cache-exemplaar. de cache wordt weer gegeven in de lijst met **resources** van uw abonnement. 

![scherm opname van het Azure HPC-cache-exemplaar in Azure Portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Volgende stappen

Nadat uw cache in de lijst met **resources** wordt weer gegeven, kunt u deze koppelen voor client toegang, deze gebruiken om uw werksetgegevens te verplaatsen naar een nieuw Azure Blob-opslag doel of extra gegevens bronnen te definiëren.

* [De Azure HPC-cache koppelen](hpc-cache-mount.md)
* [Gegevens verplaatsen naar Azure Blob-opslag voor Azure HPC-cache](hpc-cache-ingest.md)
* [Opslag doelen toevoegen](hpc-cache-add-storage.md)
