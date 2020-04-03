---
title: Configuraties converteren naar samengestelde resources voor statusconfiguratie - Azure Automation
description: Meer informatie over het converteren van configuraties naar samengestelde resources voor statusconfiguratie in Azure Automation.
keywords: dsc, powershell, configuratie, setup
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a39b038d31d1b4a614ff0acf7df2586706bb0404
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585518"
---
# <a name="convert-configurations-to-composite-resources"></a>Configuraties converteren naar samengestelde resources

> Van toepassing op: Windows PowerShell 5.1

Zodra u aan de slag bent met het ontwerpen van configuraties, u snel 'scenario's' maken die groepen instellingen beheren.
Voorbeelden hiervan zijn:

- een webserver maken
- een DNS-server maken
- Een SharePoint-server maken
- een SQL-cluster configureren
- firewall-instellingen beheren
- wachtwoordinstellingen beheren

Als u geïnteresseerd bent in het delen van dit werk met anderen, de beste optie is om de configuratie te verpakken als een [Samengestelde Resource](/powershell/scripting/dsc/resources/authoringresourcecomposite).
Het maken van samengestelde resources voor de eerste keer kan overweldigend zijn.

> [!NOTE]
> Dit artikel verwijst naar een oplossing die wordt onderhouden door de Open Source-gemeenschap.
> Ondersteuning is alleen beschikbaar in de vorm van GitHub-samenwerking, niet van Microsoft.

## <a name="community-project-compositeresource"></a>Communityproject: CompositeResource

Er is een community-oplossing met de naam [CompositeResource](https://github.com/microsoft/compositeresource) gemaakt om deze uitdaging op te lossen.

CompositeResource automatiseert het proces van het maken van een nieuwe module vanuit uw configuratie.
U begint met [het vinden van](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) het configuratiescript op uw werkstation (of buildserver), zodat het in het geheugen wordt geladen.
Gebruik vervolgens de functie van de CompositeResource-module om een conversie te automatiseren in plaats van de configuratie uit te voeren om een MOF-bestand te genereren.
De cmdlet laadt de inhoud van uw configuratie, krijgt de lijst met parameters en genereert een nieuwe module met alles wat u nodig hebt.

Zodra u een module hebt gegenereerd, u de versie verhogen en releasenotes toevoegen telkens wanneer u wijzigingen aanbrengt en deze publiceert naar uw eigen [PowerShellGet-opslagplaats.](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)

Zodra u een samengestelde resourcemodule hebt gemaakt met uw configuratie (of meerdere configuraties), u deze gebruiken in de [composable authoring experience](/azure/automation/compose-configurationwithcompositeresources) in Azure of deze toevoegen aan [DSC Configuration-scripts](/powershell/scripting/dsc/configurations/configurations) om MOF-bestanden te genereren en [de MOF-bestanden te uploaden naar Azure Automation.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Registreer vervolgens uw servers vanaf [on-premises](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) of [in Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) om configuraties op te halen.
De laatste update van het project heeft ook [runbooks](https://www.powershellgallery.com/packages?q=DscGallerySamples) voor Azure Automation gepubliceerd om het proces van het importeren van configuraties uit de PowerShell Gallery te automatiseren.

Als u het automatiseren van het maken van samengestelde resources voor DSC wilt uitproberen, gaat u naar de [PowerShell-galerie](https://www.powershellgallery.com/packages/compositeresource/) en downloadt u de oplossing of klikt u op 'Projectsite' om de [documentatie](https://github.com/microsoft/compositeresource)te bekijken.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de gewenste configuratie van Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [DSC-bronnen](/powershell/scripting/dsc/resources/resources)
- [Lokaal configuratiebeheer configureren](/powershell/scripting/dsc/managing-nodes/metaconfig)
