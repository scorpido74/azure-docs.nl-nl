---
title: Gegevens configureren op basis van STIG voor de configuratie van de Azure Automation status
description: In dit artikel leest u hoe u gegevens kunt configureren op basis van STIG voor de configuratie van Azure Automations status.
keywords: DSC, Power shell, configuratie, installatie
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d1b05f9e77d3530f3e883aa3f9d98de09c8f54c2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836988"
---
# <a name="configure-data-based-on-stig"></a>Gegevens configureren op basis van STIG

> Van toepassing op: Windows Power shell 5,1

Het maken van configuratie-inhoud voor de eerste keer kan lastig zijn.
In veel gevallen is het doel om de configuratie van servers te automatiseren volgens een ' basis lijn ' die in het ideale geval wordt afgestemd op een branche aanbeveling.

> [!NOTE]
> In dit artikel wordt verwezen naar een oplossing die wordt onderhouden door de open source-community.
> Ondersteuning is alleen beschikbaar in de vorm van GitHub-samen werking, niet van micro soft.

## <a name="community-project-powerstig"></a>Community-Project: PowerSTIG

Een community-project met de naam [PowerSTIG](https://github.com/microsoft/powerstig) is bedoeld om dit probleem op te lossen door DSC-inhoud te genereren op basis van de [open bare informatie](https://public.cyber.mil/stigs/) over Stig (technische implementatie handleiding voor beveiliging),

Het verwerken van basis lijnen is ingewik kelder dan het klinkt.
Veel organisaties moeten [uitzonde ringen documenteren](https://github.com/microsoft/powerstig#powerstigdata) voor regels en deze gegevens op schaal beheren.
PowerSTIG lost het probleem op door [samengestelde resources](https://github.com/microsoft/powerstig#powerstigdsc) op te geven voor elk gebied van de configuratie, in plaats van te proberen het hele bereik met instellingen in één groot bestand op te lossen.

Zodra de configuraties zijn gegenereerd, kunt u de DSC- [configuratie scripts](/powershell/scripting/dsc/configurations/configurations) gebruiken om MOF-bestanden te genereren en [de MOF-bestanden uploaden naar Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registreer uw servers vervolgens [on-premises](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) of [in azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) om configuraties te halen.

Als u PowerSTIG wilt uitproberen, gaat u naar de [PowerShell Gallery](https://www.powershellgallery.com) en downloadt u de oplossing of klikt u op project site om de [documentatie](https://github.com/microsoft/powerstig)weer te geven.

## <a name="next-steps"></a>Volgende stappen

- Zie [Windows Power shell desired state Configuration Overview](/powershell/scripting/dsc/overview/overview)(Engelstalig) voor meer informatie over Power shell DSC.
- Meer informatie over Power shell DSC-resources in [DSC-resources](/powershell/scripting/dsc/resources/resources).
- Zie [Configuring the local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)(Engelstalig) voor meer informatie over de configuratie van de lokale Configuration Manager.
