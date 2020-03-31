---
title: Azure-beleidsbeveiligingsbeleid maken en bewerken met de REST-API
description: Meer informatie over Azure Policy policy management via een REST API.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430938"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Een beveiligingsbeleid configureren in Azure-beleid met de REST-API

Als onderdeel van de native integratie met Azure Policy stelt Azure Security Center u in staat om gebruik te maken van de REST API van Azure Policy om beleidstoewijzingen te maken. De volgende instructies lopen u door het maken van beleidsopdrachten, evenals het aanpassen van bestaande opdrachten. 

Belangrijke concepten in Azure Policy: 

- Een **beleidsdefinitie** is een regel 

- Een **initiatief** is een verzameling beleidsdefinities (regels) 

- Een **opdracht** is een toepassing van een initiatief of een beleid op een specifieke scope (managementgroup, abonnement, etc.) 

Security Center heeft een ingebouwd initiatief dat al zijn beveiligingsbeleid omvat. Als u het beleid van Security Center voor uw Azure-resources wilt beoordelen, moet u een toewijzing maken voor de beheergroep of het abonnement dat u wilt beoordelen.

Het ingebouwde initiatief heeft standaard het beleid van Security Center ingeschakeld. U ervoor kiezen om bepaalde beleidsregels uit het ingebouwde initiatief uit te schakelen. Als u bijvoorbeeld alle beleidsregels van beveiligingscentrum wilt toepassen, behalve de firewall van **de webtoepassingstoepassing,** wijzigt u de waarde van de effectparameter van het beleid in **Uitgeschakeld**. 

## <a name="api-examples"></a>API-voorbeelden

Vervang in de volgende voorbeelden de volgende variabelen:

- **{scope}** voer de naam in van de beheergroep of het abonnement waarop u het beleid toepast.
- **{policyAssignmentName}** voer de [naam van de relevante beleidstoewijzing in](#policy-names).
- **{naam}** voer uw naam of de naam in van de beheerder die de beleidswijziging heeft goedgekeurd.

In dit voorbeeld ziet u hoe u het ingebouwde Security Center-initiatief toewijst aan een abonnements- of beheergroep
 
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

In dit voorbeeld ziet u hoe u het ingebouwde beveiligingscentrum-initiatief aan een abonnement toewijst, waarbij het volgende beleid is uitgeschakeld: 

- Systeemupdates ("systemUpdatesMonitoringEffect") 

- Beveiligingsconfiguraties ("systemConfigurationsMonitoringEffect") 

- Endpoint protection ("endpointProtectionMonitoringEffect") 

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
In dit voorbeeld ziet u hoe u een toewijzing verwijdert:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>Verwijzing naar beleidsnamen<a name="policy-names"></a>

|Beleidsnaam in Beveiligingscentrum|Beleidsnaam weergegeven in Azure-beleid |Parameternaam Beleidseffect|
|----|----|----|
|SQL-versleuteling |Onversleutelde SQL-database controleren in Azure Security Center |sqlEncryptionMonitoringEffect| 
|Controleren voor SQL |Niet-geauditeerde SQL-database controleren in Azure Security Center |sqlAuditingMonitoringEffect|
|Systeemupdates |Ontbrekende systeemupdates controleren in Azure Security Center |systemUpdatesMonitoringEffect|
|Storage-versleuteling |Ontbrekende blobversleuteling voor opslagaccounts controleren |storageEncryptionMonitoringEffect|
|JIT-netwerktoegang |Mogelijke just-in-time (JIT)-toegang controleren in Azure Security Center |jitNetworkAccessMonitoringEffect |
|Adaptieve toepassingsbesturingselementen |Mogelijke app Whitelisting bewaken in Azure Security Center |adaptieveApplicationControlsMonitoringEffect|
|Netwerkbeveiligingsgroepen |Tolerante netwerktoegang controleren in Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Beveiligingsconfiguraties |Os-kwetsbaarheden in Azure Security Center bewaken |systemConfigurationsMonitoringEffect| 
|Eindpuntbeveiliging |Ontbrekende endpointbeveiliging controleren in Azure Security Center |endpointProtectionMonitoringEffect |
|Schijfversleuteling |Onversleutelde VM-schijven controleren in Azure Security Center |diskEncryptionMonitoringEffect|
|Beoordeling van beveiligingslekken |VM-kwetsbaarheden in Azure Security Center bewaken |vulnerabilityAssessmentMonitoringEffect|
|Web Application Firewall |Onbeschermde webtoepassing controleren in Azure Security Center |webApplicationFirewallMonitoringEffect |
|Next Generation Firewall |Onbeschermde netwerkeindpunten bewaken in Azure Security Center| |


## <a name="next-steps"></a>Volgende stappen

Zie voor ander gerelateerd materiaal de volgende artikelen: 

- [Aangepast beveiligingsbeleid](custom-security-policies.md)
- [Overzicht van het beveiligingsbeleid](tutorial-security-policy.md)