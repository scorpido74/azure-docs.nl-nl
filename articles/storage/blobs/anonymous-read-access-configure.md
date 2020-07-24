---
title: Anonieme open bare Lees toegang voor containers en blobs configureren
titleSuffix: Azure Storage
description: Meer informatie over het toestaan of weigeren van anonieme toegang tot BLOB-gegevens voor het opslag account. Stel de instelling voor open bare toegang van de container in om containers en blobs beschikbaar te maken voor anonieme toegang.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/23/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: a153a3735bbc46dbbce7e58374e1015ac1ec0bfb
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133177"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Anonieme open bare Lees toegang voor containers en blobs configureren

Azure Storage ondersteunt optionele anonieme lees toegang voor containers en blobs. Anonieme toegang tot uw gegevens is standaard nooit toegestaan. Tenzij u anonieme toegang expliciet inschakelt, moeten alle aanvragen naar een container en de bijbehorende blobs worden geautoriseerd met behulp van een Azure Active Directory (Azure AD) of een gedeelde sleutel autorisatie. Wanneer u de instelling van het open bare toegangs niveau van een container configureert om anonieme toegang toe te staan, kunnen clients gegevens in die container lezen zonder de aanvraag te autoriseren.

> [!WARNING]
> Wanneer een container voor open bare toegang is geconfigureerd, kan elke client gegevens in die container lezen. Open bare toegang geeft een potentieel beveiligings risico, dus als u dit niet nodig hebt, raadt micro soft u aan het niet toe te staan voor het opslag account. Zie [anonieme open bare Lees toegang tot containers en blobs voor komen](anonymous-read-access-prevent.md)voor meer informatie.

In dit artikel wordt beschreven hoe u anonieme open bare Lees toegang configureert voor een container en de bijbehorende blobs. Zie [open bare containers en blobs anoniem openen met .net](anonymous-read-access-client.md)voor meer informatie over het anoniem benaderen van BLOB-gegevens vanuit een client toepassing.

## <a name="about-anonymous-public-read-access"></a>Over anonieme open bare Lees toegang

Open bare toegang tot uw gegevens is altijd standaard toegestaan. Er zijn twee afzonderlijke instellingen die van invloed zijn op open bare toegang:

1. **Open bare toegang voor het opslag account toestaan.** Standaard staat een opslag account toe dat een gebruiker de juiste machtigingen heeft om open bare toegang tot een container mogelijk te maken. BLOB-gegevens zijn niet beschikbaar voor open bare toegang, tenzij de gebruiker de extra stap volgt om de open bare toegangs instelling van de container expliciet te configureren.
1. **Configureer de instelling voor open bare toegang van de container.** De open bare toegangs instelling van een container is standaard uitgeschakeld, wat betekent dat autorisatie is vereist voor elke aanvraag aan de container of de gegevens ervan. Een gebruiker met de juiste machtigingen kan de open bare toegangs instelling van een container wijzigen om anonieme toegang alleen in te scha kelen als anonieme toegang is toegestaan voor het opslag account.

De volgende tabel bevat een overzicht van de manier waarop beide instellingen van invloed zijn op open bare toegang voor een container.

| Instelling voor open bare toegang | Open bare toegang is uitgeschakeld voor een container (standaard instelling) | Open bare toegang voor een container is ingesteld op container | Open bare toegang een container is ingesteld op BLOB |
|--|--|--|--|
| Open bare toegang is niet toegestaan voor het opslag account | Geen open bare toegang tot een container in het opslag account. | Geen open bare toegang tot een container in het opslag account. De instelling van het opslag account heeft voor rang op de instelling van de container. | Geen open bare toegang tot een container in het opslag account. De instelling van het opslag account heeft voor rang op de instelling van de container. |
| Open bare toegang is toegestaan voor het opslag account (standaard instelling) | Geen open bare toegang tot deze container (standaard configuratie). | Open bare toegang is toegestaan voor deze container en de bijbehorende blobs. | Open bare toegang is toegestaan voor blobs in deze container, maar niet naar de container zelf. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Open bare Lees toegang voor een opslag account toestaan of weigeren

Open bare toegang is standaard toegestaan voor afzonderlijke containers in een opslag account. Wanneer open bare toegang is toegestaan, kan een gebruiker met de juiste machtigingen de open bare toegangs instelling van een container wijzigen om anonieme open bare toegang tot de gegevens in die container in te scha kelen.

Houd er rekening mee dat open bare toegang tot een container standaard altijd is uitgeschakeld en moet expliciet worden geconfigureerd om anonieme aanvragen toe te staan. Ongeacht de instelling van het opslag account zijn uw gegevens nooit beschikbaar voor open bare toegang, tenzij een gebruiker met de juiste machtigingen deze extra stap gebruikt om open bare toegang tot de container mogelijk te maken.

Het niet toestaan van open bare toegang voor het opslag account voor komt anonieme toegang tot alle containers en blobs in dat account. Wanneer open bare toegang niet is toegestaan voor het account, is het niet mogelijk om de instelling voor open bare toegang voor een container zo te configureren dat anonieme toegang is toegestaan. Voor een betere beveiliging raadt micro soft u aan om open bare toegang voor uw opslag accounts uit te stellen, tenzij uw scenario vereist dat gebruikers anoniem toegang hebben tot BLOB-resources.

> [!IMPORTANT]
> Het niet toestaan van open bare toegang voor een opslag account heeft voor rang op de instellingen voor open bare toegang voor alle containers in dat opslag account. Wanneer open bare toegang niet is toegestaan voor het opslag account, zullen toekomstige anonieme aanvragen voor dat account mislukken. Voordat u deze instelling wijzigt, moet u weten wat het effect is op client toepassingen die anoniem toegang hebben tot gegevens in uw opslag account. Zie [anonieme open bare Lees toegang tot containers en blobs voor komen](anonymous-read-access-prevent.md)voor meer informatie.

Als u open bare toegang voor een opslag account wilt toestaan of weigeren, gebruikt u de Azure Portal of Azure CLI om de **blobPublicAccess** -eigenschap van het account te configureren. Deze eigenschap is beschikbaar voor alle opslag accounts die zijn gemaakt met het Azure Resource Manager-implementatie model. Zie [overzicht van opslag accounts](../common/storage-account-overview.md)voor meer informatie.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Ga als volgt te werk om open bare toegang voor een opslag account in de Azure Portal toe te staan of niet toe te staan:

1. Ga in Azure Portal naar uw opslagaccount.
1. Zoek de **configuratie** -instelling onder **instellingen**.
1. Stel **open bare BLOB-toegang** in op **ingeschakeld** of **uitgeschakeld**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Scherm opname van het toestaan of weigeren van open bare BLOB-toegang voor het account":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u open bare toegang wilt toestaan of weigeren voor een opslag account met Azure CLI, moet u eerst de resource-ID voor uw opslag account ophalen door de opdracht [AZ resource show](/cli/azure/resource#az-resource-show) aan te roepen. Vervolgens roept u de opdracht [AZ resource update](/cli/azure/resource#az-resource-update) aan om de eigenschap **allowBlobPublicAccess** voor het opslag account in te stellen. Als u open bare toegang wilt toestaan, stelt u de eigenschap **allowBlobPublicAccess** in op True (waar). Als u dit niet wilt toestaan, stelt u deze in op **False**.

In het volgende voor beeld wordt open bare BLOB-toegang voor het opslag account niet toegestaan. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

---

> [!NOTE]
> Het niet toestaan van open bare toegang voor een opslag account heeft geen invloed op alle statische websites die worden gehost in dat opslag account. De **$Web** container is altijd openbaar toegankelijk.

## <a name="check-whether-public-access-is-allowed-for-a-storage-account"></a>Controleren of open bare toegang is toegestaan voor een opslag account

Als u wilt controleren of open bare toegang is toegestaan voor een opslag account, haalt u de waarde van de eigenschap **allowBlobPublicAccess** op. Als u deze eigenschap in één keer wilt controleren op een groot aantal opslag accounts, gebruikt u de Azure resource Graph Explorer.

> [!IMPORTANT]
> De eigenschap **allowBlobPublicAccess** is niet standaard ingesteld en retourneert geen waarde voordat u deze expliciet hebt ingesteld. Het opslag account staat open bare toegang toe wanneer de waarde van de eigenschap **Null** is of wanneer deze **waar**is.

### <a name="check-whether-public-access-is-allowed-for-a-single-storage-account"></a>Controleren of open bare toegang is toegestaan voor één opslag account

Als u wilt controleren of open bare toegang is toegestaan voor één opslag account met behulp van Azure CLI, roept u de opdracht [AZ resource show](/cli/azure/resource#az-resource-show) en de query voor de eigenschap **allowBlobPublicAccess** aan:

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-whether-public-access-is-allowed-for-a-set-of-storage-accounts"></a>Controleren of open bare toegang is toegestaan voor een set opslag accounts

Als u wilt controleren of open bare toegang is toegestaan in een set opslag accounts met optimale prestaties, kunt u de Azure resource Graph Explorer gebruiken in de Azure Portal. Voor meer informatie over het gebruik van de resource Graph Explorer raadpleegt u [Quick Start: uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer](/azure/governance/resource-graph/first-query-portal).

Als u de volgende query uitvoert in de resource Graph Explorer, wordt een lijst met opslag accounts geretourneerd en wordt de waarde van de eigenschap **allowBlobPublicAccess** voor elk account weer gegeven:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>Het niveau van de open bare toegang instellen voor een container

Als u anonieme gebruikers lees toegang wilt verlenen voor een container en de bijbehorende blobs, moet u eerst open bare toegang toestaan voor het opslag account en vervolgens het open bare toegangs niveau van de container instellen. Als open bare toegang wordt geweigerd voor het opslag account, kunt u open bare toegang voor een container niet configureren.

Wanneer open bare toegang is toegestaan voor een opslag account, kunt u een container configureren met de volgende machtigingen:

- **Geen open bare Lees toegang:** De container en de bijbehorende blobs kunnen alleen worden geopend met een geautoriseerde aanvraag. Deze optie is de standaard waarde voor alle nieuwe containers.
- **Open bare Lees toegang alleen voor blobs:** Blobs in de container kunnen worden gelezen door anonieme aanvragen, maar de container gegevens zijn niet anoniem beschikbaar. Anonieme clients kunnen de blobs in de container niet inventariseren.
- **Open bare Lees toegang voor container en de bijbehorende blobs:** Container-en BLOB-gegevens kunnen worden gelezen door anonieme aanvragen, met uitzonde ring van instellingen voor container machtigingen en meta gegevens van containers. Clients kunnen blobs in de container inventariseren door anonieme aanvragen, maar kunnen containers niet inventariseren binnen het opslag account.

U kunt het niveau van de open bare toegang voor een afzonderlijke BLOB niet wijzigen. Niveau van open bare toegang wordt alleen op container niveau ingesteld.

Als u het open bare toegangs niveau van een container wilt instellen, gebruikt u de Azure Portal of Azure CLI. U kunt het open bare toegangs niveau van de container instellen wanneer u de container maakt, of deze instelling bijwerken op een bestaande container.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om het niveau van de open bare toegang voor een of meer bestaande containers in de Azure Portal bij te werken:

1. Navigeer naar het overzicht van het opslag account in de Azure Portal.
1. Selecteer onder **BLOB service** op de menu Blade de optie **containers**.
1. Selecteer de containers waarvoor u het niveau van open bare toegang wilt instellen.
1. Gebruik de knop **toegangs niveau wijzigen** om de instellingen voor open bare toegang weer te geven.
1. Selecteer het gewenste open bare toegangs niveau van de vervolg keuzelijst **openbaar toegangs niveau** en klik op de knop OK om de wijziging toe te passen op de geselecteerde containers.

    ![Scherm afbeelding die laat zien hoe u het niveau van open bare toegang instelt in de portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Wanneer open bare toegang niet is toegestaan voor het opslag account, kan het open bare toegangs niveau van een container niet worden ingesteld. Als u het open bare toegangs niveau van de container probeert in te stellen, ziet u dat de instelling is uitgeschakeld, omdat open bare toegang niet is toegestaan voor het account.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Scherm opname waarin wordt getoond dat het open bare toegangs niveau van de container wordt geblokkeerd wanneer open bare toegang niet is toegestaan":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Als u het niveau van de open bare toegang voor een of meer containers met Azure CLI wilt bijwerken, roept u de opdracht [AZ storage container set permission](/cli/azure/storage/container#az-storage-container-set-permission) aan. Machtig deze bewerking door de account sleutel, een connection string of een Shared Access Signature (SAS) door te geven. De [set container-ACL](/rest/api/storageservices/set-container-acl) -bewerking waarmee het open bare toegangs niveau van de container wordt ingesteld, biedt geen ondersteuning voor autorisatie met Azure AD. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens bewerkingen](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie.

In het volgende voor beeld wordt de instelling voor open bare toegang voor een container ingesteld om anonieme toegang tot de container en de blobs in te scha kelen. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

Wanneer open bare toegang niet is toegestaan voor het opslag account, kan het open bare toegangs niveau van een container niet worden ingesteld. Als u het open bare toegangs niveau van de container probeert in te stellen, treedt er een fout op die aangeeft dat open bare toegang niet is toegestaan voor het opslag account.

---

## <a name="check-the-container-public-access-setting"></a>Controleer de instelling voor open bare toegang tot de container

Als u de instelling voor open bare toegang voor een of meer containers wilt controleren, kunt u de Azure Portal, Power shell, Azure CLI, een van de Azure Storage-client bibliotheken of de resource provider van Azure Storage gebruiken. De volgende secties bieden enkele voor beelden.  

### <a name="check-the-public-access-setting-for-a-single-container"></a>Controleer de instelling voor open bare toegang voor één container

Als u het niveau van open bare toegang wilt verkrijgen voor een of meer containers met Azure CLI, roept u de opdracht [AZ storage container show permission](/cli/azure/storage/container#az-storage-container-show-permission) aan. Machtig deze bewerking door de account sleutel, een connection string of een Shared Access Signature (SAS) door te geven. De [Get container-ACL](/rest/api/storageservices/get-container-acl) -bewerking die het open bare toegangs niveau van een container retourneert, biedt geen ondersteuning voor autorisatie met Azure AD. Zie [machtigingen voor het aanroepen van BLOB-en wachtrij gegevens bewerkingen](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)voor meer informatie.

In het volgende voor beeld wordt de instelling voor open bare toegang voor een container gelezen. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Controleer de instelling voor open bare toegang voor een set containers

Het is mogelijk om te controleren welke containers in een of meer opslag accounts zijn geconfigureerd voor open bare toegang door de containers te vermelden en de instelling voor open bare toegang te controleren. Deze benadering is een praktische optie wanneer een opslag account geen groot aantal containers bevat of wanneer u de instelling controleert over een klein aantal opslag accounts. De prestaties kunnen echter afnemen als u probeert een groot aantal containers te inventariseren.

In het volgende voor beeld wordt Power shell gebruikt om de instelling voor open bare toegang op te halen voor alle containers in een opslag account. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Volgende stappen

- [Anonieme open bare Lees toegang voor containers en blobs voor komen](anonymous-read-access-prevent.md)
- [Anoniem toegang krijgen tot open bare containers en blobs met .NET](anonymous-read-access-client.md)
- [Toegang tot Azure Storage autoriseren](../common/storage-auth.md)
