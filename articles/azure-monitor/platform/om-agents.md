---
title: Operations Manager verbinden met Azure Monitor | Microsoft Documenten
description: Als u uw bestaande investering in System Center Operations Manager wilt behouden en combineren met de uitgebreide mogelijkheden van Log Analytics, kunt u Operations Manager integreren met uw werkruimte.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 92b6737f48d8d8704f461c9adac92284b323b05f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274344"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Operations Manager verbinden met Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Als u uw bestaande investering in [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) wilt behouden en uitgebreide mogelijkheden wilt gebruiken met Azure Monitor, u Operations Manager integreren met uw Log Analytics-werkruimte. Hiermee u gebruikmaken van de mogelijkheden van logboeken in Azure Monitor en Operations Manager blijven gebruiken om:

* Statuscontrole van uw IT-services met Operations Manager
* Onderhoud van de integratie met uw ITSM-beheeroplossingen voor incidenten en problemen
* Beheer van de levenscyclus van agents die lokaal zijn geïmplementeerd of op IaaS-VM's in de openbare cloud, en die u bewaakt met Operations Manager

Integratie met System Center Operations Manager voegt waarde toe aan uw serviceoperations-strategie door de snelheid en efficiëntie van Azure Monitor te gebruiken bij het verzamelen, opslaan en analyseren van logboekgegevens van Operations Manager. Azure Monitor-logboekquery's helpen bij het correleren en werken aan het identificeren van de fouten van problemen en het opduiken van herhalingen ter ondersteuning van uw bestaande probleembeheerproces. De flexibiliteit van de queryengine om prestatie-, gebeurtenis- en waarschuwingsgegevens te onderzoeken, met uitgebreide dashboards en rapportagemogelijkheden om deze gegevens op zinvolle manieren bloot te leggen, toont de kracht die Azure Monitor met Zich meebrengt bij het complimenteren van Operations Manager.

De agents die rapporteren aan de beheergroep Operations Manager verzamelen gegevens van uw servers op basis van de [gegevensbronnen](agent-data-sources.md) en oplossingen van Log Analytics die u in uw werkruimte hebt ingeschakeld. Afhankelijk van de ingeschakelde oplossingen worden hun gegevens rechtstreeks van een Operations Manager-beheerserver naar de service verzonden of vanwege de hoeveelheid gegevens die op het door de agent beheerde systeem worden verzameld, rechtstreeks van de agent naar een Log Analytics-werkruimte verzonden. De beheerserver stuurt de gegevens rechtstreeks naar de service. Deze worden nooit naar de operationele database of datawarehouse-database geschreven. Wanneer een beheerserver de verbinding met Azure Monitor verliest, worden de gegevens lokaal opgeslagen totdat de communicatie is hersteld. Als de beheerserver offline is vanwege gepland onderhoud of ongeplande uitval, hervat een andere beheerserver in de beheergroep de verbinding met Azure Monitor.  

In het volgende diagram ziet u de verbinding tussen de beheerservers en agents in een beheergroep van System Center Operations Manager en Azure Monitor, inclusief de richting en poorten.

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Als uw IT-beveiligingsbeleid computers in uw netwerk geen verbinding met internet toestaat, kunnen beheerservers worden geconfigureerd om verbinding te maken met de Log Analytics-gateway om configuratiegegevens te ontvangen en verzamelde gegevens te verzenden, afhankelijk van de oplossingen Ingeschakeld. Zie [Computers verbinden met Azure Monitor met de Logboekanalysegateway](../../azure-monitor/platform/gateway.md)voor meer informatie en stappen over het configureren van uw beheergroep Operations Manager om te communiceren via een Logboekanalysegateway naar Azure Monitor.  

## <a name="prerequisites"></a>Vereisten

Controleer voordat u begint de volgende vereisten.

* Azure Monitor ondersteunt alleen System Center Operations Manager 2016 of hoger, Operations Manager 2012 SP1 UR6 of hoger en Operations Manager 2012 R2 UR2 of hoger. Proxyondersteuning is toegevoegd aan Operations Manager 2012 SP1 UR7 en Operations Manager 2012 R2 UR3.
* Voor de integratie van System Center Operations Manager 2016 met de cloud van de Amerikaanse overheid is een bijgewerkt Advisor-beheerpakket vereist dat is opgenomen in Update Rollup 2 of hoger. System Center Operations Manager 2012 R2 vereist een bijgewerkt Advisor-beheerpakket dat is opgenomen in Update Rollup 3 of hoger.
* Alle Operations Manager-agents moeten voldoen aan de minimale vereisten voor ondersteuning. Controleer of agents minimaal worden bijgewerkt, anders kan de communicatie met Windows-agents mislukken en fouten genereren in het gebeurtenislogboek operations manager.
* Een Log Analytics-werkruimte. Bekijk het overzicht van [de log-analysewerkruimte](design-logs-deployment.md)voor meer informatie. 
* U verifieert azure met een account dat lid is van de [rol Log Analytics-bijdrager](manage-access.md#manage-access-using-azure-permissions).

* Ondersteunde regio's - Alleen de volgende Azure-regio's worden ondersteund door System Center Operations Manager om verbinding te maken met een Log Analytics-werkruimte:
    - VS - west-centraal
    - Australië - zuidoost
    - Europa -west
    - VS - oost
    - Azië - zuidoost
    - Japan - oost
    - Verenigd Koninkrijk Zuid
    - India - centraal
    - Canada - midden
    - VS - west 2

>[!NOTE]
>Recente wijzigingen in Azure API's voorkomen dat klanten de integratie tussen hun beheergroep en Azure Monitor voor het eerst met succes kunnen configureren. Voor klanten die hun beheergroep al met de service hebben geïntegreerd, wordt er geen invloed op dit effect, tenzij u uw bestaande verbinding opnieuw moet configureren.  
>Er is een nieuw managementpack uitgebracht voor de volgende versies van Operations Manager:
> - Voor System Center Operations Manager 2019 wordt dit managementpack meegeleverd met de bronmedia en geïnstalleerd tijdens de installatie van een nieuwe beheergroep of tijdens een upgrade.
>- Operations Manager 1801 management pack is ook van toepassing op Operations Manager 1807.
>- Download het beheerpakket vanaf [hier](https://www.microsoft.com/download/details.aspx?id=57173)voor System Center Operations Manager 1801.
>- Voor System Center 2016 - Operations Manager downloadt u het managementpack [vanaf hier.](https://www.microsoft.com/download/details.aspx?id=57172)  
>- Download het beheerpakket vanaf [hier](https://www.microsoft.com/download/details.aspx?id=57171)voor System Center Operations Manager 2012 R2.  


### <a name="network"></a>Netwerk

In de onderstaande informatie worden de proxy- en firewallconfiguratiegegevens vermeld die nodig zijn om de Operations Manager-agent, beheerservers en operations-console te laten communiceren met Azure Monitor. Verkeer van elk onderdeel is uitgaand van uw netwerk naar Azure Monitor.

|Resource | Poortnummer| HTTPS-controle overslaan|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Ja|  
|\*.oms.opinsights.azure.com| 443|Ja|  
|\*.blob.core.windows.net| 443|Ja|  
|\*.azure-automation.net| 443|Ja|  
|**Beheerserver**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Ja|  
|\*.ods.opinsights.azure.com| 443| Ja|  
|*.azure-automation.net | 443| Ja|  
|**Operations Manager-console naar Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 en 443||  
|\*.microsoft.com| 80 en 443||  
|\*.microsoftonline.com| 80 en 443||  
|\*.mms.microsoft.com| 80 en 443||  
|login.windows.net| 80 en 443||  
|portal.loganalytics.io| 80 en 443||
|api.loganalytics.io| 80 en 443||
|docs.loganalytics.io| 80 en 443||  

### <a name="tls-12-protocol"></a>TLS 1.2-protocol

Om de beveiliging van gegevens die onderweg zijn naar Azure Monitor te verzekeren, raden we u ten zeerste aan om de agent en de beheergroep te configureren om ten minste TLS(1.2) (Transport Layer Security) te gebruiken. Oudere versies van TLS/Secure Sockets Layer (SSL) zijn kwetsbaar bevonden en hoewel ze momenteel nog werken om achterwaartse compatibiliteit mogelijk te maken, worden ze **niet aanbevolen.** Voor meer informatie, controleer [het verzenden van gegevens veilig met TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Operations Manager koppelen aan Azure Monitor

Voer de volgende stappen uit om uw Operations Manager-beheergroep te verbinden met een van uw Log Analytics-werkruimten.

Tijdens de eerste registratie van uw Operations Manager-beheergroep met een Log Analytics-werkruimte is de optie om de proxyconfiguratie voor de beheergroep op te geven, niet beschikbaar in de Operations-console.  Deze optie is pas beschikbaar als de beheergroep bij de service is geregistreerd.  Om dit mogelijk te maken, moet u de systeemproxyconfiguratie bijwerken met Netsh op het systeem waarop de Operations-console wordt uitgevoerd om de integratie te configureren en alle beheerservers in de beheergroep.  

1. Open een opdrachtprompt met verhoogde bevoegdheid.
   a. Ga naar **Start** en typ **cmd**.
   b. Klik met de rechtermuisknop op **Opdrachtprompt** en selecteer Uitvoeren als beheerder**.
1. Voer de volgende opdracht in en druk op **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

Nadat u de volgende stappen hebt voltooid om te integreren `netsh winhttp reset proxy` met Azure Monitor, u de configuratie verwijderen door de optie **Proxyserver configureren** in de Operations-console te gebruiken om de proxy- of Logboekanalysegatewayserver op te geven.

1. Selecteer de werkruimte **Beheer** in de Operations Manager-console.
1. Vouw het knooppunt Operations Management Suite uit en klik op **Verbinding**.
1. Klik op de koppeling **Registreren bij Operations Management Suite**.
1. Voer op de pagina **Wizard Operations Management Suite voorbereiden: verificatie** het e-mailadres of telefoonnummer en het wachtwoord in van het beheerdersaccount dat is gekoppeld aan uw OMS-abonnement, en klik op **Aanmelden**.

   >[!NOTE]
   >De naam Operations Management Suite is beëindigd.

1. Nadat u bent geverifieerd, wordt u op de **wizard Operations Management Suite Onboarding: Selecteer werkruimte** gevraagd uw Azure-tenant, abonnement en Log Analytics-werkruimte te selecteren. Als u meerdere werkruimten hebt, selecteert u in de keuzelijst de werkruimte die u bij de Operations Manager-beheergroep wilt registreren, en klikt u op **Volgende**.

   > [!NOTE]
   > Operations Manager ondersteunt slechts één Log Analytics-werkruimte tegelijk. De verbinding en de computers die zijn geregistreerd bij Azure Monitor met de vorige werkruimte worden verwijderd uit Azure Monitor.
   >
   >
1. Controleer de instellingen op de pagina **Wizard Operations Management Suite voorbereiden: samenvatting**. Als deze juist zijn, klikt u op **Maken**.
1. Op de pagina **Wizard Operations Management Suite voorbereiden: voltooien** klikt u op **Sluiten**.

### <a name="add-agent-managed-computers"></a>Door een agent beheerde computers toevoegen

Nadat de integratie met uw Log Analytics-werkruimte is geconfigureerd, wordt alleen een verbinding met de service tot gevolg, er worden geen gegevens verzameld van de agents die rapporteren aan uw beheergroep. Dit gebeurt pas nadat u hebt geconfigureerd welke specifieke door agenten beheerde computers logboekgegevens verzamelen voor Azure Monitor. U kunt de computerobjecten afzonderlijk selecteren of een groep met Windows-computerobjecten selecteren. U kunt geen groep met objecten van een andere klasse selecteren, zoals logische schijven of SQL-databases.

1. Open de Operations Manager-console en selecteer de werkruimte **Beheer**.
1. Vouw het knooppunt Operations Management Suite uit en klik op **Verbinding**.
1. Klik op de koppeling **Computer/Groep toevoegen** onder de kop Acties rechts in het deelvenster.
1. In het dialoogvenster **Computer zoeken** kunt u zoeken naar computers of groepen die worden bewaakt door Operations Manager. Selecteer computers of groepen, waaronder de Operations Manager Management Server om aan boord te gaan van Azure Monitor, klik op **Toevoegen**en klik vervolgens op **OK**.

U kunt de computers en groepen die zijn geconfigureerd voor het verzamelen van gegevens bekijken in het knooppunt Beheerde computers onder Operations Management Suite in de werkruimte **Beheer** van de Operations-console. Hier kunt u zo nodig computers en groepen toevoegen of verwijderen.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Proxy-instellingen configureren in de Operations-console

Voer de volgende stappen uit als een interne proxyserver zich bevindt tussen de beheergroep en Azure Monitor. Deze instellingen worden centraal beheerd vanuit de beheergroep en gedistribueerd naar door agenten beheerde systemen die zijn opgenomen in het bereik om logboekgegevens voor Azure Monitor te verzamelen.  Dit is nuttig voor oplossingen die de beheerserver overslaan en gegevens rechtstreeks naar de service verzenden.

1. Open de Operations Manager-console en selecteer de werkruimte **Beheer**.
1. Vouw Operations Management Suite uit en klik op **Verbindingen**.
1. Klik in de weergave OMS-verbinding op **Proxyserver configureren**.
1. Selecteer op de pagina **Wizard Operations Management Suite: proxyserver** de optie **Een proxyserver gebruiken voor toegang tot Operations Management Suite** en typ de URL met het poortnummer, bijvoorbeeld http://corpproxy:80, en klik op **Voltooien**.

Als uw proxyserver verificatie vereist, voert u de volgende stappen uit om referenties en instellingen te configureren die moeten worden doorgegeven aan beheerde computers die worden gerapporteerd aan Azure Monitor in de beheergroep.

1. Open de Operations Manager-console en selecteer de werkruimte **Beheer**.
1. Selecteer onder **RunAs-configuratie** de optie **Profielen**.
1. Open het profiel **System Center Advisor RunAs-profielproxy**.
1. Klik in de wizard Uitvoeren als-profiel op Toevoegen om een Uitvoeren als-account te gebruiken. U kunt een nieuw [Uitvoeren als-account](https://technet.microsoft.com/library/hh321655.aspx) maken of een bestaand account gebruiken. Dit account moet over voldoende rechten beschikken voor het doorgeven van de proxyserver.
1. Als u het account voor beheer wilt instellen, kiest u **Een geselecteerde klasse, groep of object**, klikt u op **Selecteren...** en klikt u vervolgens op **Groep...** om het vak **Groep zoeken** te openen.
1. Zoek en selecteer de **Microsoft System Center Advisor Monitoring Server-groep**. Klik op **OK** na het selecteren van de groep om het vak **Groep zoeken** te sluiten.
1. Klik op **OK** om het vak **Een run als account toevoegen** te sluiten.
1. Klik op **Opslaan** om de wizard te voltooien en de wijzigingen op te slaan.

Nadat de verbinding is gemaakt en u configureert welke agents logboekgegevens verzamelen en rapporteren aan Azure Monitor, wordt de volgende configuratie toegepast in de beheergroep, niet noodzakelijkerwijs in volgorde:

* Het Uitvoeren als-account **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** wordt gemaakt. Het wordt gekoppeld aan het Uitvoeren als-profiel **Microsoft System Center Advisor Run As Profile Blob** voor twee klassen: **Collection Server** (server voor verzameling) en **Operations Manager Management Group** (Operations Manager-beheergroep).
* Er worden twee connectors gemaakt.  De eerste heet **Microsoft.SystemCenter.Advisor.DataConnector** en wordt automatisch geconfigureerd met een abonnement dat alle waarschuwingen die zijn gegenereerd uit instanties van alle klassen in de beheergroep doorstuurt naar Azure Monitor. De tweede connector is **Advisor Connector**, die verantwoordelijk is voor de communicatie met Azure Monitor en het delen van gegevens.
* Agents en groepen die u hebt geselecteerd voor het verzamelen van gegevens in de beheergroep, worden toegevoegd aan de **Microsoft System Center Advisor Monitoring Server-groep**.

## <a name="management-pack-updates"></a>Management pack-updates

Nadat de configuratie is voltooid, maakt de beheergroep Operations Manager een verbinding met Azure Monitor. De beheerserver wordt gesynchroniseerd met de webservice en ontvangt informatie over de bijgewerkte configuratie in de vorm van management packs voor de oplossingen die u hebt ingeschakeld en die zijn geïntegreerd met Operations Manager. Operations Manager controleert op updates van deze beheerpakketten en downloadt en importeert deze automatisch wanneer ze beschikbaar zijn. Dit gedrag wordt voornamelijk bepaald door twee regels:

* **Microsoft.SystemCenter.Advisor.MPUpdate** - Werkt de azure monitorbeheerpakketten van de basis bij. Deze regel wordt standaard elke 12 uur uitgevoerd.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** - Hiermee worden de management packs van ingeschakelde oplossingen in uw werkruimte bijgewerkt. Deze regel wordt standaard elke vijf (5) minuten uitgevoerd.

U deze twee regels overschrijven om automatisch downloaden te voorkomen door ze uit te schakelen of de frequentie wijzigen voor hoe vaak de beheerserver synchroniseert met Azure Monitor om te bepalen of een nieuw beheerpakket beschikbaar is en moet worden gedownload. Volg de stappen voor het [overschrijven van een regel of controle](https://technet.microsoft.com/library/hh212869.aspx) als u de parameter **Frequency** (Frequentie) wilt bijwerken met een waarde in seconden om het synchronisatieschema te wijzigen. Wijzig de parameter **Enabled** (Ingeschakeld) als u de regels wilt uitschakelen. Configureer de overschrijvingen voor alle objecten van de klasse Operations Manager Management Group.

Als u uw bestaande wijzigingsbeheerproces wilt blijven volgen voor het beheren van managementpackreleases in uw productiebeheergroep, u de regels uitschakelen en deze inschakelen op bepaalde momenten waarop updates zijn toegestaan. Als u een ontwikkelings- of QA-beheergroep in uw omgeving hebt en deze met internet verbonden is, kunt u die beheergroep configureren met een Log Analytics-werkruimte ter ondersteuning van dit scenario. Hiermee u de iteratieve versies van de Azure Monitor-beheerpakketten bekijken en evalueren voordat u deze vrijgeeft in uw productiebeheergroep.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Een Operations Manager-groep overschakelen naar een nieuwe Log Analytics-werkruimte

1. Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .
1. Klik in Azure Portal op **Meer services** in de linkerbenedenhoek. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics** en maak vervolgens een werkruimte.  
1. Open de Operations Manager-console met een account dat lid is van de rol Administrators in Operations Manager en selecteer de werkruimte **Beheer**.
1. Vouw Logboekanalyses uit en selecteer **Verbindingen**.
1. Selecteer de koppeling **Operations Management Suite opnieuw configureren** in het middelste gedeelte van het deelvenster.
1. Volg de **wizard Logboekanalyse Onboarding** en voer het e-mailadres of telefoonnummer en wachtwoord in van het beheerdersaccount dat is gekoppeld aan uw nieuwe werkruimte Log Analytics.

   > [!NOTE]
   > Op de pagina **Wizard Operations Management Suite voorbereiden: werkruimte selecteren** wordt de bestaande werkruimte weergegeven die momenteel wordt gebruikt.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Operations Manager-integratie valideren met Azure Monitor

Er zijn een aantal verschillende manieren waarop u controleren of de integratie van Azure Monitor to Operations Manager is geslaagd.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Integratie controleren vanuit Azure Portal

1. Klik in Azure Portal op **Meer services** in de linkerbenedenhoek. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer.
1. Selecteer de toepasselijke werkruimte in de lijst met Log Analytics-werkruimten.  
1. Selecteer achtereenvolgens **Geavanceerde instellingen**, **Verbonden bronnen** en **System Center**.
1. In de tabel onder de sectie System Center Operations Manager ziet u de naam van de beheergroep met het aantal agents en wanneer er voor het laatst gegevens zijn ontvangen.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Integratie controleren vanuit de Operations-console

1. Open de Operations Manager-console en selecteer de werkruimte **Beheer**.
1. Selecteer **Management Packs** en typ in het tekstvak **Look for:** (Zoeken naar) **Advisor** of **Intelligence**.
1. Voor elke oplossing die u hebt ingeschakeld, ziet u een bijbehorend management pack in de lijst met zoekresultaten.  Als u bijvoorbeeld de oplossing Waarschuwingenbeheer hebt ingeschakeld, wordt het management pack Microsoft System Center Advisor Waarschuwingenbeheer weergegeven in de lijst.
1. Schakel over van de weergave **Monitoring** (Bewaking) naar de weergave **Operations Management Suite\Health State** (Status van Operations Management Suite).  Selecteer een beheerserver in het deelvenster **Management Server State** (Status van beheerserver) en controleer in het deelvenster **Detail View** (Detailweergave) of de waarde voor de eigenschap **Authentication service URI** (URI van verificatieservice) overeenkomt met de id van de Log Analytics-werkruimte.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Integratie met Azure-monitor verwijderen

Als de integratie tussen uw Operations Manager-beheergroep en de Log Analytics-werkruimte niet meer nodig is, moet u verschillende stappen uitvoeren om de verbinding en de configuratie in de beheergroep correct te verwijderen. Met de volgende procedure u uw Log Analytics-werkruimte bijwerken door de verwijzing naar uw beheergroep te verwijderen, de Azure Monitor-connectors te verwijderen en vervolgens beheerpakketten te verwijderen die de integratie met de service ondersteunen.  

Beheerpakketten voor de oplossingen die u hebt ingeschakeld en die integreren met Operations Manager en de beheerpakketten die nodig zijn om integratie met Azure Monitor te ondersteunen, kunnen niet eenvoudig uit de beheergroep worden verwijderd. Dit komt omdat sommige azure monitorbeheerpakketten afhankelijk zijn van andere gerelateerde beheerpakketten. Download het script [remove a management pack with dependencies](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) (een management pack met afhankelijkheden verwijderen) van TechNet Script Center als u management packs die afhankelijk zijn van andere management packs wilt verwijderen.  

1. Open de Operations Manager-opdrachtshell met een account dat lid is van de rol Administrators in Operations Manager.

    > [!WARNING]
    > Controleer voordat u doorgaat of er geen aangepaste management packs zijn met 'Advisor' of 'IntelligencePack' in de naam, anders worden ze uit de beheergroep verwijderd tijdens de volgende stappen.
    >

1. Typ vanaf de opdrachtshell-prompt `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Typ vervolgens `Get-SCOMManagementPack -name "*IntelligencePack*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Als u ook alle resterende management packs die afhankelijk zijn van andere management packs van System Center Advisor wilt verwijderen, gebruikt u het script *RecursiveRemove.ps1* dat u eerder van TechNet Script Center hebt gedownload.  

    > [!NOTE]
    > De stap om de Advisor-beheerpakketten met PowerShell te verwijderen, verwijdert niet automatisch de interne beheerpakketten van Microsoft System Center Advisor of Microsoft System Center Advisor.  Probeer ze niet te verwijderen.  
    >  

1. Open de Operations-console van Operations Manager met een account dat lid is van de rol Administrators in Operations Manager.
1. Selecteer onder **Beheer** het knooppunt **Management Packs**, typ in het vak **Look for:** (Zoeken naar) **Advisor** en controleer of de volgende management packs nog zijn geïmporteerd in de beheergroep:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. Klik in de Azure-portal op de tegel **Instellingen.**
1. Selecteer **Verbonden bronnen**.
1. In de tabel onder de sectie Systeemcentrum Operations Manager ziet u de naam van de beheergroep die u uit de werkruimte wilt verwijderen. Klik onder de kolom **Laatste gegevens** op **Verwijderen**.  

    > [!NOTE]
    > De koppeling **Verwijderen** is pas beschikbaar als er 14 dagen geen activiteit van de verbonden beheergroep is gedetecteerd.  
    >

1. Er wordt een bevestigingsvenster weergegeven waarin u wordt gevraagd of u wilt doorgaan met het verwijderen.  Klik op **Ja** om door te gaan.

Als u de twee connectors Microsoft.SystemCenter.Advisor.DataConnector en Advisor Connector wilt verwijderen, slaat u het onderstaande PowerShell-script op uw computer op en voert u het script uit met behulp van de volgende voorbeelden:

```
    .\OM2012_DeleteConnectors.ps1 "Advisor Connector" <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 "Microsoft.SystemCenter.Advisor.DataConnector" <ManagementServerName>
```

> [!NOTE]
> Als de computer waarop u dit script uitvoert geen beheerserver is, moet mogelijk de Operations Manager-opdrachtshell erop zijn geïnstalleerd, afhankelijk van de versie van uw beheergroep.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

Als u in de toekomst van plan bent uw beheergroep opnieuw te `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` verbinden met een Log Analytics-werkruimte, moet u het beheerpakketbestand opnieuw importeren. Afhankelijk van welke versie van System Center Operations Manager in uw omgeving is geïmplementeerd, bevindt dit bestand zich op de volgende locatie:

* Op het bronmedium in de map `\ManagementPacks` voor System Center 2016 - Operations Manager of hoger.
* Van het meest recente updatepakket dat op de beheergroep is toegepast. Voor Operations Manager 2012 is `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` de bronmap en voor 2012 R2 bevindt deze zich in `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Monitor-oplossingen toevoegen vanuit de Galerie Oplossingen](../../azure-monitor/insights/solutions.md)als u functionaliteit wilt toevoegen en gegevens wilt verzamelen.
