---
title: Mini maal vereiste versie van Transport Layer Security (TLS) configureren voor een opslag account
titleSuffix: Azure Storage
description: Configureer een opslag account om een minimale versie van Transport Layer Security (TLS) te vereisen voor clients die aanvragen indienen tegen Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: ab83f0ee656dfc717284c1e26d10dcb814fe1c9e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209592"
---
# <a name="configure-minimum-required-version-of-transport-layer-security-tls-for-a-storage-account"></a>Mini maal vereiste versie van Transport Layer Security (TLS) configureren voor een opslag account

De communicatie tussen een client toepassing en een Azure Storage account wordt versleuteld met behulp van Transport Layer Security (TLS). TLS is een standaard cryptografie protocol dat privacy en gegevens integriteit tussen clients en services via internet waarborgt. Zie [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)voor meer informatie over TLS.

Azure Storage ondersteunt momenteel drie versies van het TLS-protocol: 1,0, 1,1 en 1,2. TLS 1,2 is de veiligste versie van TLS. Azure Storage gebruikt TLS 1,2 voor open bare HTTPs-eind punten, maar TLS 1,0 en TLS 1,1 worden nog steeds ondersteund voor compatibiliteit met eerdere versies.

Standaard kunnen clients met Azure Storage-accounts gegevens verzenden en ontvangen met de oudste versie van TLS, TLS 1,0 en hoger. Als u strengere beveiligings maatregelen wilt afdwingen, kunt u uw opslag account configureren om te vereisen dat clients gegevens verzenden en ontvangen met een nieuwere versie van TLS. Als een opslag account een minimum versie van TLS vereist, zullen aanvragen met een eerdere versie mislukken.

In dit artikel wordt beschreven hoe u een opslag account configureert om te vereisen dat clients aanvragen verzenden met een minimum versie van TLS. Zie [configure Transport Layer Security (TLS) (Engelstalig) voor een client toepassing](transport-layer-security-configure-client-version.md)voor informatie over het opgeven van een bepaalde versie van TLS bij het verzenden van een aanvraag van een client toepassing.

## <a name="detect-the-tls-version-used-by-client-applications"></a>De TLS-versie detecteren die door client toepassingen wordt gebruikt

Wanneer u een minimum versie van TLS afdwingt voor uw opslag account, is het risico dat aanvragen worden afgewezen van clients die gegevens verzenden met een eerdere versie van TLS. Om te begrijpen hoe het configureren van de minimale TLS-versie van invloed kan zijn op client toepassingen, raadt micro soft u aan om logboek registratie in te scha kelen voor uw Azure Storage-account en de logboeken te analyseren na een tijds interval om te bepalen welke versies van TLS-client toepassingen worden gebruikt.

Als u aanvragen wilt registreren voor uw Azure Storage-account en de TLS-versie wilt bepalen die wordt gebruikt door de client, kunt u Azure Storage logboek registratie gebruiken in Azure Monitor (preview). Zie [Azure Storage bewaken](monitor-storage.md)voor meer informatie.

Azure Storage logboek registratie in Azure Monitor ondersteunt het gebruik van logboek query's voor het analyseren van logboek gegevens. Als u een query wilt uitvoeren op Logboeken, kunt u een Azure Log Analytics-werk ruimte gebruiken. Zie [zelf studie: aan de slag met log Analytics query's](../../azure-monitor/log-query/get-started-portal.md)voor meer informatie over logboek query's.

Als u Azure Storage gegevens wilt registreren met Azure Monitor en deze wilt analyseren met Azure Log Analytics, moet u eerst een diagnostische instelling maken die aangeeft welke typen aanvragen en voor welke opslag Services u gegevens wilt registreren. Voer de volgende stappen uit om een diagnostische instelling te maken in de Azure Portal:

1. Schrijf u in [voor de Azure Storage registratie in azure monitor preview](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Maak een nieuwe Log Analytics-werk ruimte in het abonnement dat uw Azure Storage-account bevat. Nadat u logboek registratie voor uw opslag account hebt geconfigureerd, zijn de logboeken beschikbaar in de werk ruimte Log Analytics. Zie [een log Analytics-werk ruimte maken in de Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)voor meer informatie.
1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer in de sectie controle de optie **Diagnostische instellingen (preview)**.
1. Selecteer de Azure Storage-service waarvoor u aanvragen wilt registreren. Kies bijvoorbeeld **BLOB** om aanvragen te registreren bij Blob Storage.
1. Selecteer **Diagnostische instelling toevoegen**.
1. Geef een naam op voor de diagnostische instelling.
1. Kies onder **categorie Details**in de sectie **logboek** welke typen aanvragen moeten worden geregistreerd. U kunt aanvragen voor lezen, schrijven en verwijderen vastleggen in een logboek. Als u bijvoorbeeld **StorageRead** en **StorageWrite** kiest, worden de lees-en schrijf aanvragen geregistreerd voor de geselecteerde service.
1. Selecteer onder **doel gegevens** **verzenden naar log Analytics**. Selecteer uw abonnement en de Log Analytics werk ruimte die u eerder hebt gemaakt, zoals wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="Scherm afbeelding die laat zien hoe u een diagnostische instelling voor logboek registratie aanvragen maakt":::

Nadat u de diagnostische instelling hebt gemaakt, worden aanvragen voor het opslag account vervolgens geregistreerd volgens die instelling. Zie voor meer informatie [Diagnostische instelling maken om bron logboeken en metrische gegevens in azure te verzamelen](../../azure-monitor/platform/diagnostic-settings.md).

Zie [bron Logboeken (preview)](monitor-storage-reference.md#resource-logs-preview)voor een verwijzing van velden die beschikbaar zijn in azure Storage-Logboeken in azure monitor.

### <a name="query-logged-requests-by-tls-version"></a>Query's in vastgelegde aanvragen per TLS-versie

Azure Storage Logboeken in Azure Monitor bevatten de TLS-versie die wordt gebruikt om een aanvraag te verzenden naar een opslag account. Gebruik de eigenschap **TlsVersion** om de TLS-versie van een geregistreerde aanvraag te controleren.

Als u Logboeken voor de afgelopen 7 dagen wilt ophalen en wilt bepalen hoeveel aanvragen zijn gedaan voor Blob Storage met elke versie van TLS, opent u de werk ruimte Log Analytics. Plak vervolgens de volgende query in een nieuwe logboek query en voer deze uit. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

De resultaten geven het aantal aanvragen weer dat is gemaakt met elke versie van TLS:

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="Scherm opname van de log Analytics-query voor het retour neren van een TLS-versie":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>Query vastgelegde aanvragen op IP-adres van beller en de header van de gebruikers agent

Azure Storage-Logboeken in Azure Monitor omvatten ook het IP-adres van de beller en de header van de gebruikers agent, zodat u kunt evalueren welke client toepassingen toegang hebben tot het opslag account. U kunt deze waarden analyseren om te bepalen of client toepassingen moeten worden bijgewerkt voor gebruik van een nieuwere versie van TLS, of dat het acceptabel is om een aanvraag van een client te laten mislukken als deze niet wordt verzonden met de minimale TLS-versie.

Als u Logboeken voor de afgelopen 7 dagen wilt ophalen en wilt bepalen welke clients aanvragen hebben gedaan met een versie van TLS vóór TLS 1,2, plakt u de volgende query in een nieuwe logboek query en voert u deze uit. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="configure-the-minimum-tls-version-for-an-account"></a>De minimale TLS-versie voor een account configureren

Als u de minimale TLS-versie voor een opslag account wilt configureren, gebruikt u de Azure Portal of de Azure CLI om de **minimumTlsVersion** -versie voor het account in te stellen. Deze eigenschap is beschikbaar voor alle opslag accounts die zijn gemaakt met het Azure Resource Manager-implementatie model. Zie [overzicht van opslag accounts](storage-account-overview.md)voor meer informatie.

# <a name="portal"></a>[Portal](#tab/portal)

Voer de volgende stappen uit om de minimale TLS-versie voor een opslag account te configureren met de Azure Portal:

1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer de **configuratie** -instelling.
1. Gebruik onder **minimum versie van TLS**de vervolg keuzelijst om de minimale versie van TLS te selecteren die vereist is voor toegang tot gegevens in dit opslag account, zoals wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Scherm afbeelding die laat zien hoe u de minimale versie van TLS configureert in de Azure Portal":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u de minimale TLS-versie wilt configureren voor een opslag account met Azure CLI, moet u eerst de resource-ID voor uw opslag account ophalen door de opdracht [AZ resource show](/cli/azure/resource#az-resource-show) aan te roepen. Vervolgens roept u de opdracht [AZ resource update](/cli/azure/resource#az-resource-update) aan om de eigenschap **minimumTlsVersion** voor het opslag account in te stellen. Geldige waarden voor **minimumTlsVersion** zijn `TLS1_0` , `TLS1_1` en `TLS1_2` .

In het volgende voor beeld wordt de minimale TLS-versie ingesteld op 1,2. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli-interactive
storage_account_id=$(az resource show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --resource-type Microsoft.Storage/storageAccounts \
  --query id \
  --output tsv)

az resource update \
  --ids $storage_account_id \
  --set properties.minimumTlsVersion="TLS1_2"
```

---

> [!NOTE]
> Nadat u de minimale TLS-versie voor het opslag account hebt bijgewerkt, kan het tot 30 seconden duren voordat de wijziging volledig is door gegeven.

## <a name="check-the-minimum-required-tls-version-for-an-account"></a>De mini maal vereiste TLS-versie voor een account controleren

Controleer de Azure Resource Manager eigenschap **minimumTlsVersion** om de minimale vereiste TLS-versie te bepalen die voor een opslag account is geconfigureerd. Als u deze eigenschap in één keer wilt controleren op een groot aantal opslag accounts, gebruikt u de Azure resource Graph Explorer.

De eigenschap **minimumTlsVersion** is niet standaard ingesteld en retourneert geen waarde voordat u deze expliciet hebt ingesteld. Het opslag account is standaard ingesteld op het toestaan van aanvragen die worden verzonden met TLS-versie 1,0 of hoger als de waarde van de eigenschap Null is.

### <a name="check-the-minimum-required-tls-version-for-a-single-storage-account"></a>De mini maal vereiste TLS-versie voor één opslag account controleren

Als u de mini maal vereiste TLS-versie voor één opslag account wilt controleren met behulp van Azure CLI, roept u de opdracht [AZ resource show](/cli/azure/resource#az-resource-show) en de query voor de eigenschap **minimumTlsVersion** aan:

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.minimumTlsVersion \
    --output tsv
```

### <a name="check-the-minimum-required-tls-version-for-a-set-of-storage-accounts"></a>Controleer de mini maal vereiste TLS-versie voor een set opslag accounts

Als u de mini maal vereiste TLS-versie wilt controleren in een reeks opslag accounts met optimale prestaties, kunt u de Azure resource Graph Explorer gebruiken in de Azure Portal. Voor meer informatie over het gebruik van de resource Graph Explorer raadpleegt u [Quick Start: uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer](/azure/governance/resource-graph/first-query-portal).

Als u de volgende query uitvoert in de resource Graph Explorer, wordt een lijst met opslag accounts geretourneerd en wordt de minimale TLS-versie voor elk account weer gegeven:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

---

## <a name="test-the-minimum-tls-version-from-a-client"></a>De minimale TLS-versie van een client testen

Als u wilt testen of de mini maal vereiste TLS-versie voor een opslag account aanroepen met een eerdere versie verbiedt, kunt u een client configureren voor het gebruik van een eerdere versie van TLS. Zie [configure Transport Layer Security (TLS) (Engelstalig) voor een client toepassing](transport-layer-security-configure-client-version.md)voor meer informatie over het configureren van een client voor het gebruik van een specifieke versie van TLS.

Wanneer een client een opslag account opent met een TLS-versie die niet voldoet aan de minimale TLS-versie die voor het account is geconfigureerd, retourneert Azure Storage fout code 400 fout (ongeldige aanvraag) en een bericht dat aangeeft dat de TLS-versie die is gebruikt, niet is toegestaan voor het maken van aanvragen voor dit opslag account.

## <a name="next-steps"></a>Volgende stappen

- [Transport Layer Security (TLS) configureren voor een client toepassing](transport-layer-security-configure-client-version.md)
- [Beveiligings aanbevelingen voor Blob Storage](../blobs/security-recommendations.md)
