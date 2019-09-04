---
title: Eindpunt quotum verhogen-LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) biedt de mogelijkheid om het quotum van de aanvraag eindpunt meer dan één sleutel quotum te verhogen. Dit wordt gedaan door het maken van meer sleutels voor LUIS en deze toevoegen aan de LUIS-toepassing op de **publiceren** pagina in de **Resources en sleutels** sectie.
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
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256609"
---
# <a name="use-microsoft-azure-traffic-manager-to-manage-endpoint-quota-across-keys"></a>Microsoft Azure Traffic Manager gebruiken voor het beheer van eindpunt quotum voor sleutels
Language Understanding (LUIS) biedt de mogelijkheid om het quotum van de aanvraag eindpunt meer dan één sleutel quotum te verhogen. Dit wordt gedaan door het maken van meer sleutels voor LUIS en deze toevoegen aan de LUIS-toepassing op de **publiceren** pagina in de **Resources en sleutels** sectie. 

De clienttoepassing heeft voor het beheren van het verkeer over de sleutels. LUIS doen dat niet. 

In dit artikel wordt uitgelegd hoe u het verkeer beheert over sleutels met Azure [Traffic Manager][traffic-manager-marketing]. U moet al een getraind en gepubliceerde LUIS-app hebben. Als u een hebt, volgt u de vooraf gedefinieerde domein [snelstartgids](luis-get-started-create-app.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connect-to-powershell-in-the-azure-portal"></a>Verbinding maken met PowerShell in Azure portal
Open in [Azure][azure-portal] Portal het Power shell-venster. Het pictogram voor de PowerShell-venster is de **> _** in de bovenste navigatiebalk. Met behulp van PowerShell via de portal, krijgt u de meest recente versie van PowerShell en u bent geverifieerd. PowerShell in de portal vereist een [Azure Storage](https://azure.microsoft.com/services/storage/) account. 

![Schermopname van het Azure-portal met Powershell-venster openen](./media/traffic-manager/azure-portal-powershell.png)

De volgende secties [Traffic Manager PowerShell-cmdlets](https://docs.microsoft.com/powershell/module/az.trafficmanager/#traffic_manager).

## <a name="create-azure-resource-group-with-powershell"></a>Azure-resourcegroep maken met PowerShell
Voordat u de Azure-resources maakt, maakt u een resourcegroep voor alle resources bevatten. Naam van de resourcegroep `luis-traffic-manager` en gebruik de regio is `West US`. De regio van de resourcegroep slaat metagegevens op over de groep. Het wordt niet uw resources vertragen als ze zich in een andere regio. 

Een resource groep maken met de cmdlet **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** :

```powerShell
New-AzResourceGroup -Name luis-traffic-manager -Location "West US"
```

## <a name="create-luis-keys-to-increase-total-endpoint-quota"></a>LUIS-sleutels om totale eindpunt quotum te verhogen maken
1. In de Azure-portal, maakt u twee **Language Understanding** sleutels, één in de `West US` en één in de `East US`. Gebruik de bestaande resourcegroep, in de vorige sectie hebt gemaakt met de naam `luis-traffic-manager`. 

    ![Schermopname van het Azure-portal met twee LUIS sleutels in de resourcegroep van luis-traffic-manager](./media/traffic-manager/luis-keys.png)

2. Op de [Luis][LUIS] -website, in de sectie **beheren** op de pagina **Azure-resources** , wijst u sleutels toe aan de app en publiceert u de app opnieuw door de knop **publiceren** te selecteren in het menu rechtsboven. 

    De voorbeeld-URL in de **eindpunt** kolom gebruikt een GET-aanvraag met de eindpuntsleutel als een queryparameter. Kopieer de twee nieuwe sleutels eindpunt-URL's. Ze worden gebruikt als onderdeel van de configuratie van Traffic Manager verderop in dit artikel.

## <a name="manage-luis-endpoint-requests-across-keys-with-traffic-manager"></a>LUIS-eindpunt aanvragen beheren via sleutels met Traffic Manager
Traffic Manager maakt een nieuwe DNS-toegangspunt voor uw eindpunten. Deze heeft niet fungeren als gateway of proxy, maar alleen op DNS-niveau. In dit voorbeeld verandert niet de DNS-records. Hierbij wordt een DNS-bibliotheek om te communiceren met Traffic Manager om op te halen van het juiste eindpunt voor die specifieke aanvraag. _Elke_ aanvraag die bestemd zijn voor LUIS, moet u eerst een Traffic Manager-verzoek om te bepalen welk eindpunt LUIS te gebruiken. 

### <a name="polling-uses-luis-endpoint"></a>Polling maakt gebruik van LUIS-eindpunt
Traffic Manager controleert of de eindpunten regelmatig te controleren of dat het eindpunt is nog steeds beschikbaar. De URL van Traffic Manager gepeild moet toegankelijk zijn met een GET-aanvraag en een 200 retourneren. De eindpunt-URL op de **publiceren** pagina komt dit. Omdat elke eindpuntsleutel een andere route en queryreeksparameters heeft, moet elke eindpuntsleutel een verschillende polling-pad. Telkens wanneer Traffic Manager worden opgevraagd, een quotumaanvraag kosten. De queryreeks-parameter **q** van de LUIS-eindpunt is de utterance verzonden naar LUIS. Deze parameter, in plaats van een utterance dat wordt gebruikt om toe te voegen aan het logboek van LUIS-eindpunt polling van Traffic Manager als een techniek voor foutopsporing tijdens het ophalen van Traffic Manager geconfigureerd.

Omdat elke LUIS-eindpunt een eigen pad moet, moet een eigen Traffic Manager-profiel. Als u wilt beheren voor alle profielen, maakt u een [ _geneste_ Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-nested-profiles) architectuur. Een bovenliggende profiel verwijst naar de onderliggende items profielen en verkeer via deze beheren.

Nadat de Traffic Manager is geconfigureerd, moet u het pad voor het gebruik van de logboekregistratie = false queryreeks-parameter, zodat uw logboek niet vol met polling raakt.

## <a name="configure-traffic-manager-with-nested-profiles"></a>Traffic Manager configureren met geneste profielen
De volgende secties maakt twee onderliggende profielen, één voor de sleutel Oost LUIS en één voor de sleutel West LUIS. Vervolgens wordt een bovenliggende-profiel gemaakt en de twee onderliggende profielen worden toegevoegd aan het profiel van de bovenliggende. 

### <a name="create-the-east-us-traffic-manager-profile-with-powershell"></a>VS-Oost Traffic Manager-profiel maken met PowerShell
Voor het maken van de VS-Oost Traffic Manager-profiel, zijn er verschillende stappen: profiel maken, het eindpunt toevoegen en het eindpunt instellen. Een Traffic Manager-profiel kan veel eindpunten hebben, maar elk eindpunt heeft hetzelfde validatiepad. Omdat de LUIS eindpunt-URL's voor de Oost- en -abonnementen verschillen vanwege regio en het eindpunt sleutel, is elk eindpunt LUIS één eindpunt in het profiel. 

1. Profiel maken met de cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile)**

    Gebruik de volgende cmdlet om het profiel te maken. Zorg ervoor dat u wijzigt de `appIdLuis` en `subscriptionKeyLuis`. De subscriptionKey is voor de sleutel East US LUIS. Als het pad niet juist is is, met inbegrip van de LUIS-app-ID en het eindpunt sleutel, de polling Traffic Manager is een status van `degraded` omdat Traffic Manager het eindpunt LUIS is geen aanvragen. Zorg ervoor dat de waarde van `q` is `traffic-manager-east` zodat u deze waarde in de logboeken van LUIS-eindpunt kunt zien.

    ```powerShell
    $eastprofile = New-AzTrafficManagerProfile -Name luis-profile-eastus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-eastus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appID>?subscription-key=<subscriptionKey>&q=traffic-manager-east"
    ```
    
    Deze tabel bevat uitleg over iedere variabele in de cmdlet:
    
    |Configuratieparameter|Naam of waarde variabele|Doel|
    |--|--|--|
    |-Naam|Luis-profiel-VS Oost|Naam van Traffic Manager in Azure portal|
    |-ResourceGroupName|Luis-traffic-manager|In de vorige sectie hebt gemaakt|
    |-TrafficRoutingMethod|Prestaties|Zie [Traffic Manager routerings methoden][routing-methods]voor meer informatie. Als prestaties wordt gebruikt, moet de URL-aanvraag naar de Traffic Manager afkomstig zijn van de regio van de gebruiker. Als een chatbot of andere toepassing doorlopen, is de verantwoordelijkheid van de chatbot om na te bootsen van de regio in de aanroep naar de Traffic Manager. |
    |-RelativeDnsName|Luis-dns-VS Oost|Dit is het subdomein voor de service: luis-dns-eastus.trafficmanager.net|
    |-Ttl|30|Polling-interval, 30 seconden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Poorten en protocollen voor LUIS is HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-east`|Vervang `<appIdLuis>` en `<subscriptionKeyLuis>` door uw eigen waarden.|
    
    Een geslaagde aanvraag heeft geen reactie.

2. Onderstaand Oost-eind punt toevoegen met de cmdlet **[add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanagerendpointconfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-east-endpoint -TrafficManagerProfile $eastprofile -Type ExternalEndpoints -Target eastus.api.cognitive.microsoft.com -EndpointLocation "eastus" -EndpointStatus Enabled
    ```
    Deze tabel bevat uitleg over iedere variabele in de cmdlet:

    |Configuratieparameter|Naam of waarde variabele|Doel|
    |--|--|--|
    |-EndpointName|Luis-Oost-eindpunt|Eindpuntnaam weergegeven onder het profiel|
    |-TrafficManagerProfile|$eastprofile|Profiel-object gemaakt in stap 1 gebruiken|
    |-Type|ExternalEndpoints|Zie [Traffic Manager-eind punt][traffic-manager-endpoints] voor meer informatie. |
    |-Doel|eastus.API.cognitive.Microsoft.com|Dit is het domein voor het eindpunt van LUIS.|
    |-EndpointLocation|"VS-Oost|De regio van het eindpunt|
    |-EndpointStatus|Ingeschakeld|Eindpunt in te schakelen wanneer deze is gemaakt|

    De geslaagde respons ziet eruit zoals:

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

3. Het VS-eind punt voor Oost instellen met de cmdlet **[set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerprofile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $eastprofile
    ```

    Een geslaagde reactie is hetzelfde antwoord als stap 2.

### <a name="create-the-west-us-traffic-manager-profile-with-powershell"></a>VS-West Traffic Manager-profiel maken met PowerShell
Volg dezelfde stappen voor het maken van de VS-West Traffic Manager-profiel: profiel maken, het eindpunt toevoegen en het eindpunt instellen.

1. Profiel maken met de cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    Gebruik de volgende cmdlet om het profiel te maken. Zorg ervoor dat u wijzigt de `appIdLuis` en `subscriptionKeyLuis`. De subscriptionKey is voor de sleutel East US LUIS. Als het pad niet correct met inbegrip van de LUIS-app-sleutel-ID en -eindpunt is, de polling Traffic Manager is een status van `degraded` omdat Traffic Manager het eindpunt LUIS is geen aanvragen. Zorg ervoor dat de waarde van `q` is `traffic-manager-west` zodat u deze waarde in de logboeken van LUIS-eindpunt kunt zien.

    ```powerShell
    $westprofile = New-AzTrafficManagerProfile -Name luis-profile-westus -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-westus -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west"
    ```
    
    Deze tabel bevat uitleg over iedere variabele in de cmdlet:
    
    |Configuratieparameter|Naam of waarde variabele|Doel|
    |--|--|--|
    |-Naam|Luis-profiel-VS West|Naam van Traffic Manager in Azure portal|
    |-ResourceGroupName|Luis-traffic-manager|In de vorige sectie hebt gemaakt|
    |-TrafficRoutingMethod|Prestaties|Zie [Traffic Manager routerings methoden][routing-methods]voor meer informatie. Als prestaties wordt gebruikt, moet de URL-aanvraag naar de Traffic Manager afkomstig zijn van de regio van de gebruiker. Als een chatbot of andere toepassing doorlopen, is de verantwoordelijkheid van de chatbot om na te bootsen van de regio in de aanroep naar de Traffic Manager. |
    |-RelativeDnsName|Luis-dns-VS West|Dit is het subdomein voor de service: luis-dns-westus.trafficmanager.net|
    |-Ttl|30|Polling-interval, 30 seconden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Poorten en protocollen voor LUIS is HTTPS/443|
    |-MonitorPath|`/luis/v2.0/apps/<appIdLuis>?subscription-key=<subscriptionKeyLuis>&q=traffic-manager-west`|Vervang `<appId>` en `<subscriptionKey>` door uw eigen waarden. Vergeet niet dat deze eindpuntsleutel is anders dan de eindpuntsleutel Oost|
    
    Een geslaagde aanvraag heeft geen reactie.

2. Een eind punt voor de VS West toevoegen met de cmdlet **[add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName luis-west-endpoint -TrafficManagerProfile $westprofile -Type ExternalEndpoints -Target westus.api.cognitive.microsoft.com -EndpointLocation "westus" -EndpointStatus Enabled
    ```

    Deze tabel bevat uitleg over iedere variabele in de cmdlet:

    |Configuratieparameter|Naam of waarde variabele|Doel|
    |--|--|--|
    |-EndpointName|Luis-west-eindpunt|Eindpuntnaam weergegeven onder het profiel|
    |-TrafficManagerProfile|$westprofile|Profiel-object gemaakt in stap 1 gebruiken|
    |-Type|ExternalEndpoints|Zie [Traffic Manager-eind punt][traffic-manager-endpoints] voor meer informatie. |
    |-Doel|westus.API.cognitive.Microsoft.com|Dit is het domein voor het eindpunt van LUIS.|
    |-EndpointLocation|'westus'|De regio van het eindpunt|
    |-EndpointStatus|Ingeschakeld|Eindpunt in te schakelen wanneer deze is gemaakt|

    De geslaagde respons ziet eruit zoals:

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

3. Het eind punt voor de Verenigde Staten instellen met de cmdlet **[set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)**

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $westprofile
    ```

    Een geslaagde reactie is hetzelfde antwoord als stap 2.

### <a name="create-parent-traffic-manager-profile"></a>Bovenliggende Traffic Manager-profiel maken
Maken van de bovenliggende Traffic Manager-profiel en twee onderliggende Traffic Manager-profielen koppelen aan het bovenliggende item.

1. Bovenliggend profiel maken met de cmdlet **[New-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/New-azTrafficManagerProfile)**

    ```powerShell
    $parentprofile = New-AzTrafficManagerProfile -Name luis-profile-parent -ResourceGroupName luis-traffic-manager -TrafficRoutingMethod Performance -RelativeDnsName luis-dns-parent -Ttl 30 -MonitorProtocol HTTPS -MonitorPort 443 -MonitorPath "/"
    ```

    Deze tabel bevat uitleg over iedere variabele in de cmdlet:

    |Configuratieparameter|Naam of waarde variabele|Doel|
    |--|--|--|
    |-Naam|Luis-profiel-bovenliggende|Naam van Traffic Manager in Azure portal|
    |-ResourceGroupName|Luis-traffic-manager|In de vorige sectie hebt gemaakt|
    |-TrafficRoutingMethod|Prestaties|Zie [Traffic Manager routerings methoden][routing-methods]voor meer informatie. Als prestaties wordt gebruikt, moet de URL-aanvraag naar de Traffic Manager afkomstig zijn van de regio van de gebruiker. Als een chatbot of andere toepassing doorlopen, is de verantwoordelijkheid van de chatbot om na te bootsen van de regio in de aanroep naar de Traffic Manager. |
    |-RelativeDnsName|Luis-dns-bovenliggende|Dit is het subdomein voor de service: luis-dns-parent.trafficmanager.net|
    |-Ttl|30|Polling-interval, 30 seconden|
    |-MonitorProtocol<BR>-MonitorPort|HTTPS<br>443|Poorten en protocollen voor LUIS is HTTPS/443|
    |-MonitorPath|`/`|Dit pad niet van belang omdat de onderliggende eindpunt paden in plaats daarvan worden gebruikt.|

    Een geslaagde aanvraag heeft geen reactie.

2. Onderliggend Oost-onderliggend profiel toevoegen aan bovenliggend item met **[het type add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** en **NestedEndpoints**

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-useast -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $eastprofile.Id -EndpointStatus Enabled -EndpointLocation "eastus" -MinChildEndpoints 1
    ```

    Deze tabel bevat uitleg over iedere variabele in de cmdlet:

    |Configuratieparameter|Naam of waarde variabele|Doel|
    |--|--|--|
    |-EndpointName|onderliggende-endpoint-useast|Oost-profiel|
    |-TrafficManagerProfile|$parentprofile|Profiel dat u wilt dit eindpunt aan toewijzen|
    |-Type|NestedEndpoints|Zie [add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig)voor meer informatie. |
    |-TargetResourceId|$eastprofile. ID|ID van het onderliggende profiel|
    |-EndpointStatus|Ingeschakeld|Eindpuntstatus na het toevoegen van bovenliggende|
    |-EndpointLocation|"VS-Oost|[De naam van de Azure-regio](https://azure.microsoft.com/global-infrastructure/regions/) van resource|
    |-MinChildEndpoints|1|Minimum aantal onderliggende eindpunten|

    Het uiterlijk geslaagd antwoord als volgt uit en bevat de nieuwe `child-endpoint-useast` eindpunt:    

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

3. Onderliggend West-onderliggend profiel toevoegen aan bovenliggend item met de cmdlet **[add-AzTrafficManagerEndpointConfig en het](https://docs.microsoft.com/powershell/module/az.TrafficManager/Add-azTrafficManagerEndpointConfig)** **NestedEndpoints** -type

    ```powerShell
    Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint-uswest -TrafficManagerProfile $parentprofile -Type NestedEndpoints -TargetResourceId $westprofile.Id -EndpointStatus Enabled -EndpointLocation "westus" -MinChildEndpoints 1
    ```

    Deze tabel bevat uitleg over iedere variabele in de cmdlet:

    |Configuratieparameter|Naam of waarde variabele|Doel|
    |--|--|--|
    |-EndpointName|onderliggende-endpoint-uswest|West-profiel|
    |-TrafficManagerProfile|$parentprofile|Profiel dat u wilt dit eindpunt aan toewijzen|
    |-Type|NestedEndpoints|Zie [add-AzTrafficManagerEndpointConfig](https://docs.microsoft.com/powershell/module/az.trafficmanager/Add-azTrafficManagerEndpointConfig)voor meer informatie. |
    |-TargetResourceId|$westprofile. ID|ID van het onderliggende profiel|
    |-EndpointStatus|Ingeschakeld|Eindpuntstatus na het toevoegen van bovenliggende|
    |-EndpointLocation|'westus'|[De naam van de Azure-regio](https://azure.microsoft.com/global-infrastructure/regions/) van resource|
    |-MinChildEndpoints|1|Minimum aantal onderliggende eindpunten|

    Het uiterlijk geslaagd antwoord, zoals en bevat zowel de vorige `child-endpoint-useast` eindpunt en de nieuwe `child-endpoint-uswest` eindpunt:

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

4. Eind punten instellen met de cmdlet **[set-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Set-azTrafficManagerProfile)** 

    ```powerShell
    Set-AzTrafficManagerProfile -TrafficManagerProfile $parentprofile
    ```

    Een geslaagde reactie is hetzelfde antwoord als stap 3.

### <a name="powershell-variables"></a>PowerShell-variabelen
In de vorige secties, drie PowerShell-variabelen zijn gemaakt: `$eastprofile`, `$westprofile`, `$parentprofile`. Deze variabelen worden aan het einde van de configuratie van Traffic Manager gebruikt. Als u ervoor hebt gekozen om de variabelen niet te maken of verg eten of het Power shell-venster een time-out heeft, kunt u de Power shell-cmdlet **[Get-AzTrafficManagerProfile](https://docs.microsoft.com/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile)** gebruiken om het profiel opnieuw op te halen en toe te wijzen aan een variabele. 

Vervang de items in de vierkante haken, `<>`, met de juiste waarden voor elk van de drie profielen die u nodig hebt. 

```powerShell
$<variable-name> = Get-AzTrafficManagerProfile -Name <profile-name> -ResourceGroupName luis-traffic-manager
```

## <a name="verify-traffic-manager-works"></a>Controleer of Traffic Manager werkt
Om te verifiëren of de Traffic Manager-profielen werken de profielen moeten beschikken over de status van `Online` deze status is gebaseerd op het polling-pad van het eindpunt. 

### <a name="view-new-profiles-in-the-azure-portal"></a>Nieuwe profielen weergeven in Azure portal
U kunt controleren of alle drie de profielen zijn gemaakt door te kijken naar de resources in de `luis-traffic-manager` resourcegroep.

![Schermopname van het Azure resource group luis-traffic-manager](./media/traffic-manager/traffic-manager-profiles.png)

### <a name="verify-the-profile-status-is-online"></a>Controleer of dat de status van het profiel is Online
Traffic Manager controleert of het pad van elk eindpunt om te controleren of dat deze online is. Als deze online is, wordt de status van de onderliggende profielen zijn `Online`. Dit wordt weergegeven op de **overzicht** van elk profiel. 

![Schermopname van Azure Traffic Manager-profiel met Monitor de Status van de Online overzicht](./media/traffic-manager/profile-status-online.png)

### <a name="validate-traffic-manager-polling-works"></a>Traffic Manager werkt polling valideren
Een andere manier om te valideren van traffic manager werkt polling is met de LUIS-eindpunt-Logboeken. Op de pagina lijst met [Luis][LUIS] website-apps exporteert u het eindpunt logboek voor de toepassing. Omdat het Traffic Manager haalt vaak van de twee eindpunten, zijn er items in de logboeken, zelfs als ze alleen aan een paar minuten zijn. Vergeet niet om te zoeken naar gegevens, waarbij de query met begint `traffic-manager-`.

```console
traffic-manager-west    6/7/2018 19:19  {"query":"traffic-manager-west","intents":[{"intent":"None","score":0.944767}],"entities":[]}
traffic-manager-east    6/7/2018 19:20  {"query":"traffic-manager-east","intents":[{"intent":"None","score":0.944767}],"entities":[]}
```

### <a name="validate-dns-response-from-traffic-manager-works"></a>Valideren van de DNS-antwoord van Traffic Manager werkt
Verzoeken om te valideren dat de DNS-antwoord een LUIS-eindpunt retourneert, het profiel in Traffic Manager bovenliggende DNS met behulp van een DNS-client-bibliotheek. De DNS-naam voor het profiel van de bovenliggende `luis-dns-parent.trafficmanager.net`.

De volgende Node.js-code dient een aanvraag voor het profiel van de bovenliggende en retourneert een LUIS-eindpunt:

```javascript
const dns = require('dns');

dns.resolveAny('luis-dns-parent.trafficmanager.net', (err, ret) => {
  console.log('ret', ret);
});
```

De geslaagde respons met het eindpunt van LUIS is:

```json
[
    {
        value: 'westus.api.cognitive.microsoft.com', 
        type: 'CNAME'
    }
]
```

## <a name="use-the-traffic-manager-parent-profile"></a>Het profiel van de bovenliggende Traffic Manager gebruiken
Om het verkeer over alle eindpunten beheren, moet u een aanroep naar de DNS Traffic Manager van de LUIS-eindpunt vinden plaats. Deze aanroep is gemaakt voor elke aanvraag van LUIS-eindpunt en moet de geografische locatie van de gebruiker van de clienttoepassing LUIS simuleren. De DNS-responscode tussen uw LUIS-clienttoepassing en de aanvraag toevoegen aan LUIS voor de voorspelling van het eindpunt. 

## <a name="resolving-a-degraded-state"></a>Een gedegradeerde status oplossen

Schakel [Diagnostische logboeken](../../traffic-manager/traffic-manager-diagnostic-logs.md) in voor Traffic Manager om te zien waarom de eind punt status is gedegradeerd.

## <a name="clean-up"></a>Opruimen
De twee sleutels van LUIS-eindpunt, de drie Traffic Manager-profielen en de resourcegroep die deel uitmaakt van deze vijf resources verwijderen. Dit wordt gedaan via de Azure-portal. U kunt de vijf resources verwijderen uit de lijst met resources. Verwijder de resourcegroep. 

## <a name="next-steps"></a>Volgende stappen

Beoordeling [middleware](https://docs.microsoft.com/azure/bot-service/bot-builder-create-middleware?view=azure-bot-service-4.0&tabs=csaddmiddleware%2Ccsetagoverwrite%2Ccsmiddlewareshortcircuit%2Ccsfallback%2Ccsactivityhandler) opties in het BotFramework v4 om te begrijpen hoe deze code van verkeer management kan worden toegevoegd aan een bot BotFramework. 

[traffic-manager-marketing]: https://azure.microsoft.com/services/traffic-manager/
[traffic-manager-docs]: https://docs.microsoft.com/azure/traffic-manager/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[azure-portal]: https://portal.azure.com/
[azure-storage]: https://azure.microsoft.com/services/storage/
[routing-methods]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods
[traffic-manager-endpoints]: https://docs.microsoft.com/azure/traffic-manager/traffic-manager-endpoint-types
