---
title: Windows Update instellingen configureren voor gebruik met Azure Updatebeheer
description: In dit artikel worden de Windows Update-instellingen beschreven die u configureert om te werken met Azure Updatebeheer.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 804f42121293e142cf77ad73c4aab36e62e3242d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850411"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Windows Update instellingen voor Updatebeheer configureren

Azure Updatebeheer is afhankelijk van Windows Update om Windows-updates te downloaden en te installeren. Als gevolg hiervan Updatebeheer veel van de instellingen die worden gebruikt door Windows Update. Als u instellingen gebruikt om niet-Windows-updates in te scha kelen, worden deze updates ook door Updatebeheer beheerd. Als u het downloaden van updates wilt inschakelen voordat een update-implementatie plaatsvindt, kan de update-implementatie sneller, efficiënter en minder waarschijnlijk het onderhouds venster overschrijden.

## <a name="pre-download-updates"></a>Updates vooraf downloaden

Als u automatisch downloaden van updates in groepsbeleid wilt configureren, stelt u de [instelling Automatische updates configureren](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) op **3**in. Met deze instelling kunnen de vereiste updates op de achtergrond worden gedownload, maar worden ze niet geïnstalleerd. Op deze manier blijft Updatebeheer controle over de planningen, maar kunnen updates buiten het onderhouds venster van Updatebeheer worden gedownload. Dit gedrag voor komt dat het onderhouds venster de fouten in Updatebeheer overschrijdt.

U kunt deze instelling ook inschakelen door de volgende Power shell-opdracht uit te voeren op een systeem dat u wilt configureren voor het automatisch downloaden van updates:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Automatische installatie uitschakelen

In azure virtual machines (Vm's) is automatische installatie van updates standaard ingeschakeld. Dit kan ertoe leiden dat updates worden geïnstalleerd voordat u deze plant voor installatie door Updatebeheer. U kunt dit gedrag uitschakelen door de `NoAutoUpdate` register sleutel in te stellen op `1`. In het volgende Power shell-fragment ziet u hoe u dit doet:

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Instellingen voor opnieuw opstarten configureren

De register sleutels die worden vermeld in [Automatische updates configureren door het REGI ster te bewerken en de](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) [register sleutels die worden gebruikt voor het beheren van opnieuw opstarten](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , kunnen ertoe leiden dat uw computers opnieuw worden opgestart, zelfs als u in de instellingen voor **Update-implementatie** **nooit opnieuw opstarten** opgeeft. U moet deze register sleutels configureren om het beste bij uw omgeving te passen.

## <a name="enable-updates-for-other-microsoft-products"></a>Updates voor andere micro soft-producten inschakelen

Windows Update biedt standaard alleen updates voor Windows. Als u de optie **updates voor andere micro soft-producten opgeven wanneer ik Windows Update** inschakelt, ontvangt u ook updates voor andere producten, waaronder beveiligings patches voor Microsoft SQL Server en andere micro soft-software. Deze optie kan niet worden geconfigureerd door groepsbeleid. Voer de volgende Power shell-opdracht uit op de systemen waarop u andere micro soft-updates wilt inschakelen. Updatebeheer moet voldoen aan deze instelling.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS-configuratie-instellingen

Updatebeheer voldoet aan de instellingen voor Windows Server Update Services (WSUS). De WSUS-instellingen die u kunt configureren voor het werken met Updatebeheer worden hieronder weer gegeven.

### <a name="intranet-microsoft-update-service-location"></a>Locatie van micro soft-Update service in intranet

U kunt bronnen opgeven voor het scannen en downloaden van updates onder [locatie van intranet-Microsoft Update service opgeven](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Volgende stappen

Nadat u Windows Update instellingen hebt geconfigureerd, kunt u een update-implementatie plannen door de instructies in [updates en patches beheren te volgen voor uw Azure-vm's](automation-tutorial-update-management.md).
