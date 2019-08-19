---
title: Configuratie gegevens op schaal-Azure Automation
description: Meer informatie over het configureren van gegevens op schaal voor status configuratie in Azure Automation.
keywords: DSC, Power shell, configuratie, installatie
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f6bb89370c85389d6c41306ed224d27d710cd7c8
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559425"
---
# <a name="configuration-data-at-scale"></a>Configuratie gegevens op schaal

> Van toepassing op: Windows PowerShell 5.1

Het beheren van honderden of duizenden servers kan een uitdaging zijn.
Klanten hebben feedback gegeven die het meest lastigste aspect heeft om [configuratie gegevens](/powershell/dsc/configurations/configdata)te beheren.
Gegevens ordenen over logische constructs zoals locatie, type en omgeving.

> [!NOTE]
> In dit artikel wordt verwezen naar een oplossing die wordt onderhouden door de open source-community.
> Ondersteuning is alleen beschikbaar in de vorm van GitHub-samen werking, niet van micro soft.

## <a name="community-project-datum"></a>Community-Project: Datum

Er is een door de Community bijgehouden oplossing gemaakt met de naam [datum](https://github.com/gaelcolas/Datum) om deze uitdaging op te lossen.
De datum bouwt voort op fantastische ideeën van andere configuratie beheer platforms en implementeert hetzelfde type oplossing voor Power shell DSC.
Informatie wordt [in tekst bestanden ingedeeld](https://github.com/gaelcolas/Datum#3-intended-usage) op basis van logische ideeën.
Voor beelden zijn:

- Instellingen die wereld wijd moeten worden toegepast
- Instellingen die moeten worden toegepast op alle servers op een locatie
- Instellingen die moeten worden toegepast op alle database servers
- Afzonderlijke server instellingen

Deze informatie is ingedeeld in de gewenste bestands indeling (JSON, yaml of PSD1).
Vervolgens worden cmdlets verstrekt voor het genereren van configuratie gegevensbestand door [de informatie](https://github.com/gaelcolas/Datum#datum-tree) van elk bestand te consolideren in één weer gave van een server of server functie.

Zodra de gegevens bestanden zijn gegenereerd, kunt u ze gebruiken met [DSC-configuratie scripts](/powershell/dsc/configurations/write-compile-apply-configuration) om MOF-bestanden te genereren en [de MOF-bestanden te uploaden naar Azure Automation](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Registreer uw servers vervolgens [on-premises](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) of [in azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) om configuraties te halen.

Als u de datum wilt proberen, gaat u naar de [PowerShell Gallery](https://www.powershellgallery.com/packages/datum/) en downloadt u de oplossing of klikt u op project site om de [documentatie](https://github.com/gaelcolas/Datum#2-getting-started--concepts)weer te geven.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van desired state Configuration voor Windows Power shell](/powershell/dsc/overview/overview)
- [DSC-resources](/powershell/dsc/resources/resources)
- [De lokale Configuration Manager configureren](/powershell/dsc/managing-nodes/metaconfig)
