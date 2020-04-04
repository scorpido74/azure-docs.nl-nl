---
title: Installatieupdate 5.1 op storSimple 8000-serie apparaat | Microsoft Documenten
description: Hier wordt uitgelegd hoe u StorSimple 8000 Series Update 5.1 installeert op uw StorSimple 8000-serie apparaat.
services: storsimple
documentationcenter: NA
author: priestlg
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/05/2020
ms.author: v-grpr
ms.openlocfilehash: b90f0f35f908d16b0746075e638fe66769939a64
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80658038"
---
# <a name="install-update-51-on-your-storsimple-device"></a>Update 5.1 installeren op uw StorSimple-apparaat

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt uitgelegd hoe u Update 5.1 installeert op een StorSimple-apparaat waarop een eerdere softwareversie wordt uitgevoerd via de Azure-portal. <!--The hotfix method is used when you are trying to install Update 5.1 on a device running pre-Update 3 versions. The hotfix method is also used when a gateway is configured on a network interface other than DATA 0 of the StorSimple device and you are trying to update from a pre-Update 1 software version.-->

Update 5.1 bevat niet-storende beveiligingsupdates. De niet-storende of regelmatige updates kunnen worden toegepast via de Azure-portal <!--or by the hotfix method-->.

> [!IMPORTANT]
>
> * Update 5.1 is een verplichte update en moet onmiddellijk worden geïnstalleerd. Zie [Update 5.1 release notes](storsimple-update51-release-notes.md)voor meer informatie.
> * Voorafgaand aan de installatie wordt een reeks handmatige en automatische voorcontroles uitgevoerd om de status van het apparaat te bepalen in termen van hardwarestatus en netwerkconnectiviteit. Deze pre-checks worden alleen uitgevoerd als u de updates van de Azure-portal toepast.
> * Als u wilt installeren met de hotfix-methode, neemt u contact op met [Microsoft Support](mailto:support@microsoft.com).

<!--
> * We strongly recommend that when updating a device running versions prior to Update 3, you install the updates using hotfix method. If you encounter any issues, [log a support ticket](storsimple-8000-contact-microsoft-support.md). 


> * We recommend that you install the software and other regular updates via the Azure portal. You should only go to the Windows PowerShell interface of the device (to install updates) if the pre-update gateway check fails in the portal. Depending upon the version you are updating from, the updates may take 4 hours (or greater) to install. The maintenance mode updates must be installed through the Windows PowerShell interface of the device. As maintenance mode updates are disruptive updates, these result in a down time for your device.


> * If running the optional StorSimple Snapshot Manager, ensure that you have upgraded your Snapshot Manager version to Update 5.1 prior to updating the device.
-->

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-51-through-the-azure-portal"></a>Update 5.1 installeren via de Azure-portal

Voer de volgende stappen uit om uw apparaat bij te werken naar [Update 5.1](storsimple-update51-release-notes.md).

> [!NOTE]
> Microsoft haalt aanvullende diagnostische informatie van het apparaat. Als gevolg hiervan, wanneer ons operationele team apparaten identificeert die problemen hebben, zijn we beter uitgerust om informatie van het apparaat te verzamelen en problemen te diagnosticeren.

#### <a name="to-install-an-update-from-the-azure-portal"></a>Een update installeren via Azure Portal

1. Selecteer uw apparaat op de pagina van de StorSimple-service.

    ![Apparaat selecteren](./media/storsimple-8000-install-update-51/update1.png)

2. Ga naar > **Apparaatinstellingen-updates**. **Device settings**

    ![Klik op Apparaatupdates](./media/storsimple-8000-install-update-51/update2.png)

3. Er verschijnt een melding als er nieuwe updates beschikbaar zijn. U ook in het blade **Apparaatupdates** klikken op **Updates scannen**. Er wordt een taak gemaakt om te zoeken naar beschikbare updates. U ontvangt een melding wanneer de taak is voltooid.

    ![Klik op Apparaatupdates](./media/storsimple-8000-install-update-51/update3.png)

4. We raden u aan de releaseopmerkingen te bekijken voordat u een update toepast op uw apparaat. Als u updates wilt toepassen, klikt u op **Updates installeren**. Bekijk in het blade **Voor regelmatige updates bevestigen** de vereisten die u moet voltooien voordat u updates toepast. Schakel het selectievakje in om aan te geven dat u klaar bent om het apparaat bij te werken en klik op **Installeren**.

    ![Klik op Apparaatupdates](./media/storsimple-8000-install-update-51/update4.png)

5. Er wordt een reeks vereiste controles gestart. Deze controles zijn onder andere:
   
   * **Statuscontroles controllers** om te controleren of beide apparaatcontrollers in orde en online zijn.
   * **Statuscontroles hardwareonderdelen** om te controleren of alle hardwareonderdelen op uw StorSimple-apparaat in orde zijn.
   * **DATA 0-controles** om te controleren of DATA 0 is ingeschakeld op uw apparaat. Als deze interface niet is ingeschakeld, schakelt u deze in en probeert u het vervolgens opnieuw.

     De update wordt alleen gedownload en geïnstalleerd als alle controles zijn voltooid. U krijgt een melding wanneer de controles worden uitgevoerd. Als de prechecks mislukken, dan krijgt u de redenen voor het mislukken. Deze problemen aanpakken en vervolgens de bewerking opnieuw proberen. Mogelijk moet u contact opnemen met Microsoft-ondersteuning als u deze problemen niet zelf kunt oplossen.

7. Nadat de voorcontroles zijn voltooid, wordt een updatetaak gemaakt. U krijgt een melding wanneer de bijwerktaak is gemaakt.
   
    ![Maken van bijwerktaak](./media/storsimple-8000-install-update-51/update6.png)
   
    De update wordt vervolgens op het apparaat toegepast.

9. De update neemt enkele uren in beslag. Selecteer de bijwerktaak en klik op **Details** om de details van de taak te bekijken.

    ![Maken van bijwerktaak](./media/storsimple-8000-install-update-51/update8.png)

     U ook de voortgang van de updatetaak volgen vanuit **apparaatinstellingen > Vacatures.** Op het blade **Jobs** ziet u de voortgang van de update.

     ![Maken van bijwerktaak](./media/storsimple-8000-install-update-51/update7.png)

10. Nadat de taak is voltooid, navigeert u naar de **apparaatinstellingen > apparaatupdates**. De softwareversie moet nu worden bijgewerkt.


Controleer of uw apparaat de **StorSimple 8000-serie Update 5.1 (6.3.9600.17885)** gebruikt. De **laatst bijgewerkte datum** moet worden gewijzigd.
<!-- 5.1 - KB 4542887-->

<!--You will now see that the Maintenance mode updates are available (this message might continue to be displayed for up to 24 hours after you install the updates). The steps to install maintenance mode update are detailed in the next section.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## Install Update 5.1 as a hotfix

The software versions that can be upgraded using the hotfix method are:

* Update 0.1, 0.2, 0.3
* Update 1, 1.1, 1.2
* Update 2, 2.1, 2.2
* Update 3, 3.1
* Update 4
* Update 5

> [!NOTE]
> The recommended method to install Update 5.1 is through the Azure portal when trying to update from Update 3 and later version. When updating a device running versions prior to Update 3, use this procedure. You can also use this procedure if you fail the gateway check when trying to install the updates through the Azure portal. The check fails when you have a gateway assigned to a non-DATA 0 network interface and your device is running a software version earlier than Update 1.

The hotfix method involves the following three steps:

1. Download the hotfixes from the Microsoft Update Catalog.
2. Install and verify the regular mode hotfixes.
3. Install and verify the maintenance mode hotfix.

#### Download updates for your device

You must download and install the following hotfixes in the prescribed order and the suggested folders:

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Software update<br> Download both _HcsSoftwareUpdate.exe_ and _CisMSDAgent.exe_ |Regular <br></br>Non-disruptive |~ 25 mins |FirstOrderUpdate|

If updating from a device running Update 4, you only need to install the OS cumulative updates as second order updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |OS cumulative updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|

If installing from a device running Update 3 or earlier, install the following in addition to the cumulative updates.

| Order | KB | Description | Update type | Install time |Install in folder|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI driver and firmware updates <br> USM firmware update (version 3.38) |Regular <br></br>Non-disruptive |~ 3 hrs <br> (includes 2A. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |OS security updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |OS updates package <br> Download Windows Server 2012 R2 version |Regular <br></br>Non-disruptive |- |SecondOrderUpdate|



You may also need to install disk firmware updates on top of all the updates shown in the preceding tables. You can verify whether you need the disk firmware updates by running the `Get-HcsFirmwareVersion` cmdlet. If you are running these firmware versions: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, then you do not need to install these updates.

| Order | KB | Description | Update type | Install time | Install in folder|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Disk firmware |Maintenance <br></br>Disruptive |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * If updating from Update 4, the total install time is close to 4 hours.
> * Before using this procedure to apply the update, make sure that both the device controllers are online and all the hardware components are healthy.

Perform the following steps to download and install the hotfixes.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]
-->
<!--
[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]
-->

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [update 5.1-versie](storsimple-update51-release-notes.md).
