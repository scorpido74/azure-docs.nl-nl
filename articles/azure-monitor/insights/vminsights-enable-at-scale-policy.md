---
title: Azure Monitor voor VM's inschakelen met behulp van Azure Policy | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Monitor voor VM's voor meerdere virtuele machines van Azure of virtuele-machine schaal sets inschakelt met behulp van Azure Policy.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: magoedte
ms.openlocfilehash: cbb471d337bd386b6c5f2c7a960565ef29855c9c
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338221"
---
# <a name="enable-azure-monitor-for-vms-preview-by-using-azure-policy"></a>Azure Monitor voor VM's inschakelen (preview) met behulp van Azure Policy

In dit artikel wordt uitgelegd hoe u Azure Monitor voor VM's (preview) voor virtuele machines van Azure of virtuele-machine schaal sets inschakelt met behulp van Azure Policy. Aan het einde van dit proces hebt u de configuratie ingeschakeld van de Log Analytics-en Dependency-agents en de geïdentificeerde virtuele machines die niet voldoen aan het beleid.

Als u Azure Monitor voor VM's wilt detecteren, beheren en inschakelen voor alle virtuele machines of virtuele-machine schaal sets van Azure, kunt u Azure Policy of Azure PowerShell gebruiken. Azure Policy is de methode die wordt aanbevolen omdat u beleids definities kunt beheren om uw abonnementen effectief te regelen, zodat er consistente naleving en automatische inrichtingen kunnen worden ingeschakeld. Deze beleids definities:

* Implementeer de Log Analytics-agent en de agent voor afhankelijkheden.
* Rapport over nalevingsresultaten.
* Herstellen voor niet-compatibele Vm's.

Als u deze taken wilt uitvoeren met Azure PowerShell of een Azure Resource Manager sjabloon, raadpleegt u [Azure monitor voor VM's (preview) inschakelen met behulp van Azure PowerShell of Azure Resource Manager sjablonen](vminsights-enable-at-scale-powershell.md).

## <a name="manage-policy-coverage-feature-overview"></a>Overzicht van functie voor beleids dekking beheren

Oorspronkelijk heeft de ervaring met Azure Policy voor het beheren en implementeren van de beleids definities voor Azure Monitor voor VM's uitsluitend van Azure Policy uitgevoerd. De functie voor het beheren van de dekking van beleid maakt het eenvoudiger en eenvoudiger om de Azure Monitor voor VM's-initiatief in te **stellen** , te beheren en in te scha kelen. Dit omvat de eerder genoemde beleids definities. U kunt deze nieuwe functie openen vanaf het tabblad **aan de slag** in azure monitor voor VM's. Selecteer **dekking van beleid beheren** om de pagina **dekking van Azure monitor voor VM's beleid** te openen.

![Azure Monitor van het tabblad aan de slag met Vm's](./media/vminsights-enable-at-scale-policy/get-started-page-01.png)

Hier kunt u de dekking voor het initiatief in uw beheer groepen en abonnementen controleren en beheren. U kunt begrijpen hoeveel Vm's er bestaan in elk van de beheer groepen en abonnementen en de nalevings status ervan.

![Pagina beleid Azure Monitor voor VM's beheren](./media/vminsights-enable-at-scale-policy/manage-policy-page-01.png)

Deze informatie is nuttig om u te helpen bij het plannen en uitvoeren van uw governance scenario voor Azure Monitor voor VM's vanaf één centrale locatie. Hoewel Azure Policy een weer gave voor naleving biedt wanneer een beleid of initiatief wordt toegewezen aan een bereik, kunt u met deze nieuwe pagina ontdekken waar het beleid of initiatief niet is toegewezen en dit op locatie toewijzen. Alle acties, zoals toewijzen, bekijken en bewerken, worden rechtstreeks omgeleid naar Azure Policy. De pagina **dekking van Azure monitor voor VM's beleid** is een uitgebreide en geïntegreerde ervaring voor het **inschakelen van Azure monitor voor VM's**.

Op deze pagina kunt u ook uw Log Analytics-werk ruimte configureren voor Azure Monitor voor VM's, dat:

- Hiermee installeert u de installatie Servicetoewijzing en Infrastructure Insights oplossingen.
- Hiermee schakelt u de prestatie meter items van het besturings systeem in die worden gebruikt door de prestatie grafieken, werkmappen en uw aangepaste logboek query's en waarschuwingen.

![Werk ruimte Azure Monitor voor VM's configureren](./media/vminsights-enable-at-scale-policy/manage-policy-page-02.png)

Deze optie is niet gerelateerd aan beleids acties. Het is beschikbaar om een eenvoudige manier te bieden om te voldoen aan de [vereisten](vminsights-enable-overview.md) die nodig zijn om Azure monitor voor VM's in te scha kelen.  

### <a name="what-information-is-available-on-this-page"></a>Welke informatie is beschikbaar op deze pagina?
De volgende tabel bevat een overzicht van de informatie die wordt weer gegeven op de pagina beleids dekking en hoe u deze kunt interpreteren.

| Function | Description | 
|----------|-------------| 
| **Bereik** | Beheer groep en abonnementen waarvoor u toegang hebt of die u hebt overgenomen, met de mogelijkheid om in te zoomen op de hiërarchie van de beheer groep.|
| **Rol** | Uw rol aan het bereik, dat lezers, eigenaar of Inzender kan zijn. In sommige gevallen kan het lijken leeg te zijn om aan te geven dat u toegang tot het abonnement hebt, maar niet tot de beheer groep waarvan het deel uitmaakt. De gegevens in andere kolommen variëren, afhankelijk van uw rol. De rol is belang rijk om te bepalen welke gegevens u kunt zien en welke acties u kunt uitvoeren met betrekking tot het toewijzen van beleid of initiatieven (eigenaar), het bewerken ervan of het weer geven van naleving. |
| **Totaal aantal Vm's** | Aantal Vm's onder dat bereik. Voor een beheer groep is dit een som van Vm's die zijn genest onder de abonnementen of de onderliggende beheer groep. |
| **Toewijzings dekking** | Percentage Vm's die worden gedekt door het beleid of initiatief. |
| **Toewijzings status** | Informatie over de status van uw beleid of initiatief toewijzing. |
| **Compatibele Vm's** | Aantal virtuele machines die voldoen aan het beleid of initiatief. Voor het initiatief **Azure monitor voor VM's inschakelen**, is dit het aantal vm's met zowel log Analytics agent als een afhankelijkheids agent. In sommige gevallen kan het worden leeg weer gegeven als gevolg van geen toewijzing, geen Vm's of onvoldoende machtigingen. Informatie wordt verstrekt onder **nalevings status**. |
| **Naleving** | Het algemene compatibiliteits nummer is de som van afzonderlijke resources die compatibel zijn gedeeld door de som van alle afzonderlijke resources. |
| **Nalevingsstatus** | Informatie over de nalevings status voor uw beleid of initiatief toewijzing.|

Wanneer u het beleid of initiatief toewijst, kan het bereik dat u hebt geselecteerd in de toewijzing het bereik of een subset hiervan zijn. U hebt bijvoorbeeld een toewijzing voor een abonnement (beleids bereik) gemaakt en geen beheer groep (dekkings bereik). In dit geval geeft de waarde van **toewijzings dekking** de vm's aan in het beleid of initiatief bereik gedeeld door de vm's in het dekkings bereik. In andere gevallen hebt u mogelijk enkele Vm's, resource groepen of een abonnement uitgesloten van het beleids bereik. Als de waarde leeg is, geeft dit aan dat het beleid of initiatief niet bestaat of dat u geen toestemming hebt. Informatie wordt weer gegeven onder de **toewijzings status**.

## <a name="enable-by-using-azure-policy"></a>Inschakelen met behulp van Azure Policy

Azure Monitor inschakelen voor virtuele machines met behulp van Azure Policy in uw tenant:

- Het initiatief toewijzen aan een bereik: beheer groep, abonnement of resource groep.
- Nalevings resultaten controleren en herstellen.

Zie voor meer informatie over het toewijzen van Azure Policy [overzicht van Azure Policy](../../governance/policy/overview.md#policy-assignment) en bekijk de [overzicht van beheergroepen](../../governance/management-groups/overview.md) voordat u doorgaat.

### <a name="policies-for-azure-vms"></a>Beleid voor virtuele Azure-machines

De beleids definities voor een virtuele machine van Azure worden weer gegeven in de volgende tabel.

|Name |Description |type |
|-----|------------|-----|
|\[Voor\]beeld: Azure Monitor voor VM's inschakelen |Schakel Azure Monitor in voor de virtuele machines in het opgegeven bereik (beheer groep, abonnement of resource groep). Log Analytics-werkruimte wordt gebruikt als parameter. |Initiatief |
|\[Voor\]beeld: Implementatie van afhankelijkheids agent controleren – VM-installatie kopie (OS) niet vermeld |Rapporteert Vm's als niet-compatibel als de VM-installatie kopie (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|\[Voor\]beeld: Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld |Rapporteert Vm's als niet-compatibel als de VM-installatie kopie (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|\[Voor\]beeld: Afhankelijkheids agent voor Linux-Vm's implementeren |De afhankelijkheids agent voor Linux Vm's implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|\[Voor\]beeld: Afhankelijkheids agent voor Windows-Vm's implementeren |Een afhankelijkheids agent voor Windows-Vm's implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|\[Voor\]beeld: Log Analytics-agent voor Linux-Vm's implementeren |Implementeer Log Analytics-agent voor Linux-Vm's als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|\[Voor\]beeld: Log Analytics-agent voor Windows-Vm's implementeren |Log Analytics-agent voor Windows-Vm's implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |

### <a name="policies-for-azure-virtual-machine-scale-sets"></a>Beleid voor virtuele-machine schaal sets van Azure

De beleids definities voor een schaalset voor virtuele Azure-machines worden in de volgende tabel weer gegeven.

|Name |Description |type |
|-----|------------|-----|
|\[Voor\]beeld: Azure Monitor inschakelen voor schaal sets voor virtuele machines |Schakel Azure Monitor in voor de virtuele-machine schaal sets in het opgegeven bereik (beheer groep, abonnement of resource groep). Log Analytics-werkruimte wordt gebruikt als parameter. Opmerking: Als het upgrade beleid voor de schaalset is ingesteld op hand matig, past u de extensie toe op alle Vm's in de set door de upgrade op de virtuele machines aan te roepen. In de CLI is dit AZ vmss update-instances. |Initiatief |
|\[Voor\]beeld: Implementatie van afhankelijkheids agent in virtuele-machine schaal sets controleren-VM-installatie kopie (OS) niet vermeld |Rapporteert de schaalset voor virtuele machines als niet-compatibel als de VM-installatie kopie (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|\[Voor\]beeld: Implementatie van Log Analytics agent in virtuele-machine schaal sets controleren-VM-installatie kopie (OS) niet vermeld |Rapporteert de schaalset voor virtuele machines als niet-compatibel als de VM-installatie kopie (OS) niet is gedefinieerd in de lijst en de agent niet is geïnstalleerd. |Beleid |
|\[Voor\]beeld: Afhankelijkheids agent voor virtuele Linux-machine schaal sets implementeren |De afhankelijkheids agent voor virtuele Linux-machine schaal sets implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|\[Voor\]beeld: Afhankelijkheids agent voor Windows-schaal sets voor virtuele machines implementeren |Een afhankelijkheids agent voor Windows-schaal sets voor virtuele machines implementeren als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|\[Voor\]beeld: Log Analytics agent voor virtuele Linux-machine schaal sets implementeren |Implementeer Log Analytics agent voor virtuele Linux-machine schaal sets als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |
|\[Voor\]beeld: Log Analytics-agent voor schaal sets voor virtuele Windows-machines implementeren |Implementeer Log Analytics agent voor virtuele-machine schaal sets van Windows als de VM-installatie kopie (OS) in de lijst is gedefinieerd en de agent niet is geïnstalleerd. |Beleid |

Beleid voor zelfstandige (niet opgenomen in het initiatief) wordt hier beschreven:

|Name |Description |type |
|-----|------------|-----|
|\[Voor\]beeld: Controleren Log Analytics werk ruimte voor VM: niet-overeenkomend rapport |Rapport Vm's als niet-compatibel als ze niet worden vastgelegd in de Log Analytics werk ruimte die is opgegeven in het beleid of initiatief toewijzing. |Beleid |

### <a name="assign-the-azure-monitor-initiative"></a>De Azure Monitor-initiatief toewijzen
Volg deze stappen om de beleids toewijzing te maken op de pagina **dekking van Azure monitor voor VM's beleid** . Zie voor meer informatie over hoe u deze stappen hebt voltooid, [een beleidstoewijzing maken vanuit Azure portal](../../governance/policy/assign-policy-portal.md).

Wanneer u het beleid of initiatief toewijst, kan het bereik dat u hebt geselecteerd in de toewijzing het bereik zijn dat hier wordt weer gegeven, of een subset ervan. U hebt bijvoorbeeld mogelijk een toewijzing voor het abonnement (beleids bereik) gemaakt en niet de beheer groep (dekkings bereik). In dit geval geeft het dekkings percentage de Vm's in het beleid of het initiatief bereik aan, gedeeld door de Vm's in het bereik van de dekking. In andere gevallen hebt u mogelijk enkele Vm's, of resource groepen of een abonnement uitgesloten van het beleids bereik. Als deze leeg is, wordt aangegeven dat het beleid of initiatief niet bestaat of dat u geen machtigingen hebt. Informatie wordt weer gegeven onder de **toewijzings status**.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer in de Azure portal, **Monitor**. 

3. Kies **virtual machines (preview)** in het gedeelte **insightss** .
 
4. Selecteer het tabblad **aan de slag** . Selecteer op de pagina **dekking van beleid beheren**.

5. Selecteer een beheer groep of een abonnement in de tabel. Selecteer **bereik** door het weglatings teken (...) te selecteren. In het voor beeld beperkt een bereik de beleids toewijzing tot een groep virtuele machines voor afdwinging.

6. Op de pagina **toewijzing van Azure Policy** is het vooraf ingevuld met het initiatief Azure monitor voor VM's in te **scha kelen**. 
    Het vak **toewijzings naam** wordt automatisch ingevuld met de naam van het initiatief, maar u kunt dit wijzigen. U kunt ook een optionele beschrijving toevoegen. Het vak **toegewezen door** wordt automatisch ingevuld op basis van wie is aangemeld. Deze waarde is optioneel.

7. (Optioneel) Selecteer een of meer als resources wilt verwijderen uit het bereik, **uitsluitingen**.

8. In de **Log Analytics-werkruimte** vervolgkeuzelijst lijst voor de ondersteunde regio, selecteer een werkruimte.

   > [!NOTE]
   > Als de werkruimte buiten het bereik van de toewijzing valt, verleent *Inzender van Log Analytics* machtigingen voor de beleidstoewijzing Principal-ID. Als u dit niet doet, ziet u mogelijk een implementatie fout, zoals `The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ...` om toegang te verlenen, Lees [hoe u de beheerde identiteit hand matig configureert](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity).
   > 
   >  Het selectie vakje **beheerde identiteit** is ingeschakeld omdat het initiatief dat wordt toegewezen, een beleid bevat met het effect *deployIfNotExists* .
    
9. In de **identiteit beheren locatie** vervolgkeuzelijst, selecteert u de juiste regio.

10. Selecteer **Toewijzen**.

Nadat u de toewijzing hebt gemaakt, werkt de pagina **dekking van Azure monitor voor VM's beleid** de **toewijzings dekking**, de **toewijzings status**, de **compatibele vm's**en de **compatibiliteits status** aan om de wijzigingen weer te geven. 

De volgende matrix wijst elke mogelijke compatibiliteits status voor het initiatief toe.  

| Nalevingsstatus | Description | 
|------------------|-------------|
| **ACPI** | Voor alle Vm's in het bereik zijn de Log Analytics-en Dependency agents geïmplementeerd.|
| **Niet compatibel** | Niet alle virtuele machines in het bereik hebben de Log Analytics-en Dependency-agents geïmplementeerd en vereisen mogelijk herstel.|
| **Niet gestart** | Er is een nieuwe toewijzing toegevoegd. |
| **Vergren delen** | U hebt onvoldoende bevoegdheden voor de beheer groep. <sup>1</sup> | 
| **Leeg** | Er is geen beleid toegewezen. | 

<sup>1</sup> als u geen toegang hebt tot de beheer groep, vraagt u een eigenaar om toegang te bieden. U kunt ook naleving bekijken en toewijzingen beheren via de onderliggende beheer groepen of-abonnementen. 

In de volgende tabel wordt elke mogelijke toewijzings status voor het initiatief toegewezen.

| Toewijzings status | Description | 
|------------------|-------------|
| **Geleverd** | Voor alle Vm's in het bereik zijn de Log Analytics-en Dependency agents geïmplementeerd.|
| **Waarschuwing** | Het abonnement bevindt zich niet in een beheer groep.|
| **Niet gestart** | Er is een nieuwe toewijzing toegevoegd. |
| **Vergren delen** | U hebt onvoldoende bevoegdheden voor de beheer groep. <sup>1</sup> | 
| **Leeg** | Er bestaan geen Vm's of er is geen beleid toegewezen. | 
| **Actie** | Wijs een beleid toe of bewerk een toewijzing. | 

<sup>1</sup> als u geen toegang hebt tot de beheer groep, vraagt u een eigenaar om toegang te bieden. U kunt ook naleving bekijken en toewijzingen beheren via de onderliggende beheer groepen of-abonnementen.

## <a name="review-and-remediate-the-compliance-results"></a>Controleren en herstellen van de compliantieresultaten

Het volgende voor beeld is voor een Azure-VM, maar is ook van toepassing op virtuele-machine schaal sets. Zie resultaten van niet- [naleving identificeren](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)voor meer informatie over het controleren van de nalevings resultaten. Selecteer een beheer groep of een abonnement uit de tabel op de pagina **dekking van Azure monitor voor VM's beleid** . Selecteer **naleving weer geven** door het weglatings teken (...) te selecteren.   

![Naleving van het beleid voor virtuele Azure-machines](./media/vminsights-enable-at-scale-policy/policy-view-compliance-01.png)

Op basis van de resultaten van de beleids regels die zijn opgenomen in het initiatief, worden virtuele machines gerapporteerd als niet-compatibel in de volgende scenario's:

* Log Analytics agent of afhankelijkheids agent is niet geïmplementeerd.  
    Dit scenario is gebruikelijk om een bereik met bestaande virtuele machines. Om het te beperken, implementeert u de vereiste agents door [herstel taken te maken](../../governance/policy/how-to/remediate-resources.md) op basis van een niet-compatibel beleid.  
    - \[Voor\]beeld: Afhankelijkheids agent voor Linux-Vm's implementeren
    - \[Voor\]beeld: Afhankelijkheids agent voor Windows-Vm's implementeren
    - \[Voor\]beeld: Log Analytics-agent voor Linux-Vm's implementeren
    - \[Voor\]beeld: Log Analytics-agent voor Windows-Vm's implementeren

* VM-installatie kopie (OS) wordt niet geïdentificeerd in de beleids definitie.  
    De criteria van het implementatiebeleid voor zijn alleen de VM's die zijn geïmplementeerd vanuit een bekende Azure VM-installatiekopieën. Raadpleeg de documentatie om te zien of het besturingssysteem van de virtuele machine wordt ondersteund. Als dit wordt niet ondersteund, dupliceren van de update en van implementatiebeleid of te wijzigen zodat de installatiekopie van het voldoen aan het beleid.  
    - \[Voor\]beeld: Implementatie van afhankelijkheids agent controleren – VM-installatie kopie (OS) niet vermeld
    - \[Voor\]beeld: Implementatie van Log Analytics agent controleren-VM-installatie kopie (OS) niet vermeld

* Virtuele machines zijn niet aangemeld bij de opgegeven Log Analytics-werkruimte.  
    Het is mogelijk dat sommige virtuele machines binnen het initiatief bereik zijn aangemeld bij een Log Analytics-werkruimte dan de naam die opgegeven in de beleidstoewijzing. Dit beleid is een hulp programma voor het identificeren van de virtuele machines die aan een niet-compatibele werk ruimte rapporteren.  
    - \[Voor\]beeld: Controleren Log Analytics werk ruimte voor VM: niet-overeenkomend rapport

## <a name="edit-an-initiative-assignment"></a>Een initiatief toewijzing bewerken

U kunt op elk gewenst moment nadat u een initiatief aan een beheer groep of abonnement hebt toegewezen, het bewerken om de volgende eigenschappen te wijzigen:

- Toewijzingsnaam
- Description
- Toegewezen door
- Log Analytics-werkruimte
- Uitzonderingen

## <a name="next-steps"></a>Volgende stappen

Nu de bewaking voor uw virtuele machines is ingeschakeld, is deze informatie beschikbaar voor analyse met Azure Monitor voor VM's. 

- Zie [Azure monitor voor VM's status weer geven](vminsights-health.md)voor meer informatie over het gebruik van de status functie. 
- Afhankelijkheden van gedetecteerde toepassingen, Zie [weergave Azure Monitor voor virtuele machines kaart](vminsights-maps.md). 
- Zie [Azure-VM-prestaties weer geven](vminsights-performance.md)om knel punten en het algehele gebruik van de VM-prestaties te identificeren. 
- Afhankelijkheden van gedetecteerde toepassingen, Zie [weergave Azure Monitor voor virtuele machines kaart](vminsights-maps.md).
