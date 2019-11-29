---
title: Veelgestelde vragen over Azure Security Center | Microsoft Docs
description: Deze veelgestelde vragen beantwoordt vragen over Azure Security Center, een product dat u helpt bij het voor komen, detecteren en reageren op bedreigingen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: memildin
ms.openlocfilehash: 896db06204188c4347fbdced0b1bb3f216f56ef9
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558667"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Veelgestelde vragen over Azure Security Center
Deze veelgestelde vragen beantwoordt vragen over Azure Security Center, een service die u helpt bedreigingen te voor komen, te detecteren en erop te reageren met verbeterde zicht baarheid en controle over de beveiliging van uw Microsoft Azure resources.

> [!NOTE]
> Security Center gebruikt de micro soft Monitoring Agent voor het verzamelen en opslaan van gegevens. Zie [Azure Security Center platform Migration](security-center-platform-migration.md)(Engelstalig) voor meer informatie.
>
>

## <a name="general-questions"></a>Algemene vragen
### <a name="what-is-azure-security-center"></a>Wat is Azure Beveiligingscentrum?
Azure Security Center helpt u bij het detecteren, voorkomen van en reageren op bedreigingen dankzij een verhoogde zichtbaarheid van en controle over de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden blijven, en werkt met een uitgebreid ecosysteem van beveiligingsoplossingen.

### <a name="how-do-i-get-azure-security-center"></a>Hoe kan ik Azure Security Center ophalen?
Azure Security Center is ingeschakeld met uw Microsoft Azure-abonnement en is toegankelijk via de [Azure Portal](https://azure.microsoft.com/features/azure-portal/). ([Meld u aan bij de portal](https://portal.azure.com), selecteer **Bladeren**en schuif naar **Security Center**).  

## <a name="billing"></a>Billing
### <a name="how-does-billing-work-for-azure-security-center"></a>Hoe werkt de facturering voor Azure Security Center?
Security Center wordt aangeboden in twee prijscategorieën:

De **gratis laag** biedt inzicht in de beveiligings status van uw Azure-resources, basis beveiligings beleid, aanbevelingen voor beveiliging en integratie met beveiligings producten en-services van partners.

De **laag standaard** biedt geavanceerde mogelijkheden voor detectie van bedreigingen, zoals bedreigings informatie, gedrags analyse, afwijkings detectie, beveiligings incidenten en rapporten voor risico toewijzing. U kunt een gratis proef versie van de Standard-laag starten. Als u een upgrade wilt uitvoeren, selecteert u [prijs categorie](https://docs.microsoft.com/azure/security-center/security-center-pricing) in het beveiligings beleid. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

### <a name="how-can-i-track-who-in-my-organization-performed-pricing-tier-changes-in-azure-security-center"></a>Hoe kan ik bijhouden wie in mijn organisatie de prijs categorie heeft gewijzigd in Azure Security Center
Azure-abonnementen kunnen meerdere beheerders met machtigingen hebben om de prijs categorie te wijzigen. Als u wilt weten welke gebruiker een wijziging in de prijs categorie heeft uitgevoerd, gebruikt u het Azure-activiteiten logboek. Zie [hier](https://techcommunity.microsoft.com/t5/Security-Identity/Tracking-Changes-in-the-Pricing-Tier-for-Azure-Security-Center/td-p/390832)voor meer informatie.

## <a name="permissions"></a>Machtigingen
Het Azure Beveiligingscentrum gebruikt [op rollen gebaseerd toegangsbeheer (RBAC)](../role-based-access-control/role-assignments-portal.md), dat [ingebouwde rollen](../role-based-access-control/built-in-roles.md) biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

Security Center evalueert de configuratie van uw resources om beveiligings problemen en beveiligings lekken te identificeren. In Security Center ziet u alleen informatie die betrekking heeft op een resource als u de rol van eigenaar, bijdrager of lezer hebt toegewezen aan het abonnement of de resource groep waarvan een resource deel uitmaakt.

Zie [machtigingen in azure Security Center](security-center-permissions.md) voor meer informatie over rollen en toegestane acties in Security Center.

## <a name="data-collection-agents-and-workspaces"></a>Gegevens verzameling, agents en werk ruimten
Security Center verzamelt gegevens van uw Azure virtual machines (Vm's), schaal sets voor virtuele machines, IaaS containers en niet-Azure-computers (inclusief on-premises) om te controleren op beveiligings problemen en bedreigingen. De gegevens worden verzameld met behulp van de MMA, die verschillende configuraties en gebeurtenislogboeken met betrekking tot beveiliging van de machine leest en de gegevens kopieert naar uw werkruimte voor analyse.

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Ben ik gefactureerd voor Azure Monitor-logboeken op de werk ruimten die door Security Center zijn gemaakt?
Nee. Werk ruimten die zijn gemaakt door Security Center en die zijn geconfigureerd voor Azure Monitor-logboeken per knoop punt, worden niet in rekening gebracht Azure Monitor logboek kosten. Security Center facturering is altijd gebaseerd op uw Security Center beveiligings beleid en de oplossingen die zijn geïnstalleerd op een werk ruimte:

- **Gratis laag** – Security Center maakt de oplossing ' SecurityCenterFree ' in de standaardwerk ruimte. Er worden geen kosten in rekening gebracht voor de gratis laag.
- **Standard-laag** – Security Center maakt de oplossing beveiliging in de standaardwerk ruimte.

Zie [Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie over prijzen.

> [!NOTE]
> De prijs categorie voor log Analytics van werk ruimten die zijn gemaakt door Security Center heeft geen invloed op Security Center facturering.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Wat is een virtuele machine die in aanmerking komt voor automatische inrichting van de installatie van micro soft Monitoring Agent?
Windows-of Linux IaaS-Vm's komen in aanmerking als:

- De uitbrei ding voor micro soft monitoring agent is momenteel niet geïnstalleerd op de VM.
- De virtuele machine wordt uitgevoerd.
- De Windows-of Linux [Azure virtual machine-agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) is geïnstalleerd.
- De virtuele machine wordt niet gebruikt als een apparaat zoals Web Application Firewall of een firewall van de volgende generatie.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Kan ik de standaardwerk ruimten verwijderen die zijn gemaakt door Security Center?
**Het is niet raadzaam de standaardwerk ruimte te verwijderen.** Security Center gebruikt de standaardwerk ruimte voor het opslaan van beveiligings gegevens van uw virtuele machines.  Als u een werk ruimte verwijdert, kan Security Center deze gegevens niet verzamelen. sommige beveiligings aanbevelingen en-waarschuwingen zijn niet beschikbaar.

Als u wilt herstellen, verwijdert u micro soft Monitoring Agent op de virtuele machines die zijn verbonden met de verwijderde werk ruimte. Security Center installeert de agent opnieuw en maakt nieuwe standaardwerk ruimten.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hoe kan ik mijn bestaande Log Analytics-werk ruimte gebruiken?

U kunt een bestaande Log Analytics-werk ruimte selecteren om gegevens op te slaan die zijn verzameld door Security Center. Uw bestaande Log Analytics-werk ruimte gebruiken:

- De werk ruimte moet worden gekoppeld aan uw geselecteerde Azure-abonnement.
- U moet mini maal lees machtigingen hebben voor toegang tot de werk ruimte.

Een bestaande Log Analytics-werk ruimte selecteren:

1. Onder **beveiligings beleid – gegevens verzamelen**selecteert **u een andere werk ruimte gebruiken**.

   ![Een andere werk ruimte gebruiken][5]

2. Selecteer in de vervolg keuzelijst een werk ruimte om de verzamelde gegevens op te slaan.

   > [!NOTE]
   > In het vervolg keuzemenu worden alleen werk ruimten weer gegeven waartoe u toegang hebt en die zich in uw Azure-abonnement bevinden.
   >
   >

3. Selecteer **Opslaan**.
4. Nadat u **Opslaan**hebt geselecteerd, wordt u gevraagd of u de bewaakte vm's opnieuw wilt configureren.

   - Selecteer **Nee** als u de nieuwe werkruimte instellingen **alleen op nieuwe vm's wilt Toep assen**. De nieuwe werk ruimte-instellingen zijn alleen van toepassing op nieuwe agent installaties. nieuw gedetecteerde Vm's waarop de micro soft Monitoring Agent niet is geïnstalleerd.
   - Selecteer **Ja** als u de nieuwe werkruimte instellingen wilt **Toep assen op alle vm's**. Daarnaast wordt elke VM die is verbonden met een Security Center gemaakte werk ruimte opnieuw verbonden met de nieuwe doel werkruimte.

   > [!NOTE]
   > Als u Ja selecteert, moet u de werk ruimte (s) die zijn gemaakt door Security Center, pas verwijderen als alle virtuele machines opnieuw zijn verbonden met de nieuwe doel werkruimte. Deze bewerking mislukt als er te vroeg een werk ruimte wordt verwijderd.
   >
   >

   - Selecteer **Annuleren** om de bewerking te annuleren.

### Wat gebeurt er als micro soft Monitoring Agent al is geïnstalleerd als een uitbrei ding op de VM?<a name="mmaextensioninstalled"></a>
Wanneer de bewakings agent is geïnstalleerd als een uitbrei ding, staat de extensie configuratie slechts aan één werk ruimte toe. Security Center worden bestaande verbindingen met gebruikers ruimten niet overschreven. Security Center worden beveiligings gegevens van een virtuele machine opgeslagen in een werk ruimte die al is verbonden, op voor waarde dat de oplossing Security of SecurityCenterFree is geïnstalleerd. Security Center kunt de extensie versie in dit proces upgraden naar de meest recente versie.

Zie [automatische inrichting in het geval van een bestaande Agent installatie](security-center-enable-data-collection.md#preexisting)voor meer informatie.


### Wat moet ik doen als ik een micro soft Monitoring Agent heb geïnstalleerd op de computer, maar niet als een uitbrei ding (direct agent)?<a name="directagentinstalled"></a>
Als de micro soft Monitoring Agent rechtstreeks op de virtuele machine is geïnstalleerd (niet als een Azure-extensie), wordt de micro soft Monitoring Agent-extensie door Security Center geïnstalleerd en kan micro soft Monitoring Agent worden bijgewerkt naar de nieuwste versie.
De agent die is geïnstalleerd, blijft rapporteren aan de al geconfigureerde werk ruimte (n) en rapporteert bovendien aan de werk ruimte die is geconfigureerd in Security Center (multi-multihoming wordt ondersteund op Windows-computers).
Als de geconfigureerde werk ruimte een gebruikers werkruimte is (niet Security Center de standaardwerk ruimte), moet u de oplossing ' Security/' SecurityCenterFree ' installeren voor Security Center om te beginnen met het verwerken van gebeurtenissen van Vm's en computers die aan die werk ruimte rapporteren.

Voor Linux-machines wordt agent-multi-multihoming nog niet ondersteund. als er een bestaande agent wordt gedetecteerd, treedt er geen automatische inrichting op en wordt de configuratie van de computer niet gewijzigd.

Voor bestaande machines op abonnementen die vóór 17 2019 maart op Security Center worden uitgevoerd, wordt de micro soft Monitoring Agent-extensie niet geïnstalleerd en wordt de computer niet beïnvloed als er een bestaande agent wordt gedetecteerd. Voor deze computers raadpleegt u de aanbeveling bewakings agent status problemen op uw computers oplossen om de installatie problemen van de agent op deze computers op te lossen

 Voor meer informatie raadpleegt u de volgende sectie [wat er gebeurt als er al een System Center Operations Manager of OMS direct-agent is geïnstalleerd op mijn VM?](#scomomsinstalled)

### Wat gebeurt er als er al een System Center Operations Manager-agent is geïnstalleerd op mijn VM?<a name="scomomsinstalled"></a>
In Security Center wordt de micro soft Monitoring Agent-extensie naast elkaar geïnstalleerd met de bestaande System Center Operations Manager-agent. De bestaande agent blijft normaal aan de System Center Operations Manager-server rapporteren. Houd er rekening mee dat de Operations Manager agent en micro soft Monitoring Agent gemeen schappelijke runtime bibliotheken delen, die tijdens dit proces worden bijgewerkt naar de meest recente versie. Opmerking: als versie 2012 van de Operations Manager-agent is geïnstalleerd, moet u niet automatisch inrichten inschakelen (beheer mogelijkheden kunnen verloren gaan als de Operations Manager server ook versie 2012 is).

### <a name="what-is-the-impact-of-removing-these-extensions"></a>Wat is de impact van het verwijderen van deze uitbrei dingen?
Als u de micro soft-bewakings extensie verwijdert, kan Security Center geen beveiligings gegevens van de virtuele machine verzamelen. sommige beveiligings aanbevelingen en-waarschuwingen zijn niet beschikbaar. Binnen 24 uur bepaalt Security Center dat de VM de uitbrei ding mist en de uitbrei ding opnieuw installeert.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hoe kan ik de automatische agent installatie en het maken van de werk ruimte stoppen?
U kunt automatische inrichting voor uw abonnementen uitschakelen in het beveiligings beleid, maar dit wordt niet aanbevolen. Automatische inrichtings limieten Security Center aanbevelingen en waarschuwingen uitschakelen. Automatische inrichting uitschakelen:

1. Als uw abonnement is geconfigureerd voor de Standard-laag, opent u het beveiligings beleid voor dat abonnement en selecteert u de laag **gratis** .

   ![Prijscategorie][1]

2. Schakel vervolgens automatische inrichting uit door **uit** te scha kelen op de pagina **beveiligings beleid – gegevens verzameling** .
   ![Gegevensverzameling][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Moet ik de automatische agent installatie en het maken van de werk ruimte afmelden?

> [!NOTE]
> Zorg ervoor dat u secties bekijkt [Wat zijn de gevolgen van het afmelden?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) en de [Aanbevolen stappen bij](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) het afmelden als u ervoor kiest om u af te melden voor automatische inrichting.
>
>

U kunt het beste niet automatisch inrichten als het volgende van toepassing is op u:

- Automatische agent installatie door Security Center is van toepassing op het hele abonnement. U kunt automatische installatie niet Toep assen op een subset van Vm's. Als er kritieke Vm's zijn die niet kunnen worden geïnstalleerd met micro soft Monitoring Agent, kunt u het beste niet automatisch inrichten.
- Door de installatie van de micro soft Monitoring Agent (MMA)-extensie wordt de versie van de agent bijgewerkt. Dit geldt voor een directe agent en een System Center Operations Manager-agent (in de laatste, de Operations Manager en MMA delen common runtime-bibliotheken die in het proces worden bijgewerkt). Als de geïnstalleerde Operations Manager-agent versie 2012 is en is bijgewerkt, kunnen de beheer baarheids mogelijkheden verloren gaan wanneer de Operations Manager-server ook versie 2012 is. Houd er rekening mee dat u het automatisch inrichten kunt uitschakelen als de geïnstalleerde Operations Manager-agent versie 2012 is.
- Als u een aangepaste werk ruimte hebt die extern is voor het abonnement (een gecentraliseerde werk ruimte), moet u zich afmelden voor automatische inrichting. U kunt de micro soft Monitoring Agent-extensie hand matig installeren en verbinding maken met uw werk ruimte zonder Security Center de verbinding te overschrijven.
- Als u wilt voor komen dat meerdere werk ruimten per abonnement worden gemaakt en u uw eigen aangepaste werk ruimte in het abonnement hebt, hebt u twee opties:

   1. U kunt zich afmelden voor automatische inrichting. Stel na de migratie de standaard instellingen voor de werk ruimte in, zoals wordt beschreven in [Hoe kan ik mijn bestaande log Analytics werkruimte gebruiken?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. U kunt ook toestaan dat de migratie is voltooid, de micro soft monitoring agent wordt geïnstalleerd op de virtuele machines en de virtuele machines die zijn verbonden met de gemaakte werk ruimte. Selecteer vervolgens uw eigen aangepaste werk ruimte door de standaard instelling voor werk ruimten in te stellen met de optie voor het opnieuw configureren van de reeds geïnstalleerde agents. Zie [Hoe kan ik mijn bestaande log Analytics werkruimte gebruiken?](#how-can-i-use-my-existing-log-analytics-workspace) voor meer informatie.

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Wat zijn de gevolgen van het afmelden van automatische inrichting?
Wanneer de migratie is voltooid, kan Security Center geen beveiligings gegevens van de virtuele machine verzamelen en sommige beveiligings aanbevelingen en-waarschuwingen zijn niet beschikbaar. Als u zich afmeldt, installeert u micro soft Monitoring Agent hand matig. Bekijk de [Aanbevolen stappen bij het afmelden](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Wat zijn de aanbevolen stappen bij het afmelden van automatische inrichting?

Installeer de micro soft Monitoring Agent-extensie hand matig zodat Security Center beveiligings gegevens van uw virtuele machines kunt verzamelen en aanbevelingen en waarschuwingen moet geven. Zie [installatie van de agent voor](../virtual-machines/extensions/oms-windows.md) de installatie van de Windows-VM of de [agent voor Linux VM](../virtual-machines/extensions/oms-linux.md) voor meer informatie over de installatie.

U kunt de agent verbinden met een bestaande aangepaste werk ruimte of Security Center gemaakte werk ruimte. Als voor een aangepaste werk ruimte de oplossingen Security of SecurityCenterFree niet zijn ingeschakeld, moet u een oplossing Toep assen. Als u wilt Toep assen, selecteert u de aangepaste werk ruimte of het abonnement en past u een prijs categorie toe via de pagina **beveiligings beleid – prijs categorie** .

   ![Prijscategorie][1]

Met Security Center wordt de juiste oplossing op basis van de geselecteerde prijs categorie op de werk ruimte ingeschakeld.

### Hoe kan ik OMS-extensies die door Security Center zijn geïnstalleerd, verwijderen?<a name="remove-oms"></a>
U kunt micro soft Monitoring Agent hand matig verwijderen. Dit wordt niet aanbevolen omdat deze Security Center aanbevelingen en waarschuwingen beperkt.

> [!NOTE]
> Als gegevens verzameling is ingeschakeld, wordt de agent door Security Center opnieuw geïnstalleerd nadat u deze hebt verwijderd.  U moet de gegevens verzameling uitschakelen voordat u de agent hand matig verwijdert. Zie Hoe kan ik de automatische agent installatie en het maken van de werk ruimte stoppen? voor instructies voor het uitschakelen van gegevens verzameling.
>
>

Hand matig verwijderen van de agent:

1.  Open **log Analytics**in de portal.
2.  Selecteer op de pagina Log Analytics een werk ruimte:
3.  Selecteer de virtuele machines die u niet wilt bewaken en selecteer **verbinding verbreken**.

   ![De agent verwijderen][3]

> [!NOTE]
> Als een virtuele Linux-machine al een OMS-agent heeft die niet uitbrei ding is en de uitbrei ding verwijdert, wordt ook de agent verwijderd en moet de klant deze opnieuw installeren.
>
>
### <a name="how-do-i-disable-data-collection"></a>Gegevens verzameling Hoe kan ik uitschakelen?
Automatische inrichting is standaard uitgeschakeld. U kunt op elk gewenst moment automatische inrichting van resources uitschakelen door deze instelling uit te scha kelen in het beveiligings beleid. Automatisch inrichten wordt sterk aanbevolen om beveiligings waarschuwingen en aanbevelingen te ontvangen over systeem updates, OS-beveiligings problemen en Endpoint Protection.

Als u gegevens verzameling wilt uitschakelen, [meldt u zich aan bij de Azure Portal](https://portal.azure.com), selecteert u **Bladeren**, selecteert u **Security Center**en selecteert u **vervolgens beleid selecteren**. Selecteer het abonnement waarvoor u automatisch inrichten wilt uitschakelen. Wanneer u een beveiligings beleid voor abonnementen selecteert **: gegevens verzamelen** wordt geopend. Onder **automatische inrichting**selecteert u **uit**.

### <a name="how-do-i-enable-data-collection"></a>Gegevens verzameling Hoe kan ik inschakelen?
U kunt het verzamelen van gegevens voor uw Azure-abonnement inschakelen in het beveiligings beleid. Om het verzamelen van gegevens in te scha kelen. [Meld u aan bij de Azure Portal](https://portal.azure.com), selecteer **bladeren**, selecteer **Security Center**en selecteer **beveiligings beleid**. Selecteer het abonnement waarvoor u automatische inrichting wilt inschakelen. Wanneer u een beveiligings beleid voor abonnementen selecteert **: gegevens verzamelen** wordt geopend. Onder **automatische inrichting**selecteert u **aan**.

### <a name="what-happens-when-data-collection-is-enabled"></a>Wat gebeurt er wanneer gegevens verzameling is ingeschakeld?
Als automatische inrichting is ingeschakeld, wordt de micro soft monitoring agent door Security Center ingericht op alle ondersteunde virtuele machines van Azure en nieuwe die worden gemaakt. Automatische inrichting wordt aanbevolen, maar er is ook hand matige installatie van de agent beschikbaar. [Informatie over het installeren van de Microsoft Monitoring Agent-extensie](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

De agent maakt de gebeurtenis 4688 voor het maken van processen en het veld *commandline* in gebeurtenis 4688 mogelijk. Nieuwe processen die op de virtuele machine worden gemaakt, worden vastgelegd door EventLog en gecontroleerd door de detectie services van Security Center. Zie voor meer informatie over de gegevens die voor elk nieuwe proces zijn vastgelegd de [velden Beschrijving in 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). De agent verzamelt ook de 4688-gebeurtenissen die zijn gemaakt op de virtuele machine en slaat ze op in de zoek opdracht.

De agent maakt ook het verzamelen van gegevens mogelijk voor [adaptieve toepassings besturings elementen](security-center-adaptive-application.md), Security Center configureert een lokaal AppLocker-beleid in de controle modus om alle toepassingen toe te staan. Dit beleid zorgt ervoor dat AppLocker gebeurtenissen genereert die vervolgens worden verzameld en gebruikt door Security Center. Het is belang rijk te weten dat dit beleid niet wordt geconfigureerd op computers waarop al een geconfigureerd AppLocker-beleid is ingesteld. 

Als Security Center verdachte activiteiten op de virtuele machine detecteert, wordt de klant per e-mail op de hoogte gesteld als er [contact gegevens](security-center-provide-security-contact-details.md) voor de beveiliging zijn verstrekt. Er is ook een waarschuwing zichtbaar in het dash board beveiligings waarschuwingen van Security Center.

### <a name="will-security-center-work-using-an-oms-gateway"></a>Werkt Security Center met behulp van een OMS-gateway?
Ja. Azure Security Center maakt gebruik van Azure Monitor voor het verzamelen van gegevens van Azure-Vm's en-servers, met behulp van micro soft monitoring agent.
Voor het verzamelen van de gegevens moet elke virtuele machine en server verbinding maken met internet met behulp van HTTPS. De verbinding kan direct worden gebruikt, met behulp van een proxy of via de [OMS-gateway](../azure-monitor/platform/gateway.md).

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Heeft de bewakings agent invloed op de prestaties van mijn servers?
De agent verbruikt een nominale hoeveelheid systeem bronnen en moet weinig invloed hebben op de prestaties. Zie de [plannings-en bedienings handleiding](security-center-planning-and-operations-guide.md#data-collection-and-storage)voor meer informatie over de invloed op de prestaties en de agent en de uitbrei ding.

### <a name="where-is-my-data-stored"></a>Waar worden mijn gegevens opgeslagen?
Gegevens die van deze agent worden verzameld, worden opgeslagen in een bestaande Log Analytics werk ruimte die is gekoppeld aan uw abonnement of een nieuwe werk ruimte. Zie [Data Security](security-center-data-security.md)(Engelstalig) voor meer informatie.

## Bestaande Azure Monitor-logboeken klanten<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Worden bestaande verbindingen tussen Vm's en werk ruimten Security Center overschreven?
Als de micro soft Monitoring Agent al is geïnstalleerd op een virtuele machine, wordt de bestaande werkruimte verbinding niet overschreven met Security Center. In plaats daarvan maakt Security Center gebruik van de bestaande werk ruimte. De virtuele machine wordt beveiligd, op voor waarde dat de oplossing ' Security ' of ' SecurityCenterFree ' is geïnstalleerd op de werk ruimte waarmee deze wordt gerapporteerd. 

Er wordt een Security Center oplossing geïnstalleerd op de werk ruimte die is geselecteerd in het scherm voor het verzamelen van gegevens, als deze nog niet aanwezig is, en de oplossing wordt alleen toegepast op de relevante Vm's. Wanneer u een oplossing toevoegt, wordt deze automatisch geïmplementeerd op alle Windows-en Linux-agents die zijn verbonden met uw Log Analytics-werk ruimte. Met [doel items van oplossingen](../operations-management-suite/operations-management-suite-solution-targeting.md) kunt u een bereik Toep assen op uw oplossingen.

Als micro soft Monitoring Agent rechtstreeks op de virtuele machine is geïnstalleerd (niet als een Azure-extensie), worden de micro soft Monitoring Agent en de beveiligings controle niet door Security Center geïnstalleerd.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Installeert Security Center oplossingen op mijn bestaande Log Analytics-werk ruimten? Wat zijn de gevolgen voor facturering?
Als Security Center identificeert dat er al een virtuele machine is verbonden met een werk ruimte die u hebt gemaakt, Security Center de oplossingen in deze werk ruimte inschakelen volgens de prijs categorie. De oplossingen worden alleen toegepast op de relevante Azure-Vm's, via [oplossings doelen](../operations-management-suite/operations-management-suite-solution-targeting.md), zodat de facturering op dezelfde manier blijft.

- **Gratis laag** – Security Center installeert de oplossing ' SecurityCenterFree ' in de werk ruimte. Er worden geen kosten in rekening gebracht voor de gratis laag.
- **Standard-laag** – Security Center installeert de oplossing beveiliging in de werk ruimte.

   ![Oplossingen in de standaardwerk ruimte][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Ik heb al werk ruimten in mijn omgeving. kan ik deze gebruiken voor het verzamelen van beveiligings gegevens?
Als de micro soft Monitoring Agent al is geïnstalleerd als een Azure-extensie, Security Center gebruikt de bestaande verbonden werk ruimte. Er wordt een Security Center oplossing geïnstalleerd op de werk ruimte als deze nog niet aanwezig is, en de oplossing wordt alleen toegepast op de relevante Vm's via de [doel stellingen](../operations-management-suite/operations-management-suite-solution-targeting.md)van de oplossing.

Wanneer Security Center de micro soft Monitoring Agent op Vm's installeert, worden de standaardwerk ruimte (n) gebruikt die door Security Center is gemaakt.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Ik heb al een beveiligings oplossing voor mijn werk ruimten. Wat zijn de gevolgen voor facturering?
De oplossing beveiligings & controle wordt gebruikt om Security Center Standard-laag functies voor Azure-Vm's in te scha kelen. Als de oplossing beveiligings & controle al is geïnstalleerd op een werk ruimte, gebruikt Security Center de bestaande oplossing. Er is geen wijziging in de facturering.

## <a name="using-azure-security-center"></a>Azure Security Center gebruiken
### <a name="what-is-a-security-policy"></a>Wat is een beveiligings beleid?
Een beveiligings beleid bepaalt welke set besturings elementen wordt aanbevolen voor resources binnen het opgegeven abonnement. In Azure Security Center definieert u beleid voor uw Azure-abonnementen volgens de beveiligings vereisten van uw bedrijf en het type toepassingen of de gevoeligheid van de gegevens in elk abonnement.

Het beveiligings beleid dat is ingeschakeld in Azure Security Center aanbevelingen en controle van de beveiliging. Zie [beveiligings status controleren in azure Security Center](security-center-monitoring.md)voor meer informatie over beveiligings beleidsregels.

### <a name="who-can-modify-a-security-policy"></a>Wie kan een beveiligings beleid wijzigen?
Als u een beveiligings beleid wilt wijzigen, moet u een beveiligings beheerder of een eigenaar of bijdrager van het abonnement zijn.

Zie [beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md)voor meer informatie over het configureren van een beveiligings beleid.

### <a name="what-is-a-security-recommendation"></a>Wat is een beveiligings aanbeveling?
Azure Security Center analyseert de beveiligingsstatus van uw Azure-bronnen. Als er mogelijke beveiligings problemen worden geïdentificeerd, worden er aanbevelingen gemaakt. De aanbevelingen begeleiden u bij het configureren van het vereiste besturings element. Een aantal voorbeelden:

* Inrichting van anti-malware voor het identificeren en verwijderen van schadelijke software
* [Netwerk beveiligings groepen](../virtual-network/security-overview.md) en-regels voor het beheren van verkeer naar virtuele machines
* Het inrichten van een Web Application Firewall om te helpen bij het beschermen van aanvallen die gericht zijn op uw webtoepassingen
* Implementatie van ontbrekende systeemupdates
* Aanpassing van besturingssysteemconfiguraties die niet overeenkomen met de aanbevolen basislijnen

Hier worden alleen aanbevelingen weer gegeven die zijn ingeschakeld in het beveiligings beleid.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hoe kan ik de huidige beveiligings status van mijn Azure-resources weer geven?
Op de pagina **overzicht van Security Center** ziet u de algemene beveiligings postuur van uw omgeving, onderverdeeld op basis van compute-, netwerk-, opslag-& gegevens en toepassingen. Elk resource type heeft een indicator die aangeeft of er mogelijke beveiligings problemen zijn geïdentificeerd. Als u op elke tegel klikt, wordt een lijst met beveiligings problemen weer gegeven die zijn geïdentificeerd door Security Center, samen met een inventaris van de resources in uw abonnement.

### <a name="what-triggers-a-security-alert"></a>Wat wordt een beveiligings waarschuwing geactiveerd?
Azure Security Center verzamelt, analyseert en integreert automatisch logboek gegevens van uw Azure-resources, het netwerk en partner oplossingen zoals antimalware en firewalls. Wanneer er dreigingen worden gedetecteerd, wordt een beveiligingswaarschuwing gemaakt. Voorbeelden zijn detectie van:

* Geïnfecteerde virtuele machines die communiceren met bekende schadelijke IP-adressen
* Geavanceerde malware gedetecteerd met Windows fout rapportage
* Beveiligingsaanvallen op virtuele machines
* Beveiligings waarschuwingen van geïntegreerde partner beveiligings oplossingen, zoals anti-malware of Web Application firewalls

### Waarom zijn beveiligde Score waarden gewijzigd? <a name="secure-score-faq"></a>
Vanaf februari 2019 heeft Security Center de Score van enkele aanbevelingen aangepast, zodat deze beter past bij de ernst. Als gevolg van deze aanpassing kunnen de algemene waarden voor beveiligde scores worden gewijzigd.  Zie [Secure Score Calculation](security-center-secure-score.md)(Engelstalig) voor meer informatie over beveiligde scores.

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Wat is het verschil tussen bedreigingen die zijn gedetecteerd en gewaarschuwd op het micro soft Security Response Center versus Azure Security Center?
Het micro soft Security Response Center (MSRC) voert de selectie van beveiligings bewaking van het Azure-netwerk en de infra structuur uit en ontvangt bedreigings informatie en klachten van derden. Wanneer het MSRC van plan is dat klant gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij of het gebruik door de klant van Azure niet voldoet aan de voor waarden voor acceptabel gebruik, waarschuwt een beveiligings incident Manager de klant. Er treedt meestal een melding op door een e-mail te verzenden naar de beveiligings contactpersonen die zijn opgegeven in Azure Security Center of de eigenaar van het Azure-abonnement als er geen beveiligings contact is opgegeven.

Security Center is een Azure-service die voortdurend de Azure-omgeving van de klant bewaakt en analyses toepast om automatisch een breed scala aan mogelijk schadelijke activiteiten te detecteren. Deze detecties worden geoppereerd als beveiligings waarschuwingen in het dash board van Security Center.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Welke Azure-resources worden bewaakt door Azure Security Center?
Azure Security Center bewaakt de volgende Azure-resources:

* Virtuele machines (Vm's) (inclusief [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Virtuele-machineschaalsets
* Virtuele netwerken van Azure
* Azure SQL-service
* Azure-opslagaccount
* Azure-Web Apps (in [app service Environment](../app-service/environment/intro.md))
* Partner oplossingen die zijn geïntegreerd met uw Azure-abonnement, zoals een Web Application Firewall op Vm's en op App Service Environment

Daarnaast kunnen niet-Azure-computers (met inbegrip van on-premises) ook worden bewaakt door Azure Security Center (zowel [Windows-computers](./quick-onboard-windows-computer.md) als [Linux-computers](./quick-onboard-linux-computer.md) worden ondersteund)

## <a name="virtual-machines"></a>Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>Welke typen virtuele machines worden ondersteund?
Bewaking en aanbevelingen zijn beschikbaar voor virtuele machines (Vm's) die zijn gemaakt met behulp van de [klassieke en Resource Manager-implementatie modellen](../azure-classic-rm.md).

Zie de [ondersteunde platforms in azure Security Center](security-center-os-coverage.md) voor een lijst met ondersteunde platforms.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Waarom wordt de antimalware-oplossing die op mijn Azure-VM wordt uitgevoerd niet Azure Security Center herkend?
Azure Security Center heeft zicht baarheid van antimalware geïnstalleerd via Azure-extensies. Security Center is bijvoorbeeld niet in staat om antimalware te detecteren die vooraf is geïnstalleerd op een installatie kopie die u hebt ingevoerd of als u antimalware op uw virtuele machines hebt geïnstalleerd met behulp van uw eigen processen (zoals configuratie beheersystemen).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Waarom ontvang ik het bericht ' ontbrekende scan gegevens ' voor mijn VM?
Dit bericht wordt weergegeven wanneer er geen scangegevens zijn voor een VM. Het kan even duren (minder dan een uur) voordat scangegevens beschikbaar komen nadat de functie Gegevensverzameling is ingeschakeld in Azure Security Center. Nadat de eerste scangegevens zijn doorgegeven, kunt u dit bericht ontvangen omdat er helemaal geen scangegevens zijn of geen recente scangegevens. Er worden geen scangegevens aangeboden aan een VM die is gestopt. Dit bericht kan ook worden weer gegeven als de scan gegevens niet recent zijn ingevuld (in overeenstemming met het Bewaar beleid voor de Windows-agent, die een standaard waarde heeft van 30 dagen).

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Hoe vaak Security Center scant op beveiligings problemen met het besturings systeem, systeem updates en Endpoint Protection?
Hieronder vindt u de latentie tijden voor Security Center scans van beveiligings problemen, updates en problemen:

- Beveiligings configuraties van besturings systeem: gegevens worden binnen 48 uur bijgewerkt
- Systeem updates: gegevens worden binnen 24 uur bijgewerkt
- Endpoint Protection problemen: gegevens worden binnen 8 uur bijgewerkt

Security Center scant doorgaans elk uur op nieuwe gegevens en vernieuwt de aanbevelingen dienovereenkomstig. 

> [!NOTE]
> Security Center gebruikt de micro soft Monitoring Agent voor het verzamelen en opslaan van gegevens. Zie [Azure Security Center platform Migration](security-center-platform-migration.md)(Engelstalig) voor meer informatie.
>
>

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Waarom ontvang ik het bericht ' de VM-agent ontbreekt? '
De VM-agent moet op Vm's zijn geïnstalleerd om gegevens verzameling in te scha kelen. De VM-agent wordt standaard geïnstalleerd op VM's die zijn geïmplementeerd vanuit Azure Marketplace. Zie voor meer informatie over het installeren van de VM-agent op andere Vm's de blog post [VM-agent en-extensies](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
