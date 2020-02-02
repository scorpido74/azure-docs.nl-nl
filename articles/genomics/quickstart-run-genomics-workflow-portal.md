---
title: 'Snelstartgids: een werk stroom uitvoeren-Microsoft Genomics'
description: In de snelstartgids wordt uitgelegd hoe u invoergegevens kunt laden in Azure Blob Storage en een werkstroom kunt uitvoeren via de service Microsoft Genomics.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.openlocfilehash: 05b94ca9bd14392bad5288882a80f5c75590ef7b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931824"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Snelstart: een werkstroom uitvoeren via de Microsoft Genomics-service

In deze Snelstartgids uploadt u invoer gegevens naar een Azure Blob Storage-account en voert u een werk stroom uit via de Microsoft Genomics-service met behulp van de python-Genomics-client. Microsoft Genomics is een schaalbare, veilige service voor secundaire analyse waarbij genomen snel kunnen worden verwerkt. Hierbij wordt begonnen met onbewerkte leesbewerkingen en worden vervolgens uitgelijnde leesbewerkingen en variant-aanroepen geproduceerd. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Python 2.7.12 +](https://www.python.org/downloads/release/python-2714/), met `pip` geïnstalleerd en `python` in het systeempad. De Microsoft Genomics-client is niet compatibel met python 3. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Instellen: een Microsoft Genomics-account in Azure Portal maken

Als u een Microsoft Genomics account wilt maken, gaat u naar [een Genomics-account maken](https://portal.azure.com/#create/Microsoft.Genomics) in de Azure Portal. Als u nog geen Azure-abonnement hebt, maakt u er een voordat u een Microsoft Genomics-account maakt. 

![Microsoft Genomics op Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics op Azure Portal")

Configureer uw Genomics-account met de volgende informatie, zoals weergegeven in de vorige afbeelding. 

 |**Instelling**          |  **Voorgestelde waarde**  | **Beschrijving van veld** |
 |:-------------       |:-------------         |:----------            |
 |Abonnement         | De naam van uw abonnement|Dit is de factureringseenheid voor uw Azure-services; zie [Abonnementen](https://account.azure.com/Subscriptions) voor meer informatie over uw abonnement. |      
 |Resourcegroep       | MyResourceGroup       |  Met resourcegroepen kunt u meerdere Azure-resources (opslagaccount, Genomics-account enzovoort) in één groep indelen voor eenvoudig beheer. Zie [Resourcegroepen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) voor meer informatie. Zie [Naamgevingsregels](/azure/architecture/best-practices/resource-naming) voor geldige resourcegroepnamen. |
 |Accountnaam         | MyGenomicsAccount     |Kies een unieke account-id. Zie [Naamgevingsregels](/azure/architecture/best-practices/resource-naming) voor geldige namen. |
 |Locatie                   | US - west 2                    |    De service is beschikbaar in US - west 2, Europa - west en Azië - zuidoost |

U kunt **meldingen** selecteren in de bovenste menu balk om het implementatie proces te bewaken.

![Meldingen](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "Meldingen")

Zie [Wat is Microsoft Genomics?](overview-what-is-genomics.md) voor meer informatie over Microsoft Genomics.

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Instellen: de Python-client voor Microsoft Genomics installeren

U moet python en de Microsoft Genomics python-client installeren in uw lokale omgeving. 

### <a name="install-python"></a>Python installeren

De Microsoft Genomics python-client is compatibel met python 2.7.12 of een latere versie van 2.7. xx. 2.7.14 is de voorgestelde versie. U vindt de download [hier](https://www.python.org/downloads/release/python-2714/). 

> [!IMPORTANT]
> Python 3.x is niet compatibel met Python 2.7.xx.  MSGen is een Python 2.7-toepassing. Zorg er bij het uitvoeren van MSGen voor dat uw actieve Python-omgeving een 2.7.xx-versie van Python gebruikt. Er treden mogelijk fouten op bij gebruik van MSGen met een 3.x-versie van Python.

### <a name="install-the-microsoft-genomics-client"></a>De Microsoft Genomics-client installeren

Gebruik python `pip` om de Microsoft Genomics-client `msgen`te installeren. Bij de volgende instructies wordt ervan uitgegaan dat Python al op het systeempad staat. Als u problemen ondervindt met `pip` installatie niet herkend, moet u python en de submap scripts toevoegen aan het systeempad.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

Als u `msgen` niet wilt installeren als een systeem-brede binaire en systeem-brede Python-pakketten te wijzigen, gebruikt u de `–-user` markering met `pip`.
Als u de installatie op basis van een pakket of setup.py gebruikt, worden alle vereiste pakketten geïnstalleerd. Anders zijn de vereiste basis pakketten voor `msgen` 

 * [Azure-storage](https://pypi.python.org/pypi/azure-storage). 
 * [Requests](https://pypi.python.org/pypi/requests). 

U kunt deze pakketten installeren met `pip`, `easy_install` of via de standaard `setup.py`-procedures. 

### <a name="test-the-microsoft-genomics-client"></a>De Microsoft Genomics-client testen
Als u de Microsoft Genomics-client wilt testen, downloadt u het configuratie bestand van uw Genomics-account. Ga in het Azure Portal naar uw Genomics-account door **alle services** linksboven te selecteren en vervolgens te zoeken naar de accounts voor genomics.

![Microsoft Genomics op Azure Portal zoeken](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Microsoft Genomics op Azure Portal zoeken")

Selecteer het genomics-account dat u zojuist hebt gemaakt, navigeer naar **toegangs sleutels**en down load het configuratie bestand.

![Down load het configuratie bestand van Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Down load het configuratie bestand van Microsoft Genomics")

Test of de Python-client voor Microsoft Genomics werkt met de volgende opdracht

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Een Microsoft Azure Storage-account maken 
De service Microsoft Genomics verwacht dat invoeren worden opgeslagen als blok-blobs in een Azure-opslagaccount. Ook schrijft de service uitvoerbestanden als blok-blobs naar een door de gebruiker opgegeven container in een Azure-opslagaccount. De invoeren en uitvoeren kunnen zich in verschillende opslagaccounts bevinden.
Als u uw gegevens al in een Azure-opslagaccount hebt, hoeft u alleen ervoor te zorgen dat deze zich op dezelfde locatie bevinden als uw Genomics-account. Anders worden er kosten in rekening gebracht wanneer de Microsoft Genomics-service wordt uitgevoerd. Als u nog geen Azure Storage-account hebt, moet u er een maken en uw gegevens uploaden. U kunt [hier](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)meer informatie vinden over Azure-opslag accounts, zoals wat een opslag account is en welke services het biedt. Als u een Azure-opslag account wilt maken, gaat u naar [opslag account maken](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) in de Azure Portal.  

![Pagina opslag account maken](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "Pagina opslag account maken")

Configureer uw opslag account met de volgende informatie, zoals wordt weer gegeven in de vorige afbeelding. Gebruik de meeste van de standaard opties voor een opslag account, waarbij alleen wordt opgegeven dat het account BlobStorage is, niet voor algemeen gebruik. Blob-opslag kan twee tot vijf keer sneller zijn voor downloads en uploads.  Het standaard implementatie model, Azure Resource Manager, wordt aanbevolen.  

 |**Instelling**          |  **Voorgestelde waarde**  | **Beschrijving van veld** |
 |:-------------------------       |:-------------         |:----------            |
 |Abonnement         | Uw Azure-abonnement |Zie [Abonnementen](https://account.azure.com/Subscriptions) voor meer informatie over uw abonnement. |      
 |Resourcegroep       | MyResourceGroup       |  U kunt dezelfde resource groep selecteren als uw Genomics-account. Zie [naamgevings regels](/azure/architecture/best-practices/resource-naming) voor geldige namen van resource groepen |
 |Naam van opslagaccount         | MyStorageAccount     |Kies een unieke account-id. Zie [naamgevings regels](/azure/architecture/best-practices/resource-naming) voor geldige namen |
 |Locatie                  | US - west 2                  | Gebruik dezelfde locatie als de locatie van uw Genomics-account om de kosten voor uitgaand verkeer te verminderen en de latentie te verminderen.  | 
 |Prestaties                  | Standard                   | Standard is de standaardinstelling. Zie [Inleiding tot Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) voor meer informatie over standaard-en Premium-opslag accounts.    |
 |Soort account       | BlobStorage       |  Blob-opslag kan twee tot vijf keer sneller zijn dan algemeen gebruik voor downloads en uploads. |
 |Replicatie                  | Lokaal redundante opslag                  | Lokaal redundante opslag repliceert uw gegevens in het datacenter in de regio waarin u uw opslagaccount hebt gemaakt. Zie [Azure Storage-replicatie](https://docs.microsoft.com/azure/storage/common/storage-redundancy) voor meer informatie.    |
 |Toegangslaag                  | Warm                   | De toegangslaag Hot geeft aan dat de objecten in het opslagaccount vaker worden gebruikt.    |

Selecteer vervolgens **controleren + maken** om uw opslag account te maken. Net als bij het maken van uw Genomics-account kunt u **meldingen** selecteren in de bovenste menu balk om het implementatie proces te bewaken. 

## <a name="upload-input-data-to-your-storage-account"></a>De invoergegevens naar uw opslagaccount uploaden

De Microsoft Genomics-service verwacht gekoppelde eind Lees bewerkingen (fastq-of BAM-bestanden) als invoer bestanden. U kunt uw eigen gegevens uploaden, of openbare voorbeeldgegevens verkennen die voor u beschikbaar zijn gesteld. De openbare voorbeeldgegevens worden hier gehost:

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

U dient binnen uw opslagaccount een blob-container te maken voor uw invoergegevens en een tweede blob-container voor de uitvoergegevens.  Upload de invoergegevens naar uw blob-container voor invoer. Er kunnen verschillende hulpprogram ma's worden gebruikt om dit te doen, waaronder [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), [BlobPorter](https://github.com/Azure/blobporter)of [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Een werkstroom uitvoeren via de Microsoft Genomics-service met behulp van de Python-client 

Als u een werk stroom wilt uitvoeren via de Microsoft Genomics-service, bewerkt u het bestand *config. txt* om de invoer-en uitvoer opslag container voor uw gegevens op te geven.
Open het bestand *config. txt* dat u hebt gedownload van uw Genomics-account. De secties die u moet opgeven, zijn uw abonnements sleutel en de zes items onder, de naam van het opslag account, de sleutel en de naam van de container voor de invoer en uitvoer. U kunt deze informatie vinden door te navigeren in de Azure Portal om **toegang te krijgen tot sleutels** voor uw opslag account of rechtstreeks vanuit de Azure Storage Explorer.  

![Genomics-configuratie](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "Genomics-configuratie")

Als u GATK4 wilt uitvoeren, stelt u de para meter `process_name` in op `gatk4`.

De Genomics-service voert standaard VCF-bestanden uit. Als u een gVCF-uitvoer wilt gebruiken in plaats van een VCF-uitvoer (gelijk aan `-emitRefConfidence` in GATK 3. x en `emit-ref-confidence` in GATK 4. x), voegt u de para meter `emit_ref_confidence` toe aan uw *config. txt* en stelt u deze in op `gvcf`, zoals in de voor gaande afbeelding wordt weer gegeven.  Als u wilt terugkeren naar de VCF-uitvoer, moet u deze verwijderen uit het bestand *config. txt* of de para meter `emit_ref_confidence` instellen op `none`. 

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>De werkstroom in de service Microsoft Genomics verzenden met de client voor Microsoft Genomics

Gebruik de Python-client voor Microsoft Genomics om uw werkstroom te verzenden met de volgende opdracht:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

U kunt de status van uw werkstromen bekijken met de volgende opdracht: 
```python
msgen list -f c:\temp\config.txt 
```

Zodra uw werk stroom is voltooid, kunt u de uitvoer bestanden in uw Azure Storage-account weer geven in de uitvoer container die u hebt geconfigureerd. 

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u voor beelden van invoer gegevens geüpload naar Azure Storage en een werk stroom verzonden naar de Microsoft Genomics-service via de `msgen` python-client. Ga voor meer informatie over andere invoerbestandstypen die kunnen worden gebruikt met de Microsoft Genomics-service naar de volgende pagina's: [paired FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ or BAM](quickstart-input-multiple.md) (gekoppelde FASTQ/BAM/Meerdere FASTQ of BAM). U kunt ook deze zelfstudie lezen met behulp van de [Azure notebook tutorial](https://aka.ms/genomicsnotebook) (Zelfstudie in Azure-notitieblokken).
