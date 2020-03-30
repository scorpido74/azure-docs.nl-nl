---
title: Werkstroom van Windows Azure VM-architectuur | Microsoft Documenten
description: In dit artikel vindt u een overzicht van de werkstroomprocessen wanneer u een service implementeert.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 04/08/2019
ms.author: kwill
ms.openlocfilehash: 5dd57a87658554bf59acf5cee1b6daf67b8692b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71162152"
---
#    <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Werkstroom van klassieke VM-architectuur van Windows Azure 
In dit artikel vindt u een overzicht van de werkstroomprocessen die plaatsvinden wanneer u een Azure-bron zoals een virtuele machine implementeert of bijwerkt. 

> [!NOTE]
>Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: Resource Manager en klassiek. Dit artikel gaat over het gebruik van het klassieke implementatiemodel.

In het volgende diagram wordt de architectuur van Azure-bronnen gepresenteerd.

![Azure-werkstroom](./media/cloud-services-workflow-process/workflow.jpg)

## <a name="workflow-basics"></a>Basisbeginselen van werkstroom
   
**A.** RDFE / FFE is het communicatiepad van de gebruiker naar de stof. RDFE (RedDog Front End) is de openbaar belichte API die de voorkant is van de Management Portal en de Service Management API, zoals Visual Studio, Azure MMC, enzovoort.  Alle verzoeken van de gebruiker gaan via RDFE. FFE (Fabric Front End) is de laag die aanvragen van RDFE vertaalt naar stofcommando's. Alle verzoeken van RDFE gaan via de FFE om de stofcontrollers te bereiken.

**B**. De fabric controller is verantwoordelijk voor het onderhouden en bewaken van alle bronnen in het datacenter. Het communiceert met fabric host agents op de stof OS verzenden van informatie, zoals de Guest OS versie, service pakket, service configuratie, en service status.

**C**. De hostagent woont op het hostbesturingssysteem en is verantwoordelijk voor het instellen van gastbesturingssysteem en het communiceren met Guest Agent (WindowsAzureGuestAgent) om de rol bij te werken in de richting van een beoogde doelstatus en heartbeatcontroles uit te voeren met de gastagent. Als hostagent gedurende 10 minuten geen hartslagrespons ontvangt, start hostagent het besturingssysteem van de gast opnieuw.

**C2**. WaAppAgent is verantwoordelijk voor het installeren, configureren en bijwerken van WindowsAzureGuestAgent.exe.

**D**.  WindowsAzureGuestAgent is verantwoordelijk voor het volgende:

1. Het gastbesturingssysteem configureren, inclusief firewall, ACL's, LocalStorage-bronnen, servicepakket en -configuratie en certificaten.
2. Het instellen van de SID voor het gebruikersaccount waarop de rol wordt uitgevoerd.
3. Het communiceren van de rolstatus aan de stof.
4. Start WaHostBootstrapper en houdt deze in de gaten om ervoor te zorgen dat de rol in doeltoestand is.

**E**. WaHostBootstrapper is verantwoordelijk voor:

1. Het lezen van de rolconfiguratie en het starten van alle juiste taken en processen om de rol te configureren en uit te voeren.
2. Het monitoren van al zijn onderliggende processen.
3. Het statuscheck-evenement verhogen voor het rolhostproces.

**F**. IISConfigurator wordt uitgevoerd als de rol is geconfigureerd als een Volledige IIS-webrol (deze wordt niet uitgevoerd voor SDK 1.2 HWC-rollen). Zij is verantwoordelijk voor:

1. De standaard IIS-services starten
2. De herschrijfmodule configureren in de webconfiguratie
3. De AppPool instellen voor de geconfigureerde rol in het servicemodel
4. IIS-logboekregistratie instellen om de map DiagnosticStore LocalStorage aan te wijzen
5. Machtigingen en ACL's configureren
6. De website bevindt zich in %roleroot%:\sitesroot\0 en de AppPool wijst naar deze locatie om IIS uit te voeren. 

**G**. Opstarttaken worden gedefinieerd door het rolmodel en gestart door WaHostBootstrapper. Opstarttaken kunnen worden geconfigureerd om asynchroon op de achtergrond uit te voeren en de hostbootstrapper start de opstarttaak en gaat vervolgens verder naar andere opstarttaken. Opstarttaken kunnen ook worden geconfigureerd om uit te voeren in de eenvoudige (standaard) modus waarin de hostbootstrapper wacht tot de opstarttaak is voltooid en een succesvolle exitcode (0) teruggeeft voordat u doorgaat naar de volgende opstarttaak.

**H**. Deze taken maken deel uit van de SDK en worden gedefinieerd als plug-ins in de servicedefinitie van de rol (.csdef). Wanneer de **DiagnosticsAgent** en **RemoteAccessAgent** worden uitgebreid naar opstarttaken, zijn ze uniek omdat ze elk twee opstarttaken definiëren, één reguliere en één met een **/blockStartup-parameter.** De normale opstarttaak wordt gedefinieerd als een opstarttaak achtergrond, zodat deze op de achtergrond kan worden uitgevoerd terwijl de rol zelf wordt uitgevoerd. De **/blockStartup** opstarttaak wordt gedefinieerd als een eenvoudige opstarttaak, zodat WaHostBootstrapper wacht tot deze wordt afgesloten voordat deze wordt voortgezet. De **/blockStartup-taak** wacht tot de reguliere taak is voltooid, en vervolgens wordt deze afgesloten en kan de hostbootstrapper doorgaan. Dit wordt gedaan zodat diagnostiek en RDP-toegang kunnen worden geconfigureerd voordat de rolprocessen beginnen (dit gebeurt via de taak /blockStartup). Hierdoor kunnen ook diagnostiek en RDP-toegang blijven draaien nadat de hostbootstrapper de opstarttaken heeft voltooid (dit gebeurt via de normale taak).

**I.** WaWorkerHost is het standaardhostproces voor normale werknemersrollen. Dit hostproces bevat alle DLL's en invoerpuntcode van de rol, zoals OnStart en Run.

**J**. WaWebHost is het standaardhostproces voor webrollen als deze zijn geconfigureerd om de SDK 1.2-compatibele Hostable Web Core (HWC) te gebruiken. Rollen kunnen de HWC-modus inschakelen door het element uit de servicedefinitie (.csdef) te verwijderen. In deze modus worden alle code en DLL's van de service uitgevoerd vanuit het WaWebHost-proces. IIS (w3wp) wordt niet gebruikt en er zijn geen AppPools geconfigureerd in IIS-beheer omdat IIS wordt gehost in WaWebHost.exe.

**K.** WaIISHost is het hostproces voor rolinvoerpuntcode voor webrollen die Volledige IIS gebruiken. Dit proces laadt de eerste DLL die wordt gevonden en die de klasse **RoleEntryPoint** gebruikt en de code uit deze klasse uitvoert (OnStart, Run, OnStop). Alle **RoleEnvironment-gebeurtenissen** (zoals StatusCheck en Gewijzigd) die zijn gemaakt in de klasse RoleEntryPoint, worden in dit proces verhoogd.

**L**. W3WP is het standaard IIS-werkproces dat wordt gebruikt als de rol is geconfigureerd om Volledige IIS te gebruiken. Hiermee wordt de AppPool uitgevoerd die is geconfigureerd vanuit IISConfigurator. Alle RoleEnvironment-gebeurtenissen (zoals StatusCheck en Changed) die hier worden gemaakt, worden in dit proces aangekaart. Houd er rekening mee dat roleenvironment-gebeurtenissen op beide locaties worden afgenomen (WaIISHost en w3wp.exe) als u zich abonneert op gebeurtenissen in beide processen.

## <a name="workflow-processes"></a>Werkstroomprocessen

1. Een gebruiker doet een verzoek, zoals het uploaden van ".cspkg" en ".cscfg"-bestanden, het vertellen van een bron om te stoppen of een configuratiewijziging aan te brengen, enzovoort. Dit kan via de Azure-portal of een hulpprogramma dat gebruikmaakt van de Api voor servicebeheer, zoals de functie Visual Studio Publish. Deze aanvraag gaat naar RDFE om al het abonnementswerk te doen en vervolgens het verzoek aan FFE door te delen. De rest van deze werkstroomstappen zijn het implementeren van een nieuw pakket en het starten ervan.
2. FFE vindt de juiste machinepool (op basis van klantinvoer, zoals affiniteitsgroep of geografische locatie plus invoer van de stof, zoals beschikbaarheid van de machine) en communiceert met de hoofdstofcontroller in die machinepool.
3. De fabriccontroller vindt een host met beschikbare CPU-cores (of draait een nieuwe host op). Het servicepakket en de configuratie worden gekopieerd naar de host en de fabriccontroller communiceert met de hostagent op het hostbesturingssysteem om het pakket te implementeren (configureer DIP's, poorten, gastbesturingssysteem, enzovoort).
4. De hostagent start het gastbesturingssysteem en communiceert met de gastagent (WindowsAzureGuestAgent). De host stuurt hartslagen naar de gast om ervoor te zorgen dat de rol werkt naar de doelstatus.
5. WindowsAzureGuestAgent stelt het gastbesturingssysteem in (firewall, ACL's, LocalStorage, enzovoort), kopieert een nieuw XML-configuratiebestand naar c:\Config en start vervolgens het WaHostBootstrapper-proces.
6. Voor volledige IIS-webrollen start WaHostBootstrapper IISConfigurator en vertelt het om bestaande AppPools voor de webrol uit IIS te verwijderen.
7. WaHostBootstrapper leest de **opstarttaken** van E:\RoleModel.xml en begint met het uitvoeren van opstarttaken. WaHostBootstrapper wacht tot alle eenvoudige opstarttaken zijn voltooid en heeft een "succes" bericht teruggestuurd.
8. Voor volledige IIS-webrollen vertelt WaHostBootstrapper aan IISConfigurator om de `E:\Sitesroot\<index>`IIS `<index>` AppPool te configureren en `<Sites>` wijst de site naar , waar is een 0-gebaseerde index in het aantal elementen gedefinieerd voor de service.
9. WaHostBootstrapper start het hostproces, afhankelijk van het roltype:
    1. **Functie van werknemer**: WaWorkerHost.exe wordt gestart. WaHostBootstrapper voert de methode OnStart() uit. Nadat deze is geretourneerd, begint WaHostBootstrapper met het uitvoeren van de methode Run() en markeert deze tegelijkertijd als Gereed en plaatst deze in de rotatie van de load balancer (als InputEndpoints zijn gedefinieerd). WaHostBootsrapper gaat dan in een lus van het controleren van de rolstatus.
    1. **SDK 1.2 HWC WebRol**: WaWebHost is gestart. WaHostBootstrapper voert de methode OnStart() uit. Nadat deze is geretourneerd, begint WaHostBootstrapper met het uitvoeren van de methode Run() en markeert deze tegelijkertijd de rol als Ready en plaatst deze in de rotatie van de load balancer. WaWebHost geeft een opwarmverzoek uit (GET /do.rd_runtime_init). Alle webverzoeken worden verzonden naar WaWebHost.exe. WaHostBootsrapper gaat dan in een lus van het controleren van de rolstatus.
    1. **Volledige IIS-webrol**: aIISHost wordt gestart. WaHostBootstrapper voert de methode OnStart() uit. Nadat deze is geretourneerd, wordt de methode Run() uitgevoerd en wordt de rol tegelijkertijd als Gereed markeert en in de rotatie van de lastbalansplaats gebracht. WaHostBootsrapper gaat dan in een lus van het controleren van de rolstatus.
10. Binnenkomende webaanvragen voor een Volledige IIS-webrol activeert IIS om het W3WP-proces te starten en de aanvraag te dienen, hetzelfde als in een on-premises IIS-omgeving.

## <a name="log-file-locations"></a>Locatie van logboekbestand

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log.  
Dit logboek bevat wijzigingen in de service, waaronder starts, stops en nieuwe configuraties. Als de service niet verandert, u grote tijdsverschillen in dit logboekbestand verwachten.
- C:\Logs\waappagent.log.  
Dit logboek bevat statusupdates en hartslagmeldingen en wordt elke 2-3 seconden bijgewerkt.  Dit logboek bevat een historische weergave van de status van de instantie en vertelt u wanneer de instantie zich niet in de status Klaar bevond.
 
**WaHostBootstrapper WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaWebHost WaWebHost**

`C:\Resources\Directory\<guid>.<role>\WaWebHost.log`
 
**WaIISHost WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS-logboeken**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Logboeken voor Windows-gebeurtenissen**

`D:\Windows\System32\Winevt\Logs`
 



