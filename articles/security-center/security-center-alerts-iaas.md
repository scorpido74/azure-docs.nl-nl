---
title: Detectie van bedreigingen voor Vm's en servers in Azure Security Center | Microsoft Docs
description: Dit onderwerp bevat een overzicht van de VM-en server waarschuwingen die beschikbaar zijn in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a5ed91cef6e49fcb71c35f2262479be45a018651
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754301"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Detectie van bedreigingen voor Vm's en servers in Azure Security Center

Dit onderwerp bevat de verschillende typen detectie methoden en waarschuwingen die beschikbaar zijn voor virtuele machines en servers met de volgende besturings systemen. Zie [platforms en functies die door Azure Security Center worden ondersteund](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)voor een lijst met ondersteunde versies.

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows<a name="windows-machines"></a>

Azure Security Center integreert met Azure-Services om uw op Windows gebaseerde computers te controleren en te beveiligen. Security Center toont de waarschuwingen en suggesties voor herstel van al deze services in een gemakkelijk te gebruiken indeling.

* **Micro soft Defender ATP** <a name="windows-atp"></a> -Security Center breidt de beveiligings platformen voor Cloud werkbelasting uit door te integreren met micro soft Defender Advanced Threat Protection (ATP). Dit biedt uitgebreide functionaliteit voor eindpunt detectie en-antwoorden (EDR).

    > [!NOTE]
    > De micro soft Defender ATP-sensor wordt automatisch ingeschakeld op Windows-servers die gebruikmaken van Security Center.

    Wanneer micro soft Defender ATP een bedreiging detecteert, wordt er een waarschuwing gegenereerd. De waarschuwing wordt weer gegeven op het Security Center dash board. Vanuit het dash board kunt u naar de micro soft Defender ATP-console draaien en een gedetailleerd onderzoek uitvoeren om het bereik van de aanval te ontdekken. Zie voor meer informatie over micro soft Defender ATP [servers onboarding voor de micro soft Defender ATP-service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Crash dump analyse** <a name="windows-dump"></a> : wanneer software vastloopt, legt een crash dump een deel van het geheugen vast op het moment dat het vastloopt.

    Een crash kan zijn veroorzaakt door malware of schadelijke software bevatten. Om te voor komen dat wordt gedetecteerd door beveiligings producten, gebruiken verschillende vormen van malware een aanval zonder bestanden, waarmee het schrijven naar schijf of het versleutelen van software onderdelen die naar de schijf worden geschreven, wordt voor komen. Dit type aanval is moeilijk te detecteren met traditionele benaderingen op basis van schijven.

    Door gebruik te maken van geheugen analyse, kunt u dit soort aanvallen echter detecteren. Door het geheugen in de crash dump te analyseren, kunt Security Center de technieken detecteren die de aanval gebruikt. De aanval kan bijvoorbeeld proberen misbruik te maken van beveiligings problemen in de software, toegang te krijgen tot vertrouwelijke gegevens en ongemerkt blijven bestaan binnen een gemanipuleerde computer. Security Center werkt dit met minimale gevolgen voor de prestaties van hosts.

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-windows)voor meer informatie over de waarschuwingen voor de crash dump analyse.

* **Detectie** <a name="windows-fileless"></a> van aanval via een bestand: aanvallen waarbij de eind punten zijn gericht op de bedoelingen, zijn gebruikelijk. Om detectie te voor komen, worden met aanvallen zonder bestanden schadelijke nettoladingen in het geheugen geïnjecteerd. Nettoladingen van aanvallers blijven aanwezig in het geheugen van verdachte processen en kunnen een breed scala aan schadelijke activiteiten uitvoeren.

    Met de aanvals detectie op basis van een bestand, identificeren forensische-technieken met geautomatiseerd geheugen aanvals Toolkit, technieken en gedragingen. Met deze oplossing wordt uw machine periodiek gescand tijdens runtime en worden inzichten direct opgehaald uit het geheugen van essentiële beveiligings processen.

    Er wordt bewezen van exploitatie, code injectie en de uitvoering van schadelijke nettoladingen. Detectie van een aanval met een bestand genereert gedetailleerde beveiligings waarschuwingen voor het versnellen van waarschuwings sorteren, correlatie en downstream-reactie tijd. Deze aanpak is een aanvulling op op gebeurtenissen gebaseerde EDR-oplossingen, waardoor er meer detectie dekking is.

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-windows)voor meer informatie over de waarschuwingen voor detectie van aanvals bestanden.

> [!NOTE]
> U kunt Windows-waarschuwingen simuleren door [Azure Security Center Playbook: Security Alerts](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)te downloaden.

## Spreek<a name="linux-machines"></a>

Security Center controleert controle records van Linux-machines met behulp van **controle**, een van de meest voorkomende Linux-controle raamwerken. de levens duur van de mainline-kernel wordt gecontroleerd. 

* Door <a name="linux-auditd"></a> **Linux gecontroleerde waarschuwingen en integratie met micro soft Monitoring Agent (MMA)** : het gecontroleerde systeem bestaat uit een subsysteem op kernelniveau, dat verantwoordelijk is voor het bewaken van systeem aanroepen. Ze worden gefilterd op basis van een opgegeven regelset en er worden berichten naar een socket geschreven. Security Center integreert de functionaliteit van het gecontroleerde pakket binnen de micro soft Monitoring Agent (MMA). Met deze integratie kunnen gecontroleerde gebeurtenissen in alle ondersteunde Linux-distributies worden verzameld, zonder dat hiervoor vereisten gelden.  

    gecontroleerde records worden verzameld, verrijkt en geaggregeerd naar gebeurtenissen met behulp van de Linux MMA-agent. Security Center doorlopend nieuwe analyses toevoegen die gebruikmaken van Linux-signalen om schadelijk gedrag op Cloud-en on-premises Linux-machines te detecteren. Net als bij Windows-mogelijkheden, deze analyse bevinden zich in verdachte processen, dubious-aanmeldings pogingen, laden van de kernel-module en andere activiteiten. Deze activiteiten kunnen erop wijzen dat een machine een aanval ondervindt of is geschonden.  

    Hier volgen enkele voor beelden van analyses die betrekking hebben op verschillende fasen van de levens cyclus van de aanval.

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-linux)voor een lijst met de Linux-waarschuwingen.

> [!NOTE]
> U kunt Linux-waarschuwingen simuleren door [Azure Security Center Playbook te downloaden: Linux-detecties](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).

 
 ## <a name="next-steps"></a>Volgende stappen

Voor voor beelden en meer informatie over Security Center detectie raadpleegt u:

* [Hoe Azure Security Center de detectie van Cyber Attack automatiseert](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Hoe Azure Security Center beveiligings problemen detecteert met systeem beheer](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [Maak gebruik van Azure Security Center om te detecteren wanneer inbreuk op Linux-machines is aangevallen](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure Security Center kunnen opkomende beveiligings problemen in Linux detecteren](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)