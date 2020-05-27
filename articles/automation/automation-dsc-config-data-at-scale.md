---
title: Gegevens op schaal configureren voor Azure Automation status configuratie
description: In dit artikel leest u hoe u gegevens op schaal kunt configureren voor Azure Automation status configuratie.
keywords: DSC, Power shell, configuratie, installatie
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fbd721bc54ce2de6b7923b0f582c53ffc7da1a73
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837022"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Gegevens op schaal configureren voor Azure Automation status configuratie

> Van toepassing op: Windows Power shell 5,1

Het beheren van honderden of duizenden servers kan een uitdaging zijn.
Klanten hebben feedback gegeven die het meest lastigste aspect heeft om [configuratie gegevens](/powershell/scripting/dsc/configurations/configdata)te beheren.
Gegevens ordenen over logische constructs zoals locatie, type en omgeving.

> [!NOTE]
> In dit artikel wordt verwezen naar een oplossing die wordt onderhouden door de open source-community.
> Ondersteuning is alleen beschikbaar in de vorm van GitHub-samen werking, niet van micro soft.

## <a name="community-project-datum"></a>Community-Project: datum

Er is een door de Community bijgehouden oplossing gemaakt met de naam [datum](https://github.com/gaelcolas/Datum) om deze uitdaging op te lossen.
De datum bouwt voort op fantastische ideeën van andere configuratie beheer platforms en implementeert hetzelfde type oplossing voor Power shell DSC.
Informatie wordt [in tekst bestanden ingedeeld](https://github.com/gaelcolas/Datum#3-intended-usage) op basis van logische ideeën.
Voor beelden zijn:

- Instellingen die wereld wijd moeten worden toegepast
- Instellingen die moeten worden toegepast op alle servers op een locatie
- Instellingen die moeten worden toegepast op alle database servers
- Afzonderlijke server instellingen

Deze informatie is ingedeeld in de gewenste bestands indeling (JSON, yaml of PSD1).
Vervolgens worden cmdlets verstrekt voor het genereren van configuratie gegevensbestand door de informatie van elk bestand te [consolideren](https://github.com/gaelcolas/Datum#datum-tree) in één weer gave van een server of server functie.

Zodra de gegevens bestanden zijn gegenereerd, kunt u ze gebruiken met [DSC-configuratie scripts](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) om MOF-bestanden te genereren en [de MOF-bestanden te uploaden naar Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registreer uw servers vervolgens [on-premises](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) of [in azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) om configuraties te halen.

Als u de datum wilt proberen, gaat u naar de [PowerShell Gallery](https://www.powershellgallery.com/packages/datum/) en downloadt u de oplossing of klikt u op project site om de [documentatie](https://github.com/gaelcolas/Datum#2-getting-started--concepts)weer te geven.

## <a name="next-steps"></a>Volgende stappen

- Zie [Windows Power shell desired state Configuration Overview](/powershell/scripting/dsc/overview/overview)(Engelstalig) voor meer informatie over Power shell DSC.
- Meer informatie over Power shell DSC-resources in [DSC-resources](/powershell/scripting/dsc/resources/resources).
- Zie [Configuring the local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)(Engelstalig) voor meer informatie over de configuratie van de lokale Configuration Manager.
