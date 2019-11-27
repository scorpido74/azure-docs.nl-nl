---
title: Veelgestelde vragen over Azure Monitor voor VM's (preview) | Microsoft Docs
description: Antwoorden op veelgestelde vragen over Azure Monitor voor VM's die de status en prestaties van de virtuele Azure-machines bewaakt naast het automatisch detecteren en toewijzen van toepassings onderdelen en hun afhankelijkheden.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/09/2018
ms.openlocfilehash: 579538996e934c7068c397a284d819f5ddb92f08
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74305460"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Veelgestelde vragen over Azure Monitor voor VM's (preview)
Deze veelgestelde vragen over micro soft vindt u een lijst met veel gestelde antwoorden over Azure Monitor voor VM's. Als u aanvullende vragen over de oplossing hebt, gaat u naar het [discussie forum](https://feedback.azure.com/forums/34192--general-feedback) en plaatst u uw vragen. Wanneer u een vraag is vaak wordt gevraagd, toevoegen we deze aan dit artikel zodat snel en eenvoudig kunnen worden gevonden.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Kan ik onboarding uitvoeren op een bestaande werk ruimte?
Als uw virtuele machines al zijn verbonden met een Log Analytics-werk ruimte, kunt u deze werk ruimte blijven gebruiken bij onboarding naar Azure Monitor voor VM's, mits deze zich in een van de ondersteunde regio's bevindt die [hier](vminsights-enable-overview.md#prerequisites)worden vermeld.

Als er een onboarding wordt uitgevoerd, worden er prestatie meter items voor de werk ruimte geconfigureerd die ervoor zorgen dat alle Vm's rapport gegevens naar de werk ruimte worden verzameld om deze informatie te verzamelen voor weer gave en analyse in Azure Monitor voor VM's.  Als gevolg hiervan worden er prestatie gegevens weer gegeven van alle Vm's die zijn verbonden met de geselecteerde werk ruimte.  De functies status en kaart worden alleen ingeschakeld voor de virtuele machines die u hebt opgegeven voor onboarding.

Raadpleeg het artikel [overzicht inschakelen](vminsights-enable-overview.md#performance-counters-enabled) voor meer informatie over welke prestatie meter items zijn ingeschakeld.

## <a name="can-i-onboard-to-a-new-workspace"></a>Kan ik onboarding uitvoeren op een nieuwe werk ruimte? 
Als uw Vm's momenteel niet zijn verbonden met een bestaande Log Analytics-werk ruimte, moet u een nieuwe werk ruimte maken om uw gegevens op te slaan. Het maken van een nieuwe standaardwerk ruimte wordt automatisch uitgevoerd als u een enkele Azure-VM voor Azure Monitor voor VM's configureert via de Azure Portal.

Als u ervoor kiest de op scripts gebaseerde methode te gebruiken, worden deze stappen behandeld in het artikel [enable Azure monitor voor VM's (preview) met Azure PowerShell of Resource Manager-sjabloon](vminsights-enable-at-scale-powershell.md) . 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Wat moet ik doen als mijn VM al rapporteert aan een bestaande werk ruimte?
Als u al gegevens van uw virtuele machines verzamelt, is het mogelijk dat u deze al hebt geconfigureerd om gegevens te rapporteren aan een bestaande Log Analytics-werk ruimte.  Als die werk ruimte zich in een van de ondersteunde regio's bevindt, kunt u Azure Monitor voor VM's inschakelen voor die bestaande werk ruimte.  Als de werk ruimte die u al gebruikt, zich niet in een van de ondersteunde regio's bevindt, kunt u op dit moment niet op de Azure Monitor voor VM's.  We werken actief ter ondersteuning van extra regio's.

>[!NOTE]
>We configureren prestatie meter items voor de werk ruimte die van invloed zijn op alle Vm's die rapporteren aan de werk ruimte, ongeacht of u hebt gekozen om ze te Azure Monitor voor VM's. Raadpleeg onze [documentatie](../../azure-monitor/platform/data-sources-performance-counters.md)voor meer informatie over de configuratie van prestatie meter items voor de werk ruimte. Raadpleeg het artikel [enable Azure monitor voor VM's](vminsights-enable-overview.md#performance-counters-enabled) voor informatie over de tellers die zijn geconfigureerd voor Azure monitor voor VM's.  

## <a name="why-did-my-vm-fail-to-onboard"></a>Waarom is mijn VM niet op de onboarding uitgevoerd?
Wanneer een virtuele machine van Azure wordt geboardd vanuit de Azure Portal, gebeurt het volgende:

* Een standaard Log Analytics-werkruimte is gemaakt, als die optie hebt geselecteerd.
* De prestatie meter items zijn geconfigureerd voor de geselecteerde werk ruimte. Als deze stap mislukt, ziet u dat sommige prestatie grafieken en-tabellen geen gegevens weer geven voor de virtuele machine die u hebt opgevolgd. U kunt dit probleem oplossen door het Power shell-script uit te voeren dat [hier](vminsights-enable-at-scale-powershell.md#enable-performance-counters)wordt beschreven.
* De Log Analytics-agent wordt op virtuele machines van Azure geïnstalleerd met behulp van een VM-extensie, indien nodig.  
* De Azure Monitor voor VM's kaart dependency agent wordt geïnstalleerd op virtuele machines van Azure met behulp van een uitbrei ding, indien bepaald dat deze is vereist.  
* Azure Monitor onderdelen die ondersteuning bieden voor de status functie worden zo nodig geconfigureerd en de virtuele machine is geconfigureerd om status gegevens te rapporteren.

Tijdens het onboarding-proces wordt gecontroleerd op de status van elk van de bovenstaande om een meldings status te retour neren in de portal. Configuratie van de werkruimte en de installatie van de agent duurt doorgaans 5 tot 10 minuten. Het weer geven van bewakings-en status gegevens in de portal duurt 5 tot 10 minuten.  

Als u onboarding hebt gestart en berichten ziet met de melding dat de virtuele machine moet worden uitgevoerd, kunt u Maxi maal 30 minuten voor de virtuele machine het proces volt ooien. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Ik heb alleen Azure Monitor voor VM's ingeschakeld, waarom kan ik al mijn Vm's zien die worden bewaakt door de status functie?
De status functie is ingeschakeld voor alle Vm's die zijn verbonden met de Log Analytics-werk ruimte, zelfs wanneer de actie voor één virtuele machine wordt gestart.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Kan ik de planning wijzigen voor wanneer de status criteria een voor waarde evalueren?
Nee, de tijds periode en frequentie van de status criteria kunnen niet worden gewijzigd in deze release. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Kan ik status criteria uitschakelen voor een voor waarde die ik niet hoeft te controleren?
Status criteria kunnen niet worden uitgeschakeld in deze versie.

## <a name="are-the-health-alert-severities-configurable"></a>Zijn de ernst van de status waarschuwing configureerbaar?  
Ernst van status waarschuwing kan niet worden gewijzigd. deze kunnen alleen worden ingeschakeld of uitgeschakeld. Daarnaast worden enkele waarschuwings Ernst bijgewerkt op basis van de status criteria. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Als ik de instellingen van een bepaald status criterium opnieuw Configureer, kan de scope worden toegepast op een specifiek exemplaar?  
Als u een instelling van een status criterium instantie wijzigt, worden alle status criterium exemplaren van hetzelfde type op de Azure-VM gewijzigd. Bijvoorbeeld, als de drempelwaarde van de status van de vrije ruimte criterium schijfexemplaar die overeenkomt met de logische schijf C: wordt gewijzigd, geldt deze drempelwaarde voor alle andere logische schijven die zijn gedetecteerd en bewaakt voor dezelfde virtuele machine.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Bewaakt de status functie logische processors en kernen?
Nee, de status van een afzonderlijke processor en een logisch processor niveau is niet opgenomen voor een Windows. alleen het totale CPU-gebruik wordt standaard gecontroleerd om de CPU-belasting effectief te beoordelen op basis van het totale aantal logische Cpu's dat beschikbaar is voor de virtuele machine van Azure. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Zijn alle drempel waarden voor status criteria geconfigureerd?  
Drempel waarden voor de status criteria die zijn gericht op een Windows-VM, kunnen niet worden gewijzigd, omdat hun status wordt ingesteld op *actief* of *beschikbaar*. Wanneer u een query uitvoert op de status van de [API voor workload-bewaking](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components), wordt de *ComparisonOperator* -waarde **LessThan** of **GreaterThan** met een *drempel* waarde van **4** voor de service of entiteit weer gegeven als:
   - DNS-Client-Service Health-Service niet actief is. 
   - DHCP-client-service health-Service niet actief is. 
   - RPC-Service Health-Service niet actief is. 
   - Windows firewall-service health-Service niet actief is.
   - Windows-gebeurtenislogboek service health-Service niet actief is. 
   - Server-service health-Service niet actief is. 
   - Windows remote management-service health-Service wordt niet uitgevoerd. 
   - Bestandssysteemfout of beschadiging – logische schijf is niet beschikbaar.

Drempel waarden voor de volgende status criteria van Linux kunnen niet worden gewijzigd, omdat hun status al is ingesteld op *waar*. In de status wordt de *comparisonOperator* met de waarde **LessThan** en de *drempel* waarde **1** weer gegeven wanneer er een query wordt uitgevoerd vanuit de API voor het bewaken van workloads voor de entiteit, afhankelijk van de context:
   - Status van logische schijf – logische schijf is niet online / beschikbaar
   - De Status van de schijf-schijf is niet online / beschikbaar
   - Status van Network Adapter - netwerkadapter is uitgeschakeld

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Hoe kan ik wijzigen van waarschuwingen die zijn opgenomen in de status functie?
Regels voor waarschuwingen die zijn gedefinieerd voor elk criterium status worden niet weergegeven in de Azure-portal. U kunt een waarschuwings regel voor de status alleen in-of uitschakelen in de [API voor workload-bewaking](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). U kunt ook geen [Azure monitor actie groep](../../azure-monitor/platform/action-groups.md) toewijzen voor status waarschuwingen in de Azure Portal. U kunt de API voor meldings instellingen alleen gebruiken om een actie groep te configureren die wordt geactiveerd wanneer een status waarschuwing wordt geactiveerd. Op dit moment kunt u actie groepen toewijzen aan een virtuele machine, zodat alle *status waarschuwingen* voor de virtuele machine dezelfde actie groepen activeren. In tegenstelling tot traditionele Azure-waarschuwingen is er geen concept van een aparte actiegroep voor elke status waarschuwingsregel. Bovendien worden alleen actiegroepen die zijn geconfigureerd voor e-mailadres of SMS-berichten ondersteund wanneer de gezondheid van waarschuwingen worden geactiveerd. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Ik zie niet sommige of alle gegevens in de prestatiegrafieken voor mijn virtuele machine
Als u geen prestatie gegevens in de tabel schijf of in een aantal prestatie grafieken ziet, zijn de prestatie meter items mogelijk niet geconfigureerd in de werk ruimte. Voer het volgende [Power shell-script](vminsights-enable-at-scale-powershell.md#enable-with-powershell)uit om het probleem op te lossen.

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Hoe verschilt Azure Monitor voor VM's kaart functie van Servicetoewijzing?
De functie Azure Monitor voor VM's map is gebaseerd op Servicetoewijzing, maar heeft de volgende verschillen:

* De kaart weergave is toegankelijk via de VM-Blade en uit Azure Monitor voor VM's onder Azure Monitor.
* De verbindingen in de kaart kunnen nu worden geklikt en weer geven van de verbindings gegevens in het deel venster voor de geselecteerde verbinding.
* Er is een nieuwe API die wordt gebruikt om de kaarten te maken voor betere ondersteuning van complexere kaarten.
* Bewaakte Vm's zijn nu opgenomen in het knoop punt client groep en het ring diagram toont het aandeel van de bewaakte en niet-bewaakte virtuele machines in de groep.  Het kan ook worden gebruikt voor het filteren van de lijst met computers wanneer de groep wordt uitgevouwen.
* Bewaakte virtuele machines zijn nu opgenomen in de server poort groep knoop punten en het ring diagram toont het aandeel van de bewaakte en niet-bewaakte computers in de groep.  Het kan ook worden gebruikt voor het filteren van de lijst met computers wanneer de groep wordt uitgevouwen.
* De kaart stijl is bijgewerkt zodat deze consistent is met de app-toewijzing van Application Insights.
* De side panels zijn bijgewerkt en hebben niet de volledige set integraties die worden ondersteund in Servicetoewijzing-Updatebeheer, Wijzigingen bijhouden, beveiliging en Service Desk. 
* De optie voor het kiezen van groepen en machines die u wilt toewijzen, is bijgewerkt en ondersteunt nu abonnementen, resource groepen, Azure-schaal sets voor virtuele machines en Cloud Services.
* U kunt geen nieuwe Servicetoewijzing machine groepen maken in de functie Azure Monitor voor VM's kaart.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Waarom kunnen mijn prestatiegrafieken stippellijnen weergeven?
Dit kan een paar oorzaken hebben.  Als er sprake is van een onderbreking in het verzamelen van gegevens, worden de lijnen weer gegeven als gestippeld.  Als u de gegevens sampling frequentie hebt gewijzigd voor de prestatie meter items ingeschakeld (de standaard instelling is het verzamelen van gegevens elke 60 seconden), kunt u stippel lijnen in de grafiek zien als u een beperkt tijds bereik voor de grafiek kiest en de sampling frequentie kleiner is dan de Bucket grootte die in de grafiek wordt gebruikt (bijvoorbeeld de sampling frequentie is elke 10 minuten en elke Bucket in de grafiek is 5 minuten).  Als u een breder tijds bereik kiest voor weer gave, moeten de grafiek lijnen worden weer gegeven als ononderbroken lijnen in plaats van punten in dit geval.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Worden groepen ondersteund met Azure Monitor voor virtuele machines?
Ja, nadat u de afhankelijkheids agent hebt geïnstalleerd, verzamelen we informatie van de virtuele machines om groepen weer te geven op basis van het abonnement, de resource groep, de schaal sets voor virtuele machines en Cloud Services.  Als u Servicetoewijzing hebt gebruikt en computer groepen hebt gemaakt, worden deze ook weer gegeven.  Computer groepen worden ook weer gegeven in het filter groepen als u deze hebt gemaakt voor de werk ruimte die u bekijkt. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hoe kan ik raadpleegt u de details van wat is de 95e percentiel regel in de samengevoegde prestatie grafieken?
Standaard wordt de lijst gesorteerd om u de virtuele machines weer te geven die de hoogste waarde voor het 95e percentiel hebben voor de geselecteerde metriek, met uitzonde ring van de beschik bare geheugen grafiek, waarin de computers met de laagste waarde van het vijfde percentiel worden weer gegeven.  Als u op de grafiek klikt, wordt de **bovenste N lijst** weergave geopend met de juiste metriek geselecteerd.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hoe verwerken de kaart functie dubbele IP-adressen over verschillende vnets en subnetten?
Als u IP-bereiken dupliceert met Vm's of virtuele-machine schaal sets van Azure in subnetten en vnets, kan dit ertoe leiden dat Azure Monitor voor VM's kaart onjuiste gegevens weergeeft. Dit is een bekend probleem en we onderzoeken de opties om deze ervaring te verbeteren.

## <a name="does-map-feature-support-ipv6"></a>Ondersteunt de functie voor het toewijzen van IPv6?
De toewijzings functie ondersteunt momenteel alleen IPv4 en we onderzoeken de ondersteuning voor IPv6. We bieden ook ondersteuning voor IPv4 die in IPv6 is getunneld.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Wanneer ik een kaart voor een resource groep of een andere grote groep laad, kan de kaart moeilijk worden weer gegeven
We hebben verbeteringen aangebracht in de toewijzing van het verwerken van grote en complexe configuraties, maar het is ook mogelijk dat een kaart een groot aantal knoop punten, verbindingen en een knoop punt als een cluster kan hebben.  We zijn om verder te verbeteren van ondersteuning voor meer schaalbaarheid.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Waarom ziet de netwerk grafiek op het tabblad prestaties er anders uit dan de netwerk grafiek op de overzichts pagina van de Azure VM?

Op de overzichts pagina voor een Azure-VM worden grafieken weer gegeven op basis van de meting van de activiteit van de host in de gast-VM.  Voor de netwerk grafiek in het overzicht van de Azure-VM wordt alleen het netwerk verkeer weer gegeven dat wordt gefactureerd.  Dit omvat geen Inter-vnet-verkeer.  De gegevens en grafieken die voor Azure Monitor voor VM's worden weer gegeven, zijn gebaseerd op gegevens van de gast-VM en de netwerk grafiek bevat alle TCP/IP-verkeer dat binnenkomend en uitgaand is voor die virtuele machine, inclusief tussen-vnet.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hoe wordt de reactie tijd gemeten voor gegevens die zijn opgeslagen in VMConnection en worden weer gegeven in het deel venster verbinding en in werkmappen?

De reactie tijd is een benadering. Omdat we de code van de toepassing niet instrumenteren, weten we niet echt wanneer een aanvraag begint en wanneer het antwoord wordt ontvangen. In plaats daarvan zien we dat er gegevens worden verzonden via een verbinding en dat de gegevens vervolgens weer worden teruggestuurd op deze verbinding. Onze agent houdt deze verzenden en ontvangen en probeert deze te koppelen: een volg orde van verzen dingen, gevolgd door een reeks ontvangen, wordt geïnterpreteerd als een aanvraag/antwoord-paar. De tijds duur tussen deze bewerkingen is de reactie tijd. Deze bevat de netwerk latentie en de verwerkings tijd van de server.

Deze benadering werkt goed voor protocollen die op aanvraag/antwoord zijn gebaseerd: een enkele aanvraag verloopt op de verbinding en er wordt één antwoord ontvangen. Dit is het geval voor HTTP (S) (zonder pipeline), maar niet aan andere protocollen is voldaan.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Zijn er beperkingen als ik aan het gratis prijs plan van Log Analytics?
Als u Azure Monitor hebt geconfigureerd met een Log Analytics-werk ruimte met behulp van de *gratis* prijs categorie, worden in azure monitor voor VM's kaart-functie alleen vijf verbonden computers ondersteund die zijn verbonden met de werk ruimte. Als u vijf Vm's hebt verbonden met een gratis werk ruimte, verbreekt u de verbinding van een van de virtuele machines en maakt u later een nieuwe virtuele machine, de nieuwe virtuele machine wordt niet bewaakt en wordt niet weer gegeven op de pagina overzicht.  

In dit voor beeld wordt u gevraagd de optie **nu proberen** te kiezen wanneer u de virtuele machine opent en **inzichten (preview)** selecteert in het linkerdeel venster, zelfs nadat het al op de virtuele machine is geïnstalleerd.  U wordt echter niet gevraagd naar opties die normaal gesp roken optreden als deze virtuele machine niet is opvoerd aan Azure Monitor voor VM's. 

## <a name="next-steps"></a>Volgende stappen
Bekijk [Azure monitor voor VM's](vminsights-enable-overview.md) om inzicht te krijgen in de vereisten en methoden om de bewaking van uw virtuele machines mogelijk te maken.
