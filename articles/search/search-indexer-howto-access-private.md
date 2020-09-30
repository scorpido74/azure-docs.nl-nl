---
title: Indexeer functies die beveiligde bronnen openen via persoonlijke eind punten
titleSuffix: Azure Cognitive Search
description: Instructies voor het instellen van persoonlijke eind punten voor Indexeer functies om te communiceren met beveiligde bronnen
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 94763cee852893057348f8eea1fa74fa742f62a1
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534723"
---
# <a name="accessing-secure-resources-via-private-endpoints"></a>Toegang tot beveiligde resources via persoonlijke eind punten

Azure-resources (zoals opslag accounts die worden gebruikt als gegevens bronnen) kunnen zodanig worden geconfigureerd dat ze alleen kunnen worden geopend vanuit een specifieke lijst met virtuele netwerken. Ze kunnen ook worden geconfigureerd om toegang tot het ' open bare netwerk ' niet toe te staan.
Klanten kunnen Azure Cognitive Search aanvragen voor het maken van een VPN-verbinding (uitgaand) voor een [privé-eind punt](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) om veilig toegang te krijgen tot gegevens van dergelijke gegevens bronnen via Indexeer functies.

## <a name="shared-private-link-resources-management-apis"></a>Beheer-Api's voor gedeelde persoonlijke koppelings bronnen

Persoonlijke eind punten die worden gemaakt door Azure Cognitive Search op verzoek van de klant, om toegang te krijgen tot "beveiligde" bronnen worden *gedeelde persoonlijke koppelings bronnen*genoemd. De klant is toegang tot een resource (zoals een opslag account) die op de [Azure Private Link-service](https://azure.microsoft.com/services/private-link/)is ingeschakeld.

Azure Cognitive Search biedt via de API voor zoek beheer, de mogelijkheid om [gedeelde persoonlijke koppelings bronnen te maken of](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate)bij te werken. U gebruikt deze API samen met andere beheer-Api's voor *gedeelde persoonlijke koppelings bronnen* voor het configureren van toegang tot een beveiligde resource vanuit een Azure Cognitive Search indexer.

Privé-eindpunt verbindingen met een aantal resources kunnen alleen worden gemaakt via de preview-versie van de API voor zoek beheer ( `2020-08-01-Preview` ), aangeduid met de tag preview in de volgende tabel. Resources zonder de tag preview kunnen worden gemaakt via zowel de preview-API als de GA-API ( `2020-08-01` )

Hieronder ziet u de lijst met Azure-resources waarmee uitgaande privé-eind punten kunnen worden gemaakt op basis van Azure Cognitive Search. `groupId` in de onderstaande tabel moet exact worden gebruikt (hoofdletter gevoelig) in de API om een gedeelde persoonlijke koppelings bron te maken.

| Azure-resource | Groeps-id |
| --- | --- |
| Azure Storage-BLOB (of) ADLS gen 2 | `blob`|
| Azure Storage-tabellen | `table`|
| Azure Cosmos DB-SQL-API | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (preview-versie) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (preview-versie) | `sites` |

De lijst met Azure-resources waarvoor uitgaande particuliere endpoint-verbindingen worden ondersteund, kan ook worden opgevraagd via de [API List ondersteunde](https://docs.microsoft.com/rest/api/searchmanagement/privatelinkresources/listsupported).

Voor de doel einden van deze hand leiding worden een combi natie van [ARMClient](https://github.com/projectkudu/ARMClient) en [postman](https://www.postman.com/) gebruikt om de rest API-aanroepen te demonstreren.

> [!NOTE]
> In deze hand leiding wordt ervan uitgegaan dat de naam van de zoek service __Contoso-Search__ is die bestaat in de resource groep __Contoso__ van een abonnement met abonnements-id __00000000-0000-0000-0000-000000000000__. De resource-ID van deze zoek service wordt `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

De rest van de hand leiding laat zien hoe de service __Contoso-Search__ kan worden geconfigureerd zodat de bijbehorende Indexeer functies toegang hebben tot gegevens uit het beveiligde-opslag account `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>Uw opslag account beveiligen

Configureer het opslag account zodanig [dat alleen toegang is toegestaan vanuit specifieke subnetten](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network). Als u via de Azure Portal deze optie inschakelt en de set leeg laat, betekent dit dat er geen verkeer van een virtueel netwerk is toegestaan.

   ![Toegang tot virtueel netwerk](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "Toegang tot virtueel netwerk")

> [!NOTE]
> De [aanpak van de vertrouwde micro soft-service](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) kan worden gebruikt om virtuele netwerk-of IP-beperkingen voor een dergelijk opslag account over te slaan en kan de zoek service gebruiken om toegang te krijgen tot gegevens in het opslag account zoals beschreven in de [hand leiding](search-indexer-howto-access-trusted-service-exception.md). Wanneer u deze benadering echter gebruikt voor de communicatie tussen Azure Cognitive Search en het opslag account wordt uitgevoerd via het open bare IP-adres van het opslag account, via het beveiligde micro soft backbone-netwerk.

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
> In deze sectie wordt gebruikgemaakt van Azure Portal om de goedkeurings stroom voor een persoonlijk eind punt naar opslag door te lopen. In plaats daarvan kunt u ook de [rest API](https://docs.microsoft.com/rest/api/storagerp/privateendpointconnections) die beschikbaar zijn via de opslag Resource provider (RP) gebruiken.
>
> Andere providers, zoals CosmosDB, Azure SQL server etc., bieden vergelijk bare RP-Api's voor het beheren van particuliere endpoint-verbindingen.

Ga naar het tabblad '**particuliere endpoint Connections**' van het opslag account op Azure Portal. Er moet een aanvraag voor een persoonlijke eindpunt verbinding zijn met het aanvraag bericht van de vorige API-aanroep (zodra de asynchrone bewerking is __voltooid__).

   ![Goed keuring van persoonlijk eind punt](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "Goed keuring van persoonlijk eind punt")

Selecteer het persoonlijke eind punt dat is gemaakt door Azure Cognitive Search (gebruik de kolom ' persoonlijk eind punt ' om de verbinding van het particuliere eind punt te identificeren met de naam die is opgegeven in de vorige API) en kies goed keuren, met een passend bericht (het bericht is niet significant). Controleer of de verbinding met het persoonlijke eind punt als volgt wordt weer gegeven (dit kan een wille keurige plaats van 1-2 minuten duren zodat de status op de portal wordt bijgewerkt)

![Persoonlijk eind punt goedgekeurd](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "Persoonlijk eind punt goedgekeurd")

Nadat de verbindings aanvraag van het particuliere eind punt is goedgekeurd, betekent dit dat verkeer *kan* stromen via het persoonlijke eind punt. Zodra het persoonlijke eind punt is goedgekeurd, maakt Azure Cognitive Search de benodigde DNS-zone toewijzingen in de DNS-zone die hiervoor is gemaakt.

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Stap 2b: de status van de gedeelde persoonlijke koppelings resource opvragen

 Als u wilt controleren of de resource van de gedeelde persoonlijke koppeling na goed keuring is bijgewerkt, haalt u de status op via de [Get-API](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get).

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

1. [Een gegevens bron maken](https://docs.microsoft.com/rest/api/searchservice/create-data-source) die verwijst naar het beveiligde-opslag account en een geschikte container in het opslag account. Hieronder wordt deze aanvraag weer gegeven via postman.
![Gegevens bron maken](media\search-indexer-howto-secure-access\create-ds.png "Gegevens bron maken")

2. Maak op dezelfde manier [een index](https://docs.microsoft.com/rest/api/searchservice/create-index) en [Maak optioneel een vaardig heden](https://docs.microsoft.com/rest/api/searchservice/create-skillset) met behulp van de rest API.

3. [Een Indexeer functie maken](https://docs.microsoft.com/rest/api/searchservice/create-indexer) die verwijst naar de hierboven gemaakte gegevens bron, index en vaardig heden. Daarnaast dwingt u de Indexeer functie uit in de persoonlijke uitvoerings omgeving door de configuratie-eigenschap van de Indexeer functie in `executionEnvironment` te stellen op `"Private"` .
![Indexeer functie maken](media\search-indexer-howto-secure-access\create-idr.png "Indexeer functie maken")

De Indexeer functie moet worden gemaakt en moet de voortgang indexeren vanuit het opslag account via de verbinding met het persoonlijke eind punt. De status van de Indexeer functie kan worden bewaakt via de [API voor de Indexeer functie](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Als u al bestaande Indexeer functies hebt, kunt u deze gewoon bijwerken via de [put-API](https://docs.microsoft.com/rest/api/searchservice/create-indexer) om de `executionEnvironment` in te stellen op `"Private"` .

## <a name="troubleshooting-issues"></a>Problemen oplossen

- Wanneer u een Indexeer functie maakt en er een fout bericht wordt gemaakt dat lijkt op ' de referenties van de gegevens bron zijn ongeldig ', betekent dit dat de verbinding met het persoonlijke eind punt niet is *goedgekeurd* of niet werkt.
De status van de gedeelde persoonlijke koppelings bron ophalen met behulp van de [Get-API](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get). Controleer de resource als *Approved* deze is goedgekeurd `properties.provisioningState` . Als dit het geval is `Incomplete` , betekent dit dat sommige van de onderliggende afhankelijkheden voor de resource niet kunnen worden ingericht: Geef de `PUT` aanvraag opnieuw op om de gedeelde persoonlijke koppelings bron opnieuw te maken om het probleem op te lossen. Een nieuwe goed keuring kan nood zakelijk zijn: Controleer de status van de resource opnieuw om te controleren.
- Als de Indexeer functie wordt gemaakt zonder de waarde in `executionEnvironment` te stellen, kan het maken van de Indexeer functie slagen, maar wordt de uitvoerings geschiedenis weer gegeven dat de uitvoering van de Indexeer functie is mislukt. U moet [de Indexeer functie bijwerken](https://docs.microsoft.com/rest/api/searchservice/update-indexer) om de uitvoerings omgeving op te geven.
- Als de Indexeer functie wordt gemaakt zonder dat de `executionEnvironment` en wordt uitgevoerd, betekent dit dat Azure Cognitive Search heeft besloten dat de uitvoerings omgeving van de zoek service de specifieke ' persoonlijke ' omgeving is. Dit kan echter worden gewijzigd op basis van een verscheidenheid aan factoren (resources die worden verbruikt door de Indexeer functie, de belasting van de zoek service, enzovoort) en kan op een later tijdstip mislukken. Wij raden u ten zeerste `executionEnvironment` `"Private"` aan om ervoor te zorgen dat deze niet meer in de toekomst zal mislukken.
- [Quota en limieten](search-limits-quotas-capacity.md) bepalen hoeveel gedeelde persoonlijke koppelings bronnen kunnen worden gemaakt en zijn afhankelijk van de SKU van de zoek service.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over persoonlijke eind punten:

- [Wat zijn privé-eind punten?](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
- [DNS-configuraties die nodig zijn voor privé-eind punten](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)