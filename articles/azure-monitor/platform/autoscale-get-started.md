---
title: Aan de slag met automatische schaalaanpassing in Azure
description: Meer informatie over het schalen van uw resource web-app, Cloud service, virtuele machine of virtuele-machine schaal sets in Azure.
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: b8d16b4e112c9aebe86c60dc01d380d591fc7624
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743519"
---
# <a name="get-started-with-autoscale-in-azure"></a>Aan de slag met automatisch schalen in azure
In dit artikel wordt beschreven hoe u uw instellingen voor automatisch schalen instelt voor uw resource in de Microsoft Azure-portal.

Azure Monitor automatisch schalen is alleen van toepassing op [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app service-Web apps](https://azure.microsoft.com/services/app-service/web/)en [API Management Services](../../api-management/api-management-key-concepts.md).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>De instellingen voor automatisch schalen in uw abonnement detecteren

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

U kunt alle resources ontdekken waarvoor automatisch schalen van toepassing is in Azure Monitor. Voer de volgende stappen uit voor een stapsgewijze Walkthrough:

1. Open de [Azure Portal.][1]
1. Klik op het pictogram Azure Monitor in het linkerdeel venster.
  ![Azure Monitor openen][2]
1. Klik op **automatisch schalen** om alle resources weer te geven waarvoor automatisch schalen van toepassing is, samen met de huidige status van automatisch schalen.
  ![Automatisch schalen ontdekken in Azure Monitor][3]

U kunt het filter deel venster bovenaan het bereik van de lijst gebruiken om resources in een specifieke resource groep, specifieke resource typen of een specifieke resource te selecteren.

Voor elke resource vindt u het aantal huidige instanties en de status automatisch schalen. De status voor automatisch schalen kan zijn:

- **Niet geconfigureerd**: u hebt automatisch schalen nog niet ingeschakeld voor deze resource.
- **Ingeschakeld**: u hebt automatisch schalen ingeschakeld voor deze resource.
- **Uitgeschakeld**: u hebt automatisch schalen uitgeschakeld voor deze resource.

## <a name="create-your-first-autoscale-setting"></a>Uw eerste instelling voor automatisch schalen maken

We gaan nu een eenvoudige stapsgewijze procedure volgen om uw eerste instelling voor automatisch schalen te maken.

1. Open de Blade **automatisch schalen** in azure monitor en selecteer een resource die u wilt schalen. (In de volgende stappen wordt gebruikgemaakt van een App Service-abonnement dat is gekoppeld aan een web-app. U kunt [binnen vijf minuten uw eerste ASP.net-Web-app maken in Azure.][4])
1. Houd er rekening mee dat het huidige aantal instanties 1 is. Klik op **automatisch schalen inschakelen**.
  ![Schaal instelling voor nieuwe web-app][5]
1. Geef een naam op voor de schaal instelling en klik vervolgens op **een regel toevoegen**. U ziet de opties voor de schaal regel die worden geopend als een context venster aan de rechter kant. Dit stelt standaard de optie in voor het schalen van het aantal instanties met 1 als het CPU-percentage van de resource groter is dan 70 procent. Behoud de standaard waarden en klik op **toevoegen**.
  ![Schaal instelling voor een web-app maken][6]
1. U hebt nu uw eerste schaal regel gemaakt. Houd er rekening mee dat de UX aanbevolen procedures en ' wordt aanbevolen om ten minste één schaal in regel te hebben '. Hiervoor doet u het volgende:

    a. Klik op **Een regel toevoegen**.

    b. Stel **operator** in op **kleiner dan**.

    c. Stel **drempel waarde** in op **20**.

    d. Stel de **bewerking** in op het **verkorten van het aantal**.

   U hebt nu een schaal instelling die wordt geschaald of geschaald op basis van het CPU-gebruik.
   ![Schalen op basis van CPU][8]
1. Klik op **Opslaan**.

Gefeliciteerd U hebt nu uw eerste schaal instelling gemaakt om uw web-app automatisch te schalen op basis van het CPU-gebruik.

> [!NOTE]
> Dezelfde stappen zijn van toepassing om aan de slag te gaan met een virtuele-machine schaalset of een Cloud service-rol.

## <a name="other-considerations"></a>Andere overwegingen
### <a name="scale-based-on-a-schedule"></a>Schalen op basis van een planning
Naast schalen op basis van de CPU kunt u de schaal anders instellen voor specifieke dagen van de week.

1. Klik op **een schaal voorwaarde toevoegen**.
1. Het instellen van de schaal modus en de regels is hetzelfde als de standaard voorwaarde.
1. Selecteer **specifieke dagen herhalen** voor de planning.
1. Selecteer de dagen en de begin-en eind tijd waarop de schaal voorwaarde moet worden toegepast.

![Schaal voorwaarde op basis van planning][9]
### <a name="scale-differently-on-specific-dates"></a>Anders schalen op specifieke datums
Naast schalen op basis van de CPU kunt u de schaal anders instellen voor specifieke datums.

1. Klik op **een schaal voorwaarde toevoegen**.
1. Het instellen van de schaal modus en de regels is hetzelfde als de standaard voorwaarde.
1. Selecteer **begin-en eind datums** voor het schema opgeven.
1. Selecteer de begin-en eind datum en de begin-en eind tijd waarop de schaal voorwaarde moet worden toegepast.

![Schaal voorwaarde op basis van datums][10]

### <a name="view-the-scale-history-of-your-resource"></a>De schaal geschiedenis van uw resource weer geven
Wanneer uw resource omhoog of omlaag wordt geschaald, wordt er een gebeurtenis in het activiteiten logboek vastgelegd. U kunt de schaal geschiedenis van uw resource in de afgelopen 24 uur weer geven door over te scha kelen op het tabblad **uitvoerings geschiedenis** .

![Uitvoer.gesch][11]

Als u de volledige schaal geschiedenis (Maxi maal 90 dagen) wilt weer geven, selecteert u **hier om meer details weer te**geven. Het activiteiten logboek wordt geopend, waarbij automatisch schalen vooraf is geselecteerd voor uw resource en categorie.

### <a name="view-the-scale-definition-of-your-resource"></a>De schaal definitie van uw resource weer geven
Automatisch schalen is een Azure Resource Manager bron. U kunt de schaal definitie in JSON weer geven door naar het tabblad **JSON** te scha kelen.

![Schaal definitie][12]

U kunt, indien nodig, direct wijzigingen aanbrengen in JSON. Deze wijzigingen worden weer gegeven nadat u deze hebt opgeslagen.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Automatisch schalen uitschakelen en uw instanties hand matig schalen
Het kan voor komen dat u de huidige schaal instelling wilt uitschakelen en de resource hand matig wilt schalen.

Klik bovenaan op de knop **automatisch schalen uitschakelen** .
![Automatisch schalen uitschakelen][13]

> [!NOTE]
> Met deze optie wordt de configuratie uitgeschakeld. U kunt deze echter weer terughalen nadat u automatisch schalen opnieuw hebt ingeschakeld.

U kunt nu het aantal exemplaren dat u wilt schalen hand matig instellen.

![Hand matige schaal instellen][14]

U kunt altijd terugkeren naar automatisch schalen door op **automatisch schalen inschakelen** te klikken en vervolgens op te **slaan**.

## <a name="route-traffic-to-healthy-instances-app-service"></a>Verkeer routeren naar gezonde instanties (App Service)

Wanneer u naar meerdere exemplaren hebt geschaald, kunt App Service status controles uitvoeren op uw instanties om verkeer alleen naar de gezonde instanties te routeren. Hiertoe opent u de portal op uw App Service en selecteert u vervolgens **status controleren** onder **bewaking**. Selecteer **inschakelen** en geef een geldig URL-pad op voor uw toepassing, zoals `/health` of `/api/health` . Klik op **Opslaan**.

Als u de functie wilt inschakelen met ARM-sjablonen, stelt `healthcheckpath` u de eigenschap van de `Microsoft.Web/sites` resource in op het Health Check-pad op uw site, bijvoorbeeld: `"/api/health/"` . Als u de functie wilt uitschakelen, stelt u de eigenschap weer in op de lege teken reeks, `""` .

### <a name="health-check-path"></a>Pad voor status controle

Het pad moet binnen twee minuten reageren met een status code tussen 200 en 299 (inclusief). Als het pad niet binnen twee minuten reageert of een status code buiten het bereik retourneert, wordt het exemplaar als ' beschadigd ' beschouwd. Status controle kan worden geïntegreerd met de functies voor verificatie en autorisatie van App Service. het systeem bereikt het eind punt zelfs als deze secuity-functies zijn ingeschakeld. Als u uw eigen verificatie systeem gebruikt, moet het Health Check-pad anonieme toegang toestaan. Als de site HTTP**s**-only heeft ingeschakeld, wordt de status controle-aanvraag verzonden via http**s**.

Het controle traject voor de status moet de essentiële onderdelen van uw toepassing controleren. Als uw toepassing bijvoorbeeld afhankelijk is van een Data Base en een berichten systeem, moet het eind punt van de status controle verbinding maken met deze onderdelen. Als de toepassing geen verbinding kan maken met een kritiek onderdeel, moet het pad een respons code op 500-niveau retour neren om aan te geven dat de app een slechte status heeft.

#### <a name="security"></a>Beveiliging 

Ontwikkel teams in grote ondernemingen moeten vaak voldoen aan de beveiligings vereisten voor hun blootgestelde Api's. Als u het status controle-eind punt wilt beveiligen, moet u eerst functies zoals [IP-beperkingen](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules), [client certificaten](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules)of een Virtual Network gebruiken om de toegang tot de toepassing te beperken. U kunt het status controle-eind punt zelf beveiligen door te vereisen dat de `User-Agent` van de inkomende aanvragen overeenkomen `ReadyForRequest/1.0` . De gebruiker Agent kan niet worden vervalst omdat de aanvraag al is beveiligd door de eerdere beveiligings functies.

### <a name="behavior"></a>Gedrag

Wanneer het pad voor de status controle wordt gegeven, wordt door App Service het pad naar alle exemplaren gepingd. Als een geslaagde respons code niet wordt ontvangen na 5 pings, wordt dat exemplaar als ' beschadigd ' beschouwd. Een of meer beschadigde instanties worden uitgesloten van de load balancer draaiing. Bovendien, wanneer u omhoog of omlaag schaalt, App Service pingt het Health Check-pad om ervoor te zorgen dat de nieuwe exemplaren gereed zijn voor aanvragen.

De resterende in orde zijnde instanties kunnen een grotere belasting hebben. Om te voor komen dat de resterende instanties overblijven, worden niet meer dan de helft van uw instanties uitgesloten. Als een App Service plan bijvoorbeeld wordt uitgeschaald naar vier instanties en 3 van de status niet in orde, wordt Maxi maal 2 uitgesloten van de loadbalancer-rotatie. De andere twee instanties (1 in orde en 1 zijn beschadigd) blijven aanvragen ontvangen. In het slechtste scenario waarbij alle instanties een slechte status hebben, wordt geen uitgesloten.

Als een exemplaar gedurende één uur niet in orde is, wordt het vervangen door een nieuw exemplaar. Er wordt Maxi maal één exemplaar per uur vervangen, met een maximum van drie exemplaren per dag per App Service plan.

### <a name="monitoring"></a>Bewaking

Nadat u het Health Check-pad van uw toepassing hebt opgegeven, kunt u de status van uw site bewaken met behulp van Azure Monitor. Klik op de Blade **status controle** in de portal op de **metrische gegevens** in de bovenste werk balk. Hiermee opent u een nieuwe blade waar u de historische status van de site kunt zien en een nieuwe waarschuwings regel maakt. [Zie de gids over Azure monitor](../../app-service/web-sites-monitor.md)voor meer informatie over het bewaken van uw sites.

## <a name="next-steps"></a>Volgende stappen
- [Een waarschuwing voor een activiteiten logboek maken om alle bewerkingen voor het automatisch schalen van de engine voor uw abonnement te bewaken](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Een waarschuwing voor een activiteiten logboek maken voor het bewaken van alle mislukte schaal-in-en uitschaal bewerkingen voor automatisch schalen op uw abonnement](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
