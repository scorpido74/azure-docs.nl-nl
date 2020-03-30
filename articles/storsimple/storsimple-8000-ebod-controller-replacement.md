---
title: Een StorSimple 8600 EBOD-controller vervangen | Microsoft Documenten
description: Legt uit hoe u een of beide EBOD-controllers op een StorSimple 8600-apparaat verwijderen en vervangen.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: b05d1f36d1e74b3d915e216676859654fbcbacf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254883"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Een EBOD-controller vervangen op uw StorSimple-apparaat

## <a name="overview"></a>Overzicht
In deze zelfstudie wordt uitgelegd hoe u een defecte EBOD-controllermodule op uw Microsoft Azure StorSimple-apparaat vervangen. Als u een EBOD-controllermodule wilt vervangen, moet u het:

* De defecte EBOD-controller verwijderen
* Een nieuwe EBOD-controller installeren

Houd rekening met de volgende informatie voordat u begint:

* Lege EBOD-modules moeten in alle ongebruikte sleuven worden geplaatst. De behuizing koelt niet goed af als een sleuf open blijft.
* De EBOD-controller is hot-swappable en kan worden verwijderd of vervangen. Verwijder een defecte module pas als u een vervanging hebt. Wanneer u het vervangingsproces start, moet u het binnen 10 minuten voltooien.

> [!IMPORTANT]
> Voordat u een StorSimple-onderdeel probeert te verwijderen of te vervangen, moet u de conventies en andere [veiligheidsmaatregelen](storsimple-safety.md)van het [veiligheidspictogram](storsimple-safety.md#safety-icon-conventions) bekijken.

## <a name="remove-an-ebod-controller"></a>Een EBOD-controller verwijderen
Voordat u de defecte EBOD-controllermodule in uw StorSimple-apparaat vervangt, moet u ervoor zorgen dat de andere EBOD-controllermodule actief en actief is. In de volgende procedure en tabel wordt uitgelegd hoe u de module EBOD-controller verwijderen.

#### <a name="to-remove-an-ebod-module"></a>Een EBOD-module verwijderen
1. Open Azure Portal.
2. Ga naar uw apparaat en navigeer naar de status **Van de hardware instellingen** > **Hardware health**en controleer of de status van de LED voor de actieve EBOD-controllermodule groen is en de LED voor de defecte EBOD-controllermodule rood is.
3. Zoek de defecte EBOD-controllermodule aan de achterkant van het apparaat.
4. Verwijder de kabels die de EBOD-controllermodule op de controller verbinden voordat u de EBOD-module uit het systeem haalt.
5. Noteer de exacte SAS-poort van de EBOD-controllermodule die is aangesloten op de controller. U moet het systeem in deze configuratie herstellen nadat u de EBOD-module hebt vervangen.
   
   > [!NOTE]
   > Normaal gesproken is dit poort A, die in het volgende diagram als **Host** wordt aangeduid.
   
    ![Backplane van EBOD-controller](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Figuur 1** Achterkant van De module van EBOD
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Fout LED |
   | 2 |Power LED |
   | 3 |SAS-connectoren |
   | 4 |SAS LED's |
   | 5 |Alleen seriële poorten voor gebruik in de fabriek |
   | 6 |Poort A (Host in) |
   | 7 |Poort B (Host uit) |
   | 8 |Poort C (alleen in de fabriek) |

## <a name="install-a-new-ebod-controller"></a>Een nieuwe EBOD-controller installeren
In de volgende procedure en tabel wordt uitgelegd hoe u een EBOD-controllermodule in uw StorSimple-apparaat installeert.

#### <a name="to-install-an-ebod-controller"></a>Een EBOD-controller installeren
1. Controleer het EBOD-apparaat op schade, vooral aan de interfaceconnector. Installeer de nieuwe EBOD-controller niet als er pinnen zijn gebogen.
2. Schuif de module in de behuizing totdat de vergrendelingen in de open positie staan.
   
    ![EBOD-controller installeren](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Figuur 2**  Installatie van de EBOD-controllermodule
3. Sluit de vergrendeling. U hoort een klik als de vergrendeling in schakelt.
   
    ![EBOD-vergrendeling loslaten](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Figuur 3**  De EBOD-modulevergrendeling sluiten
4. Sluit de kabels opnieuw aan. Gebruik de exacte configuratie die aanwezig was voor de vervanging. Zie het volgende diagram en de volgende tabel voor meer informatie over het aansluiten van de kabels.
   
    ![Kabel uw 4U-apparaat voor stroom](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Figuur 4**. Kabels opnieuw aansluiten
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |Primaire behuizing |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Controller 0 |
   | 5 |Controller 1 |
   | 6 |EBOD-controller 0 |
   | 7 |EBOD-controller 1 |
   | 8 |EBOD-behuizing |
   | 9 |Stroomverdelingseenheden |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het vervangen van StorSimple-hardwarecomponenten](storsimple-8000-hardware-component-replacement.md).

