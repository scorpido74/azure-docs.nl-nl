---
title: Hiërarchische naamruimte azure Data Lake Storage Gen2
description: Beschrijft het concept van een hiërarchische naamruimte voor Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6ce94590a1d0de6941c27d972bdd1c4194080e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153074"
---
# <a name="azure-data-lake-storage-gen2-hierarchical-namespace"></a>Hiërarchische naamruimte azure Data Lake Storage Gen2

Een belangrijk mechanisme waarmee Azure Data Lake Storage Gen2 prestaties van het bestandssysteem kan bieden op de schaal en prijzen van objectopslag, is de toevoeging van een **hiërarchische naamruimte.** Hierdoor kan het verzamelen van objecten/bestanden binnen een account worden georganiseerd in een hiërarchie van mappen en geneste submappen op dezelfde manier als het bestandssysteem op uw computer is georganiseerd. Als een hiërarchische naamruimte is ingeschakeld, wordt een opslagaccount in staat om de schaalbaarheid en kosteneffectiviteit van objectopslag te bieden, met bestandssysteemsemantiek die bekend is met analyseengines en -frameworks.

## <a name="the-benefits-of-a-hierarchical-namespace"></a>De voordelen van een hiërarchische naamruimte

De volgende voordelen worden gekoppeld aan bestandssystemen die een hiërarchische naamruimte over blobgegevens implementeren:

- **Atoommapmanipulatie:** Object slaat een directoryhiërarchie bij benadering op door een conventie van inbeddingsslashes (/) in de objectnaam aan te nemen om padsegmenten aan te duiden. Hoewel deze conventie werkt voor het organiseren van objecten, biedt de conventie geen hulp bij acties zoals het verplaatsen, hernoemen of verwijderen van mappen. Zonder echte mappen moeten toepassingen mogelijk miljoenen afzonderlijke blobs verwerken om taken op directoryniveau te bereiken. Een hiërarchische naamruimte verwerkt deze taken daarentegen door één item (de bovenliggende map) bij te werken.

    Deze dramatische optimalisatie is vooral belangrijk voor veel big data analytics frameworks. Tools zoals Hive, Spark, etc. schrijven vaak uitvoer naar tijdelijke locaties en hernoemen de locatie aan het einde van de taak. Zonder hiërarchische naamruimte kan deze hernaam vaak langer duren dan het analyseproces zelf. Lagere taaklatentie is gelijk aan lagere total cost of ownership (TCO) voor analytics-workloads.

- **Vertrouwde interfacestijl:** Bestandssystemen worden goed begrepen door ontwikkelaars en gebruikers. Er is geen noodzaak om een nieuw opslagparadigma te leren wanneer u naar de cloud gaat, aangezien de bestandssysteeminterface die door Data Lake Storage Gen2 wordt blootgesteld, hetzelfde paradigma is dat wordt gebruikt door computers, groot en klein.

Een van de redenen waarom objectstores een hiërarchische naamruimte niet hebben ondersteund, is dat een hiërarchische naamruimte de schaal beperkt. De hiërarchische naamruimte van Data Lake Storage Gen2 schaalt echter lineair en degradeert noch de gegevenscapaciteit of -prestaties.

## <a name="deciding-whether-to-enable-a-hierarchical-namespace"></a>Beslissen of u een hiërarchische naamruimte wilt inschakelen

Nadat u een hiërarchische naamruimte in uw account hebt ingeschakeld, u deze niet terugzetten naar een platte naamruimte. Overweeg daarom of het zinvol is om een hiërarchische naamruimte in te schakelen op basis van de aard van de werkbelasting van uw objectopslag.

Sommige workloads krijgen mogelijk geen voordeel door een hiërarchische naamruimte in te schakelen. Voorbeelden hiervan zijn back-ups, beeldopslag en andere toepassingen waarbij objectorganisatie apart van de objecten zelf wordt opgeslagen (bijvoorbeeld in een aparte database). 

Hoewel de ondersteuning voor Blob-opslagfuncties en het Azure-service-ecosysteem blijft groeien, zijn er nog steeds enkele functies en Azure-services die nog niet worden ondersteund in accounts met een hiërarchische naamruimte. Zie [Bekende problemen](data-lake-storage-known-issues.md). 

In het algemeen raden we u aan een hiërarchische naamruimte in te schakelen voor opslagworkloads die zijn ontworpen voor bestandssystemen die mappen manipuleren. Dit omvat alle workloads die voornamelijk voor analyseverwerking zijn. Gegevenssets die een hoge mate van organisatie vereisen, profiteren ook door een hiërarchische naamruimte in te schakelen.

De redenen voor het inschakelen van een hiërarchische naamruimte worden bepaald door een TCO-analyse. Over het algemeen vereisen verbeteringen in de latentie van workloads als gevolg van opslagversnelling rekenresources voor minder tijd. Latentie voor veel workloads kan worden verbeterd als gevolg van atomaire directory manipulatie die is ingeschakeld door een hiërarchische naamruimte. In veel workloads vertegenwoordigt de compute resource > 85% van de totale kosten en dus komt zelfs een bescheiden vermindering van de latentie van workloads neer op een aanzienlijke hoeveelheid TCO-besparingen. Zelfs in gevallen waarin het inschakelen van een hiërarchische naamruimte de opslagkosten verhoogt, wordt de TCO nog steeds verlaagd vanwege lagere rekenkosten.

Zie [Azure Data Lake Storage Gen2-prijzen](https://azure.microsoft.com/pricing/details/storage/data-lake/)voor het analyseren van verschillen in prijzen voor gegevensopslag, transactieprijzen en opslagcapaciteittussen accounts met een platte hiërarchische naamruimte ten opzichte van een hiërarchische naamruimte.

## <a name="next-steps"></a>Volgende stappen

- [Een opslagaccount maken](./data-lake-storage-quickstart-create-account.md)
