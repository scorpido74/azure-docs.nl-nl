---
title: Overzicht van waarschuwings-en meldings bewaking in azure
description: Overzicht van waarschuwingen in Azure. Waarschuwingen, klassieke waarschuwingen, de interface waarschuwingen.
author: rboucher
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/28/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 67318fee540195fc913739d78e80649100c54e70
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034820"
---
# <a name="overview-of-alerts-in-microsoft-azure"></a>Overzicht van waarschuwingen in Microsoft Azure 

In dit artikel wordt beschreven welke waarschuwingen, hun voor delen en hoe u ze kunt gaan gebruiken.  




## <a name="what-are-alerts-in-microsoft-azure"></a>Wat zijn waarschuwingen in Microsoft Azure?
Waarschuwingen geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw bewakings gegevens. Hiermee kunt u problemen identificeren en verhelpen voordat de gebruikers van uw systeem ze merken. 

In dit artikel wordt de Unified alert-ervaring in Azure Monitor beschreven, die nu waarschuwingen bevat die door Log Analytics en Application Insights worden beheerd. De [vorige waarschuwings ervaring](alerts-classic.overview.md) en waarschuwings typen worden **klassieke waarschuwingen**genoemd. U kunt deze oudere ervaring en een ouder waarschuwings type bekijken door te klikken op **klassieke waarschuwingen weer geven** boven aan de pagina waarschuwing. 

## <a name="overview"></a>Overzicht

In het onderstaande diagram wordt de stroom van waarschuwingen aangegeven. 

![Waarschuwings stroom](media/alerts-overview/Azure-Monitor-Alerts.svg)

Waarschuwings regels worden gescheiden van waarschuwingen en de acties die worden uitgevoerd wanneer een waarschuwing wordt geactiveerd. 

**Waarschuwings regel** : de waarschuwings regel legt het doel en de criteria voor waarschuwingen vast. De waarschuwings regel kan een ingeschakelde of uitgeschakelde status hebben. Waarschuwingen worden alleen geactiveerd wanneer deze functie is ingeschakeld. 

De belangrijkste kenmerken van een waarschuwings regel zijn:

**Doel resource** : Hiermee definieert u het bereik en de signalen die beschikbaar zijn voor waarschuwingen. Een doel kan elke Azure-resource zijn. Voor beeld van doelen: een virtuele machine, een opslag account, een virtuele-machine schaalset, een Log Analytics-werk ruimte of een Application Insights-resource. Voor bepaalde resources (zoals Virtual Machines) kunt u meerdere resources opgeven als het doel van de waarschuwings regel.

**Signaal** signalen worden verzonden door de doel resource en kunnen van verschillende typen zijn. Metriek, activiteiten logboek, Application Insights en logboek.

**Criteria** : criteria is een combi natie van signaal en logica die worden toegepast op een doel bron. Voorbeelden: 
   - Percentage CPU > 70%
   - Reactie tijd van server > 4 MS 
   - Resultaat aantal van een logboek query > 100

**Naam van waarschuwing** : een specifieke naam voor de waarschuwings regel die door de gebruiker is geconfigureerd

**Beschrijving van waarschuwing** : een beschrijving voor de waarschuwings regel die door de gebruiker is geconfigureerd

**Ernst** : de ernst van de waarschuwing wanneer aan de criteria die zijn opgegeven in de waarschuwings regel wordt voldaan. Ernst kan variëren van 0 tot 4.

**Actie** -een specifieke actie die wordt uitgevoerd wanneer de waarschuwing wordt geactiveerd. Zie [actie groepen](../../azure-monitor/platform/action-groups.md)voor meer informatie.

## <a name="what-you-can-alert-on"></a>Wat u kunt waarschuwen voor

U kunt een waarschuwing ontvangen over metrische gegevens en Logboeken, zoals beschreven bij het bewaken van [data bronnen](../../azure-monitor/platform/data-sources-reference.md). Deze omvatten, maar zijn niet beperkt tot:
- Metrische waarden
- Zoek query's in Logboeken
- Activiteiten logboek gebeurtenissen
- Status van het onderliggende Azure-platform
- Tests voor Beschik baarheid van de website

Voorheen hadden Azure Monitor meet gegevens, Application Insights, Log Analytics en Service Health afzonderlijke waarschuwings mogelijkheden. Azure heeft de gebruikers interface en verschillende methoden van waarschuwingen in de loop van de tijd verbeterd en gecombineerd. Deze consolidatie wordt nog steeds uitgevoerd. Als gevolg hiervan zijn er nog enkele waarschuwings mogelijkheden in het nieuwe systeem voor waarschuwingen.  

| **Bron bewaken** | **Signaal type**  | **Beschrijving** | 
|-------------|----------------|-------------|
| Servicestatus | Activiteitenlogboek  | Wordt niet ondersteund. Zie [waarschuwingen voor activiteiten logboek maken op service meldingen](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).  |
| Application Insights | Tests voor Internet beschikbaarheid | Wordt niet ondersteund. Zie [waarschuwingen](../../azure-monitor/app/monitor-web-app-availability.md)voor webtests. Beschikbaar voor alle websites die zijn instrumenteel om gegevens naar Application Insights te verzenden. Ontvang een melding wanneer de beschik baarheid of reactie tijd van een website onder de verwachtingen ligt. |

## <a name="manage-alerts"></a>Waarschuwingen beheren
U kunt de status van een waarschuwing instellen om op te geven waar deze zich in het oplossings proces bevindt. Wanneer aan de criteria die zijn opgegeven in de waarschuwings regel wordt voldaan, wordt er een waarschuwing gemaakt of geactiveerd. deze heeft de status *Nieuw*. U kunt de status wijzigen wanneer u een waarschuwing bevestigt en wanneer u deze sluit. Alle status wijzigingen worden opgeslagen in de geschiedenis van de waarschuwing.

De volgende waarschuwings statussen worden ondersteund.

| State | Description |
|:---|:---|
| Nieuw | Het probleem is zojuist gedetecteerd en nog niet gecontroleerd. |
| Bevestigd | Een beheerder heeft de waarschuwing gecontroleerd en aan het werk gegaan. |
| Gesloten | Het probleem is opgelost. Nadat een waarschuwing is gesloten, kunt u deze opnieuw openen door deze te wijzigen in een andere status. |

De **waarschuwings status** is verschillend en onafhankelijk van de **monitor voorwaarde**. De waarschuwings status wordt ingesteld door de gebruiker. De bewakings voorwaarde is ingesteld door het systeem. Wanneer een waarschuwing wordt geactiveerd, wordt de bewakings voorwaarde van de waarschuwing ingesteld op *geactiveerd*. Wanneer de onderliggende voor waarde die de waarschuwing heeft veroorzaakt, is gewist, wordt de status van de monitor ingesteld op *opgelost*. De status van de waarschuwing wordt niet gewijzigd totdat de gebruiker deze wijzigt. Meer informatie [over het wijzigen van de status van uw waarschuwingen en slimme groepen](https://aka.ms/managing-alert-smart-group-states).

## <a name="smart-groups"></a>Slimme groepen 
Slimme groepen zijn beschikbaar als preview-versie. 

Slimme groepen zijn aggregaties van waarschuwingen op basis van machine learning algoritmen, waardoor waarschuwings lawaai en hulp bij het oplossen van problemen kunnen worden gereduceerd. Meer [informatie over slimme groepen](https://aka.ms/smart-groups) en [het beheren van uw slimme groepen](https://aka.ms/managing-smart-groups).


## <a name="alerts-experience"></a>Waarschuwings ervaring 
De pagina standaard waarschuwingen bevat een samen vatting van waarschuwingen die binnen een bepaald tijd venster zijn gemaakt. Hier worden de totale waarschuwingen voor elke ernst weer gegeven met kolommen die het totale aantal waarschuwingen in elke status voor elke Ernst identificeren. Selecteer een van de mogelijke ernst om de pagina [alle waarschuwingen](#all-alerts-page) te openen, gefilterd op die ernst.

U kunt ook [programmatisch de waarschuwings instanties opsommen die zijn gegenereerd op uw abonnement (en) door gebruik te maken van rest api's](#manage-your-alert-instances-programmatically).

> [!NOTE]
   >  Alleen waarschuwingen die in de afgelopen 30 dagen zijn gegenereerd, kunnen worden geopend op de UX of via de REST Api's.

Er worden geen oudere [klassieke waarschuwingen](#classic-alerts)weer gegeven of bijgehouden. U kunt de abonnementen of filter parameters wijzigen om de pagina bij te werken. 

![Pagina waarschuwingen](media/alerts-overview/alerts-page.png)

U kunt deze weer gave filteren door waarden te selecteren in de vervolg keuzemenu's boven aan de pagina.

| Kolom | Description |
|:---|:---|
| Subscription | Selecteer de Azure-abonnementen waarvoor u de waarschuwingen wilt weer geven. U kunt ervoor kiezen om al uw abonnementen te selecteren. Alleen waarschuwingen waarmee u toegang hebt tot de geselecteerde abonnementen, worden opgenomen in de weer gave. |
| Resource group | Selecteer één resource groep. In de weer gave zijn alleen waarschuwingen met doelen in de geselecteerde resource groep opgenomen. |
| Tijdsbereik | Alleen waarschuwingen die binnen het geselecteerde tijd venster worden geactiveerd, worden in de weer gave opgenomen. Ondersteunde waarden zijn het afgelopen uur, de afgelopen 24 uur, de afgelopen 7 dagen en de afgelopen 30 dagen. |

Selecteer de volgende waarden boven aan de pagina waarschuwingen om een andere pagina te openen.

| Value | Description |
|:---|:---|
| Totaal aantal waarschuwingen | Het totale aantal waarschuwingen dat overeenkomt met de geselecteerde criteria. Selecteer deze waarde om de weer gave alle waarschuwingen zonder filter te openen. |
| Slimme groepen | Het totale aantal slimme groepen dat is gemaakt op basis van de waarschuwingen die overeenkomen met de geselecteerde criteria. Selecteer deze waarde om de lijst met Smart groepen te openen in de weer gave alle waarschuwingen.
| Totaal aantal waarschuwingsregels | Het totale aantal waarschuwings regels in het geselecteerde abonnement en in de resource groep. Selecteer deze waarde om de regel weergave te openen die is gefilterd op het geselecteerde abonnement en de resource groep.


## <a name="manage-alert-rules"></a>Waarschuwingsregels beheren
Klik op **waarschuwings regels beheren** om de pagina **regels** weer te geven. **Regels** zijn één plaats voor het beheren van alle waarschuwings regels in uw Azure-abonnementen. De lijst bevat alle waarschuwings regels en kan worden gesorteerd op basis van doel resources, resource groepen, regel naam of status. Waarschuwings regels kunnen ook worden bewerkt, ingeschakeld of uitgeschakeld op deze pagina.  

 ![waarschuwingen-regels](./media/alerts-overview/alerts-preview-rules.png)


## <a name="create-an-alert-rule"></a>Een waarschuwingsregel maken
Waarschuwingen kunnen op een consistente manier worden ontworpen, ongeacht de bewakings service of het signaal type. Alle gebrande waarschuwingen en gerelateerde Details zijn beschikbaar op één pagina.
 
U maakt een nieuwe waarschuwings regel met de volgende drie stappen:
1. Kies het _doel_ voor de waarschuwing.
1. Selecteer het _signaal_ van de beschik bare signalen voor het doel.
1. Geef de _logica_ op die moet worden toegepast op gegevens uit het signaal.
 
Voor dit vereenvoudigde ontwerp proces hoeft u niet langer de bewakings bron of signalen te weten die worden ondersteund voordat u een Azure-resource selecteert. De lijst met beschik bare signalen wordt automatisch gefilterd op basis van de doel resource die u selecteert. Op basis van dat doel wordt u begeleid door de logica van de waarschuwings regel automatisch te definiëren.  

Meer informatie over het maken van waarschuwings regels vindt u in [waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../../azure-monitor/platform/alerts-metric.md).

Er zijn waarschuwingen beschikbaar in verschillende Azure-bewakings Services. Zie [Azure-toepassingen en-resources bewaken](../../azure-monitor/overview.md)voor meer informatie over hoe en wanneer elk van deze services moet worden gebruikt. 


## <a name="all-alerts-page"></a>Pagina alle waarschuwingen 
Klik op totaal aantal waarschuwingen om de pagina alle waarschuwingen weer te geven. Hier kunt u een lijst weer geven met waarschuwingen die zijn gemaakt in het geselecteerde tijd venster. U kunt een lijst weer geven van de afzonderlijke waarschuwingen of een lijst van de Smart-groepen die de waarschuwingen bevatten. Selecteer de banner aan de bovenkant van de pagina om tussen de weer gaven te scha kelen.

![Pagina alle waarschuwingen](media/alerts-overview/all-alerts-page.png)

U kunt de weer gave filteren door de volgende waarden te selecteren in de vervolg keuzemenu's boven aan de pagina.

| Kolom | Description |
|:---|:---|
| Subscription | Selecteer de Azure-abonnementen waarvoor u de waarschuwingen wilt weer geven. U kunt ervoor kiezen om al uw abonnementen te selecteren. Alleen waarschuwingen waarmee u toegang hebt tot de geselecteerde abonnementen, worden opgenomen in de weer gave. |
| Resource group | Selecteer één resource groep. In de weer gave zijn alleen waarschuwingen met doelen in de geselecteerde resource groep opgenomen. |
| Resourcetype | Selecteer een of meer resource typen. Alleen waarschuwingen met doelen van het geselecteerde type worden opgenomen in de weer gave. Deze kolom is alleen beschikbaar nadat een resource groep is opgegeven. |
| Resource | Selecteer een resource. De weer gave bevat alleen waarschuwingen met die resource als doel. Deze kolom is alleen beschikbaar nadat een resource type is opgegeven. |
| Severity | Selecteer een ernst van de waarschuwing of selecteer *Alles* om waarschuwingen van alle ernst op te neemt. |
| Monitorconditie | Selecteer een Bewaak voorwaarde of selecteer *Alles* om waarschuwingen voor voor waarden op te stellen. |
| Waarschuwingsstatus | Selecteer een waarschuwings status of selecteer *Alles* om waarschuwingen van status op te neemt. |
| Service bewaken | Selecteer een service of selecteer *Alles* om alle services op te laten staan. Er worden alleen waarschuwingen opgenomen die zijn gemaakt door regels die gebruikmaken van de service als doel. |
| Tijdsbereik | Alleen waarschuwingen die binnen het geselecteerde tijd venster worden geactiveerd, worden in de weer gave opgenomen. Ondersteunde waarden zijn het afgelopen uur, de afgelopen 24 uur, de afgelopen 7 dagen en de afgelopen 30 dagen. |

Selecteer **kolommen** boven aan de pagina om te selecteren welke kolommen u wilt weer geven. 

## <a name="alert-details-page"></a>Pagina waarschuwings Details
De pagina met waarschuwings Details wordt weer gegeven wanneer u een waarschuwing selecteert. Het bevat details van de waarschuwing en stelt u in staat de status ervan te wijzigen.

![Waarschuwingsdetails](media/alerts-overview/alert-detail2.png)

De pagina waarschuwings Details bevat de volgende secties.

| Section | Description |
|:---|:---|
| Samenvatting | Hiermee worden de eigenschappen en andere belang rijke informatie over de waarschuwing weer gegeven. |
| Geschiedenis | Een lijst met alle acties die worden uitgevoerd door de waarschuwing en eventuele wijzigingen aan de waarschuwing. Momenteel beperkt tot status wijzigingen. |
| Diagnostiek | Informatie over de Smart-groep waarin de waarschuwing is opgenomen. Het aantal meldingen verwijst naar het aantal waarschuwingen dat is opgenomen in de slimme groep. Bevat ook andere waarschuwingen in dezelfde slimme groep die in de afgelopen 30 dagen zijn gemaakt, ongeacht het tijd filter op de lijst pagina met waarschuwingen. Selecteer een waarschuwing om de details ervan weer te geven. |

## <a name="role-based-access-control-rbac-for-your-alert-instances"></a>Op rollen gebaseerd toegangs beheer (RBAC) voor uw waarschuwings instanties

Voor het gebruik en het beheer van waarschuwings instanties moet de gebruiker beschikken over de ingebouwde RBAC-rollen van de bewaking van de [Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) of [bewakings lezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader). Deze rollen worden ondersteund in elk Azure Resource Manager bereik, van het abonnements niveau tot gedetailleerde toewijzingen op een resource niveau. Als een gebruiker bijvoorbeeld alleen de toegang van ' bewakings bijdrager ' voor virtuele machine ' ContosoVM1 ' heeft, kan hij alleen waarschuwingen gebruiken en beheren die zijn gegenereerd op ' ContosoVM1 '.

## <a name="manage-your-alert-instances-programmatically"></a>Uw waarschuwings instanties programmatisch beheren

Er zijn veel scenario's waarin u programmatisch een query wilt uitvoeren voor waarschuwingen die zijn gegenereerd op basis van uw abonnement. Dit kan het zijn om aangepaste weer gaven te maken buiten de Azure Portal, of om uw waarschuwingen te analyseren om patronen en trends te identificeren.

U kunt een query uitvoeren voor waarschuwingen die worden gegenereerd op basis van uw abonnementen door gebruik te maken van de [Waarschuwingenbeheer rest API](https://aka.ms/alert-management-api) of door gebruik te maken van de [Azure Resource Graph-rest API voor waarschuwingen](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources).

Met de [Azure resource Graph-rest API voor waarschuwingen](https://docs.microsoft.com/rest/api/azureresourcegraph/resources/resources) kunt u op schaal zoeken naar waarschuwings exemplaren. Dit wordt aanbevolen voor scenario's waarbij u waarschuwingen moet beheren die worden gegenereerd over veel abonnementen. 

De volgende voorbeeld aanvraag voor de API retourneert het aantal waarschuwingen binnen één abonnement:

```json
{
  "subscriptions": [
    <subscriptionId>
  ],
  "query": "where type =~ 'Microsoft.AlertsManagement/alerts' | summarize count()",
  "options": {
            "dataset":"alerts"
  }
}
```
De waarschuwingen kunnen worden opgevraagd voor hun [' essentiële '](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields) velden.

De [Waarschuwingenbeheer rest API](https://aka.ms/alert-management-api) kan worden gebruikt om meer informatie te krijgen over specifieke waarschuwingen, met inbegrip van de velden voor de [waarschuwings context](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) .

## <a name="classic-alerts"></a>Klassieke waarschuwingen 

De functie voor het Azure Monitor van metrische gegevens en het activiteiten logboek voor het vastleggen van gebeurtenissen voor juni 2018 wordt ' waarschuwingen (klassiek) ' genoemd. 

Zie [klassieke waarschuwingen](./../../azure-monitor/platform/alerts-classic.overview.md) voor meer informatie


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over slimme groepen](https://aka.ms/smart-groups)
- [Meer informatie over actie groepen](../../azure-monitor/platform/action-groups.md)
- [Uw waarschuwings instanties in azure beheren](https://aka.ms/managing-alert-instances)
- [Slimme groepen beheren](https://aka.ms/managing-smart-groups)






