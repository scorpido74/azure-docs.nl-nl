---
title: Gebruik de inspecheid voor de compatibiliteits automatisering van uw Azure-infra structuur
description: Meer informatie over het gebruik van de specificaties voor het detecteren van problemen in uw Azure-implementaties
keywords: Azure, chef, devops, virtual machines, overzicht, automatiseren, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158221"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Gebruik de inspecheid voor de compatibiliteits automatisering van uw Azure-infra structuur

[Inspec](https://www.chef.io/inspec/) is de open-source taal van chef voor het beschrijven van beveiliging & nalevings regels die kunnen worden gedeeld tussen software technici, Operations en beveiligings technici. Inspecies werkt door de werkelijke status van uw infra structuur te vergelijken met de gewenste status die u in eenvoudig te lezen en eenvoudig te schrijven code kunt gebruiken. Met Inspec worden schendingen gedetecteerd en worden de bevindingen in de vorm van een rapport weer gegeven, maar wordt u aangeraden om het herstel uit te voeren.

U kunt Inspec gebruiken om de status van resources en resource groepen in een abonnement te valideren, zoals virtuele machines, netwerk configuraties, Azure Active Directory instellingen en meer.

In dit artikel worden de voor delen van het gebruik van inspecers beschreven om de beveiliging en naleving van Azure gemakkelijker te maken.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Naleving gemakkelijk te begrijpen en te beoordelen

In werk bladen of Word-documenten zijn de vereisten voor naleving open voor interpretatie. Met de specificatie transformeert u uw vereisten in gecodeerde, uitvoer bare, door de mens lees bare code. Code vervangt gesp rekken over wat er moet worden beoordeeld in het voor deel van concrete tests met een duidelijke intentie.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Problemen met betrekking tot het hele vloot detecteren en hun herstel prioriteiten toekennen

Met de detectie modus zonder agents kunt u snel op schaal beoordelen-uw blootstellings niveau. Dankzij de ingebouwde meta gegevens voor de impact/Ernst Score kunt u bepalen op welke gebieden u zich kunt richten op herstel. U kunt regels ook snel schrijven in reactie op nieuwe beveiligings problemen of voor schriften en deze onmiddellijk samen vouwen.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Virtuele Azure-machines controleren met beleids gast configuratie

Azure biedt rechtstreeks ondersteuning voor het gebruik van chef-Inspec-definities om virtuele Azure-machines te controleren via [Azure Policy-gast configuratie](/azure/governance/policy/concepts/guest-configuration). Gast configuratie evalueert een virtuele Linux-machine naar een geleverde chef-specificatie definitie en rapporteert de compatibiliteit weer via Azure Policy. De resultaten van deze controles worden ook gerapporteerd via Azure Monitor-Logboeken. waarschuwingen en andere scenario's voor automatisering inschakelen.

## <a name="satisfy-audits"></a>Voldoet aan controles

Met de specificatie kunt u op elk gewenst moment reageren op vragen over controle, niet alleen met vooraf vastgestelde intervallen zoals elk kwar taal of jaarlijks. Door continue Inspec-tests uit te voeren, voert u een controle cyclus in die precies overeenkomt met uw nauw keurige postuur en geschiedenis, in plaats van te ververbaasden door de bevindingen van een auditor.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Voer de specificatie uit in de Azure Cloud Shell](https://shell.azure.com)