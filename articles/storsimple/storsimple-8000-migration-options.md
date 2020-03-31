---
title: Opties voor gegevensmigratie van StorSimple 5000-7000-serie apparaten
description: Biedt een overzicht van de opties om gegevens uit de StorSimple 5000-7000-serie te migreren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 44a05ecb273bdf7582300c3b6a9110e2ada0994c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471819"
---
# <a name="options-to-migrate-data-from-storsimple-5000-7000-series"></a>Opties voor het migreren van gegevens uit de collectie StorSimple 5000-7000 

> [!IMPORTANT]
> Op 9 juli 2019 bereikt de StorSimple 5000/7000-serie de status end of support (EOS). We raden klanten uit de StorSimple 5000/7000-serie aan te migreren naar een van de alternatieven die in het document worden beschreven.

De StorSimple 5000-7000-serie bereikt [het einde van de ondersteuning](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) in juli 2019. De klanten die storsimple 5000-7000-serie draaien, hebben een optie om te upgraden naar andere hybride services van Azure first party. In dit artikel worden de hybride azure-opties beschreven die beschikbaar zijn om gegevens te migreren. 

## <a name="migration-options"></a>Migratieopties

De klanten die storSimple 5000-7000-serie gebruiken, hebben Azure- of externe opties.

### <a name="azure-options"></a>Azure-opties

#### <a name="upgrade-to-storsimple-8000-series"></a>Upgrade naar StorSimple 8000-serie

Upgrade naar StorSimple 8000-serie en ga zo verder op het StorSimple-platform.  Dit upgradepad vereist dat klanten hun 5000-7000-serie apparaten vervangen door een 8000-serie. De gegevens worden gemigreerd van de 5000-7000-serie apparaat met behulp van de migratie tool. Zodra de migratie is voltooid, blijven de apparaten uit de StorSimple 8000-serie gegevens naar Azure Blob Storage brengen. 

Ga voor meer informatie over het migreren van gegevens met behulp van een StorSimple 8000-serie naar [Gegevens migreren van de StorSimple 5000-7000-serie naar een apparaat uit de 8000-serie.](storsimple-8000-migrate-from-5000-7000.md)

#### <a name="migrate-to-azure-file-sync"></a>Migreren naar Azure File Sync

Met deze gloednieuwe migratieoptie kunnen klanten de bestandsshares van hun organisatie opslaan in de Azure-bestanden. Deze bestanden worden vervolgens gecentraliseerd voor on-premises toegang met Azure File Sync (AFS). AFS kan worden geïmplementeerd op een Windows Server-host. De werkelijke gegevensmigratie wordt vervolgens uitgevoerd als hostkopie of met behulp van het migratiehulpprogramma.

Ga voor meer informatie over het migreren van gegevens naar Azure File Sync naar [Gegevens migreren van StorSimple 5000-7000-reeksen naar Azure File Sync.](storsimple-5000-7000-afs-migration.md)

### <a name="third-party-options"></a>Opties van derden

#### <a name="migrate-to-panzura-freedom-nas"></a>Migreren naar Panzura Freedom NAS

StorSimple 5000-7000-klanten kunnen ervoor kiezen om te migreren naar Panzura Freedom NAS om hun gegevens in Azure te bewaren. Panzura Freedom-oplossing biedt een NAS-oplossing die datacenters, kantoren, publieke en private clouds omvat. De oplossing maakt lokale, hybride en in-cloud dataworkflows mogelijk voor NFS-, Mkb- en mobiele clients. 

Deze migratie wordt ondersteund door Panzura en klanten kunnen aan de slag door migratieondersteuning aan te vragen via de [Panzura-website.](https://panzura.com/storsimple-migration/)

#### <a name="migrate-to-cohesity"></a>Migreren naar Cohesity

Met Cohesity u gegevens van uw huidige StorSimple 5000-7000 migreren naar het Cohesity Data Platform op Azure. Het Cohesity Data Platform is een softwaregedefinieerde webschaaloplossing die bestanden, back-ups, objecten en VM's consolideert tot één cloud-native oplossing. Na migratie naar het dataplatform u gegevens en apps van cloud naar kern beheren, beveiligen en inrichten via één ruit. Met Cohesity begint u met slechts drie knooppunten. 

Meer informatie over [migratie naar het Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### <a name="migrate-to-nasuni"></a>Migreren naar Nasuni

Nasuni maakt het voor StorSimple 5000-7000-klanten gemakkelijk om te migreren en hun gegevens in Azure te bewaren.  Nasuni is een toonaangevende OP Azure gebaseerde NAS-opslagoplossing, die klanten de prestaties en beveiliging biedt die ze verwachten van on-prem-oplossingen, met cloud-economie en schaal.  Naast krachtige bestandsopslag verwerken Nasuni en Azure back-ups en DR, terwijl u uw gegevens over de hele wereld delen en samenwerken met gecentraliseerd beheer van bestandsopslag. 

Nasuni heeft de ervaring om uw migratie eenvoudig te maken – vandaag nog aan de slag:https://info.nasuni.com/nasuni-storsimple-migration

#### <a name="migrate-to-talon-fast"></a>Migreren naar Talon FAST

Talon maakt het gemakkelijk voor StorSimple 5000-7000 klanten om gebruik te blijven maken van de voordelen die ze zo veel hebben gewaardeerd in het StorSimple-platform (kleine on-site footprint ondersteund door onbeperkte cloudresources) met een nog grotere functie.  Met de Talon FAST-oplossing kunnen klanten migreren en hun gegevens in Azure bewaren, terwijl ze nu een nog kleinere externe voetafdruk hebben die alleen voor software is en voordelen toevoegen, zoals wereldwijde bestandsvergrendeling, wereldwijde naamruimte en samenwerking met meerdere sites.  Talon is een toonaangevende Azure-ecosysteemoplossing en werkt samen met wereldwijde klanten om hun on-premises bestandsserverworkloads te migreren naar een geconsolideerde, op Azure gebaseerde voetafdruk zonder dat dit ten koste gaat van de werkstroom of -ervaring van de gebruiker.  

Meer informatie over hoe u evolueren https://www.talonstorage.com/alliances/microsoft-storsimplenaar een cloudgeconsolideerde onderneming op.


## <a name="migration---frequently-asked-questions"></a>Migratie - Veelgestelde vragen

### <a name="q-when-do-the-storsimple-5000-and-7000-series-devices-reach-end-of-service"></a>V. Wanneer bereiken de StorSimple 5000- en 7000-serie apparaten het einde van de service? 

A. De StorSimple 5000-7000-serie bereikt [het einde van de service](https://support.microsoft.com/lifecycle/search?alpha=StorSimple%205000%2F7000%20Series) in juli 2019. Het einde van de service houdt in dat Microsoft na juli 2019 geen ondersteuning meer kan bieden voor zowel hardware als software van deze apparaten. We raden u ten zeerste aan om een plan te gaan formuleren om de gegevens nu van uw apparaten te migreren.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>V. Wat gebeurt er met de gegevens die ik in Azure heb opgeslagen?  

A. U de gegevens in Azure blijven gebruiken zodra u deze migreert naar een nieuwere service. 


### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>V. Wat gebeurt er met de gegevens die ik lokaal heb opgeslagen op mijn StorSimple-apparaat? 

A. De gegevens die zich op het lokale apparaat bevinden, kunnen worden gekopieerd naar de nieuwere service zoals beschreven in de migratiedocumenten.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-50007000-series-appliance"></a>V. Wat gebeurt er als ik mijn StorSimple 5000/7000-serie toestel wil behouden? 

A. Hoewel de services mogelijk blijven werken, kan Microsoft geen hardware- en softwareondersteuning meer bieden. Migratie wordt sterk aanbevolen voor bedrijfscontinuïteit.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-5000-7000-series-devices"></a>V. Welke opties zijn beschikbaar om gegevens te migreren van StorSimple 5000-7000-serie apparaten? 

A. Afhankelijk van hun scenario hebben gebruikers van de StorSimple 5000-7000-serie de volgende migratieopties. 

 - **Upgrade naar 8000-serie:** gebruik deze optie wanneer u verder wilt gaan op het StorSimple-platform. 
 - **Migreren naar Azure File Sync**: Gebruik deze optie wanneer u wilt overschakelen naar azure native format. U Azure File Sync gebruiken voor gecentraliseerd beheer van bestandsshares. 

U contact opnemen met Microsoft Support om migratieopties te bespreken die hier niet worden vermeld.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>V. Wordt migratie naar andere opslagoplossingen ondersteund?

A. Ja. Migratie naar andere opslagoplossingen met behulp van hostkopie van de gegevens wordt ondersteund.

### <a name="q-is-migration-supported-by-microsoft"></a>V. Wordt migratie ondersteund door Microsoft? 

A. Migreren van 5000 of 7000-serie is een volledig ondersteunde bewerking. In feite raadt Microsoft aan om contact op te nemen met ondersteuning voordat u met migratie begint. Migratie is momenteel een ondersteunde bewerking. Als u gegevens wilt migreren van uw StorSimple 5000-7000-serie apparaat, [opent u een supportticket.](storsimple-8000-contact-microsoft-support.md)

### <a name="q-what-is-the-pricing-model-for-both-the-migration-options"></a>V. Wat is het prijsmodel voor beide migratieopties?

A. De migratiekosten zijn afhankelijk van de optie die u kiest. Hoewel migratie zelf gratis is, als u besluit om te upgraden naar een StorSimple 8000-serie, zullen er de kosten van het hardwareapparaat zijn. 

Ook bij het gebruik van Azure File Sync kunnen de abonnementskosten voor de service van toepassing zijn. In elk geval moeten klanten ook lopende opslagkosten betalen. Raadpleeg het volgende voor een schatting: 
- [StorSimple prijzen](https://azure.microsoft.com/pricing/details/storsimple/)  
- [AFS-prijzen]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q--how-long-does-it-take-to-complete-a-migration"></a>V.  Hoe lang duurt het om een migratie te voltooien?

A. De tijd om gegevens te migreren is afhankelijk van de hoeveelheid gegevens en de geselecteerde upgradeoptie. 

### <a name="q-what-is-the-end-of-support-date-for-storsimple-8000-series"></a>V. Wat is de einddatum voor de StorSimple 8000-serie?

A. De einddatum voor storsimple 8000-serie wordt [hier](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series)gepubliceerd.


## <a name="next-steps"></a>Volgende stappen
 - [Migreer gegevens uit een StorSimple 5000-7000-serie naar een apparaat uit de 8000-serie.](storsimple-8000-migrate-from-5000-7000.md)
 - [Gegevens uit een StorSimple 5000-7000-serie migreren naar Azure File Sync](storsimple-5000-7000-afs-migration.md)
