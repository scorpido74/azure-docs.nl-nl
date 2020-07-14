---
title: Anonieme open bare Lees toegang voor containers en blobs voor komen
titleSuffix: Azure Storage
description: ''
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/06/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 90d7cd65bbc07524391f34fe0efce2b044664cef
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209648"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Anonieme open bare Lees toegang voor containers en blobs voor komen

Anonieme open bare Lees toegang tot containers en blobs in Azure Storage is een handige manier om gegevens te delen, maar kan ook een beveiligings risico opleveren. Het is belang rijk anonieme toegang zorgvuldig in te scha kelen en te begrijpen hoe anonieme toegang tot uw gegevens kan worden geëvalueerd. Operationele complexiteit, menselijke fout of kwaad aardige aanvallen op basis van gegevens die openbaar toegankelijk zijn, kunnen leiden tot dure gegevens inbreuken. Micro soft raadt u aan anonieme toegang alleen in te scha kelen wanneer dit nodig is voor uw toepassings scenario.

Standaard kan een opslag account een gebruiker met de juiste machtigingen voor het configureren van open bare toegang tot containers en blobs. U kunt deze functionaliteit uitschakelen op het niveau van het opslag account, zodat containers en blobs in het account niet kunnen worden geconfigureerd voor open bare toegang.

In dit artikel wordt beschreven hoe u anonieme aanvragen voor een opslag account analyseert en hoe u anonieme toegang voor het hele opslag account of een afzonderlijke container kunt voor komen.

## <a name="detect-anonymous-requests-from-client-applications"></a>Anonieme aanvragen van client toepassingen detecteren

Wanneer u open bare Lees toegang voor een opslag account uitschakelt, wordt er een risico voor het afwijzen van aanvragen voor containers en blobs die momenteel zijn geconfigureerd voor open bare toegang. Als u open bare toegang uitschakelt voor een opslag account, worden de instellingen voor open bare toegang overschreven voor alle containers in dat opslag account. Wanneer open bare toegang voor het opslag account is uitgeschakeld, zullen toekomstige anonieme aanvragen voor dat account mislukken.

Om te begrijpen hoe het uitschakelen van open bare toegang van invloed kan zijn op client toepassingen, raadt micro soft u aan om logboek registratie en metrische gegevens voor dat account in te scha kelen en patronen van anonieme aanvragen gedurende een tijds interval te analyseren. Metrische gegevens gebruiken om het aantal anonieme aanvragen voor het opslag account te bepalen en Logboeken gebruiken om te bepalen welke containers anoniem worden gebruikt.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Anonieme aanvragen controleren met Metrics Explorer

Als u anonieme aanvragen wilt bijhouden in een opslag account, gebruikt u Azure Metrics Explorer in de Azure Portal. Zie aan de slag [met Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)voor meer informatie over Metrics Explorer.

Volg deze stappen om een metriek te maken waarmee anonieme aanvragen worden bijgehouden:

1. Ga in Azure Portal naar uw opslagaccount. Selecteer **metrische gegevens**onder het gedeelte **bewaking** .
1. Selecteer **Metrische waarde toevoegen**. Geef in het dialoog venster **metrisch** de volgende waarden op:
    1. Zorg ervoor dat het veld bereik is ingesteld op de naam van het opslag account.
    1. Stel de **metrische naam ruimte** in op *BLOB*. Met deze metriek worden alleen aanvragen voor Blob-opslag gerapporteerd.
    1. Stel het veld **metrische waarde** in op *trans acties*.
    1. Stel het **aggregatie** veld in op *Sum*.

    Met de nieuwe metriek wordt de som weer gegeven van het aantal trans acties op Blob Storage over een bepaalde tijds interval. De resulterende metriek wordt weer gegeven, zoals wordt weer gegeven in de volgende afbeelding:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Scherm afbeelding die laat zien hoe u metrische gegevens kunt configureren voor het berekenen van BLOB-trans acties":::

1. Selecteer vervolgens de knop **filter toevoegen** om een filter te maken voor de metrische gegevens voor anonieme aanvragen.
1. Geef in het dialoog venster **filter** de volgende waarden op:
    1. Stel de waarde van de **eigenschap** in op *authenticatie*.
    1. Stel het **operator** veld in op het gelijkteken (=).
    1. Stel het veld **waarden** in op *anoniem*.
1. Selecteer in de rechter bovenhoek het tijds interval waarover u de metrische gegevens wilt weer geven. U kunt ook aangeven hoe gedetailleerd de aggregatie van aanvragen moet zijn, door intervallen van 1 minuut tot 1 maand op te geven.

Nadat u de metrische gegevens hebt geconfigureerd, worden anonieme aanvragen weer gegeven in de grafiek. De volgende afbeelding toont anonieme aanvragen die zijn samengevoegd in de afgelopen dertig minuten.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Scherm opname van geaggregeerde anonieme aanvragen op Blob Storage":::

U kunt ook een waarschuwings regel configureren om u op de hoogte te stellen wanneer er een bepaald aantal anonieme aanvragen wordt gedaan voor uw opslag account. Zie [metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../../azure-monitor/platform/alerts-metric.md)voor meer informatie.

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Logboeken analyseren om containers te identificeren die anonieme aanvragen ontvangen

Azure Storage legt vastgelegde gegevens vast over aanvragen voor het opslag account, met inbegrip van de manier waarop een aanvraag is geautoriseerd. U kunt de logboeken analyseren om te bepalen welke containers anonieme aanvragen ontvangen.

Als u aanvragen wilt registreren voor uw Azure Storage-account om anonieme aanvragen te evalueren, kunt u Azure Storage logboek registratie gebruiken in Azure Monitor (preview). Zie [Azure Storage bewaken](../common/monitor-storage.md)voor meer informatie.

Azure Storage logboek registratie in Azure Monitor ondersteunt het gebruik van logboek query's voor het analyseren van logboek gegevens. Als u een query wilt uitvoeren op Logboeken, kunt u een Azure Log Analytics-werk ruimte gebruiken. Zie [zelf studie: aan de slag met log Analytics query's](../../azure-monitor/log-query/get-started-portal.md)voor meer informatie over logboek query's.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Een diagnostische instelling maken in de Azure Portal

Als u Azure Storage gegevens wilt registreren met Azure Monitor en deze wilt analyseren met Azure Log Analytics, moet u eerst een diagnostische instelling maken die aangeeft welke typen aanvragen en voor welke opslag Services u gegevens wilt registreren. Voer de volgende stappen uit om een diagnostische instelling te maken in de Azure Portal:

1. Schrijf u in [voor de Azure Storage registratie in azure monitor preview](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Maak een nieuwe Log Analytics-werk ruimte in het abonnement dat uw Azure Storage-account bevat. Nadat u logboek registratie voor uw opslag account hebt geconfigureerd, zijn de logboeken beschikbaar in de werk ruimte Log Analytics. Zie [een log Analytics-werk ruimte maken in de Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)voor meer informatie.
1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer in de sectie controle de optie **Diagnostische instellingen (preview)**.
1. Selecteer **BLOB** om aanvragen te registreren die worden uitgevoerd op Blob Storage.
1. Selecteer **Diagnostische instelling toevoegen**.
1. Geef een naam op voor de diagnostische instelling.
1. Kies onder **categorie Details**in de sectie **logboek** welke typen aanvragen moeten worden geregistreerd. Alle anonieme aanvragen worden Lees aanvragen, dus Selecteer **StorageRead** om anonieme aanvragen vast te leggen.
1. Selecteer onder **doel gegevens** **verzenden naar log Analytics**. Selecteer uw abonnement en de Log Analytics werk ruimte die u eerder hebt gemaakt, zoals wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Scherm afbeelding die laat zien hoe u een diagnostische instelling voor logboek registratie aanvragen maakt":::

Nadat u de diagnostische instelling hebt gemaakt, worden aanvragen voor het opslag account vervolgens geregistreerd volgens die instelling. Zie voor meer informatie [Diagnostische instelling maken om bron logboeken en metrische gegevens in azure te verzamelen](../../azure-monitor/platform/diagnostic-settings.md).

Zie [bron Logboeken (preview)](../common/monitor-storage-reference.md#resource-logs-preview)voor een verwijzing van velden die beschikbaar zijn in azure Storage-Logboeken in azure monitor.

#### <a name="query-logs-for-anonymous-requests"></a>Query logboeken voor anonieme aanvragen

Azure Storage-Logboeken in Azure Monitor bevatten het type autorisatie dat is gebruikt voor het maken van een aanvraag voor een opslag account. Filter in uw logboek query op de eigenschap **AuthenticationType** om anonieme aanvragen weer te geven.

Als u Logboeken voor de afgelopen zeven dagen wilt ophalen voor anonieme aanvragen bij Blob Storage, opent u uw Log Analytics-werk ruimte. Plak vervolgens de volgende query in een nieuwe logboek query en voer deze uit. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

U kunt ook een waarschuwings regel configureren op basis van deze query om u te informeren over anonieme aanvragen. Zie [logboek waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../../azure-monitor/platform/alerts-log.md)voor meer informatie.

## <a name="remediate-anonymous-public-access"></a>Anonieme open bare toegang herstellen

Nadat u anonieme aanvragen hebt geëvalueerd voor containers en blobs in uw opslag account, kunt u actie ondernemen om open bare toegang te beperken of te voor komen. Als sommige containers in uw opslag account mogelijk beschikbaar moeten zijn voor open bare toegang, kunt u de instelling voor open bare toegang configureren voor elke container in uw opslag account. Deze optie biedt de meest gedetailleerde controle over open bare toegang. Zie [het niveau van open bare toegang instellen voor een container](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)voor meer informatie.

Voor een betere beveiliging kunt u open bare toegang voor een heel opslag account uitschakelen. De instelling voor open bare toegang voor een opslag account overschrijft de afzonderlijke instellingen voor containers in dat account. Wanneer u open bare toegang voor een opslag account uitschakelt, zijn alle containers die zijn geconfigureerd om open bare toegang toe te staan, niet meer anoniem toegankelijk. Zie [open bare Lees toegang voor een opslag account in-of uitschakelen](anonymous-read-access-configure.md#enable-or-disable-public-read-access-for-a-storage-account)voor meer informatie.

Als uw scenario vereist dat bepaalde containers beschikbaar zijn voor open bare toegang, kan het raadzaam zijn om die containers en de bijbehorende blobs te verplaatsen naar opslag accounts die zijn gereserveerd voor open bare toegang. U kunt open bare toegang vervolgens uitschakelen voor alle andere opslag accounts.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Controleren of open bare toegang tot een BLOB niet is toegestaan

Als u wilt controleren of open bare toegang tot een specifieke blob is geweigerd, kunt u proberen om de BLOB te downloaden via de bijbehorende URL. Als het downloaden is geslaagd, is de BLOB nog steeds openbaar beschikbaar. Als de BLOB niet openbaar toegankelijk is omdat de open bare toegang is uitgeschakeld voor het opslag account, wordt een fout bericht weer gegeven waarin staat dat open bare toegang niet is toegestaan voor dit opslag account.

In het volgende voor beeld ziet u hoe u Power shell gebruikt om een BLOB te downloaden via de bijbehorende URL. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Controleren of het wijzigen van de instelling voor open bare toegang van de container niet is toegestaan

Als u wilt controleren of de instelling voor open bare toegang van een container niet kan worden gewijzigd nadat de open bare toegang voor het opslag account is uitgeschakeld, kunt u proberen de instelling te wijzigen. Het wijzigen van de instelling voor open bare toegang van de container mislukt als de open bare toegang voor het opslag account is uitgeschakeld.

In het volgende voor beeld ziet u hoe u Power shell kunt gebruiken om de instelling voor open bare toegang van een container te wijzigen. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Controleer of het maken van een container waarvoor open bare toegang is ingeschakeld, niet is toegestaan

Als open bare toegang voor het opslag account is uitgeschakeld, kunt u geen nieuwe container maken waarvoor open bare toegang is ingeschakeld. Als u wilt controleren, kunt u proberen een container te maken waarvoor open bare toegang is ingeschakeld.

In het volgende voor beeld ziet u hoe u Power shell gebruikt om een container te maken waarvoor open bare toegang is ingeschakeld. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:
 
```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

## <a name="next-steps"></a>Volgende stappen

- [Anonieme open bare Lees toegang voor containers en blobs configureren](anonymous-read-access-configure.md)
- [Anoniem toegang krijgen tot open bare containers en blobs met .NET](anonymous-read-access-client.md)