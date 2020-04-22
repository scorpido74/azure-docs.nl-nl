---
title: Opties voor gegevensmigratie van Apparaten uit de StorSimple 8000-serie
description: Biedt een overzicht van de opties om gegevens uit de StorSimple 8000-serie te migreren.
services: storsimple
author: priestlg
ms.service: storsimple
ms.topic: article
ms.date: 03/25/2020
ms.author: v-grpr
ms.openlocfilehash: 974bcc657b811a10e28b41150439e83d26a208d2
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767052"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Opties voor het migreren van gegevens uit de StorSimple 8000-serie

> [!IMPORTANT]
> Op 31 december 2022 bereikt de StorSimple 8000-serie de status end of support (EOS). We raden klanten uit de StorSimple 8000-serie aan te migreren naar een van de alternatieven die in het document worden beschreven.

StorSimple 8000 serie bereikt [einde van support](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) in december 2022. De klanten die storsimple 8000-serie uitvoeren, hebben een optie om te upgraden naar andere hybride services van Azure first party. In dit artikel worden de hybride azure-opties beschreven die beschikbaar zijn om gegevens te migreren.

## <a name="migration-options"></a>Migratieopties

De klanten die StorSimple 8000-serie gebruiken, hebben Azure- of externe opties.

### <a name="azure-options"></a>Azure-opties

#### <a name="migrate-to-azure-file-sync"></a>Migreren naar Azure File Sync

Met deze gloednieuwe migratieoptie kunnen klanten de bestandsshares van hun organisatie opslaan in de Azure-bestanden. Deze bestanden worden vervolgens gecentraliseerd voor on-premises toegang met Azure File Sync (AFS). AFS kan worden geïmplementeerd op een Windows Server-host. De werkelijke gegevensmigratie wordt vervolgens uitgevoerd als hostkopie of met behulp van het migratiehulpprogramma.

Ga voor meer informatie over het migreren van gegevens naar Azure File Sync naar [StorSimple 8100 en 8600-migratie naar Azure File Sync.](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)

### <a name="third-party-options"></a>Opties van derden

#### <a name="migrate-to-panzura-freedom-nas"></a>Migreren naar Panzura Freedom NAS

Klanten uit de StorSimple 5000-7000-serie en de StorSimple 8000-serie kunnen ervoor kiezen om te migreren naar Panzura Freedom NAS om hun gegevens in Azure te bewaren. De Panzura Freedom-oplossing biedt een NAS-oplossing die datacenters, kantoren, publieke en private clouds omvat. De oplossing maakt lokale, hybride en in-cloud dataworkflows mogelijk voor NFS-, Mkb- en mobiele clients.

Deze migratie wordt ondersteund door Panzura en klanten kunnen aan de slag door migratieondersteuning aan te vragen via de [Panzura-website.](https://panzura.com/migrate-storsimple-panzura/)

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Migratie - Veelgestelde vragen

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>V. Wanneer bereiken de StorSimple 8000-serie apparaten het einde van de service?

A. StorSimple 8000-serie bereikt [einde ondersteuning](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) in december 2022. Het einde van de ondersteuning houdt in dat Microsoft na december 2022 geen ondersteuning meer kan bieden voor zowel hardware als software van deze apparaten. We raden u ten zeerste aan om een plan te gaan formuleren om de gegevens nu van uw apparaten te migreren.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>V. Wat gebeurt er met de gegevens die ik in Azure heb opgeslagen?  

A. U de gegevens in Azure blijven gebruiken zodra u deze migreert naar een nieuwere service.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>V. Wat gebeurt er met de gegevens die ik lokaal heb opgeslagen op mijn StorSimple-apparaat?

A. De gegevens die zich op het lokale apparaat bevinden, kunnen worden gekopieerd naar de nieuwere service zoals beschreven in de migratiedocumenten.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>V. Wat gebeurt er als ik mijn StorSimple 8000-serie toestel wil houden?

A. Hoewel de services mogelijk blijven werken, kan Microsoft geen hardware- en softwareondersteuning meer bieden. Migratie wordt sterk aanbevolen voor bedrijfscontinuïteit.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>V. Welke opties zijn beschikbaar om gegevens van StorSimple 8000-serie apparaten te migreren?

A. Afhankelijk van hun scenario hebben gebruikers van de StorSimple 8000-serie de volgende migratieopties:

* **Migreren naar Azure File Sync**: Gebruik deze optie wanneer u wilt overschakelen naar azure native format. U Azure File Sync gebruiken voor gecentraliseerd beheer van bestandsshares.

* **Andere opties:** U contact opnemen met Microsoft Support om migratieopties te bespreken die hier niet worden vermeld.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>V. Wordt migratie naar andere opslagoplossingen ondersteund?

A. Ja. Migratie naar andere opslagoplossingen met behulp van hostkopie van de gegevens wordt ondersteund.

### <a name="q-is-migration-supported-by-microsoft"></a>V. Wordt migratie ondersteund door Microsoft?

A. Migreren uit de 8000-serie is een volledig ondersteunde bewerking. In feite raadt Microsoft aan om contact op te nemen met ondersteuning voordat u met migratie begint. Migratie is momenteel een ondersteunde bewerking. Als u gegevens wilt migreren van uw StorSimple 8000-serieapparaat, [neemt u contact op met de ondersteuning van StorSimple.](mailto:storsimp@microsoft.com)

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>V. Wat is het prijsmodel voor migratie naar Azure File Sync?

A. Bij het gebruik van Azure File Sync kunnen de abonnementskosten voor de service van toepassing zijn. Klanten zullen ook lopende opslagkosten moeten betalen. Raadpleeg [afs-prijzen]( https://azure.microsoft.com/pricing/details/storage/files/) voor een schatting.

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>V. Hoe lang duurt het om een migratie te voltooien?

A. De tijd om gegevens te migreren is afhankelijk van de hoeveelheid gegevens en de geselecteerde upgradeoptie.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens uit een StorSimple 8000-serie migreren naar Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
