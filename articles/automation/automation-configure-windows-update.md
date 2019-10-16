---
title: Windows Update instellingen configureren voor gebruik met Azure Updatebeheer
description: In dit artikel worden de Windows Update-instellingen beschreven die u configureert om te werken met Updatebeheer
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377562"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Windows Update instellingen voor Updatebeheer configureren

Updatebeheer is afhankelijk van Windows Update om Windows-updates te downloaden en te installeren. Als gevolg hiervan respecteren we veel van de instellingen die worden gebruikt door Windows Update. Als u instellingen gebruikt om niet-Windows-updates in te scha kelen, worden deze updates ook door Updatebeheer beheerd. Als u het downloaden van updates wilt inschakelen voordat een update-implementatie wordt uitgevoerd, kunnen update-implementaties sneller zijn en minder kans lopen om het onderhouds venster te overschrijden.

## <a name="pre-download-updates"></a>Updates vooraf downloaden

Voor het configureren van updates die automatisch worden gedownload in groepsbeleid, kunt u de instelling voor het [configureren van automatische updates](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) instellen op **3**. Hiermee downloadt u de updates die nodig zijn op de achtergrond, maar worden ze niet geïnstalleerd. Dit houdt Updatebeheer in de controle over schema's, maar laat updates downloaden buiten het onderhouds venster Updatebeheer. Dit kan ervoor zorgen dat het **onderhouds venster** fouten in updatebeheer overschrijdt.

U kunt dit ook instellen met Power shell, de volgende Power shell uitvoeren op een systeem waarop u automatisch updates wilt downloaden.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Automatische installatie uitschakelen

Voor virtuele Azure-machines is automatische installatie van updates standaard ingeschakeld. Dit kan ertoe leiden dat updates worden geïnstalleerd voordat u deze plant om te worden geïnstalleerd door Updatebeheer. U kunt dit gedrag uitschakelen door de register sleutel `NoAutoUpdate` in te stellen op `1`. In het volgende Power shell-fragment ziet u een manier om dit te doen.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Instellingen voor opnieuw opstarten configureren

De register sleutels onder [configuratie automatische updates door het bewerken van het REGI ster en de](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) [register sleutels die worden gebruikt voor het beheren van opnieuw opstarten](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , kunnen ertoe leiden dat uw computers opnieuw worden opgestart, zelfs als u **nooit opnieuw opstarten** hebt opgegeven in de update-implementatie-instellingen . U moet deze register sleutels naar wens configureren voor uw omgeving.

## <a name="enable-updates-for-other-microsoft-products"></a>Updates voor andere micro soft-producten inschakelen

Windows Update biedt standaard alleen updates voor Windows. Als u **updates voor andere micro soft-producten geven tijdens het bijwerken van Windows**inschakelt, worden er updates voor andere producten weer gegeven, waaronder beveiligings patches voor SQL Server of andere software van de eerste partij. Deze optie kan niet worden geconfigureerd door groepsbeleid. Voer de volgende Power shell uit op de systemen waarvoor u andere patches voor de eerste partij wilt inschakelen, en Updatebeheer deze instelling wordt nageleefd.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS-configuratie-instellingen

**Updatebeheer** respecteert de WSUS-configuratie-instellingen. De lijst met WSUS-instellingen die u kunt configureren voor het werken met Updatebeheer wordt hieronder weer gegeven.

### <a name="intranet-microsoft-update-service-location"></a>Locatie van micro soft-Update service in intranet

U kunt bronnen opgeven voor het scannen en downloaden van updates op de locatie van de [intranet-Microsoft Update service](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Volgende stappen

Nadat u Windows Update instellingen hebt geconfigureerd, kunt u een update-implementatie plannen door de instructies onder [updates en patches beheren voor uw virtuele Azure-machines](automation-tutorial-update-management.md) te volgen.