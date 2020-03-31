---
title: Chassis vervangen op storSimple 8000-serie | Microsoft Documenten
description: Beschrijft hoe u het chassis voor uw StorSimple primaire behuizing of EBOD-behuizing verwijderen en vervangen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 7dfc39f4d08c8a49d1564a0a5bd7e3ef4156e3fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61312442"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Het chassis van uw StorSimple-apparaat vervangen
## <a name="overview"></a>Overzicht
In deze zelfstudie wordt uitgelegd hoe u een chassis in een StorSimple 8000-serie verwijderen en vervangen. De StorSimple 8100 model is een enkele behuizing apparaat (een chassis), terwijl de 8600 is een dubbele behuizing apparaat (twee chassis). Voor een 8600-model zijn er mogelijk twee chassis dat in het apparaat kan uitvallen: het chassis voor de primaire behuizing of het chassis voor de EBOD-behuizing.

In beide gevallen is het vervangende chassis dat door Microsoft wordt verzonden leeg. Er worden geen Power- en Cooling Modules (PCM's), controllermodules, solid state disk drives (SSD's), harde schijven (HDD's) of EBOD-modules meegeleverd.

> [!IMPORTANT]
> Voordat u het chassis verwijdert en vervangt, controleert u de veiligheidsinformatie in [StorSimple hardwarecomponentvervanging.](storsimple-8000-hardware-component-replacement.md)


## <a name="remove-the-chassis"></a>Het chassis verwijderen
Voer de volgende stappen uit om het chassis op uw StorSimple-apparaat te verwijderen.

#### <a name="to-remove-a-chassis"></a>Een chassis verwijderen
1. Zorg ervoor dat het StorSimple-apparaat is uitgeschakeld en losgekoppeld van alle stroombronnen.
2. Verwijder indien van toepassing alle netwerk- en SAS-kabels.
3. Haal het apparaat uit het rek.
4. Verwijder elk van de stations en noteer de sleuven waaruit ze worden verwijderd. Zie [Het schijfstation verwijderen](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive)voor meer informatie .
5. Verwijder op de EBOD-behuizing (als dit het chassis is dat is mislukt), verwijder de EBOD-controllermodules. Zie [Een EBOD-controller verwijderen](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller)voor meer informatie.
   
    Op de primaire behuizing (als dit het chassis is dat is mislukt), verwijdert u de controllers en noteert u de sleuven waaruit ze worden verwijderd. Zie [Een controller verwijderen](storsimple-8000-controller-replacement.md#remove-a-controller)voor meer informatie.

## <a name="install-the-chassis"></a>Het chassis installeren
Voer de volgende stappen uit om het chassis op uw StorSimple-apparaat te installeren.

#### <a name="to-install-a-chassis"></a>Een chassis installeren
1. Monteer het chassis in het rek. Zie Voor meer informatie: [houd uw StorSimple 8100-apparaat](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) op een rack-mount of [rack-mount uw StorSimple 8600-apparaat .](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device)
2. Nadat het chassis in het rek is gemonteerd, installeert u de controllermodules in dezelfde posities waarin ze eerder zijn geïnstalleerd.
3. Installeer de schijven in dezelfde posities en sleuven waarin ze eerder zijn geïnstalleerd.
   
   > [!NOTE]
   > We raden u aan de SSD's eerst in de sleuven te installeren en vervolgens de HDD's te installeren.
  
4. Als het apparaat in het rek is gemonteerd en de onderdelen zijn geïnstalleerd, sluit u het apparaat aan op de juiste stroombronnen en schakelt u het apparaat in. Zie Kabel [uw StorSimple 8100-apparaat](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) of [kabel uw StorSimple 8600-apparaat](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het vervangen van StorSimple-hardwarecomponenten](storsimple-8000-hardware-component-replacement.md).

