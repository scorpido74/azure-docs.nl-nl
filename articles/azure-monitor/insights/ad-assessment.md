---
title: Uw Active Directory-omgeving optimaliseren met Azure Monitor | Microsoft Documenten
description: U de Active Directory Health Check-oplossing gebruiken om het risico en de status van uw omgevingen regelmatig te beoordelen.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: 57c474c8391168702154b71e0c454253ab921dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667224"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>De Active Directory-omgeving optimaliseren met behulp van Active Directory-statuscontrole in Azure Monitor

![Ad-statuscontrolesymbool](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

U de Active Directory Health Check-oplossing gebruiken om het risico en de status van uw serveromgevingen regelmatig te beoordelen. In dit artikel u de oplossing installeren en gebruiken, zodat u corrigerende maatregelen nemen voor mogelijke problemen.

Deze oplossing biedt een geprioritteerde lijst met aanbevelingen die specifiek zijn voor uw geïmplementeerde serverinfrastructuur. De aanbevelingen zijn gecategoriseerd in vier focusgebieden, die u helpen snel inzicht te krijgen in het risico en actie te ondernemen.

De aanbevelingen zijn gebaseerd op de kennis en ervaring die Microsoft-engineers hebben opgedaan met duizenden klantbezoeken. Elke aanbeveling geeft richtlijnen over waarom een probleem voor u van belang kan zijn en hoe u de voorgestelde wijzigingen implementeren.

U focusgebieden kiezen die het belangrijkst zijn voor uw organisatie en uw voortgang bijhouden in de richting van het uitvoeren van een risicovrije en gezonde omgeving.

Nadat u de oplossing hebt toegevoegd en een controle is voltooid, wordt beknopte informatie voor focusgebieden weergegeven in het dashboard **AD-statuscontrole** voor de infrastructuur in uw omgeving. In de volgende secties wordt beschreven hoe u de informatie in het dashboard **AD Health Check** gebruikt, waar u aanbevolen acties voor uw Active Directory-serverinfrastructuur bekijken en vervolgens uitvoeren.  

![afbeelding van de tegel AD-statuscontrole](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![afbeelding van het dashboard AD Health Check](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Vereisten

* De Active Directory Health Check-oplossing vereist een ondersteunde versie van .NET Framework 4.6.2 of hoger die is geïnstalleerd op elke computer waarop de Log Analytics-agent voor Windows is geïnstalleerd (ook wel de Microsoft Monitoring Agent (MMA)) is geïnstalleerd.  De agent wordt gebruikt door System Center 2016 - Operations Manager, Operations Manager 2012 R2 en Azure Monitor.
* De oplossing ondersteunt domeincontrollers met Windows Server 2008 en 2008 R2, Windows Server 2012 en 2012 R2 en Windows Server 2016.
* Een werkruimte log-analyse om de Active Directory Health Check-oplossing toe te voegen vanuit de Azure-marktplaats in de Azure-portal. Er is geen extra configuratie vereist.

  > [!NOTE]
  > Nadat u de oplossing hebt toegevoegd, wordt het AdvisorAssessment.exe-bestand toegevoegd aan servers met agents. Configuratiegegevens worden gelezen en vervolgens naar Azure Monitor in de cloud verzonden voor verwerking. Logica wordt toegepast op de ontvangen gegevens en de gegevens worden vastgelegd door de cloudservice.
  >
  >

Om de statuscontrole uit te voeren ten opzichte van uw domeincontrollers die lid zijn van het domein dat moet worden geëvalueerd, vereist elke domeincontroller in dat domein een agent en connectiviteit met Azure Monitor met behulp van een van de volgende ondersteunde methoden:

1. Installeer de [agent Log Analytics voor Windows](../../azure-monitor/platform/agent-windows.md) als de domeincontroller nog niet is gecontroleerd door System Center 2016 - Operations Manager of Operations Manager 2012 R2.
2. Als het wordt gecontroleerd met System Center 2016 - Operations Manager of Operations Manager 2012 R2 en de beheergroep niet is geïntegreerd met Azure Monitor, kan de domeincontroller worden gekoppeld aan Azure Monitor om gegevens te verzamelen en door te sturen naar de service en nog steeds worden gecontroleerd door Operations Manager.  
3. Als anders uw beheergroep Operations Manager is geïntegreerd met de service, moet u de domeincontrollers voor het verzamelen van gegevens toevoegen door de service die de stappen volgt onder [door agent beheerde computers toevoegen](../../azure-monitor/platform/om-agents.md#connecting-operations-manager-to-azure-monitor) nadat u de oplossing in uw werkruimte hebt ingeschakeld.  

De agent op uw domeincontroller die rapporteert aan een beheergroep operations manager, gegevens verzamelt, doorstuurt naar de toegewezen beheerserver en vervolgens rechtstreeks van een beheerserver naar Azure Monitor wordt verzonden.  De gegevens worden niet naar de Operations Manager-databases geschreven.  

## <a name="active-directory-health-check-data-collection-details"></a>Details voor gegevensverzameling van Active Directory-statuscontrole

Active Directory Health Check verzamelt gegevens uit de volgende bronnen met behulp van de agent die u hebt ingeschakeld:

- Register
- LDAP
- .NET Framework
- Gebeurtenislogboek
- ACTIVE Directory Service-interfaces (ADSI)
- Windows PowerShell
- Bestandsgegevens
- Windows Management Instrumentation (WMI)
- API voor DCDIAG-gereedschap
- NTFRS (File Replication Service) API
- Aangepaste C#-code

Gegevens worden verzameld op de domeincontroller en elke zeven dagen doorgestuurd naar Azure Monitor.  

## <a name="understanding-how-recommendations-are-prioritized"></a>Inzicht in hoe de prioriteit van aanbevelingen wordt vastgesteld

Elke aanbeveling wordt gegeven een wegingswaarde die het relatieve belang van de aanbeveling identificeert. Alleen de 10 belangrijkste aanbevelingen worden getoond.

### <a name="how-weights-are-calculated"></a>Hoe gewicht wordt berekend

Wegingen zijn geaggregeerde waarden op basis van drie belangrijke factoren:

* De *kans* dat een probleem is geïdentificeerd, veroorzaakt problemen. Een hogere waarschijnlijkheid komt overeen met een grotere algemene score voor de aanbeveling.
* De *impact* van het probleem op uw organisatie als dit een probleem veroorzaakt. Een hogere impact komt overeen met een grotere algemene score voor de aanbeveling.
* De *inspanning* die nodig is om de aanbeveling uit te voeren. Een hogere inspanning komt overeen met een kleinere algemene score voor de aanbeveling.

De weging voor elke aanbeveling wordt uitgedrukt als een percentage van de totale score die beschikbaar is voor elk aandachtsgebied. Als een aanbeveling in het focusgebied Beveiliging en Naleving bijvoorbeeld een score van 5% heeft, verhoogt het implementeren van die aanbeveling uw algehele beveiligings- en nalevingsscore met 5%.

### <a name="focus-areas"></a>Aandachtsgebieden

**Beveiliging en naleving** - Dit aandachtsgebied bevat aanbevelingen voor potentiële bedreigingen en schendingen van de beveiliging, bedrijfsbeleid en technische, wettelijke en regelgevende vereisten.

**Beschikbaarheid en bedrijfscontinuïteit** - Dit aandachtsgebied bevat aanbevelingen voor servicebeschikbaarheid, tolerantie van uw infrastructuur en bedrijfsbescherming.

**Prestaties en schaalbaarheid** - Dit focusgebied bevat aanbevelingen om de IT-infrastructuur van uw organisatie te helpen groeien, ervoor te zorgen dat uw IT-omgeving voldoet aan de huidige prestatievereisten en in staat is om te reageren op veranderende infrastructuurbehoeften.

**Upgraden, migratie en implementatie** : in dit focusgebied worden aanbevelingen weergegeven waarmee u Active Directory upgraden, migreren en implementeren in uw bestaande infrastructuur.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Moet u proberen een score van 100% te halen voor elk focusgebied?

Niet per se. De aanbevelingen zijn gebaseerd op de kennis en ervaringen die Microsoft-technici hebben opgedaan bij duizenden klantbezoeken. Er zijn echter geen twee serverinfrastructuren hetzelfde en specifieke aanbevelingen kunnen min of meer relevant voor u zijn. Sommige beveiligingsaanbevelingen zijn bijvoorbeeld minder relevant als uw virtuele machines niet worden blootgesteld aan internet. Sommige beschikbaarheidsaanbevelingen zijn mogelijk minder relevant voor services die ad hoc-gegevensverzameling en -rapportage met lage prioriteit bieden. Zaken die belangrijk zijn voor een volwassen bedrijf kunnen minder belangrijk zijn voor een start-up. U bepalen welke aandachtsgebieden uw prioriteiten zijn en vervolgens kijken hoe uw scores in de loop van de tijd veranderen.

Elke aanbeveling bevat richtlijnen over waarom het belangrijk is. U moet deze richtlijnen gebruiken om te beoordelen of de implementatie van de aanbeveling geschikt is voor u, gezien de aard van uw IT-services en de zakelijke behoeften van uw organisatie.

## <a name="use-health-check-focus-area-recommendations"></a>Aanbevelingen voor het focusgebied van de statuscontrole gebruiken

Nadat deze is geïnstalleerd, u de samenvatting van aanbevelingen bekijken met de tegel Statuscontrole op de oplossingspagina in de Azure-portal.

Bekijk de samengevatte nalevingsbeoordelingen voor uw infrastructuur en zoom vervolgens in op aanbevelingen.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>Aanbevelingen voor een aandachtsgebied bekijken en corrigerende maatregelen nemen

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Klik **op** de pagina Overzicht op de tegel **Active Directory Health Check.**

2. Bekijk op de pagina **Statuscontrole** de overzichtsinformatie in een van de focusgebiedbladen en klik vervolgens op een pagina om aanbevelingen voor dat focusgebied weer te geven.

3. Op een van de pagina's van het focusgebied u de geprioritteerde aanbevelingen voor uw omgeving bekijken. Klik op een aanbeveling onder **Getroffen objecten** om meer informatie te bekijken over waarom de aanbeveling wordt gedaan.

    ![afbeelding van aanbevelingen voor health check](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. U corrigerende maatregelen nemen die worden voorgesteld in **voorgestelde acties**. Wanneer het item is aangepakt, worden latere beoordelingen opgenomen waarin aanbevolen acties zijn uitgevoerd en wordt uw nalevingsscore verhoogd. Gecorrigeerde items worden weergegeven als **doorgegeven objecten**.

## <a name="ignore-recommendations"></a>Aanbevelingen negeren

Als u aanbevelingen hebt die u wilt negeren, u een tekstbestand maken dat Azure Monitor gebruikt om te voorkomen dat aanbevelingen in uw beoordelingsresultaten worden weergegeven.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>Om aanbevelingen te identificeren die u negeert

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

Gebruik de volgende query om aanbevelingen op te sommen die zijn mislukt voor computers in uw omgeving.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Hier is een screenshot met de logboekquery:<

![mislukte aanbevelingen](media/ad-assessment/ad-failed-recommendations.png)

Kies aanbevelingen die u wilt negeren. U gebruikt de waarden voor RecommendationId in de volgende procedure.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>Een tekstbestand IgnoreRecommendations.txt maken en gebruiken

1. Maak een bestand met de naam IgnoreRecommendations.txt.

2. Plak of typ elke RecommendationId voor elke aanbeveling die u wilt dat Azure Monitor op een afzonderlijke regel negeert en sla het bestand op en sluit het.

3. Plaats het bestand in de volgende map op elke computer waar u wilt dat Azure Monitor aanbevelingen negeert.

   * Op computers met de Microsoft Monitoring Agent (rechtstreeks verbonden of via Operations Manager) - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * Op de Beheerserver Operations Manager 2012 R2 - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * Op de beheerserver operations manager 2016 - *SystemDrive*:\Program Files\Microsoft System Center 2016\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>Controleren of aanbevelingen worden genegeerd

Nadat de volgende geplande statuscontrole is uitgevoerd, worden standaard om de zeven dagen de opgegeven aanbevelingen gemarkeerd *als Genegeerd* en worden ze niet weergegeven op het dashboard.

1. U de volgende logboekquery's gebruiken om alle genegeerde aanbevelingen weer te geven.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Als u later besluit dat u genegeerde aanbevelingen wilt zien, verwijdert u alle ignorerecommendations.txt-bestanden of verwijdert u aanbevelingen ervan.

## <a name="ad-health-check-solutions-faq"></a>Veelgestelde vragen over oplossingen van AD-statuscontrole

*Welke controles worden uitgevoerd door de AD Assessment-oplossing?*

* In de volgende query ziet u een beschrijving van alle controles die momenteel worden uitgevoerd:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
De resultaten kunnen vervolgens worden geëxporteerd naar Excel voor verdere beoordeling.

*Hoe vaak wordt een health check uitgevoerd?*

* De cheque loopt om de zeven dagen.

*Is er een manier om te configureren hoe vaak de statuscontrole wordt uitgevoerd?*

* Momenteel niet.

*Als een andere server voor wordt ontdekt nadat ik een health check oplossing heb toegevoegd, zal het worden gecontroleerd*

* Ja, zodra het wordt ontdekt wordt gecontroleerd vanaf dat dan, om de zeven dagen.

*Wanneer wordt een server buiten gebruik gesteld als deze buiten gebruik wordt gesteld?*

* Als een server gedurende 3 weken geen gegevens indient, wordt deze verwijderd.

*Wat is de naam van het proces dat de gegevensverzameling doet?*

* AdvisorAssessment.exe

*Hoe lang duurt het voordat gegevens worden verzameld?*

* De werkelijke gegevensverzameling op de server duurt ongeveer 1 uur. Het kan langer duren op servers met een groot aantal Active Directory-servers.

*Is er een manier om te configureren wanneer gegevens worden verzameld?*

* Momenteel niet.

*Waarom alleen de top 10 aanbevelingen weergeven?*

* In plaats van u een uitputtende overweldigende lijst met taken te geven, raden we u aan u eerst te concentreren op het aanpakken van de geprioritteerde aanbevelingen. Nadat u ze hebt aangepakt, worden aanvullende aanbevelingen beschikbaar. Als u de gedetailleerde lijst liever ziet, u alle aanbevelingen bekijken met behulp van een logboekquery.

*Is er een manier om een aanbeveling te negeren?*

* Ja, zie Aanbevelingen hierboven [negeren.](#ignore-recommendations)

## <a name="next-steps"></a>Volgende stappen

Gebruik [azure monitor-logboekquery's](../log-query/log-query-overview.md) voor meer informatie over het analyseren van gedetailleerde gegevens en aanbevelingen voor AD-statuscontrole.
