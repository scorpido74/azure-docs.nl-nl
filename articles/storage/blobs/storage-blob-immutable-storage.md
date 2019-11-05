---
title: Onveranderbare opslag voor Azure Storage-blobs | Microsoft Docs
description: Azure Storage biedt WORM (eenmaal schrijven, lezen) voor Blob-opslag (object) die gebruikers in staat stelt om gegevens op te slaan in een niet-bewerk bare, niet-aanpas bare status voor een opgegeven interval.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/01/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 0c7e178d520084dbf963c4c7ebaf9b8873a36938
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521058"
---
# <a name="store-business-critical-data-in-azure-blob-storage-immutably"></a>Bedrijfs kritieke gegevens opslaan in Azure Blob-opslag immutably 

Onveranderbare opslag voor Azure Blob-opslag stelt gebruikers in staat om bedrijfskritische gegevens objecten op te slaan in een WORM (eenmaal schrijven, gelezen). Met deze status worden de gegevens niet-kan worden gewist en niet kunnen worden gewijzigd voor een door de gebruiker opgegeven interval. Blob-objecten kunnen worden gemaakt en gelezen, maar niet gewijzigd of verwijderd voor de duur van de Bewaar periode. Onveranderbare opslag is ingeschakeld voor Algemeen v2-en Blob Storage-accounts in alle Azure-regio's.

## <a name="overview"></a>Overzicht

Onveranderbare opslag helpt de organisatie van de gezondheids zorg, financiële instellingen en gerelateerde industrieën, met name broker-dealer organisaties, om gegevens veilig op te slaan. Het kan ook worden gebruikt in elk scenario om kritieke gegevens te beschermen tegen wijziging of verwijdering. 

Typische toepassingen zijn onder andere:

- **Naleving van regelgeving**: onveranderbare opslag voor Azure Blob-opslag helpt organisaties bij het adresseren van 17a-4 (f), CFTC 1.31 (d), FINRA en andere voor Schriften. Een technisch document met Cohasset Associates bevat informatie over hoe onveranderbare opslag adressen deze regelgeving kan worden gedownload via de [micro soft-portal voor service vertrouwen](https://aka.ms/AzureWormStorage). De [Vertrouwenscentrum van Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) bevat gedetailleerde informatie over onze nalevings certificeringen.

- **Bewaren van beveiligde documenten**: onveranderbare opslag voor Azure Blob-opslag zorgt ervoor dat gegevens niet kunnen worden gewijzigd of verwijderd door een gebruiker, met inbegrip van gebruikers met beheerders bevoegdheden voor het account.

- **Juridische bewaring**: onveranderbare opslag voor Azure Blob-opslag biedt gebruikers de mogelijkheid gevoelige gegevens op te slaan die van cruciaal belang zijn voor rechts zaken of voor het gebruik van een onrecht matige afsluitings status voor de gewenste duur totdat de bewaring wordt verwijderd. Deze functie is niet beperkt tot juridische use-cases, maar kan ook worden beschouwd als een op gebeurtenissen gebaseerde bewaring of een Enter prise Lock, waarbij de nood zaak om gegevens te beveiligen op basis van gebeurtenis triggers of bedrijfs beleid vereist is.

Onveranderbare opslag ondersteunt het volgende:

- **[Ondersteuning voor Bewaar beleid op basis van tijd](#time-based-retention)** : gebruikers kunnen beleid instellen om gegevens op te slaan voor een opgegeven interval. Wanneer een Bewaar beleid op basis van tijd is ingesteld, kunnen blobs worden gemaakt en gelezen, maar niet gewijzigd of verwijderd. Nadat de Bewaar periode is verlopen, kunnen blobs worden verwijderd, maar niet worden overschreven.

- **[Ondersteuning voor beleid voor juridische bewaring](#legal-holds)** : als het Bewaar interval niet bekend is, kunnen gebruikers juridische bewaringen instellen om gegevens immutably op te slaan totdat de juridische bewaring is gewist.  Als er een beleid voor juridische bewaring is ingesteld, kunnen blobs worden gemaakt en gelezen, maar niet gewijzigd of verwijderd. Elke juridische bewaring is gekoppeld aan een door de gebruiker gedefinieerde alfanumerieke code (zoals een case-ID, gebeurtenis naam enzovoort) die wordt gebruikt als een id-teken reeks. 

- **Ondersteuning voor alle BLOB-lagen**: het virus beleid is onafhankelijk van de Azure Blob Storage-laag en is van toepassing op alle lagen: Hot, cool en Archive. Gebruikers kunnen gegevens overzetten naar de meest kosten geoptimaliseerde laag voor hun werk belastingen terwijl ze gegevens Onveranderbaarheid onderhouden.

- **Configuratie op container niveau**: gebruikers kunnen op het niveau van de Bewaar termijn op basis van een op tijd gebaseerd beleid voor het bewaren van inhoud en juridische bewarings codes configureren. Met behulp van eenvoudige instellingen op container niveau kunnen gebruikers op tijd gebaseerd Bewaar beleid maken en vergren delen, bewaar intervallen uitbreiden, juridische bewaringen instellen en wissen. Deze beleids regels zijn van toepassing op alle blobs in de container, zowel bestaande als nieuwe.

- **Ondersteuning voor de controle logboek registratie**: elke container bevat een beleids audit logboek. Het bevat Maxi maal zeven op tijd gebaseerde Bewaar opdrachten voor vergrendeld Bewaar beleid op basis van tijd en bevat de gebruikers-ID, het type opdracht, de tijds tempels en het Bewaar interval. Voor juridische bewaringen bevat het logboek de Tags gebruikers-ID, opdracht type, tijds tempels en juridische bewaring. Dit logboek wordt bewaard gedurende de levens duur van het beleid, overeenkomstig de richt lijnen van de 17a-4 (f). In het [activiteiten logboek van Azure](../../azure-monitor/platform/activity-logs-overview.md) wordt een uitgebreidere logboek van alle activiteiten van het controle vlak weer gegeven. bij het inschakelen van [Azure Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) worden de gegevenslaag bewerkingen bewaard en weer gegeven. Het is de verantwoordelijkheid van de gebruiker om deze logboeken permanent op te slaan, zoals vereist voor regelgevende of andere doel einden.

## <a name="how-it-works"></a>Het werkt als volgt

Onveranderbare opslag voor Azure Blob Storage ondersteunt twee soorten WORMen of onveranderbaar beleid: Bewaar periode en juridische bewaring op basis van tijd. Wanneer een Bewaar beleid op basis van tijd of wettelijk geblokkeerd wordt toegepast op een container, worden alle bestaande blobs in minder dan 30 seconden omgezet in een onveranderbare WORM status. Alle nieuwe blobs die naar die container worden geüpload, worden ook verplaatst naar de onveranderlijke status. Zodra alle blobs zijn verplaatst naar de onveranderbare status, wordt het onveranderbare beleid bevestigd en worden alle overschrijvings-of verwijder bewerkingen voor bestaande en nieuwe objecten in de onveranderbare container niet toegestaan.

Het verwijderen van containers en accounts is ook niet toegestaan als er blobs zijn die worden beveiligd door onveranderbaar beleid. De bewerking voor het verwijderen van een container mislukt als er ten minste één BLOB bestaat met een vergrendeld Bewaar beleid op basis van tijd of een juridische bewaring. De verwijdering van het opslagaccount mislukt als er minimaal één WORM-container is die een juridische bewaring of een blob met een actieve retentieperiode bevat. 

### <a name="time-based-retention"></a>Bewaar periode op basis van tijd

> [!IMPORTANT]
> Een Bewaar beleid op basis van tijd moet worden *vergrendeld* om de BLOB een compatibele onveranderlijke status (schrijven en verwijderen beveiligd) te hebben voor SEC 17a-4 (f) en andere naleving van regelgeving. We raden u aan het beleid binnen een redelijke hoeveelheid tijd te vergren delen, meestal minder dan 24 uur. De initiële status van een toegepast Bewaar beleid op basis van tijd is *ontgrendeld*, zodat u de functie kunt testen en wijzigingen kunt aanbrengen in het beleid voordat u het vergrendelt. Hoewel de status *ontgrendeld* Onveranderbaarheid beveiliging biedt, is het niet raadzaam om de *Ontgrendelde* status te gebruiken voor andere doel einden dan kortings functies. 

Wanneer een Bewaar beleid op basis van tijd op een container wordt toegepast, blijven alle blobs in de container onveranderbaar voor de duur van de *daad werkelijke* Bewaar periode. De effectieve retentieperiode voor bestaande blobs is gelijk aan het verschil tussen het tijdstip waarop de blob is gemaakt en de door de gebruiker opgegeven retentieperiode.

Voor nieuwe blobs is de effectieve retentieperiode gelijk aan de door de gebruiker opgegeven retentieperiode. Omdat gebruikers het Bewaar interval kunnen uitbreiden, gebruikt onveranderlijke opslag de meest recente waarde van het door de gebruiker opgegeven Bewaar interval om de ingangs periode te berekenen.

> [!TIP]
> **Voor beeld:** Een gebruiker maakt op tijd gebaseerd Bewaar beleid met een Bewaar interval van vijf jaar.
>
> De bestaande Blob in die container, _testblob1_, is één jaar geleden gemaakt. De daad werkelijke Bewaar periode voor _testblob1_ is vier jaar.
>
> Er is nu een nieuwe blob, _testblob2_, geüpload naar de container. De daad werkelijke Bewaar periode voor deze nieuwe blob is vijf jaar.

Een niet-vergrendeld op tijd gebaseerd Bewaar beleid wordt alleen aanbevolen voor het testen van functies en een beleid moet worden vergrendeld om compatibel te zijn met SEC 17a-4 (f) en andere naleving van regelgeving. Wanneer een Bewaar beleid op basis van tijd is vergrendeld, kan het beleid niet worden verwijderd en zijn Maxi maal 5 verhogingen van de ingangs periode Maxi maal toegestaan. Zie de sectie [aan](#getting-started) de slag voor meer informatie over het instellen en vergren delen van Bewaar beleid op basis van tijd.

### <a name="legal-holds"></a>Juridische bewaring

Wanneer u een juridische wacht ruimte instelt, blijven alle bestaande en nieuwe blobs in de status onveranderbaar totdat de juridische bewaring is gewist. Zie de sectie [aan](#getting-started) de slag voor meer informatie over het instellen en wissen van juridische bewaringen.

Een container kan op hetzelfde moment zowel een juridische bewaring als een op tijd gebaseerd Bewaar beleid hebben. Alle blobs in die container blijven onveranderbaar totdat alle juridische bewaringen zijn gewist, zelfs als hun ingangs periode is verlopen. Een BLOB blijft daarentegen onveranderbaar totdat de daad werkelijke Bewaar periode is verstreken, zelfs als alle juridische bewaringen zijn gewist.

De volgende tabel bevat de typen BLOB-bewerkingen die zijn uitgeschakeld voor de verschillende onveranderbare scenario's. Zie de API-documentatie van [Azure Blob service](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) voor meer informatie.

|Scenario  |BLOB-status  |BLOB-bewerkingen niet toegestaan  |
|---------|---------|---------|
|Effectieve retentieperiode voor de blob is nog niet verlopen en/of wettelijke bewaring is ingesteld     |Onveranderbaar: beveiligd tegen verwijderen en schrijven         | Plaats BLOB<sup>1</sup>, put blok<sup>1</sup>, blok lijst<sup>1</sup>, container verwijderen, Blob verwijderen, Blob-meta gegevens instellen, pagina plaatsen, Blob-eigenschappen instellen, moment opname-blob, incrementele Kopieer-blob, toevoeg blok         |
|Effectieve retentieperiode voor blob is verlopen     |Alleen beveiligd tegen schrijven (verwijderen is toegestaan)         |Put-BLOB<sup>1</sup>, blok<sup>1</sup>plaatsen, blokkerings lijst<sup>1</sup>toevoegen, Blob-meta gegevens instellen, pagina plaatsen, Blob-eigenschappen instellen, moment opname-blob, incrementele Kopieer-blob, blok toevoegen         |
|Alle juridische bewaringen zijn gewist en er is geen Bewaar beleid op basis van tijd ingesteld op de container     |Veranderlijk         |Geen         |
|Er wordt geen WORM beleid gemaakt (Bewaar periode op basis van tijd of juridische bewaring)     |Veranderlijk         |Geen         |

<sup>1</sup> met de toepassing kunnen deze bewerkingen een nieuwe Blob maken. Alle volgende overschrijvings bewerkingen op een bestaand BLOB-pad in een onveranderbare container zijn niet toegestaan.

## <a name="supported-values"></a>Ondersteunde waarden

### <a name="time-based-retention"></a>Bewaar periode op basis van tijd
- Voor een opslag account is het maximum aantal containers met vergrendeld op tijd gebaseerd onveranderbaar beleid 1.000.
- Het minimale Bewaar interval is 1 dag. De maximum waarde is 146.000 dagen (400 jaar).
- Voor een container is het maximum aantal bewerkingen voor het uitbreiden van een Bewaar interval voor vergrendelde, op tijd gebaseerde onveranderlijke beleids regels 5.
- Voor een container worden Maxi maal 7 op tijd gebaseerde controle logboeken voor Bewaar beleid bewaard voor een vergrendeld beleid.

### <a name="legal-hold"></a>Juridische bewaring
- Voor een opslag account is het maximum aantal containers met een geldige Hold instelling 1.000.
- Voor een container is het maximum aantal juridische bewarings codes 10.
- De minimale lengte van een geldige Hold-tag is 3 alfanumerieke tekens. De maximale lengte is 23 alfanumerieke tekens.
- Voor een container worden voor de duur van het beleid Maxi maal 10 controle logboeken voor juridische bewaring bewaard.

## <a name="pricing"></a>Prijzen

Er worden geen extra kosten in rekening gebracht voor het gebruik van deze functie. Onveranderbare gegevens worden op dezelfde manier geprijsd als normale, geveranderde gegevens. Zie de [pagina met Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/)voor prijs informatie over Azure Blob Storage.

## <a name="getting-started"></a>Aan de slag
Onveranderbare opslag is alleen beschikbaar voor Algemeen v2-en Blob Storage-accounts. Deze accounts moeten worden beheerd via [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Zie [een opslag account upgraden](../common/storage-account-upgrade.md)voor meer informatie over het upgraden van een bestaand algemeen v1-opslag account.

De meest recente releases van de [Azure Portal](https://portal.azure.com), [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)en [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) ondersteunen onveranderbare opslag voor Azure Blob Storage. Er is ook ondersteuning voor de [client bibliotheek](#client-libraries) .

### <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Maak een nieuwe container of selecteer een bestaande container om de blobs op te slaan die u in onveranderbare toestand wilt bewaren.
 De container moet zich in een GPv2-of Blob Storage-account bevinden.
2. Selecteer **toegangs beleid** in de container instellingen. Selecteer vervolgens **+ beleid toevoegen** onder **onveranderlijke Blob-opslag**.

    ![Container instellingen in de portal](media/storage-blob-immutable-storage/portal-image-1.png)

3. Als u op tijd gebaseerde Bewaar periode wilt inschakelen, selecteert u **op tijd gebaseerd bewaren** in de vervolg keuzelijst.

    !["Op tijd gebaseerde Bewaar periode" geselecteerd onder beleids type](media/storage-blob-immutable-storage/portal-image-2.png)

4. Voer het Bewaar interval in dagen (acceptabele waarden van 1 tot 146000 dagen) in.

    ![Vak ' Bewaar periode bijwerken naar '](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    De oorspronkelijke status van het beleid wordt ontgrendeld, zodat u de functie kunt testen en wijzigingen kunt aanbrengen in het beleid voordat u het vergrendelt. Het vergren delen van het beleid is essentieel voor de naleving van voor schriften zoals SEC 17a-4.

5. Het beleid vergren delen. Klik met de rechter muisknop op het weglatings teken ( **...** ) en het volgende menu wordt weer gegeven met extra acties:

    ![' Beleid vergren delen ' in het menu](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. Selecteer **beleid vergren delen** en bevestig de vergren deling. Het beleid is nu vergrendeld en kan niet worden verwijderd. alleen uitbrei dingen van het Bewaar interval zijn toegestaan. Het verwijderen van blobs en onderdrukkingen zijn niet toegestaan. 

    ![Het vergrendelings beleid in het menu bevestigen](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. Als u geldige bewaringen wilt inschakelen, selecteert u **+ beleid toevoegen**. Selecteer **juridisch vasthouden** in de vervolg keuzelijst.

    ![' Juridisch hold ' in het menu onder beleids type](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. Maak een juridische bewaring met een of meer tags.

    ![Vak ' label naam ' onder het beleids type](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. Als u een juridische blok kering wilt wissen, verwijdert u gewoon de code van de toegepaste juridische id.

### <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

De functie is opgenomen in de volgende opdracht groepen: `az storage container immutability-policy` en `az storage container legal-hold`. Voer `-h` hierop uit om de opdrachten weer te geven.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

De AZ. Storage-module ondersteunt onveranderbare opslag.  Voer de volgende stappen uit om de functie in te scha kelen:

1. Zorg ervoor dat de meest recente versie van PowerShellGet is geïnstalleerd: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Verwijder alle eerdere installatie van Azure PowerShell.
3. Installeer Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

De [Power shell-voorbeeld code](#sample-powershell-code) sectie verderop in dit artikel illustreert het gebruik van de functie.

---

## <a name="client-libraries"></a>Clientbibliotheken

De volgende client bibliotheken ondersteunen onveranderbare opslag voor Azure Blob-opslag:

- [.NET-client bibliotheek versie 7.2.0-Preview en hoger](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Node. js-client bibliotheek versie 4.0.0 en hoger](https://www.npmjs.com/package/azure-arm-storage)
- [Python-client bibliotheek versie 2.0.0 release Candi date 2 en hoger](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Java-client bibliotheek](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>Veelgestelde vragen

**Kunt u documentatie voor WORM compatibiliteit opgeven?**

Ja. Om naleving te documenteren, heeft micro soft een toonaangevende onafhankelijke beoordelings onderneming gehouden die is gespecialiseerd in record beheer en Information governance, Cohasset Associates, om te evalueren welke Azure onveranderbaar is Blob Storage en aan de naleving van de vereisten voor de financiële dienst verlening. Cohasset heeft gevalideerd dat door Azure onveranderbare Blob Storage, wanneer deze worden gebruikt om op tijd gebaseerde blobs in een WORM te bewaren, voldoet aan de relevante opslag vereisten van CFTC regel 1.31 (c)-(d), FINRA regel 4511 en seconde regel 17a-4. Micro soft heeft deze regels als doel gesteld, omdat deze de meest beschrijvende richt lijnen voor het bewaren van records voor financiële instellingen vertegenwoordigen. Het Cohasset-rapport is beschikbaar in het [vertrouwens centrum van micro soft-Services](https://aka.ms/AzureWormStorage). Neem contact op met de ondersteuning van Azure als u een verklaring van micro soft wilt aanvragen met betrekking tot de WORM naleving.

**Is de functie alleen van toepassing op blok-blobs, maar ook op pagina-en toevoeg-blobs?**

Onveranderbare opslag kan worden gebruikt in combi natie met een BLOB-type dat is ingesteld op het niveau van de container, maar we raden u aan om WORM te gebruiken voor containers die voornamelijk blok-blobs bevatten. In tegens telling tot blok-blobs moeten nieuwe pagina-blobs en toevoeg-blobs worden gemaakt buiten een WORM container en vervolgens worden gekopieerd in. Nadat u deze blobs naar een WORM container hebt gekopieerd, worden er geen nieuwe *toevoeg* -blobs of wijzigingen aan een pagina-BLOB toegestaan. Daarom is het instellen van een WORM beleid voor een container waarin Vhd's worden opgeslagen (pagina-blobs) voor actieve Virtual Machines sterk afgeraden, omdat hiermee de VM-schijf wordt vergrendeld.

**Moet ik een nieuw opslag account maken om deze functie te gebruiken?**

Nee, u kunt onveranderlijke opslag gebruiken met bestaande of nieuw gemaakte Algemeen v2-of Blob Storage-accounts. Deze functie is bedoeld voor gebruik met blok-blobs in GPv2-en Blob Storage-accounts. Algemeen v1-opslag accounts worden niet ondersteund, maar kunnen eenvoudig naar Algemeen v2 worden geüpgraded. Zie [een opslag account upgraden](../common/storage-account-upgrade.md)voor meer informatie over het upgraden van een bestaand algemeen v1-opslag account.

**Kan ik zowel een rechts Bewaar beleid op basis van een wacht tijd Toep assen?**

Ja, een container kan zowel een juridische bewaring als een op tijd gebaseerd Bewaar beleid hebben. Alle blobs in die container blijven onveranderbaar totdat alle juridische bewaringen zijn gewist, zelfs als hun ingangs periode is verlopen. Een BLOB blijft daarentegen onveranderbaar totdat de daad werkelijke Bewaar periode is verstreken, zelfs als alle juridische bewaringen zijn gewist.

**Zijn beleids regels voor juridische bewaring alleen voor juridische procedures of zijn er andere scenario's voor het gebruik?**

Nee, juridisch Hold is alleen de algemene term die wordt gebruikt voor een niet op tijd gebaseerd Bewaar beleid. Het hoeft niet alleen te worden gebruikt voor procedures die betrekking hebben op een rechts zaak. Beleid voor juridische bewaring is handig voor het uitschakelen van overschrijving en verwijderingen voor het beveiligen van belang rijke gegevens van de bedrijfs WORM, waarbij de Bewaar periode onbekend is. U kunt dit als bedrijfs beleid gebruiken voor het beveiligen van de workloads van uw missie kritieke WORM of voor het gebruik van het beleid als stagingbeleid voordat een aangepaste gebeurtenis trigger het gebruik van een op tijd gebaseerd Bewaar beleid vereist. 

**Kan ik een _vergrendeld_ op tijd gebaseerd Bewaar beleid of juridische bewaring verwijderen?**

Alleen een niet-vergrendeld op tijd gebaseerd Bewaar beleid kan uit een container worden verwijderd. Wanneer een Bewaar beleid op basis van tijd is vergrendeld, kan het niet meer worden verwijderd. alleen efficiënte Bewaar periode-uitbrei dingen zijn toegestaan. Codes voor juridische bewaring kunnen worden verwijderd. Wanneer alle juridische Tags worden verwijderd, wordt de juridische bewaring verwijderd.

**Wat gebeurt er als ik een container probeer te verwijderen die een *vergrendeld* retentiebeleid op basis van tijd of een juridische bewaring heeft?**

De bewerking voor het verwijderen van een container mislukt als er ten minste één BLOB bestaat met een vergrendeld Bewaar beleid op basis van tijd of een juridische bewaring. De bewerking voor het verwijderen van een container wordt alleen uitgevoerd als er geen blob met een actief Bewaar interval bestaat en er geen geldige bewaringen zijn. U moet de blobs verwijderen voordat u de container kunt verwijderen.

**Wat gebeurt er als ik een opslagaccount probeer te verwijderen met een WORM-container die een *vergrendeld* retentiebeleid op basis van tijd of een juridische bewaring heeft?**

De verwijdering van het opslagaccount mislukt als er minimaal één WORM-container is die een juridische bewaring of een blob met een actieve retentieperiode bevat. U moet alle WORM containers verwijderen voordat u het opslag account kunt verwijderen. Zie de vorige vraag voor meer informatie over het verwijderen van een container.

**Kan ik de gegevens verplaatsen tussen verschillende blob-lagen (dynamische toegang, statische toegang, archieftoegang) als de blob de status onveranderbaar heeft?**

Ja, u kunt de opdracht BLOB-laag instellen gebruiken om gegevens over de BLOB-lagen te verplaatsen terwijl de gegevens in de status compatibel onveranderlijk blijven. Onveranderbare opslag wordt ondersteund op de BLOB-lagen hot, cool en Archive.

**Wat gebeurt er als ik niet meer betaal en mijn retentieperiode niet is verlopen?**

In het geval van een niet-betaling, geldt het normale Bewaar beleid voor gegevens, zoals is vastgelegd in de voor waarden van uw contract met micro soft.

**Biedt u ook een evaluatie- of respijtperiode voor de zojuist beschreven functie?**

Ja. Wanneer een Bewaar beleid op basis van tijd voor het eerst wordt gemaakt, heeft dit een status die niet is *vergrendeld* . In deze status kunt u elke gewenste wijziging aanbrengen in het Bewaar interval, zoals verg Roten of verkleinen en zelfs het beleid verwijderen. Nadat het beleid is vergrendeld, blijft het vergrendeld totdat het retentie-interval verloopt. Met dit vergrendelde beleid voor komt u dat het Bewaar interval wordt verwijderd en gewijzigd. We raden u ten zeerste aan de *Ontgrendelde* status alleen te gebruiken voor proef doeleinden en het beleid binnen een periode van 24 uur te vergren delen. Deze procedures helpen u te voldoen aan de beschik over de SEC 17a-4 (f) en andere voor Schriften.

**Kan ik zacht verwijderen naast onveranderbaar BLOB-beleid gebruiken?**

Ja. [Zacht verwijderen voor Azure Blob Storage](storage-blob-soft-delete.md) is van toepassing op alle containers in een opslag account, ongeacht of het Bewaar beleid geldig is of op basis van tijd. We raden u aan om voorlopig verwijderen in te scha kelen voor extra beveiliging voordat onveranderbaar WORM beleid wordt toegepast en bevestigd. 

**Waar is de functie beschikbaar?**

Onveranderbare opslag is beschikbaar in de regio's openbaar, China en overheid van Azure. Als er geen onveranderlijke opslag ruimte beschikbaar is in uw regio, neemt u contact op met ondersteuning en e-mail azurestoragefeedback@microsoft.com.

## <a name="sample-powershell-code"></a>Power shell-voorbeeld code

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Het volgende Power shell-voorbeeld script is ter referentie. Met dit script maakt u een nieuw opslag account en een nieuwe container. Vervolgens wordt uitgelegd hoe u juridische bewaringen instelt en wist, een op tijd gebaseerd Bewaar beleid maakt en vergrendelt (ook wel een Onveranderbaarheid-beleid genoemd) en het Bewaar interval uitbreiden.

Het Azure Storage-account instellen en testen:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

Juridische bewaringen instellen en wissen:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Onveranderbaarheid-beleid maken of bijwerken:
```powershell
# with an account name or container name
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Onveranderbaarheid-beleid ophalen:
```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzRmStorageContainerImmutabilityPolicy -Container $containerObject
```

Onveranderbaarheid-beleid vergren delen (add-Force om de prompt te sluiten):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Onveranderbaarheid-beleid uitbreiden:
```powershell

# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Een ontgrendeld Onveranderbaarheid-beleid verwijderen (add-Force om de prompt te sluiten):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
