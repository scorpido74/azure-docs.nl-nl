---
title: Configuratie op basis van STIG om te gebruiken in statusconfiguratie - Azure Automation
description: Meer informatie over configuraties op basis van STIG voor statusconfiguratie in Azure Automation.
keywords: dsc, powershell, configuratie, setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028272"
---
# <a name="configuration-based-on-stig"></a>Configuration op basis van STIG

> Van toepassing op: Windows PowerShell 5.1

Het voor het eerst maken van configuratie-inhoud kan een uitdaging zijn.
In veel gevallen is het doel om de configuratie van servers te automatiseren na een "baseline" die hopelijk wordt afgestemd op een aanbeveling in de branche.

> [!NOTE]
> Dit artikel verwijst naar een oplossing die wordt onderhouden door de Open Source-gemeenschap.
> Ondersteuning is alleen beschikbaar in de vorm van GitHub-samenwerking, niet van Microsoft.

## <a name="community-project-powerstig"></a>Gemeenschapsproject: PowerSTIG

Een community project genaamd [PowerSTIG](https://github.com/microsoft/powerstig) heeft als doel om dit probleem op te lossen door het genereren van DSC-inhoud op basis van [openbare informatie](https://public.cyber.mil/stigs/) over STIG (Security Technical Implementation Guide),

Omgaan met basislijnen is ingewikkelder dan het klinkt.
Veel organisaties moeten [uitzonderingen op](https://github.com/microsoft/powerstig#powerstigdata) regels documenteren en die gegevens op schaal beheren.
PowerSTIG lost het probleem op door [samengestelde bronnen](https://github.com/microsoft/powerstig#powerstigdsc) te verstrekken om elk gebied van de configuratie aan te pakken in plaats van te proberen het hele scala aan instellingen in één groot bestand aan te pakken.

Zodra de configuraties zijn gegenereerd, u de [DSC-configuratiescripts](/powershell/scripting/dsc/configurations/configurations) gebruiken om MOF-bestanden te genereren en [de MOF-bestanden te uploaden naar Azure Automation.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Registreer vervolgens uw servers vanaf [on-premises](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) of [in Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) om configuraties op te halen.

Als u PowerSTIG wilt uitproberen, gaat u naar de [PowerShell-galerie](https://www.powershellgallery.com) en downloadt u de oplossing of klikt u op 'Projectsite' om de [documentatie](https://github.com/microsoft/powerstig)te bekijken.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de gewenste configuratie van Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [DSC-bronnen](/powershell/scripting/dsc/resources/resources)
- [Lokaal configuratiebeheer configureren](/powershell/scripting/dsc/managing-nodes/metaconfig)
