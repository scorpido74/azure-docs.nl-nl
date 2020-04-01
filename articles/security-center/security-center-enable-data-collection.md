---
title: Gegevensverzameling in Azure Security Center | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u een Log Analytics Agent installeert en een Log Analytics-werkruimte instelt waarin de verzamelde gegevens kunnen worden opgeslagen.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 61d0a57c541837ab3aebf65e47d757f7ecbe7e40
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435985"
---
# <a name="data-collection-in-azure-security-center"></a>Gegevensverzameling in Azure Security Center
Security Center verzamelt gegevens van uw Virtuele Azure-machines (VM's), virtuele machineschaalsets, IaaS-containers en niet-Azure-computers (inclusief on-premises) computers om te controleren op beveiligingsproblemen en bedreigingen. Gegevens worden verzameld met behulp van de Log Analytics Agent, die verschillende beveiligingsconfiguraties en gebeurtenislogboeken van de machine leest en de gegevens kopieert naar uw werkruimte voor analyse. Voorbeelden van dergelijke gegevens zijn: type en versie van het besturingssysteem, logboeken van het besturingssysteem (Windows-gebeurtenislogboeken), het uitvoeren van processen, machinenaam, IP-adressen en ingelogde gebruiker. De Log Analytics Agent kopieert ook crashdumpbestanden naar uw werkruimte.

Het verzamelen van gegevens is vereist om inzicht te bieden in ontbrekende updates, verkeerd geconfigureerde beveiligingsinstellingen voor het besturingssysteem, de status van endpointbeveiliging en bescherming tegen gezondheid en bedreigingen. 

In dit artikel wordt beschreven hoe u een Log Analytics Agent installeert en een Log Analytics-werkruimte instelt waarin de verzamelde gegevens kunnen worden opgeslagen. Beide bewerkingen zijn nodig om het verzamelen van gegevens mogelijk te maken. 

> [!NOTE]
> - Gegevensverzameling is alleen nodig voor Compute-resources (VM's, virtuele machineschaalsets, IaaS-containers en niet-Azure-computers). U profiteren van Azure Security Center, zelfs als u geen agents indient. u hebt echter beperkte beveiliging en de hierboven vermelde mogelijkheden worden niet ondersteund.  
> - Zie [Ondersteunde platforms in Azure Security Center](security-center-os-coverage.md)voor de lijst met ondersteunde platforms.
> - Het opslaan van gegevens in Log Analytics, of u nu een nieuwe of bestaande werkruimte gebruikt, kan extra kosten met zich meebrengen voor gegevensopslag. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

## <a name="enable-automatic-provisioning-of-the-log-analytics-agent"></a>Automatische inrichting van de Log Analytics-agent inschakelen<a name="auto-provision-mma"></a>

Als u de gegevens van de machines wilt verzamelen, moet u de Log Analytics-agent hebben geïnstalleerd. Installatie van de agent kan automatisch worden gedaan (aanbevolen) of u de agent handmatig installeren.  

>[!NOTE]
> Automatische inrichting is standaard uitgeschakeld. Als u Beveiligingscentrum wilt instellen om automatische inrichting standaard te installeren, stelt u deze in **op Aan**.
>

Wanneer automatische inrichting is ingeschakeld, voorziet Security Center in de Log Analytics Agent op alle ondersteunde Azure VM's en eventuele nieuwe die zijn gemaakt. Automatische inlevering wordt sterk aanbevolen, maar handmatige agent installatie is ook beschikbaar. [Meer informatie over het installeren van de logboekanalyseagent-extensie](#manual-agent).



Ga als eerste over een automatische inrichting van de Log Analytics-agent:
1. Selecteer onder het hoofdmenu Van het Beveiligingscentrum de optie **Prijzen & instellingen**.
2. Klik op het toepasselijke abonnement

   ![Abonnement selecteren][7]

3. Selecteer **Gegevensverzameling**.
4. Selecteer **Onder Automatisch inrichten**de optie **Op** om automatische inrichting in te schakelen.
5. Selecteer **Opslaan**.

   ![Automatische inrichting inschakelen][1]

>[!NOTE]
> - Zie [Automatische inrichting in geval van een reeds bestaande agentinstallatie](#preexisting)voor instructies voor het inrichten van een reeds bestaande installatie.
> - Zie [De extensie Log-Analyseagent handmatig installeren](#manual-agent)voor instructies over handmatige inrichten.
> - Zie [Automatische inrichting uitschakelen](#offprovisioning)voor instructies voor het uitschakelen van automatische inrichting.
> - Zie [Onboarding van Azure Security Center automatiseren met PowerShell](security-center-powershell-onboarding.md)voor instructies over het instappen van beveiligingscentrum met PowerShell.
>

## <a name="workspace-configuration"></a>Werkruimteconfiguratie
Gegevens die worden verzameld door Security Center worden opgeslagen in een of meer werkruimten van Log Analytics. U ervoor kiezen om gegevens te laten verzamelen uit Azure VM's die zijn opgeslagen in werkruimten die zijn gemaakt door het Beveiligingscentrum of in een bestaande werkruimte die u hebt gemaakt. 

Werkruimteconfiguratie is ingesteld per abonnement en veel abonnementen kunnen dezelfde werkruimte gebruiken.

### <a name="using-a-workspace-created-by-security-center"></a>Een werkruimte gebruiken die is gemaakt door beveiligingscentrum

Beveiligingscentrum kan automatisch een standaardwerkruimte maken waarin de gegevens kunnen worden opgeslagen. 

Ga als lid van het beveiligingscentrum naar een werkruimte die is gemaakt door Security Center:

1. Selecteer **onder Standaardwerkruimteconfiguratie**de optie Werkruimte(s) gebruiken die is gemaakt door beveiligingscentrum.
   ![Prijscategorie selecteren][10] 

1. Klik op **Opslaan**.<br>
    Security Center maakt een nieuwe resourcegroep en standaardwerkruimte in die geolocatie en verbindt de agent met die werkruimte. De naamgevingsconventie voor de werkruimte- en resourcegroep is:<br>
   **Werkruimte: DefaultWorkspace-[subscription-ID]-[geo]<br> Resource Group: DefaultResourceGroup-[geo]**

   Als een abonnement VM's van meerdere geolocaties bevat, maakt Beveiligingscentrum meerdere werkruimten. Er worden meerdere werkruimten gemaakt om de privacyregels voor gegevens te handhaven.
1. Security Center schakelt automatisch een Security Center-oplossing in op de werkruimte volgens de prijscategorie die voor het abonnement is ingesteld. 

> [!NOTE]
> De prijscategorie Log Analytics van werkruimten die door Security Center zijn gemaakt, heeft geen invloed op de facturering van het Beveiligingscentrum. De facturering voor Security Center is altijd gebaseerd op uw Security Center-beveiligingsbeleid en de oplossingen die in een werkruimte zijn geïnstalleerd. Voor de gratis versie schakelt Azure Security Center de oplossing *SecurityCenterFree* in voor de standaardwerkruimte. Voor de standaardversie schakelt Azure Security Center de oplossing *Security* in voor de standaardwerkruimte.
> Het opslaan van gegevens in Log Analytics kan extra kosten met zich meebrengen voor gegevensopslag. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

Zie [Bestaande loganalytics-klanten](./faq-azure-monitor-logs.md)voor meer informatie over bestaande loganalytics-accounts.

### <a name="using-an-existing-workspace"></a>Een bestaande werkruimte gebruiken

Als u al een bestaande Log Analytics-werkruimte hebt, u dezelfde werkruimte gebruiken.

Als u uw bestaande Log Analytics-werkruimte wilt gebruiken, moet u lees- en schrijfmachtigingen op de werkruimte hebben.

> [!NOTE]
> Oplossingen die zijn ingeschakeld voor de bestaande werkruimte, worden toegepast op Azure VM's die ermee zijn verbonden. Voor betaalde oplossingen kan dit leiden tot extra kosten. Controleer voor privacyoverwegingen of uw geselecteerde werkruimte zich in de juiste geografische regio bevindt.
> Het opslaan van gegevens in logboekanalyses kan extra kosten met zich meebrengen voor gegevensopslag. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

Ga als u een bestaande loganalytics-werkruimte selecteert:

1. Selecteer **onder Standaardwerkruimteconfiguratie**de optie **Een andere werkruimte gebruiken**.

   ![Bestaande werkruimte selecteren][2]

2. Selecteer in het keuzemenu een werkruimte om verzamelde gegevens op te slaan.

   > [!NOTE]
   > In het pull down-menu zijn alle werkruimten voor al uw abonnementen beschikbaar. Zie [selectie van werkruimtes voor meerdere abonnementen](security-center-enable-data-collection.md#cross-subscription-workspace-selection) voor meer informatie. U moet toestemming hebben om toegang te krijgen tot de werkruimte.
   >
   >

3. Selecteer **Opslaan**.
4. Nadat u **Opslaan**hebt geselecteerd, wordt u gevraagd of u bewaakte VM's wilt opnieuw configureren die eerder zijn verbonden met een standaardwerkruimte.

   - Selecteer **Nee** als u de nieuwe werkruimte-instellingen alleen op nieuwe VM's wilt toepassen. De nieuwe werkruimte-instellingen zijn alleen van toepassing op nieuwe agentinstallaties; nieuw ontdekte VM's die de Log Analytics Agent niet hebben geïnstalleerd.
   - Selecteer **Ja** als u de nieuwe werkruimte-instellingen wilt toepassen op alle VM's. Bovendien wordt elke VM die is aangesloten op een door beveiligingscentrum gemaakte werkruimte opnieuw verbonden met de nieuwe doelwerkruimte.

   > [!NOTE]
   > Als u Ja selecteert, mag u de werkruimte(s) die door Security Center zijn gemaakt, niet verwijderen totdat alle VM's opnieuw zijn verbonden met de nieuwe doelwerkruimte. Deze bewerking mislukt als een werkruimte te vroeg wordt verwijderd.
   >
   >

   - Selecteer **Annuleren** om de bewerking te annuleren.

     ![Bestaande werkruimte selecteren][3]

5. Selecteer de prijscategorie voor de gewenste werkruimte die u wilt instellen voor de Log Analytics Agent. <br>Als u een bestaande werkruimte wilt gebruiken, stelt u de prijscategorie voor de werkruimte in. Hiermee wordt een beveiligingscentrumoplossing op de werkruimte geïnstalleerd als deze nog niet aanwezig is.

    a.  Selecteer in het hoofdmenu Van het Beveiligingscentrum de optie **Prijzen & instellingen**.
     
    b.  Selecteer de gewenste werkruimte waarin u de agent wilt verbinden.
        ![Selecteer][7] werkruimte c. Stel de prijscategorie in.
        ![Prijscategorie selecteren][9]
   
   >[!NOTE]
   >Als de werkruimte al een **beveiligings-** of **SecurityCenterFree-oplossing** heeft ingeschakeld, worden de prijzen automatisch ingesteld. 

## <a name="cross-subscription-workspace-selection"></a>Selectie van werkruimtes voor meerdere abonnementen
Wanneer u een werkruimte selecteert waarin u uw gegevens wilt opslaan, zijn alle werkruimten voor al uw abonnementen beschikbaar. Het voordeel hiervan is dat u gegevens kunt verzamelen van virtuele machines die in verschillende abonnementen worden uitgevoerd en deze kunt opslaan in de werkruimte van uw keuze. Deze optie is handig als u een centrale werkruimte gebruikt in uw organisatie en u deze ook wilt gebruiken voor het verzamelen van beveiligingsgegevens. Zie [Werkruimtetoegang beheren](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)voor meer informatie over het beheren van werkruimten.


## <a name="data-collection-tier"></a>Laag gegevensverzameling
Het selecteren van een laag voor gegevensverzameling in Azure Security Center heeft alleen invloed op de opslag van beveiligingsgebeurtenissen in de Log Analytics-werkruimte. De loganalytics-agent verzamelt en analyseert nog steeds de beveiligingsgebeurtenissen die nodig zijn voor de bedreigingsbeveiliging van Azure Security Center, ongeacht welke beveiligingslaag u (indien van toepassing) in uw log analytics-werkruimte wilt opslaan. Als u ervoor kiest om beveiligingsgebeurtenissen op te slaan in uw werkruimte, kunt u gebeurtenissen vanuit die werkruimte onderzoeken, zoeken en controleren. 
> [!NOTE]
> Het opslaan van gegevens in logboekanalyses kan extra kosten met zich meebrengen voor gegevensopslag. Zie de pagina [prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.
> 
> U het juiste filterbeleid kiezen voor uw abonnementen en werkruimten uit vier reeksen gebeurtenissen die in uw werkruimte moeten worden opgeslagen: 

- **Geen** – Opslag van beveiligingsgebeurtenissen uitschakelen. Dit is de standaardinstelling.
- **Minimaal** : een kleinere set evenementen voor klanten die het evenementvolume willen minimaliseren.
- **Vaak -** Dit is een reeks gebeurtenissen die de meeste klanten tevreden stelt en hen een volledig controlespoor toestaat.
- **Alle evenementen** – Voor klanten die ervoor willen zorgen dat alle evenementen worden opgeslagen.


> [!NOTE]
> Deze sets voor beveiligingsgebeurtenissen zijn alleen beschikbaar op de standaardlaag van het Beveiligingscentrum. Bekijk de pagina [Prijzen](security-center-pricing.md) voor meer informatie over de tariefopties van Security Center.
Deze sets zijn ontworpen om typische scenario's aan te pakken. Zorg ervoor dat u evalueert welke voldoet aan uw behoeften voordat u het implementeert.
>
>

Om de gebeurtenissen te bepalen die deel uitmaken van de **gemeenschappelijke** en **minimale** gebeurtenissets, hebben we samengewerkt met klanten en industriestandaarden om meer te weten te komen over de ongefilterde frequentie van elk evenement en het gebruik ervan. We gebruikten de volgende richtlijnen in dit proces:

- **Minimaal** - Zorg ervoor dat deze set alleen gebeurtenissen omvat die kunnen duiden op een geslaagde schending en belangrijke gebeurtenissen met een zeer laag volume. Deze set bevat bijvoorbeeld een geslaagde en mislukte login voor gebruikers (gebeurtenis-id's 4624, 4625), maar bevat geen afmelding die belangrijk is voor controle, maar niet zinvol voor detectie en een relatief hoog volume heeft. Het grootste deel van het gegevensvolume van deze set is de aanmeldingsgebeurtenissen en gebeurtenis voor het maken van processen (gebeurtenis-id 4688).
- **Algemeen -** Geef een volledig gebruikerscontrolespoor in deze set. Deze set bevat bijvoorbeeld zowel aanmeldingen van gebruikers als afmelden voor gebruikers (gebeurtenis-id 4634). We omvatten controleacties zoals wijzigingen in beveiligingsgroepen, Kerberos-bewerkingen voor belangrijke domeinen en andere gebeurtenissen die worden aanbevolen door brancheorganisaties.

Gebeurtenissen met een zeer laag volume werden opgenomen in de gemeenschappelijke set als de belangrijkste motivatie om het te kiezen over alle gebeurtenissen is om het volume te verminderen en niet te filteren op specifieke gebeurtenissen.

Hier is een volledige uitsplitsing van de security en App Locker gebeurtenis-iDs voor elke set:

| Gegevenslaag | Verzamelde gebeurtenis-indicatoren |
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
> - Als u Groepsbeleidsobject (GPO) gebruikt, wordt aanbevolen om het controlebeleid Process Creation Event 4688 en het *veld CommandLine* in gebeurtenis 4688 in te schakelen. Zie [De veelgestelde vragen](faq-data-collection-agents.md#what-happens-when-data-collection-is-enabled)van Security Center voor meer informatie over Process Creation Event 4688. Zie [Aanbevelingen voor het auditbeleid voor](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)meer informatie over dit controlebeleid.
> -  Als u gegevensverzameling voor [adaptieve toepassingsbesturingselementen](security-center-adaptive-application.md)wilt inschakelen, configureert Beveiligingscentrum een lokaal AppLocker-beleid in de auditmodus om alle toepassingen toe te staan. Dit zal ertoe leiden dat AppLocker gebeurtenissen genereert die vervolgens worden verzameld en benut door Security Center. Het is belangrijk op te merken dat dit beleid niet wordt geconfigureerd op machines waarop al een geconfigureerd AppLocker-beleid is. 
> - Als u Windows Filtering Platform [Event ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)wilt verzamelen, moet u [auditfilteringplatformverbinding](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) inschakelen (Auditpol /set /subcategorie:"Filtering Platform Connection" /Succes:Inschakelen)
>

Ga als u uw filterbeleid kiezen:
1. Selecteer op de pagina **Gegevensverzameling** uw filterbeleid onder **Beveiligingsgebeurtenissen**.
2. Selecteer **Opslaan**.

   ![Filterbeleid kiezen][5]

### <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>Automatische inrichting in geval van een reeds bestaande agentinstallatie<a name="preexisting"></a> 

In de volgende use cases wordt aangegeven hoe automatische voorzieningen werken in gevallen waarin er al een agent of extensie is geïnstalleerd. 

- Log Analytics Agent is geïnstalleerd op de machine, maar niet als een extensie (Direct agent)<br>
Als de Log Analytics Agent rechtstreeks op de VM is geïnstalleerd (niet als Azure-extensie), installeert Beveiligingscentrum de extensie Log Analytics Agent en kan de Log Analytics Agent worden geüupgradet naar de nieuwste versie.
De geïnstalleerde agent blijft rapporteren aan de reeds geconfigureerde werkruimte(s) en rapporteert bovendien aan de werkruimte die is geconfigureerd in Security Center (Multi-homing wordt ondersteund op Windows-machines).
Als de geconfigureerde werkruimte een gebruikerswerkruimte is (niet de standaardwerkruimte van het Beveiligingscentrum), moet u de oplossing 'beveiliging/'securityfree erop installeren zodat Beveiligingscentrum gebeurtenissen kan starten met het verwerken van gebeurtenissen van VM's en computers die naar die werkruimte rapporteren.<br>
<br>
Voor Linux-machines wordt Agent multi-homing nog niet ondersteund - dus als een bestaande agentinstallatie wordt gedetecteerd, zal automatische provisioning niet plaatsvinden en wordt de configuratie van de machine niet gewijzigd.
<br>
Voor bestaande machines op abonnementen die vóór 2019-03-17 zijn aangesloten bij abonnementen op beveiligingscentrum, wanneer een bestaande agent wordt gedetecteerd, wordt de log-analyseagent-extensie niet geïnstalleerd en wordt de machine niet beïnvloed. Zie voor deze machines de aanbeveling 'Gezondheidsproblemen met bewakingsagent op uw machines oplossen' om de problemen met de installatie van de agent op deze machines op te lossen.

  
- System Center Operations Manager-agent is op de machine geïnstalleerd<br>
Beveiligingscentrum installeert de extensie Log Analytics Agent naast de bestaande Operations Manager. De bestaande operations manager-agent blijft normaal rapporteren aan de Operations Manager-server. Houd er rekening mee dat de Operations Manager-agent en de Log Analytics Agent algemene runtime-bibliotheken delen, die tijdens dit proces worden bijgewerkt naar de nieuwste versie.
Opmerking - Als Operations Manager-agent versie 2012 is geïnstalleerd, schakelt u de automatische inrichting **niet** in.<br>

- Er is een reeds bestaande VM-extensie aanwezig<br>
    - Wanneer de bewakingsagent als extensie is geïnstalleerd, kan met de extensieconfiguratie slechts één werkruimte worden gemeld. Security Center overschrijft bestaande verbindingen met gebruikerswerkruimten niet. Security Center slaat beveiligingsgegevens van de VM op in de werkruimte die al is aangesloten, op voorwaarde dat de 'security' of 'securityFree'-oplossing erop is geïnstalleerd. Security Center kan de extensieversie in dit proces upgraden naar de nieuwste versie.  
    - Als u wilt zien naar welke werkruimte de bestaande extensie gegevens verzendt, voert u de test uit naar [Validatie van de connectiviteit met Azure Security Center.](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/) U ook Logboekanalysewerkruimten openen, een werkruimte selecteren, de VM selecteren en de koppeling van de log-analyse-agent bekijken. 
    - Als u een omgeving hebt waarin de Log Analytics-agent is geïnstalleerd op clientwerkstations en rapporteert aan een bestaande Log Analytics-werkruimte, bekijkt u de lijst met [besturingssystemen die worden ondersteund door Azure Security Center](security-center-os-coverage.md) om te controleren of uw besturingssysteem wordt ondersteund. Zie [Bestaande klanten voor logboekanalyse](./faq-azure-monitor-logs.md)voor meer informatie.
 
### <a name="turn-off-automatic-provisioning"></a>Automatische inrichting uitschakelen<a name="offprovisioning"></a>
U automatische voorzieningen uit resources op elk gewenst moment uitschakelen door deze instelling uit te schakelen in het beveiligingsbeleid. 


1. Ga terug naar het hoofdmenu van het Beveiligingscentrum en selecteer het beveiligingsbeleid.
2. Klik op **Instellingen bewerken** in de rij van het abonnement waarvoor u automatische inrichting wilt uitschakelen.
3. Ga naar het **beveiligingsbeleid – Het** blad Gegevensverzameling onder **Automatisch inrichten** selecteren **.**
4. Selecteer **Opslaan**.

   ![Automatische inrichting uitschakelen][6]

Wanneer automatische inrichting is uitgeschakeld (uitgeschakeld), wordt de sectie standaardwerkruimteconfiguratie niet weergegeven.

Als u automatische voorziening uitschakelt nadat deze eerder was ingeschakeld:
-   Agenten zullen niet worden ingericht op nieuwe VM's.
-   Security Center stopt met het verzamelen van gegevens uit de standaardwerkruimte.
 
> [!NOTE]
>  Als u de automatische inrichting uitschakelt, wordt de logboekanalyseagent niet verwijderd uit Azure VM's waar de agent is ingericht. Zie [Hoe verwijder ik OMS-extensies die zijn geïnstalleerd door Security Center voor](faq-data-collection-agents.md#remove-oms)informatie over het verwijderen van de OMS-extensie.
>
    
## <a name="manual-agent-provisioning"></a>Handmatige agentprovisioning<a name="manual-agent"></a>
 
Er zijn verschillende manieren om de Log Analytics Agent handmatig te installeren. Controleer bij het handmatig installeren of u automatische inrichting uitschakelt.

### <a name="operations-management-suite-vm-extension-deployment"></a>Implementatie van Operations Management Suite VM-extensie 

U de Log Analytics Agent handmatig installeren, zodat Security Center beveiligingsgegevens van uw VM's kan verzamelen en aanbevelingen en waarschuwingen kan geven.
1. Selecteer Automatische inlevering – UIT.
2. Maak een werkruimte en stel de prijscategorie in voor de werkruimte die u wilt instellen:

   a.  Selecteer in het hoofdmenu Beveiligingscentrum de optie **Beveiligingsbeleid**.
     
   b.  Selecteer de werkruimte waarin u de agent wilt verbinden. Zorg ervoor dat de werkruimte zich in hetzelfde abonnement bevindt dat u in Security Center gebruikt en dat u lees-/schrijfmachtigingen op de werkruimte hebt.
       ![Werkruimte selecteren][8]
3. Stel de prijscategorie in.
   ![Prijscategorie selecteren][9] 
   >[!NOTE]
   >Als de werkruimte al een **beveiligings-** of **SecurityCenterFree-oplossing** heeft ingeschakeld, worden de prijzen automatisch ingesteld. 
   > 

4. Als u de agents op nieuwe VM's wilt implementeren met behulp van een resourcemanagersjabloon, installeert u de extensie van de VIRTUELE-machine van OMS:

   a.  [De OMS-extensie voor virtuele machines voor Windows installeren](../virtual-machines/extensions/oms-windows.md)
    
   b.  [De OMS-extensie voor virtuele machines voor Linux installeren](../virtual-machines/extensions/oms-linux.md)
5. Als u de extensies op bestaande VM's wilt implementeren, volgt u de instructies in [Gegevens verzamelen over Virtuele Azure-machines.](../azure-monitor/learn/quick-collect-azurevm.md)

   > [!NOTE]
   > De sectie **Gebeurtenis- en prestatiegegevens verzamelen** is optioneel.
   >
6. Als u PowerShell wilt gebruiken om de extensie te implementeren, gebruikt u het volgende PowerShell-voorbeeld:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Ga naar **Log Analytics** en klik op **Geavanceerde instellingen**.
    
      ![Logboekanalyses instellen][11]

   2. Kopieer de waarden uit **WorkspaceID** en **Primaire sleutel**.
  
      ![Waarden kopiëren][12]

   3. Bevolk de openbare config en de particuliere config met deze waarden:
     
           $PublicConf = @{
               "workspaceId"= "<WorkspaceID value>"
           }
 
           $PrivateConf = @{
               "workspaceKey"= "<Primary key value>"
           }

      - Bij het installeren op een Windows-vm:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -settings $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Bij het installeren op een Linux VM:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Zie [Onboarding van Azure Security Center automatiseren met PowerShell](security-center-powershell-onboarding.md)voor instructies over het instappen van beveiligingscentrum met PowerShell.

## <a name="troubleshooting"></a>Problemen oplossen

-   Zie Problemen met de [status van de agent controleren](security-center-troubleshooting-guide.md#mon-agent)om problemen met automatische installatieproblemen voor voorzieningen te identificeren.

-  Zie Netwerkvereisten voor [probleemoplossingsbewaking.](security-center-troubleshooting-guide.md#mon-network-req)
-   Zie Problemen met het [instappen](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)van Operations Management Suite oplossen om problemen met handmatige onboarding te identificeren.

- Ga als volgt te werk om problemen met niet-bewaakte VM's en computers te identificeren:

    Een VM of computer wordt niet gecontroleerd door Security Center als de machine de extensie Log Analytics-agent niet uitvoert. Een machine kan al een lokale agent hebben geïnstalleerd, bijvoorbeeld de OMS-direct agent of de System Center Operations Manager-agent. Machines met deze agents worden geïdentificeerd als niet-gecontroleerd omdat deze agents niet volledig worden ondersteund in Security Center. Om volledig te kunnen profiteren van alle mogelijkheden van het Security Center, is de extensie van de Log Analytics-agent vereist.

    Zie [Statusproblemen](security-center-troubleshooting-guide.md#mon-agent)van de agent controleren voor meer informatie over de redenen waarom Beveiligingscentrum niet kan controleren dat vm's en computers zijn geïnitialiseerd voor automatische provisioning.


## <a name="next-steps"></a>Volgende stappen
In dit artikel ziet u hoe het verzamelen en automatisch inrichten van gegevens in Security Center werkt. Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Azure Security Center FAQ](faq-general.md): raadpleeg veelgestelde vragen over het gebruik van de service.
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
