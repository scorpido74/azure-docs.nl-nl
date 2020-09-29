---
title: Azure Policy beveiligings beleid maken en bewerken met behulp van de REST API
description: Meer informatie over Azure Policy-beleids beheer via een REST API.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6fcfed585aece4fd57c085e0c9f6fdcd6cbeae74
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448358"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Een beveiligings beleid configureren in Azure Policy met behulp van de REST API

Als onderdeel van de systeem eigen integratie met Azure Policy, Azure Security Center kunt u de REST API van Azure Policy benutten om beleids toewijzingen te maken. De volgende instructies begeleiden u bij het maken van beleids toewijzingen, evenals het aanpassen van bestaande toewijzingen. 

Belang rijke concepten in Azure Policy: 

- Een **beleids definitie** is een regel 

- Een **initiatief** is een verzameling beleids definities (regels) 

- Een **toewijzing** is een toepassing van een initiatief of een beleid voor een specifiek bereik (beheer groep, abonnement, enz.) 

Security Center heeft een ingebouwd initiatief dat alle beveiligings beleidsregels omvat. Als u het beleid van Security Center op uw Azure-resources wilt beoordelen, moet u een toewijzing maken voor de beheer groep of het abonnement dat u wilt beoordelen.

Het ingebouwde initiatief heeft standaard alle beleids regels van Security Center ingeschakeld. U kunt ervoor kiezen om bepaalde beleids regels uit het ingebouwde initiatief uit te scha kelen. Als u bijvoorbeeld alle beleids regels van Security Center wilt Toep assen, met uitzonde ring van **Web Application firewall**, wijzigt u de waarde van de effect parameter van het beleid in **uitgeschakeld**. 

## <a name="api-examples"></a>API-voorbeelden

Vervang deze variabelen in de volgende voor beelden:

- **{Scope}** Voer de naam in van de beheer groep of het abonnement waarop u het beleid toepast.
- **{policyAssignmentName}** Voer de [naam van de relevante beleids toewijzing in](#policy-names).
- **{name}** Voer uw naam in of de naam van de beheerder die de beleids wijziging heeft goedgekeurd.

Dit voor beeld laat zien hoe u het ingebouwde Security Center initiatief toewijst aan een abonnement of beheer groep
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

Dit voor beeld laat zien hoe u het ingebouwde Security Center initiatief toewijst aan een abonnement, waarbij het volgende beleid is uitgeschakeld: 

- Systeem updates ("systemUpdatesMonitoringEffect") 

- Beveiligings configuraties ("systemConfigurationsMonitoringEffect") 

- Endpoint Protection ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
In dit voor beeld ziet u hoe u een toewijzing verwijdert:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>Naslag informatie over beleids namen <a name="policy-names"></a>

|Beleids naam in Security Center|Beleids naam die wordt weer gegeven in Azure Policy |Parameter naam van beleids effect|
|----|----|----|
|SQL-versleuteling |Niet-versleutelde SQL database in Azure Security Center bewaken |sqlEncryptionMonitoringEffect| 
|Controleren voor SQL |Ongecontroleerde SQL database in Azure Security Center bewaken |sqlAuditingMonitoringEffect|
|Systeemupdates |Ontbrekende systeem updates bewaken in Azure Security Center |systemUpdatesMonitoringEffect|
|Storage-versleuteling |Ontbrekende blobversleuteling voor opslagaccounts controleren |storageEncryptionMonitoringEffect|
|JIT-netwerk toegang |Mogelijke JIT-toegang (just-in-time) in een netwerk controleren in Azure Security Center |jitNetworkAccessMonitoringEffect |
|Adaptieve toepassingsbesturingselementen |Mogelijke apps toestaan lijsten in Azure Security Center te controleren |adaptiveApplicationControlsMonitoringEffect|
|Netwerkbeveiligingsgroepen |Bewaak strikte netwerk toegang in Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Beveiligingsconfiguraties |Beveiligings problemen met het besturings systeem in Azure Security Center bewaken |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |Ontbrekende Endpoint Protection bewaken in Azure Security Center |endpointProtectionMonitoringEffect |
|Schijfversleuteling |Niet-versleutelde VM-schijven in Azure Security Center bewaken |diskEncryptionMonitoringEffect|
|Evaluatie van beveiligingsproblemen |VM-beveiligings problemen in Azure Security Center bewaken |vulnerabilityAssessmentMonitoringEffect|
|Web Application Firewall |Niet-beveiligde webtoepassing in Azure Security Center bewaken |webApplicationFirewallMonitoringEffect |
|Next Generation Firewall |Niet-beveiligde netwerk eindpunten bewaken in Azure Security Center| |


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer gerelateerde materialen: 

- [Aangepast beveiligings beleid](custom-security-policies.md)
- [Overzicht van beveiligings beleid](tutorial-security-policy.md)