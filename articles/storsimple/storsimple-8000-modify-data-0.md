---
title: Gegevens 0-instellingen wijzigen op apparaat uit de StorSimple 8000-serie | Microsoft Documenten
description: Meer informatie over het gebruik van Windows PowerShell voor StorSimple om de DATA 0-netwerkinterface op uw StorSimple-apparaat opnieuw te configureren.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: 3cf136c5ddec8f4998d15c597914e1f806453945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60631580"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>De instellingen van de DATA 0-netwerkinterface op uw StorSimple 8000-serie wijzigen

## <a name="overview"></a>Overzicht

Uw Microsoft Azure StorSimple-apparaat heeft zes netwerkinterfaces, van DATA 0 tot DATA 5. De DATA 0-interface is altijd geconfigureerd via de Windows PowerShell-interface of de seriële console en is automatisch cloudingeschakeld. Houd er rekening mee dat u de netwerkinterface van DATA 0 niet configureren via de Azure-portal.

De DATA 0-interface wordt eerst geconfigureerd via de installatiewizard tijdens de eerste implementatie van het StorSimple-apparaat. Wanneer het apparaat zich in een operationele modus bevindt, moet u mogelijk de INSTELLINGEN voor GEGEVENS 0 opnieuw configureren. Deze zelfstudie biedt twee methoden om de netwerkinstellingen van DATA 0 te wijzigen, beide via Windows PowerShell voor StorSimple.

Na het lezen van deze tutorial, zult u in staat zijn om:

* Gegevens0-netwerkinstelling wijzigen via de wizard Setup
* Gegevens 0-netwerkinstellingen `Set-HcsNetInterface` wijzigen via de cmdlet

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Gegevens 0-netwerkinstellingen wijzigen via de wizard Setup
U de netwerkinstellingen van DATA 0 opnieuw configureren door verbinding te maken met de Windows PowerShell-interface van uw StorSimple-apparaat en een installatiewizardsessie te starten. Voer de volgende stappen uit om de instellingen van GEGEVENS 0 te wijzigen:

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Gegevens 0-netwerkinstellingen wijzigen via de wizard Setup
1. Kies in het menu van de seriële console optie 1, **Log in met volledige toegang**. Geef het wachtwoord van de **apparaatbeheerder**op wanneer daarom wordt gevraagd. Het standaardwachtwoord `Password1`is .
2. Typ het volgende na de opdrachtprompt:
   
    `Invoke-HcsSetupWizard`
3. Er wordt een wizard Setup weergegeven om de DATA 0-interface van uw apparaat te configureren. Geef nieuwe waarden op voor het IP-adres, de gateway en het netmasker.

> [!NOTE]
> De IP-instellingen voor vaste controllers moeten opnieuw worden geconfigureerd via het **netwerkinstellingenblad** van het StorSimple-apparaat in de Azure-portal. Ga voor meer informatie naar [Netwerkinterfaces wijzigen.](storsimple-8000-modify-device-config.md#modify-network-interfaces)

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Gegevens 0-netwerkinstellingen wijzigen via de cmdlet Set-HcsNetInterface
Een alternatieve manier om de data-netwerkinterface opnieuw `Set-HcsNetInterface` te configureren is door het gebruik van de cmdlet. De cmdlet wordt uitgevoerd vanuit de Windows PowerShell-interface van uw StorSimple-apparaat. Bij het gebruik van deze procedure kunnen de vaste IP's van de controller hier ook worden geconfigureerd. Voer de volgende stappen uit om de INSTELLINGEN VOOR GEGEVENS 0 te wijzigen: 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Data 0-netwerkinstellingen wijzigen via de cmdlet Set-HcsNetInterface
1. Kies in het menu van de seriële console optie 1, **Log in met volledige toegang**. Geef het wachtwoord van de apparaatbeheerder op wanneer daarom wordt gevraagd. Het standaardwachtwoord `Password1`is .
2. Typ het volgende na de opdrachtprompt:
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    Typ in de schuine haakjes de volgende waarden voor GEGEVENS 0:
   
   * IPv4-adres
   * IPv4-gateway
   * IPv4-subnetmasker
   * Vast IPv4-adres voor controller 0
   * Vast IPv4-adres voor Controller 1
     
     Ga voor meer informatie over het gebruik van deze cmdlet naar [Windows PowerShell voor StorSimple-cmdlet-referentie.](https://technet.microsoft.com/library/dn688161.aspx)

## <a name="next-steps"></a>Volgende stappen
* Als u andere netwerkinterfaces dan DATA 0 wilt configureren, u de [netwerkinstellingen configureren in de Azure-portal](storsimple-8000-modify-device-config.md)gebruiken. 
* Als u problemen ondervindt bij het configureren van uw netwerkinterfaces, raadpleegt u [Problemen met implementatie oplossen.](storsimple-troubleshoot-deployment.md)

