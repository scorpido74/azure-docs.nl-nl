---
title: Meer informatie over de status van uw virtuele machines in azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de status van virtuele machines en onderliggende besturings systemen met Azure Monitor voor VM's begrijpt.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: c9dfd3bfcacc1f50bb9be04c927bf3cbb4895f6d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555256"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Inzicht in de status van uw virtuele machines in azure

Azure bevat services voor specifieke rollen of taken in de bewakings ruimte, maar biedt geen gedetailleerde status perspectieven van besturings systemen (OSs) gehost op Azure virtual machines (Vm's). Hoewel u Azure Monitor voor verschillende voor waarden kunt gebruiken, is het niet ontworpen om de status van kern onderdelen of de algehele status van virtuele machines te model leren en weer te geven.

Met behulp van Azure Monitor voor VM's status kunt u de beschik baarheid en prestaties van een Windows-of Linux-gast besturingssysteem actief bewaken. De status functie maakt gebruik van een model dat belang rijke onderdelen en hun relaties weergeeft, biedt criteria waarmee wordt aangegeven hoe de status van een onderdeel moet worden gemeten en er wordt een waarschuwing verzonden wanneer een onjuiste voor waarde wordt gedetecteerd.

Het weer geven van de algehele status van een virtuele Azure-machine en het onderliggende besturings systeem kan vanuit twee perspectieven worden waargenomen: rechtstreeks vanuit een VM of op alle virtuele machines in een resource groep van Azure Monitor.

In dit artikel wordt beschreven hoe u snel status problemen kunt evalueren, onderzoeken en oplossen wanneer deze worden gedetecteerd door de functie voor Azure Monitor voor VM's status.

Zie [Azure monitor voor VM's inschakelen](vminsights-enable-overview.md)voor meer informatie over het configureren van Azure monitor voor VM's.

## <a name="monitoring-configuration-details"></a>Bewakings configuratie Details

In deze sectie vindt u een overzicht van de standaard status criteria voor het bewaken van Azure Windows-en Linux-Vm's. Alle status criteria zijn vooraf geconfigureerd om een waarschuwing te verzenden wanneer ze een onjuiste voor waarde detecteren.

| Monitor naam | Frequentie (min.) | Lookback duur (min) | Operator | Spreek | Waarschuwen over status | Ernst | Categorie workload | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| Logische schijf online | 5 | 15 | <> | 1 (waar) | Kritiek | Sev1 | Linux | 
| Beschik bare ruimte op logische schijf | 5 | 15 | < | 200 MB (waarschuwing)<br> 100 MB (kritiek) | Waarschuwing | Sev1<br> Sev2 | Linux | 
| % Vrije inodes voor logische schijf | 5 | 15 | < | 5% | Kritiek | Sev1 | Linux | 
| Percentage beschik bare ruimte logische schijf | 5 | 15 | < | 5% | Kritiek | Sev1 | Linux | 
| Status van de netwerk adapter | 5 | 15 | <> | 1 (waar) | Waarschuwing | Sev2 | Linux | 
| Beschik bare mega bytes geheugen van besturings systeem | 5 | 10 | < | 2,5 MB | Kritiek | Sev1 | Linux | 
| Aantal seconden/Lees tijd schijf | 5 | 25 | > | 0,05 s | Kritiek | Sev1 | Linux | 
| Schijf gemiddelde tijd schijf overdracht | 5 | 25 | > | 0,05 s | Kritiek | Sev1 | Linux | 
| Schijf gemiddelde schrijf tijd schijf | 5 | 25 | > | 0,05 s | Kritiek | Sev1 | Linux | 
| Schijf status | 5 | 25 | <> | 1 (waar) | Kritiek | Sev1 | Linux | 
| Totale percentage processor tijd van het besturings systeem | 5 | 10 | >= | 95% | Kritiek | Sev1 | Linux | 
| Totaal percentage CPU-gebruik | 5 | 10 | >= | 95% | Kritiek | Sev1 | Windows | 
| Fout in het bestands systeem of beschadiging | 60 | 60 | <> | 4 | Kritiek | Sev1 | Windows | 
| Gemiddeld aantal seconden per Lees bewerking voor logische schijf | 1 | 15 | > | 0.04 s | Waarschuwing | Sev2 | Windows | 
| Gemiddeld aantal seconden per overdracht logische schijf | 1 | 15 | > | 0.04 s | Waarschuwing | Sev2 | Windows | 
| Gemiddeld aantal seconden per schrijf bewerking logische schijf (logische schijf) | 1 | 15 | > | 0.04 s | Waarschuwing | Sev2 | Windows | 
| Huidige wachtrij lengte voor de schijf (logische schijf) | 5 | 60 | >= | 32 | Waarschuwing | Sev2 | Windows | 
| Beschik bare ruimte op logische schijf (MB) | 15 | 60 | > | 500 MB-waarschuwing<br> 300 MB kritiek | Kritiek | Sev1<br> Sev2 | Windows | 
| Beschik bare ruimte op logische schijf (%) | 15 | 60 | > | waarschuwing voor 10%<br> 5% kritiek | Kritiek | Sev1<br> Sev2 | Windows |
| Percentage niet-actieve tijd van logische schijf | 15 | 360 | <= | 20% | Waarschuwing | Sev2 | Windows | 
| Percentage gebruikte band breedte gelezen | 5 | 60 | >= | 60% | Waarschuwing | Sev2 | Windows | 
| Totaal percentage gebruikte band breedte | 5 | 60 | >= | 75% | Waarschuwing | Sev2 | Windows | 
| Percentage gebruikte band breedte voor schrijven | 5 | 60 | >= | 60% | Waarschuwing | Sev2 | Windows | 
| Service Health DHCP-client | 5 | 12 | <> | 4 (wordt uitgevoerd) | Kritiek | Sev1 | Windows | 
| DNS-Client Service Health | 5 | 12 | <> | 4 (wordt uitgevoerd) | Kritiek | Sev1 | Windows | 
| Windows-gebeurtenis logboek Service Health | 5 | 12 | <> | 4 (wordt uitgevoerd) | Kritiek | Sev1 | Windows | 
| Windows Firewall Service Health | 5 | 12 | <> | 4 (wordt uitgevoerd) | Kritiek | Sev1 | Windows | 
| RPC-Service Health | 5 | 12 | <> | 4 (wordt uitgevoerd) | Kritiek | Sev1 | Windows | 
| Server Service Health | 5 | 12 | <> | 4 (wordt uitgevoerd) | Kritiek | Sev1 | Windows | 
| Service Health voor extern beheer van Windows | 5 | 12 | <> | 4 (wordt uitgevoerd) | Kritiek | Sev1 | Windows | 
| Beschik bare mega bytes aan geheugen | 5 | 10 | < | 100 MB | Kritiek | Sev1 | Windows | 
| Vermeldingen in tabel met beschik bare systeem pagina's | 5 | 10 | <= | 5000 | Kritiek | Sev1 | Windows | 
| Geheugen pagina's per seconde | 5 | 10 | >= | 5000/s | Waarschuwing | Sev1 | Windows | 
| Percentage toegewezen geheugen in gebruik | 5 | 10 | > | 80% | Kritiek | Sev1 | Windows | 
| Gemiddeld aantal seconden per overdracht schijf | 1 | 15 | > | 0.04 s | Waarschuwing | Sev2 | Windows | 
| Gemiddeld aantal seconden per schrijf bewerking schijf | 1 | 15 | > | 0.04 s | Waarschuwing | Sev2 | Windows | 
| Huidige wachtrij lengte voor de schijf | 5 | 60 | >= | 32 | Waarschuwing | Sev2 | Windows | 
| Percentage niet-actieve tijd schijf | 5 | 60 | >= | 20% | Waarschuwing | Sev2 | Windows | 

>[!NOTE]
>De duur Lookback geeft aan hoe vaak in het venster terugkijken de metrische waarden worden gecontroleerd, bijvoorbeeld de laatste vijf minuten.  

>[!NOTE]
>Frequentie geeft aan hoe vaak de metrische waarschuwing controleert als aan de voor waarden wordt voldaan, bijvoorbeeld elke minuut.  Het is de frequentie waarmee het criterium van de status wordt uitgevoerd en lookback is de duur waarover het status criterium wordt geëvalueerd. Status criterium wordt bijvoorbeeld geëvalueerd als het **CPU-gebruik** van de voor waarde hoger is dan 95 procent met een frequentie van 5 minuten en gedurende 15 minuten langer dan 95% is (3 opeenvolgende evaluatie cycli), dan wordt de status bijgewerkt naar kritiek Ernst als dit nog niet is gebeurd.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Als u zich wilt aanmelden, gaat u naar de [Azure Portal](https://portal.azure.com).

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Inleiding tot Azure Monitor voor VM's status

Voordat u de status functie voor één virtuele machine of een groep virtuele machines gebruikt, is het belang rijk om te begrijpen hoe de gegevens worden weer gegeven en wat de visualisaties vertegenwoordigen.

### <a name="view-health-directly-from-a-vm"></a>Status direct vanuit een virtuele machine weer geven

Als u de status van een virtuele Azure-machine wilt weer geven, selecteert u **inzichten (preview)** in het linkerdeel venster van de virtuele machine. Op de pagina van de VM Insights wordt het tabblad **status** standaard geopend en toont de status weergave van de virtuele machine.

![Overzicht van de status van een geselecteerde Azure-virtuele machine Azure Monitor voor VM's](./media/vminsights-health/vminsights-directvm-health-01.png)

In de sectie **VM-status** van de gast bevat de tabel de status rollup van prestatie onderdelen die worden bewaakt door de status criteria voor de virtuele machine en het totale aantal VM-status waarschuwingen dat is gegenereerd door beschadigde onderdelen. Deze onderdelen zijn onder andere **CPU**, **geheugen**, **schijf**en **netwerk**. Vouw de dubbele punt haak naast de status van de gast-VM uit om de status van de onderdelen ervan weer te geven.

![Status van de Azure Monitor voor VM's onderdeel van een geselecteerde Azure-virtuele machine](./media/vminsights-health/vminsights-directvm-health-02.png)

Als u de status naast het onderdeel selecteert, wordt de status diagnose in de context van het geselecteerde onderdeel geopend. Hierin wordt de samen stelling van de status van het onderdeel weer gegeven, waarbij wordt beschreven welke status criteria worden gebruikt voor het berekenen van de status. Zie [status diagnostiek en werken met status criteria](#health-diagnostics)voor meer informatie. Zie [waarschuwingen](#alerts)voor meer informatie over waarschuwingen.

De statussen die zijn gedefinieerd voor een virtuele machine, worden beschreven in de volgende tabel:

|Diapictogram |Status |Betekenis |
|-----|-------------|---------------|
| |In orde |De virtuele machine bevindt zich binnen de gedefinieerde status voorwaarden. Deze status geeft aan dat er geen problemen zijn gedetecteerd en dat de virtuele machine normaal functioneert. Met een bovenliggende totaliserings monitor wordt de status samengevouwen en wordt de best mogelijke status van het onderliggende element weer gegeven.|
| |Kritiek |De status bevindt zich niet in de gedefinieerde status, wat aangeeft dat er een of meer kritieke problemen zijn gedetecteerd. Deze problemen moeten worden opgelost om de normale functionaliteit te herstellen. Met een bovenliggende totaliserings monitor wordt de status weer gegeven en wordt de beste status van het onderliggende element weer gegeven.|
| |Waarschuwing |De status ligt tussen twee drempel waarden voor de gedefinieerde status, waarbij de ene de waarschuwings status aangeeft en de andere duidt op een kritieke status (er kunnen drie status drempels worden geconfigureerd), of wanneer een niet-kritiek probleem kritieke problemen kan veroorzaken als onbekende. Als er een bovenliggende totaliserings monitor is, wordt er een waarschuwing weer gegeven als een of meer onderliggende items een waarschuwings status hebben. Als één onderliggend element een kritieke status heeft en een ander onderliggend item met een waarschuwings status, wordt de status van de bovenliggende Rollup als kritiek weer gegeven.|
| |Onbekend |De status kan om verschillende redenen niet worden berekend. De volgende sectie bevat aanvullende details en mogelijke oplossingen. |

Een onbekende status kan worden veroorzaakt door de volgende problemen:

- De agent is opnieuw geconfigureerd en er worden geen meldingen meer gerapporteerd aan de werk ruimte die is opgegeven toen Azure Monitor voor VM's werd ingeschakeld. Als u de agent wilt configureren om te rapporteren aan de werk ruimte, raadpleegt u [een werk ruimte toevoegen of verwijderen](../platform/agent-manage.md#adding-or-removing-a-workspace).
- De virtuele machine is verwijderd.
- De werk ruimte die is gekoppeld aan Azure Monitor voor VM's, is verwijderd. U kunt de werk ruimte herstellen als u de voor delen van Premier Support hebt. Ga naar [premier](https://premier.microsoft.com/) en open een ondersteunings aanvraag.
- De oplossings afhankelijkheden zijn verwijderd. Als u de ServiceMap-en InfrastructureInsights-oplossingen in uw Log Analytics-werk ruimte opnieuw wilt inschakelen, installeert u de ServiceMap-oplossing opnieuw met behulp van de [Azure Resource Manager-sjabloon](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution). Als u de InfastructureInsights-oplossing, de e-mail vminsights@microsoft.com, opnieuw wilt installeren. 
- De virtuele machine is afgesloten.
- De Azure VM-service is niet beschikbaar of het onderhoud wordt uitgevoerd.
- Er is voldaan aan de [dagelijkse gegevens of de Bewaar limiet](../platform/manage-cost-storage.md) voor de werk ruimte.

Selecteer **status diagnostiek weer geven** om een pagina te openen met alle onderdelen van een virtuele machine, gekoppelde status criteria, status wijzigingen en andere problemen die worden gedetecteerd door bewakings onderdelen die betrekking hebben op de virtuele machine.

Zie [status diagnostiek](#health-diagnostics)voor meer informatie.

In de sectie **status** wordt een tabel weer gegeven met de status rollup van prestatie onderdelen die worden gecontroleerd op basis van de status criteria. Deze onderdelen zijn onder andere **CPU**, **geheugen**, **schijf**en **netwerk**. Als u een onderdeel selecteert, wordt er een pagina geopend met een lijst met alle bewakings criteria en de status van het onderdeel.

Wanneer u toegang hebt tot de status van een virtuele machine van Azure waarop Windows wordt uitgevoerd, wordt de status van de vijf belangrijkste Windows-services weer gegeven onder de status van de **kern Services**. Als u een van de Services selecteert, wordt er een pagina geopend met een lijst met de status criteria die voor dat onderdeel worden bewaakt samen met de status.

Als u de naam van de status criteria selecteert, wordt het deel venster met eigenschappen geopend. In dit deel venster kunt u de configuratie gegevens bekijken, zoals of de status criteria een corresponderende Azure Monitor waarschuwing hebben.

Zie [status diagnostiek en werken met status criteria](#health-diagnostics)voor meer informatie.

### <a name="aggregate-vm-perspective"></a>Samen vatting van VM-perspectief

Als u de status verzameling voor al uw virtuele machines in een resource groep wilt weer geven, selecteert u **Azure monitor** in de navigatie lijst in de portal en selecteert u vervolgens **virtual machines (preview)** .

![Bewakings weergave van de VM-inzichten van Azure Monitor](./media/vminsights-health/vminsights-aggregate-health.png)

Selecteer in de vervolg keuzelijst **abonnement** en **resource groep** de juiste resource groep die de vm's bevat die aan de groep zijn gerelateerd, om de gemelde status weer te geven. Uw selectie is alleen van toepassing op de status functie en de **prestaties** of **kaart** tabbladen worden niet overbelast.

Het tabblad **status** bevat de volgende informatie:

* Het aantal virtuele machines in een kritieke of slechte staat, vergeleken met het aantal gezonde of niet-verzonden gegevens (aangeduid met een onbekende status).
* En hoeveel virtuele machines per besturings systeem rapporteren een slechte status.
* Hoeveel virtuele machines zijn beschadigd vanwege een probleem met een processor, schijf, geheugen of netwerk adapter, gecategoriseerd op basis van de status.
* Hoeveel virtuele machines zijn beschadigd vanwege een probleem met een kern besturingssysteem service, gecategoriseerd op basis van de status.

Op het tabblad **status** kunt u de kritieke problemen identificeren die worden gedetecteerd door de status criteria die de virtuele machine controleren en de waarschuwings Details en bijbehorende kennis artikelen bekijken. Deze artikelen kunnen helpen bij het diagnosticeren en oplossen van problemen. Selecteer een van de mogelijke ernst om de pagina [alle waarschuwingen](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) te openen, gefilterd op die ernst.

In de lijst **VM-distributie per besturings systeem** worden de vm's weer gegeven die zijn opgenomen in de distributie van Windows-edities of Linux, samen met de bijbehorende versie. In elke besturingssysteem categorie worden de Vm's verder uitgesplitst, op basis van de status van de virtuele machine.

![Perspectief van virtuele machine distributie van VM Insights](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

Selecteer een wille keurige kolom, inclusief **VM-aantal**, **kritiek**, **waarschuwing**, **in orde**of **onbekend**. De lijst met gefilterde resultaten weer geven op de pagina **virtual machines** die overeenkomt met de geselecteerde kolom.

Als u bijvoorbeeld alle Vm's wilt bekijken waarop Red Hat Enterprise Linux versie 7,5 wordt uitgevoerd, selecteert u de waarde voor het **aantal** vm's voor dat besturings systeem en worden de vm's weer geven die overeenkomen met het filter en de huidige status.

![Voor beeld van een Rollup van Red Hat Linux-Vm's](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

Klik op **status weer geven** selectie vakje en de status wordt geretourneerd voor de gefilterde resultaten in de tabel.  

![Voor beeld van de status van Red Hat Linux-Vm's](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

Voor een van de items in de lijst kunt u op de corresponderende status klikken om status diagnostiek te starten. hier ziet u hoe de status van de geselecteerde virtuele machine wordt geëvalueerd. 

Als u op de pagina **virtual machines** de naam van een virtuele machine selecteert onder de naam van de virtuele **machine**, gaat u naar de pagina van het **VM-exemplaar** . Op deze pagina vindt u meer informatie over de problemen met waarschuwingen en status criteria die van invloed zijn op de geselecteerde virtuele machine. Filter de status Details door in de linkerbovenhoek van de pagina de **status pictogram te** selecteren om te zien welke onderdelen slecht zijn. U kunt ook de VM-status waarschuwingen weer geven die zijn gegenereerd door een niet-gezond onderdeel gecategoriseerd op basis van de ernst van de waarschuwing.

Selecteer in de lijst weergave van de **virtuele machine** de naam van een virtuele machine om de **status** pagina voor die virtuele machine te openen, op dezelfde manier als u direct **inzichten (preview)** van de virtuele machine hebt geselecteerd.

![VM-inzichten van een geselecteerde Azure-virtuele machine](./media/vminsights-health/vminsights-directvm-health.png)

De **virtual machines (preview) op Azure monitor** pagina toont een totaliserings status voor de virtuele machine en waarschuwingen. Deze status wordt gecategoriseerd op Ernst, wat de status van VM-waarschuwingen vertegenwoordigt die optreedt wanneer de status in orde wordt veranderd in beschadigd, op basis van criteria. Als u **vm's in kritieke omstandigheden** selecteert, wordt een pagina geopend met een lijst met een of meer virtuele machines met een kritieke status.

Als u de status van een van de virtuele machines selecteert, wordt de weer gave **status diagnostiek** van de virtuele machine weer gegeven. In deze weer gave kunt u bepalen welke status criteria een probleem met de status status aangeven. Wanneer de pagina **status diagnostiek** wordt geopend, worden alle VM-onderdelen en de bijbehorende status criteria weer gegeven met de huidige status.

Zie [status diagnostiek](#health-diagnostics)voor meer informatie.

Als u **alle status criteria weer geven** selecteert, wordt een pagina geopend met een lijst met alle beschik bare status criteria voor deze functie. De informatie kan verder worden gefilterd op basis van de volgende opties:

* **Type**. Er zijn drie soorten status criteria om de voor waarden te beoordelen en de algehele status van een bewaakte virtuele machine samen te stellen:
    - **Eenheid**. Meet een aspect van een virtuele machine. Dit type status criteria kan een prestatie meter item controleren om de prestaties van het onderdeel te bepalen, een script uit te voeren voor het uitvoeren van een synthetische trans actie of het volgen van een gebeurtenis die een fout aangeeft. Het filter is standaard ingesteld op eenheid.
    - **Afhankelijkheid**. Biedt een status aggregatie tussen verschillende entiteiten. Met deze status criteria kan de status van een entiteit afhankelijk zijn van de status van een ander type entiteit waarvan het gebruikmaakt voor een succes volle bewerking.
    - **Samen voegen**. Biedt een gecombineerde gezondheids status van vergelijk bare status criteria. Het status criterium eenheid en afhankelijkheid wordt doorgaans geconfigureerd onder een aggregatie status criterium. Naast het bieden van een betere algemene organisatie van de vele verschillende gezondheids criteria die zijn gericht op een entiteit, biedt aggregatie status criterium een unieke status voor afzonderlijke categorieën van de entiteiten.

* **Categorie**. Het type status criteria dat wordt gebruikt om Vergelijk bare criteria te groeperen voor rapportage doeleinden. Deze categorieën zijn **Beschik baarheid** en **prestaties**.

Als u wilt zien welke instanties een slechte status hebben, selecteert u een waarde onder de kolom **beschadigd onderdeel** . Op deze pagina geeft een tabel een lijst van de onderdelen die een kritieke status hebben.

## <a name="health-diagnostics"></a>Status diagnostiek

Op de pagina **status diagnostiek** kunt u het status model van een virtuele machine visualiseren. Op deze pagina vindt u een lijst met alle VM-onderdelen, bijbehorende status criteria, status wijzigingen en andere belang rijke problemen die worden aangegeven door bewaakte onderdelen die betrekking hebben op de virtuele machine.

![Voor beeld van de pagina status diagnostiek voor een virtuele machine](./media/vminsights-health/health-diagnostics-page-01.png)

Start diagnostische gegevens over de status door de volgende methoden te gebruiken:

* Op basis van de status van de totalisatie van het gecombineerde VM-perspectief in Azure Monitor:

    1. Op de pagina **status** selecteert u het pictogram voor **kritiek**, **waarschuwing**, **in orde**of **onbekend** in de para graaf status van de **gast-VM**.
    2. Ga naar de pagina met een lijst met alle virtuele machines die overeenkomen met die gefilterde categorie.
    3. Selecteer de waarde in de kolom **status** om de status diagnostiek te openen die zijn afgestemd op die VM.

* Door het besturings systeem van het gecombineerde VM-perspectief in Azure Monitor. Wanneer u onder **VM-distributie**een van de kolom waarden selecteert, wordt de pagina **virtual machines** geopend en wordt een lijst geretourneerd in de tabel die overeenkomt met de gefilterde categorie. Als u de waarde onder **status** kolom selecteert, wordt de status diagnostiek voor de geselecteerde virtuele machine geopend.
 
* Selecteer in de gast-VM op het tabblad Azure Monitor voor VM's **status** de optie **status diagnostiek weer geven**.

Met status diagnostiek worden status gegevens ingedeeld in twee categorieën: Beschik baarheid en prestaties.
 
Alle status criteria die zijn gedefinieerd voor een onderdeel, zoals een logische schijf, een CPU, enzovoort, kunnen worden weer gegeven zonder dat de twee categorieën worden gefilterd. Deze weer gaven kunnen worden weer gegeven in een volledig overzicht van de criteria, of via het filteren van de resultaten per categorie wanneer u **Beschik baarheid** of **prestaties**selecteert.

De criteria categorie kan ook worden weer gegeven naast de kolom **status criteria** . Als de criteria niet overeenkomen met de geselecteerde categorie, wordt in de kolom **status criteria** een bericht weer gegeven waarin **geen beschik bare status criteria voor de geselecteerde categorie zijn** opgenomen.

De status van een status criterium wordt bepaald door een van de vier typen: **kritiek**, **waarschuwing**, **in orde**en **onbekend**. De eerste drie kunnen worden geconfigureerd, wat inhoudt dat u de drempel waarden van de monitors rechtstreeks in het configuratie venster van de **status criteria** kunt wijzigen. Dit is ook mogelijk met behulp van de [controle bewerking](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)Azure monitor rest API update. **Onbekend** kan niet worden geconfigureerd en is gereserveerd voor specifieke scenario's.

De pagina **status diagnostiek** heeft drie hoofd secties:

* Onderdeel model
* Statuscriteria
* Status wijzigingen

![Secties van de pagina status diagnostiek](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>Onderdeel model

De kolom uiterst links op de pagina **status diagnostiek** is **onderdeel model**. Alle onderdelen, die zijn gekoppeld aan de virtuele machine, worden weer gegeven in deze kolom, samen met de huidige status.

In het volgende voor beeld zijn de gedetecteerde onderdelen **schijf**, **logische schijf**, **processor**, **geheugen**en **besturings systeem**. Er zijn meerdere exemplaren van deze onderdelen gedetecteerd en weer gegeven in deze kolom.

In de volgende afbeelding ziet u bijvoorbeeld dat de virtuele machine twee exemplaren van logische schijven, **C:** en **D:** , met de status in orde heeft:

![Voor beeld van onderdeel model dat wordt weer gegeven in status diagnostiek](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>Status criteria

De middelste kolom op de pagina status diagnostiek is **status criteria**. Het status model dat is gedefinieerd voor de virtuele machine wordt weer gegeven in een hiërarchische structuur. Het status model voor een VM bestaat uit eenheids-en statistische status criteria.

![Voor beeld van status criteria die worden weer gegeven in status diagnostiek](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

Met een status criterium wordt de status van een bewaakt exemplaar gemeten. Dit kan een drempel waarde zijn, de status van een entiteit, enzovoort. Een status criterium heeft twee of drie Configureer bare drempel waarden voor de status, zoals eerder beschreven. Het status criterium kan op elk moment slechts één van de mogelijke statussen zijn.

Het status model definieert criteria die de status van het totale doel en de algehele onderdelen van het doel bepalen. De hiërarchie van criteria wordt weer gegeven in de sectie **status criteria** op de pagina **status diagnostiek** .

Het beleid voor het samen stellen van statussen maakt deel uit van de configuratie van geaggregeerde status criteria (de standaard instelling is ingesteld op het **ergste aantal**). U vindt een standaardset met status criteria die worden uitgevoerd als onderdeel van deze functie in de sectie [configuratie details controleren](#monitoring-configuration-details) van dit artikel.

U kunt ook de lijst met Azure Monitor REST API [exemplaren controleren per resource](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) gebruiken om een lijst met alle status criteria op te halen. Dit criterium bevat configuratie details die worden uitgevoerd op de Azure VM-resource.

Voor het criterium type voor de **eenheids** status kan de configuratie worden gewijzigd door de koppeling naar het beletsel teken aan de rechter kant te selecteren. Selecteer **Details weer geven** om het configuratie deel venster te openen.

![Een voor beeld van een status criterium configureren](./media/vminsights-health/health-diagnostics-vm-example-02.png)

Als u in het deel venster configuratie voor de geselecteerde status criteria het voor beeld **gemiddelde aantal seconden per schrijf bewerking**gebruikt, kan de drempel worden geconfigureerd met een andere numerieke waarde. Het is een monitor met twee statussen, wat betekent dat deze alleen van **gezonde** in **waarschuwing**kan veranderen.

Andere status criteria gebruiken soms drie statussen, waar u de waarde voor waarschuwingen en kritieke drempel waarden voor statussen kunt configureren. U kunt ook een drempel waarde wijzigen met behulp van Azure Monitor REST API [monitor-configuratie](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update).

>[!NOTE]
>Het Toep assen van configuratie wijzigingen van status criteria op één exemplaar past deze toe op alle bewaakte exemplaren. Als u bijvoorbeeld **schijf-1 D:** selecteert en vervolgens de **gemiddelde aantal seconden per schrijf** drempel voor de schijf wijzigt, geldt de wijziging voor alle gedetecteerde en bewaakte instanties op de VM.


![Een voor beeld van een status criterium voor een unit-monitor configureren](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

Als u meer wilt weten over de status criteria, hebt u kennis artikelen opgenomen die u helpen bij het identificeren van problemen, oorzaken en oplossingen. Selecteer **informatie weer geven** op de pagina om het gerelateerde kennis artikel te bekijken.

Zie [Azure monitor Health Knowledge-documentatie](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)voor meer informatie over de kennis artikelen die deel uitmaken van Azure monitor voor VM's status.

### <a name="state-changes"></a>Status wijzigingen

De kolom uiterst rechts van de pagina **status diagnostiek** is **status wijzigingen**. Deze kolom bevat alle status wijzigingen die zijn gekoppeld aan de status criteria die zijn geselecteerd in de sectie **status criteria** , of de status wijziging van de virtuele machine als een virtuele machine is geselecteerd in de kolom **onderdeel model** of **status criteria** van de tabel.

![Voor beeld van status wijzigingen die worden weer gegeven in status diagnostiek](./media/vminsights-health/health-diagnostics-page-statechanges.png)

In de volgende sectie ziet u de status-en de bijbehorende tijd. Deze informatie bevat de meest recente status boven aan de kolom.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>Koppeling van het onderdeel model, de status criteria en de kolom Status wijziging

De drie kolommen zijn onderling gekoppeld. Wanneer u een instantie in de kolom **component model** selecteert, wordt de kolom **status criteria** gefilterd op die onderdeel weergave. De kolom **status wijzigingen** wordt dienovereenkomstig bijgewerkt op basis van de geselecteerde status criteria.

![Voor beeld van het selecteren van een bewaakt exemplaar en resultaten](./media/vminsights-health/health-diagnostics-vm-example-01.png)

Als u bijvoorbeeld *schijf-1 D:* in de lijst onder **onderdeel model**, **status criteria** filters naar *schijf-1d:* en **status wijzigingen** ziet, wordt de status wijziging weer gegeven op basis van de beschik baarheid van *schijf-1 D:* .

Als u een bijgewerkte status wilt zien, kunt u de pagina status diagnostiek vernieuwen door de koppeling **vernieuwen** te selecteren. Als er een update is voor de status van het status criterium op basis van het vooraf gedefinieerde polling-interval, kunt u met deze taak voor komen dat er wordt gewacht en de meest recente status weer spie gelen. De **status** van de status criteria is een filter waarmee u de resultaten kunt bereiken op basis van de geselecteerde status: in orde, waarschuwing, kritiek, onbekend en alle. De **laatst bijgewerkte** tijd in de rechter bovenhoek vertegenwoordigt de laatste keer dat de pagina status diagnostiek is vernieuwd.

## <a name="alerts"></a>Waarschuwingen

Azure Monitor voor VM's status kan worden geïntegreerd met [Azure-waarschuwingen](../../azure-monitor/platform/alerts-overview.md). Er wordt een waarschuwing gegeven wanneer vooraf gedefinieerde criteria, wanneer gedetecteerd, worden gewijzigd van de status in orde in een slechte staat. Waarschuwingen worden gecategoriseerd op Ernst, van Ernst 0 tot en met Ernst 4, met Ernst 0 als het hoogste niveau.

Waarschuwingen zijn niet gekoppeld aan een actie groep om u te waarschuwen wanneer de waarschuwing is geactiveerd. Een gebruiker met de rol van eigenaar bij het abonnements bereik moet meldingen configureren door de stappen in de sectie [waarschuwingen configureren](#configure-alerts) te volgen.

Het totale aantal VM-status waarschuwingen dat is gecategoriseerd op ernst is beschikbaar op het **status** dashboard in het gedeelte **waarschuwingen** . Wanneer u het totaal aantal waarschuwingen of het nummer selecteert dat overeenkomt met een Ernst niveau, wordt de pagina **waarschuwingen** geopend en worden alle waarschuwingen weer gegeven die overeenkomen met uw selectie.

Als u bijvoorbeeld de rij selecteert die overeenkomt met **Ernst niveau 1**, ziet u de volgende weer gave:

![Voor beeld van alle waarschuwingen voor Ernst niveau 1](./media/vminsights-health/vminsights-sev1-alerts-01.png)

De pagina **alle waarschuwingen** heeft geen bereik om alleen waarschuwingen weer te geven die overeenkomen met uw selectie. Het wordt ook gefilterd op **resource type** om alleen status waarschuwingen weer te geven die zijn gegenereerd door een VM-resource. Deze indeling wordt weer gegeven in de lijst met waarschuwingen, onder de **doel bron**van de kolom, waar de virtuele machine van Azure wordt weer gegeven met de verhoogde waarschuwing wanneer aan een onjuiste voor waarde is voldaan.

Waarschuwingen van andere resource typen of-services zijn niet bedoeld om te worden opgenomen in deze weer gave. Deze waarschuwingen omvatten logboek waarschuwingen, die zijn gebaseerd op logboek query's of metrische waarschuwingen die u normaal gesp roken van de standaard pagina voor het Azure Monitor [alle waarschuwingen](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) weer geven.

U kunt deze weer gave filteren door waarden te selecteren in de vervolg keuzelijsten boven aan de pagina.

|Kolom |Beschrijving |
|-------|------------|
|Abonnement |Selecteer een Azure-abonnement. Alleen waarschuwingen in het geselecteerde abonnement zijn opgenomen in de weer gave. |
|Resourcegroep |Selecteer één resource groep. In de weer gave zijn alleen waarschuwingen met doelen in de geselecteerde resource groep opgenomen. |
|Resourcetype |Selecteer een of meer resource typen. Standaard worden alleen waarschuwingen van virtuele doel **machines** geselecteerd en opgenomen in deze weer gave. Deze kolom is alleen beschikbaar nadat een resource groep is opgegeven. |
|Bron |Selecteer een resource. De weer gave bevat alleen waarschuwingen met die resource als doel. Deze kolom is alleen beschikbaar nadat een resource type is opgegeven. |
|Ernst |Selecteer een ernst van de waarschuwing of selecteer **Alles** om waarschuwingen van alle ernst op te neemt. |
|Bewakings voorwaarde |Selecteer een monitor voorwaarde om waarschuwingen te filteren als deze zijn geactiveerd of opgelost door het systeem als de voor waarde niet langer actief is. U kunt ook **Alles** selecteren om waarschuwingen van alle voor waarden op te stellen. |
|Waarschuwings status |Selecteer een waarschuwings status, **Nieuw**, **bevestigen**, **gesloten**of **Alles** om waarschuwingen van alle statussen op te genomen. |
|Service bewaken |Selecteer een service of selecteer **Alles** om alle services op te laten gebruiken. Alleen waarschuwingen van VM Insights worden ondersteund voor deze functie.|
|Tijdsbereik| Alleen waarschuwingen die binnen het geselecteerde tijd venster worden geactiveerd, worden in de weer gave opgenomen. Ondersteunde waarden zijn het afgelopen uur, de afgelopen 24 uur, de afgelopen 7 dagen en de afgelopen 30 dagen. |

Wanneer u een waarschuwing selecteert, wordt de detail pagina van de **waarschuwing** weer gegeven. Deze pagina bevat details van de waarschuwing en stelt u in staat om de status te wijzigen.

Zie [waarschuwingen maken, weer geven en beheren met Azure monitor](../../azure-monitor/platform/alerts-metric.md)voor meer informatie over het beheren van waarschuwingen.

>[!NOTE]
>Het maken van nieuwe waarschuwingen op basis van status criteria of het wijzigen van bestaande waarschuwings regels voor de status in Azure Monitor vanuit de portal wordt momenteel niet ondersteund.


![Deel venster waarschuwings Details voor een geselecteerde waarschuwing](./media/vminsights-health/alert-details-pane-01.png)

U kunt de status van een waarschuwing wijzigen voor een of meer waarschuwingen door ze te selecteren en vervolgens **status wijzigen** te selecteren op de pagina **alle waarschuwingen** in de linkerbovenhoek. Selecteer een van de statussen in het deel venster **waarschuwings status wijzigen** , voeg een beschrijving van de wijziging in het veld **Opmerking** toe en selecteer **OK** om uw wijzigingen door te voeren. Wanneer de gegevens worden geverifieerd en de wijzigingen worden toegepast, houdt u de voortgang bij **meldingen** in het menu bij.

### <a name="configure-alerts"></a>Waarschuwingen configureren
U kunt bepaalde waarschuwingen voor waarschuwings beheer niet beheren vanuit het Azure Portal. Deze taken moeten worden uitgevoerd met behulp van de [Azure Monitor rest API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Specifiek:

- Inschakelen of uitschakelen van een waarschuwing voor status criteria
- Meldingen voor status criteria instellen

In elk voor beeld wordt [ARMClient](https://github.com/projectkudu/armclient) gebruikt op uw Windows-computer. Zie [using ARMClient](../platform/rest-api-walkthrough.md#use-armclient)als u niet bekend bent met deze methode.

#### <a name="enable-or-disable-an-alert-rule"></a>Een waarschuwings regel in-of uitschakelen

Als u een waarschuwing wilt in-of uitschakelen voor specifieke status criteria, moet de eigenschap **alertGeneration** worden gewijzigd met de waarde **uitgeschakeld** of **ingeschakeld**.

Het volgende voor beeld laat zien hoe u een query kunt uitvoeren op die waarde voor de criteria **LogicalDisk\Avg schijf seconden per overdracht**om de *monitorId* te identificeren.

1. Typ **armclient. exe-aanmelding**in een Terminal venster. Hiermee wordt u gevraagd u aan te melden bij Azure.

2. Voer de volgende opdracht in om alle status criteria op te halen die actief zijn op een specifieke virtuele machine en de waarde voor de eigenschap *monitorId* te identificeren:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    In het volgende voor beeld ziet u de uitvoer van de opdracht *ARMCLIENT Get* . Noteer de waarde van *MonitorId*. Deze waarde is vereist voor de volgende stap, waarbij we de ID van de status criteria moeten opgeven en de bijbehorende eigenschap moeten wijzigen om een waarschuwing te maken.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. Voer de volgende opdracht in om de eigenschap *alertGeneration* te wijzigen:

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. Voer de GET-opdracht in die wordt gebruikt in stap 2 om te controleren of de waarde van de eigenschap is ingesteld op **uitgeschakeld**.

#### <a name="associate-an-action-group-with-health-criteria"></a>Een actie groep koppelen aan de status criteria

Azure Monitor voor VM's status ondersteunt SMS-en e-mail meldingen wanneer waarschuwingen worden gegenereerd op basis van slechte status criteria. Als u meldingen wilt configureren, noteert u de naam van de geconfigureerde actie groep om SMS-of e-mail meldingen te verzenden.

>[!NOTE]
>Deze actie moet worden uitgevoerd voor elke bewaakte virtuele machine waarvoor u een melding wilt ontvangen. Deze is niet van toepassing op alle virtuele machines in een resource groep.

1. Voer in een Terminal venster de *armclient. exe-aanmelding*in. Hiermee wordt u gevraagd u aan te melden bij Azure.

2. Voer de volgende opdracht in om een actie groep te koppelen aan waarschuwings regels:
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. Als u wilt controleren of de waarde van de eigenschap **actionGroupResourceIds** is bijgewerkt, voert u de volgende opdracht in:

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    De uitvoer moet er als volgt uitzien:
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-VM-prestaties weer geven](vminsights-performance.md)om beperkingen en algemene VM-prestaties te identificeren.

- Zie [Azure monitor voor VM's kaart weer geven](vminsights-maps.md)voor meer informatie over gedetecteerde toepassings afhankelijkheden.
