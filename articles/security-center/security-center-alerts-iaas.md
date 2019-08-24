---
title: Detectie van bedreigingen voor Vm's en servers in Azure Security Center | Microsoft Docs
description: Dit onderwerp bevat een overzicht van de VM-en server waarschuwingen die beschikbaar zijn in Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 055b578c1d976b7f85c65ab4e028f9d609861cd4
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013333"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Detectie van bedreigingen voor Vm's en servers in Azure Security Center

Dit onderwerp bevat de verschillende typen detectie methoden en waarschuwingen die beschikbaar zijn voor virtuele machines en servers met de volgende besturings systemen. Zie [platforms en functies die door Azure Security Center worden ondersteund](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)voor een lijst met ondersteunde versies.

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows<a name="windows-machines"></a>

Azure Security Center integreert met Azure-Services om uw op Windows gebaseerde computers te controleren en te beveiligen. Security Center toont de waarschuwingen en suggesties voor herstel van al deze services in een gemakkelijk te gebruiken indeling.

### Windows Server Defender ATP<a nanme="windows-atp"></a>

Security Center breidt de beveiligings platforms voor Cloud werkbelasting uit door te integreren met behulp van Windows Server Defender Advanced Threat Protection (ATP). Dit biedt uitgebreide functionaliteit voor eindpunt detectie en-antwoorden (EDR).

> [!NOTE]
> De Windows Server Defender ATP-sensor wordt automatisch ingeschakeld op Windows-servers die gebruikmaken van Security Center.

Wanneer Windows Server Defender ATP een bedreiging detecteert, wordt er een waarschuwing gegenereerd. De waarschuwing wordt weer gegeven op het Security Center dash board. U kunt vanuit het dash board naar de Windows Defender ATP-console draaien en een gedetailleerd onderzoek uitvoeren om het bereik van de aanval te ontdekken. Zie voor meer informatie over Windows Server Defender ATP [servers onboarding naar de Windows Defender ATP-service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Crash dump analyse<a nanme="windows-dump"></a>

Wanneer software vastloopt, bevat een crashdump een gedeelte van het geheugen ten tijde van de crash.

Een crash kan zijn veroorzaakt door malware of schadelijke software bevatten. Om te voor komen dat wordt gedetecteerd door beveiligings producten, gebruiken verschillende vormen van malware een aanval zonder bestanden, waarmee het schrijven naar schijf of het versleutelen van software onderdelen die naar de schijf worden geschreven, wordt voor komen. Dit type aanval is moeilijk te detecteren met traditionele benaderingen op basis van schijven.

Door gebruik te maken van geheugen analyse, kunt u dit soort aanvallen echter detecteren. Door het geheugen in de crash dump te analyseren, kunt Security Center de technieken detecteren die de aanval gebruikt. De aanval kan bijvoorbeeld proberen misbruik te maken van beveiligings problemen in de software, toegang te krijgen tot vertrouwelijke gegevens en ongemerkt blijven bestaan binnen een gemanipuleerde computer. Security Center werkt dit met minimale gevolgen voor de prestaties van hosts.

> [!div class="mx-tableFixed"]

|Waarschuwing|Description|
|---|---|
|**Code injectie gedetecteerd**|Code-injectie is het invoegen van uitvoerbare modules in actieve processen of threads. Deze techniek wordt gebruikt door schadelijke software om toegang te krijgen tot gegevens, terwijl ze zichzelf verbergen om te voor komen dat ze worden gevonden en verwijderd. <br/>Met deze waarschuwing wordt aangegeven dat de crashdump een geïnjecteerde module bevat. Om onderscheid te maken tussen kwaad aardige en niet-schadelijke geïnjecteerde modules Security Center controleert of de geïnjecteerde module voldoet aan een profiel van verdacht gedrag.|
|**Er is een verdacht code segment gedetecteerd**|Hiermee wordt aangegeven dat een code segment is toegewezen met behulp van niet-standaard methoden, zoals reflectieve injectie en verlegen van het proces. De waarschuwing bevat aanvullende kenmerken van het code segment dat is verwerkt om context te bieden voor de mogelijkheden en het gedrag van het gerapporteerde code segment.|
|**Shell code gedetecteerd**|Shellcode is de nettolading die wordt uitgevoerd nadat de schadelijke software misbruik heeft gemaakt van een beveiligingslek in de software.<br/>Deze waarschuwing geeft aan dat de crash dump analyse uitvoer bare code heeft gedetecteerd die het gedrag vertoont dat gewoonlijk wordt uitgevoerd door schadelijke nettoladingen. Hoewel niet-schadelijke software dit gedrag ook kan uitvoeren, is het niet gebruikelijk om normaal software te ontwikkelen.|

### Detectie van bestanden met een aanval<a nanme="windows-fileless"></a>

Aanvallen die gericht zijn op uw eind punten, zijn gebruikelijk. Om detectie te voor komen, worden met aanvallen zonder bestanden schadelijke nettoladingen in het geheugen geïnjecteerd. Nettoladingen van aanvallers blijven aanwezig in het geheugen van verdachte processen en kunnen een breed scala aan schadelijke activiteiten uitvoeren.

Met de aanvals detectie op basis van een bestand, identificeren forensische-technieken met geautomatiseerd geheugen aanvals Toolkit, technieken en gedragingen. Met deze oplossing wordt uw machine periodiek gescand tijdens runtime en worden inzichten direct opgehaald uit het geheugen van essentiële beveiligings processen.

Er wordt bewezen van exploitatie, code injectie en de uitvoering van schadelijke nettoladingen. Detectie van een aanval met een bestand genereert gedetailleerde beveiligings waarschuwingen voor het versnellen van waarschuwings sorteren, correlatie en downstream-reactie tijd. Deze aanpak is een aanvulling op op gebeurtenissen gebaseerde EDR-oplossingen, waardoor er meer detectie dekking is.

> [!NOTE]
> U kunt Windows-waarschuwingen simuleren door [Azure Security Center Playbook te downloaden: Beveiligings waarschuwingen](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).

> [!div class="mx-tableFixed"]

|Waarschuwing|Description|
|---|---|
|**Aanvals techniek met bestanden gedetecteerd**|Het geheugen van het opgegeven proces bevat een aanvals Toolkit met een bestand. Meterpreter. Aanvals toolkiten zonder bestanden hebben doorgaans geen aanwezigheid op het bestands systeem, waardoor detectie door traditionele antivirus software lastig is.|

### <a name="further-reading"></a>Lees hier meer over

Voor voor beelden en meer informatie over Security Center detectie raadpleegt u:

* [Hoe Azure Security Center de detectie van Cyber Attack automatiseert](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Hoe Azure Security Center beveiligings problemen detecteert met systeem beheer](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Spreek<a name="linux-machines"></a>

Security Center controleert controle records van Linux-machines met behulp van **controle**, een van de meest voorkomende Linux-controle raamwerken. de levens duur van de mainline-kernel wordt gecontroleerd. 

### Door Linux gecontroleerde waarschuwingen en integratie met micro soft Monitoring Agent (MMA)<a name="linux-auditd"></a>

Het gecontroleerde systeem bestaat uit een subsysteem op kernelniveau dat verantwoordelijk is voor het bewaken van systeem aanroepen. Ze worden gefilterd op basis van een opgegeven regelset en er worden berichten naar een socket geschreven. Security Center integreert de functionaliteit van het gecontroleerde pakket binnen de micro soft Monitoring Agent (MMA). Met deze integratie kunnen gecontroleerde gebeurtenissen in alle ondersteunde Linux-distributies worden verzameld, zonder dat hiervoor vereisten gelden.  

gecontroleerde records worden verzameld, verrijkt en geaggregeerd naar gebeurtenissen met behulp van de Linux MMA-agent. Security Center doorlopend nieuwe analyses toevoegen die gebruikmaken van Linux-signalen om schadelijk gedrag op Cloud-en on-premises Linux-machines te detecteren. Net als bij Windows-mogelijkheden, deze analyse bevinden zich in verdachte processen, dubious-aanmeldings pogingen, laden van de kernel-module en andere activiteiten. Deze activiteiten kunnen erop wijzen dat een machine een aanval ondervindt of is geschonden.  

Hier volgen enkele voor beelden van analyses die betrekking hebben op verschillende fasen van de levens cyclus van de aanval.

> [!div class="mx-tableFixed"]

|Waarschuwing|Description|
|---|---|
|**Proces dat wordt weer gegeven op een ongebruikelijke manier toegang tot het bestand met SSH-geautoriseerde sleutels**|Een bestand met SSH-geautoriseerde sleutels is geopend in een methode die vergelijkbaar is met bekende malware-campagnes. Deze toegang kan erop wijzen dat een aanvaller permanente toegang tot een computer probeert te krijgen.|
|**Gedetecteerde persistentie-poging**|Er is met de analyse van de hostgegevens gedetecteerd dat er een opstart script voor de modus voor één gebruiker is geïnstalleerd. <br/>Omdat een rechtmatig proces zou moeten worden uitgevoerd in deze modus, kan dit erop wijzen dat een aanvaller een schadelijk proces heeft toegevoegd aan elk uitvoerings niveau om persistentie te garanderen.|
|**Manipulatie van geplande taken gedetecteerd**|Er is door de analyse van hostgegevens een mogelijke manipulatie van geplande taken gedetecteerd. Aanvallers voegen vaak geplande taken toe aan computers die zijn aangetast om persistentie te verkrijgen.|
|**Wijziging in verdacht bestand tijds tempel**|Er is een verdachte wijziging van de tijds tempel gedetecteerd. Aanvallers kopiëren vaak tijds tempels van bestaande, rechtmatige bestanden naar nieuwe hulpprogram ma's om te voor komen dat deze nieuwe verwijderde bestanden worden gedetecteerd.|
|**Er is een nieuwe gebruiker toegevoegd aan de groep sudo**|Er is door de analyse van hostgegevens gedetecteerd dat een gebruiker is toegevoegd aan de sudo-groep, waardoor de leden opdrachten met hoge bevoegdheden kunnen uitvoeren.|
|**Waarschijnlijke misbruik van DynoRoot-beveiligingslek in de DHCP-client**|De analyse van hostgegevens heeft de uitvoering van een ongebruikelijke opdracht gedetecteerd, met een bovenliggend proces van dhclient script.|
|**Verdachte kernel-module gedetecteerd**|Er is door de analyse van hostgegevens een gedeeld object bestand gevonden dat wordt geladen als een kernel-module. Dit kan legitieme activiteiten zijn of een indicatie dat er is geknoeid met een van uw computers.|
|**Het proces dat is gekoppeld aan de analyse van digitale valuta is gedetecteerd**|De analyse van hostgegevens heeft de uitvoering van een proces gedetecteerd dat normaal gesp roken samenhangt met de digitale valuta analyse.|
|**Mogelijk poort door sturen naar extern IP-adres**|De analyse van de hostgegevens heeft de initiëring van het door sturen van poorten gedetecteerd naar een extern IP-adres.|

> [!NOTE]
> U kunt Windows-waarschuwingen simuleren door [Azure Security Center Playbook te downloaden: Linux-detecties](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).


Zie voor meer informatie:  

* [Maak gebruik van Azure Security Center om te detecteren wanneer inbreuk op Linux-machines is aangevallen](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure Security Center kunnen opkomende beveiligings problemen in Linux detecteren](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 