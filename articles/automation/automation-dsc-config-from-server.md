---
title: Configuraties maken op bestaande servers - Azure Automation
description: Meer informatie over het maken van configuraties van bestaande servers voor Azure Automation.
keywords: dsc, powershell, configuratie, setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d888c8fd3bf7cd44c37e7225618bd197f419d275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76030125"
---
# <a name="create-configurations-from-existing-servers"></a>Configuraties maken vanaf bestaande servers

> Van toepassing op: Windows PowerShell 5.1

Het maken van configuraties vanaf bestaande servers kan een uitdagende taak zijn.
Je zou niet willen dat *alle* instellingen, alleen degenen die je de zorg over.
Zelfs dan moet u weten in welke volgorde de instellingen moeten worden toegepast om de configuratie met succes toe te passen.

> [!NOTE]
> Dit artikel verwijst naar een oplossing die wordt onderhouden door de Open Source-gemeenschap.
> Ondersteuning is alleen beschikbaar in de vorm van GitHub-samenwerking, niet van Microsoft.

## <a name="community-project-reversedsc"></a>Communityproject: ReverseDSC

Er is een community-oplossing met de naam [ReverseDSC](https://github.com/microsoft/reversedsc) gemaakt om in dit gebied te werken vanaf SharePoint.

De oplossing bouwt voort op de [SharePointDSC-bron](https://github.com/powershell/sharepointdsc) en breidt deze uit naar het orkestreren [van het verzamelen](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) van informatie van bestaande SharePoint-servers.
De nieuwste versie heeft meerdere [extractiemodi](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) om te bepalen welk niveau van informatie moet worden opgenomen.

Het resultaat van het gebruik van de oplossing is het genereren van [configuratiegegevens](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) die kunnen worden gebruikt met SharePointDSC-configuratiescripts.

Zodra de gegevensbestanden zijn gegenereerd, u ze gebruiken met [DSC Configuration-scripts](/powershell/scripting/dsc/overview/overview) om MOF-bestanden te genereren en [de MOF-bestanden te uploaden naar Azure Automation.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Registreer vervolgens uw servers vanaf [on-premises](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) of [in Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) om configuraties op te halen.

Als u ReverseDSC wilt uitproberen, gaat u naar de [PowerShell-galerie](https://www.powershellgallery.com/packages/ReverseDSC/) en downloadt u de oplossing of klikt u op 'Projectsite' om de [documentatie](https://github.com/Microsoft/sharepointDSC.reverse)te bekijken.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de gewenste configuratie van Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [DSC-bronnen](/powershell/scripting/dsc/resources/resources)
- [Lokaal configuratiebeheer configureren](/powershell/scripting/dsc/managing-nodes/metaconfig)
