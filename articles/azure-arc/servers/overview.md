---
title: Overzicht van Azure Arc voor servers (preview)
description: Lees meer informatie over het gebruik van Azure Arc voor servers (preview) om servers te beheren die buiten Azure worden gehost alsof het Azure-resources zijn.
keywords: azure automation, DSC, powershell, configuratie van gewenste status, updatebeheer, bijhouden van wijzigingen, inventaris, runbooks, python, grafisch, hybride
ms.custom: references_regions
ms.date: 08/06/2020
ms.topic: overview
ms.openlocfilehash: c368307df911f5143541bb5337eb76a208416909
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228084"
---
# <a name="what-is-azure-arc-enabled-servers-preview"></a>Wat is Azure Arc voor servers (preview)?

Met Azure Arc voor servers (preview) kunt u uw Windows- en Linux-machines die buiten Azure worden gehost, beheren in uw bedrijfsnetwerk of via een andere cloudprovider, op dezelfde manier als systeemeigen Azure-VM's. Wanneer een hybride machine aan Azure wordt gekoppeld, wordt deze een gekoppelde machine en wordt deze behandeld als een resource in Azure. Elke gekoppelde machine heeft een resource-id, wordt beheerd als onderdeel van een resourcegroep binnen een abonnement en profiteert van standaard Azure-constructies zoals Azure Policy en tags toepassen.

Om deze ervaring te bieden voor hybride machines die buiten Azure worden gehost, moet de Azure Connected Machine-agent worden geïnstalleerd op elke machine die u aan Azure wilt koppelen. Deze agent levert geen andere functionaliteit en vervangt de [Log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md) van Azure niet. De Log Analytics-agent voor Windows en Linux is vereist wanneer u het besturingssysteem en workloads op de machine proactief wilt monitoren, deze wilt beheren met Automation-runbooks of oplossingen zoals Updatebeheer, of andere Azure-services zoals [Azure Security Center](../../security-center/security-center-intro.md) wilt gebruiken.

>[!NOTE]
>Deze preview-versie is bedoeld voor evaluatiedoeleinden en wij raden u aan hiermee geen essentiële machines te beheren.
>

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Wanneer u uw machine verbindt met Azure Arc voor servers (preview), kunt u de volgende configuratiebeheertaken uitvoeren:

- [Azure Policy-gastconfiguraties](../../governance/policy/concepts/guest-configuration.md) toewijzen met dezelfde ervaring als beleidstoewijzing voor virtuele Azure-machines.

- Controleer de prestaties van het gastbesturingssysteem van uw verbonden computer en ontdek toepassingscomponenten om hun processen en afhankelijkheden te controleren met andere resources die de door de toepassing worden gecommuniceerd met behulp van [Azure Monitor voor VM's](../../azure-monitor/insights/vminsights-overview.md).

- Vereenvoudig de implementatie met andere Azure-services zoals Azure Automation State Configuration en Azure Monitor Log Analytics-werkruimte door gebruik te maken van de ondersteunde [Azure VM-extensies](manage-vm-extensions.md) voor uw niet-Azure Windows- of Linux-machines. Dit omvat het uitvoeren van configuratie na de implementatie of software-installatie met behulp van de aangepaste scriptextensie.

Aanmeldingsgegevens die zijn verzameld en opgeslagen in een Log Analytics-werkruimte van de hybride machine bevatten nu eigenschappen die specifiek zijn voor de machine, zoals een resource-id. Dit kan worden gebruikt ter ondersteuning van toegang tot het logboek met [resource-context](../../azure-monitor/platform/design-logs-deployment.md#access-mode).

## <a name="supported-regions"></a>Ondersteunde regio’s

Azure Arc voor servers (preview) ondersteunt alleen bepaalde regio's:

- EastUS
- WestUS2
- West-Europa
- SoutheastAsia

In de meeste gevallen moet de locatie die u selecteert bij het maken van het installatiescript de Azure-regio zijn die geografisch het dichtst bij de locatie van uw machine ligt. Data-at-rest wordt opgeslagen in de Azure-geografie die de door u opgegeven regio bevat. Dit kan ook van invloed zijn op welke regio u kiest als u gegevenslocatievereisten hebt. Als de Azure-regio waaraan uw machine is gekoppeld wordt beïnvloed door een storing, heeft dit geen invloed op de gekoppelde machine, maar beheerbewerkingen die gebruikmaken van Azure kunnen mogelijk niet worden voltooid. Als u meerdere locaties hebt die een geografisch redundante service ondersteunen, kunt u bij een regionale storing het beste de machines in elke locatie aan een andere Azure-regio koppelen.

### <a name="agent-status"></a>Agenstatus

De Connected Machine-agent stuurt elke 5 minuten een standaard heartbeat-bericht naar de service. Als de service de heartbeat-berichten van een machine niet meer ontvangt, wordt de machine als offline beschouwd en wordt de status in de portal na 15 tot 30 minuten automatisch gewijzigd in **Verbinding verbroken**. Wanneer er weer een heartbeat-bericht van de Connected Machine-agent wordt ontvangen, wordt de status van de machine automatisch gewijzigd in **Verbonden**.

## <a name="next-steps"></a>Volgende stappen

Lees voordat u Arc voor servers (preview) evalueert of inschakelt op meerdere hybride machines het artikel [Overzicht van Connected Machine Agent](agent-overview.md) om inzicht te krijgen in de vereisten, de technische details van de agent en de implementatiemethoden.
