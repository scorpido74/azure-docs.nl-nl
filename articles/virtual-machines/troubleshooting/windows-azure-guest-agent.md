---
title: Problemen met Windows Azure Guest agent oplossen
description: Problemen met de Windows Azure Guest agent oplossen werkt niet
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/07/2020
ms.author: genli
ms.openlocfilehash: c3295365859ad3291a95b616cccc6fa265237a01
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263964"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>Problemen met Windows Azure Guest agent oplossen

Windows Azure Guest agent is een virtuele machine (VM)-agent. Hiermee kan de virtuele machine communiceren met de infrastructuur controller (de onderliggende fysieke server waarop de virtuele machine wordt gehost) op het IP-adres 168.63.129.16. Dit is een virtueel openbaar IP-adres dat de communicatie vereenvoudigt. Zie [Wat is IP-adres 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)? voor meer informatie.

 De virtuele machine die wordt gemigreerd naar Azure vanuit on-premises of die is gemaakt met behulp van een aangepaste installatie kopie, heeft geen Windows Azure Guest agent geïnstalleerd. In deze scenario's moet u de VM-agent hand matig installeren. Zie [overzicht van Azure virtual machine agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)voor meer informatie over het installeren van de VM-agent.

Nadat de installatie van Windows Azure Guest agent is voltooid, ziet u de volgende services die worden vermeld in Services. msc op de VM:
 
- Windows Azure Guest Agent-service
- Telemetrieservice
- RD-Agent service

**Windows Azure Guest Agent-service**: deze service is de service die verantwoordelijk is voor alle logboek registratie in WAppAgent. log. Deze service is verantwoordelijk voor het configureren van verschillende uitbrei dingen en communicatie van gast-naar-host. 

**Telemetrie-service**: deze service is verantwoordelijk voor het verzenden van de telemetriegegevens van de virtuele machine naar de back-endserver.

**RD-Agent service**: deze service is verantwoordelijk voor de installatie van de gast agent. Transparant installatie programma is ook een onderdeel van de RD-agent die helpt bij het upgraden van andere onderdelen en services van gast agent. RDAgent is ook verantwoordelijk voor het verzenden van heartbeats van de gast-VM naar de Hosta Gent op de fysieke server.

> [!NOTE]
> Vanaf versie 2.7.41491.971 van de VM-gast agent is het telemetrie-onderdeel opgenomen in de RDAgent-service. Daarom wordt deze telemetrie-service mogelijk niet weer gegeven in nieuw gemaakte Vm's.

## <a name="checking-agent-status-and-version"></a>Agent status en-versie controleren

Ga naar de pagina met eigenschappen van de virtuele machine in Azure Portal en controleer de **agent status**. Als de Windows Azure Guest-agent correct werkt, ziet u de status **gereed**. Als de VM-agent de status **niet gereed** heeft, werken de uitbrei dingen en de **opdracht uitvoeren** op de Azure Portal niet.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Problemen met de VM-agent met de status niet gereed oplossen

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>Stap 1 Controleer of de service Windows Azure Guest agent is geïnstalleerd

- Controleren op het pakket

    Ga naar de map C:\WindowsAzure. Als u de map werken ziet waarin het versie nummer wordt weer gegeven, betekent dit dat Windows Azure Guest agent is geïnstalleerd op de VM. U kunt ook zoeken naar het geïnstalleerde pakket.  Als Windows Azure Guest agent is geïnstalleerd op de VM, wordt het pakket opgeslagen op de volgende locatie: `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` .
    
    U kunt de volgende Power shell-opdracht uitvoeren om te controleren of de VM-agent is geïmplementeerd op de VM:
    
    `Get-Az VM -ResourceGroup “RGNAME” – Name “VMNAME” -displayhint expand`
    
    Zoek in de uitvoer de eigenschap **ProvisionVMAgent** en controleer of de waarde is ingesteld op **True**. Als dit het geval is, betekent dit dat de agent is geïnstalleerd op de virtuele machine.
    
- Controleer de services en processen

   Ga naar de Services-console (Services. msc) en controleer de status van de volgende services: Windows Azure Guest Agent-service, RDAgent-service, Windows Azure-telemetrie-service en Windows Azure Network Agent-service.
 
    U kunt ook controleren of deze services worden uitgevoerd door taak beheer te controleren op de volgende processen:
       
    - WindowsAzureGuestAgent.exe: Windows Azure Guest Agent-service
    - WaAppAgent.exe: RDAgent-service
    - WindowsAzureNetAgent.exe: Windows Azure Network Agent-service
    - WindowsAzureTelemetryService.exe: Windows Azure-telemetrie-service

   Als u deze processen en services niet kunt vinden, betekent dit dat er geen Windows Azure Guest-agent is geïnstalleerd.

- Het programma en de functie controleren

    Ga in het configuratie scherm naar **Program ma's en onderdelen** om te bepalen of de service Windows Azure Guest agent is geïnstalleerd.

Als er geen pakketten, services en processen worden uitgevoerd en u ziet niet zelfs Windows Azure Guest agent geïnstalleerd onder Program Ma's en onderdelen, installeert u de [Windows Azure Guest Agent-service](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows). Als de gast agent niet correct wordt geïnstalleerd, kunt u [de VM-agent offline installeren](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).

Als u de services ziet en deze worden uitgevoerd, moet u de service opnieuw starten die controleert of het probleem is opgelost. Als de services zijn gestopt, start u deze en wacht u enkele minuten. Controleer vervolgens of de **agent status** **gereed**is voor rapportage. Als u merkt dat deze services vastlopen, kunnen sommige processen van derden ervoor zorgen dat deze services vastlopen. Neem contact op met [Microsoft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)voor meer informatie over het oplossen van deze problemen.

### <a name="step-2-check-whether-auto-update-is-working"></a>Stap 2 controleren of automatisch bijwerken werkt

De Windows Azure Guest-agent bevat een functie voor automatisch bijwerken. Er wordt automatisch gecontroleerd of er nieuwe updates zijn en installeert deze. Als de functie voor automatisch bijwerken niet correct werkt, verwijdert u de Windows Azure Guest agent en installeert u deze met behulp van de volgende stappen:

1. Als Windows Azure Guest agent wordt weer gegeven in **Program ma's en onderdelen**, verwijdert u de Windows Azure-gast agent.

2. Open een opdracht prompt venster met beheerders bevoegdheden.

3. Stop de services van de gast agent. Als de services niet worden gestopt, moet u de services instellen op **hand matig opstarten** en vervolgens de virtuele machine opnieuw opstarten.
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. De services van de gast agent verwijderen:
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. Onder `C:\WindowsAzure` Maak een map met de naam oud.

1. Verplaats mappen met de naam packages of werken naar de map OLD.

1. Down load en Installeer [hier](https://go.microsoft.comfwlink/?linkid=394789&clcid=0x409)de nieuwste versie van het installatie pakket voor de agent. U moet over beheerders rechten beschikken om de installatie te volt ooien.

1. Installeer de gast agent met behulp van de volgende opdracht:

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    Controleer vervolgens of de services van de gast agent correct worden gestart.
 
    In zeldzame gevallen waarin de gast agent niet correct wordt geïnstalleerd, kunt u [de VM-agent offline installeren](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Stap 3 Controleer of de virtuele machine verbinding kan maken met de infrastructuur controller

Gebruik een hulp programma zoals PsPing om te testen of de virtuele machine verbinding kan maken met 168.63.129.16 op de poorten 80, 32526 en 443. Als de virtuele machine niet op de verwachte manier verbinding maakt, controleert u of uitgaande communicatie via de poorten 80, 443 en 32526 is geopend in uw lokale firewall op de virtuele machine. Als dit IP-adres wordt geblokkeerd, kan de VM-agent onverwacht gedrag in verschillende scenario's weer geven.

## <a name="advanced-troubleshooting"></a>Geavanceerde probleemoplossing

Gebeurtenissen voor het oplossen van problemen met Windows Azure Guest agent worden vastgelegd in de volgende logboek bestanden:

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

Hier volgen enkele veelvoorkomende scenario's waarin de status van de Windows Azure-gast agent **niet gereed** kan zijn of niet meer werkt zoals verwacht.

### <a name="agent-stuck-on-starting"></a>De agent is vastgelopen op het begin

In het WaAppAgent-logboek kunt u zien dat de agent vastloopt aan het begin proces en niet kan worden gestart.

**Logboek gegevens**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] WindowsAzureGuestAgent gestart. Versie 2.7.41491.901

**Bepaling**
 
Op de VM wordt nog steeds de oudere versie van de Windows Azure-gast agent uitgevoerd. In de map C:\WindowsAzure ziet u mogelijk dat er meerdere exemplaren van Windows Azure Guest agent zijn geïnstalleerd, met inbegrip van een aantal van dezelfde versie. Omdat er meerdere agent exemplaren zijn geïnstalleerd, start de virtuele machine niet de meest recente versie van de Windows Azure-gast agent.

**Oplossing**

Verwijder de Windows Azure-gast agent hand matig en installeer deze opnieuw door de volgende stappen uit te voeren:

1. Open configuratie scherm > Program Ma's en onderdelen en verwijder de Windows Azure-gast agent.
1. Open taak beheer en stop de volgende services: Windows Azure Guest Agent-service, RDAgent-service, Windows Azure telemetrie-service en Windows Azure Network Agent-service.
1. Maak onder C:\WindowsAzure een map met de naam oud.
1. Verplaats mappen met de naam packages of werken naar de map OLD. Verplaats ook de werken-mappen in C:\WindowsAzure\logs die als GuestAgent_x. x. xxxxx worden gestart naar de oude map.
1. Down load en installeer de meest recente versie van de MSI-agent. U moet over beheerders rechten beschikken om de installatie te volt ooien.
1. Installeer de gast agent met behulp van de volgende MSI-opdracht:
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. Controleer of de RDAgent, Windows Azure Guest agent en Windows Azure telemetrie-Services nu worden uitgevoerd.
 
1. Controleer WaAppAgent. log om te controleren of de meest recente versie van Windows Azure Guest agent wordt uitgevoerd.
 
1. Verwijder de oude map onder C:\WindowsAzure.
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Kan geen verbinding maken met WireServer IP (host-IP) 

U ziet dat de volgende fout berichten worden weer gegeven in WaAppAgent. log en telemetrie. log:

**Logboek gegevens**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**Bepaling**

De virtuele machine kan de wireserver-hostserver niet bereiken.

**Oplossing**

1. Omdat wireserver niet bereikbaar is, maakt u verbinding met de virtuele machine met behulp van Extern bureaublad en probeert u vervolgens toegang te krijgen tot de volgende URL vanuit een Internet browser: http://168.63.129.16/?comp=versions 
1. Als u de URL niet kunt bereiken uit stap 1, controleert u de netwerk interface om te bepalen of deze is ingesteld als DHCP-ingeschakeld en DNS heeft. Als u de DHCP-status van de netwerk interface wilt controleren, voert u de volgende opdracht uit:  `netsh interface ip show config` .
1. Als DHCP is uitgeschakeld, voert u de volgende handelingen uit om de waarde geel in te stellen op de naam van uw Interface: `netsh interface ip set address name="Name of the interface" source=dhcp` .
1. Controleer of er problemen zijn die kunnen worden veroorzaakt door een firewall, een proxy of een andere bron die de toegang tot het IP-adres 168.63.129.16 kunnen blok keren.
1. Controleer of Windows Firewall of een firewall van derden de toegang blokkeert tot de poorten 80, 443 en 32526. Zie [Wat is IP-adres 168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16)? voor meer informatie over waarom dit adres niet moet worden geblokkeerd.

### <a name="guest-agent-is-stuck-stopping"></a>De gast agent is vastgelopen  

U ziet dat de volgende fout berichten worden weer gegeven in WaAppAgent. log:

**Logboek gegevens** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**Bepaling**

De Windows Azure Guest-agent is vastgelopen tijdens het stoppen.

**Oplossing**

1. Zorg ervoor dat WaAppAgent.exe wordt uitgevoerd op de virtuele machine. Als deze niet actief is, start u de rdgagent-service opnieuw op en wacht u vijf minuten. Wanneer WaAppAgent.exe wordt uitgevoerd, moet u het WindowsAzureGuest.exe proces beëindigen.
2. Als het probleem niet wordt opgelost door stap 1, verwijdert u de momenteel geïnstalleerde versie en installeert u de nieuwste versie van de agent hand matig.

### <a name="npcap-loopback-adapter"></a>Npcap loop back-adapter 

U ziet dat de volgende fout berichten worden weer gegeven in WaAppAgent. log:
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**Bepaling**

De Npcap loop back-adapter is op de virtuele machine geïnstalleerd door wireshark. Wireshark is een open source-hulp programma voor het profileren van het netwerk verkeer en het analyseren van pakketten. Een dergelijk hulp programma wordt vaak een Network Analyzer, Network Protocol Analyzer of sniffer genoemd.

**Oplossing**

De Npcap loop back-adapter is waarschijnlijk geïnstalleerd door WireShark. Probeer het programma uit te scha kelen en controleer of het probleem is opgelost.

## <a name="next-steps"></a>Volgende stappen

[Neem contact op met micro soft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)voor meer problemen met het probleem ' Windows Azure Guest agent werkt niet '.