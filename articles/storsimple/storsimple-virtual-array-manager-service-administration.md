---
title: Microsoft Azure StorSimple Manager Virtual Array-beheer | Microsoft Documenten
description: Lees hoe u uw StorSimple on-premises Virtual Array beheert met de StorSimple Device Manager-service in de Azure-portal.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: bb6bb491ca71e5ced5aecc8137e9e1cbd950e80b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62123802"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Gebruik de StorSimple Device Manager-service om uw StorSimple Virtual Array te beheren
![installatieprocesstroom](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Overzicht
In dit artikel wordt de Serviceinterface van StorSimple Device Manager beschreven, inclusief hoe u verbinding maken met de serviceinterface en de verschillende beschikbare opties, en worden koppelingen naar de specifieke workflows die via deze gebruikersinterface kunnen worden uitgevoerd.

Na het lezen van dit artikel, zult u weten hoe:

* Verbinding maken met de StorSimple Device Manager-service
* Navigeren in de gebruikersinterface van StorSimple Device Manager
* Beheer uw StorSimple Virtual Array via de StorSimple Device Manager-service

> [!NOTE]
> Als u de beheeropties wilt bekijken die beschikbaar zijn voor het apparaat uit de StorSimple 8000-serie, gaat u [naar De StorSimple Manager-service gebruiken om uw StorSimple-apparaat te beheren.](storsimple-manager-service-administration.md)
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Verbinding maken met de StorSimple Device Manager-service
De StorSimple Device Manager-service wordt uitgevoerd in Microsoft Azure en maakt verbinding met meerdere StorSimple Virtual Arrays. U gebruikt een centrale Microsoft Azure-portal die in een browser wordt uitgevoerd om deze apparaten te beheren. Ga als volgt te werk om verbinding te maken met de StorSimple Device Manager-service.

#### <a name="to-connect-to-the-service"></a>Verbinding maken met de service
1. Ga [https://ms.portal.azure.com](https://ms.portal.azure.com)naar.
2. Meld u met uw Microsoft-accountreferenties aan bij de Microsoft Azure-portal (rechtsboven in het deelvenster).
3. Navigeer naar Bladeren --> 'Filter' op StorSimple-apparaatmanagers om al uw apparaatbeheerders in een bepaald abonnement te bekijken.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>De StorSimple Device Manager-service gebruiken om beheertaken uit te voeren
In de volgende tabel vindt u een overzicht van alle algemene beheertaken en complexe werkstromen die kunnen worden uitgevoerd binnen het overzichtsblad van de StorSimple Device Manager-service. Deze taken worden georganiseerd op basis van de bladen waarop ze worden geïnitieerd.

Klik op de juiste procedure in de tabel voor meer informatie over elke werkstroom.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple-werkstromen voor apparaatbeheer
| Als u dit wilt doen ... | Gebruik deze procedure |
| --- | --- |
| Een service maken</br>Een service verwijderen</br>De serviceregistratiesleutel ophalen</br>De serviceregistratiesleutel opnieuw genereren |[De StorSimple Device Manager-service implementeren](storsimple-virtual-array-manage-service.md) |
| De activiteitslogboeken weergeven |[Het serviceoverzicht StorSimple gebruiken](storsimple-virtual-array-service-summary.md) |
| Een virtuele array deactiveren</br>Een virtuele array verwijderen |[Een virtuele array deactiveren of verwijderen](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Herstel na noodgevallen en failover van het apparaat</br>Vereisten voor failover</br>Business continuity disaster recovery (BCDR)</br>Fouten tijdens herstel na noodgevallen |[Disaster recovery en device failover voor uw StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| Back-up van aandelen en volumes</br>Neem een handmatige back-up</br>Het back-upschema wijzigen</br>Bestaande back-ups weergeven |[Een back-up van uw StorSimple Virtual Array](storsimple-virtual-array-backup.md) |
| Shares van een back-upset klonen</br>Volumes van een back-upset klonen</br>Itemniveauherstel (alleen bestandsserver) |[Kloon uit een back-up van uw StorSimple Virtual Array](storsimple-virtual-array-clone.md) |
| Over opslagaccounts</br>Een opslagaccount toevoegen</br>Een opslagaccount bewerken</br>Een opslagaccount verwijderen |[Opslagaccounts beheren voor de Virtuele Array van StorSimple](storsimple-virtual-array-manage-storage-accounts.md) |
| Informatie over toegangscontrolerecords</br>Een toegangscontrolerecord toevoegen of wijzigen </br>Een toegangscontrolerecord verwijderen |[Toegangsbeheerrecords beheren voor de Virtuele Array van StorSimple](storsimple-virtual-array-manage-acrs.md) |
| Taakdetails weergeven |[StorSimple Virtual Array-taken beheren](storsimple-virtual-array-manage-jobs.md) |
| Waarschuwingsinstellingen configureren</br>Waarschuwingen ontvangen</br>Waarschuwingen beheren</br>Waarschuwingen bekijken |[Waarschuwingen voor de Virtuele Array van StorSimple weergeven en beheren](storsimple-virtual-array-manage-alerts.md) |
| Het wachtwoord van de apparaatbeheerder wijzigen |[Het wachtwoord van de storSimple Virtual Array-apparaatbeheerder wijzigen](storsimple-virtual-array-change-device-admin-password.md) |
| Software-updates installeren |[Uw virtuele array bijwerken](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> U moet de [lokale web-gebruikersinterface](storsimple-ova-web-ui-admin.md) gebruiken voor de volgende taken:
> 
> * [De versleutelingssleutel voor servicegegevens ophalen](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Een ondersteuningspakket maken](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Een virtuele array stoppen en opnieuw starten](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Volgende stappen
Ga voor informatie over de webgebruikersinterface en hoe u deze gebruiken naar [De Web-gebruikersinterface van StorSimple gebruiken om uw StorSimple Virtual Array te beheren.](storsimple-ova-web-ui-admin.md)

