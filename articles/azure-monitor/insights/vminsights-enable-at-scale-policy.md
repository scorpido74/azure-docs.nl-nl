---
title: Azure Monitor voor VM's inschakelen met Azure Policy
description: In dit artikel wordt beschreven hoe u Azure Monitor voor VM's voor meerdere virtuele machines van Azure of virtuele-machine schaal sets inschakelt met behulp van Azure Policy.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 7d3c4e0f4bd34f996bb39426af39a692a6f79c5c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507174"
---
# <a name="enable-azure-monitor-for-vms-by-using-azure-policy"></a>Azure Monitor voor VM's inschakelen met Azure Policy

In dit artikel wordt uitgelegd hoe u Azure Monitor voor VM's voor virtuele Azure-machines, virtuele-machine schaal sets en Azure-Arc-machines inschakelt met behulp van Azure Policy. Aan het einde van dit proces hebt u de configuratie ingeschakeld van de Log Analytics-en Dependency-agents en de geïdentificeerde virtuele machines die niet voldoen aan het beleid.

Als u Azure Monitor voor VM's wilt detecteren, beheren en inschakelen voor alle virtuele machines of virtuele-machine schaal sets van Azure, kunt u Azure Policy of Azure PowerShell gebruiken. Azure Policy is de methode die wordt aanbevolen omdat u beleids definities kunt beheren om uw abonnementen effectief te regelen, zodat er consistente naleving en automatische inrichtingen kunnen worden ingeschakeld. Deze beleids definities:

* Implementeer de Log Analytics agent en de afhankelijkheids agent.
* Rapport over de nalevings resultaten.
* Herstellen voor niet-compatibele Vm's.

Als u deze taken wilt uitvoeren met Azure PowerShell of een Azure Resource Manager sjabloon, raadpleegt u [Azure monitor voor VM's inschakelen met Azure PowerShell of Azure Resource Manager sjablonen](vminsights-enable-at-scale-powershell.md).

## <a name="prerequisites"></a>Vereisten
Voorafgaand aan het gebruik van het beleid om uw Azure Vm's en virtuele-machine schaal sets bij te werken naar Azure monitoring voor Vm's, moet u de VMInsights-oplossing inschakelen in de werk ruimte die u gaat gebruiken om uw bewakings gegevens op te slaan. Deze taak kan worden uitgevoerd vanaf de pagina **aan de slag** in azure monitor op het tabblad **andere opties voor onboarding** .  Selecteer **een werk ruimte configureren**, waarin u wordt gevraagd de werk ruimte te selecteren die u wilt configureren.

![Werkruimte configureren](media/vminsights-enable-at-scale-policy/configure-workspace.png)

U kunt de werk ruimte ook configureren door **beleid inschakelen gebruiken** te kiezen en vervolgens op de knop werk **ruimte werkruimte configureren** te klikken.  Hiermee installeert u de VMInsights-oplossing in de geselecteerde werk ruimte, waarmee de werk ruimte de bewakings gegevens opslaat die worden verzonden door de Vm's en virtuele-machine schaal sets die u inschakelt met behulp van beleid. 

![Inschakelen via beleid](media/vminsights-enable-at-scale-policy/enable-using-policy.png)

## <a name="manage-policy-coverage-feature-overview"></a>Overzicht van functie voor beleids dekking beheren

Azure Monitor voor VM's-beleids dekking vereenvoudigt het detecteren, beheren en inschakelen van een schaal voor het inschakelen van de **Azure monitor voor VM's** -initiatief, inclusief de eerder genoemde beleids definities. U kunt deze functie gebruiken door **andere opties voor onboarding** te selecteren op het tabblad **aan de slag** in azure monitor voor VM's. Selecteer **dekking van beleid beheren** om de pagina **dekking van Azure monitor voor VM's beleid** te openen.

![Azure Monitor van het tabblad aan de slag met Vm's](./media/vminsights-enable-at-scale-policy/get-started-page.png)

Hier kunt u de dekking voor het initiatief in uw beheer groepen en abonnementen controleren en beheren. U kunt begrijpen hoeveel Vm's er bestaan in elk van de beheer groepen en abonnementen en de nalevings status ervan.

![Pagina beleid Azure Monitor voor VM's beheren](media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Deze informatie is nuttig om u te helpen bij het plannen en uitvoeren van uw governance scenario voor Azure Monitor voor VM's vanaf één centrale locatie. Hoewel Azure Policy een weer gave voor naleving biedt wanneer een beleid of initiatief wordt toegewezen aan een bereik, kunt u met deze nieuwe pagina ontdekken waar het beleid of initiatief niet is toegewezen en dit op locatie toewijzen. Alle acties, zoals toewijzen, bekijken en bewerken, worden rechtstreeks omgeleid naar Azure Policy. De pagina **dekking van Azure monitor voor VM's beleid** is een uitgebreide en geïntegreerde ervaring voor het **inschakelen van Azure monitor voor VM's**.

Op deze pagina kunt u ook uw Log Analytics-werk ruimte configureren voor Azure Monitor voor VM's, waarmee de *VMInsights* -oplossing wordt geïnstalleerd.

![Werk ruimte Azure Monitor voor VM's configureren](media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Deze optie is niet gerelateerd aan beleids acties. Het is beschikbaar om een eenvoudige manier te bieden om te voldoen aan de [vereisten](vminsights-enable-overview.md) die nodig zijn om Azure monitor voor VM's in te scha kelen.  

### <a name="what-information-is-available-on-this-page"></a>Welke informatie is beschikbaar op deze pagina?

De volgende tabel bevat een overzicht van de informatie die wordt weer gegeven op de pagina beleids dekking en hoe u deze kunt interpreteren.

| Functie | Beschrijving | 
|----------|-------------| 
| **Bereik** | Beheer groep en abonnementen waarvoor u toegang hebt of die u hebt overgenomen, met de mogelijkheid om in te zoomen op de hiërarchie van de beheer groep.|
| **Role** | Uw rol aan het bereik, dat lezers, eigenaar of Inzender kan zijn. In sommige gevallen kan het lijken leeg te zijn om aan te geven dat u toegang tot het abonnement hebt, maar niet tot de beheer groep waarvan het deel uitmaakt. De gegevens in andere kolommen variëren, afhankelijk van uw rol. De rol is belang rijk om te bepalen welke gegevens u kunt zien en welke acties u kunt uitvoeren met betrekking tot het toewijzen van beleid of initiatieven (eigenaar), het bewerken ervan of het weer geven van naleving. |
| **Totaal aantal Vm's** | Aantal Vm's onder dat bereik. Voor een beheer groep is dit een som van Vm's die zijn genest onder de abonnementen of de onderliggende beheer groep. |
| **Toewijzings dekking** | Percentage Vm's die worden gedekt door het beleid of initiatief. |
| **Toewijzings status** | Informatie over de status van uw beleid of initiatief toewijzing. |
| **Compatibele Vm's** | Aantal virtuele machines die voldoen aan het beleid of initiatief. Voor het initiatief **Azure monitor voor VM's inschakelen**, is dit het aantal vm's met zowel log Analytics agent als een afhankelijkheids agent. In sommige gevallen kan het worden leeg weer gegeven als gevolg van geen toewijzing, geen Vm's of onvoldoende machtigingen. Informatie wordt verstrekt onder **nalevings status**. |
| **Naleving** | Het algemene compatibiliteits nummer is de som van afzonderlijke resources die compatibel zijn gedeeld door de som van alle afzonderlijke resources. |
| **Nalevingsstatus** | Informatie over de nalevings status voor uw beleid of initiatief toewijzing.|

Wanneer u het beleid of initiatief toewijst, kan het bereik dat u hebt geselecteerd in de toewijzing het bereik of een subset hiervan zijn. U hebt bijvoorbeeld een toewijzing voor een abonnement (beleids bereik) gemaakt en geen beheer groep (dekkings bereik). In dit geval geeft de waarde van **toewijzings dekking** de vm's aan in het beleid of initiatief bereik gedeeld door de vm's in het dekkings bereik. In andere gevallen hebt u mogelijk enkele Vm's, resource groepen of een abonnement uitgesloten van het beleids bereik. Als de waarde leeg is, geeft dit aan dat het beleid of initiatief niet bestaat of dat u geen toestemming hebt. Informatie wordt weer gegeven onder de **toewijzings status**.

## <a name="enable-by-using-azure-policy"></a>Inschakelen met behulp van Azure Policy

Azure Monitor voor VM's in te scha kelen met behulp van Azure Policy in uw Tenant:

- Het initiatief toewijzen aan een bereik: beheer groep, abonnement of resource groep.
- Nalevings resultaten controleren en herstellen.

Voor meer informatie over het toewijzen van Azure Policy, raadpleegt u [Azure Policy overzicht](../../governance/policy/overview.md#assignments) en raadpleegt u het [overzicht van beheer groepen](../../governance/management-groups/overview.md) voordat u doorgaat.

### <a name="policies-for-azure-vms"></a>Beleid voor virtuele Azure-machines

De beleids definities voor een virtuele machine van Azure worden weer gegeven in de volgende tabel.

|Naam |Description |Type |
|-----|------------|-----|
|Azure Monitor voor VM's inschakelen |Schakel Azure Monitor in voor de virtuele machines in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. |Initiatieven |
|Implementatie van afhankelijkheids agent controleren – VM-installatie kopie (OS) niet vermeld |Rapporteert Vm's als niet-compatibel als de VM-installatie kopie (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld |Rapporteert Vm's als niet-compatibel als de VM-installatie kopie (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|Afhankelijkheidsagent implementeren voor Linux-VM's |De afhankelijkheids agent voor Linux Vm's implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|Afhankelijkheidsagent implementeren voor Windows-VM's |Een afhankelijkheids agent voor Windows-Vm's implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|Log Analytics-agent voor Linux-VM’s implementeren |Implementeer Log Analytics-agent voor Linux-Vm's als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|Log Analytics-agent voor Windows-VM's implementeren |Log Analytics-agent voor Windows-Vm's implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |


### <a name="policies-for-hybrid-azure-arc-machines"></a>Beleid voor hybride Azure Arc-machines

De beleids definities voor Hybrid Azure Arc-computers worden weer gegeven in de volgende tabel.

|Naam |Beschrijving |Type |
|-----|------------|-----|
| [Preview-versie]: Log Analytics-agent moet worden geïnstalleerd op uw Linux Azure Arc-machines |Rapporteert hybride Azure Arc-machines als niet-compatibel voor Linux-Vm's als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
| [Preview-versie]: Log Analytics-agent moet worden geïnstalleerd op uw Windows Azure-Arc-machines |Rapporteert hybride Azure Arc-machines als niet-compatibel voor Windows-Vm's als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
| [Preview-versie]: afhankelijkheids agent implementeren op hybride Linux Azure Arc-machines |Een afhankelijkheids agent voor Linux Hybrid Azure Arc-machines implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
| [Preview-versie]: afhankelijkheids agent implementeren op hybride Windows Azure Arc-machines |Een afhankelijkheids agent voor Windows Hybrid Azure Arc-machines implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
| [Preview-versie]: Log Analytics-agent implementeren op Linux Azure Arc-machines |Implementeer Log Analytics agent voor Linux Hybrid Azure Arc machines als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
| [Preview-versie]: Log Analytics agent implementeren op Windows Azure Arc-machines |Log Analytics-agent voor Windows Hybrid Azure Arc-machines implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |


### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Beleid voor virtuele-machine schaal sets van Azure

De beleids definities voor een schaalset voor virtuele Azure-machines worden in de volgende tabel weer gegeven.

|Naam |Beschrijving |Type |
|-----|------------|-----|
|Azure Monitor inschakelen voor schaal sets voor virtuele machines |Schakel Azure Monitor in voor de virtuele-machine schaal sets in het opgegeven bereik (beheer groep, abonnement of resource groep). Hiermee wordt Log Analytics werk ruimte als para meter gebruikt. Opmerking: als het upgrade beleid van uw schaalset is ingesteld op hand matig, past u de extensie toe op alle virtuele machines in de set door de upgrade op de Vm's aan te roepen. In de CLI is dit `az vmss update-instances` . |Initiatieven |
|Implementatie van afhankelijkheids agent in virtuele-machine schaal sets controleren-VM-installatie kopie (OS) niet vermeld |Rapporteert de schaalset voor virtuele machines als niet-compatibel als de VM-installatie kopie (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|Implementatie van Log Analytics agent in virtuele-machine schaal sets controleren-VM-installatie kopie (OS) niet vermeld |Rapporteert de schaalset voor virtuele machines als niet-compatibel als de VM-installatie kopie (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|Afhankelijkheidsagent implementeren voor virtuele-machineschaalsets van Linux |De afhankelijkheids agent voor virtuele Linux-machine schaal sets implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|Afhankelijkheidsagent implementeren voor virtuele-machineschaalsets van Windows |Een afhankelijkheids agent voor Windows-schaal sets voor virtuele machines implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|Log Analytics-agent implementeren voor virtuele-machineschaalsets van Linux |Implementeer Log Analytics agent voor virtuele Linux-machine schaal sets als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|Log Analytics-agent implementeren voor virtuele-machineschaalsets van Windows |Implementeer Log Analytics agent voor virtuele-machine schaal sets van Windows als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |

Zelfstandig beleid (niet inbegrepen bij het initiatief) wordt hier beschreven:

|Naam |Beschrijving |Type |
|-----|------------|-----|
|Controleren Log Analytics werk ruimte voor VM: niet-overeenkomend rapport |Rapport Vm's als niet-compatibel als ze niet worden vastgelegd in de Log Analytics werk ruimte die is opgegeven in het beleid of initiatief toewijzing. |Beleid |

### <a name="assign-the-azure-monitor-initiative"></a>Het Azure Monitor-initiatief toewijzen

Volg deze stappen om de beleids toewijzing te maken op de pagina **dekking van Azure monitor voor VM's beleid** . Zie [een beleids toewijzing maken op basis van de Azure Portal](../../governance/policy/assign-policy-portal.md)voor meer informatie over het uitvoeren van deze stappen.

Wanneer u het beleid of initiatief toewijst, kan het bereik dat u hebt geselecteerd in de toewijzing het bereik zijn dat hier wordt weer gegeven, of een subset ervan. U hebt bijvoorbeeld mogelijk een toewijzing voor het abonnement (beleids bereik) gemaakt en niet de beheer groep (dekkings bereik). In dit geval geeft het dekkings percentage de Vm's in het beleid of het initiatief bereik aan, gedeeld door de Vm's in het bereik van de dekking. In andere gevallen hebt u mogelijk enkele Vm's, of resource groepen of een abonnement uitgesloten van het beleids bereik. Als deze leeg is, wordt aangegeven dat het beleid of initiatief niet bestaat of dat u geen machtigingen hebt. Informatie wordt weer gegeven onder de **toewijzings status**.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer in de Azure Portal **monitor**. 

3. Kies **virtual machines** in het gedeelte **insightss** .
 
4. Selecteer het tabblad **aan de slag** . Selecteer op de pagina **dekking van beleid beheren**.

5. Selecteer een beheer groep of een abonnement in de tabel. Selecteer **bereik** door het weglatings teken (...) te selecteren. In het voor beeld beperkt een bereik de beleids toewijzing tot een groep virtuele machines voor afdwinging.

6. Op de pagina **toewijzing van Azure Policy** is het vooraf ingevuld met het initiatief Azure monitor voor VM's in te **scha kelen**. 
    Het vak **toewijzings naam** wordt automatisch ingevuld met de naam van het initiatief, maar u kunt dit wijzigen. U kunt ook een optionele beschrijving toevoegen. Het vak **toegewezen door** wordt automatisch ingevuld op basis van wie is aangemeld. Deze waarde is optioneel.

7. Beschrijving Als u een of meer resources uit het bereik wilt verwijderen, selecteert u **uitsluitingen**.

8. Selecteer in de vervolg keuzelijst **log Analytics werkruimte** voor de ondersteunde regio een werk ruimte.

   > [!NOTE]
   > Als de werk ruimte zich buiten het bereik van de toewijzing bevindt, moet u *log Analytics Inzender* machtigingen verlenen aan de principal-id van de beleids toewijzing. Als u dit niet doet, ziet u mogelijk een implementatie fout zoals `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` het verlenen van toegang, Lees [hoe u de beheerde identiteit hand matig configureert](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Het selectie vakje **beheerde identiteit** is ingeschakeld omdat het initiatief dat wordt toegewezen, een beleid bevat met het effect *deployIfNotExists* .
    
9. Selecteer de gewenste regio in de vervolg keuzelijst **identiteits locatie beheren** .

10. Selecteer **Toewijzen**.

Nadat u de toewijzing hebt gemaakt, werkt de pagina **dekking van Azure monitor voor VM's beleid** de **toewijzings dekking**, de **toewijzings status**, de **compatibele vm's**en de **compatibiliteits status** aan om de wijzigingen weer te geven. 

De volgende matrix wijst elke mogelijke compatibiliteits status voor het initiatief toe.  

| Nalevings status | Description | 
|------------------|-------------|
| **ACPI** | Voor alle Vm's in het bereik zijn de Log Analytics-en Dependency agents geïmplementeerd.|
| **Niet compatibel** | Niet alle virtuele machines in het bereik hebben de Log Analytics-en Dependency-agents geïmplementeerd en vereisen mogelijk herstel.|
| **Niet gestart** | Er is een nieuwe toewijzing toegevoegd. |
| **Vergrendelen** | U hebt onvoldoende bevoegdheden voor de beheer groep. <sup>1</sup> | 
| **Leeg** | Er is geen beleid toegewezen. | 

<sup>1</sup> als u geen toegang hebt tot de beheer groep, vraagt u een eigenaar om toegang te bieden. U kunt ook naleving bekijken en toewijzingen beheren via de onderliggende beheer groepen of-abonnementen. 

In de volgende tabel wordt elke mogelijke toewijzings status voor het initiatief toegewezen.

| Toewijzings status | Description | 
|------------------|-------------|
| **Geslaagd** | Voor alle Vm's in het bereik zijn de Log Analytics-en Dependency agents geïmplementeerd.|
| **Waarschuwing** | Het abonnement bevindt zich niet in een beheer groep.|
| **Niet gestart** | Er is een nieuwe toewijzing toegevoegd. |
| **Vergrendelen** | U hebt onvoldoende bevoegdheden voor de beheer groep. <sup>1</sup> | 
| **Leeg** | Er bestaan geen Vm's of er is geen beleid toegewezen. | 
| **Actie** | Wijs een beleid toe of bewerk een toewijzing. | 

<sup>1</sup> als u geen toegang hebt tot de beheer groep, vraagt u een eigenaar om toegang te bieden. U kunt ook naleving bekijken en toewijzingen beheren via de onderliggende beheer groepen of-abonnementen.

## <a name="review-and-remediate-the-compliance-results"></a>De compliantie resultaten controleren en herstellen

Het volgende voor beeld is voor een Azure-VM, maar is ook van toepassing op virtuele-machine schaal sets. Zie resultaten van niet- [naleving identificeren](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)voor meer informatie over het controleren van de nalevings resultaten. Selecteer een beheer groep of een abonnement uit de tabel op de pagina **dekking van Azure monitor voor VM's beleid** . Selecteer **naleving weer geven** door het weglatings teken (...) te selecteren.   

![Naleving van beleid voor Azure-Vm's](./media/vminsights-enable-at-scale-policy/policy-view-compliance.png)

Op basis van de resultaten van de beleids regels die zijn opgenomen in het initiatief, worden virtuele machines gerapporteerd als niet-compatibel in de volgende scenario's:

* Log Analytics agent of afhankelijkheids agent is niet geïmplementeerd.  
    Dit scenario is gebruikelijk voor een bereik met bestaande Vm's. Om het te beperken, implementeert u de vereiste agents door [herstel taken te maken](../../governance/policy/how-to/remediate-resources.md) op basis van een niet-compatibel beleid.  
    - Afhankelijkheidsagent implementeren voor Linux-VM's
    - Afhankelijkheidsagent implementeren voor Windows-VM's
    - Log Analytics-agent voor Linux-VM’s implementeren
    - Log Analytics-agent voor Windows-VM's implementeren

* VM-installatie kopie (OS) wordt niet geïdentificeerd in de beleids definitie.  
    De criteria van het implementatie beleid bevatten alleen de Vm's die zijn geïmplementeerd op basis van bekende VM-installatie kopieën van Azure. Raadpleeg de documentatie om te controleren of het VM-besturings systeem wordt ondersteund. Als dit niet wordt ondersteund, dupliceert u het implementatie beleid en werkt u het bij of wijzigt u het om de installatie kopie compatibel te maken.  
    - Implementatie van afhankelijkheids agent controleren – VM-installatie kopie (OS) niet vermeld
    - Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld

* Vm's worden niet aangemeld bij de opgegeven Log Analytics-werk ruimte.  
    Het is mogelijk dat sommige Vm's in het initiatief bereik worden aangemeld bij een andere Log Analytics werk ruimte dan de virtuele machine die is opgegeven in de beleids toewijzing. Dit beleid is een hulp programma voor het identificeren van de virtuele machines die aan een niet-compatibele werk ruimte rapporteren.  
    - Controleren Log Analytics werk ruimte voor VM: niet-overeenkomend rapport

## <a name="edit-an-initiative-assignment"></a>Een initiatief toewijzing bewerken

U kunt op elk gewenst moment nadat u een initiatief aan een beheer groep of abonnement hebt toegewezen, het bewerken om de volgende eigenschappen te wijzigen:

- Toewijzings naam
- Description
- Toegewezen door
- Log Analytics-werkruimte
- Uitzonderingen

## <a name="next-steps"></a>Volgende stappen

Nu de bewaking voor uw virtuele machines is ingeschakeld, is deze informatie beschikbaar voor analyse met Azure Monitor voor VM's. 

- Zie [Azure monitor voor VM's kaart weer geven](vminsights-maps.md)om gedetecteerde toepassings afhankelijkheden weer te geven. 

- Zie [Azure-VM-prestaties weer geven](vminsights-performance.md)om knel punten en het algehele gebruik van de VM-prestaties te identificeren. 
