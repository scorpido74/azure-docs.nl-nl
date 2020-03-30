---
title: Overzicht van waarschuwingen en meldingsbewaking in Azure
description: Overzicht van waarschuwingen in Azure. Waarschuwingen, klassieke waarschuwingen en de interface voor waarschuwingen.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 01/28/2018
ms.openlocfilehash: 7ca77531ed3e1fae8ec297e430597452c7512aea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274786"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Overzicht van waarschuwingen in Microsoft Azure 

In dit artikel wordt beschreven wat waarschuwingen zijn, wat de voordelen ervan zijn en hoe u ermee aan de slag.  

## <a name="what-are-alerts-in-microsoft-azure"></a>Wat zijn waarschuwingen in Microsoft Azure?
Waarschuwingen stellen u proactief op de hoogte wanneer belangrijke voorwaarden worden gevonden in uw bewakingsgegevens. Hiermee u problemen identificeren en aanpakken voordat de gebruikers van uw systeem deze opmerken. 

In dit artikel wordt de uniforme waarschuwingservaring in Azure Monitor besproken, die waarschuwingen bevat die eerder zijn beheerd door Log Analytics en Application Insights. De [vorige waarschuwingservaring](alerts-classic.overview.md) en waarschuwingstypen worden *klassieke waarschuwingen*genoemd. U deze oudere ervaring en het oudere waarschuwingstype weergeven door **klassieke waarschuwingen** boven aan de waarschuwingspagina weergeven te selecteren. 

## <a name="overview"></a>Overzicht

Het onderstaande diagram geeft de stroom van waarschuwingen weer. 

![Diagram van waarschuwingsstroom](media/alerts-overview/Azure-Monitor-Alerts.svg)

Waarschuwingsregels worden gescheiden van waarschuwingen en de acties die worden uitgevoerd wanneer een waarschuwing wordt geactiveerd. De waarschuwingsregel legt het doel en de criteria voor waarschuwingvast. De waarschuwingsregel kan ingeschakeld of uitgeschakeld zijn. Waarschuwingen alleen brand wanneer ingeschakeld. 

De volgende kenmerken van een waarschuwingsregel zijn:

**Doelbron:** definieert het bereik en de signalen die beschikbaar zijn voor waarschuwingen. Een doel kan elke Azure-bron zijn. Voorbeelddoelen: een virtuele machine, een opslagaccount, een virtuele machineschaalset, een Log Analytics-werkruimte of een Application Insights-bron. Voor bepaalde resources (zoals virtuele machines) u meerdere resources opgeven als doel van de waarschuwingsregel.

**Signaal**: Uitgezonden door de doelbron. Signalen kunnen van de volgende typen zijn: metric, activity log, Application Insights en log.

**Criteria:** een combinatie van signaal en logica toegepast op een doelbron. Voorbeelden: 

- Percentage CPU-> 70%
- Serverresponstijd > 4 ms 
- Aantal resultaten van een logboekquery > 100

**Waarschuwingsnaam:** een specifieke naam voor de waarschuwingsregel die door de gebruiker is geconfigureerd.

**Waarschuwingsbeschrijving:** een beschrijving voor de waarschuwingsregel die door de gebruiker is geconfigureerd.

**Ernst**: de ernst van de waarschuwing nadat is voldaan aan de criteria die in de waarschuwingsregel zijn opgegeven. De ernst kan variëren van 0 tot 4.

- Sev 0 = Kritiek
- Sev 1 = Fout
- Sev 2 = Waarschuwing
- Sev 3 = Informatief
- Sev 4 = Verbose 

**Actie:** Een specifieke actie die wordt ondernomen wanneer de waarschuwing wordt geactiveerd. Zie [Actiegroepen voor](../../azure-monitor/platform/action-groups.md)meer informatie.

## <a name="what-you-can-alert-on"></a>Waar u op waarschuwen

U waarschuwen op metrische gegevens en logboeken, zoals beschreven in [het bewaken van gegevensbronnen.](../../azure-monitor/platform/data-sources.md) Deze omvatten, maar zijn niet beperkt tot:

- Metrische waarden
- Query's voor zoeken in logboeken
- Activiteitenlogboekgebeurtenissen
- Status van het onderliggende Azure-platform
- Tests voor de beschikbaarheid van een website

Voorheen beschikten Azure Monitor-statistieken, Toepassingsinzichten, Logboekanalyse en Servicestatus over afzonderlijke waarschuwingsmogelijkheden. Na verloop van tijd heeft Azure zowel de gebruikersinterface als de verschillende waarschuwingsmethoden verbeterd en gecombineerd. Deze consolidatie is nog in behandeling. Als gevolg hiervan zijn er nog enkele waarschuwingsmogelijkheden die nog niet in het nieuwe waarschuwingssysteem zijn.  

| **Monitorbron** | **Signaaltype**  | **Beschrijving** |
|-------------|----------------|-------------|
| Status van service | Activiteitenlogboek  | Wordt niet ondersteund. Zie [Meldingen van activiteitenlogboeken maken op servicemeldingen](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Tests voor webbeschikbaarheid | Wordt niet ondersteund. Zie [Waarschuwingen voor webtests](../../azure-monitor/app/monitor-web-app-availability.md). Beschikbaar voor elke website die is uitgerust om gegevens naar Application Insights te verzenden. Ontvang een melding wanneer de beschikbaarheid of responsiviteit van een website lager is dan verwacht. |

## <a name="manage-alerts"></a>Waarschuwingen beheren
U de status van een waarschuwing instellen om aan te geven waar deze zich in het oplossingsproces bevindt. Wanneer aan de criteria in de waarschuwingsregel is voldaan, wordt een waarschuwing gemaakt of geactiveerd en heeft deze de status *Nieuw*. U de status wijzigen wanneer u een waarschuwing erkent en deze sluit. Alle statuswijzigingen worden opgeslagen in de geschiedenis van de waarschuwing.

De volgende waarschuwingsstatussen worden ondersteund.

| Status | Beschrijving |
|:---|:---|
| Nieuw | Het probleem is net ontdekt en is nog niet beoordeeld. |
| Bevestigd | Een beheerder heeft de waarschuwing beoordeeld en is ermee aan de slag gegaan. |
| Gesloten | Het probleem is opgelost. Nadat een waarschuwing is gesloten, u deze opnieuw openen door deze te wijzigen in een andere status. |

*De waarschuwingsstatus* is anders en onafhankelijk van de status van de *monitor*. De waarschuwingsstatus wordt ingesteld door de gebruiker. De status van de monitor wordt door het systeem ingesteld. Wanneer een waarschuwing wordt geactiveerd, wordt de status van de monitor van de waarschuwing *geactiveerd*. Wanneer de onderliggende voorwaarde die ervoor zorgde dat de waarschuwing is geactiveerd, wordt gewist, wordt de toestand van de monitor *opgelost*. De waarschuwingsstatus wordt pas gewijzigd als de gebruiker deze wijzigt. Meer informatie over [het wijzigen van de status van uw waarschuwingen en slimme groepen.](https://aka.ms/managing-alert-smart-group-states)

## <a name="smart-groups"></a>Slimme groepen 

Slimme groepen zijn aggregaties van waarschuwingen op basis van machine learning-algoritmen, die kunnen helpen bij het verminderen van waarschuwingsgeluiden en hulp bij het oplossen van problemen. [Meer informatie over slimme groepen](https://aka.ms/smart-groups) en het beheren van uw slimme [groepen](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Ervaring met waarschuwingen 
De pagina Standaardwaarschuwingen biedt een overzicht van waarschuwingen die binnen een bepaald tijdsbereik worden gemaakt. Het toont de totale waarschuwingen voor elke ernst, met kolommen die het totale aantal waarschuwingen in elke status voor elke ernst identificeren. Selecteer een van de ernst om de pagina [Alle waarschuwingen te](#all-alerts-page) openen die op die ernst is gefilterd.

U ook [programmatisch de waarschuwingsinstanties opsommen die op uw abonnementen zijn gegenereerd met behulp van REST-API's.](#manage-your-alert-instances-programmatically)

> [!NOTE]
   >  U hebt alleen toegang tot waarschuwingen die in de afgelopen 30 dagen zijn gegenereerd.

Het toont geen klassieke waarschuwingen of houdt deze niet bij. U de abonnementen of filterparameters wijzigen om de pagina bij te werken. 

![Schermafbeelding van de pagina Waarschuwingen](media/alerts-overview/alerts-page.png)

U deze weergave filteren door waarden te selecteren in de vervolgkeuzemenu's boven aan de pagina.

| Kolom | Beschrijving |
|:---|:---|
| Abonnement | Selecteer de Azure-abonnementen waarvoor u de waarschuwingen wilt weergeven. U er optioneel voor kiezen om al uw abonnementen te selecteren. Alleen waarschuwingen waartoe u toegang hebt in de geselecteerde abonnementen zijn opgenomen in de weergave. |
| Resourcegroep | Selecteer één resourcegroep. Alleen waarschuwingen met doelen in de geselecteerde resourcegroep worden in de weergave opgenomen. |
| Tijdsbereik | Alleen waarschuwingen die binnen het geselecteerde tijdsbereik worden geactiveerd, worden in de weergave opgenomen. Ondersteunde waarden zijn het afgelopen uur, de afgelopen 24 uur, de afgelopen 7 dagen en de afgelopen 30 dagen. |

Selecteer de volgende waarden boven aan de pagina Waarschuwingen om een andere pagina te openen:

| Waarde | Beschrijving |
|:---|:---|
| Totaal aantal waarschuwingen | Het totale aantal waarschuwingen dat overeenkomt met de geselecteerde criteria. Selecteer deze waarde om de weergave Alle waarschuwingen zonder filter te openen. |
| Slimme groepen | Het totale aantal slimme groepen dat is gemaakt op basis van de waarschuwingen die overeenkomen met de geselecteerde criteria. Selecteer deze waarde om de lijst met slimme groepen te openen in de weergave Alle waarschuwingen.
| Totale waarschuwingsregels | Het totale aantal waarschuwingsregels in de geselecteerde abonnements- en resourcegroep. Selecteer deze waarde om de weergave Regels te openen die is gefilterd op de geselecteerde abonnements- en resourcegroep.


## <a name="manage-alert-rules"></a>Waarschuwingsregels beheren
Als u de pagina **Regels wilt** weergeven, selecteert **u Waarschuwingsregels beheren**. De pagina Regels is één plaats voor het beheren van alle waarschuwingsregels voor uw Azure-abonnementen. Het bevat alle waarschuwingsregels en kan worden gesorteerd op basis van doelbronnen, resourcegroepen, regelnaam of status. U ook waarschuwingsregels op deze pagina bewerken, in- of uitschakelen.  

 ![Schermafbeelding van de pagina Regels](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Een waarschuwingsregel maken
U waarschuwingen op een consistente manier maken, ongeacht de bewakingsservice of het signaaltype. Alle geactiveerde waarschuwingen en gerelateerde details zijn beschikbaar op één pagina.
 
U als u een nieuwe waarschuwingsregel maken:
1. Kies het _doel_ voor de waarschuwing.
1. Selecteer het _signaal_ uit de beschikbare signalen voor het doel.
1. Geef de _logica_ op die moet worden toegepast op gegevens van het signaal.
 
Dit vereenvoudigde ontwerpproces vereist niet langer dat u de bewakingsbron of -signalen kent die worden ondersteund voordat u een Azure-bron selecteert. De lijst met beschikbare signalen wordt automatisch gefilterd op basis van de doelbron die u selecteert. Ook op basis van dat doel wordt u automatisch door het definiëren van de logica van de waarschuwingsregel geleid.  

U meer informatie krijgen over het maken van waarschuwingsregels in [Het maken, weergeven en beheren van waarschuwingen met Azure Monitor.](../../azure-monitor/platform/alerts-metric.md)

Waarschuwingen zijn beschikbaar in verschillende Azure-bewakingsservices. Zie [Azure-toepassingen en -bronnen controleren](../../azure-monitor/overview.md)voor informatie over het gebruik van elk van deze services. 


## <a name="all-alerts-page"></a>Pagina Alle waarschuwingen 
Als u de pagina **Alle waarschuwingen wilt bekijken,** selecteert u **Totaalaantal waarschuwingen**. Hier u een lijst met waarschuwingen bekijken die binnen de geselecteerde tijd zijn gemaakt. U een lijst met afzonderlijke waarschuwingen bekijken of een lijst met de slimme groepen die de waarschuwingen bevatten. Selecteer de banner boven aan de pagina om tussen weergaven te schakelen.

![Schermafbeelding van de pagina Alle waarschuwingen](media/alerts-overview/all-alerts-page.png)

U de weergave filteren door de volgende waarden te selecteren in de vervolgkeuzemenu's boven aan de pagina:

| Kolom | Beschrijving |
|:---|:---|
| Abonnement | Selecteer de Azure-abonnementen waarvoor u de waarschuwingen wilt weergeven. U er optioneel voor kiezen om al uw abonnementen te selecteren. Alleen waarschuwingen waartoe u toegang hebt in de geselecteerde abonnementen zijn opgenomen in de weergave. |
| Resourcegroep | Selecteer één resourcegroep. Alleen waarschuwingen met doelen in de geselecteerde resourcegroep worden in de weergave opgenomen. |
| Resourcetype | Selecteer een of meer resourcetypen. Alleen waarschuwingen met doelen van het geselecteerde type worden in de weergave opgenomen. Deze kolom is alleen beschikbaar nadat een resourcegroep is opgegeven. |
| Resource | Selecteer een resource. Alleen waarschuwingen met die resource als doel worden in de weergave opgenomen. Deze kolom is alleen beschikbaar nadat een resourcetype is opgegeven. |
| Severity | Selecteer een waarschuwingof selecteer **Alles** om waarschuwingen van alle ernst op te nemen. |
| Status van monitor | Selecteer een monitorvoorwaarde of selecteer **Alles** om waarschuwingen van alle voorwaarden op te nemen. |
| Waarschuwingsstatus | Selecteer een waarschuwingsstatus of selecteer **Alles** om waarschuwingen van alle statussen op te nemen. |
| Monitorservice | Selecteer een service of selecteer **Alles** om alle services op te nemen. Alleen waarschuwingen die zijn gemaakt door regels die service als doel gebruiken, worden opgenomen. |
| Tijdsbereik | Alleen waarschuwingen die binnen het geselecteerde tijdsbereik worden geactiveerd, worden in de weergave opgenomen. Ondersteunde waarden zijn het afgelopen uur, de afgelopen 24 uur, de afgelopen 7 dagen en de afgelopen 30 dagen. |

Selecteer **Kolommen** boven aan de pagina om te selecteren welke kolommen u wilt weergeven. 

## <a name="alert-details-page"></a>Pagina Waarschuwingsgegevens
Wanneer u een waarschuwing selecteert, geeft deze pagina details van de waarschuwing en u de status ervan wijzigen.

![Schermafbeelding van pagina Waarschuwingsdetails](media/alerts-overview/alert-detail2.png)

De pagina Details van waarschuwing bevat de volgende secties:

| Sectie | Beschrijving |
|:---|:---|
| Samenvatting | Hiermee worden de eigenschappen en andere belangrijke informatie over de waarschuwing weergegeven. |
| Geschiedenis | Geeft een overzicht van elke actie die door de waarschuwing wordt uitgevoerd en eventuele wijzigingen in de waarschuwing. Momenteel beperkt tot statuswijzigingen. |
| Diagnostiek | Informatie over de slimme groep waarin de waarschuwing is opgenomen. Het *aantal waarschuwingen* verwijst naar het aantal waarschuwingen dat is opgenomen in de slimme groep. Bevat andere waarschuwingen in dezelfde slimme groep die in de afgelopen 30 dagen zijn gemaakt, ongeacht het tijdsfilter op de pagina met waarschuwingen. Selecteer een waarschuwing om de details weer te geven. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>RBAC (Role-based access control) voor uw waarschuwingsinstanties

Het verbruik en het beheer van waarschuwingsinstanties vereist dat de gebruiker de ingebouwde RBAC-rollen heeft van het bewaken van [de bijdrager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) of het bewaken van [de lezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader). Deze rollen worden ondersteund op elk Azure Resource Manager-bereik, van het abonnementsniveau tot gedetailleerde toewijzingen op resourceniveau. Als een gebruiker bijvoorbeeld alleen toegang tot `ContosoVM1`de inzender voor virtuele machines `ContosoVM1`heeft, kan die gebruiker alleen waarschuwingen gebruiken die op .

## <a name="manage-your-alert-instances-programmatically"></a>Uw waarschuwingsinstanties programmatisch beheren

U programmatisch query's opvragen voor waarschuwingen die zijn gegenereerd tegen uw abonnement. Dit kan zijn om aangepaste weergaven buiten de Azure-portal te maken of om uw waarschuwingen te analyseren om patronen en trends te identificeren.

U vragen stellen naar waarschuwingen die zijn gegenereerd tegen uw abonnementen met behulp van de [API voor waarschuwingsbeheer](https://aka.ms/alert-management-api) of met behulp van de [Azure Resource Graph](../../governance/resource-graph/overview.md) en de REST API voor [resources.](/rest/api/azureresourcegraph/resourcegraph(2019-04-01)/resources/resources)

Met de RESOURCE Graph REST API for Resources u op schaal vragen naar waarschuwingsinstanties. Dit wordt aanbevolen wanneer u waarschuwingen moet beheren die voor veel abonnementen zijn gegenereerd. 

Met de volgende voorbeeldaanvraag voor de RESOURCE Graph REST API wordt het aantal waarschuwingen binnen één abonnement geretourneerd:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "AlertsManagementResources | where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()"
}
```

U ook het resultaat van deze resourcegrafiekquery zien in de portal met Azure Resource Graph Explorer: [portal.azure.com](https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/AlertsManagementResources%20%7C%20where%20type%20%3D~%20%27Microsoft.AlertsManagement%2Falerts%27%20%7C%20summarize%20count())

U de waarschuwingen voor hun [essentiële](alerts-common-schema-definitions.md#essentials) velden opvragen.

Gebruik de [API Voor het beheer van waarschuwingen voor](https://aka.ms/alert-management-api) meer informatie over specifieke waarschuwingen, waaronder de [contextvelden voor waarschuwingen.](alerts-common-schema-definitions.md#alert-context)

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over slimme groepen](https://aka.ms/smart-groups)
- [Meer informatie over actiegroepen](../../azure-monitor/platform/action-groups.md)
- [Uw waarschuwingsexemplaren beheren in Azure](https://aka.ms/managing-alert-instances)
- [Slimme groepen beheren](https://aka.ms/managing-smart-groups)
- [Meer informatie over de prijzen voor Azure-waarschuwingen](https://azure.microsoft.com/pricing/details/monitor/)






