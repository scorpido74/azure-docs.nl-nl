---
title: Back-upbeleid voor de StorSimple 8000-serie beheren | Microsoft Documenten
description: Hier wordt uitgelegd hoe u de StorSimple Device Manager-service gebruiken voor het maken en beheren van handmatige back-ups, back-upschema's en back-upretentie op een apparaat uit de StorSimple 8000-serie.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/05/2017
ms.author: alkohli
ms.openlocfilehash: 607379f8645226a031646376df9ca18f4d3164bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267792"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-manage-backup-policies"></a>De StorSimple Device Manager-service in Azure-portal gebruiken om back-upbeleid te beheren


## <a name="overview"></a>Overzicht

In deze zelfstudie wordt uitgelegd hoe u het beleidblade van de StorSimple Device **Manager-service Back-up** gebruiken om back-upprocessen en back-upretentie voor uw StorSimple-volumes te beheren. Het beschrijft ook hoe u een handmatige back-up voltooien.

Wanneer u een back-up van een volume maakt, u ervoor kiezen om een lokale momentopname of een cloudmomentopname te maken. Als u een back-up maakt van een lokaal vastgemaakt volume, raden we u aan een cloudmomentopname op te geven. Het nemen van een groot aantal lokale momentopnamen van een lokaal vastgemaakt volume in combinatie met een gegevensset met veel verloop zal resulteren in een situatie waarin u snel uit de lokale ruimte zou kunnen raken. Als u ervoor kiest om lokale momentopnamen te maken, raden we u aan minder dagelijkse momentopnamen te maken om een back-up te maken van de meest recente status, deze een dag te bewaren en deze vervolgens te verwijderen.

Wanneer u een cloudmomentopname maakt van een lokaal vastgemaakt volume, kopieert u alleen de gewijzigde gegevens naar de cloud, waar deze worden gededupliceerd en gecomprimeerd.

## <a name="the-backup-policy-blade"></a>Het beleidsblad Back-up

Met **het back-upbeleidsblad** voor uw StorSimple-apparaat u back-upbeleid beheren en lokale en cloudmomentopnamen plannen. Back-upbeleid wordt gebruikt om back-upschema's en back-upretentie te configureren voor een verzameling volumes. Met back-upbeleid u een momentopname van meerdere volumes tegelijk maken. Dit betekent dat de back-ups die door een back-upbeleid zijn gemaakt, crashconsistente kopieën zijn.

Met de tabeltabellijst voor back-upbeleid u het bestaande back-upbeleid ook filteren op een of meer van de volgende velden:

* **Beleidsnaam** : de naam die aan het beleid is gekoppeld. De verschillende soorten polissen omvatten:

  * Gepland beleid, dat expliciet door de gebruiker is gemaakt.
  * Geïmporteerd beleid, dat oorspronkelijk is gemaakt in de StorSimple Snapshot Manager. Deze hebben een tag die de StorSimple Snapshot Manager-host beschrijft waarvan het beleid is geïmporteerd.

  > [!NOTE]
  > Automatisch of standaard back-upbeleid is niet langer ingeschakeld op het moment van het maken van volume.

* **Laatste succesvolle back-up** - De datum en tijd van de laatste succesvolle back-up die is genomen met dit beleid.

* **Volgende back-up** : de datum en tijd van de volgende geplande back-up die door dit beleid wordt geïnitieerd.

* **Volumes** – De volumes die aan het beleid zijn gekoppeld. Alle volumes die zijn gekoppeld aan een back-upbeleid worden gegroepeerd wanneer back-ups worden gemaakt.

* **Schema's** : het aantal schema's dat is gekoppeld aan het back-upbeleid.

De veelgebruikte bewerkingen die u uitvoeren voor back-upbeleid zijn:

* Een back-upbeleid toevoegen
* Een schema toevoegen of wijzigen
* Een volume toevoegen of verwijderen
* Een back-upbeleid verwijderen
* Neem een handmatige back-up

## <a name="add-a-backup-policy"></a>Een back-upbeleid toevoegen

Voeg een back-upbeleid toe om uw back-ups automatisch te plannen. Wanneer u voor het eerst een volume maakt, is er geen standaardback-upbeleid gekoppeld aan uw volume. U moet een back-upbeleid toevoegen en toewijzen om volumegegevens te beschermen.

Voer de volgende stappen uit in de Azure-portal om een back-upbeleid voor uw StorSimple-apparaat toe te voegen. Nadat u het beleid hebt toegevoegd, u een planning definiëren (zie [Een planning toevoegen of wijzigen).](#add-or-modify-a-schedule)

[!INCLUDE [storsimple-8000-add-backup-policy-u2](../../includes/storsimple-8000-add-backup-policy-u2.md)]

## <a name="add-or-modify-a-schedule"></a>Een schema toevoegen of wijzigen

U een schema toevoegen of wijzigen dat is gekoppeld aan een bestaand back-upbeleid op uw StorSimple-apparaat. Voer de volgende stappen uit in de Azure-portal om een planning toe te voegen of te wijzigen.

[!INCLUDE [storsimple-8000-add-modify-backup-schedule](../../includes/storsimple-8000-add-modify-backup-schedule-u2.md)]


## <a name="add-or-remove-a-volume"></a>Een volume toevoegen of verwijderen

U een volume toevoegen of verwijderen dat is toegewezen aan een back-upbeleid op uw StorSimple-apparaat. Voer de volgende stappen uit in de Azure-portal om een volume toe te voegen of te verwijderen.

[!INCLUDE [storsimple-8000-add-volume-backup-policy-u2](../../includes/storsimple-8000-add-remove-volume-backup-policy-u2.md)]


## <a name="delete-a-backup-policy"></a>Een back-upbeleid verwijderen

Voer de volgende stappen uit in de Azure-portal om een back-upbeleid op uw StorSimple-apparaat te verwijderen.

[!INCLUDE [storsimple-8000-delete-backup-policy](../../includes/storsimple-8000-delete-backup-policy.md)]

## <a name="take-a-manual-backup"></a>Neem een handmatige back-up

Voer de volgende stappen uit in de Azure-portal om een on-demand (handmatige) back-up te maken voor één volume.

[!INCLUDE [storsimple-8000-create-manual-backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

