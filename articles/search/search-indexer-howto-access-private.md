---
title: Indexeer verbindingen via een persoonlijk eind punt
titleSuffix: Azure Cognitive Search
description: Indexeer verbindingen configureren voor toegang tot inhoud van andere Azure-resources die zijn beveiligd via een persoonlijk eind punt.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcacd6c2e1353c71d8e4e25c95ee2b563e7b3fba
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150422"
---
# <a name="indexer-connections-through-a-private-endpoint-azure-cognitive-search"></a>Indexeer verbindingen via een persoonlijk eind punt (Azure Cognitive Search)

Veel Azure-resources (zoals Azure Storage-accounts) kunnen worden geconfigureerd om verbindingen te accepteren van een specifieke lijst met virtuele netwerken en externe verbindingen te weigeren die afkomstig zijn van een openbaar netwerk. Als u een Indexeer functie gebruikt om gegevens in azure Cognitive Search te indexeren en uw gegevens bron zich op een particulier netwerk bevindt, kunt u een (uitgaand) [persoonlijk eind punt verbinding](../private-link/private-endpoint-overview.md) maken om de gegevens te bereiken.

Voor het gebruik van deze indexer-verbindings methode zijn er twee vereisten:

+ De Azure-resource die inhoud of code levert, moet eerder zijn geregistreerd bij de [Azure Private Link-service](https://azure.microsoft.com/services/private-link/).

+ Azure Cognitive Search-service moet Basic of hoger zijn (niet beschikbaar in de gratis laag). Voor Indexeer functies met een vaardig heden moet de zoek service bovendien S2 of hoger zijn. Zie [service limieten](search-limits-quotas-capacity.md#shared-private-link-resource-limits)voor meer informatie.

## <a name="shared-private-link-resources-management-apis"></a>Beheer-Api's voor gedeelde persoonlijke koppelings bronnen

Privé-eind punten van beveiligde resources die zijn gemaakt via Azure Cognitive Search Api's worden *gedeelde persoonlijke koppelings resources* genoemd omdat u toegang hebt tot een resource (zoals een opslag account) die aan de [Azure Private Link-service](https://azure.microsoft.com/services/private-link/)is ingeschakeld.

Azure Cognitive Search biedt via de beheer REST API een [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) bewerking die u kunt gebruiken om toegang te configureren vanaf een Azure Cognitive Search indexer.

Privé-eindpunt verbindingen met sommige resources kunnen alleen worden gemaakt met de preview-versie van de API voor zoek beheer ( `2020-08-01-Preview` of hoger), aangeduid met de tag preview in de volgende tabel. Resources zonder de tag preview kunnen worden gemaakt met behulp van de preview-versie of de algemeen beschik bare API ( `2020-08-01` of hoger).

Hieronder ziet u de lijst met Azure-resources waarmee uitgaande privé-eind punten kunnen worden gemaakt op basis van Azure Cognitive Search. De `groupId` waarden in de onderstaande tabel moeten exact worden gebruikt als geschreven (hoofdletter gevoelig) in de API om een gedeelde persoonlijke koppelings bron te maken.

| Azure-resource | Groeps-id |
| --- | --- |
| Azure Storage-BLOB (of) ADLS gen 2 | `blob`|
| Azure Storage-tabellen | `table`|
| Azure Cosmos DB-SQL-API | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (preview-versie) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (preview-versie) | `sites` |

De lijst met Azure-resources waarvoor uitgaande particuliere endpoint-verbindingen worden ondersteund, kan ook worden opgevraagd met de [API List ondersteunde](/rest/api/searchmanagement/privatelinkresources/listsupported).

In de rest van dit artikel wordt een combi natie van [ARMClient](https://github.com/projectkudu/ARMClient) en [postman](https://www.postman.com/) gebruikt om de rest API-aanroepen te demonstreren.

> [!NOTE]
> In dit artikel wordt ervan uitgegaan dat de naam van de zoek service __Contoso-Search__ is die bestaat in de resource groep __Contoso__ van een abonnement met abonnements-id __00000000-0000-0000-0000-000000000000__. De resource-ID van deze zoek service wordt `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

In de rest van de voor beelden ziet u hoe de service __Contoso-Search__ kan worden geconfigureerd zodat de bijbehorende Indexeer functies toegang hebben tot gegevens uit het beveiligde-opslag account `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>Uw opslag account beveiligen

Configureer het opslag account zodanig [dat alleen toegang is toegestaan vanuit specifieke subnetten](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Als u in het Azure Portal deze optie inschakelt en de set leeg laat, betekent dit dat er geen verkeer van een virtueel netwerk is toegestaan.

   ![Toegang tot virtueel netwerk](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "Toegang tot virtueel netwerk")

> [!NOTE]
> De [aanpak van de vertrouwde micro soft-service](../storage/common/storage-network-security.md#trusted-microsoft-services) kan worden gebruikt om virtuele netwerk-of IP-beperkingen voor een dergelijk opslag account over te slaan en de zoek service toegang te geven tot gegevens in het opslag account, zoals beschreven in de [Indexeer functie toegang tot Azure Storage met behulp van de uitzonde ring voor vertrouwde services ](search-indexer-howto-access-trusted-service-exception.md). Wanneer u deze benadering echter gebruikt voor de communicatie tussen Azure Cognitive Search en het opslag account wordt uitgevoerd via het open bare IP-adres van het opslag account, via het beveiligde micro soft backbone-netwerk.

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Stap 1: een gedeelde persoonlijke koppelings bron maken naar het opslag account

Maak de volgende API-aanroep om Azure Cognitive Search aan te vragen om een uitgaand privé-eind punt verbinding te maken met het opslag account

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

De inhoud van het `create-pe.json` bestand (dat de hoofd tekst van de aanvraag vertegenwoordigt in de API) is als volgt:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

Er `202 Accepted` wordt een antwoord geretourneerd op geslaagd: het proces voor het maken van een uitgaand privé-eind punt is een langlopende (asynchrone) bewerking. Hierin moeten de volgende bronnen worden geïmplementeerd:

1. Een persoonlijk eind punt dat is toegewezen aan een privé-IP-adres in een `"Pending"` status. Het privé-IP-adres wordt opgehaald uit de adres ruimte die is toegewezen aan het virtuele netwerk van de specifieke uitvoerings omgeving van de zoek service. Na goed keuring van het persoonlijke eind punt, is communicatie van Azure Cognitive Search naar het opslag account afkomstig van het privé-IP-adres en een beveiligd persoonlijk koppelings kanaal.
2. Een privé-DNS-zone voor het type resource, op basis van de `groupId` . Dit zorgt ervoor dat alle DNS-Zoek opdrachten naar de privé bron het IP-adres gebruiken dat is gekoppeld aan het persoonlijke eind punt.

Zorg ervoor dat u de juiste opgeeft `groupId` voor het type resource waarvoor u het persoonlijke eind punt maakt. Niet-overeenkomend leidt tot een niet-geslaagd antwoord bericht.

Net als alle asynchrone Azure-bewerkingen `PUT` retourneert de aanroep een `Azure-AsyncOperation` header-waarde die er als volgt uitziet:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Deze URI kan regel matig worden gecontroleerd om de status van de bewerking te verkrijgen. We raden u aan om te wachten tot de status van de resource bewerking voor gedeelde persoonlijke koppelingen een Terminal status heeft bereikt (dat wil zeggen `succeeded` ) voordat u doorgaat.

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Stap 2a: de verbinding met het persoonlijke eind punt voor het opslag account goed keuren

> [!NOTE]
> In deze sectie wordt gebruikgemaakt van Azure Portal om de goedkeurings stroom voor een persoonlijk eind punt naar opslag door te lopen. In plaats daarvan kunt u ook de [rest API](/rest/api/storagerp/privateendpointconnections) die beschikbaar zijn via de opslag Resource provider (RP) gebruiken.
>
> Andere providers, zoals CosmosDB, Azure SQL server etc., bieden vergelijk bare RP-Api's voor het beheren van particuliere endpoint-verbindingen.

Ga naar het tabblad '**particuliere endpoint Connections**' van het opslag account op Azure Portal. Er moet een aanvraag voor een persoonlijke eindpunt verbinding zijn met het aanvraag bericht van de vorige API-aanroep (zodra de asynchrone bewerking is __voltooid__).

   ![Goed keuring van persoonlijk eind punt](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "Goed keuring van persoonlijk eind punt")

Selecteer het persoonlijke eind punt dat is gemaakt door Azure Cognitive Search (gebruik de kolom ' persoonlijk eind punt ' om de verbinding van het particuliere eind punt te identificeren met de naam die is opgegeven in de vorige API) en kies goed keuren, met een passend bericht (het bericht is niet significant). Controleer of de verbinding met het persoonlijke eind punt als volgt wordt weer gegeven (dit kan een wille keurige plaats van 1-2 minuten duren zodat de status op de portal wordt bijgewerkt)

![Persoonlijk eind punt goedgekeurd](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "Persoonlijk eind punt goedgekeurd")

Nadat de verbindings aanvraag van het particuliere eind punt is goedgekeurd, betekent dit dat verkeer *kan* stromen via het persoonlijke eind punt. Zodra het persoonlijke eind punt is goedgekeurd, maakt Azure Cognitive Search de benodigde DNS-zone toewijzingen in de DNS-zone die hiervoor is gemaakt.

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Stap 2b: de status van de gedeelde persoonlijke koppelings resource opvragen

 Als u wilt controleren of de resource van de gedeelde persoonlijke koppeling na goed keuring is bijgewerkt, haalt u de status op met behulp van de [Get-API](/rest/api/searchmanagement/sharedprivatelinkresources/get).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Als de `properties.provisioningState` resource is `Succeeded` en `properties.status` is `Approved` , betekent dit dat de resource van de gedeelde persoonlijke koppeling functioneel is en dat Indexeer functies kunnen worden geconfigureerd om te communiceren via het persoonlijke eind punt.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>Stap 3: Indexeer functie configureren om uit te voeren in de privé omgeving

> [!NOTE]
> Deze stap kan worden uitgevoerd, zelfs voordat de verbinding met een particulier eind punt wordt goedgekeurd. Totdat de verbinding met een particulier eind punt is goedgekeurd, wordt een Indexeer functie die probeert te communiceren met een beveiligde bron (zoals het opslag account), beëindigd in een tijdelijke fout status. Nieuwe Indexeer functies kunnen niet worden gemaakt. Zodra de verbinding met een particulier eind punt is goedgekeurd, hebben Indexeer functies toegang tot het privé-opslag account.

1. [Een gegevens bron maken](/rest/api/searchservice/create-data-source) die verwijst naar het beveiligde-opslag account en een geschikte container in het opslag account. Hieronder ziet u deze aanvraag in postman.
![Gegevens bron maken](media\search-indexer-howto-secure-access\create-ds.png "Gegevens bron maken")

1. Maak op dezelfde manier [een index](/rest/api/searchservice/create-index) en [Maak optioneel een vaardig heden](/rest/api/searchservice/create-skillset) met behulp van de rest API.

1. [Een Indexeer functie maken](/rest/api/searchservice/create-indexer) die verwijst naar de hierboven gemaakte gegevens bron, index en vaardig heden. Daarnaast dwingt u de Indexeer functie uit in de persoonlijke uitvoerings omgeving door de configuratie-eigenschap van de Indexeer functie in `executionEnvironment` te stellen op `"Private"` .
![Indexeer functie maken](media\search-indexer-howto-secure-access\create-idr.png "Indexeer functie maken")

De Indexeer functie moet worden gemaakt en moet de voortgang indexeren vanuit het opslag account via de verbinding met het persoonlijke eind punt. De status van de Indexeer functie kan worden bewaakt met behulp van de API voor de [Indexeer functie](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Als u al bestaande Indexeer functies hebt, kunt u deze gewoon bijwerken via de [put-API](/rest/api/searchservice/create-indexer) om de `executionEnvironment` in te stellen op `"Private"` .

## <a name="troubleshooting-issues"></a>Problemen oplossen

- Wanneer u een Indexeer functie maakt en er een fout bericht wordt gemaakt dat lijkt op ' de referenties van de gegevens bron zijn ongeldig ', betekent dit dat de verbinding met het persoonlijke eind punt niet is *goedgekeurd* of niet werkt.
De status van de gedeelde persoonlijke koppelings bron ophalen met behulp van de [Get-API](/rest/api/searchmanagement/sharedprivatelinkresources/get). Controleer de resource als *Approved* deze is goedgekeurd `properties.provisioningState` . Als dit het geval is `Incomplete` , betekent dit dat sommige van de onderliggende afhankelijkheden voor de resource niet kunnen worden ingericht: Geef de `PUT` aanvraag opnieuw op om de gedeelde persoonlijke koppelings bron opnieuw te maken om het probleem op te lossen. Een nieuwe goed keuring kan nood zakelijk zijn: Controleer de status van de resource opnieuw om te controleren.
- Als de Indexeer functie wordt gemaakt zonder de waarde in `executionEnvironment` te stellen, kan het maken van de Indexeer functie slagen, maar wordt de uitvoerings geschiedenis weer gegeven dat de uitvoering van de Indexeer functie is mislukt. U moet [de Indexeer functie bijwerken](/rest/api/searchservice/update-indexer) om de uitvoerings omgeving op te geven.
- Als de Indexeer functie wordt gemaakt zonder dat de `executionEnvironment` en wordt uitgevoerd, betekent dit dat Azure Cognitive Search heeft besloten dat de uitvoerings omgeving van de zoek service de specifieke ' persoonlijke ' omgeving is. Dit kan echter worden gewijzigd op basis van een verscheidenheid aan factoren (resources die worden verbruikt door de Indexeer functie, de belasting van de zoek service, enzovoort) en kan op een later tijdstip mislukken. Wij raden u ten zeerste `executionEnvironment` `"Private"` aan om ervoor te zorgen dat deze niet meer in de toekomst zal mislukken.
- [Quota en limieten](search-limits-quotas-capacity.md) bepalen hoeveel gedeelde persoonlijke koppelings bronnen kunnen worden gemaakt en zijn afhankelijk van de SKU van de zoek service.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over persoonlijke eind punten:

- [Wat zijn privé-eind punten?](../private-link/private-endpoint-overview.md)
- [DNS-configuraties die nodig zijn voor privé-eind punten](../private-link/private-endpoint-dns.md)