---
title: Uw StorSimple-apparaat bijwerken | Microsoft Documenten
description: Hier wordt uitgelegd hoe u de functie StorSimple-update gebruiken om updates en hotfixes in de onderhoudsmodus te installeren.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: twooley
ms.openlocfilehash: c9451afaefdd220b5f87d4650c7844f06926b03a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933425"
---
# <a name="update-your-storsimple-8000-series-device"></a>Uw StorSimple 8000-serie apparaat bijwerken
> [!NOTE]
> De klassieke portal voor StorSimple is afgeschaft. De StorSimple-apparaatbeheerfuncties worden automatisch volgens schema verplaatst naar de nieuwe Azure Portal. U ontvangt hierover een e-mailbericht en een portalmelding. Dit document wordt binnenkort ook ingetrokken. Zie [FAQ: Move to Azure portal](storsimple-8000-move-azure-portal-faq.md) (Veelgestelde vragen: verplaatsing naar Azure Portal) voor vragen met betrekking tot de verplaatsing.

## <a name="overview"></a>Overzicht
Met de StorSimple-updates u uw StorSimple-apparaat eenvoudig up-to-date houden. Afhankelijk van het updatetype u updates toepassen op het apparaat via de klassieke Azure-portal of via de Windows PowerShell-interface. In deze zelfstudie worden de updatetypen beschreven en hoe u elk van deze typen installeren.

U twee typen apparaatupdates toepassen: 

* Regelmatige (of normale modus) updates
* Updates van de onderhoudsmodus

U regelmatige updates installeren via de klassieke Azure-portal of Windows PowerShell; U moet windows PowerShell echter gebruiken om updates in de onderhoudsmodus te installeren. 

Elk updatetype wordt hieronder afzonderlijk beschreven.

### <a name="regular-updates"></a>Regelmatige updates
Regelmatige updates zijn niet-storende updates die kunnen worden geïnstalleerd wanneer het apparaat in de normale modus staat. Deze updates worden via de Microsoft Update-website toegepast op elke apparaatcontroller. 

> [!IMPORTANT]
> Er kan een mislukte controller optreden tijdens het updateproces. Dit heeft echter geen invloed op de beschikbaarheid of werking van het systeem.
> 
> 

* Zie Regelmatige updates installeren via de klassieke Azure-portal voor meer informatie over het installeren van regelmatige updates [via de klassieke Azure-portal.](#install-regular-updates-via-the-azure-classic-portal)
* U ook regelmatige updates installeren via Windows PowerShell voor StorSimple. Zie Regelmatige [updates installeren via Windows PowerShell voor StorSimple voor](#install-regular-updates-via-windows-powershell-for-storsimple)meer informatie.

### <a name="maintenance-mode-updates"></a>Updates van de onderhoudsmodus
Updates in de onderhoudsmodus zijn storende updates, zoals upgrades van de schijffirmware. Voor deze updates moet het apparaat in de onderhoudsmodus worden geplaatst. Zie Stap [2: Onderhoudsmodus invoeren](#step2)voor meer informatie. U de klassieke Azure-portal niet gebruiken om updates in de onderhoudsmodus te installeren. In plaats daarvan moet u Windows PowerShell gebruiken voor StorSimple. 

Zie Updates van de [onderhoudsmodus installeren via Windows PowerShell voor StorSimple voor](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)meer informatie over het installeren van updates in de onderhoudsmodus.

> [!IMPORTANT]
> Updates in de onderhoudsmodus moeten afzonderlijk op elke controller worden toegepast. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Reguliere updates installeren via de klassieke Azure-portal
U de klassieke Azure-portal gebruiken om updates toe te passen op uw StorSimple-apparaat.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Installeer regelmatige updates via Windows PowerShell voor StorSimple
U windows PowerShell voor StorSimple ook gebruiken om regelmatige (normale modus) updates toe te passen.

> [!IMPORTANT]
> Hoewel u regelmatig updates installeren met Windows PowerShell voor StorSimple, raden we u ten zeerste aan om regelmatige updates te installeren via de klassieke Azure-portal. Vanaf update 1 worden vooraf controles uitgevoerd voordat updates van de portal worden geïnstalleerd. Deze pre-controles zullen storingen voorkomen en zorgen voor een soepelere ervaring. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Updates voor de onderhoudsmodus installeren via Windows PowerShell voor StorSimple
U gebruikt Windows PowerShell voor StorSimple om onderhoudsmodusupdates toe te passen op uw StorSimple-apparaat. Alle I/O-aanvragen worden onderbroken in deze modus. Ook services zoals niet-vluchtig random access memory (NVRAM) of de clusterservice worden gestopt. Beide controllers worden opnieuw opgestart wanneer u deze modus in- of uitsluit. Wanneer u deze modus afsluit, worden alle services hervat en moeten ze gezond zijn. (Dit kan enkele minuten duren.)

Als u updates in de onderhoudsmodus moet toepassen, ontvangt u een waarschuwing via de klassieke Azure-portal dat u updates hebt die moeten worden geïnstalleerd. Deze waarschuwing bevat instructies voor het gebruik van Windows PowerShell voor StorSimple om de updates te installeren. Nadat u uw apparaat hebt bijgewerkt, gebruikt u dezelfde procedure om het apparaat te wijzigen in de normale modus. Zie [Stap 4: Onderhoudsmodus afsluiten](#step4)voor stapsgewijze instructies.

> [!IMPORTANT]
> * Controleer voordat u de onderhoudsmodus invoert of beide apparaatcontrollers in orde zijn door de **hardwarestatus** op de pagina Onderhoud in de klassieke Azure-portal **te** controleren. Als de controller niet in orde is, neemt u contact op met Microsoft Support voor de volgende stappen. Ga voor meer informatie naar Microsoft Support. 
> * Wanneer u in de onderhoudsmodus bent, moet u de update eerst toepassen op één controller en vervolgens op de andere controller.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Stap 1: Verbinding maken met de seriële console<a name="step1">
Gebruik eerst een toepassing zoals PuTTY om toegang te krijgen tot de seriële console. In de volgende procedure wordt uitgelegd hoe u PuTTY gebruikt om verbinding te maken met de seriële console.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Stap 2: De onderhoudsmodus invoeren<a name="step2">
Nadat u verbinding hebt gemaakt met de console, bepaalt u of er updates moeten worden geïnstalleerd en voert u de onderhoudsmodus in om deze te installeren.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Stap 3: Uw updates installeren<a name="step3">
Installeer vervolgens uw updates.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Stap 4: Onderhoudsmodus afsluiten<a name="step4">
Tot slot, sluit de onderhoudsmodus af.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Hotfixes installeren via Windows PowerShell voor StorSimple
In tegenstelling tot updates voor Microsoft Azure StorSimple worden hotfixes geïnstalleerd vanuit een gedeelde map. Net als bij updates zijn er twee soorten hotfixes: 

* Regelmatige hotfixes 
* Hotfixes in onderhoudsmodus  

In de volgende procedures wordt uitgelegd hoe u Windows PowerShell voor StorSimple gebruiken om warme oplossingen voor normale en onderhoudsmodus-hotfixes te installeren.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Wat gebeurt er met updates als u een fabrieksreset van het apparaat uitvoert?
Als een apparaat wordt gereset naar fabrieksinstellingen, gaan alle updates verloren. Nadat het apparaat voor het opnieuw instellen van de fabriek is geregistreerd en geconfigureerd, moet u handmatig updates installeren via de klassieke Azure-portal en/of Windows PowerShell voor StorSimple. Zie Het apparaat opnieuw [instellen naar de standaardinstellingen van de fabriek](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)voor meer informatie over fabrieksreset.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het gebruik van Windows PowerShell voor StorSimple om uw StorSimple-apparaat te beheren.](storsimple-windows-powershell-administration.md)
* Meer informatie over [het gebruik van de StorSimple Manager-service om uw StorSimple-apparaat te beheren.](storsimple-manager-service-administration.md)

