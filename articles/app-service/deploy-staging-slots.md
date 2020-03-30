---
title: Faseringsomgevingen instellen
description: Meer informatie over het implementeren van apps naar een niet-productiesleuf en autoswap in productie. Verhoog de betrouwbaarheid en elimineer downtime van apps van implementaties.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 03/04/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 21e025088e59c7f65f848b332ecb393b05918261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300840"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Faseringsomgevingen in Azure App Service instellen
<a name="Overview"></a>

Wanneer u uw web-app, web-app op Linux, mobiele back-end of API-app implementeert naar [Azure App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)u een aparte implementatiesleuf gebruiken in plaats van de standaardproductiesleuf wanneer u actief bent in de laag **Standard,** **Premium**of **Isolated** App Service-abonnement. Implementatieslots zijn live-apps met hun eigen hostnamen. App-inhoud en configuraties elementen kunnen worden verwisseld tussen twee implementatie slots, met inbegrip van de productie sleuf. 

Het implementeren van uw toepassing naar een niet-productiesleuf heeft de volgende voordelen:

* U app-wijzigingen in een implementatiesleuf voor tijdelijke implementatie valideren voordat u deze omwisselt met de productiesleuf.
* Als u een app eerst naar een sleuf implementeert en deze in productie maakt, wordt ervoor gezorgd dat alle exemplaren van de sleuf worden opgewarmd voordat deze in productie worden gemaakt. Dit elimineert downtime wanneer u uw app implementeert. De omleiding van het verkeer is naadloos en er worden geen aanvragen verwijderd vanwege swapbewerkingen. U deze hele workflow automatiseren door [autoswap](#Auto-Swap) te configureren wanneer pre-swap validatie niet nodig is.
* Na een swap heeft de sleuf met eerder gefaseerde app nu de vorige productie-app. Als de wijzigingen die zijn omgeruild in de productiesleuf niet zijn zoals u verwacht, u dezelfde swap onmiddellijk uitvoeren om uw laatst bekende goede site terug te krijgen.

Elke app-serviceplanlaag ondersteunt een ander aantal implementatiesleuven. Er zijn geen extra kosten verbonden aan het gebruik van implementatieslots. Zie [App-servicelimieten](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)voor meer informatie over het aantal sleuven dat de laag van uw app ondersteunt. 

Als u uw app wilt schalen naar een andere laag, moet u ervoor zorgen dat de doellaag het aantal sleuven ondersteunt dat uw app al gebruikt. Als uw app bijvoorbeeld meer dan vijf sleuven heeft, u deze niet naar de **standaardlaag** schalen, omdat de **standaardlaag** slechts vijf implementatiesleuven ondersteunt. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Een sleuf toevoegen
De app moet worden uitgevoerd in de laag **Standaard,** **Premium**of **Geïsoleerd** om meerdere implementatiesleuven in te schakelen.


1. zoek naar en selecteer **App Services** en selecteer uw app in de [Azure-portal.](https://portal.azure.com/) 
   
    ![Zoeken naar App-services](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. Selecteer in het linkerdeelvenster de optie **Implementatiesleuven** > **Sleuf toevoegen**.
   
    ![Een nieuwe implementatiesite toevoegen](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Als de app zich nog niet in **de**standaard-, **premium-** of **geïsoleerde** laag bevindt, ontvangt u een bericht dat de ondersteunde lagen aangeeft voor het inschakelen van gefaseerdpubliceren. Op dit punt hebt u de optie om **Upgrade** te selecteren en naar het tabblad **Schalen** van uw app te gaan voordat u verdergaat.
   > 

3. Geef in het dialoogvenster **Een sleuf toevoegen** de sleuf een naam en selecteer of u een app-configuratie wilt klonen vanuit een andere implementatiesleuf. Selecteer **Toevoegen** om door te gaan.
   
    ![Configuratiebron](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    U een configuratie klonen vanuit een bestaande sleuf. Instellingen die kunnen worden gekloond zijn app-instellingen, verbindingstekenreeksen, taalframeworkversies, websockets, HTTP-versie en platformbitness.

4. Nadat de sleuf is toegevoegd, selecteert u **Sluiten** om het dialoogvenster te sluiten. De nieuwe sleuf wordt nu weergegeven op de pagina **Implementatiesleuven.** **Verkeer %** is standaard ingesteld op 0 voor de nieuwe sleuf, waarbij al het klantverkeer naar de productiesleuf wordt doorgestuurd.

5. Selecteer de nieuwe implementatiesleuf om de resourcepagina van die sleuf te openen.
   
    ![Titel van implementatiesleuf](./media/web-sites-staged-publishing/StagingTitle.png)

    De faseringssleuf heeft een beheerpagina, net als elke andere App Service-app. U de configuratie van de sleuf wijzigen. De naam van de sleuf wordt boven aan de pagina weergegeven om u eraan te herinneren dat u de implementatiesleuf bekijkt.

6. Selecteer de URL van de app op de resourcepagina van de sleuf. De implementatiesleuf heeft een eigen hostnaam en is ook een live-app. Zie [IP-beperkingen](app-service-ip-restrictions.md)van Azure App Service om de toegang van het publiek tot de implementatiesleuf te beperken.

De nieuwe implementatiesleuf heeft geen inhoud, zelfs niet als u de instellingen vanuit een andere sleuf kloont. U bijvoorbeeld [publiceren naar deze sleuf met Git](app-service-deploy-local-git.md). U implementeren naar de sleuf vanuit een andere repository branch of een andere repository. 

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Wat gebeurt er tijdens een swap

### <a name="swap-operation-steps"></a>Bewerkingsstappen verwisselen

Wanneer u twee sleuven (meestal vanuit een faseringssleuf in de productiesleuf) verwisselt, doet App Service het volgende om ervoor te zorgen dat de doelsleuf geen downtime ondervindt:

1. Pas de volgende instellingen uit de doelsleuf (bijvoorbeeld de productiesleuf) toe op alle exemplaren van de bronsleuf: 
    - [Slotspecifieke app-instellingen](#which-settings-are-swapped) en verbindingstekenreeksen, indien van toepassing.
    - [Continue](deploy-continuous-deployment.md) implementatie-instellingen, indien ingeschakeld.
    - [Verificatie-instellingen voor App-service,](overview-authentication-authorization.md) indien ingeschakeld.
    
    Een van deze gevallen trigger alle instanties in de bronsleuf opnieuw te starten. Tijdens [swap met preview](#Multi-Phase)markeert dit het einde van de eerste fase. De swapbewerking wordt onderbroken en u valideren dat de bronsleuf correct werkt met de instellingen van de doelsleuf.

1. Wacht tot elk exemplaar in de bronsleuf de herstart voltooit. Als een instantie niet opnieuw wordt opgestart, wordt alle wijzigingen in de bronsleuf teruggezet en wordt de bewerking gestopt.

1. Als [lokale cache](overview-local-cache.md) is ingeschakeld, activeert u de initialisatie van de lokale cache door een HTTP-aanvraag in te dienen bij de toepassingshoofdmap ("/") voor elk exemplaar van de bronsleuf. Wacht tot elk exemplaar een HTTP-antwoord retourneert. Lokale cacheinitialisatie zorgt ervoor dat elke instantie opnieuw wordt opgestart.

1. Als [automatisch wisselen](#Auto-Swap) is ingeschakeld met aangepaste [opwarmservice,](#Warm-up)activeert u [Toepassingsinitiatie](https://docs.microsoft.com/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) door een HTTP-aanvraag in te dienen bij de toepassingsbasis ("/") voor elk exemplaar van de bronsleuf.

    Als `applicationInitialization` deze niet is opgegeven, activeert u een HTTP-aanvraag naar de toepassingsmap van de bronsleuf voor elke instantie. 
    
    Als een instantie een HTTP-antwoord retourneert, wordt deze als opgewarmd beschouwd.

1. Als alle instanties op de bronsleuf met succes zijn opgewarmd, verwisselt u de twee sleuven door de routeringsregels voor de twee sleuven te wijzigen. Na deze stap heeft de doelsleuf (bijvoorbeeld de productiesleuf) de app die eerder is opgewarmd in de bronsleuf.

1. Nu de bronsleuf de pre-swap-app eerder in de doelsleuf heeft, voert u dezelfde bewerking uit door alle instellingen toe te passen en de instanties opnieuw te starten.

Op elk punt van de swapbewerking vindt al het werk van het initialiseren van de verwisselde apps plaats op de bronsleuf. De doelsleuf blijft online terwijl de bronsleuf wordt voorbereid en opgewarmd, ongeacht waar de swap slaagt of mislukt. Als u een faseringssleuf wilt verwisselen met de productiesleuf, moet u ervoor zorgen dat de productiesleuf altijd de doelsleuf is. Op deze manier heeft de swapbewerking geen invloed op uw productie-app.

### <a name="which-settings-are-swapped"></a>Welke instellingen worden verwisseld?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Als u een app-instelling of verbindingstekenreeks wilt configureren om vast te houden aan een specifieke sleuf (niet verwisseld), gaat u naar de **pagina Configuratie** voor die sleuf. Voeg een instelling toe of bewerk deze en selecteer vervolgens **de instelling voor implementatiesleuf**. Als u dit selectievakje inschakelt, wordt aan App Service gezegd dat de instelling niet verwisselbaar is. 

![Sleufinstelling](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Twee slots verwisselen 
U implementatieslots uitwisselen op de pagina **Implementatieslots van** uw app en op de pagina **Overzicht.** Zie Wat er tijdens de [swap gebeurt voor](#AboutConfiguration)technische details over de slotswap.

> [!IMPORTANT]
> Voordat u een app van een implementatiesleuf in productie wisselt, moet u ervoor zorgen dat de productie uw doelsleuf is en dat alle instellingen in de bronsleuf precies zijn geconfigureerd zoals u ze in productie wilt hebben.
> 
> 

Deployment slots swap:

1. Ga naar de **pagina Implementatieslots van** uw app en selecteer **Swap**.
   
    ![Knop Wisselen](./media/web-sites-staged-publishing/SwapButtonBar.png)

    In het dialoogvenster **Swap** worden instellingen weergegeven in de geselecteerde bron- en doelsleuven die worden gewijzigd.

2. Selecteer de gewenste **bron-** en **doelsleuven.** Meestal is het doel de productiesleuf. Selecteer ook de tabbladen **Bronwijzigingen** en **Doelwijzigingen** en controleer of de configuratiewijzigingen worden verwacht. Als je klaar bent, kun je de slots meteen verwisselen door **Swap te**selecteren.

    ![Omwisselen voltooien](./media/web-sites-staged-publishing/SwapImmediately.png)

    Om te zien hoe uw doelsleuf zou worden uitgevoerd met de nieuwe instellingen voordat de swap daadwerkelijk plaatsvindt, selecteert u niet **Swap,** maar volg de instructies in [Swap met preview](#Multi-Phase).

3. Wanneer u klaar bent, sluit u het dialoogvenster door **Sluiten te**selecteren.

Als u problemen hebt, [raadpleegt u Swaps oplossen](#troubleshoot-swaps).

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Wisselen met voorbeeld (multi-phase swap)

Voordat u in productie gaat als doelsleuf, bevestigt u dat de app wordt uitgevoerd met de verwisselde instellingen. De bronsleuf wordt ook opgewarmd voor de swapvoltooiing, wat wenselijk is voor bedrijfskritische toepassingen.

Wanneer u een swap uitvoert met voorbeeld, voert App Service dezelfde [swapbewerking](#AboutConfiguration) uit, maar wordt na de eerste stap onderbroken. U het resultaat vervolgens controleren op de faseringssleuf voordat u de swap voltooit. 

Als u de swap annuleert, past App Service configuratie-elementen opnieuw toe op de bronsleuf.

Om te wisselen met preview:

1. Volg de stappen in [Implementatiesleuven swap,](#Swap) maar selecteer **Swap uitvoeren met voorbeeld**.

    ![Wisselen met voorbeeld](./media/web-sites-staged-publishing/SwapWithPreview.png)

    In het dialoogvenster ziet u hoe de configuratie in de bronsleuf verandert in fase 1 en hoe de bron- en doelsleuf in fase 2 veranderen.

2. Wanneer u klaar bent om de swap te starten, selecteert u **Swap starten**.

    Wanneer fase 1 is voltooid, wordt u hiervan op de hoogte gesteld in het dialoogvenster. Bekijk een voorbeeld van de `https://<app_name>-<source-slot-name>.azurewebsites.net`swap in de bronsleuf door naar . 

3. Wanneer u klaar bent om de ruil in behandeling te voltooien, selecteert u **Swap voltooien** in **swapactie** en selecteert u **Complete Swap**.

    Als u een ruil in behandeling wilt annuleren, selecteert u **Swap annuleren.**

4. Wanneer u klaar bent, sluit u het dialoogvenster door **Sluiten te**selecteren.

Als u problemen hebt, [raadpleegt u Swaps oplossen](#troubleshoot-swaps).

Zie [Automatiseren met PowerShell](#automate-with-powershell)voor het automatiseren van een multifaseswap.

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Een swap terugdraaien
Als er fouten optreden in de doelsleuf (bijvoorbeeld de productiesleuf) na een slotswap, herstelt u de slots naar hun pre-swap-statussen door onmiddellijk dezelfde twee slots om te wisselen.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Automatisch wisselen configureren

> [!NOTE]
> Auto swap wordt niet ondersteund in web apps op Linux.

Autoswap stroomlijnt Azure DevOps-scenario's waarin u uw app continu wilt implementeren zonder koude starts en nul downtime voor klanten van de app. Wanneer automatisch wisselen is ingeschakeld vanuit een sleuf in productie, elke keer dat u uw codewijzigingen in die sleuf duwt, wisselt App Service de app automatisch [om in productie](#swap-operation-steps) nadat deze is opgewarmd in de bronsleuf.

   > [!NOTE]
   > Voordat u automatische swap configureert voor de productiesleuf, u overwegen automatische swap te testen op een doelsleuf zonder productie.
   > 

Automatisch wisselen configureren:

1. Ga naar de resourcepagina van uw app. Selecteer de*\<gewenste bronsleuf * **van implementaties** > > >  **algemene configuratieinstellingen** > **General settings**.
   
2. Selecteer **Auto swap enabled** **Aan**. Selecteer vervolgens de gewenste doelsleuf voor **De implementatiesleuf automatisch wisselen**en selecteer **Opslaan** op de opdrachtbalk. 
   
    ![Selecties voor het configureren van automatische swap](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Voer een codepush uit naar de bronsleuf. Automatisch wisselen gebeurt na een korte tijd en de update wordt weergegeven op de URL van uw doelsleuf.

Als u problemen hebt, [raadpleegt u Swaps oplossen](#troubleshoot-swaps).

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Aangepaste warming-up opgeven

Voor sommige apps zijn aangepaste opwarmacties vereist voordat de swap wordt uitgevoerd. Met `applicationInitialization` het configuratie-element in web.config u aangepaste initialisatieacties opgeven. De [swapbewerking](#AboutConfiguration) wacht tot deze aangepaste warming-up is voltooid voordat deze wordt geruild met de doelsleuf. Hier is een voorbeeld web.config fragment.

    <system.webServer>
        <applicationInitialization>
            <add initializationPage="/" hostName="[app hostname]" />
            <add initializationPage="/Home/About" hostName="[app hostname]" />
        </applicationInitialization>
    </system.webServer>

Zie De meest voorkomende `applicationInitialization` fouten in [de implementatiesleufswaps en hoe u deze oplossen](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)voor meer informatie over het aanpassen van het element.

U ook het opwarmgedrag aanpassen met een of beide van de volgende [app-instellingen:](configure-common.md)

- `WEBSITE_SWAP_WARMUP_PING_PATH`: Het pad naar ping om uw site op te warmen. Voeg deze app-instelling toe door een aangepast pad op te geven dat begint met een slash als waarde. Een voorbeeld is `/statuscheck`. De standaardwaarde is `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Geldige HTTP-antwoordcodes voor de opwarmbewerking. Voeg deze app-instelling toe met een door komma's gescheiden lijst met HTTP-codes. Een voorbeeld `200,202` is. Als de geretourneerde statuscode niet in de lijst staat, worden de opwarm- en swapbewerkingen gestopt. Standaard zijn alle antwoordcodes geldig.

> [!NOTE]
> Het `<applicationInitialization>` configuratie-element maakt deel uit van elke app-opstart, terwijl de twee instellingen voor opwarmgedrag alleen van toepassing zijn op sleufswaps.

Als u problemen hebt, [raadpleegt u Swaps oplossen](#troubleshoot-swaps).

## <a name="monitor-a-swap"></a>Een swap controleren

Als de [swapbewerking](#AboutConfiguration) lang duurt, u informatie over de swapbewerking krijgen in het [activiteitenlogboek.](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

Selecteer op de resourcepagina van uw app in de portal in het linkerdeelvenster de optie **Activiteitslogboek**.

Er wordt een swapbewerking `Swap Web App Slots`weergegeven in de logboekquery als . U het uitbreiden en een van de subbewerkingen of fouten selecteren om de details te bekijken.

## <a name="route-traffic"></a>Routeverkeer

Standaard worden alle clientaanvragen naar de productie-URL van de app (`http://<app_name>.azurewebsites.net`) doorgestuurd naar de productiesleuf. U een deel van het verkeer naar een andere sleuf leiden. Deze functie is handig als u feedback van gebruikers nodig hebt voor een nieuwe update, maar u bent nog niet klaar om deze vrij te geven in productie.

### <a name="route-production-traffic-automatically"></a>Productieverkeer automatisch routeren

Ga als volgt te werk om het productieverkeer automatisch te routeren:

1. Ga naar de resourcepagina van uw app en selecteer **Implementatiesleuven**.

2. Geef in de kolom **Verkeer %** van de sleuf waarnaar u wilt routeren een percentage op (tussen 0 en 100) op om de hoeveelheid totaal verkeer weer te geven dat u wilt routeren. Selecteer **Opslaan**.

    ![Een verkeerspercentage instellen](./media/web-sites-staged-publishing/RouteTraffic.png)

Nadat de instelling is opgeslagen, wordt het opgegeven percentage clients willekeurig doorgestuurd naar de niet-productiesleuf. 

Nadat een client automatisch naar een specifieke sleuf is doorgestuurd, wordt deze voor de levensduur van die clientsessie 'vastgemaakt' aan die sleuf. In de clientbrowser u zien aan welke sleuf `x-ms-routing-name` uw sessie is vastgemaakt door te kijken naar de cookie in uw HTTP-headers. Een verzoek dat is doorgestuurd naar de "staging" slot heeft de cookie `x-ms-routing-name=staging`. Een verzoek dat wordt doorgestuurd naar de `x-ms-routing-name=self`productiesleuf heeft de cookie.

   > [!NOTE]
   > Naast de Azure-portal u [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) de opdracht in de Azure CLI ook gebruiken om de routeringspercentages in te stellen van CI/CD-hulpprogramma's zoals DevOps-pijplijnen of andere automatiseringssystemen.
   > 

### <a name="route-production-traffic-manually"></a>Productieverkeer handmatig routeren

Naast automatische verkeersroutering kan App Service aanvragen naar een specifieke sleuf leiden. Dit is handig wanneer u wilt dat uw gebruikers zich kunnen aanmelden voor of zich kunnen afmelden voor uw bèta-app. Als u het productieverkeer handmatig `x-ms-routing-name` wilt routeren, gebruikt u de queryparameter.

Als u gebruikers bijvoorbeeld wilt laten afmelden voor uw bèta-app, u deze koppeling op uw webpagina plaatsen:

```HTML
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

De `x-ms-routing-name=self` tekenreeks geeft de productiesleuf aan. Nadat de clientbrowser toegang heeft tot de koppeling, wordt deze doorgestuurd naar de productiesleuf. Elk volgend verzoek `x-ms-routing-name=self` heeft de cookie die de sessie vastspeldt op het productieslot.

Als u gebruikers wilt laten kiezen voor uw bèta-app, stelt u dezelfde queryparameter in op de naam van de niet-productiesleuf. Hier volgt een voorbeeld:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Nieuwe sleuven krijgen standaard een `0%`routeringsregel van , weergegeven in grijs. Wanneer u deze waarde `0%` expliciet instelt op (weergegeven in zwarte tekst), kunnen `x-ms-routing-name` uw gebruikers handmatig toegang krijgen tot de tijdelijke sleuf met behulp van de queryparameter. Maar ze worden niet automatisch naar de sleuf geleid omdat het routeringspercentage is ingesteld op 0. Dit is een geavanceerd scenario waarin u uw faseringssleuf verbergen voor het publiek, terwijl interne teams wijzigingen op de sleuf kunnen testen.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Een sleuf verwijderen

Zoek naar en selecteer uw app. Selecteer **de sleuf Implementatiesleuven** > *\<om>*  >  **overzicht**te verwijderen. Selecteer **Verwijderen** op de opdrachtbalk.  

![Een implementatiesleuf verwijderen](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatiseren met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell is een module die cmdlets biedt om Azure te beheren via Windows PowerShell, inclusief ondersteuning voor het beheren van implementatiesleuven in Azure App Service.

Zie [Microsoft Azure PowerShell installeren en configureren](/powershell/azure/overview)voor informatie over het installeren en configureren van Azure PowerShell en over het verifiëren van Azure PowerShell met uw Azure-abonnement.  

---
### <a name="create-a-web-app"></a>Een webtoepassing maken
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Een sleuf maken
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Een swap starten met een voorbeeld (multi-phase swap) en de configuratie van de doelsleuf toepassen op de bronsleuf
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Een in behandeling zijnde swap annuleren (swap met beoordeling) en de bronsleufconfiguratie herstellen
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Implementatiesites wisselen
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Swapgebeurtenissen controleren in het activiteitenlogboek
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Een sleuf verwijderen
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatiseren met Resource Manager-sjablonen

[Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview) zijn declaratieve JSON-bestanden die worden gebruikt om de implementatie en configuratie van Azure-resources te automatiseren. Als u slots wilt verwisselen met resourcebeheersjablonen, stelt u twee eigenschappen in op de bronnen *Microsoft.Web/sites/slots* en *Microsoft.Web/sites:*

- `buildVersion`: dit is een tekenreekseigenschap die de huidige versie van de app vertegenwoordigt die in de sleuf wordt geïmplementeerd. Bijvoorbeeld: "v1", "1.0.0.1" of "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: dit is een tekenreekseigenschap die aangeeft wat `buildVersion` de sleuf moet hebben. Als de targetBuildVersion niet `buildVersion`gelijk is aan de huidige , dan zal `buildVersion`dit leiden tot de swap operatie door het vinden van de sleuf die de opgegeven heeft .

### <a name="example-resource-manager-template"></a>Voorbeeld sjabloon ResourceManager

Met de volgende sjabloon `buildVersion` Resourcemanager wordt de `targetBuildVersion` van de faseringssleuf bijgewerkt en wordt de taak op de productiesleuf ingesteld. Dit zal de twee slots te wisselen. De sjabloon gaat ervan uit dat u al een webapp hebt gemaakt met een sleuf met de naam "staging".

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Deze resourcemanagersjabloon is idempotent, wat betekent dat deze herhaaldelijk kan worden uitgevoerd en dezelfde status van de sleuven kan produceren. Na de eerste `targetBuildVersion` uitvoering komt `buildVersion`de huidige, zodat een swap niet wordt geactiveerd.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatiseer met de CLI

Zie [az webapp-implementatiesleuf](/cli/azure/webapp/deployment/slot)voor [Azure CLI-opdrachten](https://github.com/Azure/azure-cli) voor implementatieslots.

## <a name="troubleshoot-swaps"></a>Swaps oplossen

Als er een fout optreedt tijdens een [slotswap,](#AboutConfiguration)is deze aangemeld *d:\home\LogFiles\eventlog.xml*. Het is ook ingelogd in het toepassingsspecifieke foutlogboek.

Hier zijn enkele veelvoorkomende swapfouten:

- Er is een HTTP-aanvraag voor de toepassingsroot getimed. De swapbewerking wacht 90 seconden voor elke HTTP-aanvraag en wordt tot 5 keer opnieuw geprobeerd. Als er een time-out is opgetreden tussen alle pogingen, wordt de swapbewerking gestopt.

- Initialisatie van de lokale cache kan mislukken wanneer de app-inhoud het lokale schijfquotum overschrijdt dat is opgegeven voor de lokale cache. Zie [Overzicht van lokale cache](overview-local-cache.md)voor meer informatie.

- Tijdens [de aangepaste warming-up](#Warm-up)worden de HTTP-aanvragen intern gedaan (zonder de externe URL te doorlopen). Ze kunnen mislukken met bepaalde URL-herschrijfregels in *Web.config*. Regels voor het omleiden van domeinnamen of het afdwingen van HTTPS kunnen bijvoorbeeld voorkomen dat opwarmverzoeken de app-code bereiken. Als u dit probleem wilt oplossen, wijzigt u uw herschrijfregels door de volgende twee voorwaarden toe te voegen:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Zonder een aangepaste warming-up kunnen de regels voor het herschrijven van de URL nog steeds HTTP-aanvragen blokkeren. Als u dit probleem wilt oplossen, wijzigt u uw herschrijfregels door de volgende voorwaarde toe te voegen:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Sommige [IP-beperkingsregels](app-service-ip-restrictions.md) kunnen voorkomen dat de swapbewerking HTTP-aanvragen naar uw app verzendt. IPv4-adresbereiken die `10.` `100.` beginnen met en intern zijn voor uw implementatie. U moet hen toestaan verbinding te maken met uw app.

- Na het wisselen van slot kan de app een onverwachte herstart ervaren. Dit komt omdat na een swap de hostname binding configuratie gaat uit sync, die op zichzelf niet leiden tot herstart. Bepaalde onderliggende opslaggebeurtenissen (zoals failovers met opslagvolume) kunnen deze verschillen echter detecteren en alle werkprocessen dwingen opnieuw op te starten. Als u dit soort opnieuw opstarten wilt minimaliseren, stelt u de [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` app-instelling](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) in op *alle sleuven.* Deze app-instelling werkt echter *niet* met WCF-apps (Windows Communication Foundation).

## <a name="next-steps"></a>Volgende stappen
[Toegang tot niet-productiesleuven blokkeren](app-service-ip-restrictions.md)
