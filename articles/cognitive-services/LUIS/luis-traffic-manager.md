---
title: Eindpuntquotum verhogen - LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) biedt de mogelijkheid om het quotum voor eindpuntaanvragen te verhogen tot boven het quotum van één sleutel. Dit wordt gedaan door meer sleutels voor LUIS te maken en deze toe te voegen aan de LUIS-toepassing op de pagina **Publiceren** in de sectie **Resources en sleutels.**
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: c4ea9c5663755a4feb1693dd925d99b10c466140
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70256609"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Microsoft Azure Traffic Manager gebruiken om eindpuntquota voor sleutels te beheren
Language Understanding (LUIS) biedt de mogelijkheid om het quotum voor eindpuntaanvragen te verhogen tot boven het quotum van één sleutel. Dit wordt gedaan door meer sleutels voor LUIS te maken en deze toe te voegen aan de LUIS-toepassing op de pagina **Publiceren** in de sectie **Resources en sleutels.** 

De client-applicatie moet het verkeer over de sleutels beheren. LUIS doet dat niet. 

In dit artikel wordt uitgelegd hoe u het verkeer over de sleutelen beheert met Azure [Traffic Manager][traffic-manager-marketing]. U moet al een getrainde en gepubliceerde LUIS-app hebben. Als u er geen hebt, volgt u het snel gestarte domein [Vooraf gebouwd.](luis-get-started-create-app.md) 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Verbinding maken met PowerShell in de Azure-portal
Open [Azure][azure-portal] het PowerShell-venster in de Azure-portal. Het pictogram voor het PowerShell-venster is de **>_** in de bovenste navigatiebalk. Door PowerShell van de portal te gebruiken, krijgt u de nieuwste PowerShell-versie en bent u geverifieerd. PowerShell in de portal vereist een [Azure Storage-account.](https://azure.microsoft.com/services/storage/) 

![Schermafbeelding van Azure-portal met Powershell-venster geopend](./media/traffic-manager/azure-portal-powershell.png)

In de volgende secties worden [Traffic Manager PowerShell-cmdlets gebruikt.](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager)

## <a name="create-azure-resource-group-with-powershell"></a>Azure-brongroep maken met PowerShell
Voordat u de Azure-resources maakt, maakt u een resourcegroep met alle bronnen. Geef de `luis-traffic-manager` resourcegroep een `West US`naam en gebruik het gebied is . Het gebied van de resourcegroep slaat metagegevens op over de groep. Het zal niet vertragen uw middelen als ze in een andere regio. 

Resourcegroep maken met de cmdlet **[Nieuw-AzResourceGroep:](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)**

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>LUIS-sleutels maken om het totale eindpuntquotum te verhogen
1. Maak in de Azure-portal twee **taalinformatietoetsen,** één in de `West US` en één in de `East US`. Gebruik de bestaande resourcegroep die is `luis-traffic-manager`gemaakt in de vorige sectie, met de naam . 

    ![Schermafbeelding van Azure-portal met twee LUIS-sleutels in de brongroep Luis-Traffic-Manager](./media/traffic-manager/luis-keys.png)

2. Wijs in de [LUIS-website][LUIS] in de sectie **Beheren** op de pagina **Azure Resources** sleutels toe aan de app en publiceer de app opnieuw door de knop **Publiceren** in het menu rechtsboven te selecteren. 

    De voorbeeld-URL in de **eindpuntkolom** gebruikt een GET-aanvraag met de eindpuntsleutel als queryparameter. Kopieer de URL's van het eindpunt van de twee nieuwe sleutels. Ze worden later in dit artikel gebruikt als onderdeel van de Traffic Manager-configuratie.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>LUIS-eindpuntaanvragen beheren voor alle sleutels met Traffic Manager
Traffic Manager maakt een nieuw DNS-toegangspunt voor uw eindpunten. Het fungeert niet als een gateway of proxy, maar strikt op DNS-niveau. In dit voorbeeld worden geen DNS-records gewijzigd. Het maakt gebruik van een DNS-bibliotheek om te communiceren met Traffic Manager om het juiste eindpunt voor die specifieke aanvraag te krijgen. _Voor_ elke aanvraag die bedoeld is voor LUIS, moet eerst een verzoek om verkeersbeheer worden gebruikt om te bepalen welk LUIS-eindpunt moet worden gebruikt. 

### <a name="polling-uses-luis-endpoint"></a>Polling gebruikt LUIS-eindpunt
Traffic Manager peilt de eindpunten periodiek om te controleren of het eindpunt nog beschikbaar is. De ondervraagde URL van traffic manager moet toegankelijk zijn met een GET-verzoek en een 200 retourneren. De URL van het eindpunt **op de pagina Publiceren** doet dit. Aangezien elke eindpuntsleutel een andere route- en querytekenreeksparameters heeft, heeft elke eindpuntsleutel een ander stempad nodig. Elke keer dat Traffic Manager polls, het kost een quotum aanvraag. De querytekenreeksparameter **q** van het LUIS-eindpunt is de utterance die naar LUIS wordt verzonden. Deze parameter wordt in plaats van het verzenden van een utterance gebruikt om Traffic Manager polling toe te voegen aan het LUIS-eindpuntlogboek als een foutopsporingstechniek terwijl Traffic Manager wordt geconfigureerd.

Omdat elk LUIS-eindpunt zijn eigen pad nodig heeft, heeft het een eigen Traffic Manager-profiel nodig. Als u alle profielen wilt beheren, maakt u een [ _geneste_ Traffic Manager-architectuur.](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) Een ouderprofiel wijst naar de profielen van de kinderen en beheert het verkeer over deze profielen.

Zodra de verkeersbeheermanager is geconfigureerd, moet u het pad wijzigen om de parameter logging=false query string te gebruiken, zodat uw logboek niet wordt gevuld met polling.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Verkeersbeheer configureren met geneste profielen
De volgende secties maken twee onderliggende profielen, een voor de Oost LUIS-toets en een voor de West LUIS-toets. Vervolgens wordt een bovenliggend profiel gemaakt en worden de twee onderliggende profielen toegevoegd aan het bovenliggende profiel. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>Het East US Traffic Manager-profiel maken met PowerShell
Als u het East US Traffic Manager-profiel wilt maken, zijn er verschillende stappen: profiel maken, eindpunt toevoegen en eindpunt instellen. Een Traffic Manager-profiel kan veel eindpunten hebben, maar elk eindpunt heeft hetzelfde validatiepad. Omdat de URL's van luis-eindpunten voor de oost- en west-abonnementen verschillen als gevolg van de toets regio en eindpunt, moet elk LUIS-eindpunt één eindpunt in het profiel zijn. 

1. Profiel maken met **[cmdlet Nieuw-AzTrafficManagerProfiel](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    Gebruik de volgende cmdlet om het profiel te maken. Zorg ervoor dat `appIdLuis` `subscriptionKeyLuis`u de en . De abonnementSleutel is voor de Oost-Amerikaanse LUIS-sleutel. Als het pad niet correct is, inclusief de LUIS-app-id en `degraded` eindpuntsleutel, is de polling van Traffic Manager een status van omdat Traffic Manage het LUIS-eindpunt niet kan aanvragen. Zorg ervoor dat `q` `traffic-manager-east` de waarde van is, zodat u deze waarde zien in de LUIS-eindpuntlogboeken.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Deze tabel legt elke variabele in de cmdlet uit:
    
    |Configuratieparameter|Variabele naam of waarde|Doel|
    |--|--|--|
    |-Name|luis-profiel-eastus|Naam verkeersbeheer in Azure-portal|
    |-ResourceGroupName|luis-traffic-manager|Gemaakt in vorige sectie|
    |-TrafficRoutingMethod|Prestaties|Zie [Routeringsmethoden voor verkeersbeheer][routing-methods]voor meer informatie . Als u de prestaties gebruikt, moet de URL-aanvraag voor de verkeersbeheerder afkomstig zijn uit de regio van de gebruiker. Als je een chatbot of andere applicatie doorloopt, is het de verantwoordelijkheid van de chatbot om de regio na te bootsen in de oproep aan de Traffic Manager. |
    |-RelativednsName|luis-dns-eastus|Dit is het subdomein voor de service: luis-dns-eastus.trafficmanager.net|
    |-Ttl|30|Polling interval, 30 seconden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Poort en protocol voor LUIS is HTTPS/443|
    |-MonitorPad|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Vervang `<appIdLuis>` `<subscriptionKeyLuis>` en met je eigen waarden.|
    
    Een succesvol verzoek heeft geen antwoord.

2. Oost-AMERIKAANS eindpunt toevoegen met **[add-AzTrafficManagerEndpointConfig-cmdlet](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Deze tabel legt elke variabele in de cmdlet uit:

    |Configuratieparameter|Variabele naam of waarde|Doel|
    |--|--|--|
    |-EndpointName|luis-oost-eindpunt|Eindpuntnaam weergegeven onder het profiel|
    |-TrafficManagerProfiel|$eastprofile|Profielobject gebruiken dat is gemaakt in stap 1|
    |-Type|Externe eindpunten|Zie [Eindpunt Verkeersbeheer voor][traffic-manager-endpoints] meer informatie |
    |-Doel|eastus.api.cognitive.microsoft.com|Dit is het domein voor het LUIS-eindpunt.|
    |-Eindpuntlocatie|"eastus"|Regio van het eindpunt|
    |-Eindpuntstatus|Ingeschakeld|Eindpunt inschakelen wanneer het wordt gemaakt|

    De succesvolle reactie ziet eruit als volgt:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-eastus
    Name                             : luis-profile-eastus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-eastus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/<luis-app-id>?subscription-key=f0517d185bcf467cba5147d6260bb868&q=traffic-manager-east
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-east-endpoint}
    ```

3. Oost-VS-eindpunt instellen met **[cmdlet Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Een succesvol antwoord zal dezelfde reactie zijn als stap 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>Het West US Traffic Manager-profiel maken met PowerShell
Als u het West US Traffic Manager-profiel wilt maken, voert u dezelfde stappen uit: profiel maken, eindpunt toevoegen en eindpunt instellen.

1. Profiel maken met **[cmdlet Nieuw-AzTrafficManagerProfiel](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    Gebruik de volgende cmdlet om het profiel te maken. Zorg ervoor dat `appIdLuis` `subscriptionKeyLuis`u de en . De abonnementSleutel is voor de Oost-Amerikaanse LUIS-sleutel. Als het pad niet correct is, inclusief de LUIS-app-id en `degraded` eindpuntsleutel, is de polling van Traffic Manager een status van omdat Traffic Manage het LUIS-eindpunt niet kan aanvragen. Zorg ervoor dat `q` `traffic-manager-west` de waarde van is, zodat u deze waarde zien in de LUIS-eindpuntlogboeken.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Deze tabel legt elke variabele in de cmdlet uit:
    
    |Configuratieparameter|Variabele naam of waarde|Doel|
    |--|--|--|
    |-Name|luis-profiel-westus|Naam verkeersbeheer in Azure-portal|
    |-ResourceGroupName|luis-traffic-manager|Gemaakt in vorige sectie|
    |-TrafficRoutingMethod|Prestaties|Zie [Routeringsmethoden voor verkeersbeheer][routing-methods]voor meer informatie . Als u de prestaties gebruikt, moet de URL-aanvraag voor de verkeersbeheerder afkomstig zijn uit de regio van de gebruiker. Als je een chatbot of andere applicatie doorloopt, is het de verantwoordelijkheid van de chatbot om de regio na te bootsen in de oproep aan de Traffic Manager. |
    |-RelativednsName|luis-dns-westus|Dit is het subdomein voor de service: luis-dns-westus.trafficmanager.net|
    |-Ttl|30|Polling interval, 30 seconden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Poort en protocol voor LUIS is HTTPS/443|
    |-MonitorPad|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Vervang `<appId>` `<subscriptionKey>` en met je eigen waarden. Onthoud deze eindpunttoets is anders dan de oosteindpunttoets|
    
    Een succesvol verzoek heeft geen antwoord.

2. West US-eindpunt toevoegen met **[add-AzTrafficManagerEndpointConfig-cmdlet](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Deze tabel legt elke variabele in de cmdlet uit:

    |Configuratieparameter|Variabele naam of waarde|Doel|
    |--|--|--|
    |-EndpointName|luis-west-eindpunt|Eindpuntnaam weergegeven onder het profiel|
    |-TrafficManagerProfiel|$westprofile|Profielobject gebruiken dat is gemaakt in stap 1|
    |-Type|Externe eindpunten|Zie [Eindpunt Verkeersbeheer voor][traffic-manager-endpoints] meer informatie |
    |-Doel|westus.api.cognitive.microsoft.com|Dit is het domein voor het LUIS-eindpunt.|
    |-Eindpuntlocatie|"Westus"|Regio van het eindpunt|
    |-Eindpuntstatus|Ingeschakeld|Eindpunt inschakelen wanneer het wordt gemaakt|

    De succesvolle reactie ziet eruit als volgt:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-westus
    Name                             : luis-profile-westus
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-westus
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /luis/v2.0/apps/c3fc5d1e-5187-40cc-af0f-fbde328aa16b?subscription-key=e3605f07e3cc4bedb7e02698a54c19cc&q=traffic-manager-west
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {luis-west-endpoint}
    ```

3. West US-eindpunt instellen met **[cmdlet Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Een succesvol antwoord is dezelfde reactie als stap 2.

### <a name="create-parent-traffic-manager-profile"></a>Bovenliggend Traffic Manager-profiel maken
Maak het bovenliggende Traffic Manager-profiel en koppel twee onderliggende Verkeersbeheerprofielen aan de ouder.

1. Bovenliggend profiel maken met **[cmdlet Nieuw-AzTrafficManagerProfiel](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Deze tabel legt elke variabele in de cmdlet uit:

    |Configuratieparameter|Variabele naam of waarde|Doel|
    |--|--|--|
    |-Name|luis-profiel-ouder|Naam verkeersbeheer in Azure-portal|
    |-ResourceGroupName|luis-traffic-manager|Gemaakt in vorige sectie|
    |-TrafficRoutingMethod|Prestaties|Zie [Routeringsmethoden voor verkeersbeheer][routing-methods]voor meer informatie . Als u de prestaties gebruikt, moet de URL-aanvraag voor de verkeersbeheerder afkomstig zijn uit de regio van de gebruiker. Als je een chatbot of andere applicatie doorloopt, is het de verantwoordelijkheid van de chatbot om de regio na te bootsen in de oproep aan de Traffic Manager. |
    |-RelativednsName|luis-dns-ouder|Dit is het subdomein voor de service: luis-dns-parent.trafficmanager.net|
    |-Ttl|30|Polling interval, 30 seconden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Poort en protocol voor LUIS is HTTPS/443|
    |-MonitorPad|`/`|Dit pad maakt niet uit omdat de onderliggende eindpuntpaden in plaats daarvan worden gebruikt.|

    Een succesvol verzoek heeft geen antwoord.

2. Oost-AMERIKAANS onderliggend profiel toevoegen aan ouder met **[add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** en **NestedEndpoints-type**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Deze tabel legt elke variabele in de cmdlet uit:

    |Configuratieparameter|Variabele naam of waarde|Doel|
    |--|--|--|
    |-EndpointName|kind-eindpunt-useast|Oostprofiel|
    |-TrafficManagerProfiel|$parentprofile|Profiel om dit eindpunt toe te wijzen aan|
    |-Type|Geneste eindpunten|Zie [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig)voor meer informatie. |
    |-TargetResourceId|$eastprofile. Id|ID van het onderliggende profiel|
    |-Eindpuntstatus|Ingeschakeld|Eindpuntstatus na toevoeging aan bovenliggende|
    |-Eindpuntlocatie|"eastus"|[Azure-regionaam](https://azure.microsoft.com/global-infrastructure/regions/) van resource|
    |-MinChildEndpoints|1|Minimumaantal voor onderliggende eindpunten|

    De succesvolle reactie ziet er als `child-endpoint-useast` volgt uit en bevat het nieuwe eindpunt:    

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast}
    ```

3. West US-onderliggend profiel toevoegen aan ouder met **[add-AzTrafficManagerEndpointConfig-cmdlet](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** en **NestedEndpoints-type**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Deze tabel legt elke variabele in de cmdlet uit:

    |Configuratieparameter|Variabele naam of waarde|Doel|
    |--|--|--|
    |-EndpointName|kind-eindpunt-uswest|West profiel|
    |-TrafficManagerProfiel|$parentprofile|Profiel om dit eindpunt toe te wijzen aan|
    |-Type|Geneste eindpunten|Zie [Add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig)voor meer informatie. |
    |-TargetResourceId|$westprofile. Id|ID van het onderliggende profiel|
    |-Eindpuntstatus|Ingeschakeld|Eindpuntstatus na toevoeging aan bovenliggende|
    |-Eindpuntlocatie|"Westus"|[Azure-regionaam](https://azure.microsoft.com/global-infrastructure/regions/) van resource|
    |-MinChildEndpoints|1|Minimumaantal voor onderliggende eindpunten|

    De succesvolle respons ziet eruit `child-endpoint-useast` en bevat zowel `child-endpoint-uswest` het vorige eindpunt als het nieuwe eindpunt:

    ```console
    Id                               : /subscriptions/<azure-subscription-id>/resourceGroups/luis-traffic-manager/providers/Microsoft.Network/trafficManagerProfiles/luis-profile-parent
    Name                             : luis-profile-parent
    ResourceGroupName                : luis-traffic-manager
    RelativeDnsName                  : luis-dns-parent
    Ttl                              : 30
    ProfileStatus                    : Enabled
    TrafficRoutingMethod             : Performance
    MonitorProtocol                  : HTTPS
    MonitorPort                      : 443
    MonitorPath                      : /
    MonitorIntervalInSeconds         : 30
    MonitorTimeoutInSeconds          : 10
    MonitorToleratedNumberOfFailures : 3
    Endpoints                        : {child-endpoint-useast, child-endpoint-uswest}
    ```

4. Eindpunten instellen met **[cmdlet Set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** 

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Een succesvol antwoord is dezelfde reactie als stap 3.

### <a name="powershell-variables"></a>PowerShell-variabelen
In de vorige secties werden drie PowerShell-variabelen gemaakt: `$eastprofile`, `$westprofile`, `$parentprofile` Deze variabelen worden gebruikt tegen het einde van de Traffic Manager-configuratie. Als u ervoor hebt gekozen de variabelen niet te maken, of vergeten, of uw PowerShell-venster uit, u de PowerShell-cmdlet, **[Get-AzTrafficManagerProfile,](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** gebruiken om het profiel opnieuw te krijgen en aan een variabele toe te wijzen. 

Vervang de items in `<>`hoekhaakjes, met de juiste waarden voor elk van de drie profielen die u nodig hebt. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Verkeersbeheerwerkzaamheden verifiëren
Om te controleren of de traffic manager-profielen werken, moeten de profielen de status `Online` Deze status hebben, is gebaseerd op het stempad van het eindpunt. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Nieuwe profielen weergeven in de Azure-portal
U controleren of alle drie de profielen `luis-traffic-manager` zijn gemaakt door te kijken naar de bronnen in de resourcegroep.

![Schermafbeelding van Luis-Traffic-Manager van Azure-bronnengroep](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Controleren of de profielstatus online is
De Traffic Manager peilt het pad van elk eindpunt om er zeker van te zijn dat het online is. Als het online is, is `Online`de status van de onderliggende profielen . Dit wordt weergegeven in het **overzicht** van elk profiel. 

![Schermafbeelding van Azure Traffic Manager-profieloverzicht met monitorstatus van online](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Polling-werkzaamheden voor traffic manager valideren
Een andere manier om de polling werken van de verkeersbeheerder te valideren, is met de LUIS-eindpuntlogboeken. Exporteer [LUIS][LUIS] op de pagina luis-website-apps het eindpuntlogboek voor de toepassing. Omdat Traffic Manager polls vaak voor de twee eindpunten, zijn er items in de logs, zelfs als ze slechts op een paar minuten. Vergeet niet om te zoeken naar `traffic-manager-`items waar de query begint met .

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>DNS-respons valideren vanuit Traffic Manager-werken
Als u wilt valideren dat het DNS-antwoord een LUIS-eindpunt retourneert, vraagt u het dns-profiel Verkeer beheren aan met behulp van een DNS-clientbibliotheek. De DNS-naam voor `luis-dns-parent.trafficmanager.net`het bovenliggende profiel is .

Met de volgende Node.js-code wordt een aanvraag voor het bovenliggende profiel ingediend en wordt een LUIS-eindpunt geretourneerd:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

De succesvolle reactie met het LUIS-eindpunt is:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Het bovenliggende profiel van Traffic Manager gebruiken
Als u verkeer over eindpunten wilt beheren, moet u een oproep invoegen naar de DNS-beheerder van De Verkeersbeheerder om het LUIS-eindpunt te vinden. Deze oproep wordt gedaan voor elke LUIS-eindpuntaanvraag en moet de geografische locatie van de gebruiker van de LUIS-clienttoepassing simuleren. Voeg de DNS-antwoordcode toe tussen uw LUIS-clienttoepassing en het verzoek aan LUIS voor de eindpuntvoorspelling. 

## <a name="resolving-a-degraded-state"></a>Een gedegradeerde status oplossen

Schakel [diagnostische logboeken](../../traffic-manager/traffic-manager-diagnostic-logs.md) voor Traffic Manager in om te zien waarom de status van eindpunt wordt afgebroken.

## <a name="clean-up"></a>Opruimen
Verwijder de twee LUIS-eindpunttoetsen, de drie Traffic Manager-profielen en de resourcegroep die deze vijf bronnen bevat. Dit gebeurt vanuit de Azure-portal. U verwijdert de vijf bronnen uit de lijst met bronnen. Verwijder vervolgens de brongroep. 

## <a name="next-steps"></a>Volgende stappen

Bekijk [middleware-opties](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) in BotFramework v4 om te begrijpen hoe deze verkeersbeheercode kan worden toegevoegd aan een BotFramework-bot. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
