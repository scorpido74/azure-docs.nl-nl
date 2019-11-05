---
title: Uw computers en toepassingen beveiligen in Azure Security Center | Microsoft Docs
description: Dit document bevat aanbevelingen in Security Center waarmee u uw virtuele machines en computers en uw web-apps en App Service omgevingen kunt beveiligen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 2317d0206e3fc4a342606d17c172ac42cbe82870
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520679"
---
# <a name="protecting-your-machines-and-applications-in-azure-security-center"></a>Uw computers en toepassingen in Azure Security Center beveiligen
Azure Security Center analyseert de beveiligings status van uw Azure-resources, niet-Azure-servers en virtuele machines. Wanneer Security Center mogelijke beveiligings problemen identificeert, worden er aanbevelingen gemaakt die u door het proces van het configureren van de benodigde besturings elementen leiden. Aanbevelingen zijn van toepassing op Azure-resource typen: virtuele machines (Vm's) en computers, toepassingen, netwerken, SQL en identiteit en toegang.

Dit artikel heeft betrekking op aanbevelingen die van toepassing zijn op machines en toepassingen.

## <a name="monitoring-security-health"></a>Beveiligingsstatus bewaken
U kunt de beveiligings status van uw resources controleren op het dash board **Security Center – Overview** . De sectie **bronnen** bevat het aantal gedetecteerde problemen en de beveiligings status voor elk resource type.

U kunt een lijst met alle problemen weer geven door **aanbevelingen**te selecteren. Zie [Beveiligingsaanbevelingen implementeren in Azure Security Center](security-center-recommendations.md) voor meer informatie over het toepassen van aanbevelingen.

Zie [aanbevelingen](security-center-virtual-machine-protection.md#compute-and-app-recs)voor een volledige lijst met aanbevelingen voor reken-en app-Services.

Als u wilt door gaan, selecteert u **compute & apps** onder **resources** of in het hoofd menu van Security Center.
![Security Center-dash board](./media/security-center-virtual-machine-recommendations/overview.png)

## <a name="monitor-compute-and-app-services"></a>Reken-en app-services bewaken
Onder **compute &-apps**bevinden zich de volgende tabbladen:

- **Overzicht**: bewaking en aanbevelingen door Security Center.
- **VM’s en computers**: lijst met alle virtuele machines, computers en de bijbehorende actuele beveiligingsstatussen.
- **Cloud Services**: lijst van al uw web- en werkrollen die worden bewaakt door Security Center.
- **App Services**: een lijst met uw app service-omgevingen en de huidige beveiligings status van elk.
- **Containers**: een lijst met uw containers en de beveiligings beoordeling van de configuraties.
- **Reken resources (preview)** : een lijst met aanbevelingen voor uw reken resources, zoals service Fabric clusters en Event hubs.

Als u wilt door gaan, selecteert u **compute & apps** onder **resource Security-hygiëne**.

![Compute](./media/security-center-virtual-machine-recommendations/compute.png)

Op elk tabblad kunt u meerdere sectie hebben en in elke sectie kunt u een afzonderlijke optie selecteren voor meer informatie over de aanbevolen stappen voor het oplossen van het specifieke probleem.

### Niet-bewaakte Vm's en computers<a name="unmonitored-vms-and-computers"></a>
Een virtuele machine of computer wordt niet bewaakt door Security Center als op de computer niet de micro soft Monitoring Agent-extensie wordt uitgevoerd. Op een computer is mogelijk al een lokale agent geïnstalleerd, bijvoorbeeld de OMS direct-agent of de System Center Operations Manager-agent. Computers met deze agents worden geïdentificeerd als niet-bewaakt omdat deze agents niet volledig worden ondersteund in Security Center. De Microsoft Monitoring Agent-extensie is vereist om volledig te profiteren van alle mogelijkheden van Security Center.

U kunt de uitbrei ding op de niet-bewaakte virtuele machine of computer installeren naast de reeds geïnstalleerde lokale agent. Configureer beide agents op dezelfde manier en verbind ze met dezelfde werkruimte. Hierdoor kan Security Center communiceren met de Microsoft Monitoring Agent-extensie en gegevens verzamelen. Zie [Enable the VM extension](../azure-monitor/learn/quick-collect-azurevm.md) (De VM-extensie inschakelen) voor installatie-instructies voor de Microsoft Monitoring Agent-extensie.

Zie [Problemen met de agentstatus controleren](security-center-troubleshooting-guide.md#mon-agent) voor meer informatie over de redenen waarom Security Center VM's en computers die zijn geïnitialiseerd voor automatische inrichting niet afdoende kan bewaken.

### <a name="recommendations"></a>Aanbevelingen
Deze sectie bevat een reeks aanbevelingen voor elke VM en computer, Web-en werk rollen, Azure App Service Web Apps en Azure App Service Environment die Security Center monitors. De eerste kolom bevat de aanbeveling. De tweede kolom bevat het totale aantal resources dat wordt beïnvloed door deze aanbeveling. De derde kolom toont de ernst van het probleem.

Elke aanbeveling heeft een set acties die u kunt uitvoeren nadat u deze hebt geselecteerd. Als u bijvoorbeeld **ontbrekende systeem updates**selecteert, wordt het aantal vm's en computers waarvoor patches ontbreken, en de ernst van de ontbrekende update weer gegeven.

**Systeem updates Toep assen** bevat een samen vatting van essentiële updates in een grafiek indeling, één voor Windows en één voor Linux. Het tweede gedeelte bevat een tabel met de volgende informatie:

- **NAAM**: naam van de ontbrekende update.
- **Nee. VAN Vm's & COMPUTERS**: totaal aantal vm's en computers waarop deze update ontbreekt.
- **Update Ernst**: hierin wordt de ernst van de betreffende aanbeveling beschreven:

    - **Kritiek**: er bestaat een beveiligings probleem met een zinvolle resource (toepassing, virtuele machine of netwerk beveiligings groep) en vereist aandacht.
    - **Belang rijk**: er zijn niet-kritieke of extra stappen nodig om een proces te volt ooien of een beveiligings probleem op te lossen.
    - **Gemiddeld**: er moet een beveiligings probleem worden opgelost, maar dit vereist geen onmiddellijke aandacht. (Aanbevelingen met de ernstaanduiding Laag worden niet standaard weergegeven, maar u kunt hierop filteren als u deze aanbevelingen wilt bekijken.)


- **STATUS**: de huidige status van de aanbeveling:

    - **Open**: de aanbeveling is nog niet opgelost.
    - **Wordt uitgevoerd**: de aanbeveling wordt momenteel toegepast op deze resources en u hoeft geen actie te ondernemen.
    - **Opgelost**: de aanbeveling is al voltooid. (Als het probleem is opgelost, wordt de vermelding grijs).

Klik op de naam van de ontbrekende update om meer informatie voor de aanbeveling weer te geven.


> [!NOTE]
> De aanbevelingen voor beveiliging zijn hetzelfde als die in de tegel **aanbevelingen** . Zie [beveiligings aanbevelingen implementeren in azure Security Center](security-center-recommendations.md)voor meer informatie over het oplossen van aanbevelingen.
>
>

### <a name="vms-and-computers"></a>Vm's en computers
De sectie Vm's en computers biedt een overzicht van alle aanbevelingen voor de virtuele machine en de computer. Elke kolom vertegenwoordigt een reeks aanbevelingen.

![Aanbevelingen voor de computer en virtuele machine](./media/security-center-virtual-machine-recommendations/vm-computers.png)

Er zijn vier typen pictogrammen die in deze lijst worden weer gegeven:

![Niet-Azure-computer](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computer zonder Azure.

![Azure Resource Manager VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM.

![Klassieke Azure-VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Klassieke Azure-VM.


![Vm's die zijn geïdentificeerd in de werk ruimte](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) Virtuele machines die alleen zijn geïdentificeerd vanuit de werkruimte die deel uitmaakt van het weergegeven abonnement. Dit omvat Vm's van andere abonnementen die rapporteren aan de werk ruimte in dit abonnement en Vm's die met Operations Manager direct agent zijn geïnstalleerd en geen Resource-ID hebben.

Het pictogram dat onder elke aanbeveling wordt weer gegeven, helpt u snel de virtuele machine en computer te identificeren die aandacht nodig hebben, en het type aanbeveling. U kunt ook de filters gebruiken om de lijst te doorzoeken op **resource type** en op **Ernst**.

Als u wilt inzoomen op de beveiligings aanbevelingen voor elke virtuele machine, klikt u op de virtuele machine.
Hier ziet u de beveiligings Details voor de virtuele machine of computer. Onderaan ziet u de aanbevolen actie en de ernst van elk probleem.
![Cloudservices](./media/security-center-virtual-machine-recommendations/recommendation-list.png)

### <a name="cloud-services"></a>Cloud services
Voor Cloud Services wordt een aanbeveling gemaakt wanneer de versie van het besturings systeem verouderd is.

![Cloud services](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

In een scenario waarin u een aanbeveling hebt (wat niet het geval is voor het vorige voor beeld), moet u de stappen in de aanbeveling volgen om de versie van het besturings systeem bij te werken. Wanneer een update beschikbaar is, ontvangt u een waarschuwing (rood of oranje, afhankelijk van de ernst van het probleem). Wanneer u deze waarschuwing in de WebRole1 (Windows Server wordt uitgevoerd met uw web-app automatisch geïmplementeerd in IIS) of WorkerRole1 (Windows Server wordt uitgevoerd met uw web-app automatisch geïmplementeerd in IIS), ziet u meer informatie over deze aanbeveling.

Als u een meer beschrijvende uitleg wilt zien over deze aanbeveling, klikt u op **Besturingssysteemversie bijwerken** onder de kolom **BESCHRIJVING**.



![Besturingssysteemversie bijwerken](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png)

### <a name="app-services"></a>App-services
Als u de App Service informatie wilt weer geven, moet u App Service inschakelen in uw abonnement. Zie [app service beveiligen met Azure Security Center](security-center-app-services.md)voor instructies over het inschakelen van deze functie.
[!NOTE]
> Bewakings App Service is in de preview-fase en is alleen beschikbaar voor de standaard-laag van Security Center.


Onder **app Services**vindt u een lijst met uw app service-omgevingen en het status overzicht op basis van de evaluatie Security Center uitgevoerd.

![App-services](./media/security-center-virtual-machine-recommendations/app-services.png)

In deze lijst worden drie typen pictogrammen weer gegeven:

![App Services-omgeving](./media/security-center-virtual-machine-recommendations/ase.png) App Services-omgeving.

![Webtoepassing](./media/security-center-virtual-machine-recommendations/web-app.png) Webtoepassing.

![Functie toepassing](./media/security-center-virtual-machine-recommendations/function-app.png) Functie toepassing.

1. Selecteer een webtoepassing. Er wordt een samen vatting weer gegeven met drie tabbladen:

   - **Aanbevelingen**: gebaseerd op evaluaties die zijn uitgevoerd door Security Center die zijn mislukt.
   - **Geslaagde evaluaties**: een lijst met evaluaties die zijn uitgevoerd door Security Center die zijn door gegeven.
   - **Niet-beschik bare evaluaties**: lijst met evaluaties die niet kunnen worden uitgevoerd vanwege een fout of omdat de aanbeveling niet relevant is voor de specifieke app service

   Onder **aanbevelingen** vindt u een lijst met aanbevelingen voor de geselecteerde webtoepassing en ernst van elke aanbeveling.

   ![Aanbevelingen voor App Services](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

2. Selecteer een aanbeveling voor een beschrijving van de aanbeveling en een lijst met slechte resources, in orde zijnde resources en niet-gescande resources.

   - Onder de kolom **door gegeven beoordelingen** vindt u een lijst met door gegeven evaluaties.  De ernst van deze evaluaties is altijd groen.

   - Selecteer een door gegeven evaluatie in de lijst voor een beschrijving van de evaluatie, een lijst met slechte en gezonde resources en een lijst met niet-gescande resources. Er is een tabblad voor beschadigde resources, maar deze lijst is altijd leeg sinds de evaluatie is geslaagd.

     ![App Service herstel](./media/security-center-virtual-machine-recommendations/app-service-remediation.png)

## <a name="virtual-machine-scale-sets"></a>Virtuele-machineschaalsets
Security Center detecteert automatisch of u schaal sets hebt ingesteld en raadt u aan om micro soft Monitoring Agent op deze schaal sets te installeren. 

De micro soft Monitoring Agent installeren: 

1. Selecteer de aanbeveling **de bewakings agent installeren op de virtuele-machine schaalset**. U krijgt een lijst met niet-bewaakte schaal sets.
2. Selecteer een onjuiste schaalset. Volg de instructies voor het installeren van de bewakings agent met behulp van een bestaande gevulde werk ruimte of maak een nieuwe. Zorg ervoor dat u de [prijs categorie](security-center-pricing.md) voor de werk ruimte instelt als deze niet is ingesteld.

   ![MMS installeren](./media/security-center-virtual-machine-recommendations/install-mms.png)

Nieuwe schaal sets instellen om micro soft monitoring agent automatisch te installeren:
1. Ga naar Azure Policy en klik op **definities**.
2. Zoek naar het beleid **implementeren log Analytics agent voor virtuele-machine schaal sets voor Windows** en klik erop.
3. Klik op **Toewijzen**.
4. Stel het **bereik** en de **log Analytics-werk ruimte** in en klik op **toewijzen**.

Als u alle bestaande schaal sets wilt instellen om micro soft Monitoring Agent te installeren, gaat u in Azure Policy naar **herstel** en past u het bestaande beleid toe op bestaande schaal sets.


## Aanbevelingen voor Compute en apps<a name="compute-and-app-recs"></a>
|Resourcetype|Beveiligingsscore|Aanbeveling|Beschrijving|
|----|----|----|----|
|App service|20|Webtoepassing mag alleen toegankelijk zijn via HTTPS|Beperk de toegang tot webtoepassingen alleen via HTTPS.|
|App service|20|functie-app mag alleen toegankelijk zijn via HTTPS|Beperk de toegang tot functie-apps alleen via HTTPS.|
|App service|5|Diagnostische logboeken in App Services moeten worden ingeschakeld|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|App service|10|Externe fout opsporing moet worden uitgeschakeld voor webtoepassing|Schakel fout opsporing voor webtoepassingen uit als u deze niet meer nodig hebt. Voor fout opsporing op afstand moeten binnenkomende poorten worden geopend op een functie-app.|
|App service|10|Fout opsporing op afstand moet worden uitgeschakeld voor functie toepassing|Schakel fout opsporing voor functie-app uit als u deze niet meer nodig hebt. Voor fout opsporing op afstand moeten binnenkomende poorten worden geopend op een functie-app.|
|App service|10|Niet alle (' * ') bronnen toestaan om toegang te krijgen tot uw toepassing| Stel de para meter WEBSITE_LOAD_CERTIFICATES niet in op ' '. Als u de para meter instelt op ' ', worden alle certificaten geladen in het persoonlijke certificaat archief van uw webtoepassingen. Dit kan leiden tot misbruik van het principe van minimale bevoegdheden, omdat het onwaarschijnlijk is dat de site toegang moet hebben tot alle certificaten tijdens runtime.|
|App service|20|CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw webtoepassingen|Stel alleen vereiste domeinen in staat om te communiceren met uw webtoepassing. Cross Origin Resource Sharing (CORS) mag niet toestaan dat alle domeinen toegang hebben tot uw webtoepassing.|
|App service|20|CORS mag niet alle bronnen toestaan om toegang te krijgen tot uw functie-app| Sta toe dat alleen de vereiste domeinen met uw functie toepassing kunnen communiceren. Cross Origin Resource Sharing (CORS) mag niet toestaan dat alle domeinen toegang hebben tot uw functie toepassing.|
|Reken resources (batch)|1|Metrische waarschuwings regels moeten worden geconfigureerd voor batch-accounts|Metrische waarschuwings regels configureren voor een batch-account en de metrische groep inschakelen Complete gebeurtenissen en groep verwijderen start gebeurtenissen verwijderen|
|Reken resources (Service Fabric)|10|Service Fabric-clusters mogen alleen gebruikmaken van Azure Active Directory voor client verificatie|Voer client verificatie alleen uit via Azure Active Directory in Service Fabric.|
|Reken bronnen (Automation-account)|5|De variabelen van het Automation-account moeten worden versleuteld|Versleuteling van variabele-assets van Automation-account inschakelen bij het opslaan van gevoelige gegevens.|
|Reken bronnen (zoeken)|5|Het inschakelen van Diagnostische logboeken voor zoek services controleren|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken resources (Service Bus)|5|Diagnostische logboeken in Service Bus moeten worden ingeschakeld|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken bronnen (stream Analytics)|5|Diagnostische logboeken in Azure Stream Analytics moeten worden ingeschakeld|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken resources (batch)|5|Diagnostische logboeken inschakelen in batch-accounts|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken bronnen (Event Hub)|5|Diagnostische logboeken in Event hub moeten worden ingeschakeld|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken resources (Logic apps)|5|Diagnostische logboeken inschakelen in Logic Apps|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. |
|Reken resources (Service Fabric)|15|Stel de eigenschap ClusterProtectionLevel in op EncryptAndSign in Service Fabric|Service Fabric biedt drie beveiligings niveaus (geen, ondertekening en EncryptAndSign) voor communicatie tussen knoop punten met behulp van een primair cluster certificaat.  Stel het beveiligings niveau in om ervoor te zorgen dat alle knoop punt-naar-knooppunt berichten zijn versleuteld en digitaal zijn ondertekend. |
|Reken resources (Service Bus)|1|Alle autorisatie regels verwijderen behalve RootManageSharedAccessKey uit Service Bus naam ruimte |Service Bus-clients mogen geen toegangs beleid op naam ruimte niveau gebruiken dat toegang biedt tot alle wacht rijen en onderwerpen in een naam ruimte. Als u wilt uitlijnen met het beveiligings model met minimale bevoegdheden, moet u toegangs beleid maken op het niveau van de entiteit voor wacht rijen en onderwerpen om alleen toegang te verlenen aan de specifieke entiteit.|
|Reken bronnen (Event Hub)|1|Alle autorisatie regels behalve RootManageSharedAccessKey moeten worden verwijderd uit de Event hub-naam ruimte|Event hub-clients mogen geen toegangs beleid op naam ruimte niveau gebruiken dat toegang biedt tot alle wacht rijen en onderwerpen in een naam ruimte. Als u wilt uitlijnen met het beveiligings model met minimale bevoegdheden, moet u toegangs beleid maken op het niveau van de entiteit voor wacht rijen en onderwerpen om alleen toegang te verlenen aan de specifieke entiteit.|
|Reken bronnen (Event Hub)|5|Autorisatie regels voor de Event hub-entiteit moeten worden gedefinieerd|Verificatie regels controleren op de Event hub-entiteit om beperkte toegang te verlenen.|
|Machine|50|Bewakings agent op uw computers installeren|Installeer de bewakings agent om het verzamelen van gegevens, het scannen van updates, het scannen van basis lijnen en Endpoint Protection op elke computer in te scha kelen.|
|Machine|50|Automatische inrichting en gegevens verzameling inschakelen voor uw abonnementen |Schakel automatische inrichting en gegevens verzameling voor computers in uw abonnementen in om het verzamelen van gegevens, het scannen van updates, het scannen van basis lijnen en Endpoint Protection op elke computer die is toegevoegd aan uw abonnementen in te scha kelen.|
|Machine|40|Problemen met de status van de bewakings agent op uw computers oplossen|Los de problemen met de bewakings agent op uw computers op door de instructies in de hand leiding voor probleem oplossing te volgen voor volledige Security Center beveiliging| 
|Machine|40|Problemen met de status van Endpoint Protection op uw computers oplossen|Los de problemen met de bewakings agent op uw computers op door de instructies in de hand leiding voor het oplossen van problemen te volgen voor volledige Security Center beveiliging.|
|Machine|40|Problemen met ontbrekende scan gegevens op uw computers oplossen|Problemen met ontbrekende scan gegevens op virtuele machines en computers oplossen. Ontbrekende scan gegevens op uw computers leiden tot ontbrekende beveiligings beoordelingen zoals het scannen van updates, het scannen van basis lijnen en het scannen van de eindpunt beveiligings oplossing ontbreekt.|
|Machine|40|Systeem updates moeten worden geïnstalleerd op uw computers|Ontbrekende systeem beveiliging en essentiële updates installeren om uw virtuele Windows-en Linux-machines en-computers te beveiligen
|Machine|15|Een firewall voor webtoepassingen toevoegen| Implementeer een Web Application Firewall-oplossing (WAF) om uw webtoepassingen te beveiligen. |
|Machine|40|De versie van het besturings systeem bijwerken voor de Cloud service rollen|Werk de versie van het besturings systeem (OS) bij voor de Cloud service rollen in de meest recente versie die beschikbaar is voor uw besturingssysteem familie.|
|Machine|35|Beveiligings problemen in de beveiligings configuratie op uw computers moeten worden hersteld|Herstel beveiligings problemen in beveiligings configuratie op uw computers om ze te beschermen tegen aanvallen.|
|Machine|35|Beveiligings problemen in de beveiligings configuratie in uw containers herstellen|Herstel beveiligings problemen in beveiligings configuratie op computers waarop docker is geïnstalleerd om ze te beschermen tegen aanvallen.|
|Machine|25|Besturings elementen voor adaptieve toepassingen inschakelen|Schakel toepassings beheer in om te bepalen welke toepassingen kunnen worden uitgevoerd op uw virtuele machines die zich in azure bevinden. Dit helpt uw Vm's te beschermen tegen schadelijke software. Security Center gebruikt machine learning voor het analyseren van de toepassingen die op elke virtuele machine worden uitgevoerd en helpt u bij het Toep assen van regels die deze intelligentie gebruiken toe te passen. Deze mogelijkheid vereenvoudigt het proces van het configureren en onderhouden van regels voor het toestaan van toepassingen.|
|Machine|20|Endpoint Protection-oplossing op uw computers installeren|Installeer een Endpoint Protection-oplossing op uw virtuele machines om ze te beschermen tegen bedreigingen en beveiligings problemen.|
|Machine|20|Start uw machines opnieuw op om systeem updates toe te passen|Start uw machines opnieuw op om de systeem updates toe te passen en de computer te beveiligen tegen beveiligings problemen.|
|Machine|15|Schijf versleuteling moet worden toegepast op virtuele machines|Versleutel de schijven van uw virtuele machine met Azure Disk Encryption voor virtuele Windows-en Linux-machines. Azure Disk Encryption (ADE) maakt gebruik van de industrie standaard BitLocker-functie van Windows en de DM-cryptografie functie van Linux om besturings systeem-en gegevens schijven te versleutelen om uw gegevens te beschermen en te beveiligen en om te voldoen aan de beveiliging en naleving van uw organisatie verplichtingen in de Azure-sleutel kluis van de klant. Als aan uw nalevings-en beveiligings vereiste de gegevens end moet worden versleuteld met behulp van uw versleutelings sleutels, inclusief versleuteling van de tijdelijke (lokaal gekoppelde tijdelijke) schijf, moet u gebruikmaken van Azure Disk Encryption. Managed Disks worden standaard versleuteld op rest met behulp van Azure Storage-service versleuteling, waarbij de versleutelings sleutels door micro soft beheerde sleutels zijn in Azure. Als deze voldoet aan uw nalevings-en beveiligings vereisten, kunt u gebruikmaken van de standaard Managed Disk Encryption om te voldoen aan uw vereisten.|
|Machine|30|Een oplossing voor de evaluatie van beveiligings problemen installeren op uw virtuele machines|Een oplossing voor de evaluatie van beveiligings problemen installeren op uw virtuele machines|
|Machine|15|Een firewall voor webtoepassingen toevoegen| Implementeer een Web Application Firewall-oplossing (WAF) om uw webtoepassingen te beveiligen. |
|Machine|30|Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken|Virtuele machines waarvoor een beveiligings probleem van een oplossing van derden wordt geïmplementeerd, worden voortdurend beoordeeld op basis van de beveiligings problemen van toepassingen en besturings systemen. Wanneer dergelijke beveiligings problemen worden gevonden, zijn deze beschikbaar voor meer informatie als onderdeel van de aanbeveling.|
|Machine|30|Een oplossing voor de evaluatie van beveiligings problemen installeren op uw virtuele machines|Een oplossing voor de evaluatie van beveiligings problemen installeren op uw virtuele machines|
|Machine|1|Virtuele machines moeten worden gemigreerd naar nieuwe AzureRM-resources|Gebruik Azure Resource Manager voor uw virtuele machines om beveiligings uitbreidingen te bieden zoals: sterker toegangs beheer (RBAC), betere controle, implementatie en beheer op basis van Resource Manager, toegang tot beheerde identiteiten, toegang tot de sleutel kluis voor geheimen, Verificatie op basis van Azure AD en ondersteuning voor Tags en resource groepen voor eenvoudiger beveiligings beheer. |
|Machine|30|Beveiligings problemen moeten worden opgelost met een oplossing voor de evaluatie van de beveiligings lekken|Virtuele machines waarvoor een beveiligings probleem van een oplossing van derden wordt geïmplementeerd, worden voortdurend beoordeeld op basis van de beveiligings problemen van toepassingen en besturings systemen. Wanneer dergelijke beveiligings problemen worden gevonden, zijn deze beschikbaar voor meer informatie als onderdeel van de aanbeveling.|
|Schaalset voor virtuele machines |4|Diagnostische logboeken in Virtual Machine Scale Sets moeten worden ingeschakeld|Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor opnieuw maken voor onderzoek doeleinden. Dit is handig als er een beveiligings incident optreedt of als uw netwerk is aangetast.|
|Schaalset voor virtuele machines|35|Beveiligings problemen in de beveiligings configuratie van de schaal sets van virtuele machines moeten worden hersteld|Herstel beveiligings problemen in de beveiligings configuratie van de schaal sets voor virtuele machines om ze te beschermen tegen aanvallen. |
|Schaalset voor virtuele machines|5|Endpoint Protection-status fouten herstellen op schaal sets voor virtuele machines|Herstel de status van eindpunt beveiliging op uw schaal sets voor virtuele machines om ze te beschermen tegen bedreigingen en beveiligings problemen. |
|Schaalset voor virtuele machines|10|Endpoint Protection moet worden geïnstalleerd op virtuele machines|Installeer een Endpoint Protection-oplossing op uw schaal sets voor virtuele machines om ze te beschermen tegen bedreigingen en beveiligings problemen. |
|Schaalset voor virtuele machines|40|Systeem updates op virtuele-machine schaal sets moeten worden geïnstalleerd|Installeer de ontbrekende systeem beveiliging en essentiële updates om de schaal sets voor virtuele Windows-en Linux-machines te beveiligen. |
 





## <a name="next-steps"></a>Volgende stappen
Zie het volgende voor meer informatie over de aanbevelingen die van toepassing zijn op andere Azure-resource typen:


* [Uw machines en toepassingen beveiligen in Azure Security Center](security-center-virtual-machine-protection.md)
* [Identiteit en toegang bewaken in Azure Security Center](security-center-identity-access.md)
* [Protecting your network in Azure Security Center](security-center-network-recommendations.md) (Uw netwerk beveiligen in Azure Security Center)
* [Uw Azure SQL-service beveiligen in Azure Security Center](security-center-sql-service-recommendations.md)

Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [Azure Security Center FAQ](security-center-faq.md) (Veelgestelde vragen over Azure Security Center): raadpleeg veelgestelde vragen over het gebruik van de service.

