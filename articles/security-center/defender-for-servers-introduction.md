---
title: 'Azure Defender voor servers: de voor delen en functies'
description: Meer informatie over de voor delen en functies van Azure Defender voor servers.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 29f15aa7d1cbcd260ce44f6083138681ceb9fd79
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936560"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Inleiding tot Azure Defender voor servers

Azure Defender voor servers voegt bedreigingen voor detectie en geavanceerde beveiliging toe voor uw Windows-en Linux-computers.

Voor Windows integreert Azure Defender met Azure-Services om uw op Windows gebaseerde computers te controleren en te beveiligen. Security Center toont de waarschuwingen en suggesties voor herstel van al deze services in een gemakkelijk te gebruiken indeling.

Voor Linux verzamelt Azure Defender controle records van Linux-machines met behulp van **gecontroleerde**, een van de meest voorkomende Linux-controle raamwerken. de levens duur van de mainline-kernel wordt gecontroleerd. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Wat zijn de voor delen van Azure Defender voor servers?

De detectie-en beveiligings mogelijkheden van dreigingen van Azure Defender voor servers zijn onder andere:

- **Scan voor evaluatie van beveiligings problemen voor vm's** -de beveiligings scanner die is opgenomen in azure Security Center, wordt aangedreven door Qualys. 

    Qualys ' scanner is een van de toonaangevende hulp middelen voor het real-time identificeren van beveiligings problemen in uw Azure-Virtual Machines. U hebt geen Qualys-licentie nodig of zelfs een Qualys-account: alles wordt naadloos verwerkt binnen Security Center. [Meer informatie](deploy-vulnerability-assessment-vm.md).

- Just **-in-time-VM-toegang** : Threat actors bewaart actief toegankelijke computers met open-beheer poorten, zoals RDP of SSH. Al uw virtuele machines zijn potentiële doelen voor een aanval. Wanneer een virtuele machine is aangetast, wordt deze als toegangs punt gebruikt om verdere resources in uw omgeving aan te vallen.

    Wanneer u Azure Defender voor servers inschakelt, kunt u just-in-time-VM-toegang gebruiken om het inkomende verkeer naar uw Vm's te vergren delen, waardoor de bloot stelling aan aanvallen wordt verkleind. [Meer informatie](just-in-time-explained.md).

- Met **FIM (File Integrity Monitoring)** (FIM) (File Integrity Monitoring), ook wel bekend als wijzigings controle, worden bestanden en registers van het besturings systeem, toepassings software en anderen gecontroleerd op wijzigingen die mogelijk duiden op een aanval. Een vergelijkings methode wordt gebruikt om te bepalen of de huidige status van het bestand afwijkt van de laatste scan van het bestand. U kunt deze vergelijking gebruiken om te bepalen of er geldige of verdachte wijzigingen zijn aangebracht in uw bestanden.

    Wanneer u Azure Defender voor servers inschakelt, kunt u FIM gebruiken om de integriteit van Windows-bestanden, uw Windows-registers en Linux-bestanden te valideren. [Meer informatie](security-center-file-integrity-monitoring.md).

- Besturings elementen voor adaptieve **toepassings controles (AAC)** zijn een intelligente en geautomatiseerde oplossing voor het definiëren van toestaan van lijsten met bekende veilige toepassingen voor uw machines.

    Wanneer u besturings elementen voor adaptieve toepassingen hebt ingeschakeld en geconfigureerd, ontvangt u beveiligings waarschuwingen als een andere toepassing dan de toepassingen die u hebt gedefinieerd als veilig worden uitgevoerd. [Meer informatie](security-center-adaptive-network-hardening.md).

- **Adaptieve netwerk beveiliging (ANH)** : het Toep assen van netwerk beveiligings groepen (NSG) voor het filteren van verkeer naar en van resources, verbetert uw netwerk beveiliging postuur. Er kunnen echter wel enkele gevallen zijn waarin het daad werkelijke verkeer dat via de NSG stroomt, een subset is van de gedefinieerde NSG-regels. In dergelijke gevallen kunt u de beveiligings postuur verder verbeteren door de NSG-regels te verfijnen op basis van de werkelijke verkeers patronen.

    Adaptieve netwerk beveiliging biedt aanbevelingen voor verdere beveiliging van de NSG-regels. Er wordt gebruikgemaakt van een machine learning algoritme dat betrekking heeft op het werkelijke verkeer, bekende vertrouwde configuratie, bedreigings informatie en andere indica toren van inbreuk, en geeft aanbevelingen om alleen verkeer van specifieke IP-poort-Tuples toe te staan. [Meer informatie](security-center-adaptive-network-hardening.md).

- **Integratie met micro soft Defender Advanced Threat Protection (ATP) (alleen Windows)** : Azure Defender integreert met micro soft Defender Advanced Threat Protection (ATP). Samen bieden ze uitgebreide functionaliteit voor eindpunt detectie en-antwoorden (EDR). [Meer informatie](security-center-wdatp.md).

    > [!IMPORTANT]
    > De micro soft Defender ATP-sensor wordt automatisch ingeschakeld op Windows-servers die gebruikmaken van Security Center.

    Wanneer micro soft Defender ATP een bedreiging detecteert, wordt er een waarschuwing gegenereerd. De waarschuwing wordt weer gegeven in Security Center. Vanuit Security Center kunt u ook naar de micro soft Defender ATP-console draaien en een gedetailleerd onderzoek uitvoeren om het bereik van de aanval te ontdekken. Zie voor meer informatie over micro soft Defender ATP [servers onboarding voor de micro soft Defender ATP-service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

- Met de **beveiliging van docker host** -Azure Security Center worden niet-beheerde containers aangeduid die worden gehost op IaaS Linux-vm's of andere Linux-machines waarop docker-containers worden uitgevoerd. Security Center doorlopend de configuraties van deze containers evalueren. Vervolgens worden ze vergeleken met de CIS-Referentie (Center for Internet Security). Security Center bevat de volledige regelset van de CIS docker-Bench Mark en waarschuwt u als uw containers niet voldoen aan een van de besturings elementen. [Meer informatie](harden-docker-hosts.md).

- **Detectie van bestanden zonder een aanval (alleen Windows)** : aanvallen zonder bestanden voeren schadelijke nettoladingen in het geheugen in om detectie door op schijf gebaseerde scan technieken te voor komen. De nettolading van de aanvaller bevindt zich vervolgens in het geheugen van verdachte processen en voert een breed scala aan schadelijke activiteiten uit.

  Met de aanvals detectie op basis van een bestand, identificeren forensische-technieken met geautomatiseerd geheugen aanvals Toolkit, technieken en gedragingen. Met deze oplossing wordt uw machine periodiek gescand tijdens runtime en worden inzichten direct opgehaald uit het geheugen van processen. Specifieke inzichten voor Linux bevatten de identificatie van: 

  - Bekende tool kits en crypto analyse-software 

  - Shell code, een klein stukje code dat doorgaans wordt gebruikt als de payload bij de exploitatie van een software beveiligings probleem.

  - Geïnjecteerd schadelijk uitvoerbaar bestand in het proces geheugen

  Detectie van een aanval met een bestand genereert gedetailleerde beveiligings waarschuwingen met de beschrijvingen met aanvullende proces meta gegevens, zoals netwerk activiteit. Dit versnelt de reactie tijd van de waarschuwing sorteren, correlatie en downstream. Deze benadering vormt een aanvulling op op gebeurtenissen gebaseerde EDR-oplossingen en biedt meer detectie dekking.

  Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-windows)voor meer informatie over de waarschuwingen voor detectie van aanvals bestanden.

- Door **Linux gecontroleerde waarschuwingen en integratie van log Analytics agent (alleen Linux)** : het gecontroleerde systeem bestaat uit een subsysteem op kernelniveau, dat verantwoordelijk is voor het bewaken van systeem aanroepen. Ze worden gefilterd op basis van een opgegeven regelset en er worden berichten naar een socket geschreven. Security Center integreert de functionaliteit van het gecontroleerde pakket binnen de Log Analytics-agent. Met deze integratie kunnen gecontroleerde gebeurtenissen in alle ondersteunde Linux-distributies worden verzameld, zonder dat hiervoor vereisten gelden.

    gecontroleerde records worden verzameld, verrijkt en geaggregeerd in gebeurtenissen met behulp van de Log Analytics agent voor Linux agent. Security Center doorlopend nieuwe analyses toevoegen die gebruikmaken van Linux-signalen om schadelijk gedrag op Cloud-en on-premises Linux-machines te detecteren. Net als bij Windows-mogelijkheden, deze analyse bevinden zich in verdachte processen, dubious-aanmeldings pogingen, laden van de kernel-module en andere activiteiten. Deze activiteiten kunnen erop wijzen dat een machine een aanval ondervindt of is geschonden.  

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-linux)voor een lijst met de Linux-waarschuwingen.


## <a name="simulating-alerts"></a>Waarschuwingen simuleren

U kunt waarschuwingen simuleren door een van de volgende playbooks te downloaden:

- Voor Windows: [Azure Security Center Playbook: beveiligings waarschuwingen](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

- Voor Linux: [Azure Security Center Playbook: Linux-detecties](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).




## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Azure Defender voor servers. 

Raadpleeg de volgende artikelen voor gerelateerde materialen: 

- Of een waarschuwing wordt gegenereerd door Security Center, of door Security Center van een ander beveiligings product is ontvangen, kunt u het exporteren. Als u uw waarschuwingen wilt exporteren naar Azure Sentinel, eventuele SIEM van derden of een ander extern hulp programma, volgt u de instructies in [waarschuwingen exporteren naar een Siem](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Azure Defender inschakelen](security-center-pricing.md)