---
title: Werken met beveiligings beleid | Microsoft Docs
description: In dit artikel wordt beschreven hoe u kunt werken met beveiligings beleid in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: memildin
ms.openlocfilehash: 11e1c837e416a5d2105f494c43823298edfd4355
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200494"
---
# <a name="working-with-security-policies"></a>Werken met beveiligingsbeleid

In dit artikel wordt uitgelegd hoe beveiligings beleid wordt geconfigureerd en hoe u deze in Security Center kunt weer geven. Azure Security Center wijst automatisch het [ingebouwde beveiligings beleid](security-center-policy-definitions.md) toe voor elk abonnement dat onboarded is. U kunt ze configureren in [Azure Policy](../governance/policy/overview.md), waarmee u ook beleid kunt instellen voor verschillende beheer groepen en meerdere abonnementen.

Voor instructies over het instellen van beleids regels met behulp [van Power shell raadpleegt u Quick Start: Een beleids toewijzing maken om niet-compatibele resources te identificeren met behulp](../governance/policy/assign-policy-powershell.md)van de module Azure PowerShell.

>[!NOTE]
> Security Center is de integratie met Azure Policy gestart. Bestaande klanten worden automatisch gemigreerd naar het nieuwe ingebouwde initiatief in Azure Policy, in plaats van het vorige beveiligings beleid in Security Center. Deze wijziging heeft geen invloed op uw resources of omgeving, behalve de aanwezigheid van het nieuwe initiatief in Azure Policy.

## <a name="what-are-security-policies"></a>Wat is beveiligingsbeleid?
Een beveiligingsbeleid definieert de gewenste configuratie van uw workloads en helpt ervoor te zorgen dat aan de beveiligingsvereisten van het bedrijf of aan regelgeving wordt voldaan. In Azure Policy kunt u beleid voor uw Azure-abonnementen definiëren en deze aanpassen aan uw type werk belasting of de gevoeligheid van uw gegevens. Toepassingen die gebruikmaken van gereguleerde gegevens, zoals persoons gegevens of klant gegevens, kunnen bijvoorbeeld een hoger beveiligings niveau vereisen dan andere workloads. Als u een beleid wilt instellen voor meerdere abonnementen of voor beheer groepen, stelt u deze in [Azure Policy](../governance/policy/overview.md).

Uw beveiligings beleid is een schijf met de aanbevelingen voor beveiliging die u in Azure Security Center krijgt. U kunt de compatibiliteit met deze controleren om mogelijke beveiligings problemen te identificeren en bedreigingen te verhelpen. Zie de lijst met [ingebouwde beveiligings beleidsregels](security-center-policy-definitions.md)voor meer informatie over het bepalen van de optie die geschikt is voor u.

Als u Security Center inschakelt, wordt het ingebouwde beveiligings beleid voor Security Center in Azure Policy als een ingebouwd initiatief, onder de categorie Security Center, weer gegeven. Het ingebouwde initiatief wordt automatisch toegewezen aan alle abonnementen die bij Security Center zijn geregistreerd (gratis of Standard-categorie). Het ingebouwde initiatief bevat alleen controlebeleid.


### <a name="management-groups"></a>Beheergroepen
Als uw organisatie veel abonnementen heeft, moet u de toegang, beleidsregels en naleving voor deze abonnementen op een efficiënte manier kunnen beheren. Azure Beheergroepen biedt een scopeniveau boven abonnementen. U organiseert abonnementen in containers, zogenaamde 'beheergroepen', en past uw governance-beleid op de beheergroepen toe. Alle abonnementen in een beheergroep nemen automatisch het beleid over dat op de beheergroep is toegepast. Elke directory krijgt één beheergroep op het hoogste niveau, de 'hoofdbeheergroep'. Deze hoofdbeheergroep is zo in de hiërarchie ingebouwd dat alle beheergroepen en abonnementen hierin zijn opgevouwen. Met deze hoofdbeheergroep kunt u algemene beleidsregels en RBAC-toewijzingen op directoryniveau toepassen. Als u beheer groepen wilt instellen voor gebruik met Azure Security Center, volgt u de instructies in de [volledige Tenant zichtbaarheid voor Azure Security Center](security-center-management-groups.md).

> [!NOTE]
> Het is belangrijk dat u de hiërarchie van beheergroepen en abonnementen begrijpt. Zie [Organize your resources with Azure Management Groups](../governance/management-groups/index.md#root-management-group-for-each-directory) (Resources organiseren met Azure Beheergroepen) voor meer informatie over beheergroepen, hoofdbeheer en toegang tot beheergroepen.
>

## <a name="how-security-policies-work"></a>Hoe beveiligingsbeleid werkt
In Security Center wordt voor elk van uw Azure-abonnementen automatisch een standaardbeveiligingsbeleid gemaakt. U kunt het beleid in Azure Policy bewerken om het volgende te doen:
- Nieuwe beleidsdefinities maken.
- Beleid toepassen binnen beheergroepen en abonnementen; deze kunnen de hele organisatie of een zakelijke eenheid binnen de organisatie vertegenwoordigen.
- Beleidsnaleving bewaken.

Lees [Beleid maken en beheren om naleving af te dwingen](../governance/policy/tutorials/create-and-manage.md) voor meer informatie over Azure Policy.

De Azure-beleid bestaat uit de volgende onderdelen:

- Een **beleid** is een regel.
- Een **initiatief** is een verzameling beleids regels.
- Een **toewijzing** is de toepassing van een initiatief of beleid op een specifiek bereik (beheer groep, abonnement of resource groep).

## <a name="view-security-policies"></a>Beveiligingsbeleid bekijken

Ga als volgt te werk als u uw beveiligingsbeleidsregels wilt weergeven in Security Center:

1. Selecteer in het **Security Center** -dash board **beveiligings beleid**.

    ![Het deelvenster Beleidsbeheer](./media/security-center-policies/security-center-policy-mgt.png)

   In het scherm **beleids beheer** ziet u het aantal beheer groepen, abonnementen en werk ruimten, evenals de structuur van uw beheer groep.

   > [!NOTE]
   > Het Security Center dash board kan een groter aantal abonnementen onder de **abonnements dekking** tonen dan het aantal abonnementen dat wordt weer gegeven onder **beleids beheer**. Abonnementsdekking toont het aantal Standaard-, gratis en 'Niet gedekt' abonnementen. Voor niet-gedekte abonnementen is Security Center niet ingeschakeld en worden niet weer gegeven onder **beleids beheer**.
   >

2. Selecteer het abonnement of de beheer groep waarvan u het beleid wilt weer geven.

   - Het scherm **beveiligings beleid** toont de actie die wordt uitgevoerd door het beleid dat is toegewezen aan het abonnement of de beheer groep die u hebt geselecteerd.
   - Gebruik bovenaan de koppelingen die worden weer gegeven voor het openen van elke beleids **toewijzing** die van toepassing is op het abonnement of de beheer groep. U kunt de koppelingen gebruiken om toegang te krijgen tot de toewijzing en het beleid te bewerken of uit te scha kelen. Als u bijvoorbeeld ziet dat een bepaalde beleids toewijzing de eindpunt beveiliging daad werkelijk weigert, kunt u de koppeling gebruiken om het beleid te openen en te bewerken of uit te scha kelen.
   - In de lijst met beleids regels kunt u de juiste toepassing van het beleid voor uw abonnement of beheer groep bekijken. Dit betekent dat de instellingen van elk beleid dat van toepassing is op het bereik, in aanmerking worden genomen en dat u het cumulatieve resultaat van de actie die door het beleid wordt toegepast. Als het beleid bijvoorbeeld in één toewijzing is uitgeschakeld, maar in een ander item is ingesteld op AuditIfNotExist, is het cumulatieve effect van toepassing op AuditIfNotExist. Het meer actieve effect heeft altijd voor rang.
   - Het effect van het beleid kan zijn: Append, audit, AuditIfNotExists, deny, DeployIfNotExists, disabled. Zie [beleids effecten begrijpen](../governance/policy/concepts/effects.md)voor meer informatie over hoe effecten worden toegepast.

   ![scherm beleid](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> Wanneer u toegewezen beleids regels bekijkt, kunt u meerdere toewijzingen zien en kunt u zien hoe elke toewijzing zelf is geconfigureerd.

## <a name="edit-security-policies"></a>Beveiligingsbeleid bewerken
U kunt het standaardbeveiligingsbeleid voor elk van uw Azure-abonnementen en beheergroepen bewerken in [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Als u een beveiligings beleid wilt wijzigen, moet u een eigenaar of beveiligings beheerder van het abonnement of de bovenliggende beheer groep zijn.

Zie en [beleids regels maken en beheren om naleving af te dwingen](../governance/policy/tutorials/create-and-manage.md)voor instructies over het bewerken van een beveiligings beleid in azure Policy.

U kunt beveiligings beleid via de Azure Policy Portal bewerken via REST API of met behulp van Windows Power shell. Het volgende voor beeld bevat instructies voor het bewerken met behulp van REST API.


## <a name="disable-security-policies"></a>Beveiligings beleid uitschakelen
Als het standaard beveiligings beleid een aanbeveling genereert die niet relevant is voor uw omgeving, kunt u deze stoppen door de beleids definitie die de aanbeveling verzendt, uit te scha kelen.
Zie [beveiligings aanbevelingen beheren](security-center-recommendations.md)voor meer informatie over aanbevelingen.

1. Klik in het Security Center, in de sectie **beleids & naleving** , op **beveiligings beleid**.

   ![beleids beheer](./media/tutorial-security-policy/policy-management.png)

2. Klik op het abonnement of de beheer groep waarvoor u de aanbeveling wilt uitschakelen.

   > [!Note]
   > Houd er rekening mee dat een beheer groep het beleid toepast op de abonnementen. Als u dus het beleid van een abonnement uitschakelt en het abonnement behoort tot een beheer groep die nog steeds hetzelfde beleid gebruikt, blijft de aanbevelingen voor het beleid worden ontvangen. Het beleid wordt nog steeds toegepast vanuit het beheer niveau en de aanbevelingen worden nog steeds gegenereerd.

1. Klik op het toegewezen beleid.

   ![beleid uitschakelen](./media/tutorial-security-policy/security-policy.png)

1. Zoek in de sectie **para meters** naar het beleid dat de aanbeveling aanroept die u wilt uitschakelen, en selecteer in de vervolg keuzelijst de optie **uitgeschakeld**

   ![beleid uitschakelen](./media/tutorial-security-policy/disable-policy.png)
1. Klik op **Opslaan**.
   > [!Note]
   > Het kan tot 12 uur duren voordat de wijzigingen van het beleid uitschakelen zijn doorgevoerd.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Een beveiligings beleid configureren met behulp van de REST API

Als onderdeel van de systeem eigen integratie met Azure Policy, Azure Security Center kunt u de REST API van Azure Policy benutten om beleids toewijzingen te maken. De volgende instructies begeleiden u bij het maken van beleids toewijzingen, evenals het aanpassen van bestaande toewijzingen. 

Belang rijke concepten in Azure Policy: 

- Een **beleids definitie** is een regel 

- Een **initiatief** is een verzameling beleids definities (regels) 

- Een **toewijzing** is een toepassing van een initiatief of een beleid voor een specifiek bereik (beheer groep, abonnement, enz.) 

Security Center heeft een ingebouwd initiatief dat alle beveiligings beleidsregels omvat. Als u het beleid van Security Center op uw Azure-resources wilt beoordelen, moet u een toewijzing maken voor de beheer groep of het abonnement dat u wilt beoordelen.

Het ingebouwde initiatief heeft standaard alle beleids regels van Security Center ingeschakeld. U kunt ervoor kiezen om bepaalde beleids regels uit het ingebouwde initiatief uit te scha kelen, bijvoorbeeld om alle beleids regels van Security Center toe te passen, met uitzonde ring van **Web Application firewall**, door de waarde van de effect parameter van het beleid te wijzigen in **uitgeschakeld**. 

### <a name="api-examples"></a>API-voor beelden

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

### Naslag informatie over beleids namen<a name="policy-names"></a>

|Beleids naam in Security Center|Beleids naam die wordt weer gegeven in Azure Policy |Parameter naam van beleids effect|
|----|----|----|
|SQL-versleuteling |Niet-versleutelde SQL database in Azure Security Center bewaken |sqlEncryptionMonitoringEffect| 
|Controleren voor SQL |Ongecontroleerde SQL database in Azure Security Center bewaken |sqlAuditingMonitoringEffect|
|Systeemupdates |Ontbrekende systeem updates bewaken in Azure Security Center |systemUpdatesMonitoringEffect|
|Storage-versleuteling |Ontbrekende BLOB-versleuteling voor opslag accounts controleren |storageEncryptionMonitoringEffect|
|JIT-netwerk toegang |Bewaak mogelijke netwerk JIT-toegang (just-in-time) in Azure Security Center |jitNetworkAccessMonitoringEffect |
|Besturingselementen voor adaptieve toepassingen |Mogelijke app-white list in Azure Security Center bewaken |adaptiveApplicationControlsMonitoringEffect|
|Netwerkbeveiligingsgroepen |Bewaak strikte netwerk toegang in Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Beveiligingsconfiguraties |Beveiligings problemen met het besturings systeem in Azure Security Center bewaken |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |Ontbrekende Endpoint Protection in Azure Security Center controleren |endpointProtectionMonitoringEffect |
|Schijfversleuteling |Niet-versleutelde VM-schijven in Azure Security Center bewaken |diskEncryptionMonitoringEffect|
|Evaluatie van beveiligingsproblemen |VM-beveiligings problemen in Azure Security Center bewaken |vulnerabilityAssessmentMonitoringEffect|
|Web Application Firewall |Niet-beveiligde webtoepassing in Azure Security Center bewaken |webApplicationFirewallMonitoringEffect |
|Next Generation Firewall |Niet-beveiligde netwerk eindpunten bewaken in Azure Security Center| |


### <a name="who-can-edit-security-policies"></a>Wie kan beveiligings beleid bewerken?
Security Center gebruikt op rollen gebaseerd Access Control (RBAC), dat ingebouwde rollen biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure. Wanneer gebruikers Security Center openen, zien ze alleen informatie die betrekking heeft op de resources waartoe ze toegang hebben. Dit betekent dat gebruikers de rol van eigenaar, bijdrager of lezer zijn toegewezen aan het abonnement of de resource groep waartoe een resource behoort. Naast deze rollen zijn er twee specifieke Security Center-rollen:

- Beveiligings lezer: Beschikken over weergave rechten voor Security Center, waaronder aanbevelingen, waarschuwingen, beleid en status, maar ze kunnen geen wijzigingen aanbrengen.
- Beveiligings beheerder: Hebben dezelfde weergave rechten als de beveiligings lezer, en ze kunnen ook het beveiligings beleid bijwerken en aanbevelingen en waarschuwingen negeren.



## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe u beveiligings beleid in Azure Policy kunt bewerken. Zie de volgende artikelen voor meer informatie over Security Center:

* [Azure Security Center plannings-en bedienings handleiding](security-center-planning-and-operations-guide.md): Meer informatie over het plannen en begrijpen van de ontwerp overwegingen over Azure Security Center.
* [Beveiligings status controleren in azure Security Center](security-center-monitoring.md): Meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md): Informatie over het beheren van en reageren op beveiligingswaarschuwingen.
* [Partner oplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): Meer informatie over het bewaken van de status van uw partneroplossingen.
* [Krijg inzicht in de hele Tenant voor Azure Security Center](security-center-management-groups.md): Meer informatie over het instellen van beheer groepen voor Azure Security Center.
* [Azure Security Center Veelgestelde vragen](security-center-faq.md): Krijg antwoorden op veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligings blog](https://blogs.msdn.com/b/azuresecurity/): Raadpleeg de blogberichten over beveiliging en naleving in Azure.

Zie [Wat is Azure Policy?](../governance/policy/overview.md) voor meer informatie over Azure Policy.
