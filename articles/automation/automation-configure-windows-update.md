---
title: Instellingen voor Windows Update configureren om te werken met Azure Update Management
description: In dit artikel worden de Instellingen voor Windows Update beschreven die u configureert om met Azure Update Management te werken.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279024"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Windows Update-instellingen configureren voor Updatebeheer

Azure Update Management is afhankelijk van [windows update-client](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) om Windows-updates te downloaden en te installeren. Er zijn specifieke instellingen die door de Windows Update-client worden gebruikt wanneer u verbinding maakt met Windows Server Update Services (WSUS) of Windows Update. Veel van deze instellingen kunnen worden beheerd met:

- Editor voor lokaal groepsbeleid
- Groepsbeleid
- PowerShell
- Het register rechtstreeks bewerken

Updatemanagement respecteert veel van de instellingen die zijn opgegeven om de Windows Update-client te bedienen. Als u instellingen gebruikt om niet-Windows-updates in te schakelen, beheert Update Management deze updates ook. Als u het downloaden van updates wilt inschakelen voordat een update-implementatie plaatsvindt, kan de implementatie van updates sneller, efficiënter en minder waarschijnlijk het onderhoudsvenster overschrijden.

## <a name="pre-download-updates"></a>Updates vooraf downloaden

Als u automatisch downloaden van updates wilt configureren, maar deze niet automatisch wilt installeren, u groepsbeleid gebruiken om de [instelling Automatische updates configureren](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) in te stellen op **3**. Met deze instelling u de vereiste updates op de achtergrond downloaden en wordt u ervan op de hoogte gesteld dat de updates klaar zijn om te installeren. Op deze manier blijft Update Management de controle houden over de planningen, maar kunnen updates worden gedownload buiten het onderhoudsvenster Updatebeheer. Met dit gedrag voorkomt u dat **het onderhoudsvenster** fouten in Updatebeheer overschrijdt.

U deze instelling inschakelen met PowerShell door de volgende opdracht uit te voeren:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Opnieuw opstarten-instellingen configureren

De registersleutels die worden vermeld in [Automatische updates configureren door het register](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) te bewerken en [registersleutels die worden gebruikt om opnieuw opstarten te beheren,](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kunnen ervoor zorgen dat uw machines opnieuw worden opgestart, zelfs als u **Nooit opnieuw opstarten** opgeeft in de instellingen voor implementatie **bijwerken.** Configureer deze registersleutels die het beste bij uw omgeving passen.

## <a name="enable-updates-for-other-microsoft-products"></a>Updates inschakelen voor andere Microsoft-producten

Standaard is de Windows Update-client geconfigureerd om alleen updates voor Windows te bieden. Als u de **instelling Geef me updates inschakelt voor andere Microsoft-producten wanneer ik de Windows-instelling werk,** ontvangt u ook updates voor andere producten, waaronder beveiligingspatches voor Microsoft SQL Server en andere Microsoft-software. Deze optie kan worden geconfigureerd als u de nieuwste [bestanden voor beheerderssjablonen](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) hebt gedownload en gekopieerd die beschikbaar zijn voor Windows 2016 en hoger.

Als u Windows Server 2012 R2 uitvoert, kan deze instelling niet worden geconfigureerd op groepsbeleid. Voer de volgende PowerShell-opdracht uit op deze machines. Updatebeheer voldoet aan deze instelling.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS-configuratie-instellingen

Updatebeheer ondersteunt WSUS-instellingen. De WSUS-instellingen die u configureren voor het werken met Updatebeheer, worden hieronder weergegeven.

### <a name="intranet-microsoft-update-service-location"></a>Locatie van Microsoft-updateservice op intranet

U bronnen opgeven voor het scannen en downloaden van updates onder [Intranet Microsoft Update-servicelocatie opgeven.](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location) Standaard is de Windows Update-client geconfigureerd om updates van Windows Update te downloaden. Wanneer u een WSUS-server opgeeft als bron voor uw machines, mislukt de implementatie van de update als de updates niet zijn goedgekeurd in WSUS. 

Als u machines wilt beperken tot alleen die interne updateservice, configureert [u Geen verbinding maken met internetlocaties van Windows Update.](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations) 

## <a name="next-steps"></a>Volgende stappen

Nadat u windows-update-instellingen hebt geconfigureerd, u een update-implementatie plannen door de instructies te volgen in [Updates en patches beheren voor uw Azure VM's.](automation-tutorial-update-management.md)
