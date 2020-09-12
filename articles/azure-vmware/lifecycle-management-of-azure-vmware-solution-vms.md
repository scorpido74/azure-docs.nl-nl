---
title: Levenscyclus beheer van virtuele machines met Azure VMware-oplossingen
description: Meer informatie over het beheren van alle aspecten van de levens cyclus van uw Azure VMware-oplossing-Vm's met Microsoft Azure systeem eigen hulpprogram ma's.
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 20948ec088d11468b5750ca89979050965246b58
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664230"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Levenscyclus beheer van virtuele machines met Azure VMware-oplossingen

Met Microsoft Azure systeem eigen hulp middelen kunt u uw virtuele machines (Vm's) bewaken en beheren in de Azure-omgeving. Daarnaast kunt u hiermee uw Vm's bewaken en beheren op Azure VMware-oplossing en uw on-premises Vm's. In dit overzicht bekijken we de geïntegreerde Azure-bewakings architectuur en hoe u de systeem eigen hulpprogram ma's kunt gebruiken voor het beheren van uw Azure VMware-oplossings-Vm's in de levens cyclus.

## <a name="benefits"></a>Voordelen

- Systeem eigen services van Azure kunnen worden gebruikt voor het beheren van uw virtuele machines in een hybride omgeving (Azure, Azure VMware-oplossing en on-premises).
- Geïntegreerde bewaking en zicht baarheid van uw Azure, Azure VMware-oplossing en on-premises Vm's.
- Met Azure Updatebeheer in Azure Automation kunt u updates van het besturings systeem voor zowel uw Windows-als Linux-machines beheren. 
- Azure Security Center biedt geavanceerde beveiliging tegen bedreigingen, waaronder:
    - Bestandsintegriteit controleren
    - Beveiligings waarschuwingen met een bestand
    - Evaluatie van patch voor besturings systeem
    - Evaluatie van onjuiste beveiligings configuratie
    - Endpoint Protection-evaluatie 
- Implementeer eenvoudig de micro soft Monitoring Agent (MMA) met behulp van Azure ARC voor nieuwe Vm's. 
- Met uw Log Analytics-werk ruimte in Azure Monitor kan de verzameling van Logboeken en prestatie meter items worden verzameld met MMA of extensies. Verzamel gegevens en logboeken op één punt en presenteer die gegevens aan verschillende Azure systeem eigen services. 
- Extra voor delen van Azure Monitor zijn: 
    - Naadloze bewaking 
    - Beter inzicht in de infra structuur 
    - Chat meldingen 
    - Automatische resolutie 
    - Kosten efficiëntie 

## <a name="integrated-azure-monitoring-architecture"></a>Geïntegreerde Azure-bewakings architectuur

In het volgende diagram ziet u de geïntegreerde bewakings architectuur voor virtuele machines van Azure VMware-oplossingen.

![Geïntegreerde Azure-bewakings architectuur](media/lifecycle-mgmt-avs-vms/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>Azure native Services integreren en implementeren

**Azure Arc** breidt Azure-beheer uit naar elke infra structuur, waaronder Azure VMware-oplossing, on-premises of andere Cloud platforms. Azure ARC kan worden geïmplementeerd door een Azure Kubernetes service (AKS)-cluster te installeren in de Azure VMware-oplossings omgeving. Zie [verbinding maken met een Azure Arc-Kubernetes-cluster](../azure-arc/kubernetes/connect-cluster.md)voor meer informatie.

Virtuele machines van Azure VMware-oplossingen kunnen worden bewaakt via MMA (ook wel Log Analytics agent of OMS Linux-agent genoemd). MMA kan automatisch worden geïnstalleerd wanneer Vm's worden ingericht via levenscyclus werk stromen van ARC VM. MMA kan ook worden geïnstalleerd wanneer u Vm's implementeert vanuit een sjabloon in vCenter. opnieuw, met virtuele machines die via ARC-werk stromen worden ingericht. Alle ingerichte Vm's van Azure VMware-oplossingen kunnen vervolgens MMA geïnstalleerd hebben en logboeken verzenden naar de Azure Log Analytics-werk ruimte. Zie [log Analytics agent Overview](../azure-monitor/platform/log-analytics-agent.md)(Engelstalig) voor meer informatie.

Met **log Analytics werk ruimte** kan de verzameling van Logboeken en prestatie meter items worden verzameld met MMA of uitbrei dingen. Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/learn/quick-create-workspace.md)om uw log Analytics-werk ruimte te maken.
- Zie [log Analytics-agent installeren op Windows-computers](../azure-monitor/platform/agent-windows.md)als u Windows-vm's wilt toevoegen aan uw log Analytics-werk ruimte.
- Zie [log Analytics-agent installeren op Linux-computers](../azure-monitor/platform/agent-linux.md)als u Linux-vm's wilt toevoegen aan uw log Analytics-werk ruimte.

**Azure updatebeheer** in azure Automation beheert updates van het besturings systeem voor uw Windows-en Linux-computers in een hybride omgeving. Hiermee wordt de compatibiliteit van patches bewaakt en worden waarschuwingen over de afwijking van patches doorgestuurd naar Azure Monitor voor herstel. Azure Updatebeheer moet verbinding maken met uw Log Analytics-werk ruimte om opgeslagen gegevens te kunnen gebruiken om de status van updates op uw Vm's te beoordelen.
- Als u Log Analytics wilt toevoegen aan Azure Updatebeheer, moet u eerst [een Azure Automation-account maken](../automation/automation-create-standalone-account.md).
- Als u uw Log Analytics-werk ruimte wilt koppelen aan uw Automation-account, raadpleegt u [log Analytics werk ruimte en Automation-account](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).
- Als u Azure Updatebeheer wilt inschakelen voor uw virtuele machines, raadpleegt u [updatebeheer inschakelen vanuit een Automation-account](../automation/update-management/update-mgmt-enable-automation-account.md).
- Zodra u Vm's hebt toegevoegd aan Azure Updatebeheer, kunt u [updates op Vm's implementeren en resultaten bekijken](../automation/update-management/update-mgmt-deploy-updates.md). 

**Azure Security Center** biedt geavanceerde beveiliging tegen bedreigingen in uw hybride werk belastingen in de Cloud en on-premises. Hiermee wordt het beveiligings probleem van Vm's van Azure VMware-oplossingen beoordeeld en worden indien nodig waarschuwingen gegenereerd. Deze beveiligings waarschuwingen kunnen worden doorgestuurd naar Azure Monitor voor oplossing.
- Voor Azure Security Center is geen implementatie vereist. Zie een lijst met [ondersteunde functies voor virtuele machines](../security-center/security-center-services.md)voor meer informatie.
- Als u virtuele machines met Azure VMware-oplossingen en niet-Azure-Vm's wilt toevoegen aan Azure Security Center, raadpleegt u [Windows-computers Onboarden om](../security-center/quick-onboard-windows-computer.md) Linux-computers Azure Security Center en [onboarding te Azure Security Center](../security-center/quick-onboard-linux-computer.md).
- Na het toevoegen van Vm's, Azure Security Center analyseert de beveiligings status van de resources om mogelijke beveiligings problemen te identificeren. Het bevat ook aanbevelingen op het tabblad Overzicht. Zie [beveiligings aanbevelingen in azure Security Center](../security-center/security-center-recommendations.md)voor meer informatie.
- U kunt beveiligings beleid definiëren in Azure Security Center. Zie [werken met beveiligings beleid](../security-center/tutorial-security-policy.md)voor meer informatie over het configureren van uw beveiligings beleid.

**Azure monitor** is een uitgebreide oplossing voor het verzamelen, analyseren en werken op telemetrie in uw Cloud-en on-premises omgevingen. Hiervoor is geen implementatie vereist.
- Met Azure Monitor kunt u gegevens verzamelen uit verschillende bronnen om te controleren en analyseren. Zie [bronnen van bewakings gegevens voor Azure monitor](../azure-monitor/platform/data-sources.md)voor meer informatie.
- U kunt ook verschillende typen gegevens verzamelen voor analyse, visualisatie en waarschuwingen. Zie [Azure monitor data platform](../azure-monitor/platform/data-platform.md)voor meer informatie.
- Zie [log Analytics-werk ruimte configureren voor Azure monitor voor VM's](../azure-monitor/insights/vminsights-configure-workspace.md)om Azure monitor te configureren met uw log Analytics-werk ruimte.
- U kunt waarschuwings regels maken om problemen in uw omgeving te identificeren, zoals een hoog gebruik van resources, ontbrekende patches, weinig schijf ruimte en heartbeat van uw Vm's. U kunt ook een geautomatiseerd antwoord op gedetecteerde gebeurtenissen instellen door een waarschuwing te verzenden naar de ITSM-hulpprogram ma's (IT Service Management). Melding over waarschuwings detectie kan ook via e-mail worden verzonden. Als u dergelijke regels wilt maken, raadpleegt u:
    - [Metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../azure-monitor/platform/alerts-metric.md).
    - [Logboek waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../azure-monitor/platform/alerts-log.md).
    - [Actie regels](../azure-monitor/platform/alerts-action-rules.md) om geautomatiseerde acties en meldingen in te stellen.
    - [Verbind Azure met ITSM-hulpprogram ma's met behulp van IT Service Management-connector](../azure-monitor/platform/itsmc-overview.md).

**Azure Platform as a Service (PaaS)** is een ontwikkel-en implementatie omgeving in de Cloud, met resources waarmee u Cloud toepassingen kunt leveren. U kunt bijvoorbeeld Azure SQL Database integreren met uw Azure VMware-oplossing-Vm's. SQL-waarschuwingen kunnen vervolgens worden gecorreleerd met Azure VMware-oplossing VM-waarschuwingen. Stel bijvoorbeeld dat de SQL Database poot van uw toepassing zich in de Azure-PAAS bevindt en dat de toepassingslaag van dezelfde toepassing wordt gehost op uw Azure VMware-oplossing-Vm's. Database waarschuwingen kunnen vervolgens worden gecorreleerd met waarschuwingen voor webtoepassingen. Probleem oplossing is vereenvoudigd met één geïntegreerde zicht baarheid van Azure, Azure VMware-oplossing en on-premises Vm's.
