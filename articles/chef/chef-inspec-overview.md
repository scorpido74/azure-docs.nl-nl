---
title: InSpec gebruiken voor compliance-automatisering van uw Azure-infrastructuur
description: Meer informatie over het gebruik van InSpec om problemen in uw Azure-implementaties op te sporen
keywords: azure, chef-kok, devops, virtuele machines, overzicht, automatiseren, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158221"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>InSpec gebruiken voor compliance-automatisering van uw Azure-infrastructuur

[InSpec](https://www.chef.io/inspec/) is de open-sourcetaal van Chef voor het beschrijven van beveiligings- & nalevingsregels die kunnen worden gedeeld tussen softwareengineers, operations en beveiligingstechnici. InSpec werkt door de werkelijke status van uw infrastructuur te vergelijken met de gewenste status die u uitdrukt in gemakkelijk te lezen en gemakkelijk te schrijven InSpec-code. InSpec detecteert schendingen en geeft bevindingen weer in de vorm van een rapport, maar geeft u de controle over de sanering.

U InSpec gebruiken om de status van resources en resourcegroepen in een abonnement te valideren, waaronder virtuele machines, netwerkconfiguraties, Azure Active Directory-instellingen en meer.

In dit artikel worden de voordelen beschreven van het gebruik van InSpec om beveiliging en naleving eenvoudiger te maken op Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Maak compliance gemakkelijk te begrijpen en te beoordelen

Nalevingsdocumentatie geschreven in spreadsheets of Word-documenten laat vereisten open voor interpretatie. Met InSpec zet u uw vereisten om in versieve, uitvoerbare, door de mens leesbare code. Code vervangt gesprekken over wat moet worden beoordeeld ten gunste van tastbare tests met duidelijke bedoeling.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Detecteren van vlootbrede problemen en prioriteren van de sanering

InSpec's agentless detect mode stelt u in staat om snel - op schaal - uw blootstellingsniveau te beoordelen. Ingebouwde metagegevens voor impact/ernstscores bepalen op welke gebieden u zich moet richten voor herstel. U ook snel regels schrijven in reactie op nieuwe kwetsbaarheden of regelgeving en deze onmiddellijk uitrollen.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Azure-virtuele machines controleren met beleidsgastconfiguratie

Azure ondersteunt rechtstreeks het gebruik van Chef InSpec-definities om virtuele Azure-machines te controleren via [Azure Policy Guest Configuration.](/azure/governance/policy/concepts/guest-configuration) Gastconfiguratie evalueert een Virtuele Linux-machine aan een meegeleverde Chef InSpec-definitie en rapporteert naleving via Azure Policy. De resultaten van deze audits worden ook gerapporteerd via Azure Monitor-logboeken; waarschuwingen en andere automatiseringsscenario's mogelijk maken.

## <a name="satisfy-audits"></a>Voldoen aan audits

Met InSpec u op elk gewenst moment op auditvragen reageren - niet alleen met vooraf bepaalde intervallen, zoals driemaandelijks of jaarlijks. Door continu InSpec-tests uit te voeren, voert u een auditcyclus in om uw exacte nalevingshouding en -geschiedenis te kennen, in plaats van verrast te worden door de bevindingen van een auditor.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Probeer InSpec in de Azure Cloud Shell](https://shell.azure.com)