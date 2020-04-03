---
title: Configuratiegegevens op schaal - Azure Automation
description: Meer informatie over het configureren van gegevens op schaal voor statusconfiguratie in Azure Automation.
keywords: dsc, powershell, configuratie, setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585563"
---
# <a name="configuration-data-at-scale"></a>Configuratiegegevens op schaal

> Van toepassing op: Windows PowerShell 5.1

Het beheren van honderden of duizenden servers kan een uitdaging zijn.
Klanten hebben feedback gegeven dat het moeilijkste aspect eigenlijk het beheren van [configuratiegegevens](/powershell/scripting/dsc/configurations/configdata)is.
Informatie ordenen over logische constructies zoals locatie, type en omgeving.

> [!NOTE]
> Dit artikel verwijst naar een oplossing die wordt onderhouden door de Open Source-gemeenschap.
> Ondersteuning is alleen beschikbaar in de vorm van GitHub-samenwerking, niet van Microsoft.

## <a name="community-project-datum"></a>Communityproject: Datum

Er is een door de community onderhouden oplossing met de naam [Datum](https://github.com/gaelcolas/Datum) gemaakt om deze uitdaging op te lossen.
Datum bouwt voort op geweldige ideeën van andere configuratiebeheerplatforms en implementeert hetzelfde type oplossing voor PowerShell DSC.
Informatie wordt [georganiseerd in tekstbestanden](https://github.com/gaelcolas/Datum#3-intended-usage) op basis van logische ideeën.
Voorbeelden hiervan zijn:

- Instellingen die wereldwijd moeten worden toegepast
- Instellingen die moeten gelden voor alle servers op een locatie
- Instellingen die van toepassing moeten zijn op alle databaseservers
- Individuele serverinstellingen

Deze informatie is georganiseerd in de bestandsindeling die u verkiest (JSON, Yaml of PSD1).
Vervolgens worden cmdlets verstrekt om configuratiegegevensbestanden te genereren door [de informatie](https://github.com/gaelcolas/Datum#datum-tree) van elk bestand te consolideren in één weergave van een server of serverrol.

Zodra de gegevensbestanden zijn gegenereerd, u ze gebruiken met [DSC Configuration-scripts](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) om MOF-bestanden te genereren en [de MOF-bestanden te uploaden naar Azure Automation.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Registreer vervolgens uw servers vanaf [on-premises](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) of [in Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) om configuraties op te halen.

Als u Datum wilt uitproberen, gaat u naar de [PowerShell-galerie](https://www.powershellgallery.com/packages/datum/) en downloadt u de oplossing of klikt u op 'Projectsite' om de [documentatie](https://github.com/gaelcolas/Datum#2-getting-started--concepts)te bekijken.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de gewenste configuratie van Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [DSC-bronnen](/powershell/scripting/dsc/resources/resources)
- [Lokaal configuratiebeheer configureren](/powershell/scripting/dsc/managing-nodes/metaconfig)
