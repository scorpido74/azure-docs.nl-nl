---
title: Azure Machine Learning Studio inschakelen in een virtueel netwerk
titleSuffix: Azure Machine Learning
description: Gebruik Azure Machine Learning Studio om toegang te krijgen tot gegevens die zijn opgeslagen in een virtueel netwerk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 58395463c494a95a8842cddbe4d51544ce03d212
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713365"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Azure Machine Learning Studio gebruiken in een virtueel Azure-netwerk

In dit artikel leert u hoe u Azure Machine Learning Studio kunt gebruiken in een virtueel netwerk. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> - Toegang tot de Studio vanaf een resource binnen een virtueel netwerk.
> - De Studio toegang geven tot gegevens die zijn opgeslagen in een virtueel netwerk.
> - Begrijpen hoe opslag beveiliging wordt beïnvloed door de Studio.

Dit artikel is deel vijf uit een serie van vijf delen die u begeleidt bij het beveiligen van een Azure Machine Learning werk stroom. We raden u ten zeerste aan om in [deel één het overzicht van VNet](how-to-network-security-overview.md) te lezen om eerst inzicht te krijgen in de algehele architectuur. 

Zie de andere artikelen in deze serie:

[1. VNet-overzicht](how-to-network-security-overview.md)  >  [2. Beveilig de werk ruimte](how-to-secure-workspace-vnet.md)  >  [3. De trainings omgeving](how-to-secure-training-vnet.md)  >  [4 beveiligen. Beveilig de vergoedings omgeving](how-to-secure-inferencing-vnet.md)  >  [5. De functionaliteit van Studio inschakelen](how-to-enable-studio-virtual-network.md)


> [!IMPORTANT]
> Hoewel het meren deel van Studio werkt met gegevens die zijn opgeslagen in een virtueel netwerk, hebben geïntegreerde notebooks __dit niet__. Geïntegreerde notebooks bieden geen ondersteuning voor het gebruik van opslag die zich in een virtueel netwerk bevindt. In plaats daarvan kunt u Jupyter-notebooks van een reken instantie gebruiken. Zie de sectie [toegang tot gegevens in een reken instantie-notitie blok]() voor meer informatie.


## <a name="prerequisites"></a>Vereisten

+ Lees het [overzicht van netwerk beveiliging](how-to-network-security-overview.md) voor inzicht in algemene scenario's voor virtuele netwerken en de algehele architectuur van virtuele netwerken.

+ Een bestaand virtueel netwerk en subnet dat moet worden gebruikt.

+ Een bestaande [Azure machine learning-werk ruimte waarvoor een persoonlijke koppeling is ingeschakeld](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ [Het virtuele netwerk is toegevoegd](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)met een bestaand Azure Storage-account.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Toegang tot de Studio vanaf een resource binnen het VNet

Als u de Studio opent vanuit een resource binnen een virtueel netwerk (bijvoorbeeld een reken instantie of virtuele machine), moet u uitgaand verkeer van het virtuele netwerk naar de Studio toestaan. 

Als u bijvoorbeeld netwerk beveiligings groepen (NSG) gebruikt om uitgaand verkeer te beperken, voegt __u een regel__ toe aan een servicetag bestemming __AzureFrontDoor.__ front-end.

## <a name="access-data-using-the-studio"></a>Toegang tot gegevens met behulp van Studio

Nadat u [een Azure Storage-account aan uw virtuele netwerk hebt toegevoegd](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts), moet u uw opslag account configureren voor het gebruik van [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) om de toegang tot uw gegevens te verlenen aan de Studio. De Studio ondersteunt opslag accounts die zijn geconfigureerd voor het gebruik van service-eind punten of privé-eind punten. Opslag accounts gebruiken standaard service-eind punten. Zie [privé-eind punten voor Azure Storage gebruiken](../storage/common/storage-private-endpoints.md) om persoonlijke eind punten voor opslag in te scha kelen

Als u de beheerde identiteit niet inschakelt, wordt deze fout ook weer gegeven, `Error: Unable to profile this dataset. This might be because your data is stored behind a virtual network or your data does not support profile.` worden de volgende bewerkingen uitgeschakeld:

* Bekijk de gegevens in de Studio.
* Gegevens visualiseren in de ontwerp functie.
* Een AutoML-experiment verzenden.
* Een label project starten.

De Studio ondersteunt het lezen van gegevens uit de volgende gegevensopslag typen in een virtueel netwerk:

* Azure Blob
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="configure-datastores-to-use-managed-identity"></a>Gegevens opslag configureren voor het gebruik van beheerde identiteit

Azure Machine Learning maakt gebruik van [data stores](concept-data.md#datastores) om verbinding te maken met opslag accounts. Gebruik de volgende stappen om uw gegevens opslag te configureren voor het gebruik van beheerde identiteit. 

1. Selecteer __gegevens opslag__in de Studio.

1. Selecteer __+ Nieuw gegevens archief__als u een nieuwe gegevens opslag wilt maken.

    Als u een bestaande gegevens opslag wilt bijwerken, selecteert u de gegevens opslag en selecteert u __referenties bijwerken__.

1. Selecteer in de instellingen voor gegevens archief de optie __Ja__  __Als u wilt dat Azure machine learning-service toegang heeft tot de opslag met behulp van een door werk ruimte beheerde identiteit__.


Met deze stappen wordt de door de werk ruimte beheerde identiteit als __lezer__ aan de opslag service toegevoegd met behulp van het Azure resource-based Access Control (RBAC). Met __Reader__ toegang kan de werk ruimte Firewall instellingen ophalen en ervoor zorgen dat gegevens het virtuele netwerk niet verlaten.

> [!NOTE]
> Het kan tot tien minuten duren voordat deze wijzigingen van kracht worden.

## <a name="technical-notes-for-managed-identity"></a>Technische opmerkingen voor beheerde identiteit

Het gebruik van beheerde identiteit voor toegang tot opslag Services heeft betrekking op enkele beveiligings overwegingen. Deze overwegingen zijn uniek voor het type opslag account dat u wilt openen. In deze sectie worden de wijzigingen voor elk type opslag account beschreven.

### <a name="azure-blob-storage"></a>Azure Blob Storage

Voor __Azure Blob Storage__wordt de door de werk ruimte beheerde identiteit ook toegevoegd als een [gegevens lezer voor blobs](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , zodat deze gegevens uit de Blob-opslag kan lezen.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Toegangs beheer Azure Data Lake Storage Gen2

U kunt zowel RBAC-als POSIX-toegangscontrole lijsten (Acl's) gebruiken om de toegang tot gegevens binnen een virtueel netwerk te beheren.

Als u RBAC wilt gebruiken, voegt u de door de werk ruimte beheerde identiteit toe aan de rol [BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Zie [toegangs beheer op basis van rollen](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control)voor meer informatie.

Als u Acl's wilt gebruiken, kan de door de werk ruimte beheerde identiteit toegang krijgen net als elk ander beveiligings principe. Zie [toegangs beheer lijsten voor bestanden en mappen](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)voor meer informatie.

### <a name="azure-data-lake-storage-gen1-access-control"></a>Toegangs beheer Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 ondersteunt alleen Access Control Lists in POSIX-stijl. U kunt de door de werk ruimte beheerde identiteits toegang toewijzen aan bronnen net als andere beveiligings principes. Zie [toegangs beheer in azure data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md)voor meer informatie.

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database opgenomen gebruiker

Als u toegang wilt krijgen tot gegevens die zijn opgeslagen in een Azure SQL Database met behulp van beheerde identiteit, moet u een SQL-Inge sloten gebruiker maken die is gekoppeld aan de beheerde identiteit. Zie voor meer informatie over het maken van een gebruiker van een externe provider [opgenomen gebruikers maken die zijn toegewezen aan Azure AD-identiteiten](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

Nadat u een SQL-Inge sloten gebruiker hebt gemaakt, moet u er machtigingen voor verlenen met behulp van de [opdracht T-SQL toewijzen](https://docs.microsoft.com/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-default-datastore"></a>Standaard gegevens opslag van Azure Machine Learning Designer

De ontwerp functie gebruikt het opslag account dat aan uw werk ruimte is gekoppeld om standaard uitvoer op te slaan. U kunt het echter opgeven om de uitvoer op te slaan in een gegevens opslag waartoe u toegang hebt. Als uw omgeving gebruikmaakt van virtuele netwerken, kunt u deze besturings elementen gebruiken om ervoor te zorgen dat uw gegevens veilig en toegankelijk blijven.

Een nieuwe standaard opslag voor een pijp lijn instellen:

1. Selecteer in een pijp lijn concept het **tandwiel pictogram instellingen** in de buurt van de titel van de pijp lijn.
1. Selecteer **standaard gegevens opslag selecteren**.
1. Geef een nieuwe gegevens opslag op.

U kunt ook de standaard gegevens opslag per module negeren. Dit geeft u de controle over de opslag locatie voor elke afzonderlijke module.

1. Selecteer de module waarvan u de uitvoer wilt opgeven.
1. Vouw de sectie **uitvoer instellingen** uit.
1. Selecteer **standaard instellingen voor uitvoer negeren**.
1. Selecteer **uitvoer instellingen instellen**.
1. Geef een nieuwe gegevens opslag op.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is een optioneel onderdeel van een serie virtuele netwerken met vier delen. Raadpleeg de rest van de artikelen voor meer informatie over het beveiligen van een virtueel netwerk:

* [Deel 1: overzicht van virtueel netwerk](how-to-network-security-overview.md)
* [Deel 2: de resources van de werk ruimte beveiligen](how-to-secure-workspace-vnet.md)
* [Deel 3: de trainings omgeving beveiligen](how-to-secure-training-vnet.md)
* [Deel 4: de omgeving voor het afwijzen van interferentie beveiligen](how-to-secure-inferencing-vnet.md)