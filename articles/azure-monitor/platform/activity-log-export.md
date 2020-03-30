---
title: Het Azure-activiteitenlogboek exporteren
description: Azure Activity-logboek exporteren naar opslag voor archivering of Azure Event Hubs voor exporteren buiten Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: edaa585ffb3448a80b021aa924a9d654ac829931
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096285"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Azure-activiteitslogboek exporteren naar opslag of Azure-gebeurtenishubs

> [!IMPORTANT]
> De methode voor het verzenden van het Azure Activity-logboek naar Azure Storage en Azure Event Hubs is gewijzigd in [diagnostische instellingen.](diagnostic-settings.md) Dit artikel beschrijft de legacy-methode die in het proces van wordt afgeschaft. Zie Bijwerken naar [azure activity log collection en exporteren](diagnostic-settings-legacy.md) voor een vergelijking.


Het [Azure-activiteitenlogboek](platform-logs-overview.md) biedt inzicht in gebeurtenissen op abonnementsniveau die zich in uw Azure-abonnement hebben voorgedaan. Naast het bekijken van het activiteitslogboek in de Azure-portal of het kopiëren naar een Log Analytics-werkruimte waar deze kan worden geanalyseerd met andere gegevens die door Azure Monitor zijn verzameld, u een logboekprofiel maken om het activiteitslogboek te archiveren naar een Azure-opslagaccount of te streamen naar een Gebeurtenishub.

## <a name="archive-activity-log"></a>Archiefactiviteitenlogboek
Het archiveren van het activiteitenlogboek naar een opslagaccount is handig als u uw logboekgegevens langer dan 90 dagen wilt bewaren (met volledige controle over het bewaarbeleid) voor audit, statische analyse of back-up. Als u uw gebeurtenissen slechts 90 dagen of minder hoeft te bewaren, hoeft u geen archivering in te stellen voor een opslagaccount, omdat gebeurtenissen in het activiteitenlogboek 90 dagen in het Azure-platform worden bewaard.

## <a name="stream-activity-log-to-event-hub"></a>Logboek van activiteit streamen naar gebeurtenishub
[Azure Event Hubs](/azure/event-hubs/) is een datastreamingplatform en gebeurtenisopnameservice die miljoenen gebeurtenissen per seconde kan ontvangen en verwerken. Gegevens die naar een Event Hub worden verzonden, kunnen worden omgezet en opgeslagen door gebruik te maken van een provider voor realtime analytische gegevens of batchverwerking/opslagadapters. Twee manieren waarop u de streamingmogelijkheid voor het activiteitenlogboek gebruiken, zijn:
* **Stream naar logboekregistratie- en telemetriesystemen van derden:** na verloop van tijd wordt Azure Event Hubs-streaming het mechanisme om uw Activiteitslogboek door te geven aan externe SM's en loganalyseoplossingen.
* **Bouw een aangepast telemetrie- en loggingplatform:** Als u al een op maat gemaakt telemetrieplatform hebt of erover nadenkt om er een te bouwen, stelt het zeer schaalbare publicatie-abonneren van Event Hubs u in staat om het activiteitenlogboek flexibel in te nemen.

## <a name="prerequisites"></a>Vereisten

### <a name="storage-account"></a>Storage-account
Als u uw activiteitenlogboek archiveert, moet u [een opslagaccount maken](../../storage/common/storage-account-create.md) als u er nog geen hebt. U mag geen bestaand opslagaccount gebruiken met andere, niet-bewakingsgegevens die erin zijn opgeslagen, zodat u de toegang tot bewakingsgegevens beter beheren. Als u echter ook logboeken en statistieken archiveert naar een opslagaccount, u ervoor kiezen om datzelfde opslagaccount te gebruiken om alle bewakingsgegevens op een centrale locatie te bewaren.

Het opslagaccount hoeft niet in hetzelfde abonnement te zitten als het abonnement dat logboeken uitzendt, zolang de gebruiker die de instelling configureert, de juiste RBAC-toegang tot beide abonnementen heeft. 

> [!TIP]
> Zie [Azure Storage-firewalls en virtuele netwerken configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) voor het bieden van toegang tot een opslagaccount achter een beveiligd virtueel netwerk.

### <a name="event-hubs"></a>Event Hubs
Als u uw activiteitenlogboek naar een gebeurtenishub verzendt, moet u [een gebeurtenishub maken](../../event-hubs/event-hubs-create.md) als u er nog geen hebt. Als u eerder activiteitslogboekgebeurtenissen hebt gestreamd naar de naamruimte van deze gebeurtenishubs, wordt die gebeurtenishub opnieuw gebruikt.

Het beleid voor gedeelde toegang definieert de machtigingen die het streamingmechanisme heeft. Voor streaming naar gebeurtenishubs zijn machtigingen voor beheren, verzenden en luisteren vereist. U beleid voor gedeelde toegang voor de naamruimte van gebeurtenishubs maken of wijzigen in de Azure-portal onder het tabblad Configureren voor de naamruimte van uw gebeurtenishubs.

Als u het logboekprofiel van het logboek activiteit wilt bijwerken om streaming op te nemen, moet u de machtiging ListKey hebben voor de autorisatieregel gebeurtenishubs. De naamruimte van Gebeurtenishubs hoeft niet in hetzelfde abonnement te zitten als het abonnement dat logboeken uitzendt, zolang de gebruiker die de instelling configureert, de juiste RBAC-toegang heeft tot beide abonnementen en beide abonnementen in dezelfde AAD-tenant staan.

Stream het activiteitenlogboek naar een gebeurtenishub door [een logboekprofiel te maken](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Een logboekprofiel maken
U definieert hoe uw Azure Activity-logboek wordt geëxporteerd met behulp van een **logboekprofiel.** Elk Azure-abonnement kan slechts één logboekprofiel hebben. Deze instellingen kunnen worden geconfigureerd via de optie **Exporteren** in het blade activiteitslogboek in de portal. Ze kunnen ook programmatisch worden geconfigureerd [met behulp van de Azure Monitor REST API,](https://msdn.microsoft.com/library/azure/dn931927.aspx)PowerShell-cmdlets of CLI.

Het logboekprofiel definieert het volgende.

**Waar het activiteitenlogboek moet worden verzonden.** Momenteel zijn de beschikbare opties Storage Account of Event Hubs.

**Welke gebeurteniscategorieën moeten worden verzonden.** De betekenis van *categorie* in logprofielen en gebeurtenissen in het activiteitenlogboek is anders. In het logboekprofiel vertegenwoordigt *categorie* het bewerkingstype (Schrijven, Verwijderen, Actie). In een gebeurtenis Activiteitslogboek vertegenwoordigt de eigenschap *"** de eigenschap '* de bron of het type gebeurtenis (bijvoorbeeld Beheer, Servicestatus en Waarschuwing).

**Welke regio's (locaties) moeten worden geëxporteerd.** U moet alle locaties opnemen, omdat veel gebeurtenissen in het activiteitenlogboek wereldwijde gebeurtenissen zijn.

**Hoe lang het activiteitenlogboek moet worden bewaard in een opslagaccount.** Een waarde van nul voor de optie Retentie houdt in dat logboeken permanent worden bewaard. Anders kan de waarde een willekeurig aantal dagen tussen 1 en 365 zijn.

Als bewaarbeleid is ingesteld, maar het opslaan van logboeken in een opslagaccount is uitgeschakeld, heeft het bewaarbeleid geen effect. Bewaarbeleid wordt per dag toegepast, dus aan het einde van een dag (UTC) worden logboeken van de dag die nu verder gaat dan het bewaarbeleid verwijderd. Als u bijvoorbeeld een bewaarbeleid van één dag had, worden de logboeken van de dag ervoor vandaag aan het begin van de dag verwijderd. Het verwijderingsproces begint om middernacht UTC, maar houd er rekening mee dat het tot 24 uur kan duren voordat de logboeken uit uw opslagaccount worden verwijderd.


> [!IMPORTANT]
> Mogelijk ontvangt u een foutmelding bij het maken van een logboekprofiel als de Microsoft.Insights-bronprovider niet is geregistreerd. Zie [Azure-bronproviders en -typen](../../azure-resource-manager/management/resource-providers-and-types.md) om deze provider te registreren.


### <a name="create-log-profile-using-the-azure-portal"></a>Logboekprofiel maken met de Azure-portal

Maak of bewerk een logboekprofiel met de optie **Exporteren naar gebeurtenishub** in de Azure-portal.

1. Selecteer **Activiteitslogboek**in het menu **Azure Monitor** in de Azure-portal .
3. Klik op **Diagnostische instellingen**.

   ![Diagnostische instellingen](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klik op de paarse banner voor de legacy-ervaring.

    ![Ervaring met legacy](media/diagnostic-settings-subscription/legacy-experience.png)

3. Geef in het blad dat wordt weergegeven het volgende op:
   * Regio's met de gebeurtenissen om te exporteren. U moet alle regio's selecteren om ervoor te zorgen dat u belangrijke gebeurtenissen niet mist, omdat het activiteitenlogboek een globaal (niet-regionaal) is en de meeste gebeurtenissen dus geen regio hebben die eraan is gekoppeld.
   * Als u naar een opslagaccount wilt schrijven:
       * Het opslagaccount waarop u gebeurtenissen wilt opslaan.
       * Het aantal dagen dat u deze gebeurtenissen in opslag wilt behouden. Een instelling van 0 dagen behoudt de logboeken voor altijd.
   * Als u naar gebeurtenishub wilt schrijven:
       * De Service Bus Namespace waarin u een Event Hub wilt maken voor het streamen van deze evenementen.

     ![Het blad van het logboek van de activiteitslogboek exporteren](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Klik **op Opslaan** om deze instellingen op te slaan. De instellingen worden onmiddellijk toegepast op uw abonnement.


### <a name="configure-log-profile-using-powershell"></a>Logboekprofiel configureren met PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als er al een logboekprofiel bestaat, moet u eerst het bestaande logboekprofiel verwijderen en vervolgens een nieuw profiel maken.

1. Gebruiken `Get-AzLogProfile` om te bepalen of er een logboekprofiel bestaat.  Als er een logboekprofiel bestaat, noteer dan de *eigenschap naam.*

1. Gebruik `Remove-AzLogProfile` om het logboekprofiel te verwijderen met de waarde uit de *eigenschap naam.*

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Gebruik `Add-AzLogProfile` om een nieuw logboekprofiel te maken:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Eigenschap | Vereist | Beschrijving |
    | --- | --- | --- |
    | Name |Ja |Naam van uw logboekprofiel. |
    | StorageAccountId |Nee |Resource-id van het opslagaccount waar het activiteitenlogboek moet worden opgeslagen. |
    | serviceBusRuleId |Nee |ServiceBusregel-ID voor de naamruimte servicebus waarin u gebeurtenishubs wilt laten maken. Dit is een tekenreeks `{service bus resource ID}/authorizationrules/{key name}`met het formaat: . |
    | Locatie |Ja |Door komma's gescheiden lijst met regio's waarvoor u activiteitslogboekgebeurtenissen wilt verzamelen. |
    | RetentieInDays |Ja |Aantal dagen waarvoor gebeurtenissen in de opslagrekening moeten worden bewaard, tussen 1 en 365. Een waarde van nul slaat de logboeken voor onbepaalde tijd op. |
    | Categorie |Nee |Door komma's gescheiden lijst met gebeurteniscategorieën die moeten worden verzameld. Mogelijke waarden zijn _Schrijven,_ _Verwijderen_en _Actie_. |

### <a name="example-script"></a>Voorbeeldscript
Hieronder volgt een voorbeeld van PowerShell-script om een logboekprofiel te maken dat het activiteitenlogboek schrijft naar zowel een opslagaccount als gebeurtenishub.

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


### <a name="configure-log-profile-using-azure-cli"></a>Logboekprofiel configureren met Azure CLI

Als er al een logboekprofiel bestaat, moet u eerst het bestaande logboekprofiel verwijderen en vervolgens een nieuw logboekprofiel maken.

1. Gebruiken `az monitor log-profiles list` om te bepalen of er een logboekprofiel bestaat.
2. Gebruik `az monitor log-profiles delete --name "<log profile name>` om het logboekprofiel te verwijderen met de waarde uit de *eigenschap naam.*
3. Gebruik `az monitor log-profiles create` om een nieuw logboekprofiel te maken:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Eigenschap | Vereist | Beschrijving |
    | --- | --- | --- |
    | name |Ja |Naam van uw logboekprofiel. |
    | storage-account-id |Ja |Resource-id van het opslagaccount waarop activiteitslogboeken moeten worden opgeslagen. |
    | locaties |Ja |Lijst met objecten met spatie gescheiden waarvoor u activiteitslogboekgebeurtenissen wilt verzamelen. U een lijst met alle `az account list-locations --query [].name`regio's voor uw abonnement bekijken via. |
    | Dagen |Ja |Aantal dagen waarvoor evenementen moeten worden bewaard, tussen 1 en 365. Een waarde van nul zal de logs voor onbepaalde tijd (voor altijd) opslaan.  Als nul, moet de ingeschakelde parameter worden ingesteld op false. |
    |enabled | Ja |Waar of onwaar.  Wordt gebruikt om het bewaarbeleid in of uit te schakelen.  Als True, moet de parameter days een waarde hebben die groter is dan 0.
    | categorieën |Ja |Lijst met door ruimte gescheiden lijst met gebeurteniscategorieën die moeten worden verzameld. Mogelijke waarden zijn Schrijven, Verwijderen en Actie. |



## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het activiteitenlogboek](../../azure-resource-manager/management/view-activity-logs.md)
* [Logboeken voor activiteit verzamelen in Azure-monitorlogboeken](activity-log-collect.md)
