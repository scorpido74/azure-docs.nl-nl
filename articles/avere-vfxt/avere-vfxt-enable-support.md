---
title: Ondersteuning voor avere vFXT-Azure inschakelen
description: Ondersteuning voor het inschakelen van AVERE vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: ac7db46a681fcde6bfcbb7695e2d66724f738918
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256227"
---
# <a name="enable-support-uploads"></a>Ondersteuningsuploads inschakelen

Met de avere vFXT voor Azure kunnen automatisch ondersteunings gegevens over uw cluster worden geüpload. Met deze uploads kunnen ondersteunings medewerkers de best mogelijke klanten service bieden.

## <a name="steps-to-enable-uploads"></a>Stappen voor het inschakelen van uploads

Voer de volgende stappen uit in het configuratie scherm van AVERE om ondersteuning te activeren. (Lees [toegang tot het vFXT-cluster](avere-vfxt-cluster-gui.md) voor meer informatie over het openen van het configuratie scherm van AVERE.)

1. Ga bovenaan naar het tabblad **instellingen** .
1. Klik links op de koppeling **ondersteuning** en accepteer het privacybeleid.

   ![Scherm opname van het configuratie scherm van AVERE en het pop-upvenster met de knop bevestigen om het privacybeleid te accepteren](media/avere-vfxt-privacy-policy.png)

1. Klik op het drie hoekje links van **klant gegevens** om de sectie uit te vouwen.
1. Klik op de knop **Upload gegevens opnieuw valideren** .
1. Stel de ondersteunings naam van het cluster in de **unieke naam** van het cluster in. Zorg ervoor dat het cluster uniek wordt geïdentificeerd ter ondersteuning van mede werkers.
1. Schakel de selectie vakjes in voor **Statistieken bewaking**, **algemene informatie uploaden**en **informatie over het uploaden van crashes**.
1. Klik op **Submit**

   ![Scherm afbeelding met de sectie met voltooide klant gegevens van de pagina met ondersteunings instellingen](media/avere-vfxt-support-info.png)

1. Klik op het drie hoekje links van **beveiligde proactieve ondersteuning (SPS)** om de sectie uit te vouwen.
1. Schakel het selectie vakje in voor het inschakelen van de **SPS-koppeling**.
1. Klik op **Submit**

   ![Scherm afbeelding met de sectie beveiligde proactieve ondersteuning op de pagina ondersteunings instellingen](media/avere-vfxt-support-sps.png)

## <a name="next-steps"></a>Volgende stappen

Als u een on-premises of een bestaand Cloud opslag systeem aan het cluster wilt toevoegen, volgt u de instructies in [opslag configureren](avere-vfxt-add-storage.md). 

Als u klaar bent om aan de slag te gaan met het koppelen van clients aan het cluster, lees dan [het avere vFXT-cluster koppelen](avere-vfxt-mount-clients.md).