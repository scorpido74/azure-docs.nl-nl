---
title: Onboarding naar Azure Security Center met Power shell
description: Dit document helpt u bij het voorbereiden van Azure Security Center met Power shell-cmdlets.
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
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603686"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Het onboarden van Azure Security Center automatiseren met Power shell

U kunt uw Azure-workloads programmatisch beveiligen met behulp van de Azure Security Center Power shell-module.
Met behulp van Power shell kunt u taken automatiseren en de menselijke fout voor komen die inherent zijn aan hand matige taken. Dit is vooral handig bij grootschalige implementaties waarbij tien tallen abonnementen betrokken zijn met honderden en duizenden resources, die allemaal moeten worden beveiligd vanaf het begin.

Met behulp van de onboarding Azure Security Center met Power shell kunt u uw Azure-resources op een programmatische manier voorbereiden en beheren en de benodigde beveiligings controles toevoegen.

Dit artikel bevat een voor beeld van een Power shell-script dat kan worden gewijzigd en in uw omgeving kan worden gebruikt om Security Center in uw abonnementen uit te vouwen. 

In dit voor beeld wordt Security Center ingeschakeld voor een abonnement met ID: d07c0080-170c-4c24-861d-9c817742786c en worden de aanbevolen instellingen toegepast die een hoog niveau van beveiliging bieden door de implementatie van de laag standaard van Security Center. Dit biedt geavanceerde mogelijkheden voor bedreigings beveiliging en detectie:

1. Stel het [Security Center standaard beveiligings niveau in](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Stel de Log Analytics-werk ruimte in waarnaar de gegevens die worden verzameld op de Vm's die zijn gekoppeld aan het abonnement worden verzonden met behulp van de micro soft monitoring agent. in dit voor beeld is dit een bestaande door de gebruiker gedefinieerde werk ruimte (myWorkspace).

3. Activeer de automatische agent inrichting van Security Center waarin [de micro soft monitoring agent wordt geïmplementeerd](security-center-enable-data-collection.md#auto-provision-mma).

5. Stel de ciso van de organisatie [in als de beveiligings contact persoon voor Security Center waarschuwingen en](security-center-provide-security-contact-details.md)belang rijke gebeurtenissen.

6. Wijs het [standaard beveiligings beleid](tutorial-security-policy.md)van Security Center toe.

## <a name="prerequisites"></a>Vereisten

Deze stappen moeten worden uitgevoerd voordat u de Security Center-cmdlets uitvoert:

1.  Voer Power shell uit als beheerder.
2.  Voer de volgende opdrachten uit in Power shell:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Onboard Security Center met Power shell

1.  Uw abonnementen registreren bij de provider van de Security Center-resource:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Optioneel: het dekkings niveau (prijs categorie) van de abonnementen instellen (indien niet gedefinieerd, wordt de prijs categorie ingesteld op gratis):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Een Log Analytics-werk ruimte configureren waarin de agents worden gerapporteerd. U moet een Log Analytics-werk ruimte hebben die u al hebt gemaakt, waarbij de Vm's van het abonnement worden gerapporteerd aan. U kunt meerdere abonnementen definiëren om aan dezelfde werk ruimte te rapporteren. Als deze niet is gedefinieerd, wordt de standaardwerk ruimte gebruikt.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  De installatie van micro soft monitoring agent automatisch inrichten op uw Azure-Vm's:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Het wordt aanbevolen automatische inrichting in te scha kelen om ervoor te zorgen dat uw virtuele Azure-machines automatisch worden beveiligd door Azure Security Center.
    >

5.  Optioneel: u kunt het beste de contact gegevens van de beveiliging definiëren voor de abonnementen die u wilt voorbereiden. deze worden gebruikt als ontvangers van waarschuwingen en meldingen die worden gegenereerd door Security Center:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Wijs het standaard Security Center Policy Initiative toe:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

U hebt nu onboarded Azure Security Center met Power shell.

U kunt deze Power shell-cmdlets nu met automatiserings scripts gebruiken om op een programmatische manier te herhalen voor abonnementen en bronnen. Dit bespaart tijd en vermindert de kans op menselijke fouten. U kunt dit [voorbeeld script](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) gebruiken als verwijzing.






## <a name="see-also"></a>Zie ook
Zie het volgende artikel voor meer informatie over hoe u Power shell kunt gebruiken om onboarding naar Security Center te automatiseren:

* [AZ. Security](https://docs.microsoft.com/powershell/module/az.security).

Zie het volgende artikel voor meer informatie over Security Center:

* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.