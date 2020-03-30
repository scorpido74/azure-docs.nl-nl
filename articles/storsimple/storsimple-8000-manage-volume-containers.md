---
title: Volumecontainers beheren voor apparaten uit de StorSimple 8000-serie
description: Hier wordt uitgelegd hoe u de pagina Containers voor het volume van de StorSimple Device Manager-service gebruiken om een volumecontainer toe te voegen, te wijzigen of te verwijderen.
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
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: cbdad3c68848ce552811ee658bb29df74a6fad19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267675"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Gebruik de StorSimple-apparaatbeheerfunctie om StorSimple-volumecontainers te beheren

## <a name="overview"></a>Overzicht
In deze zelfstudie wordt uitgelegd hoe u de StorSimple Device Manager-service gebruiken voor het maken en beheren van StorSimple-volumecontainers.

Een volumecontainer in een Microsoft Azure StorSimple-apparaat bevat een of meer volumes die opslagaccount-, versleutelings- en bandbreedteverbruiksinstellingen delen. Een apparaat kan meerdere volumecontainers hebben voor al zijn volumes. 

Een volumecontainer heeft de volgende kenmerken:

* **Volumes** : de gelaagde of lokaal vastgemaakte StorSimple-volumes die zich in de volumecontainer bevinden. 
* **Versleuteling** : een versleutelingssleutel die voor elke volumecontainer kan worden gedefinieerd. Deze sleutel wordt gebruikt voor het versleutelen van de gegevens die van uw StorSimple-apparaat naar de cloud worden verzonden. Een militaire-grade AES-256 bit sleutel wordt gebruikt met de gebruiker ingevoerde sleutel. Om uw gegevens te beveiligen, raden we u aan altijd cloudopslagversleuteling in te schakelen.
* **Opslagaccount** : het Azure-opslagaccount dat wordt gebruikt om de gegevens op te slaan. Alle volumes die zich in een volumecontainer bevinden, delen deze opslagrekening. U een opslagaccount kiezen uit een bestaande lijst of een nieuw account maken wanneer u de volumecontainer maakt en vervolgens de toegangsreferenties voor dat account opgeven.
* **Cloudbandbreedte** : de bandbreedte die door het apparaat wordt verbruikt wanneer de gegevens van het apparaat naar de cloud worden verzonden. U een bandbreedtebeheer afdwingen door een waarde tussen 1 Mbps en 1000 Mbps op te geven wanneer u deze container maakt. Als u wilt dat het apparaat alle beschikbare bandbreedte verbruikt, stelt u dit veld in op **Onbeperkt.** U ook een bandbreedtesjabloon maken en toepassen om bandbreedte toe te wijzen op basis van planning.

In de volgende procedures wordt uitgelegd hoe u het **storSimple volume-containerblad** gebruiken om de volgende algemene bewerkingen te voltooien:

* Een volumecontainer toevoegen
* Een volumecontainer wijzigen
* Een volumecontainer verwijderen

## <a name="add-a-volume-container"></a>Een volumecontainer toevoegen
Voer de volgende stappen uit om een volumecontainer toe te voegen.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Een volumecontainer wijzigen
Voer de volgende stappen uit om een volumecontainer te wijzigen.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Een volumecontainer verwijderen
Een volumecontainer heeft volumes erin. Het kan alleen worden verwijderd als alle volumes die erin staan eerst worden verwijderd. Voer de volgende stappen uit om een volumecontainer te verwijderen.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het beheren van StorSimple-volumes.](storsimple-8000-manage-volumes-u2.md) 
* Meer informatie over [het gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)

