---
title: StorSimple Virtual Array web gebruikersinterfacebeheer | Microsoft Documenten
description: Beschrijft hoe u basistaken voor apparaatbeheer uitvoert via de webgebruikersinterface van StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 92671206a4171ca838423f55b526191ef30e5c35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254727"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Gebruik de webgebruikersinterface om uw StorSimple Virtual Array te beheren
![installatieprocesstroom](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Overzicht
De zelfstudies in dit artikel zijn van toepassing op de Microsoft Azure StorSimple Virtual Array (ook bekend als het storSimple on-premises virtuele apparaat) met de release algemene beschikbaarheid (GA) van maart 2016. In dit artikel worden enkele complexe werkstromen en beheertaken beschreven die kunnen worden uitgevoerd op de Virtuele Array van StorSimple. U de StorSimple Virtual Array beheren met de Gebruikersinterface van de StorSimple Manager-service (aangeduid als de gebruikersinterface van de portal) en de lokale web-gebruikersinterface voor het apparaat. In dit artikel wordt zich gericht op de taken die u uitvoeren met de webgebruikersinterface.

Dit artikel bevat de volgende zelfstudies:

* De versleutelingssleutel voor servicegegevens
* Problemen met installatiefouten in de web-gebruikersinterface oplossen
* Een logboekpakket genereren
* Apparaat uitschakelen of opnieuw opstarten

## <a name="get-the-service-data-encryption-key"></a>De versleutelingssleutel voor servicegegevens
Er wordt een versleutelingssleutel voor servicegegevens gegenereerd wanneer u uw eerste apparaat registreert bij de StorSimple Manager-service. Deze sleutel is dan vereist met de serviceregistratiesleutel om extra apparaten te registreren bij de StorSimple Manager-service.

Als u de versleutelingssleutel voor servicegegevens kwijt bent en deze moet ophalen, voert u de volgende stappen uit in de lokale webgebruikersinterface van het apparaat dat bij uw service is geregistreerd.

#### <a name="to-get-the-service-data-encryption-key"></a>De versleutelingssleutel voor servicegegevens
1. Maak verbinding met de lokale webgebruikersinterface. Ga naar > **Configuratiecloudinstellingen**. **Configuration**
2. Klik onder aan de pagina op **Versleutelingssleutel servicegegevens opvragen.** Er verschijnt een sleutel. Kopieer en bewaar deze sleutel.
   
    ![versleutelingssleutel voor servicegegevens krijgen 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Problemen met installatiefouten in de web-gebruikersinterface oplossen
In sommige gevallen wanneer u het apparaat configureert via de lokale webgebruikersinterface, u fouten tegenkomen. Als u dergelijke fouten wilt diagnosticeren en oplossen, u de diagnostische tests uitvoeren.

#### <a name="to-run-the-diagnostic-tests"></a>De diagnostische tests uitvoeren
1. Ga in de lokale webgebruikersinterface naar**Diagnostische tests** **oplossen** > .
   
    ![diagnostische gegevens uitvoeren 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. Klik onder aan de pagina op **Diagnostische tests uitvoeren**. Hiermee worden tests gestart om eventuele problemen met uw netwerk, apparaat, webproxy, tijd of cloudinstellingen te diagnosticeren. U krijgt een melding dat het apparaat tests uitvoert.
3. Nadat de tests zijn voltooid, worden de resultaten weergegeven. In het volgende voorbeeld worden de resultaten van diagnostische tests weergegeven. Houd er rekening mee dat de webproxy-instellingen niet zijn geconfigureerd op dit apparaat en dat de webproxytest daarom niet is uitgevoerd. Alle andere tests voor netwerkinstellingen, DNS-server en tijdinstellingen zijn geslaagd.
   
    ![diagnostische gegevens uitvoeren 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Een logboekpakket genereren
Een logboekpakket bestaat uit alle relevante logboeken die Microsoft Support kunnen helpen bij het oplossen van apparaatproblemen. In deze release kan een logpakket worden gegenereerd via de lokale web-gebruikersinterface.

#### <a name="to-generate-the-log-package"></a>Het logboekpakket genereren
1. Ga in de lokale webgebruikersinterface naar **Logboeken van probleemoplossingssysteem** > **System logs**.
   
    ![logboekpakket genereren 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. Klik onder aan de pagina op **Logboekpakket maken**. Er wordt een pakket van de systeemlogboeken gemaakt. Dit duurt een paar minuten.
   
    ![logboekpakket genereren 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    U wordt hiervan op de hoogte gesteld nadat het pakket is gemaakt en de pagina wordt bijgewerkt om de tijd en datum aan te geven waarop het pakket is gemaakt.
   
    ![logboekpakket genereren 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. Klik **op Logboekpakket downloaden**. Een zip-pakket zal worden gedownload op uw systeem.
   
    ![logboekpakket genereren 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. U het gedownloade logboekpakket uitpakken en de systeemlogboekbestanden bekijken.

## <a name="shut-down-and-restart-your-device"></a>Uw apparaat afsluiten en opnieuw opstarten
U uw virtuele apparaat afsluiten of opnieuw opstarten met behulp van de lokale web-gebruikersinterface. We raden u aan voordat u opnieuw wordt opgestart, de volumes of aandelen offline te halen op de host en vervolgens op het apparaat. Dit minimaliseert de kans op gegevensbeschadiging. 

#### <a name="to-shut-down-your-virtual-device"></a>Uw virtuele apparaat uitschakelen
1. Ga in de lokale webgebruikersinterface naar De instellingen **voor onderhoudskracht** > **Power settings**.
2. Klik onder aan de pagina op **Afsluiten**.
   
    ![apparaat afsluiten 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. Er verschijnt een waarschuwing waarin staat dat een uitschakeling van het apparaat elke IO die aan de gang was zal onderbreken, wat resulteert in een downtime. Klik op het vinkje ![vinkje](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![waarschuwing voor het afsluiten van het apparaat](./media/storsimple-ova-web-ui-admin/image37.png)
   
    U krijgt een melding dat de afsluiting is gestart.
   
    ![apparaatuitschakeling gestart](./media/storsimple-ova-web-ui-admin/image38.png)
   
    Het apparaat wordt nu afgesloten. Als u uw apparaat wilt starten, moet u dat doen via de Hyper-V Manager.

#### <a name="to-restart-your-virtual-device"></a>Uw virtuele apparaat opnieuw opstarten
1. Ga in de lokale webgebruikersinterface naar De instellingen **voor onderhoudskracht** > **Power settings**.
2. Klik onder aan de pagina op **Opnieuw starten**.
   
    ![apparaat opnieuw opstarten](./media/storsimple-ova-web-ui-admin/image36.png)
3. Er verschijnt een waarschuwing waarin staat dat het opnieuw opstarten van het apparaat alle IOs die aan de gang waren zal onderbreken, wat resulteert in een downtime. Klik op het vinkje ![vinkje](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![waarschuwing opnieuw opstarten](./media/storsimple-ova-web-ui-admin/image37.png)
   
    U krijgt een melding dat de herstart is gestart.
   
    ![herstart gestart](./media/storsimple-ova-web-ui-admin/image39.png)
   
    Terwijl de herstart aan de gang is, verliest u de verbinding met de gebruikersinterface. U de herstart controleren door de gebruikersinterface periodiek te vernieuwen. U ook de herstartstatus van het apparaat controleren via de Hyper-V Manager.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [gebruik van de StorSimple Manager-service om uw apparaat te beheren.](storsimple-virtual-array-manager-service-administration.md)

