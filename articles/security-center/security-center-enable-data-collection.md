---
title: Gegevensverzameling in Azure Security Center | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een Log Analytics-agent installeert en hoe u een Log Analytics-werkruimte instelt waarin de verzamelde gegevens worden opgeslagen.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 04/27/2020
ms.author: memildin
ms.openlocfilehash: 92c73fed84910e525378aa18e02456960acf9911
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447235"
---
# <a name="data-collection-in-azure-security-center"></a>Gegevensverzameling in Azure Security Center
Security Center verzamelt gegevens van uw virtuele Azure-machines (VM's), virtuele-machineschaalsets, IaaS-containers en niet-Azure-computers (waaronder on-premises computers) om deze te bewaken op beveiligingsproblemen en bedreigingen. De gegevens worden verzameld met behulp van de Log Analytics-agent, die verschillende configuraties en gebeurtenislogboeken met betrekking tot beveiliging van de machine leest en de gegevens kopieert naar uw werkruimte voor analyse. Voorbeelden van dergelijke gegevens zijn: besturingssysteemtype en -versie, besturingssysteemlogboeken (Windows-gebeurtenislogboeken), actieve processen, computernaam, IP-adressen en aangemelde gebruiker.

Het verzamelen van gegevens is vereist om inzicht te krijgen in ontbrekende updates, onjuist geconfigureerde beveiligingsinstellingen voor het besturingssysteem, eindpuntbeveiligingsstatus, beveiliging van de status en beveiliging tegen bedreigingen. 

In dit artikel wordt beschreven hoe u een Log Analytics-agent installeert en hoe u een Log Analytics-werkruimte instelt waarin de verzamelde gegevens worden opgeslagen. Beide bewerkingen zijn vereist om gegevensverzameling mogelijk te maken. 

> [!NOTE]
> - Het verzamelen van gegevens is alleen nodig voor compute-bronnen (VM's, virtuele-machineschaalsets, IaaS-containers en niet-Azure-computers). U kunt profiteren van Azure Security Center, zelfs als u geen agents inricht. U hebt echter beperkte beveiliging en de mogelijkheden die hierboven worden vermeld, worden niet ondersteund.  
> - Zie [Ondersteunde platforms in Azure Security Center](security-center-os-coverage.md) voor een lijst met ondersteunde platforms.
> - Wanneer u gegevens opslaat in Log Analytics, ongeacht of u een nieuwe of bestaande werkruimte gebruikt, kunnen er extra kosten in rekening worden gebracht voor gegevensopslag. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Automatische inrichting van de Log Analytics-agent inschakelen <a name="auto-provision-mma"></a>

Als u de gegevens van de machines wilt verzamelen, moet de Log Analytics-agent zijn geïnstalleerd. Installatie van de agent kan automatisch worden uitgevoerd (aanbevolen) of u kunt de agent handmatig installeren. Automatische inrichting is standaard uitgeschakeld.

Als automatisch inrichten aan staat, implementeert Security Center de Log Analytics-agent op alle ondersteunde Azure-VM's en op nieuwe VM's. Automatische inrichting wordt aanbevolen, maar u kunt de agent handmatig installeren als dat nodig is (Zie [Handmatige installatie van de Log Analytics-agent](#manual-agent)).



Automatische inrichting van de Log Analytics-agent inschakelen:

1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center.
1. Selecteer het betreffende abonnement.
1. Stel op de pagina **Gegevensverzameling** **Automatische inrichting** in op **Aan**.
1. Selecteer **Opslaan**.

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="Automatische inrichting van de Log Analytics-agent inschakelen":::

>[!TIP]
> Als een werkruimte moet worden ingericht, kan de installatie van de agent tot wel 25 minuten duren.

Als de agent is geïmplementeerd op uw machines, kan Security Center extra aanbevelingen doen met betrekking tot de updatestatus van het systeem, de beveiligingsconfiguraties van het besturingssysteem en de eindpuntbeveiliging. Ook kan Security Center aanvullende beveiligingswaarschuwingen genereren.

>[!NOTE]
> Wanneer u automatische inrichting instelt op **Uit**, wordt de Log Analytics-agent niet verwijderd van Azure-VM's waarop de agent al is ingericht. Door automatische inrichting uit te schakelen, wordt de beveiligingsbewaking voor uw resources beperkt.

>[!NOTE]
> - Zie [Automatische inrichting in het geval van een reeds bestaande agentinstallatie](#preexisting).
> - Zie [De Log Analytics-agentextensie handmatig installeren](#manual-agent) voor instructies voor handmatig inrichten.
> - Zie [Automatische inrichting uitschakelen](#offprovisioning) voor instructies over het uitschakelen van automatisch inrichten.
> - Voor instructies over het onboarden van Security Center met behulp van PowerShell raadpleegt u [Het onboarden van Azure Security Center automatiseren met PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Werkruimteconfiguratie
Gegevens die worden verzameld door Security Center worden opgeslagen in een of meer werkruimten van Log Analytics. Uw gegevens kunnen worden verzameld van Azure-VM's die zijn opgeslagen in werkruimten die zijn gemaakt door Security Center of in een bestaande werkruimte die u hebt gemaakt. 

De configuratie van een werkruimte wordt per abonnement ingesteld, en verschillende abonnementen kunnen dezelfde werkruimte gebruiken.

### <a name="using-a-workspace-created-by-security-center"></a>Door Security Center gemaakte werkruimte gebruiken

Security Center kan automatisch een standaardwerkruimte maken waarin de gegevens worden opgeslagen. 

Door Security Center gemaakte werkruimte selecteren:

1. Onder **Configuratie van de standaardwerkruimte** selecteert u Werkruimten gebruiken die zijn gemaakt door Security Center.
    :::image type="content" source="./media/security-center-enable-data-collection/workspace-selection.png" alt-text="Automatische inrichting van de Log Analytics-agent inschakelen"::: 

1. Klik op **Opslaan**.<br>
    Security Center maakt een nieuwe resourcegroep en standaardwerkruimte in die geolocatie en verbindt de agent met die werkruimte. De naamconventie voor de werkruimte en de resourcegroep is:<br>
   **Werkruimte: Standaardwerkruimte-[abonnement-ID]-[geo]<br> Resourcegroep: Standaardresourcegroep-[geo]**

   Als een abonnement VM's van meerdere geolocaties bevat, maakt Security Center meerdere werkruimten. Er worden meerdere werkruimten gemaakt om te voldoen aan de privacyregels voor gegevens.
1. Security Center schakelt automatisch een Security Center-oplossing voor de werkruimte in volgens de prijscategorie voor het abonnement. 

> [!NOTE]
> De Log Analytics-prijscategorie van door Security Center gemaakte werkruimten heeft geen invloed op de facturering voor Security Center. De facturering voor Security Center is altijd gebaseerd op uw Security Center-beveiligingsbeleid en de oplossingen die in een werkruimte zijn geïnstalleerd. Voor abonnementen zonder Azure Defender schakelt Azure Security Center de *SecurityCenterFree*-oplossing in voor de standaardwerkruimte. Voor abonnementen met Azure Defender schakelt Azure Security Center de *Security*-oplossing in voor de standaardwerkruimte.
> Voor het opslaan van gegevens in Log Analytics worden mogelijk extra kosten voor gegevensopslag in rekening gebracht. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

Zie [Bestaande Log Analytics-klanten](./faq-azure-monitor-logs.md)voor meer informatie over bestaande Log Analytics-accounts.

### <a name="using-an-existing-workspace"></a>Een bestaande werkruimte gebruiken

Als u al een bestaande Log Analytics-werkruimte hebt, wilt u mogelijk dezelfde werkruimte gebruiken.

Als u uw bestaande Log Analytics-werkruimte wilt gebruiken, moet u lees- en schrijfmachtigingen hebben voor de werkruimte.

> [!NOTE]
> Oplossingen die zijn ingeschakeld voor de geselecteerde werkruimte worden toegepast op Azure-VM's die daarmee zijn verbonden. Voor betaalde oplossingen kan dit extra kosten met zich meebrengen. Zorg er met het oog op de gegevensprivacy voor dat de geselecteerde werkruimte zich in de juiste geografische regio bevindt.
> Voor het opslaan van gegevens in Log Analytics worden mogelijk extra kosten voor gegevensopslag in rekening gebracht. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

Een bestaande Log Analytics-werkruimte selecteren:

1. Selecteer onder **Configuratie van de standaardwerkruimte** **Een andere werkruimte gebruiken**.
    :::image type="content" source="./media/security-center-enable-data-collection/use-another-workspace.png" alt-text="Automatische inrichting van de Log Analytics-agent inschakelen"::: 

2. Selecteer in de vervolgkeuzelijst een werkruimte om de verzamelde gegevens op te slaan.

   > [!NOTE]
   > In het vervolgkeuzemenu zijn alle werkruimten voor al uw abonnementen beschikbaar. Zie [Werkruimteselectie voor meerdere abonnementen](security-center-enable-data-collection.md#cross-subscription-workspace-selection) voor meer informatie. U moet gemachtigd zijn om toegang te krijgen tot de werkruimte.
   >
   >

3. Selecteer **Opslaan**.
4. Nadat u **Opslaan** hebt geselecteerd, wordt u gevraagd of u de bewaakte VM's die eerder waren verbonden met een standaardwerkruimte, opnieuw wilt configureren.

   - Selecteer **Nee** als u wilt dat de nieuwe werkruimte-instellingen alleen worden toegepast op nieuwe VM's. De nieuwe werkruimte-instellingen zijn alleen van toepassing op nieuwe agentinstallaties: nieuw gedetecteerde VM's waarop de Log Analytics-agent niet is geïnstalleerd.
   - Selecteer **Ja** als u wilt dat de nieuwe werkruimte-instellingen worden toegepast op alle VM's. Daarnaast wordt elke VM die is verbonden met een door Security Center gemaakte werkruimte opnieuw verbonden met de nieuwe doelwerkruimte.

   > [!NOTE]
   > Als u Ja selecteert, moet u de werkruimte(n) die zijn gemaakt door Security Center pas verwijderen als alle VM's opnieuw zijn verbonden met de nieuwe doelwerkruimte. Deze bewerking mislukt als een werkruimte te vroeg wordt verwijderd.
   >
   >

   - Als u de bewerking wilt annuleren, selecteert u **Annuleren**.

     ![Opties bekijken voor het opnieuw configureren van bewaakte VM's][3]

5. Selecteer of Azure Defender moet worden ingeschakeld voor de werkruimte.

    Stel de prijscategorie voor de werkruimte in als u een bestaande werkruimte wilt gebruiken. Hiermee wordt een Security Center-oplossing geïnstalleerd in de werkruimte als deze nog niet aanwezig is.

    1. Selecteer in het hoofdmenu van Security Center de optie **Prijzen en instellingen**.
     
    1. Selecteer de werkruimte waarmee u verbinding wilt maken met de agent.

    1. Selecteer **Azure Defender aan** of **Azure Defender uit**.

   
   >[!NOTE]
   >Als voor de werkruimte al een **Security**- of **SecurityCenterFree**-oplossing is ingeschakeld, wordt de prijs automatisch ingesteld. 


## <a name="cross-subscription-workspace-selection"></a>Werkruimteselectie voor meerdere abonnementen
Wanneer u een werkruimte selecteert voor het opslaan van uw gegevens, kunt u kiezen uit alle werkruimten die in al uw abonnementen beschikbaar zijn. Het voordeel hiervan is dat u gegevens kunt verzamelen van virtuele machines die in verschillende abonnementen worden uitgevoerd en deze kunt opslaan in de werkruimte van uw keuze. Deze optie is handig als u een centrale werkruimte gebruikt in uw organisatie en u deze ook wilt gebruiken voor het verzamelen van beveiligingsgegevens. Zie [Werkruimtetoegang beheren](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) voor meer informatie over het beheren van werkruimten.



## <a name="data-collection-tier"></a>Laag voor gegevensverzameling
Het selecteren van een laag voor gegevensverzameling in Azure Security Center heeft alleen invloed op de opslag van beveiligingsgebeurtenissen in de Log Analytics-werkruimte. De Log Analytics-agent blijft de beveiligingsgebeurtenissen verzamelen en analyseren die Azure Security Center nodig heeft om u te beschermen tegen bedreigingen, ongeacht welke laag van beveiligingsgebeurtenissen u opslaat in uw Log Analytics-werkruimte. Als u ervoor kiest om beveiligingsgebeurtenissen op te slaan in uw werkruimte, kunt u gebeurtenissen vanuit die werkruimte onderzoeken, zoeken en controleren. 
> [!NOTE]
> Voor het opslaan van gegevens in Log Analytics worden mogelijk extra kosten voor gegevensopslag in rekening gebracht. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.
> 
> U kunt het juiste filterbeleid voor uw abonnementen en werkruimten kiezen uit vier sets gebeurtenissen die in uw werkruimte kunnen worden opgeslagen: 

- **Geen** : opslag voor beveiligingsgebeurtenissen uitschakelen. Dit is de standaardinstelling.
- **Minimaal**: een kleine set gebeurtenissen voor klanten die het gebeurtenisvolume willen minimaliseren.
- **Algemeen**: dit is een set gebeurtenissen die voldoet voor de meeste klanten en waarmee ze over een volledig auditlogboek beschikken.
- **Alle gebeurtenissen**: voor klanten die willen controleren of alle gebeurtenissen zijn opgeslagen.


> [!NOTE]
> Deze beveiligingsgebeurtenissensets zijn alleen beschikbaar met Azure Defender. Bekijk de pagina [Prijzen](security-center-pricing.md) voor meer informatie over de tariefopties van Security Center.
Deze sets zijn ontworpen om typische scenario's te ondersteunen. Controleer welke set aan uw behoeften voldoet voordat u deze implementeert.
>
>

Bij het bepalen van de gebeurtenissen die worden opgenomen in de gebeurtenissets **Algemeen** en **Minimaal** hebben we samengewerkt met klanten en industriestandaards gebruikt om de ongefilterde frequentie van elke gebeurtenis en het gebruik ervan te weten te komen. Bij dit proces hebben we de volgende richtlijnen gebruikt:

- **Minimaal**: ervoor zorgen dat deze set alleen gebeurtenissen bevat die kunnen wijzen op een geslaagde inbraak, en belangrijke gebeurtenissen met een zeer laag volume. Deze set bevat bijvoorbeeld geslaagde en mislukte aanmeldingspogingen van gebruikers (gebeurtenis-id's 4624 en 4625), maar geen afmeldingen; die zijn belangrijk voor de controle, maar zijn niet belangrijk voor detectie en hebben een betrekkelijk hoog volume. Het grootste deel van het gegevensvolume van deze set bestaat uit de aanmeldingsgebeurtenissen en de procesaanmaakgebeurtenis (gebeurtenis-id 4688).
- **Algemeen**: een volledig auditlogboek bieden in deze set. Deze set bevat bijvoorbeeld zowel aanmeldingen als afmeldingen van gebruikers (gebeurtenis-ID 4634). Hierin zijn controleacties opgenomen zoals wijzigingen van beveiligingsgroep, belangrijke Kerberos-bewerkingen voor domeincontrollers, en andere gebeurtenissen die worden aanbevolen door brancheorganisaties.

Gebeurtenissen met een zeer laag volume zijn opgenomen in de set Algemeen, aangezien de belangrijkste motivatie om deze te verkiezen boven Alle gebeurtenissen het verminderen van het volume is, en niet om specifieke gebeurtenissen eruit te filteren.

Hier volgt een volledige uitsplitsing van de gebeurtenis-id's van Security en App Locker voor elke set:

| Gegevenslaag | Verzamelde gebeurtenis-id's |
| --- | --- |
| Minimaal | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Algemeen | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Als u gebruikmaakt van GPO (groepsbeleidsobject), is het raadzaam om het controlebeleid voor procesaanmaakgebeurtenis 4688 en het veld *CommandLine* binnen gebeurtenis 4688 in te schakelen. Zie de [Veelgestelde vragen](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled) van Security Center voor meer informatie over procesaanmaakgebeurtenis 4688. Zie [Audit Policy Recommendations](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations) (aanbevelingen voor controlebeleid) voor meer informatie over deze controlebeleidsregels.
> -  Om het verzamelen van gegevens voor [besturingselementen voor adaptieve toepassingsregelaars](security-center-adaptive-application.md)in te schakelen, configureert Security Center een lokaal AppLocker-beleid in de controlemodus om alle toepassingen toe te staan. Dit zorgt ervoor dat AppLocker gebeurtenissen genereert die vervolgens door Security Center worden verzameld en gebruikt. Het is belangrijk te weten dat dit beleid niet wordt geconfigureerd op machines waarop al een AppLocker-beleid is geconfigureerd. 
> - Als u Windows-filterplatform [gebeurtenis-id 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156) wilt verzamelen, moet u [	Filterplatform-verbinding controleren](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) inschakelen (Auditpol / set / subcategory:"Filtering Platform Connection" /Success:Enable)
>

Uw filterbeleid kiezen:
1. Selecteer op de pagina **Gegevensverzameling** uw filterbeleid onder **Aanvullende onbewerkte gegevens opslaan - Windows-beveiligingsgebeurtenissen**.
 
1. Selecteer **Opslaan**.
    :::image type="content" source="./media/security-center-enable-data-collection/data-collection-tiers.png" alt-text="Automatische inrichting van de Log Analytics-agent inschakelen":::

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatische inrichting in het geval van een reeds bestaande agentinstallatie <a name="preexisting"></a> 

In de volgende use-cases wordt aangegeven hoe automatische inrichting werkt in gevallen waarin er al een agent of extensie is geïnstalleerd. 

- Log Analytics agent is geïnstalleerd op de computer, maar niet als een extensie (directe agent)<br>
Als de Log Analytics-agent rechtstreeks op de VM is geïnstalleerd (niet als een Azure-extensie), installeert Security Center de Log Analytics-agentextensie, en wordt de Log Analytics-agent mogelijk bijgewerkt naar de nieuwste versie.
De geïnstalleerde agent blijft rapporteren aan de reeds geconfigureerde werkruimte(n), en rapporteert bovendien aan de in Security Center geconfigureerde werkruimte (multihoming wordt ondersteund op Windows-machines).
Als de geconfigureerde werkruimte een gebruikerswerkruimte is (niet de standaardwerkruimte van Security Center), moet u de oplossing 'security/securityFree' erin installeren voor Security Center kan beginnen met het verwerken van gebeurtenissen van VM's en computers aan die werkruimte kunnen gaan rapporteren.<br>
<br>
Voor Linux-machines wordt Agent-multihoming nog niet ondersteund. Als er een bestaande agentinstallatie wordt gedetecteerd, treedt er geen automatische inrichting op en wordt de configuratie van de machine niet gewijzigd.
<br>
Voor bestaande machines op abonnementen die vóór 17 maart 2019 bij Security Center zijn geönboard, wordt de Log Analytics-agentextensie niet geïnstalleerd wanneer een bestaande agent wordt gedetecteerd, en de machine wordt niet beïnvloed. Zie voor deze machines de aanbeveling 'Resolve monitoring agent health issues on your machines' (Agentstatusproblemen op uw machines oplossen) om de agentinstallatieproblemen op deze machines op te lossen.

  
- System Center Operations Manager-agent is geïnstalleerd op de machine<br>
Security Center installeert de Log Analytics-agentextensie naast de bestaande Operations Manager. De bestaande Operations Manager-agent blijft normaal aan de Operations Manager-server rapporteren. De Operations Manager-agent en Log Analytics-agent hebben gemeenschappelijke runtime-bibliotheken, die tijdens dit proces worden bijgewerkt naar de nieuwste versie. Als Operations Manager-agent versie 2012 is geïnstalleerd, schakel automatische inrichting dan **niet** in.<br>

- Er is een reeds bestaande VM-extensie aanwezig<br>
    - Wanneer de bewakingsagent is geïnstalleerd als een extensie, staat de extensieconfiguratie slechts rapportage aan één werkruimte toe. Security Center overschrijft bestaande verbindingen met gebruikerswerkruimten niet. Security Center slaat beveiligingsgegevens van de VM op in de reeds verbonden werkruimte, mits de oplossing 'security' of 'securityFree' erop is geïnstalleerd. Tijdens die proces kan Security Center de extensie upgraden naar de nieuwste versie.  
    - Als u wilt zien naar welke werkruimte de bestaande extensie gegevens verzendt, voert u de test uit om de [verbinding met Azure Security Center te valideren](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). U kunt ook Log Analytics-werkruimten openen, een werkruimte selecteren, de VM selecteren en kijken naar de verbinding met de Log Analytics-agent. 
    - Als u een omgeving hebt waarin de Log Analytics-agent is geïnstalleerd op clientwerkstations en rapporteert aan een bestaande Log Analytics-werkruimte, kunt u de lijst met [door Azure Security Center ondersteunde besturingssystemen](security-center-os-coverage.md) bekijken om u ervan te verzekeren dat uw besturingssysteem wordt ondersteund. Zie [bestaande Log Analytics-klanten](./faq-azure-monitor-logs.md)voor meer informatie.
 
### <a name="turn-off-automatic-provisioning"></a>Automatische inrichting uitschakelen <a name="offprovisioning"></a>
Automatische inrichting van de Log Analytics-agent uitschakelen:

1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center in de portal.
2. Selecteer het betreffende abonnement.

    :::image type="content" source="./media/security-center-enable-data-collection/select-subscription.png" alt-text="Automatische inrichting van de Log Analytics-agent inschakelen":::

3. Selecteer **Gegevensverzameling**.
4. Selecteer **Uit** onder **Automatische inrichting** om automatische inrichting uit te schakelen.
5. Selecteer **Opslaan**. 


Als automatisch inrichten is uitgeschakeld, wordt de sectie Configuratie van de standaardwerkruimte niet weergegeven.

Als u automatische inrichting uitschakelt nadat het aan heeft gestaan, worden er geen agents meer ingericht op nieuwe VM's.

 
> [!NOTE]
>  Wanneer u automatische inrichting uitschakelt, wordt de Log Analytics-agent niet verwijderd van Azure-VM's waarop de agent al is ingericht. Zie voor meer informatie over het verwijderen van de OMS-extensie [Hoe kan ik OMS-extensies die door Security Center zijn geïnstalleerd, verwijderen?](faq-data-collection-agents.md#remove-oms).
>
    
## <a name="manual-agent-provisioning"></a>Handmatige agentinrichting <a name="manual-agent"></a>
 
Er zijn verschillende manieren om de Log Analytics-agent handmatig te installeren. Wanneer u handmatig installeert, zorg er dan voor dat u automatische inrichting uitschakelt.

### <a name="operations-management-suite-vm-extension-deployment"></a>Implementatie van VM-extensie van Operations Management Suite 

U kunt de Log Analytics-agent handmatig installeren, zodat Security Center beveiligingsgegevens van uw VM's kan verzamelen en aanbevelingen en waarschuwingen geven.

1. Schakel automatische inrichting uit.

1. Maak desgewenst een werkruimte.

1. Schakel Azure Defender in voor de werkruimte waarin u de Log Analytics-agent installeert:

    1. Selecteer **Prijzen en instellingen** in het hoofdmenu van Security Center.

    1. Stel de werkruimte in waar u de agent gaat installeren. Zorg ervoor dat de werkruimte tot hetzelfde abonnement behoort dat u in Security Center gebruikt en dat u voor de werkruimte lees- en schrijfmachtigingen hebt.

    1. Zet Azure Defender aan en selecteer **Opslaan**.

       >[!NOTE]
       >Als voor de werkruimte al een **Security**- of **SecurityCenterFree**-oplossing is ingeschakeld, wordt de prijs automatisch ingesteld. 

1. Als u de agents op nieuwe VM's wilt implementeren met behulp van een Resource Manager-sjabloon, installeert u de Log Analytics agent:

   a.  [De Log Analytics-agent voor Windows installeren](../virtual-machines/extensions/oms-windows.md)
    
   b.  [De Log Analytics-agent voor Linux installeren](../virtual-machines/extensions/oms-linux.md)

1. Als u de extensies op bestaande VM's wilt implementeren, volgt u de instructies in [Gegevens over Azure Virtual Machines verzamelen](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > De sectie **Gebeurtenis- en prestatiegegevens verzamelen** is optioneel.
   >

1. Als u PowerShell wilt gebruiken om de extensie te implementeren, gebruikt u de instructies in de documentatie voor virtual machines:

    - [Voor Windows-machines](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#powershell-deployment)

    - [Voor Linux-machines](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-linux?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#azure-cli-deployment)



> [!NOTE]
> Voor instructies over het onboarden van Security Center met behulp van PowerShell raadpleegt u [Het onboarden van Azure Security Center automatiseren met PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Problemen oplossen

-   Zie [Status van agents controleren](security-center-troubleshooting-guide.md#mon-agent) als u problemen met de automatische inrichting van de installatie wilt identificeren.

-  Zie [Problemen oplossen met de netwerkvereisten voor de Monitoring Agent ](security-center-troubleshooting-guide.md#mon-network-req) om de netwerkvereisten voor de bewakingsagent te identificeren.
-   Zie [Onboarding-problemen van Operations Management Suite oplossen](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) voor het identificeren van problemen met handmatige onboarding.

- Problemen met niet-bewaakte VM's en computers identificeren:

    Een VM of computer wordt niet bewaakt door Security Center als de Log Analytics-agentextensie niet wordt uitgevoerd op de machine. Er kan al een lokale agent zijn geïnstalleerd op de machine, bijvoorbeeld de directe OMS-agent of de System Center Operations Manager-agent. Machines met deze agents worden geïdentificeerd als niet-bewaakt omdat deze agents niet volledig worden ondersteund in Security Center. De Log Analytics-agentextensie is vereist om volledig te profiteren van alle mogelijkheden van Security Center.

    Zie [Problemen met de agentstatus controleren](security-center-troubleshooting-guide.md#mon-agent) voor meer informatie over de redenen waarom Security Center VM's en computers die zijn geïnitialiseerd voor automatische inrichting niet afdoende kan bewaken.


## <a name="next-steps"></a>Volgende stappen
In dit artikel werd uitgelegd hoe het verzamelen van gegevens en automatisch inrichten in Security Center werkt. Zie de volgende pagina's voor meer informatie over het Security Center:

* [Azure Security Center FAQ](faq-general.md): raadpleeg veelgestelde vragen over het gebruik van de service.
* [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md): meer informatie over het bewaken van de status van uw Azure-resources.



<!--Image references-->
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
