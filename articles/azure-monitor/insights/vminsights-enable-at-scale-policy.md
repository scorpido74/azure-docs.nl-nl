---
title: Azure-monitor voor VM's inschakelen met Azure-beleid
description: In dit artikel wordt beschreven hoe u Azure Monitor voor VM's inschakelt voor meerdere Azure-virtuele machines of virtuele machineschaalsets met azure-beleid.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 73c18d45136eea90ad29dc1bd40c4539dddc0ee6
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767254"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Azure-monitor voor VM's inschakelen met Azure-beleid

In dit artikel wordt uitgelegd hoe u Azure Monitor voor VM's inschakelt voor virtuele Azure-machines of virtuele machineschaalsets met Azure Policy. Aan het einde van dit proces hebt u de loganalyse- en afhankelijkheidsagents en de geïdentificeerde virtuele machines die niet voldoen, geconfigureerd.

Als u Azure Monitor voor VM's voor al uw Azure-virtuele machines of virtuele machineschaalsets wilt ontdekken, beheren en inschakelen, u Azure Policy of Azure PowerShell gebruiken. Azure Policy is de methode die we aanbevelen omdat u beleidsdefinities beheren om uw abonnementen effectief te beheren om consistente naleving en automatische inschakelen van nieuw ingerichte VM's te garanderen. Deze beleidsdefinities:

* Implementeer de agent Log Analytics en de afhankelijkheidsagent.
* Rapport over de nalevingsresultaten.
* Herstel voor niet-conforme VM's.

Zie [Azure Monitor voor VM's inschakelen met Azure PowerShell- of Azure Resource Manager-sjablonen](vminsights-enable-at-scale-powershell.md)als u deze taken wilt uitvoeren met Azure PowerShell- of Azure Resource Manager-sjablonen.

## <a name="prerequisites"></a>Vereisten
Voordat u Beleid gebruikt om uw Azure VM's en virtuele machineschaalsets aan boord te nemen van Azure Monitoring voor VM's, moet u de VMInsights-oplossing inschakelen op de werkruimte die u gebruikt om uw bewakingsgegevens op te slaan. Deze taak kan worden voltooid vanaf de pagina **Aan de slag** in Azure Monitor op het tabblad Andere **onboarding-opties.**  Selecteer **Een werkruimte configureren,** waarmee u de werkruimte wilt selecteren die moet worden geconfigureerd.

![Werkruimte configureren](media/vminsights-enable-at-scale-policy/configure-workspace.png)

U uw werkruimte ook configureren door **Het gebruiksbeleid inschakelen** te kiezen en vervolgens de **werkbalkknop Werkruimte configureren** te selecteren.  Hiermee wordt de VMInsights-oplossing geïnstalleerd op de geselecteerde werkruimte, waarmee de werkruimte de bewakingsgegevens kan opslaan die door de VM's worden verzonden en virtuele machineschaalsets die u met Beleid inschakelen. 

![Inschakelen met beleid](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>Overzicht van de functie Beleidsdekking beheren

Azure Monitor for VMs Beleidsdekking vereenvoudigt het ontdekken, beheren en op schaal inschakelen van het initiatief **Azure Monitor for VM's** inschakelen, inclusief de eerder genoemde beleidsdefinities. Als u toegang wilt krijgen tot deze functie, selecteert u **Andere onboarding-opties** op het tabblad **Aan de slag** in Azure Monitor voor VM's. Selecteer **Beleidsdekking beheren** om de pagina **Beleidsdekking azure-monitor voor VM's te openen.**

![Azure-monitor vanaf tabblad VM's Aan de slag](./media/vminsights-enable-at-scale-policy/get-started-page.png)

Vanaf hier u de dekking voor het initiatief controleren en beheren in uw beheergroepen en abonnementen. U begrijpen hoeveel VM's er bestaan in elk van de beheergroepen en abonnementen en hun nalevingsstatus.

![Pagina Azure Monitor voor VM's Beheerbeleid](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Deze informatie is handig om u te helpen bij het plannen en uitvoeren van uw governancescenario voor Azure Monitor voor VM's vanaf één centrale locatie. Hoewel Azure Policy een nalevingsweergave biedt wanneer een beleid of een initiatief is toegewezen aan een bereik, u met deze nieuwe pagina ontdekken waar het beleid of initiatief niet is toegewezen en deze toewijzen. Alle acties zoals toewijzen, bekijken en bewerken, verwijzen rechtstreeks om naar Azure Policy. De pagina **Azure Monitor for VMs-beleidsdekking** is een uitgebreide en geïntegreerde ervaring voor alleen het initiatief **Azure-monitor inschakelen voor VM's.**

Vanaf deze pagina u ook uw Log Analytics-werkruimte configureren voor Azure Monitor voor VM's, die:

- Installeert de Service Map-oplossing.
- Hiermee worden de prestatiemeteritems van het besturingssysteem gebruikt die worden gebruikt door de prestatiegrafieken, werkmappen en uw aangepaste logboekquery's en waarschuwingen.

![Azure Monitor voor VM's configureren werkruimte](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Deze optie heeft geen betrekking op beleidsacties. Het is beschikbaar om een eenvoudige manier te bieden om te voldoen aan de [vereisten](vminsights-enable-overview.md) die nodig zijn voor het inschakelen van Azure Monitor voor VM's.  

### <a name="what-information-is-available-on-this-page"></a>Welke informatie is beschikbaar op deze pagina?

In de volgende tabel vindt u een overzicht van de informatie die wordt weergegeven op de pagina beleidsdekking en hoe deze moet worden geïnterpreteerd.

| Functie | Beschrijving | 
|----------|-------------| 
| **Bereik** | Beheergroep en abonnementen waartoe u toegang hebt of hebt overgenomen met de mogelijkheid om in te zoomen via de hiërarchie van de beheergroep.|
| **Rol** | Uw rol in het bereik, dat mogelijk lezer, eigenaar of bijdrager is. In sommige gevallen kan het leeg lijken om aan te geven dat u mogelijk toegang hebt tot het abonnement, maar niet tot de beheergroep waartoe het behoort. De informatie in andere kolommen is afhankelijk van uw rol. De rol is essentieel bij het bepalen welke gegevens u zien en acties die u uitvoeren op het gebied van het toewijzen van beleid of initiatieven (eigenaar), het bewerken ervan of het bekijken van naleving. |
| **Totaal VM's** | Aantal VM's onder dat bereik. Voor een beheergroep is dit een som van VM's die zijn genest onder de abonnementen of onderliggende beheergroep. |
| **Toewijzingsdekking** | Percentage VM's dat onder het beleid of initiatief valt. |
| **Toewijzingsstatus** | Informatie over de status van uw beleids- of initiatiefopdracht. |
| **Compatibele VM's** | Aantal VM's dat voldoet aan het beleid of initiatief. Voor het initiatief **Azure Monitor voor VM's inschakelen,** is dit het aantal VM's dat zowel de Log Analytics-agent als de afhankelijkheidsagent heeft. In sommige gevallen kan het leeg lijken vanwege geen toewijzing, geen VM's of niet genoeg machtigingen. Informatie wordt verstrekt onder **nalevingsstaat**. |
| **Naleving** | Het algemene nalevingsnummer is de som van verschillende resources die voldoen gedeeld door de som van alle afzonderlijke resources. |
| **Nalevingsstatus** | Informatie over de nalevingsstatus voor uw beleids- of initiatieftoewijzing.|

Wanneer u het beleid of initiatief toewijst, kan het bereik dat in de toewijzing is geselecteerd, het bereik zijn dat wordt weergegeven of een subset ervan. U hebt bijvoorbeeld een toewijzing voor een abonnement (beleidsbereik) gemaakt en geen beheergroep (dekkingsbereik). In dit geval geeft de waarde van **toewijzingsdekking** de VM's aan in het beleid of initiatiefbereik gedeeld door de VM's in dekkingsbereik. In een ander geval hebt u mogelijk bepaalde VM's, resourcegroepen of een abonnement uitgesloten van het beleidsbereik. Als de waarde leeg is, geeft dit aan dat het beleid of het initiatief niet bestaat of dat u geen toestemming hebt. Informatie wordt verstrekt onder **toewijzingsstatus**.

## <a name="enable-by-using-azure-policy"></a>Inschakelen met Azure Policy

Ga als volgt te werk om Azure Monitor voor VM's in te schakelen met Azure Policy in uw tenant:

- Wijs het initiatief toe aan een scope: beheergroep, abonnement of resourcegroep.
- Complianceresultaten controleren en herstellen.

Zie Het overzicht van Azure [Policy](../../governance/policy/overview.md#assignments) voor meer informatie over het toewijzen van Azure Policy en bekijk het [overzicht van beheergroepen](../../governance/management-groups/overview.md) voordat u verdergaat.

### <a name="policies-for-azure-vms"></a>Beleidsregels voor Azure VM's

De beleidsdefinities voor een Azure VM worden weergegeven in de volgende tabel.

|Naam |Beschrijving |Type |
|-----|------------|-----|
|Azure-monitor voor VM's inschakelen |Azure Monitor inschakelen voor de virtuele machines in de opgegeven scope (beheergroep, abonnement of resourcegroep). Neemt de werkruimte Log Analytics als parameter. |Initiatief |
|Implementatie van de controleafhankelijkheidsagent – VM-afbeelding (OS) niet-vermeld |Rapporteert VM's als niet-compatibel als de VM-afbeelding (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|Implementatie van auditloganalyses - VM-afbeelding (OS) niet vermeld |Rapporteert VM's als niet-compatibel als de VM-afbeelding (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|Afhankelijkheidsagent implementeren voor Linux VM's |De afhankelijkheidsagent voor Linux-VM's implementeren als de VM-afbeelding (OS) is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|Afhankelijkheidsagent voor Windows VM's implementeren |De afhankelijkheidsagent voor Windows VM's implementeren als de VM-afbeelding (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|Log Analytics-agent implementeren voor Linux VM's |De systeemstoer loganalyse voor Linux-VM's implementeren als de VM-afbeelding (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|Log Analytics-agent voor Windows VM's implementeren |De agent Log Analytics voor Windows VM's implementeren als de VM-afbeelding (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Beleidsregels voor azure-seten voor virtuele machineschalen

De beleidsdefinities voor een Azure-schaalset voor virtuele machines worden weergegeven in de volgende tabel.

|Naam |Beschrijving |Type |
|-----|------------|-----|
|Azure Monitor inschakelen voor virtuele machineschaalsets |Azure Monitor inschakelen voor de virtuele machineschaalsets in de opgegeven scope (beheergroep, abonnement of resourcegroep). Neemt de werkruimte Log Analytics als parameter. Opmerking: Als uw upgradebeleid voor schaalinstellingen is ingesteld op Handmatig, past u de extensie toe op alle VM's in de set door er een upgrade op aan te roepen. In de CLI, `az vmss update-instances`dit is . |Initiatief |
|Implementatie van afhankelijkheidsagent controleren in virtuele machineschaalsets – VM-afbeelding (OS) niet-vermeld |Rapporteert de virtuele machineschaal die is ingesteld als niet-compatibel als de VM-afbeelding (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|Implementatie van audit log analytics-agent in virtuele machineschaalsets – VM-afbeelding (OS) niet-vermeld |Rapporteert de virtuele machineschaal die is ingesteld als niet-compatibel als de VM-afbeelding (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|Afhankelijkheidsagent implementeren voor virtuele machineschaalsets van Linux |De afhankelijkheidsagent voor virtuele machineschaal van Linux implementeren als de VM-afbeelding (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|Afhankelijkheidsagent voor Windows-waardenvoor virtuele machineschalen implementeren |De afhankelijkheidsagent voor Windows-virtuele machineschaal sets implementeren als de VM-afbeelding (OS) is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|Log Analytics-agent implementeren voor virtuele machineschaalsets van Linux |Implementeer de agent Logboekanalyse voor linux virtuele machineschaalsets als de VM Image (OS) is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|Log Analytics-agent voor virtuele machineschaalsets van Windows implementeren |Implementeer de agent Logboekanalyse voor Windows met virtuele machineschaal als de VM-afbeelding (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |

Zelfstandig beleid (niet inbegrepen bij het initiatief) wordt hier beschreven:

|Naam |Beschrijving |Type |
|-----|------------|-----|
|Audit Log Analytics-werkruimte voor VM – Mismatch rapporteren |Rapporteer VM's als niet-compatibel als ze niet worden aangemeld bij de werkruimte Log Analytics die is opgegeven in de beleids- of initiatieftoewijzing. |Beleid |

### <a name="assign-the-azure-monitor-initiative"></a>Het Azure Monitor-initiatief toewijzen

Voer de volgende stappen uit om de beleidstoewijzing te maken op de pagina **Beleidsdekking azure-monitor voor VMs.** Zie [Een beleidstoewijzing maken vanuit de Azure-portal](../../governance/policy/assign-policy-portal.md)als u wilt begrijpen hoe u deze stappen uitvoeren.

Wanneer u het beleid of initiatief toewijst, kan het bereik dat in de toewijzing is geselecteerd, het bereik zijn dat hier wordt vermeld of een subset ervan. U hebt bijvoorbeeld een toewijzing voor het abonnement (beleidsbereik) gemaakt en niet de beheergroep (dekkingsbereik). In dit geval geeft het dekkingspercentage de VM's aan in de beleids- of initiatiefscope die door de VM's in het dekkingsbereik wordt gedeeld. In een ander geval hebt u mogelijk bepaalde VM's, resourcegroepen of een abonnement uitgesloten van het beleidsbereik. Als het leeg is, geeft dit aan dat het beleid of initiatief niet bestaat of dat u geen machtigingen hebt. Informatie wordt verstrekt onder **toewijzingsstatus**.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer In de Azure-portal de optie **Monitor**. 

3. Kies **Virtuele machines** in de sectie **Insights.**
 
4. Selecteer het tabblad **Aan de slag.** Selecteer op de pagina **Beleidsdekking beheren**.

5. Selecteer een beheergroep of een abonnement in de tabel. Selecteer **Bereik** door de ellips (...). In het voorbeeld beperkt een scope de beleidstoewijzing tot een groepering van virtuele machines voor handhaving.

6. Op de **toewijzingspagina azure-beleid** wordt deze vooraf ingevuld met het initiatief **Azure Monitor inschakelen voor VM's.** 
    Het vak **Toewijzingsnaam** wordt automatisch gevuld met de naam van het initiatief, maar u deze wijzigen. U ook een optionele beschrijving toevoegen. Het vak **Toegewezen aan** wordt automatisch ingevuld op basis van wie is ingelogd. Deze waarde is optioneel.

7. (Optioneel) Als u een of meer bronnen uit het bereik wilt verwijderen, selecteert u **Uitsluitingen**.

8. Selecteer een werkruimte in de vervolgkeuzelijst **Logboekanalyse werkruimte** voor het ondersteunde gebied.

   > [!NOTE]
   > Als de werkruimte buiten het bereik van de toewijzing valt, verleent u *machtigingen voor log-analytics-bijdragen* aan de hoofd-id van de beleidstoewijzing. Als u dit niet doet, ziet u `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` mogelijk een implementatiefout zoals Toegang verlenen, controleert [u hoe u de beheerde identiteit handmatig configureert.](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)
   > 
   >  Het selectievakje **Beheerde identiteit** is ingeschakeld omdat het toegewezen initiatief een beleid bevat met het effect *DeployIfNotExists.*
    
9. Selecteer in de vervolgkeuzelijst **Identiteit beheren** de juiste regio.

10. Selecteer **Toewijzen**.

Nadat u de toewijzing hebt gemaakt, wordt in de pagina **Azure Monitor for VMs-beleidsdekking** **toewijzingsdekking,** **toewijzingsstatus,** **compliant VM's**en **nalevingsstatus** bijgewerkt om de wijzigingen weer te geven. 

De volgende matrix brengt elke mogelijke nalevingsstatus voor het initiatief in kaart.  

| Nalevingsstatus | Beschrijving | 
|------------------|-------------|
| **Compatibel** | Alle VM's in het bereik hebben de log-analyse- en afhankelijkheidsagents die op hen zijn geïmplementeerd.|
| **Niet compatibel** | Niet alle VM's in het bereik hebben de log-analyse- en afhankelijkheidsagents die op hen zijn geïmplementeerd en moeten mogelijk worden hersteld.|
| **Niet gestart** | Er is een nieuwe opdracht toegevoegd. |
| **Vergrendelen** | U hebt niet voldoende bevoegdheden voor de beheergroep. <sup>1.</sup> | 
| **Leeg** | Er is geen beleid toegewezen. | 

<sup>1</sup> Als u geen toegang hebt tot de beheergroep, vraagt u een eigenaar om toegang te verlenen. Of bekijk compliance en beheer opdrachten via de onderliggende beheergroepen of -abonnementen. 

In de volgende tabel wordt elke mogelijke toewijzingsstatus voor het initiatief in kaart gebracht.

| Toewijzingsstatus | Beschrijving | 
|------------------|-------------|
| **Geslaagd** | Alle VM's in het bereik hebben de log-analyse- en afhankelijkheidsagents die op hen zijn geïmplementeerd.|
| **Waarschuwing** | Het abonnement is niet onder een beheergroep.|
| **Niet gestart** | Er is een nieuwe opdracht toegevoegd. |
| **Vergrendelen** | U hebt niet voldoende bevoegdheden voor de beheergroep. <sup>1.</sup> | 
| **Leeg** | Er bestaan geen VM's of er is geen beleid toegewezen. | 
| **Actie** | Wijs een beleid toe of bewerk een toewijzing. | 

<sup>1</sup> Als u geen toegang hebt tot de beheergroep, vraagt u een eigenaar om toegang te verlenen. Of bekijk compliance en beheer opdrachten via de onderliggende beheergroepen of -abonnementen.

## <a name="review-and-remediate-the-compliance-results"></a>De nalevingsresultaten controleren en herstellen

Het volgende voorbeeld is voor een Azure VM, maar het is ook van toepassing op virtuele machineschaalsets. Zie Resultaten van niet-naleving identificeren voor meer informatie over het beoordelen [van nalevingsresultaten.](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources) Selecteer op de pagina **Beleidsdekking azure monitor voor VMs** een beheergroep of een abonnement in de tabel. Selecteer **Naleving weergeven** door de ellips (...).   

![Naleving van het beleid voor Azure VM's](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

Op basis van de resultaten van het beleid dat bij het initiatief is opgenomen, worden VM's gerapporteerd als niet-conform in de volgende scenario's:

* Log Analytics-agent of afhankelijkheidsagent wordt niet geïmplementeerd.  
    Dit scenario is typisch voor een bereik met bestaande VM's. Implementeer de vereiste agents om dit te beperken door [hersteltaken](../../governance/policy/how-to/remediate-resources.md) te maken op een niet-compatibel beleid.  
    - Afhankelijkheidsagent implementeren voor Linux VM's
    - Afhankelijkheidsagent voor Windows VM's implementeren
    - Log Analytics-agent implementeren voor Linux VM's
    - Log Analytics-agent voor Windows VM's implementeren

* VM-afbeelding (OS) wordt niet geïdentificeerd in de beleidsdefinitie.  
    De criteria van het implementatiebeleid omvatten alleen VM's die zijn geïmplementeerd op basis van bekende Azure VM-afbeeldingen. Controleer de documentatie om te zien of het VM-besturingssysteem wordt ondersteund. Als deze niet wordt ondersteund, dupliceert u het implementatiebeleid en werkt u het bij om de afbeelding compatibel te maken.  
    - Implementatie van de controleafhankelijkheidsagent – VM-afbeelding (OS) niet-vermeld
    - Implementatie van auditloganalyses - VM-afbeelding (OS) niet vermeld

* VM's loggen niet in bij de opgegeven Log Analytics-werkruimte.  
    Het is mogelijk dat sommige VM's in het initiatiefbereik zich aanmelden bij een Log Analytics-werkruimte, anders dan de werkruimte die is opgegeven in de beleidstoewijzing. Dit beleid is een hulpmiddel om te bepalen welke VM's worden gemeld aan een niet-compatibele werkruimte.  
    - Audit Log Analytics-werkruimte voor VM – Mismatch rapporteren

## <a name="edit-an-initiative-assignment"></a>Een initiatiefopdracht bewerken

U het op elk moment nadat u een initiatief aan een beheergroep of abonnement hebt toegewezen, bewerken om de volgende eigenschappen te wijzigen:

- Toewijzingsnaam
- Beschrijving
- Toegewezen door
- Log Analytics-werkruimte
- Uitzonderingen

## <a name="next-steps"></a>Volgende stappen

Nu bewaking is ingeschakeld voor uw virtuele machines, is deze informatie beschikbaar voor analyse met Azure Monitor voor VM's. 

- Zie Azure Monitor for [VM-toewijzing weergeven](vminsights-maps.md)om gedetecteerde toepassingsafhankelijkheden weer te geven. 

- Zie [Azure VM-prestaties weergeven](vminsights-performance.md)om knelpunten en algemeen gebruik met de prestaties van uw VM te identificeren. 
