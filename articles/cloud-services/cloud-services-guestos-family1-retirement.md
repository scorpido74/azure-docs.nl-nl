---
title: Gast OS familie 1 pensioenkennisgeving | Microsoft Documenten
description: Geeft informatie over wanneer het azure guest OS Family 1-pensioen is gebeurd en hoe u bepalen of u wordt beïnvloed
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: ae2df6f47d99fc5d452a6d3ea70f2dd2e4e7416b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359580"
---
# <a name="guest-os-family-1-retirement-notice"></a>Kennisgeving van buitengebruikstelling van serie 1 van gastbesturingssysteem
De pensionering van OS Family 1 werd voor het eerst aangekondigd op 1 juni 2013.

**2 september 2014** Het Azure Guest-besturingssysteem (Guest OS) Family 1.x, dat is gebaseerd op het Windows Server 2008-besturingssysteem, is officieel buiten gebruik gesteld. Alle pogingen om nieuwe services te implementeren of bestaande services te upgraden met Gezins 1 mislukken met een foutmelding waarin staat dat de GastOS-familie 1 is uitgeschakeld.

**3 november 2014** Uitgebreide ondersteuning voor Guest OS Family 1 is beëindigd en deze is volledig met pensioen. Alle diensten die nog op Family 1 staan, worden beïnvloed. We kunnen die diensten op elk moment stopstaan. Er is geen garantie dat uw services blijven draaien, tenzij u ze zelf handmatig upgradet.

Als u aanvullende vragen hebt, gaat u naar de [Cloud Services Forums](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) of neemt u contact op met [Azure-ondersteuning.](https://azure.microsoft.com/support/options/)

## <a name="are-you-affected"></a>Heb je er last van?
Uw Cloud Services worden beïnvloed als een van de volgende wijzigingen van toepassing is:

1. U hebt een waarde van "osFamily = "1" expliciet opgegeven in het Bestand ServiceConfiguration.cscfg voor uw Cloud Service.
2. U hebt geen waarde voor osFamily die expliciet is opgegeven in het Bestand ServiceConfiguration.cscfg voor uw Cloud Service. Momenteel gebruikt het systeem in dit geval de standaardwaarde van "1".
3. De Azure-portal vermeldt de gezinswaarde van uw gastbesturingssysteem als "Windows Server 2008".

Als u wilt weten welke van uw cloudservices welk OS-gezin wordt uitgevoerd, u het volgende script uitvoeren in Azure PowerShell, hoewel u [Azure PowerShell](/powershell/azureps-cmdlets-docs) eerst moet instellen. Zie Azure Guest OS [Family 1 End of Life: juni 2014 voor](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx)meer informatie over het script.

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Uw cloudservices worden beïnvloed door het pensioen van OS Family 1 als de kolom osFamily in de scriptuitvoer leeg is of een '1' bevat.

## <a name="recommendations-if-you-are-affected"></a>Aanbevelingen als u wordt beïnvloed
We raden u aan uw Cloud Service-rollen te migreren naar een van de ondersteunde gast-osfamilies:

**Gastos-familie 4.x** - Windows Server 2012 R2 *(aanbevolen)*

1. Controleer of uw toepassing SDK 2.1 of hoger gebruikt met .NET framework 4.0, 4.5 of 4.5.1.
2. Stel het kenmerk osFamily in op '4' in het bestand ServiceConfiguration.cscfg en implementeer uw cloudservice opnieuw.

**Gastos-familie 3.x** - Windows Server 2012

1. Zorg ervoor dat uw toepassing SDK 1.8 of hoger gebruikt met .NET framework 4.0 of 4.5.
2. Stel het kenmerk osFamily in op '3' in het bestand ServiceConfiguration.cscfg en implementeer uw cloudservice opnieuw.

**Gastos-familie 2.x** - Windows Server 2008 R2

1. Zorg ervoor dat uw toepassing SDK 1.3 en hoger gebruikt met .NET framework 3.5 of 4.0.
2. Stel het kenmerk osFamily in op '2' in het bestand ServiceConfiguration.cscfg en implementeer uw cloudservice opnieuw.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Uitgebreide ondersteuning voor gastbesturingssysteem familie 1 eindigde op 3 november 2014
Cloudservices op Guest OS-familie 1 worden niet langer ondersteund. Migreer zo snel mogelijk van familie 1 om onderbreking van de service te voorkomen.  

## <a name="next-steps"></a>Volgende stappen
Bekijk de nieuwste [Gast OS releases](cloud-services-guestos-update-matrix.md).
