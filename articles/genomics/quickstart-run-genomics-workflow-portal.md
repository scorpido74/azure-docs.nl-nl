---
title: 'Snelstart: een werkstroom uitvoeren - Microsoft Genomics'
description: In de snelstartgids wordt uitgelegd hoe u invoergegevens kunt laden in Azure Blob Storage en een werkstroom kunt uitvoeren via de service Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.openlocfilehash: 05b94ca9bd14392bad5288882a80f5c75590ef7b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76931824"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Snelstartgids: een werkstroom uitvoeren via de service Microsoft Genomics

In deze quickstart uploadt u invoergegevens naar een Azure Blob-opslagaccount en voert u een werkstroom uit via de Microsoft Genomics-service met behulp van de Python Genomics-client. Microsoft Genomics is een schaalbare, veilige service voor secundaire analyse waarbij genomen snel kunnen worden verwerkt. Hierbij wordt begonnen met onbewerkte leesbewerkingen en worden vervolgens uitgelijnde leesbewerkingen en variant-aanroepen geproduceerd. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 
- [Python 2.7.12+,](https://www.python.org/downloads/release/python-2714/)met `pip` `python` geïnstalleerd en in uw systeempad. De Microsoft Genomics-client is niet compatibel met Python 3. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Instellen: een Microsoft Genomics-account in Azure Portal maken

Als u een Microsoft Genomics-account wilt maken, navigeert u naar [Een Genomics-account maken](https://portal.azure.com/#create/Microsoft.Genomics) in de Azure-portal. Als u nog geen Azure-abonnement hebt, maakt u er een voordat u een Microsoft Genomics-account maakt. 

![Microsoft Genomics op Azure-portal](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics op Azure-portal")

Configureer uw Genomics-account met de volgende informatie, zoals weergegeven in de vorige afbeelding. 

 |**Instelling**          |  **Voorgestelde waarde**  | **Veldbeschrijving** |
 |:-------------       |:-------------         |:----------            |
 |Abonnement         | De naam van uw abonnement|Dit is de factureringseenheid voor uw Azure-services; zie [Abonnementen](https://account.azure.com/Subscriptions) voor meer informatie over uw abonnement. |      
 |Resourcegroep       | MyResourceGroup       |  Met resourcegroepen kunt u meerdere Azure-resources (opslagaccount, Genomics-account enzovoort) in één groep indelen voor eenvoudig beheer. Zie [Resourcegroepen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) voor meer informatie. Zie [Naamgevingsregels](/azure/architecture/best-practices/resource-naming) voor geldige resourcegroepnamen. |
 |Accountnaam         | MyGenomicsAccount     |Kies een unieke account-id. Zie [Naamgevingsregels](/azure/architecture/best-practices/resource-naming) voor geldige namen. |
 |Locatie                   | VS - west 2                    |    De service is beschikbaar in VS - west 2, Europa - west en Azië - zuidoost |

U **Meldingen** selecteren in de bovenste menubalk om het implementatieproces te controleren.

![Meldingen](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "Meldingen")

Zie Wat is Microsoft Genomics voor meer informatie over Microsoft [Genomics?](overview-what-is-genomics.md)

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Instellen: de Python-client voor Microsoft Genomics installeren

U moet zowel Python als de Microsoft Genomics Python-client in uw lokale omgeving installeren. 

### <a name="install-python"></a>Python installeren

De Microsoft Genomics Python-client is compatibel met Python 2.7.12 of een latere 2.7.xx-versie. 2.7.14 is de voorgestelde versie. U vindt de download [hier](https://www.python.org/downloads/release/python-2714/). 

> [!IMPORTANT]
> Python 3.x is niet compatibel met Python 2.7.xx.  MSGen is een Python 2.7-toepassing. Zorg er bij het uitvoeren van MSGen voor dat uw actieve Python-omgeving een 2.7.xx-versie van Python gebruikt. Er treden mogelijk fouten op bij gebruik van MSGen met een 3.x-versie van Python.

### <a name="install-the-microsoft-genomics-client"></a>De Microsoft Genomics-client installeren

Gebruik `pip` Python om de `msgen`Microsoft Genomics-client te installeren. Bij de volgende instructies wordt ervan uitgegaan dat Python al op het systeempad staat. Als u problemen `pip` hebt met installatie die niet wordt herkend, moet u Python en de submap scripts toevoegen aan uw systeempad.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

Als u niet wilt `msgen` installeren als een systeembrede binaire en wijzigen systeem-brede Python pakketten, gebruik maken van de `–-user` vlag met `pip`.
Als u de installatie op basis van een pakket of setup.py gebruikt, worden alle vereiste pakketten geïnstalleerd. Anders zijn de basisvereiste pakketten voor `msgen` 

 * [Azure-opslag](https://pypi.python.org/pypi/azure-storage). 
 * [Requests](https://pypi.python.org/pypi/requests). 

U kunt deze pakketten installeren met `pip`, `easy_install` of via de standaard `setup.py`-procedures. 

### <a name="test-the-microsoft-genomics-client"></a>De Microsoft Genomics-client testen
Download het config-bestand van uw Genomics-account om de Microsoft Genomics-client te testen. Navigeer in de Azure-portal naar uw Genomics-account door **Alle services** linksboven te selecteren en vervolgens naar Genomics-accounts te zoeken en te selecteren.

![Microsoft Genomics zoeken op Azure-portal](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Microsoft Genomics zoeken op Azure-portal")

Selecteer het Genomics-account dat u zojuist hebt gemaakt, navigeer naar **Toegangssleutels**en download het configuratiebestand.

![Config-bestand downloaden van Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Config-bestand downloaden van Microsoft Genomics")

Test of de Python-client voor Microsoft Genomics werkt met de volgende opdracht

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Een Microsoft Azure Storage-account maken 
De service Microsoft Genomics verwacht dat invoeren worden opgeslagen als blok-blobs in een Azure-opslagaccount. Ook schrijft de service uitvoerbestanden als blok-blobs naar een door de gebruiker opgegeven container in een Azure-opslagaccount. De invoeren en uitvoeren kunnen zich in verschillende opslagaccounts bevinden.
Als u uw gegevens al in een Azure-opslagaccount hebt, hoeft u alleen ervoor te zorgen dat deze zich op dezelfde locatie bevinden als uw Genomics-account. Anders worden er kosten in rekening gebracht bij het uitvoeren van de Microsoft Genomics-service. Als u nog geen Azure-opslagaccount hebt, moet u er een maken en uw gegevens uploaden. Hier vindt u meer informatie over [Azure-opslagaccounts,](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)inclusief wat een opslagaccount is en welke services het biedt. Als u een Azure-opslagaccount wilt maken, navigeert u naar [Opslagaccount maken](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) in de Azure-portal.  

![Pagina Opslagaccount maken](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "Pagina Opslagaccount maken")

Configureer uw opslagaccount met de volgende gegevens, zoals weergegeven in de vorige afbeelding. Gebruik de meeste standaardopties voor een opslagaccount, waarbij alleen wordt opgegeven dat het account BlobStorage is, geen algemeen doel. Blob-opslag kan twee tot vijf keer sneller zijn voor downloads en uploads.  Het standaardimplementatiemodel Azure Resource Manager wordt aanbevolen.  

 |**Instelling**          |  **Voorgestelde waarde**  | **Veldbeschrijving** |
 |:-------------------------       |:-------------         |:----------            |
 |Abonnement         | Uw Azure-abonnement |Zie [Abonnementen](https://account.azure.com/Subscriptions) voor meer informatie over uw abonnement. |      
 |Resourcegroep       | MyResourceGroup       |  U dezelfde resourcegroep selecteren als uw Genomics-account. Zie [Naamregels voor](/azure/architecture/best-practices/resource-naming) geldige resourcegroepen |
 |Naam van opslagaccount         | MyStorageAccount     |Kies een unieke account-id. Zie [Naamregels voor](/azure/architecture/best-practices/resource-naming) geldige namen |
 |Locatie                  | VS - west 2                  | Gebruik dezelfde locatie als de locatie van uw Genomics-account om de kosten voor uitweiding te verminderen en de latentie te verminderen.  | 
 |Prestaties                  | Standard                   | Standard is de standaardinstelling. Zie [Inleiding tot Microsoft Azure-opslag](https://docs.microsoft.com/azure/storage/common/storage-introduction) voor meer informatie over standaard- en premiumopslagaccounts    |
 |Soort account       | BlobOpslag       |  Blob-opslag kan twee tot vijf keer sneller zijn dan algemeen gebruik voor downloads en uploads. |
 |Replicatie                  | Lokaal redundante opslag                  | Lokaal redundante opslag repliceert uw gegevens in het datacenter in de regio waarin u uw opslagaccount hebt gemaakt. Zie [Azure Storage-replicatie](https://docs.microsoft.com/azure/storage/common/storage-redundancy) voor meer informatie    |
 |Toegangslaag                  | Warm                   | De toegangslaag Hot geeft aan dat de objecten in het opslagaccount vaker worden gebruikt.    |

Selecteer vervolgens **Controleren + maken** om uw opslagaccount te maken. Net als bij het aanmaken van uw Genomics-account u **Meldingen** selecteren in de bovenste menubalk om het implementatieproces te controleren. 

## <a name="upload-input-data-to-your-storage-account"></a>De invoergegevens naar uw opslagaccount uploaden

De Microsoft Genomics-service verwacht gekoppelde eindreads (fastq- of bam-bestanden) als invoerbestanden. U kunt uw eigen gegevens uploaden, of openbare voorbeeldgegevens verkennen die voor u beschikbaar zijn gesteld. De openbare voorbeeldgegevens worden hier gehost:

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

U dient binnen uw opslagaccount een blob-container te maken voor uw invoergegevens en een tweede blob-container voor de uitvoergegevens.  Upload de invoergegevens naar uw blob-container voor invoer. Hiervoor kunnen verschillende hulpprogramma's worden gebruikt, waaronder [Microsoft Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/) [BlobPorter](https://github.com/Azure/blobporter)of [AzCopy.](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Een werkstroom uitvoeren via de Microsoft Genomics-service met behulp van de Python-client 

Als u een werkstroom wilt uitvoeren via de Microsoft Genomics-service, bewerkt u het *config.txt-bestand* om de invoer- en uitvoeropslagcontainer voor uw gegevens op te geven.
Open het *config.txt-bestand* dat u hebt gedownload van uw Genomics-account. De secties die u moet opgeven zijn uw abonnementssleutel en de zes items onderaan, de naam van het opslagaccount, de sleutel en de containernaam voor zowel de invoer als de uitvoer. U deze informatie vinden door te navigeren in de Azure-portal naar **Toegangssleutels** voor uw opslagaccount of rechtstreeks vanuit de Azure Storage Explorer.  

![Genomics config](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "Genomics config")

Als u GATK4 wilt uitvoeren, `process_name` stelt `gatk4`u de parameter in op .

De Genomics-service voert standaard VCF-bestanden uit. Als u een gVCF-uitvoer wilt in plaats `-emitRefConfidence` van een VCF-uitgang (gelijk aan in GATK 3.x `emit-ref-confidence` en in GATK 4.x), voegt u `emit_ref_confidence` de parameter toe aan uw *config.txt* en stelt u deze in op `gvcf`, zoals in de voorgaande figuur wordt weergegeven.  Als u terug wilt gaan naar VCF-uitvoer, verwijdert u `emit_ref_confidence` deze `none`uit het *config.txt-bestand* of stelt u de parameter in op . 

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>De werkstroom in de service Microsoft Genomics verzenden met de client voor Microsoft Genomics

Gebruik de Python-client voor Microsoft Genomics om uw werkstroom te verzenden met de volgende opdracht:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

U kunt de status van uw werkstromen bekijken met de volgende opdracht: 
```python
msgen list -f c:\temp\config.txt 
```

Zodra uw werkstroom is voltooid, u de uitvoerbestanden in uw Azure-opslagaccount weergeven in de uitvoercontainer die u hebt geconfigureerd. 

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u voorbeeldinvoergegevens geüpload naar Azure-opslag `msgen` en een werkstroom ingediend bij de Microsoft Genomics-service via de Python-client. Zie de volgende pagina's: [gekoppelde FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ of BAM](quickstart-input-multiple.md)voor meer informatie over andere invoerbestandstypen die kunnen worden gebruikt met de Microsoft Genomics-service. U kunt ook deze zelfstudie lezen met behulp van de [Azure notebook tutorial](https://aka.ms/genomicsnotebook) (Zelfstudie in Azure-notitieblokken).
