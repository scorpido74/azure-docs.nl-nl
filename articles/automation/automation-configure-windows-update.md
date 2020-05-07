---
title: Windows Update instellingen configureren voor gebruik met Azure Updatebeheer
description: In dit artikel worden de Windows Update-instellingen beschreven die u configureert om te werken met Azure Updatebeheer.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: b9b5f2b19b29eae0132ec01a9f3fb7e8355361f5
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82779447"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Windows Update instellingen voor Updatebeheer configureren

Azure Updatebeheer is afhankelijk van [Windows Update-client](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) om Windows-updates te downloaden en te installeren. Er zijn specifieke instellingen die worden gebruikt door de Windows Update-client wanneer er verbinding wordt gemaakt met Windows Server Update Services (WSUS) of Windows Update. Veel van deze instellingen kunnen worden beheerd met:

- Editor voor lokaal groepsbeleid
- Groepsbeleid
- PowerShell
- Het REGI ster rechtstreeks bewerken

Updatebeheer veel van de instellingen die zijn opgegeven om de Windows Update-client te beheren. Als u instellingen gebruikt om niet-Windows-updates in te scha kelen, worden deze updates ook door Updatebeheer beheerd. Als u het downloaden van updates wilt inschakelen voordat een update-implementatie plaatsvindt, kan de update-implementatie sneller, efficiënter en minder waarschijnlijk het onderhouds venster overschrijden.

Raadpleeg [uw implementatie plannen voor het bijwerken van virtuele Windows-machines in azure met WSUS](https://docs.microsoft.com/azure/architecture/example-scenario/wsus/)voor aanvullende aanbevelingen voor het instellen van WSUS in uw Azure-abonnement en uw virtuele Windows-machines veilig te houden.

## <a name="pre-download-updates"></a>Updates vooraf downloaden

Als u automatisch downloaden van updates wilt configureren, maar deze niet automatisch wilt installeren, kunt u groepsbeleid gebruiken om de [instelling Automatische updates configureren](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) in te stellen op **3**. Met deze instelling kunnen de vereiste updates op de achtergrond worden gedownload en wordt u gewaarschuwd dat de updates gereed zijn om te worden geïnstalleerd. Op deze manier blijft Updatebeheer controle over de planningen, maar kunnen updates buiten het onderhouds venster van Updatebeheer worden gedownload. Dit gedrag voor komt dat het **onderhouds venster** fouten in updatebeheer overschrijdt.

U kunt deze instelling inschakelen met behulp van Power shell door de volgende opdracht uit te voeren:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Instellingen voor opnieuw opstarten configureren

De register sleutels die worden vermeld in [Automatische updates configureren door het REGI ster te bewerken en de](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) [register sleutels die worden gebruikt voor het beheren van opnieuw opstarten](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) , kunnen ertoe leiden dat uw computers opnieuw worden opgestart, zelfs als u in de instellingen voor **Update-implementatie** **nooit opnieuw opstarten** opgeeft. Configureer deze register sleutels op het beste aansluiten bij uw omgeving.

## <a name="enable-updates-for-other-microsoft-products"></a>Updates voor andere micro soft-producten inschakelen

Windows Update-client is standaard geconfigureerd voor het leveren van updates voor Windows. Als u de optie **updates voor andere micro soft-producten opgeven wanneer ik Windows Update** inschakelt, ontvangt u ook updates voor andere producten, waaronder beveiligings patches voor Microsoft SQL Server en andere micro soft-software. Deze optie kan worden geconfigureerd als u de meest recente [beheer sjabloon bestanden](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) hebt gedownload en gekopieerd die beschikbaar zijn voor Windows 2016 en hoger.

Als u Windows Server 2012 R2 uitvoert, kan deze instelling niet worden geconfigureerd door groepsbeleid. Voer de volgende Power shell-opdracht uit op deze computers. Updatebeheer voldoet aan deze instelling.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS-configuratie-instellingen

Updatebeheer ondersteunt WSUS-instellingen. De WSUS-instellingen die u kunt configureren voor het werken met Updatebeheer worden hieronder weer gegeven.

### <a name="intranet-microsoft-update-service-location"></a>Locatie van micro soft-Update service in intranet

U kunt bronnen opgeven voor het scannen en downloaden van updates onder [locatie van intranet-Microsoft Update service opgeven](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Windows Update-client is standaard geconfigureerd voor het downloaden van updates van Windows Update. Wanneer u een WSUS-server als bron voor uw machines opgeeft en de updates niet zijn goedgekeurd in WSUS, mislukt de update-implementatie. 

Configureer [geen verbinding maken met Windows Update Internet locaties](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)om computers te beperken tot alleen die interne Update service. 

## <a name="next-steps"></a>Volgende stappen

Nadat u Windows Update instellingen hebt geconfigureerd, kunt u een update-implementatie plannen door de instructies in [updates en patches beheren te volgen voor uw Azure-vm's](automation-tutorial-update-management.md).
