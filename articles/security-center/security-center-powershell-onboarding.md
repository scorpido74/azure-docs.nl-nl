---
title: Onboard naar Azure Security Center met PowerShell
description: Dit document leidt u door het proces van onboarding Azure Security Center met PowerShell-cmdlets.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2018
ms.author: memildin
ms.openlocfilehash: 5aaaf539c07a7ba2c2463d5bfd1f452853f52379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603686"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Onboarding van Azure Security Center automatiseren met PowerShell

U uw Azure-workloads programmatisch beveiligen met de PowerShell-module azure Security Center.
Met PowerShell u taken automatiseren en de menselijke fout vermijden die inherent is aan handmatige taken. Dit is vooral handig bij grootschalige implementaties waarbij tientallen abonnementen met honderden en duizenden resources betrokken zijn , die allemaal vanaf het begin moeten worden beveiligd.

Onboarding Azure Security Center met PowerShell stelt u in staat om onboarding en beheer van uw Azure-resources programmatisch te automatiseren en de nodige beveiligingscontroles toe te voegen.

In dit artikel vindt u een voorbeeld van PowerShell-script dat in uw omgeving kan worden gewijzigd en gebruikt om Beveiligingscentrum voor uw abonnementen uit te rollen. 

In dit voorbeeld schakelen we Security Center in op een abonnement met ID: d07c0080-170c-4c24-861d-9c817742786c en passen de aanbevolen instellingen toe die een hoog beschermingsniveau bieden, door de standaardlaag van beveiligingscentrum te implementeren, die de standaardlaag van beveiligingscentrum implementeert, die de aanbevolen instellingen biedt die een hoog beschermingsniveau bieden, geavanceerde mogelijkheden voor bescherming en detectie van bedreigingen:

1. Stel het [standaardbeschermingsniveau van het Beveiligingscentrum in.](https://azure.microsoft.com/pricing/details/security-center/) 
 
2. Stel de werkruimte Log Analytics in waarnaar de Microsoft Monitoring Agent de gegevens verzendt die worden gebruikt op de VM's die aan het abonnement zijn gekoppeld, in dit voorbeeld een bestaande door de gebruiker gedefinieerde werkruimte (myWorkspace).

3. Activeer de automatische agentinrichting van het beveiligingscentrum die [de Microsoft Monitoring Agent implementeert.](security-center-enable-data-collection.md#auto-provision-mma)

5. Stel de CISO van de organisatie [in als de beveiligingscontactpersoon voor waarschuwingen van het Beveiligingscentrum en opmerkelijke gebeurtenissen.](security-center-provide-security-contact-details.md)

6. Het [standaardbeveiligingsbeleid](tutorial-security-policy.md)van beveiligingscentrum toewijzen .

## <a name="prerequisites"></a>Vereisten

Deze stappen moeten worden uitgevoerd voordat u de cmdlets van het Beveiligingscentrum uitvoert:

1.  Voer PowerShell uit als beheerder.
2.  Voer de volgende opdrachten uit in PowerShell:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>In boord van beveiligingscentrum met PowerShell

1.  Registreer uw abonnementen op de Security Center Resource Provider:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Optioneel: stel het dekkingsniveau (prijscategorie) van de abonnementen in (Als deze niet is gedefinieerd, is de prijscategorie ingesteld op Gratis):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Configureer een Log Analytics-werkruimte waaraan de agents rapporteren. U moet een Log Analytics-werkruimte hebben die u al hebt gemaakt, waaraan de VM's van het abonnement worden gemeld. U meerdere abonnementen definiëren om aan dezelfde werkruimte te rapporteren. Als deze niet is gedefinieerd, wordt de standaardwerkruimte gebruikt.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Installatie van de Microsoft-bewakingsagent automatisch inrichten op uw Azure VM's:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Het wordt aanbevolen om automatische inrichting in te schakelen om ervoor te zorgen dat uw virtuele Azure-machines automatisch worden beschermd door Azure Security Center.
    >

5.  Optioneel: het wordt ten zeerste aanbevolen om de beveiligingscontactgegevens te definiëren voor de abonnementen die u aan boord hebt, die worden gebruikt als ontvangers van waarschuwingen en meldingen die worden gegenereerd door security center:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Wijs het standaard beleidsinitiatief beveiligingscentrum toe:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

U hebt nu met PowerShell een Azure Security Center aan boord genomen!

U deze PowerShell-cmdlets nu gebruiken met automatiseringsscripts om programmatisch te herhalen tussen abonnementen en resources. Dit bespaart tijd en vermindert de kans op menselijke fouten. U dit [voorbeeldscript](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) als referentie gebruiken.






## <a name="see-also"></a>Zie ook
Zie het volgende artikel voor meer informatie over hoe u PowerShell gebruiken om onboarding te automatiseren in beveiligingscentrum:

* [Az.Security](https://docs.microsoft.com/powershell/module/az.security).

Zie het volgende artikel voor meer informatie over Beveiligingscentrum:

* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Beveiligingswaarschuwingen beheren en beantwoorden in Azure Security Center:](security-center-managing-and-responding-alerts.md) meer informatie over het beheren en reageren op beveiligingswaarschuwingen.