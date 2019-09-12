---
title: Verzamelen van gegevens in Azure Security Center | Microsoft Docs
description: " Informatie over het verzamelen van gegevens in Azure Security Center inschakelen. "
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 0cbb6f022dbeded2bbfb19769595be69ec62c311
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910634"
---
# <a name="data-collection-in-azure-security-center"></a>Verzamelen van gegevens in Azure Security Center
Security Center verzamelt gegevens van uw virtuele machines van Azure (Vm's), schaal sets voor virtuele machines, IaaS containers en niet-Azure-computers (inclusief on-premises) om te controleren op beveiligings problemen en bedreigingen. Gegevens worden verzameld met behulp van de Log Analytics-agent, die verschillende aan beveiliging gerelateerde configuraties en gebeurtenis logboeken van de computer leest en de gegevens naar uw werk ruimte kopieert voor analyse. Voorbeelden van dergelijke gegevens zijn: besturingssysteem systeemtype en versie, besturingssysteemlogboeken (Windows-gebeurtenislogboeken), actieve processen, computernaam, IP-adressen en aangemelde gebruiker. De Log Analytics-agent kopieert ook crash dump bestanden naar uw werk ruimte.

Het verzamelen van gegevens is vereist om aan te bieden inzicht in de ontbrekende updates, onjuist geconfigureerde OS-beveiligingsinstellingen, endpoint protection inschakelen en status- en bedreigingsbeveiliging detecties. 

In dit artikel wordt beschreven hoe u een Log Analytics Agent installeert en hoe u een Log Analytics werk ruimte instelt waarin de verzamelde gegevens worden opgeslagen. Beide bewerkingen zijn vereist om gegevens te verzamelen. 

> [!NOTE]
> - Het verzamelen van gegevens is alleen nodig voor reken bronnen (Vm's, schaal sets voor virtuele machines, IaaS containers en niet-Azure-computers). U kunt profiteren van Azure Security Center, zelfs als u agents; niet inrichten echter, u hebt beperkte beveiliging en bovenstaande functies worden niet ondersteund.  
> - Zie voor een lijst van ondersteunde platforms, [ondersteunde platforms in Azure Security Center](security-center-os-coverage.md).
> - Het opslaan van gegevens in Log Analytics, ongeacht of u een nieuwe of bestaande werk ruimte gebruikt, kan er extra kosten in rekening worden gebracht voor gegevens opslag. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

## Automatische inrichting van de Log Analytics-agent inschakelen<a name="auto-provision-mma"></a>

Als u de gegevens van de computers wilt verzamelen, moet de Log Analytics-agent zijn geïnstalleerd. De installatie van de agent kan automatisch worden uitgevoerd (aanbevolen) of u kunt de agent hand matig installeren.  

>[!NOTE]
> Automatische inrichting is standaard uitgeschakeld. Als u wilt instellen in Security Center voor het installeren van automatische inrichting standaard, ingesteld op **op**.
>

Als automatische inrichting is ingeschakeld, wordt Security Center de Log Analytics agent ingericht op alle ondersteunde Azure-Vm's en eventuele nieuwe virtuele machines die worden gemaakt. Automatische inrichting wordt sterk aanbevolen, maar handmatige agentinstallatie is ook beschikbaar. [Meer informatie over het installeren van de log Analytics agent-extensie](#manual-agent).



Automatische inrichting van de Log Analytics-agent inschakelen:
1. Selecteer in het hoofd menu van Security Center **prijzen & instellingen**.
2. Klik op het betreffende abonnement

   ![Abonnement selecteren][7]

3. Selecteer **gegevens verzameling**.
4. Onder **automatische inrichting**, selecteer **op** voor automatische inrichting inschakelen.
5. Selecteer **Opslaan**.

   ![Automatische inrichting inschakelen][1]

>[!NOTE]
> - Zie voor instructies over het inrichten van een bestaande installatie [automatische inrichting in geval van een bestaande installatie van de agent](#preexisting).
> - Zie [de log Analytics agent-extensie hand matig installeren](#manual-agent)voor instructies over het hand matig inrichten.
> - Zie voor instructies voor automatische inrichting uitschakelen, [automatische inrichting uitschakelen](#offprovisioning).
> - Voor instructies over hoe onboarding Security Center met behulp van PowerShell, Zie [automatiseren onboarding van Azure Security Center met behulp van PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Configuratie van de werkruimte
Gegevens die zijn verzameld door Security Center wordt opgeslagen in Log Analytics-werkruimte. U kunt gegevens die worden verzameld van Azure VM's die zijn opgeslagen in werkruimten die zijn gemaakt door Security Center of in een bestaande werkruimte die u hebt gemaakt. 

Configuratie van de standaardwerkruimte per abonnement is ingesteld en veel abonnementen kunnen dezelfde werkruimte gebruiken.

### <a name="using-a-workspace-created-by-security-center"></a>Een werkruimte gemaakt met Security Center gebruiken

Security center kan automatisch een standaardwerkruimte voor het opslaan van de gegevens maken. 

Een werkruimte te selecteren die zijn gemaakt door Security Center:

1. Onder **configuratie van de standaardwerkruimte**, selecteer werkruimten die zijn gemaakt door Security center gebruiken.
   ![Prijscategorie selecteren][10] 

1. Klik op **Opslaan**.<br>
    Security Center maakt u een nieuwe resource-groep en de standaard werkruimte in die geolocatie en verbindt de agent voor de desbetreffende werkruimte. De naamconventie voor de werkruimte en de resource is:<br>
   **Werk ruimte DefaultWorkspace-[abonnements-id]-[geo]<br> resource groep: DefaultResourceGroup-[geo]**

   Als een abonnement virtuele machines van meerdere geolocations bevat, maakt Security Center meerdere werkruimten. Meerdere werkruimten worden gemaakt voor het onderhouden van regels voor privacy van gegevens.
1. Security Center wordt automatisch ingeschakeld voor een oplossing voor Security Center in de werkruimte per de prijscategorie voor het abonnement instellen. 

> [!NOTE]
> De prijscategorie van werkruimten die zijn gemaakt door Security Center met Log Analytics heeft geen invloed op de facturering van Security Center. Security Center-facturering is altijd gebaseerd op het Security Center-beveiligingsbeleid en de oplossingen die zijn geïnstalleerd op een werkruimte. Voor de laag gratis, schakelt Azure Security Center de *SecurityCenterFree* -oplossing op de standaardwerkruimte. Voor de Standard-laag, schakelt Azure Security Center de *Security* -oplossing op de standaardwerkruimte.
> Bij het opslaan van gegevens in Log Analytics worden mogelijk extra kosten in rekening gebracht voor gegevens opslag. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

Zie voor meer informatie over bestaande log Analytics-accounts [bestaande log Analytics-klanten](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Met behulp van een bestaande werkruimte

Als u al een bestaande Log Analytics-werk ruimte hebt, wilt u mogelijk dezelfde werk ruimte gebruiken.

Voor het gebruik van uw bestaande Log Analytics-werkruimte, hebt u lees- en schrijfmachtigingen heeft voor de werkruimte.

> [!NOTE]
> Oplossingen die zijn ingeschakeld op de bestaande werkruimte wordt toegepast op Azure-VM's die zijn verbonden met het. Voor betaalde oplossingen kan dit resulteren in extra kosten in rekening gebracht. Zorg ervoor dat de geselecteerde werkruimte zich in de juiste geografische regio voor overwegingen bij de privacy van gegevens.
> Bij het opslaan van gegevens in log Analytics worden mogelijk extra kosten in rekening gebracht voor gegevens opslag. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

Selecteer een bestaande Log Analytics-werkruimte:

1. Onder **configuratie van de standaardwerkruimte**, selecteer **gebruik een andere werkruimte**.

   ![Selecteer een bestaande werkruimte][2]

2. Selecteer een werkruimte voor het opslaan van verzamelde gegevens in de vervolgkeuzelijst.

   > [!NOTE]
   > In de vervolgkeuzelijst menu zijn alle werkruimten in al uw abonnementen beschikbaar. Zie [cross-werkruimte kunt selecteren. abonnement](security-center-enable-data-collection.md#cross-subscription-workspace-selection) voor meer informatie. U moet gemachtigd voor toegang tot de werkruimte.
   >
   >

3. Selecteer **Opslaan**.
4. Na het selecteren van **opslaan**, wordt u gevraagd als u wilt reconfigure bewaakte VM's die eerder zijn verbonden met een standaardwerkruimte.

   - Selecteer **Nee** als u wilt dat de nieuwe werkruimte-instellingen wilt toepassen op nieuwe virtuele machines. De nieuwe werk ruimte-instellingen zijn alleen van toepassing op nieuwe agent installaties. nieuw gedetecteerde Vm's waarop de Log Analytics-agent niet is geïnstalleerd.
   - Selecteer **Ja** als u wilt dat de nieuwe werkruimte-instellingen wilt toepassen op alle virtuele machines. Bovendien wordt elke virtuele machine verbonden met een Security Center-werkruimte gemaakt verbonden aan de nieuwe doelwerkruimte.

   > [!NOTE]
   > Als u Ja selecteert, moet u de werkruimten die zijn gemaakt door Security Center totdat alle virtuele machines hebt opnieuw is verbonden met de nieuwe doelwerkruimte niet verwijderen. Met deze bewerking mislukt als een werkruimte te vroeg is verwijderd.
   >
   >

   - Selecteer **annuleren** om de bewerking te annuleren.

     ![Selecteer een bestaande werkruimte][3]

5. Selecteer de prijs categorie voor de gewenste werk ruimte die u wilt instellen voor de Log Analytics agent. <br>Voor het gebruik van een bestaande werkruimte, stelt u de prijscategorie voor de werkruimte. Hiermee installeert u een security Center-oplossing in de werkruimte als deze nog niet is aanwezig.

    a.  Selecteer in het hoofd menu van Security Center **prijzen & instellingen**.
     
    b.  Selecteer de gewenste werk ruimte waarin u de agent wilt verbinden.
        ![Selecteer werkruimte][7] c. De prijscategorie instellen.
        ![Prijscategorie selecteren][9]
   
   >[!NOTE]
   >Als de werkruimte is al een **Security** of **SecurityCenterFree** oplossing is ingeschakeld, de prijzen wordt automatisch ingesteld. 

## <a name="cross-subscription-workspace-selection"></a>Abonnementoverschrijdende werkruimte kunt selecteren.
Wanneer u een werkruimte waarin u uw gegevens kunt opslaan selecteert, worden alle werkruimten in al uw abonnementen beschikbaar. Abonnementoverschrijdende werkruimte kunt selecteren. Hiermee kunt u gegevens van virtuele machines die worden uitgevoerd in verschillende abonnementen verzamelen en op te slaan in de werkruimte van uw keuze. Deze optie is handig als u van een gecentraliseerde werkruimte in uw organisatie gebruikmaakt en wilt gebruiken voor verzameling van beveiligingsgegevens. Zie voor meer informatie over het beheren van werkruimten [werkruimtetoegang beheren](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Verzameling gegevenslaag
Een verzameling gegevenslaag selecteren in Azure Security Center heeft alleen invloed op de opslag van beveiligingsgebeurtenissen in uw Log Analytics-werkruimte. De Log Analytics-agent verzamelt en analyseert nog steeds de beveiligings gebeurtenissen die vereist zijn voor de bedreigings detecties van de Azure Security Center, ongeacht welke laag beveiligings gebeurtenissen u in uw Log Analytics-werk ruimte wilt opslaan (indien van toepassing). Voor het opslaan van beveiligingsgebeurtenissen in uw werkruimte te kiezen kunt voor onderzoek, zoeken en controle van de gebeurtenissen die in uw werkruimte. 
> [!NOTE]
> Bij het opslaan van gegevens in log Analytics worden mogelijk extra kosten in rekening gebracht voor gegevens opslag. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.
> 
> U kunt het recht filterbeleid voor uw abonnementen en werkruimten op vier sets gebeurtenissen worden opgeslagen in uw werkruimte kiezen: 

- **Geen** – gebeurtenisopslag beveiliging uitschakelen. Dit is de standaardinstelling.
- **Minimale** – een kleiner aantal gebeurtenissen voor klanten die willen minimaliseren van het volume van de gebeurtenis.
- **Algemene** : dit is een set van gebeurtenissen die voldoet aan de meeste klanten en kan ze een volledige audittrail.
- **Alle gebeurtenissen** – voor klanten die willen Zorg ervoor dat alle gebeurtenissen worden opgeslagen.


> [!NOTE]
> Deze gebeurtenissen security sets zijn alleen beschikbaar op Standard-laag van Security Center bevinden. Bekijk de pagina [Prijzen](security-center-pricing.md) voor meer informatie over de tariefopties van Security Center.
Deze sets zijn ontworpen voor typische scenario's. Zorg ervoor dat u evalueren welke aan uw behoeften voldoet voordat u deze implementeert.
>
>

Om te bepalen van de gebeurtenissen die tot behoren de **algemene** en **minimale** evenementen, wij werken samen met klanten en industrienormen voor meer informatie over de ongefilterde frequentie van elke gebeurtenis en hun gebruik. We de volgende richtlijnen in dit proces gebruikt:

- **Minimale** -Zorg ervoor dat deze set omvat alleen de gebeurtenissen die kunnen wijzen op een geslaagde inbreuk en belangrijke gebeurtenissen die een zeer lage volume hebt. Deze set bevat bijvoorbeeld de gebruiker geslaagde en mislukte aanmeldingen (gebeurtenis-Id's 4624, 4625), maar bevat geen afmelding die belang rijk is voor controle, maar niet zinvol voor detectie en relatief hoog volume. De meeste van het gegevensvolume van deze set is de aanmeldgebeurtenissen en het maken van procesgebeurtenis (gebeurtenis-ID 4688).
- **Algemene** -bieden een volledige gebruikersaudittrail in deze set. Deze set bevat bijvoorbeeld zowel gebruikers aanmeldingen als gebruikers afmeldingen (gebeurtenis-ID 4634). We nemen controle van acties zoals wijzigingen, belangrijke domain controller Kerberos operations en andere gebeurtenissen die door de bedrijfstak van organisaties worden aanbevolen.

Gebeurtenissen die zeer laag volume hebben zijn opgenomen in de gemeenschappelijke set als de belangrijkste reden om te kiezen dat het over alle gebeurtenissen is dat er minder i en niet voor het filteren van specifieke gebeurtenissen.

Hier volgt een compleet overzicht van de beveiliging en AppLocker gebeurtenis-id's voor elke set:

| Gegevenslaag | Verzamelde gebeurtenis indicatoren |
| --- | --- |
| Minimaal | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Algemeen | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Als u gebruikmaakt van groepsbeleidsobject (GPO), is het aanbevolen dat u controlebeleid proces maken gebeurtenis 4688 inschakelt en de *CommandLine* veld binnen gebeurtenis 4688. Zie voor meer informatie over het proces maken gebeurtenis 4688 van Security Center [Veelgestelde vragen over](security-center-faq.md#what-happens-when-data-collection-is-enabled). Voor meer informatie over deze controlebeleid, Zie [Audit beleid aanbevelingen](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Om gegevens te verzamelen voor [besturingselementen voor adaptieve toepassingen](security-center-adaptive-application.md), Security Center configureert u een lokale AppLocker-beleid in de controlemodus om toe te staan alle toepassingen. Dit zorgt ervoor dat AppLocker voor het genereren van gebeurtenissen die worden verzameld en gebruikt door Security Center. Het is belangrijk te weten dat dit beleid niet worden geconfigureerd op alle computers waarop er al een geconfigureerde AppLocker-beleid is. 
> - Voor het verzamelen van Windows-Filterplatform [gebeurtenis-ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156), moet u inschakelen [Audit Filtering Platform verbinding](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol / / subcategory: 'Filtering Platform verbinding' /Success:Enable)
>

Uw filterbeleid kiezen:
1. Selecteer op de pagina **gegevens verzameling** het filter beleid onder **beveiligings gebeurtenissen**.
2. Selecteer **Opslaan**.

   ![Kies beleid filteren][5]

### Automatische inrichting in het geval van een reeds bestaande Agent installatie<a name="preexisting"></a> 

De volgende gevallen gebruik opgeven hoe automatische inrichting in gevallen wanneer er al een agent of -extensie is geïnstalleerd. 

- Log Analytics agent is geïnstalleerd op de computer, maar niet als een uitbrei ding (direct agent)<br>
Als de Log Analytics-agent rechtstreeks op de virtuele machine is geïnstalleerd (niet als een Azure-extensie), wordt Security Center de Log Analytics agent-extensie geïnstalleerd en kan de Log Analytics agent worden bijgewerkt naar de nieuwste versie.
De agent die is geïnstalleerd, blijft rapporteren aan de al geconfigureerde werk ruimte (n) en rapporteert bovendien aan de werk ruimte die is geconfigureerd in Security Center (multi-multihoming wordt ondersteund op Windows-computers).
Als de geconfigureerde werk ruimte een gebruikers werkruimte is (niet Security Center de standaard werkruimte), moet u de oplossing ' Security/securityFree ' installeren voor Security Center om te beginnen met het verwerken van gebeurtenissen van Vm's en computers die aan die werk ruimte rapporteren.<br>
<br>
Voor Linux-machines wordt agent-multi-multihoming nog niet ondersteund. als er een bestaande agent wordt gedetecteerd, treedt er geen automatische inrichting op en wordt de configuratie van de computer niet gewijzigd.
<br>
Voor bestaande machines op abonnementen die voor Security Center vóór 2019-03-17 worden uitgevoerd, wordt de uitbrei ding van de Log Analytics agent niet geïnstalleerd en wordt de computer niet beïnvloed als er een bestaande agent wordt gedetecteerd. Zie voor deze computers de aanbeveling ' bewakings agent status problemen oplossen op uw computers ' om de installatie problemen van de agent op deze computers op te lossen.

  
- System Center Operations Manager agent is geïnstalleerd op de computer<br>
In Security Center wordt de Log Analytics agent-extensie naast elkaar geïnstalleerd op de bestaande Operations Manager. De bestaande Operations Manager-agent blijft normaal aan de Operations Manager-server rapporteren. Houd er rekening mee dat de Operations Manager agent en Log Analytics agent gemeen schappelijke runtime-bibliotheken delen, die tijdens dit proces worden bijgewerkt naar de meest recente versie.
Opmerking: als Operations Manager agent versie 2012 is geïnstalleerd, **moet u niet** automatisch inrichten inschakelen op.<br>

- Een bestaande VM-extensie is aanwezig<br>
    - Wanneer de bewakings agent is geïnstalleerd als een uitbrei ding, staat de extensie configuratie slechts aan één werk ruimte toe. Security Center wordt de bestaande verbindingen met werkruimten van de gebruiker niet overschreven. Security Center worden beveiligings gegevens van de virtuele machine opgeslagen in de werk ruimte die al is verbonden, op voor waarde dat de oplossing Security of securityFree is geïnstalleerd. Security Center kunt de extensie versie in dit proces upgraden naar de meest recente versie.  
    - Om te zien op welke werkruimte de bestaande extensie verzendt gegevens naar de test uit te voeren [connectiviteit met Azure Security Center valideren](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/). U kunt ook Log Analytics-werk ruimten openen, een werk ruimte selecteren, de virtuele machine selecteren en kijken naar de Log Analytics agent-verbinding. 
    - Als u een omgeving hebt waarin de Log Analytics-agent is geïnstalleerd op client werkstations en rapportage aan een bestaande Log Analytics-werk ruimte, raadpleegt u de lijst met [besturings systemen die worden ondersteund door Azure Security Center](security-center-os-coverage.md) om ervoor te zorgen dat uw besturings systeem geboden. Zie [bestaande log Analytics-klanten](security-center-faq.md#existingloganalyticscust)voor meer informatie.
 
### Automatische inrichting uitschakelen <a name="offprovisioning"></a>
U kunt automatische inrichting van resources op elk gewenst moment door het uitschakelen van deze instelling in het beveiligingsbeleid uitschakelen. 


1. Ga terug naar het hoofdmenu van Security Center en selecteert u het beveiligingsbeleid.
2. Klik op **Instellingen bewerken** in de rij van het abonnement waarvoor u automatisch inrichten wilt uitschakelen.
3. Op de **beveiligingsbeleid – gegevensverzameling** blade onder **automatische inrichting** Selecteer **uit**.
4. Selecteer **Opslaan**.

   ![Automatische inrichting uitschakelen][6]

Wanneer automatische inrichting is uitgeschakeld (uitgeschakeld), wordt de configuratiesectie van de standaard-werkruimte niet weergegeven.

Als u automatisch inrichten na uitschakelen voorheen op:
-   Agents worden niet ingericht op nieuwe virtuele machines.
-   Security Center stopt het verzamelen van gegevens van de standaardwerkruimte.
 
> [!NOTE]
>  Als u automatische inrichting uitschakelt, wordt de Log Analytics-agent niet verwijderd van Azure-Vm's waar de agent is ingericht. Zie voor meer informatie over het verwijderen van de OMS-extensie [hoe verwijder ik OMS-extensies geïnstalleerd door Security Center](security-center-faq.md#remove-oms).
>
    
## Handmatige configuratie van agent <a name="manual-agent"></a>
 
Er zijn verschillende manieren om de Log Analytics agent hand matig te installeren. Als u handmatig installeert, zorg er dan voor dat u de automatische inrichting uitschakelt.

### <a name="operations-management-suite-vm-extension-deployment"></a>Implementatie van operations Management Suite VM-extensie 

U kunt de Log Analytics-agent hand matig installeren, zodat Security Center beveiligings gegevens van uw virtuele machines kan verzamelen en aanbevelingen en waarschuwingen kunt geven.
1. Selecteer automatisch inrichten – uit.
2. Maak een werk ruimte en stel de prijs categorie in voor de werk ruimte die u wilt instellen voor de Log Analytics-agent:

   a.  Selecteer in het hoofdmenu van Security Center **beveiligingsbeleid**.
     
   b.  Selecteer de werkruimte waarin u van plan bent om de agent verbinding te maken. Zorg ervoor dat de werkruimte zich in hetzelfde abonnement u in Security Center en dat u lees-/ schrijfmachtigingen beschikt in de werkruimte hebben.
       ![Selecteer een werkruimte][8]
3. De prijscategorie instellen.
   ![Prijscategorie selecteren][9] 
   >[!NOTE]
   >Als de werkruimte is al een **Security** of **SecurityCenterFree** oplossing is ingeschakeld, de prijzen wordt automatisch ingesteld. 
   > 

4. Als u de agents op de nieuwe virtuele machines met behulp van Resource Manager-sjabloon te implementeren wilt, installeert u de extensie van de OMS-virtuele machine:

   a.  [De extensie van de OMS-virtuele machine voor Windows installeren](../virtual-machines/extensions/oms-windows.md)
    
   b.  [De extensie van de OMS-virtuele machine voor Linux installeren](../virtual-machines/extensions/oms-linux.md)
5. Volg de instructies in voor het implementeren van de extensies op bestaande virtuele machines, [gegevens verzamelen over Azure Virtual Machines](../azure-monitor/learn/quick-collect-azurevm.md).

   > [!NOTE]
   > De sectie **gebeurtenissen en prestatiegegevens verzamelen** is optioneel.
   >
6. Als u PowerShell wilt implementeren de extensie, gebruik de volgende PowerShell-voorbeeld:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Ga naar **Log Analytics** en klikt u op **geavanceerde instellingen**.
    
      ![Set-logboekanalyse][11]

   2. Kopieer de waarden van **WorkspaceID** en **primaire sleutel**.
  
      ![Waarden kopiëren][12]

   3. De openbare configuratie en de persoonlijke configuratie met deze waarden vullen:
     
           $PublicConf = '{
               "workspaceId": "WorkspaceID value"
           }' 
 
           $PrivateConf = '{
               "workspaceKey": "<Primary key value>”
           }' 

      - Wanneer u installeert op een Windows-VM:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Wanneer u installeert op een Linux-VM:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Voor instructies over hoe onboarding Security Center met behulp van PowerShell, Zie [automatiseren onboarding van Azure Security Center met behulp van PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Problemen oplossen

-   Zie voor het identificeren van problemen met de installatie automatisch inrichten, [problemen met de status van de agentstatus](security-center-troubleshooting-guide.md#mon-agent).

-  Zie voor het identificeren van de netwerkvereisten voor monitoring agent [probleemoplossing monitoring agent netwerkvereisten](security-center-troubleshooting-guide.md#mon-network-req).
-   Zie voor het identificeren van handmatige onboarding-problemen, [Operations Management Suite-onboarding oplossen](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Zie voor het identificeren van niet-bewaakte VM's en computers problemen, [niet-bewaakte VM's en computers](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers).

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd hoe gegevens verzamelen en automatische inrichting in Security Center werkt. Zie de volgende onderwerpen voor meer informatie over het Beveiligingscentrum:

* [Azure Security Center FAQ](security-center-faq.md): raadpleeg veelgestelde vragen over het gebruik van de service.
* [Beveiligingsstatus bewaken in Azure Security Center](security-center-monitoring.md): meer informatie over het bewaken van de status van uw Azure-resources.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
