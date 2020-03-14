---
title: 'Azure Security Center Veelgestelde vragen: gegevens verzameling en agents'
description: Veelgestelde vragen over het verzamelen van gegevens, agents en werk ruimten voor Azure Security Center, een product waarmee u bedreigingen kunt voor komen, detecteren en reageren
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
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 8317a13b9ef87679836f55627268deefa4500dce
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245471"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Veelgestelde vragen: vragen over het verzamelen van gegevens, agents en werk ruimten

Security Center verzamelt gegevens van uw virtuele machines van Azure (Vm's), schaal sets voor virtuele machines, IaaS containers en niet-Azure-computers (inclusief lokale computers) om te controleren op beveiligings problemen en bedreigingen. De gegevens worden verzameld met behulp van de MMA, die verschillende configuraties en gebeurtenislogboeken met betrekking tot beveiliging van de machine leest en de gegevens kopieert naar uw werkruimte voor analyse.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Ben ik gefactureerd voor Azure Monitor-logboeken op de werk ruimten die door Security Center zijn gemaakt?

Nee. Werk ruimten die zijn gemaakt door Security Center en die zijn geconfigureerd voor Azure Monitor-logboeken per knoop punt, worden niet in rekening gebracht Azure Monitor logboek kosten. Security Center-facturering is altijd gebaseerd op het Security Center-beveiligingsbeleid en de oplossingen die zijn geïnstalleerd op een werkruimte:

- **Gratis laag** – Security Center maakt de oplossing ' SecurityCenterFree ' in de standaardwerk ruimte. Er worden geen kosten in rekening gebracht voor de gratis laag.

- **Standard-laag** – Security Center maakt de oplossing beveiliging in de standaardwerk ruimte.

Zie [Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie over prijzen.

> [!NOTE]
> De prijs categorie voor log Analytics van werk ruimten die zijn gemaakt door Security Center heeft geen invloed op Security Center facturering.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Wat is een virtuele machine de definitie voor automatische inrichting van de Microsoft Monitoring Agent-installatie?

Windows- of Linux IaaS-VM's in aanmerking komt als:

- De Microsoft Monitoring Agent-extensie is momenteel niet geïnstalleerd op de virtuele machine.
- De virtuele machine wordt uitgevoerd.
- De Windows-of Linux [Azure virtual machine-agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) is geïnstalleerd.
- De virtuele machine wordt niet gebruikt als een apparaat, zoals de web application firewall of de firewall van volgende generatie.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Kan ik de Standaardwerkruimten die zijn gemaakt door Security Center wilt verwijderen?

**Het is niet raadzaam de standaardwerk ruimte te verwijderen.** Security Center maakt gebruik van de Standaardwerkruimten voor het opslaan van beveiligingsgegevens van uw virtuele machines. Als u een werkruimte verwijdert, kan Security Center voor het verzamelen van deze gegevens en enkele aanbevelingen voor beveiliging en waarschuwingen zijn niet beschikbaar.

Als u wilt herstellen, verwijdert u de Microsoft Monitoring Agent op de virtuele machines die zijn verbonden met de verwijderde werkruimte. Security Center de agent opnieuw installeren en nieuwe Standaardwerkruimten maken.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hoe kan ik mijn bestaande Log Analytics-werkruimte gebruiken?

U kunt een bestaande werkruimte voor logboekanalyse voor het opslaan van gegevens die zijn verzameld door Security Center kunt selecteren. Gebruik uw bestaande Log Analytics-werkruimte:

- De werkruimte moet worden gekoppeld aan uw geselecteerde Azure-abonnement.
- Ten minste, moet u machtigingen voor toegang tot de werkruimte hebt gelezen.

Selecteer een bestaande Log Analytics-werkruimte:

1. Onder **beveiligings beleid – gegevens verzamelen**selecteert **u een andere werk ruimte gebruiken**.

    ![Een andere werkruimte gebruiken][4]

1. Selecteer een werkruimte voor het opslaan van verzamelde gegevens in de vervolgkeuzelijst.

    > [!NOTE]
    > In de vervolgkeuzelijst menu, worden alleen werkruimten die u hebt toegang tot en zijn in uw Azure-abonnement weergegeven.

1. Selecteer **Opslaan**. U wordt gevraagd of u de bewaakte Vm's opnieuw wilt configureren.

    - Selecteer **Nee** als u de nieuwe werkruimte instellingen **alleen op nieuwe vm's wilt Toep assen**. De nieuwe werkruimte-instellingen zijn alleen van toepassing op nieuwe agentinstallaties; nieuwe gedetecteerde virtuele machines die geen van de Microsoft Monitoring Agent is geïnstalleerd.
    - Selecteer **Ja** als u de nieuwe werkruimte instellingen wilt **Toep assen op alle vm's**. Bovendien wordt elke virtuele machine verbonden met een Security Center-werkruimte gemaakt verbonden aan de nieuwe doelwerkruimte.

    > [!NOTE]
    > Als u **Ja**selecteert, verwijdert u geen werk ruimten die zijn gemaakt door Security Center totdat alle virtuele machines opnieuw zijn verbonden met de nieuwe doel werkruimte. Met deze bewerking mislukt als een werkruimte te vroeg is verwijderd.

    - Als u de bewerking wilt annuleren, selecteert u **Annuleren**.

## Wat gebeurt er als micro soft Monitoring Agent al is geïnstalleerd als een uitbrei ding op de VM?<a name="mmaextensioninstalled"></a>

Wanneer de bewakings agent is geïnstalleerd als een uitbrei ding, staat de extensie configuratie slechts aan één werk ruimte toe. Security Center wordt de bestaande verbindingen met werkruimten van de gebruiker niet overschreven. Security Center worden beveiligings gegevens van een virtuele machine opgeslagen in een werk ruimte die al is verbonden, op voor waarde dat de oplossing Security of SecurityCenterFree is geïnstalleerd. Security Center kunt de extensie versie in dit proces upgraden naar de meest recente versie.

Zie [automatische inrichting in het geval van een bestaande Agent installatie](security-center-enable-data-collection.md#preexisting)voor meer informatie.



## Wat gebeurt er als een micro soft Monitoring Agent rechtstreeks op de computer is geïnstalleerd, maar niet als een uitbrei ding (direct agent)?<a name="directagentinstalled"></a>

Als de micro soft Monitoring Agent rechtstreeks op de virtuele machine is geïnstalleerd (niet als een Azure-extensie), wordt de micro soft Monitoring Agent-extensie door Security Center geïnstalleerd en kan micro soft Monitoring Agent worden bijgewerkt naar de nieuwste versie.

De agent die is geïnstalleerd, blijft rapporteren aan de al geconfigureerde werk ruimte (n) en rapporteert bovendien aan de werk ruimte die is geconfigureerd in Security Center (multi-multihoming wordt ondersteund op Windows-computers).

Als de geconfigureerde werk ruimte een gebruikers werkruimte is (niet Security Center de standaardwerk ruimte), moet u de oplossing ' Security/' SecurityCenterFree ' installeren voor Security Center om te beginnen met het verwerken van gebeurtenissen van Vm's en computers die aan die werk ruimte rapporteren.

Voor Linux-machines wordt agent-multi-multihoming nog niet ondersteund. als er een bestaande agent wordt gedetecteerd, treedt er geen automatische inrichting op en wordt de configuratie van de computer niet gewijzigd.

Voor bestaande machines op abonnementen die vóór 17 2019 maart op Security Center worden uitgevoerd, wordt de micro soft Monitoring Agent-extensie niet geïnstalleerd en wordt de computer niet beïnvloed als er een bestaande agent wordt gedetecteerd. Voor deze computers raadpleegt u de aanbeveling bewakings agent status problemen op uw computers oplossen om de installatie problemen van de agent op deze computers op te lossen

Voor meer informatie raadpleegt u de volgende sectie [wat er gebeurt als er al een System Center Operations Manager of OMS direct-agent is geïnstalleerd op mijn VM?](#scomomsinstalled)

## Wat gebeurt er als er al een System Center Operations Manager-agent is geïnstalleerd op mijn VM?<a name="scomomsinstalled"></a>

In Security Center wordt de micro soft Monitoring Agent-extensie naast elkaar geïnstalleerd met de bestaande System Center Operations Manager-agent. De bestaande agent blijft normaal aan de System Center Operations Manager-server rapporteren. Houd er rekening mee dat de Operations Manager agent en micro soft Monitoring Agent gemeen schappelijke runtime bibliotheken delen, die tijdens dit proces worden bijgewerkt naar de meest recente versie. Opmerking: als versie 2012 van de Operations Manager-agent is geïnstalleerd, moet u niet automatisch inrichten inschakelen (beheer mogelijkheden kunnen verloren gaan als de Operations Manager server ook versie 2012 is).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Wat zijn de gevolgen van het verwijderen van deze extensies?

Als u de extensie voor Microsoft Monitoring verwijdert, Security Center kan geen beveiligingsgegevens verzamelen van de virtuele machine en enkele aanbevelingen voor beveiliging en waarschuwingen zijn niet beschikbaar. Security Center bepaalt binnen 24 uur dat de virtuele machine in de uitbreiding ontbreekt en de extensie opnieuw geïnstalleerd.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hoe voorkom ik automatische agent-installatie en werkruimte maken

U kunt uitschakelen automatische inrichting van uw abonnementen in het beveiligingsbeleid, maar dit wordt niet aanbevolen. Het uitschakelen van automatische inrichting limieten Security Center aanbevelingen en waarschuwingen. Uitschakelen van automatische inrichting:

1. Als uw abonnement is geconfigureerd voor de Standard-laag, opent u het beveiligings beleid voor dat abonnement en selecteert u de laag **gratis** .

   ![Prijscategorie][1]

1. Schakel vervolgens automatische inrichting uit door **uit** te scha kelen op de pagina **beveiligings beleid – gegevens verzameling** .
   ![Gegevensverzameling][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Moet ik opt-out voor de automatische installatie van agent en werkruimte maken?

> [!NOTE]
> Zorg ervoor dat u secties bekijkt [Wat zijn de gevolgen van het afmelden?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) en de [Aanbevolen stappen bij](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) het afmelden als u ervoor kiest om u af te melden voor automatische inrichting.

U wilt afmelden voor automatische inrichting als voor u het volgende geldt:

- Automatische installatie van agent door Security Center is van toepassing op het hele abonnement. U kunt geen automatische installatie toepassen op een subset van virtuele machines. Als er kritieke VM's die met de Microsoft Monitoring Agent kunnen niet worden geïnstalleerd, moet u ervoor kiezen automatische inrichting van buiten.
- Door de installatie van de micro soft Monitoring Agent (MMA)-extensie wordt de versie van de agent bijgewerkt. Dit geldt voor een directe agent en een System Center Operations Manager-agent (in de laatste, de Operations Manager en MMA delen common runtime-bibliotheken die in het proces worden bijgewerkt). Als de geïnstalleerde Operations Manager-agent versie 2012 is en is bijgewerkt, kunnen de beheer baarheids mogelijkheden verloren gaan wanneer de Operations Manager-server ook versie 2012 is. Houd er rekening mee dat u het automatisch inrichten kunt uitschakelen als de geïnstalleerde Operations Manager-agent versie 2012 is.
- Als u een aangepaste werk ruimte hebt die extern is voor het abonnement (een gecentraliseerde werk ruimte), moet u zich afmelden voor automatische inrichting. U kunt handmatig installeren van de Microsoft Monitoring Agent-extensie en verbindt dit uw werkruimte zonder Security Center voor het overschrijven van de verbinding.
- Als u wilt voorkomen dat het maken van meerdere werkruimten per abonnement en u uw eigen aangepaste werkruimte binnen het abonnement hebt, hebt u twee opties:

   1. U kunt afmelden voor automatische inrichting. Stel na de migratie de standaard instellingen voor de werk ruimte in, zoals wordt beschreven in [Hoe kan ik mijn bestaande log Analytics werkruimte gebruiken?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. Of u kunt de migratie is voltooid, de Microsoft Monitoring Agent worden geïnstalleerd op de virtuele machines, en de virtuele machines die zijn verbonden met de gemaakte werkruimte. Selecteer vervolgens uw eigen aangepaste werkruimte door in te stellen de standaardinstelling van de werkruimte met inschrijving voor het opnieuw configureren van de reeds geïnstalleerde agents. Zie [Hoe kan ik mijn bestaande log Analytics werkruimte gebruiken?](#how-can-i-use-my-existing-log-analytics-workspace) voor meer informatie.


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Wat zijn de gevolgen van het geen automatische inrichting?

Wanneer de migratie is voltooid, kan Security Center geen beveiligings gegevens van de virtuele machine verzamelen en sommige beveiligings aanbevelingen en-waarschuwingen zijn niet beschikbaar. Als u zich afmeldt, installeert u micro soft Monitoring Agent hand matig. Bekijk de [Aanbevolen stappen bij het afmelden](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Wat zijn de aanbevolen stappen wanneer u geen automatische inrichting?

Installeer de micro soft Monitoring Agent-extensie hand matig zodat Security Center beveiligings gegevens van uw virtuele machines kunt verzamelen en aanbevelingen en waarschuwingen moet geven. Zie [installatie van de agent voor](../virtual-machines/extensions/oms-windows.md) de installatie van de Windows-VM of de [agent voor Linux VM](../virtual-machines/extensions/oms-linux.md) voor meer informatie over de installatie.

U kunt de agent koppelen aan een bestaande aangepaste werkruimte of Security Center-werkruimte gemaakt. Als u een aangepaste werkruimte heeft geen van de 'Beveiliging' of 'SecurityCenterFree' oplossingen die zijn ingeschakeld, moet u een oplossing van toepassing. Als u wilt Toep assen, selecteert u de aangepaste werk ruimte of het abonnement en past u een prijs categorie toe via de pagina **beveiligings beleid – prijs categorie** .

   ![Prijscategorie][1]

Security Center kunnen de juiste oplossing is in de werkruimte op basis van de geselecteerde prijscategorie.


## Hoe kan ik OMS-extensies die door Security Center zijn geïnstalleerd, verwijderen?<a name="remove-oms"></a>

U kunt de Microsoft Monitoring Agent handmatig verwijderen. Dit wordt niet aanbevolen omdat deze wordt beperkt door Security Center aanbevelingen en waarschuwingen.

> [!NOTE]
> Als gegevensverzameling is ingeschakeld, kan Security Center de agent wordt opnieuw nadat u deze verwijderen.  U moet verzamelen van gegevens voordat u de agent handmatig verwijdert uitschakelen. Zie Hoe kan ik de automatische agent installatie en het maken van de werk ruimte stoppen? voor instructies voor het uitschakelen van gegevens verzameling.

De agent handmatig te verwijderen:

1.  Open **log Analytics**in de portal.

1.  Selecteer op de pagina Log Analytics een werk ruimte:

1.  Selecteer de virtuele machines die u niet wilt bewaken en selecteer **verbinding verbreken**.

   ![De agent verwijderen][3]

> [!NOTE]
> Als een virtuele Linux-machine al een OMS-agent heeft die niet uitbrei ding is en u de uitbrei ding verwijdert, wordt ook de agent verwijderd en moet u deze opnieuw installeren.


## <a name="how-do-i-disable-data-collection"></a>Hoe kan ik het verzamelen van gegevens uitschakelen?

Automatische inrichting is standaard uitgeschakeld. Automatische inrichting van resources op elk gewenst moment door het uitschakelen van deze instelling in het beveiligingsbeleid, kunt u uitschakelen. Automatisch inrichten wordt sterk aanbevolen om beveiligings waarschuwingen en aanbevelingen te ontvangen over systeem updates, OS-beveiligings problemen en Endpoint Protection.

Als u gegevens verzameling wilt uitschakelen, [meldt u zich aan bij de Azure Portal](https://portal.azure.com), selecteert u **Bladeren**, selecteert u **Security Center**en selecteert u **vervolgens beleid selecteren**. Selecteer het abonnement waarvoor u automatisch inrichten wilt uitschakelen. Wanneer u een beveiligings beleid voor abonnementen selecteert **: gegevens verzamelen** wordt geopend. Onder **automatische inrichting**selecteert u **uit**.


## <a name="how-do-i-enable-data-collection"></a>Hoe kan ik het verzamelen van gegevens inschakelen?

U kunt het verzamelen van gegevens inschakelen voor uw Azure-abonnement in het beveiligingsbeleid. Om gegevens te verzamelen. [Meld u aan bij de Azure Portal](https://portal.azure.com), selecteer **bladeren**, selecteer **Security Center**en selecteer **beveiligings beleid**. Selecteer het abonnement dat u wilt inschakelen automatische inrichting. Wanneer u een beveiligings beleid voor abonnementen selecteert **: gegevens verzamelen** wordt geopend. Onder **automatische inrichting**selecteert u **aan**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Wat gebeurt er wanneer gegevensverzameling is ingeschakeld?

Als automatisch inrichten is ingeschakeld, ondersteund levert Security Center de Microsoft Monitoring Agent op alle Azure-VM's en een nieuwe VM's die worden gemaakt. Automatische inrichting wordt aanbevolen, maar er is ook hand matige installatie van de agent beschikbaar. [Informatie over het installeren van de Microsoft Monitoring Agent-extensie](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

De agent maakt de gebeurtenis 4688 voor het maken van processen en het veld *commandline* in gebeurtenis 4688 mogelijk. Nieuwe processen die zijn gemaakt op de virtuele machine zijn vastgelegd door EventLog en bewaakt door Security Center de detectie van services. Zie voor meer informatie over de gegevens die voor elk nieuwe proces zijn vastgelegd de [velden Beschrijving in 4688](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields). De agent verzamelt de 4688 gebeurtenissen die zijn gemaakt op de virtuele machine ook en slaat ze op in het zoekvak.

De agent maakt ook het verzamelen van gegevens mogelijk voor [adaptieve toepassings besturings elementen](security-center-adaptive-application.md), Security Center configureert een lokaal AppLocker-beleid in de controle modus om alle toepassingen toe te staan. Dit beleid zorgt ervoor dat AppLocker gebeurtenissen genereert die vervolgens worden verzameld en gebruikt door Security Center. Het is belangrijk te weten dat dit beleid niet worden geconfigureerd op alle computers waarop er al een geconfigureerde AppLocker-beleid is. 

Als Security Center verdachte activiteiten op de virtuele machine detecteert, wordt de klant per e-mail op de hoogte gesteld als er [contact gegevens](security-center-provide-security-contact-details.md) voor de beveiliging zijn verstrekt. Een waarschuwing wordt ook weergegeven in het dashboard van Security Center security-waarschuwingen.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Werkt Security Center met behulp van een OMS-gateway?

Ja. Azure Security Center maakt gebruik van Azure Monitor voor het verzamelen van gegevens van Azure-Vm's en-servers, met behulp van micro soft monitoring agent.
Voor het verzamelen van de gegevens moet elke virtuele machine en server verbinding maken met internet met behulp van HTTPS. De verbinding kan direct worden gebruikt, met behulp van een proxy of via de [OMS-gateway](../azure-monitor/platform/gateway.md).


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>De Monitoring Agent invloed op de prestaties van mijn servers?

De agent een nominaal bedrag van systeembronnen verbruikt en moet weinig invloed hebben op de prestaties. Zie de [plannings-en bedienings handleiding](security-center-planning-and-operations-guide.md#data-collection-and-storage)voor meer informatie over de invloed op de prestaties en de agent en de uitbrei ding.


## <a name="where-is-my-data-stored"></a>Waar worden mijn gegevens opgeslagen?

Gegevens die worden verzameld van deze agent worden opgeslagen in een bestaande Log Analytics-werkruimte die is gekoppeld aan uw abonnement of een nieuwe werkruimte. Zie [Data Security](security-center-data-security.md)(Engelstalig) voor meer informatie.


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png