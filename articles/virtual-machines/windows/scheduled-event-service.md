---
title: Geplande gebeurtenissen voor uw Windows VM's in Azure bewaken
description: Meer informatie over het bewaken van uw virtuele Azure-machines voor geplande gebeurtenissen.
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073315"
---
# <a name="monitoring-scheduled-events"></a>Geplande gebeurtenissen bewaken

Updates worden elke dag toegepast op verschillende delen van Azure, om de services op deze services veilig en up-to-date te houden. Naast geplande updates kunnen er ook ongeplande gebeurtenissen plaatsvinden. Als er bijvoorbeeld hardwaredegradatie of -fout wordt gedetecteerd, moeten Azure-services mogelijk ongepland onderhoud uitvoeren. Met behulp van live migratie, geheugen behoud van updates en in het algemeen het bijhouden van een strikte bar op de impact van updates, in de meeste gevallen deze gebeurtenissen zijn bijna transparant voor klanten, en ze hebben geen invloed of hooguit leiden tot een paar seconden van virtuele machine bevriezen. Echter, voor sommige toepassingen, zelfs een paar seconden van virtuele machine bevriezen kan een impact veroorzaken. Vooraf weten over aankomend Azure-onderhoud is belangrijk om de beste ervaring voor die toepassingen te garanderen. [Scheduled Events service](scheduled-events.md) biedt u een programmatische interface om op de hoogte te worden gesteld van aankomend onderhoud, en stelt u in staat om het onderhoud op een elegante manier af te handelen. 

In dit artikel laten we zien hoe u geplande gebeurtenissen gebruiken om op de hoogte te worden gesteld van onderhoudsgebeurtenissen die van invloed kunnen zijn op uw VM's en een basisautomatisering bouwen die kan helpen bij monitoring en analyse.


## <a name="routing-scheduled-events-to-log-analytics"></a>Geplande gebeurtenissen routeren naar Log Analytics

Geplande gebeurtenissen zijn beschikbaar als onderdeel van de [Azure Instance Metadata Service](instance-metadata-service.md), die beschikbaar is op elke virtuele Azure-machine. Klanten kunnen automatisering schrijven om het eindpunt van hun virtuele machines op te vragen om geplande onderhoudsmeldingen te vinden en oplossingen uit te voeren, zoals het opslaan van de status en het uit de rotatie halen van de virtuele machine. We raden gebouwautomatisering aan om de geplande gebeurtenissen vast te leggen, zodat u een controlelogboek van Azure-onderhoudsgebeurtenissen hebben. 

In dit artikel nemen we je mee door hoe je geplande onderhoudsgebeurtenissen vastleggen om Analytics te registreren. Vervolgens activeren we een aantal basismeldingsacties, zoals het verzenden van een e-mail naar uw team en het krijgen van een historisch overzicht van alle gebeurtenissen die van invloed zijn geweest op uw virtuele machines. Voor de gebeurtenisaggregatie en automatisering gebruiken we [Log Analytics,](/azure/azure-monitor/learn/quick-create-workspace)maar u elke bewakingsoplossing gebruiken om deze logboeken te verzamelen en automatisering te activeren.

![Diagram met de levenscyclus van gebeurtenissen](./media/notifications/events.png)

## <a name="prerequisites"></a>Vereisten

In dit voorbeeld moet u een [Virtuele Windows-machine maken in een beschikbaarheidsset.](tutorial-availability-sets.md) Geplande gebeurtenissen bieden meldingen over wijzigingen die van invloed kunnen zijn op een van de virtuele machines in uw beschikbaarheidsset, Cloud Service, Virtual Machine Scale Set of standalone VM's. We zullen een [dienst](https://github.com/microsoft/AzureScheduledEventsService) uitvoeren die polls voor geplande evenementen op een van de VM's die zal fungeren als een verzamelaar, om evenementen te krijgen voor alle andere VM's in de beschikbaarheid set.    

Verwijder de groepresourcegroep niet aan het einde van de zelfstudie.

U moet ook [een Log Analytics-werkruimte maken](/azure/azure-monitor/learn/quick-create-workspace) die we gebruiken om informatie uit de VM's in de beschikbaarheidsset samen te voegen.

## <a name="set-up-the-environment"></a>De omgeving instellen

U moet nu 2 initiële VM's in een beschikbaarheidsset hebben. Nu moeten we een 3e VM maken, genaamd myCollectorVM, in dezelfde beschikbaarheidsset. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Download het installatiebestand .zip van het project van [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Maak verbinding met **myCollectorVM** en kopieer het .zip-bestand naar de virtuele machine en haal alle bestanden eruit. Open op uw VM een PowerShell-prompt. Verplaats uw prompt naar `SchService.ps1`de map `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`met bijvoorbeeld: , en stel de service in.

```powershell
.\SchService.ps1 -Setup
```

Start de service.

```powershell
.\SchService.ps1 -Start
```

De service begint nu elke 10 seconden met het peilen voor geplande gebeurtenissen en keurt de gebeurtenissen goed om het onderhoud te versnellen.  Freeze, Reboot, Redeploy en Preempt zijn de gebeurtenissen die zijn vastgelegd door Gebeurtenissen plannen.   Houd er rekening mee dat u het script uitbreiden om bepaalde oplossingen te activeren voordat u de gebeurtenis goedkeurt.

Valideer de servicestatus en zorg ervoor dat deze wordt uitgevoerd.

```powershell
.\SchService.ps1 -status  
```

Dit moet `Running`terugkeren.

De service begint nu elke 10 seconden met het peilen voor geplande gebeurtenissen en keurt de gebeurtenissen goed om het onderhoud te versnellen.  Freeze, Reboot, Redeploy en Preempt zijn de gebeurtenissen die zijn vastgelegd door Gebeurtenissen plannen. U het script uitbreiden om een aantal oplossingen te activeren voordat u de gebeurtenis goedkeurt.

Wanneer een van de bovenstaande gebeurtenissen wordt vastgelegd door de planningsgebeurtenisservice, wordt deze aangemeld bij de status van gebeurtenisgebeurtenis van de gebeurtenis toepassing, gebeurtenistype, resources (namen van virtuele machines) en NotBefore (minimale opzegtermijn). U de gebeurtenissen met ID 1234 vinden in het gebeurtenislogboek voor toepassingen.

Zodra de service is ingesteld en gestart, worden gebeurtenissen in de logboeken van Windows-toepassingen logboeken.   Als u wilt controleren of dit werkt, start u een van de virtuele machines in de beschikbaarheidsset opnieuw en ziet u een gebeurtenis die wordt aangemeld bij logboeken in Windows-logboeken > toepassingslogboek met de vm opnieuw is gestart. 

![Schermafbeelding van de logboekkijker.](./media/notifications/event-viewer.png)

Wanneer gebeurtenissen worden vastgelegd door de planningsgebeurtenisservice, wordt deze geregistreerd in de toepassing, zelfs als u zich aanmeldt bij gebeurtenisstatus, gebeurtenistype, resources (VM-naam) en NotBefore (minimale opzegtermijn). U de gebeurtenissen met ID 1234 vinden in het gebeurtenislogboek voor toepassingen.

> [!NOTE] 
> In dit voorbeeld, de virtuele machines waren in een beschikbaarheid set, die ons in staat stelde om een virtuele machine aan te wijzen als de verzamelaar om te luisteren en route geplande gebeurtenissen naar onze log analytics werkt ruimte. Als u zelfstandige virtuele machines hebt, u de service op elke virtuele machine uitvoeren en deze vervolgens afzonderlijk verbinden met uw werkruimte voor logboekanalyse.
>
> Voor onze set-up hebben we voor Windows gekozen, maar u een soortgelijke oplossing op Linux ontwerpen.

Op elk moment u de Geplande gebeurtenisservice `–stop` `–remove`stoppen/verwijderen met behulp van de switches en.

## <a name="connect-to-the-workspace"></a>Verbinding maken met de werkruimte


We willen nu een Log Analytics Workspace koppelen aan de verzamelaar VM. De werkruimte Log Analytics fungeert als een opslagplaats en we configureren gebeurtenislogboekverzameling om de toepassingslogboeken van de verzamel-VM vast te leggen. 

 Als u de geplande gebeurtenissen wilt routeren naar het gebeurtenislogboek, dat door onze service als toepassingslogboek wordt opgeslagen, moet u uw virtuele machine verbinden met uw loganalytics-werkruimte.  
 
1. Open de pagina voor de werkruimte die u hebt gemaakt.
1. Selecteer **onder Verbinding maken met een gegevensbron** Azure virtual machines **(VM's)**.

    ![Verbinding maken met een virtuele machine als gegevensbron](./media/notifications/connect-to-data-source.png)

1. Zoeken naar en selecteer **myCollectorVM**. 
1. Selecteer **Connect**op de nieuwe pagina voor **myCollectorVM**.

Hiermee wordt de [Microsoft Monitoring-agent](/azure/virtual-machines/extensions/oms-windows) in uw virtuele machine geïnstalleerd. Het duurt enkele minuten om uw VM aan te sluiten op de werkruimte en de extensie te installeren. 

## <a name="configure-the-workspace"></a>De werkruimte configureren

1. Open de pagina voor uw werkruimte en selecteer **Geavanceerde instellingen**.
1. Selecteer **Gegevens** in het linkermenu en selecteer **vervolgens Windows-gebeurtenislogboeken**.
1. Begin in **Verzamelen in de volgende gebeurtenislogboeken**met het typen van *toepassingen* en selecteer **Vervolgens Toepassing** in de lijst.

    ![Geavanceerde instellingen selecteren](./media/notifications/advanced.png)

1. Laat **FOUT,** **WAARSCHUWING**en **INFORMATIE geselecteerd** en selecteer **Opslaan** om de instellingen op te slaan.


> [!NOTE]
> Er zal enige vertraging zijn en het kan tot 10 minuten duren voordat het logboek beschikbaar is. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Een waarschuwingsregel maken met Azure Monitor 


Zodra de gebeurtenissen naar Log Analytics zijn gepusht, u de volgende [query](/azure/azure-monitor/log-query/get-started-portal) uitvoeren om te zoeken naar de planningsgebeurtenissen.

1. Selecteer boven aan de pagina **Logboeken** en plak het volgende in het tekstvak:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Selecteer **Opslaan**en typ *logQuery* voor de naam, laat **Query** als type, typ *VMLogs* als **categorie**en selecteer **Vervolgens Opslaan**. 

    ![De query opslaan](./media/notifications/save-query.png)

1. Selecteer **Nieuwe waarschuwingsregel**. 
1. Ga **op** de pagina `collectorworkspace` Regel maken als **bron**.
1. Selecteer **onder voorwaarde**het item Wanneer de zoekopdracht van het *klantenlogboek is <login undefined> *. De pagina **Signaallogica configureren** wordt geopend.
1. Voer **onder Drempelwaarde** *0* in en selecteer **Gereed**.
1. Selecteer **onder Acties**de optie Actiegroep **maken**. De pagina **Actiegroep toevoegen** wordt geopend.
1. Typ *myActionGroup*in **de naam van**de actiegroep .
1. In **Korte naam**, typ **myActionGroup**.
1. Selecteer **myResourceGroupAvailability**in **de groep Resourcegroep**.
1. Selecteer onder Acties in **ACTIENAAM** **type E-mail**en selecteer **Vervolgens E-mail/SMS/Push/Voice**. De **pagina E-mail/SMS/Push/Voice** wordt geopend.
1. Selecteer **E-mail,** typ uw e-mailadres en selecteer **OK**.
1. Selecteer **OK**op de pagina **Actiegroep toevoegen** . 
1. Typ *myAlert* voor de **regelnaam Waarschuwen**op de pagina **Regel maken** onder **WAARSCHUWINGSGEGEVENS**en typ *vervolgens de waarschuwingsregel E-mail* voor de **beschrijving**.
1. Wanneer u klaar bent, selecteert u **Waarschuwingsregel maken**.
1. Start een van de VM's opnieuw in de beschikbaarheidsset. Binnen een paar minuten ontvangt u een e-mail dat de waarschuwing is geactiveerd.

Als u uw waarschuwingsregels wilt beheren, gaat u naar de brongroep, selecteert u **Waarschuwingen** in het linkermenu en selecteert u **Waarschuwingsregels beheren** boven aan de pagina.

     
## <a name="next-steps"></a>Volgende stappen

Zie de [servicepagina Geplande evenementen](https://github.com/microsoft/AzureScheduledEventsService) op GitHub voor meer informatie.
