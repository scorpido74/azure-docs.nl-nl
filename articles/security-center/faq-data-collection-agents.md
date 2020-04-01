---
title: Veelgestelde vragen over Azure Security Center - gegevensverzameling en -agents
description: Veelgestelde vragen over het verzamelen van gegevens, agents en werkruimten voor Azure Security Center, een product waarmee u bedreigingen voorkomen, detecteren en erop reageren
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
ms.openlocfilehash: 0dbad1a94479430426dae47df7ca3a3ecd9dc980
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436201"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>Veelgestelde vragen - Vragen over het verzamelen van gegevens, agents en werkruimten

Security Center verzamelt gegevens van uw Virtuele Azure-machines (VM's), virtuele machineschaalsets, IaaS-containers en niet-Azure-computers (inclusief on-premises machines) om te controleren op beveiligingsproblemen en bedreigingen. Gegevens worden verzameld met behulp van de Log Analytics-agent, die verschillende beveiligingsconfiguraties en gebeurtenislogboeken van de machine leest en de gegevens kopieert naar uw werkruimte voor analyse.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Worden er kosten in rekening gebracht voor Azure Monitor-logboeken over de werkruimten die zijn gemaakt door Security Center?

Nee. Werkruimten die zijn gemaakt door Beveiligingscentrum, terwijl deze zijn geconfigureerd voor Azure Monitor-logboeken per knooppunt facturering, hoeven geen azure monitor-logboeken in rekening te brengen. Facturering in beveiligingscentrum is altijd gebaseerd op uw beveiligingsbeleid voor beveiligingspersoneel en de oplossingen die op een werkruimte zijn geïnstalleerd:

- **Gratis laag** – Security Center maakt de 'SecurityCenterFree'-oplossing op de standaardwerkruimte mogelijk. U wordt niet gefactureerd voor de gratis laag.

- **Standaardlaag** – Security Center maakt de 'Beveiligingsoplossing' op de standaardwerkruimte mogelijk.

Zie Prijsvragen van [het Beveiligingscentrum](https://azure.microsoft.com/pricing/details/security-center/)voor meer informatie over prijzen.

> [!NOTE]
> De prijscategorie loganalyses van werkruimten die door Security Center zijn gemaakt, heeft geen invloed op de facturering van het Beveiligingscentrum.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Wat kwalificeert een VM voor automatische inrichting van de installatie van de Log Analytics-agent?

Windows- of Linux IaaS VM's komen in aanmerking als:

- De extensie van de agent Log Analytics is momenteel niet geïnstalleerd op de VM.
- De VM is in de lopende staat.
- De Windows- of Linux [Azure Virtual Machine Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) is geïnstalleerd.
- De VM wordt niet gebruikt als toestel, zoals webapplicatiefirewall of firewall van de volgende generatie.


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Kan ik de standaardwerkruimten verwijderen die zijn gemaakt door Security Center?

**Het verwijderen van de standaardwerkruimte wordt afgeraden.** Security Center gebruikt de standaardwerkruimten om beveiligingsgegevens van uw VM's op te slaan. Als u een werkruimte verwijdert, kan Security Center deze gegevens niet verzamelen en zijn sommige beveiligingsaanbevelingen en waarschuwingen niet beschikbaar.

Als u wilt herstellen, verwijdert u de agent Log Analytics op de VM's die zijn verbonden met de verwijderde werkruimte. Security Center installeert de agent opnieuw en maakt nieuwe standaardwerkruimten.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Hoe kan ik mijn bestaande Log Analytics-werkruimte gebruiken?

U een bestaande Log Analytics-werkruimte selecteren om gegevens op te slaan die door Het Beveiligingscentrum zijn verzameld. Ga als u uw bestaande Log Analytics-werkruimte wilt gebruiken:

- De werkruimte moet zijn gekoppeld aan uw geselecteerde Azure-abonnement.
- U moet minimaal leesmachtigingen hebben om toegang te krijgen tot de werkruimte.

Ga als u een bestaande loganalytics-werkruimte selecteert:

1. Selecteer **Onder Beveiligingsbeleid – Gegevensverzameling**de optie **Een andere werkruimte gebruiken**.

    ![Een andere werkruimte gebruiken][4]

1. Selecteer in het keuzemenu een werkruimte om verzamelde gegevens op te slaan.

    > [!NOTE]
    > In het menu Omlaag trekken worden alleen werkruimten weergegeven waartoe u toegang hebt en die zich in uw Azure-abonnement bevinden.

1. Selecteer **Opslaan**. U wordt gevraagd of u bewaakte VM's opnieuw wilt configureren.

    - Selecteer **Nee** als u alleen de nieuwe werkruimte-instellingen wilt **toepassen op nieuwe VM's.** De nieuwe werkruimte-instellingen zijn alleen van toepassing op nieuwe agentinstallaties; nieuw ontdekte VM's die de Log Analytics-agent niet hebben geïnstalleerd.
    - Selecteer **Ja** als u de nieuwe werkruimte-instellingen wilt **toepassen op alle VM's.** Bovendien wordt elke VM die is aangesloten op een door beveiligingscentrum gemaakte werkruimte opnieuw verbonden met de nieuwe doelwerkruimte.

    > [!NOTE]
    > Als u **Ja**selecteert, verwijdert u geen werkruimten die door Security Center zijn gemaakt totdat alle VM's opnieuw zijn verbonden met de nieuwe doelwerkruimte. Deze bewerking mislukt als een werkruimte te vroeg wordt verwijderd.

    - Als u de bewerking wilt annuleren, selecteert u **Annuleren**.

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Wat gebeurt er als de Log Analytics-agent al is geïnstalleerd als extensie op de VM?<a name="mmaextensioninstalled"></a>

Wanneer de bewakingsagent als extensie is geïnstalleerd, kan met de extensieconfiguratie slechts één werkruimte worden gemeld. Security Center overschrijft bestaande verbindingen met gebruikerswerkruimten niet. Security Center slaat beveiligingsgegevens van een VM op in een werkruimte die al is verbonden, op voorwaarde dat de oplossing 'Beveiliging' of 'SecurityCenterFree' is geïnstalleerd. Security Center kan de extensieversie in dit proces upgraden naar de nieuwste versie.

Zie [Automatische inrichting in geval van een reeds bestaande agentinstallatie](security-center-enable-data-collection.md#preexisting)voor meer informatie.



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Wat gebeurt er als een Log Analytics-agent rechtstreeks op de machine is geïnstalleerd, maar niet als extensie (Direct Agent)?<a name="directagentinstalled"></a>

Als de Log Analytics-agent rechtstreeks op de VM is geïnstalleerd (niet als Azure-extensie), installeert Beveiligingscentrum de extensie Log Analytics-agent en kan de log-analyse-agent worden geüupgradet naar de nieuwste versie.

De geïnstalleerde agent blijft rapporteren aan de reeds geconfigureerde werkruimte(s) en rapporteert bovendien aan de werkruimte die is geconfigureerd in Security Center (Multi-homing wordt ondersteund op Windows-machines).

Als de geconfigureerde werkruimte een gebruikerswerkruimte is (niet de standaardwerkruimte van het Beveiligingscentrum), moet u de oplossing 'Beveiliging/'SecurityCenterFree' erop installeren zodat Beveiligingscentrum om gebeurtenissen van VM's en computers die naar die werkruimte rapporteren, te kunnen verwerken.

Voor Linux-machines wordt Agent multi-homing nog niet ondersteund - dus als een bestaande agentinstallatie wordt gedetecteerd, zal automatische provisioning niet plaatsvinden en wordt de configuratie van de machine niet gewijzigd.

Voor bestaande machines op abonnementen die vóór 17 maart 2019 zijn aangesloten bij Security Center, wanneer een bestaande agent wordt gedetecteerd, wordt de extensie loganalytics-agent niet geïnstalleerd en wordt de machine niet beïnvloed. Zie voor deze machines de aanbeveling 'Gezondheidsproblemen met bewakingsagent's op uw machines oplossen om de problemen met de installatie van de agent op deze machines op te lossen

Zie de volgende sectie [Wat gebeurt er als een System Center Operations Manager of OMS-direct agent al op mijn VM is geïnstalleerd?](#scomomsinstalled)

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>Wat gebeurt er als een System Center Operations Manager-agent al op mijn VM is geïnstalleerd?<a name="scomomsinstalled"></a>

Beveiligingscentrum installeert de extensie log-analyse-agent naast de bestaande System Center Operations Manager-agent. De bestaande agent blijft normaal rapporteren aan de System Center Operations Manager-server. Houd er rekening mee dat de operations manager-agent en de log-analyse-agent algemene runtime-bibliotheken delen, die tijdens dit proces worden bijgewerkt naar de nieuwste versie. Opmerking - Als versie 2012 van de Operations Manager-agent is geïnstalleerd, schakelt u automatische inrichting niet in (beheerbaarheidsmogelijkheden kunnen verloren gaan wanneer de Operations Manager-server ook versie 2012 is).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>Wat is de impact van het verwijderen van deze extensies?

Als u de Microsoft Monitoring Extension verwijdert, kan Security Center geen beveiligingsgegevens van de VM verzamelen en zijn sommige beveiligingsaanbevelingen en waarschuwingen niet beschikbaar. Binnen 24 uur bepaalt Security Center dat de VM de extensie mist en installeert de extensie opnieuw.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Hoe stop ik de installatie van automatische agent en het maken van werkruimtes?

U automatische inrichting voor uw abonnementen uitschakelen in het beveiligingsbeleid, maar dit wordt niet aanbevolen. Het uitschakelen van automatische inrichting beperkt aanbevelingen en waarschuwingen van het Beveiligingscentrum. Automatische inrichting uitschakelen:

1. Als uw abonnement is geconfigureerd voor de standaardlaag, opent u het beveiligingsbeleid voor dat abonnement en selecteert u de laag **Gratis.**

   ![Prijscategorie][1]

1. Schakel vervolgens automatische inrichting uit door **Uit te** kiezen op de pagina **Beveiligingsbeleid – Gegevensverzameling.**
   ![Gegevensverzameling][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Moet ik me afmelden voor de installatie en het maken van werkruimtes voor automatische agent?

> [!NOTE]
> Zorg ervoor dat u secties bekijkt [Wat zijn de gevolgen van afmelden?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) en aanbevolen stappen bij het [afmelden](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) als u ervoor kiest om u af te melden voor automatische provisioning?

U zich afmelden voor automatische inrichting als het volgende op u van toepassing is:

- Automatische agentinstallatie door Security Center is van toepassing op het volledige abonnement. U geen automatische installatie toepassen op een subset van VM's. Als er kritieke VM's zijn die niet kunnen worden geïnstalleerd bij de Log Analytics-agent, moet u zich afmelden voor automatische provisioning.
- De installatie van de extensie van de Log Analytics-agent werkt de versie van de agent bij. Dit geldt voor een direct agent en een System Center Operations Manager-agent (in de laatste delen de Operations Manager- en Log Analytics-agent gemeenschappelijke runtime-bibliotheken - die in het proces worden bijgewerkt). Als de geïnstalleerde Operations Manager-agent versie 2012 is en is bijgewerkt, kunnen beheerbaarheidsmogelijkheden verloren gaan wanneer de Operations Manager-server ook versie 2012 is. Overweeg u af te melden voor automatische inrichting als de geïnstalleerde Operations Manager-agent versie 2012 is.
- Als u een aangepaste werkruimte buiten het abonnement hebt (een gecentraliseerde werkruimte), moet u zich afmelden voor automatische inlevering. U de extensie log-analyseagent handmatig installeren en deze uw werkruimte aansluiten zonder dat Security Center de verbinding overneemt.
- Als u wilt voorkomen dat er meerdere werkruimten per abonnement worden gemaakt en u uw eigen aangepaste werkruimte binnen het abonnement hebt, hebt u twee opties:

   1. U zich afmelden voor automatische inrichting. Na migratie stelt u de standaardwerkruimte-instellingen in zoals beschreven in [Hoe kan ik mijn bestaande Log Analytics-werkruimte gebruiken?](#how-can-i-use-my-existing-log-analytics-workspace)

   1. U ook toestaan dat de migratie is voltooid, de log-analyse-agent wordt geïnstalleerd op de VM's en de VM's die zijn verbonden met de gemaakte werkruimte. Selecteer vervolgens uw eigen aangepaste werkruimte door de standaardwerkruimte-instelling in te stellen met de optie om de reeds geïnstalleerde agents opnieuw te configureren. Zie [Hoe kan ik mijn bestaande Log Analytics-werkruimte gebruiken voor](#how-can-i-use-my-existing-log-analytics-workspace) meer informatie?


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Wat zijn de gevolgen van de afzaging van automatische voorzieningen?

Wanneer de migratie is voltooid, kan Security Center geen beveiligingsgegevens van de VM verzamelen en zijn sommige beveiligingsaanbevelingen en waarschuwingen niet beschikbaar. Als u zich afmeldt, installeert u de log-analyse-agent handmatig. Zie [aanbevolen stappen bij het afmelden](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Wat zijn de aanbevolen stappen bij het afmelden van automatische provisioning?

Installeer de extensie log-analyseagent handmatig, zodat Security Center beveiligingsgegevens van uw VM's kan verzamelen en aanbevelingen en waarschuwingen kan geven. Zie [agentinstallatie voor Windows VM](../virtual-machines/extensions/oms-windows.md) of [agentinstallatie voor Linux VM](../virtual-machines/extensions/oms-linux.md) voor richtlijnen voor installatie.

U de agent verbinden met een bestaande aangepaste werkruimte of een door beveiligingscentrum gemaakte werkruimte. Als een aangepaste werkruimte de 'Security'- of 'SecurityCenterFree'-oplossingen niet heeft ingeschakeld, moet u een oplossing toepassen. Als u wilt toepassen, selecteert u de aangepaste werkruimte of het aangepaste abonnement en past u een prijscategorie toe via de pagina **Beveiligingsbeleid : Prijslaag.**

   ![Prijscategorie][1]

Security Center schakelt de juiste oplossing op de werkruimte in op basis van de geselecteerde prijscategorie.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Hoe verwijder ik OMS-extensies die zijn geïnstalleerd door Security Center?<a name="remove-oms"></a>

U de log-analyse-agent handmatig verwijderen. Dit wordt niet aanbevolen omdat het aanbevelingen en waarschuwingen van het Beveiligingscentrum beperkt.

> [!NOTE]
> Als het verzamelen van gegevens is ingeschakeld, installeert Security Center de agent opnieuw nadat u de agent hebt verwijderd.  U moet het verzamelen van gegevens uitschakelen voordat u de agent handmatig verwijdert. Zie Hoe stop ik de installatie van automatische agent en het maken van werkruimtes? voor instructies voor het uitschakelen van het verzamelen van gegevens.

Ga als u de agent handmatig verwijderen:

1.    Open **Log Analytics**in de portal .

1.    Selecteer op de pagina Log Analytics een werkruimte:

1.    Selecteer de VM's die u niet wilt controleren en selecteer **Verbreken**.

   ![De agent verwijderen][3]

> [!NOTE]
> Als een Linux VM al een niet-extensie OMS-agent heeft, verwijdert het verwijderen van de extensie de agent ook en moet u deze opnieuw installeren.


## <a name="how-do-i-disable-data-collection"></a>Hoe schakel ik het verzamelen van gegevens uit?

Automatische inrichting is standaard uitgeschakeld. U automatische inrichting uit resources op elk gewenst moment uitschakelen door deze instelling uit te schakelen in het beveiligingsbeleid. Automatische inrichting wordt ten zeerste aanbevolen om beveiligingswaarschuwingen en aanbevelingen te ontvangen over systeemupdates, beveiligingslekken en endpoint-beveiliging.

Als u gegevensverzameling wilt uitschakelen, [meldt u zich aan bij de Azure-portal,](https://portal.azure.com)selecteert u **Bladeren,** selecteer **Beveiligingscentrum**en selecteert **u Beleid selecteren**. Selecteer het abonnement waarvoor u automatisch inrichten wilt uitschakelen. Wanneer u een **abonnementsbeveiligingsbeleid selecteert,** wordt het verzamelen van gegevens geopend. Selecteer **Auto provisioning** **Uit .**


## <a name="how-do-i-enable-data-collection"></a>Hoe schakel ik het verzamelen van gegevens in?

U het verzamelen van gegevens voor uw Azure-abonnement inschakelen in het beveiligingsbeleid. Gegevensverzameling inschakelen. [Meld u aan bij de Azure-portal,](https://portal.azure.com)selecteer **Bladeren,** selecteer **Beveiligingscentrum**en selecteer **Beveiligingsbeleid**. Selecteer het abonnement dat u automatisch inrichten wilt inschakelen. Wanneer u een **abonnementsbeveiligingsbeleid selecteert,** wordt het verzamelen van gegevens geopend. Selecteer Onder **Automatisch inrichten**de optie **Aan**.


## <a name="what-happens-when-data-collection-is-enabled"></a>Wat gebeurt er als het verzamelen van gegevens is ingeschakeld?

Wanneer automatische inrichting is ingeschakeld, voorziet Security Center in de loganalytics-agent op alle ondersteunde Azure VM's en eventuele nieuwe nieuwe vm's. Automatische inlevering wordt aanbevolen, maar handmatige agent installatie is ook beschikbaar. [Meer informatie over het installeren van de extensie van de Log Analytics-agent](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

De agent maakt het procescreatiegebeurtenis 4688 en het *veld CommandLine* in gebeurtenis 4688 mogelijk. Nieuwe processen die op de VM zijn gemaakt, worden door EventLog geregistreerd en gecontroleerd door de detectieservices van security center. Zie [beschrijvingsvelden in 4688 voor](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)meer informatie over de gegevens die voor elk nieuw proces zijn vastgelegd. De agent verzamelt ook de 4688 gebeurtenissen die zijn gemaakt op de VM en slaat ze op in zoekopdrachten.

De agent maakt het ook mogelijk gegevens verzameling voor [Adaptive Application Controls](security-center-adaptive-application.md), Security Center configureert een lokale AppLocker beleid in audit modus om alle toepassingen mogelijk te maken. Dit beleid zorgt ervoor dat AppLocker gebeurtenissen genereert, die vervolgens worden verzameld en gebruikt door Security Center. Het is belangrijk op te merken dat dit beleid niet wordt geconfigureerd op machines waarop al een geconfigureerd AppLocker-beleid is. 

Wanneer Security Center verdachte activiteiten op de VM detecteert, wordt de klant per e-mail op de hoogte gesteld als [er beveiligingscontactgegevens](security-center-provide-security-contact-details.md) zijn verstrekt. Er is ook een waarschuwing zichtbaar in het dashboard met beveiligingswaarschuwingen van het beveiligingscentrum.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Werkt Security Center met een OMS-gateway?

Ja. Azure Security Center maakt gebruik van Azure Monitor om gegevens te verzamelen van Azure VM's en servers met behulp van de Log Analytics-agent.
Om de gegevens te verzamelen, moeten elke vm en server verbinding maken met internet via HTTPS. De verbinding kan direct zijn, via een proxy of via de [OMS Gateway.](../azure-monitor/platform/gateway.md)


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Heeft de Monitoring Agent invloed op de prestaties van mijn servers?

De agent verbruikt een nominale hoeveelheid systeembronnen en mag weinig invloed hebben op de prestaties. Zie de [plannings- en operationele handleiding](security-center-planning-and-operations-guide.md#data-collection-and-storage)voor meer informatie over de impact op de prestaties en de agent en extensie.


## <a name="where-is-my-data-stored"></a>Waar worden mijn gegevens opgeslagen?

Gegevens die van deze agent worden verzameld, worden opgeslagen in een bestaande Log Analytics-werkruimte die is gekoppeld aan uw abonnement of een nieuwe werkruimte. Zie [Gegevensbeveiliging](security-center-data-security.md)voor meer informatie.


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
