---
title: Overzicht van Azure Arc voor servers (preview)
description: Meer informatie over het gebruik van Azure Arc voor servers om machines te beheren die buiten Azure worden gehost alsof ze een Azure-resource zijn.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure automation, DSC, powershell, configuratie van gewenste status, updatebeheer, bijhouden van wijzigingen, inventaris, runbooks, python, grafisch, hybride
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 95a01db7d4d889df4695390bfd0d01510d83a817
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648044"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Wat is Azure Arc voor servers (preview)?

Met Azure Arc voor servers (preview) kunt u uw Windows-en Linux-machines die buiten Azure worden gehost, beheren op uw bedrijfsnetwerk of via een andere cloudprovider, op dezelfde manier als systeemeigen virtuele Azure-machines. Wanneer een hybride machine aan Azure wordt gekoppeld, wordt deze een gekoppelde machine en wordt deze behandeld als een resource in Azure. Elke gekoppelde machine heeft een resource-id, wordt beheerd als onderdeel van een resourcegroep binnen een abonnement en profiteert van standaard Azure-constructies zoals Azure Policy en tags toepassen.

Om deze ervaring te bieden voor hybride machines die buiten Azure worden gehost, moet de Azure Connected Machine-agent worden geïnstalleerd op elke machine die u aan Azure wilt koppelen. Deze agent levert geen andere functionaliteit en vervangt de [Log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md) van Azure niet. De Log Analytics-agent voor Windows en Linux is vereist wanneer u het besturingssysteem en workloads op de machine proactief wilt monitoren, deze wilt beheren met Automation-runbooks of oplossingen zoals Updatebeheer, of andere Azure-services zoals [Azure Security Center](../../security-center/security-center-intro.md) wilt gebruiken.

>[!NOTE]
>Deze preview-versie is bedoeld voor evaluatiedoeleinden en wij raden u aan hiermee geen essentiële machines te beheren.
>

## <a name="supported-scenarios"></a>Ondersteunde scenario's

Azure Arc voor servers (preview) ondersteunt de volgende scenario's met gekoppelde machines:

- [Azure Policy-gastconfiguraties](../../governance/policy/concepts/guest-configuration.md) toewijzen met dezelfde ervaring als beleidstoewijzing voor virtuele Azure-machines.
- Gegevens vastleggen die worden verzameld door de Log Analytics-agent en opgeslagen in de Log Analytics-werkruimte waarin de computer is geregistreerd. De logboekgegevens van de hybride machine bevatten nu eigenschappen die specifiek zijn voor de machine, zoals een resource-id, die kunnen worden gebruikt ter ondersteuning van toegang tot het [resource-context](../../azure-monitor/platform/design-logs-deployment.md#access-mode)-logboek.

## <a name="supported-regions"></a>Ondersteunde regio’s

Azure Arc voor servers (preview) ondersteunt alleen bepaalde regio's:

- WestUS2
- West-Europa
- WestAsia

In de meeste gevallen moet de locatie die u selecteert bij het maken van het installatiescript de Azure-regio zijn die geografisch het dichtst bij de locatie van uw machine ligt. Data-at-rest wordt opgeslagen in de Azure-geografie die de door u opgegeven regio bevat. Dit kan ook van invloed zijn op welke regio u kiest als u gegevenslocatievereisten hebt. Als de Azure-regio waaraan uw machine is gekoppeld wordt beïnvloed door een storing, heeft dit geen invloed op de gekoppelde machine, maar beheerbewerkingen die gebruikmaken van Azure kunnen mogelijk niet worden voltooid. Als u meerdere locaties hebt die een geografisch redundante service bieden, kunt u in het geval van een regionale storing het beste de machines per locatie aan verschillende Azure-regio's koppelen.

### <a name="agent-status"></a>Agenstatus

De Connected Machine-agent stuurt elke 5 minuten een standaard heartbeat-bericht naar de service. Als de service de heartbeat-berichten van een machine niet meer ontvangt, wordt de machine als offline beschouwd en wordt de status in de portal na 15 tot 30 minuten automatisch gewijzigd in **Verbinding verbroken**. Wanneer er weer een heartbeat-bericht van de Connected Machine-agent wordt ontvangen, wordt de status van de machine automatisch gewijzigd in **Verbonden**.

## <a name="next-steps"></a>Volgende stappen

Lees, voordat u ARC voor servers evalueert of inschakelt op meerdere hybride machines, het artikel [Connected Machine agent overview](agent-overview.md) (Overzicht van Connected Machine-agent) voor de vereisten, de technische details van de agent en de implementatiemethoden.
