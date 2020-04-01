---
title: Azure Traffic Analytics-schema-update - maart 2020 | Microsoft Documenten
description: Voorbeeldquery's met nieuwe velden in het Traffic Analytics-schema.
services: network-watcher
documentationcenter: na
author: vinigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/06/2020
ms.author: vinigam
ms.openlocfilehash: 4fe981576e3f6e58b0886d9c0d2eb2915d8b7720
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396611"
---
# <a name="sample-queries-with-new-fields-in-the-traffic-analytics-schema-august-2019-schema-update"></a>Voorbeeldquery's met nieuwe velden in het Traffic Analytics-schema (schema-update van augustus 2019)

Het [logboekschema van Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-schema) bevat de volgende nieuwe velden: **SrcPublicIPs_s,** **DestPublicIPs_s,** **NSGRule_s**. De nieuwe velden bieden informatie over ip-adressen van bron en bestemming en vereenvoudigen query's.

In de komende maanden zullen de volgende oudere velden worden afgeschaft: **VMIP_s**, **Subscription_g**, **Region_s**, **NSGRules_s**, **Subnet_s**, **VM_s**, **NIC_s**, **PublicIPs_s**, **FlowCount_d**.

In de volgende drie voorbeelden ziet u hoe u de oude velden vervangen door de nieuwe velden.

## <a name="example-1-vmip_s-subscription_g-region_s-subnet_s-vm_s-nic_s-and-publicips_s-fields"></a>Voorbeeld 1: VMIP_s, Subscription_g, Region_s, Subnet_s, VM_s, NIC_s en PublicIPs_s

We hoeven bron- en doelaanvragen niet af te leiden uit het **FlowDirection_s** veld voor AzurePublic- en ExternalPublic-stromen. Het kan ook ongepast zijn om het **FlowDirection_s** veld te gebruiken voor een virtueel netwerktoestel.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend isAzureOrExternalPublicFlows = FlowType_s in ("AzurePublic", "ExternalPublic")
| extend SourceAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), VM1_s),
SourceAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', VM_s, "N/A"), SrcIP_s),
SourceAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subscription_g, "N/A"), Subscription1_g),
SourceAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Region_s, "N/A"), Region1_s),
SourceAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', Subnet_s, "N/A"), Subnet1_s),
SourceAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'O', NIC_s, "N/A"), NIC1_s),
DestAzureVM = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), VM2_s),
DestAzureVMIP = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', VM_s, "N/A"), DestIP_s),
DestAzureVMSubscription = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subscription_g, "N/A"), Subscription2_g),
DestAzureRegion = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Region_s, "N/A"), Region2_s),
DestAzureSubnet = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', Subnet_s, "N/A"), Subnet2_s),
DestAzureNIC = iif(isAzureOrExternalPublicFlows, iif(FlowDirection_s == 'I', NIC_s, "N/A"), NIC2_s),
SourcePublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'I', PublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isAzureOrExternalPublicFlows and FlowDirection_s == 'O', PublicIPs_s, "N/A")
```


```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend SourceAzureVM = iif(isnotempty(VM1_s), VM1_s, "N/A"),
SourceAzureVMIP = iif(isnotempty(SrcIP_s), SrcIP_s, "N/A"),
SourceAzureVMSubscription = iif(isnotempty(Subscription1_g), Subscription1_g, "N/A"),
SourceAzureRegion = iif(isnotempty(Region1_s), Region1_s, "N/A"),
SourceAzureSubnet = iif(isnotempty(Subnet1_s), Subnet1_s, "N/A"),
SourceAzureNIC = iif(isnotempty(NIC1_s), NIC1_s, "N/A"),
DestAzureVM = iif(isnotempty(VM2_s), VM2_s, "N/A"),
DestAzureVMIP = iif(isnotempty(DestIP_s), DestIP_s, "N/A"),
DestAzureVMSubscription = iif(isnotempty(Subscription2_g), Subscription2_g, "N/A"),
DestAzureRegion = iif(isnotempty(Region2_s), Region2_s, "N/A"),
DestAzureSubnet = iif(isnotempty(Subnet2_s), Subnet2_s, "N/A"),
DestAzureNIC = iif(isnotempty(NIC2_s), NIC2_s, "N/A"),
SourcePublicIPsAggregated = iif(isnotempty(SrcPublicIPs_s), SrcPublicIPs_s, "N/A"),
DestPublicIPsAggregated = iif(isnotempty(DestPublicIPs_s), DestPublicIPs_s, "N/A")
```

## <a name="example-2-nsgrules_s-field"></a>Voorbeeld 2: NSGRules_s veld

Het oude veld gebruikte het formaat:

<Indexwaarde 0)>|<NSG_>van regelnaam |<Flow Direction>|<Flow Status>|<FlowCount ProcessedByRule>

We verzamelen geen gegevens meer in een netwerkbeveiligingsgroep (NSG). In het bijgewerkte schema bevat **NSGList_s** slechts één NSG. Ook **NSGRules** bevat slechts één regel. We hebben de ingewikkelde opmaak hier en in andere velden verwijderd, zoals in het voorbeeld wordt weergegeven.

```Old Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "1"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| extend NSGName = NSGList_s // remains same
| extend NSGRuleName = NSGRuleComponents[1],
         FlowDirection = NSGRuleComponents[2],
         FlowStatus = NSGRuleComponents[3],
         FlowCountProcessedByRule = NSGRuleComponents[4]
| project NSGName, NSGRuleName, FlowDirection, FlowStatus, FlowCountProcessedByRule
```

```New Kusto query
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FASchemaVersion_s == "2"
| extend NSGRuleComponents = split(NSGRules_s, "|")
| project NSGName = NSGList_s,
NSGRuleName = NSGRule_s ,
FlowDirection = FlowDirection_s,
FlowStatus = FlowStatus_s,
FlowCountProcessedByRule = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d
```

## <a name="example-3-flowcount_d-field"></a>Voorbeeld 3: FlowCount_d veld

Omdat we geen gegevens over de NSG club, de **FlowCount_d** is gewoon:

**AllowedInFlows_d** + DeniedInFlows_d**AllowedOutFlows_d DeniedOutFlows_d** + **DeniedOutFlows_d** **AllowedInFlows_d** + 

Slechts één van de vier velden is niet nul. De andere drie velden zijn nul. De velden worden gevuld om de status aan te geven en te tellen in de NIC waar de stroom is vastgelegd.

Ter illustratie van deze voorwaarden:

- Als de stroom is toegestaan, wordt een van de vooraf vastgestelde velden 'Toegestaan' ingevuld.
- Als de stroom is geweigerd, wordt een van de vooraf vastgestelde velden 'Geweigerd' ingevuld.
- Als de stroom binnenstroom is ingevoerd, wordt een van de achterblijvers met InFlows_d ingevuld.
- Als de stroom buiten uitgaan was, wordt een van de achtergewantfixed velden 'OutFlows_d' ingevuld.

Afhankelijk van de omstandigheden weten we welke van de vier velden zal worden bevolkt.

## <a name="next-steps"></a>Volgende stappen

- Zie [Veelgestelde vragen](traffic-analytics-faq.md)over Traffic Analytics voor antwoorden op veelgestelde vragen.
- Zie [Traffic Analytics-documentatie](traffic-analytics.md)voor meer informatie over functionaliteit.
