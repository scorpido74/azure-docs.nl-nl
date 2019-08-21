---
title: Het Azure-activiteiten logboek exporteren
description: Azure-activiteiten logboek exporteren naar opslag voor archivering of Azure Event Hubs voor het exporteren buiten Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: d34040722ac8793fd4bbb02f2d3fa59247f8267c
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639640"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Azure-activiteiten logboek exporteren naar opslag of Azure Event Hubs
Het [Azure-activiteiten logboek](activity-logs-overview.md) biedt inzicht in gebeurtenissen op abonnements niveau die in uw Azure-abonnement zijn opgetreden. Naast het weer geven van het activiteiten logboek in de Azure Portal of het kopiëren naar een Log Analytics werk ruimte waar het kan worden geanalyseerd met andere gegevens die zijn verzameld door Azure Monitor, kunt u een logboek profiel maken om het activiteiten logboek te archiveren in een Azure Storage-account of om het te streamen naar een  Event hub.

## <a name="archive-activity-log"></a>Activiteiten logboek archiveren
Het archiveren van het activiteiten logboek naar een opslag account is handig als u uw logboek gegevens langer dan 90 dagen wilt bewaren (met volledige controle over het Bewaar beleid) voor controle, statische analyses of back-up. Als u uw gebeurtenissen gedurende 90 dagen wilt behouden of minder hoeft u geen archief in te stellen op een opslag account, omdat activiteiten in het Azure-platform gedurende 90 dagen worden bewaard.

## <a name="stream-activity-log-to-event-hub"></a>Activiteiten logboek streamen naar Event hub
[Azure Event hubs](/azure/event-hubs/) is een service voor het streamen van gegevens en gebeurtenissen, waarmee miljoenen gebeurtenissen per seconde kunnen worden ontvangen en verwerkt. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen door gebruik te maken van een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. U kunt de streaming-mogelijkheid voor het activiteiten logboek op twee manieren gebruiken:
* **Stroom naar logboek registratie van derden en telemetrie-systemen**: Na verloop van tijd wordt Azure Event Hubs streaming het mechanisme om uw activiteiten logboek te pipeen in Siem's-en log Analytics-oplossingen van derden.
* **Een aangepast telemetrie-en logboek registratie platform bouwen**: Als u al een aangepast telemetrie-platform hebt of als u er een wilt maken, kunt u het activiteiten logboek flexibel opnemen met de zeer schaal bare publicatie van Event Hubs. 

## <a name="prerequisites"></a>Vereisten

### <a name="storage-account"></a>Storage-account
Als u uw activiteiten logboek archiveert, moet u [een opslag account maken](../../storage/common/storage-quickstart-create-account.md) als u er nog geen hebt. Gebruik geen bestaand opslag account met andere, niet-bewakings gegevens die erin zijn opgeslagen, zodat u de toegang tot bewakings gegevens beter kunt beheren. Als u ook Diagnostische logboeken en metrische gegevens naar een opslag account archiveert, kunt u ervoor kiezen om hetzelfde opslag account te gebruiken om alle bewakings gegevens op een centrale locatie te bewaren.

Het opslag account hoeft zich niet in hetzelfde abonnement te benemen als het abonnement dat Logboeken verzendt, zolang de gebruiker die de instelling configureert de juiste RBAC-toegang heeft tot beide abonnementen.
> [!NOTE]
>  U kunt momenteel geen gegevens archiveren naar een opslag account dat zich achter een beveiligd virtueel netwerk bevindt.

### <a name="event-hubs"></a>Event Hubs
Als u uw activiteiten logboek naar een Event Hub verzendt, moet u [een event hub maken](../../event-hubs/event-hubs-create.md) als u er nog geen hebt. Als u eerder activiteiten logboek gebeurtenissen hebt gestreamd naar deze Event Hubs naam ruimte, wordt dat Event Hub opnieuw gebruikt.

Het beleid voor gedeelde toegang definieert de machtigingen die het streaming-mechanisme heeft. Streaming naar Event Hubs vereist machtigingen voor beheren, verzenden en Luis teren. U kunt gedeelde toegangs beleid voor de Event Hubs naam ruimte maken of wijzigen in de Azure Portal onder het tabblad configureren voor uw Event Hubs naam ruimte.

Als u het logboek profiel voor het activiteiten logboek wilt bijwerken zodat streaming wordt toegevoegd, moet u de machtiging ListKey hebben voor die Event Hubs autorisatie regel. De naam ruimte van de Event Hubs hoeft zich niet te bevinden in hetzelfde abonnement als het abonnement dat Logboeken verzendt, zolang de gebruiker die de instelling configureert, de juiste RBAC-toegang heeft tot beide abonnementen en beide abonnementen zich in dezelfde AAD-Tenant bevinden.

Het activiteiten logboek streamen naar een event hub door [een logboek profiel te maken](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Een logboek profiel maken
U bepaalt hoe uw Azure-activiteiten logboek wordt geëxporteerd met een **logboek profiel**. Elk Azure-abonnement kan slechts één logboek profiel hebben. Deze instellingen kunnen worden geconfigureerd via de optie **exporteren** op de Blade activiteiten logboek in de portal. Ze kunnen ook programmatisch worden geconfigureerd [met behulp van de Azure Monitor rest API](https://msdn.microsoft.com/library/azure/dn931927.aspx), Power shell-cmdlets of cli.

Het logboek profiel definieert het volgende.

**Waar het activiteiten logboek moet worden verzonden.** Momenteel zijn de beschik bare opties opslag account of Event Hubs.

**Welke gebeurtenis categorieën moeten worden verzonden.** De betekenis van *categorie* in logboek profielen en activiteiten logboek gebeurtenissen wijkt af. In het logboek profiel vertegenwoordigt de *categorie* het bewerkings type (schrijven, verwijderen, actie). In een activiteiten logboek gebeurtenis vertegenwoordigt de eigenschap *Category** de bron of het type gebeurtenis (bijvoorbeeld beheer, ServiceHealth en waarschuwing).

**Welke regio's (locaties) moeten worden geëxporteerd.** U moet alle locaties toevoegen omdat veel gebeurtenissen in het activiteiten logboek globale gebeurtenissen zijn.

**Hoe lang het activiteiten logboek moet worden bewaard in een opslag account.** Een bewaarperiode van nul dagen betekent dat Logboeken altijd worden bewaard. De waarde kan anders een willekeurig aantal dagen tussen 1 en 2147483647 zijn.

Als het Bewaar beleid is ingesteld, maar logboeken worden opgeslagen in een opslag account is uitgeschakeld, hebben het Bewaar beleid geen effect. Bewaarbeleid zijn toegepast per dag, dus aan het einde van een dag (UTC), logboeken van de dag dat nu is buiten de bewaarperiode van beleid worden verwijderd. Bijvoorbeeld, als u een beleid voor het bewaren van één dag had, worden aan het begin van de dag vandaag nog de logboeken van de dag voor gisteren vernietigd. De verwijderbewerking begint bij middernacht UTC, maar houd er rekening mee dat het kan tot 24 uur duren voor de logboeken worden verwijderd uit uw storage-account.



> [!WARNING]
> De indeling van de logboek gegevens in het opslag account is gewijzigd in JSON-regels op nov. 1, 2018. [Raadpleeg dit artikel voor een beschrijving van de gevolgen en hoe u uw tooling kunt bijwerken om de nieuwe indeling te verwerken. ](diagnostic-logs-append-blobs.md)


> [!IMPORTANT]
> Er wordt mogelijk een fout bericht weer gegeven bij het maken van een logboek profiel als de resource provider micro soft. Insights niet is geregistreerd. Zie [Azure-resource providers en-typen](../../azure-resource-manager/resource-manager-supported-services.md) om deze provider te registreren.

### <a name="create-log-profile-using-the-azure-portal"></a>Een logboek profiel maken met behulp van de Azure Portal

Maak of bewerk een logboek profiel met de optie **exporteren naar Event hub** in de Azure Portal.

1. Selecteer in het menu **monitor** in de Azure Portal **exporteren naar Event hub**.

    ![De knop exporteren in de portal](media/activity-log-export/portal-export.png)

3. Op de Blade die wordt weer gegeven, geeft u het volgende op:
   * Regio's met de gebeurtenissen die moeten worden geëxporteerd. U moet alle regio's selecteren om ervoor te zorgen dat u geen sleutel gebeurtenissen mist omdat het activiteiten logboek een globaal (niet-regionaal) logboek is en dus er aan de meeste gebeurtenissen geen regio is gekoppeld. 
   * Als u naar het opslag account wilt schrijven:
       * Het opslag account waarnaar u gebeurtenissen wilt opslaan.
       * Het aantal dagen dat u deze gebeurtenissen in de opslag ruimte wilt behouden. Met een instelling van 0 dagen worden de logboeken voor altijd bewaard.
   * Als u naar Event Hub wilt schrijven:
       * De Service Bus naam ruimte waarin een event hub moet worden gemaakt voor het streamen van deze gebeurtenissen.

     ![Blade activiteiten logboek exporteren](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Klik op **Opslaan** om deze instellingen op te slaan. De instellingen worden onmiddellijk toegepast op uw abonnement.


### <a name="configure-log-profile-using-powershell"></a>Logboek profiel configureren met Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als er al een logboek profiel bestaat, moet u eerst het bestaande logboek profiel verwijderen en vervolgens een nieuwe maken.

1. Gebruiken `Get-AzLogProfile` om te bepalen of er een logboek profiel bestaat.  Als er een logboek profiel bestaat, noteert u de eigenschap *name* .

1. Gebruik `Remove-AzLogProfile` om het logboek profiel te verwijderen met de waarde van de eigenschap *name* .

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Gebruiken `Add-AzLogProfile` om een nieuw logboek profiel te maken:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Eigenschap | Vereist | Description |
    | --- | --- | --- |
    | Name |Ja |De naam van het logboek profiel. |
    | StorageAccountId |Nee |De resource-ID van het opslag account waarin het activiteiten logboek moet worden opgeslagen. |
    | serviceBusRuleId |Nee |Service Bus regel-ID voor de Service Bus naam ruimte waarin u Event hubs wilt maken. Dit is een teken reeks met de volgende `{service bus resource ID}/authorizationrules/{key name}`indeling:. |
    | Location |Ja |Een door komma's gescheiden lijst met regio's waarvoor u activiteiten logboek gebeurtenissen wilt verzamelen. |
    | RetentionInDays |Ja |Aantal dagen dat gebeurtenissen moeten worden bewaard in het opslag account, tussen 1 en 2147483647. Met de waarde nul worden de logboeken voor onbepaalde tijd opgeslagen. |
    | Categorie |Nee |Een door komma's gescheiden lijst met gebeurtenis categorieën die moeten worden verzameld. Mogelijke waarden zijn _schrijven_, _verwijderen_en _actie_. |

### <a name="example-script"></a>Voorbeeldscript
Hier volgt een voor beeld van een Power shell-script voor het maken van een logboek profiel waarmee het activiteiten logboek naar zowel een opslag account als Event Hub wordt geschreven.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Logboek profiel configureren met behulp van Azure CLI

Als er al een logboek profiel bestaat, moet u eerst het bestaande logboek profiel verwijderen en vervolgens een nieuw logboek profiel maken.

1. Gebruiken `az monitor log-profiles list` om te bepalen of er een logboek profiel bestaat.
2. Gebruik `az monitor log-profiles delete --name "<log profile name>` om het logboek profiel te verwijderen met de waarde van de eigenschap *name* .
3. Gebruiken `az monitor log-profiles create` om een nieuw logboek profiel te maken:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Eigenschap | Vereist | Description |
    | --- | --- | --- |
    | name |Ja |De naam van het logboek profiel. |
    | storage-account-id |Ja |De resource-ID van het opslag account waarnaar de activiteiten logboeken moeten worden opgeslagen. |
    | locations |Ja |Een door spaties gescheiden lijst met regio's waarvoor u activiteiten logboek gebeurtenissen wilt verzamelen. U kunt een lijst weer geven met alle regio's voor uw abonnement `az account list-locations --query [].name`met behulp van. |
    | days |Ja |Aantal dagen dat gebeurtenissen moeten worden bewaard, tussen 1 en 365. Met de waarde nul worden de logboeken voor onbepaalde tijd opgeslagen (permanent).  Als de waarde nul is, moet de para meter ingeschakeld worden ingesteld op True. |
    |enabled | Ja |Waar of ONWAAR.  Wordt gebruikt om het Bewaar beleid in of uit te scha kelen.  Indien waar, moet de para meter Days een waarde zijn die groter is dan 0.
    | categories |Ja |Een door spaties gescheiden lijst met gebeurtenis categorieën die moeten worden verzameld. Mogelijke waarden zijn schrijven, verwijderen en actie. |



## <a name="activity-log-schema"></a>Schema activiteiten logboek
Of er naar Azure Storage of event hub worden verzonden, worden de gegevens van het activiteiten logboek naar JSON geschreven met de volgende indeling.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```
De elementen in deze JSON worden in de volgende tabel beschreven.

| De naam van element | Description |
| --- | --- |
| time |Tijds tempel wanneer de gebeurtenis is gegenereerd door de Azure-service die de aanvraag verwerkt die overeenkomt met de gebeurtenis. |
| resourceId |De resource-ID van de betrokken resource. |
| operationName |Naam van de bewerking. |
| category |De categorie van de actie, bijvoorbeeld Schrijven, lezen, actie. |
| resultType |Het type resultaat, bijvoorbeeld. Geslaagd, mislukt, starten |
| resultSignature |Afhankelijk van het resource type. |
| durationMs |Duur van de bewerking in milliseconden |
| callerIpAddress |IP-adres van de gebruiker die de bewerking, UPN-claim of SPN-claim heeft uitgevoerd op basis van Beschik baarheid. |
| correlationId |Meestal een GUID in de teken reeks indeling. Gebeurtenissen die een correlationId delen, horen bij dezelfde uber-actie. |
| identity |JSON-BLOB waarmee de autorisatie en claims worden beschreven. |
| authorization |BLOB van RBAC-eigenschappen van de gebeurtenis. Bevat meestal de eigenschappen ' Action ', ' Role ' en ' scope '. |
| level |Het niveau van de gebeurtenis. Een van de volgende waarden: _Kritiek_, _fout_, _waarschuwing_, _informatief_en _uitgebreid_ |
| location |De regio waarin de locatie is opgetreden (of algemeen). |
| properties |`<Key, Value>` Set paren (bijvoorbeeld woorden lijst) waarin de details van de gebeurtenis worden beschreven. |

> [!NOTE]
> De eigenschappen en het gebruik van deze eigenschappen kunnen variëren, afhankelijk van de resource.



## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het activiteiten logboek](../../azure-resource-manager/resource-group-audit.md)
* [Activiteiten logboek in Azure Monitor logboeken verzamelen](activity-log-collect.md)
