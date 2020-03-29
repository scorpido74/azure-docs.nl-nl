---
title: Azure-opties voor gegevensoverdracht voor kleine gegevenssets met een lage tot matige netwerkbandbreedte| Microsoft Documenten
description: Lees hoe u een Azure-oplossing voor gegevensoverdracht kiest wanneer u een lage tot matige netwerkbandbreedte in uw omgeving hebt en u van plan bent kleine gegevenssets over te zetten.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 3e6f4f3eb312f0d4d96a008c0944a9608d0bf4a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60397274"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Gegevensoverdracht voor kleine gegevenssets met weinig tot gemiddelde netwerkbandbreedte
 
Dit artikel geeft een overzicht van de oplossingen voor gegevensoverdracht wanneer u een lage tot matige netwerkbandbreedte in uw omgeving hebt en u van plan bent kleine gegevenssets over te zetten. Het artikel beschrijft ook de aanbevolen opties voor gegevensoverdracht en de respectievelijke sleutelcapaciteitsmatrix voor dit scenario.

Als u een overzicht wilt begrijpen van alle beschikbare opties voor gegevensoverdracht, gaat u naar [Een Azure-oplossing voor gegevensoverdracht kiezen.](storage-choose-data-transfer-solution.md)

## <a name="scenario-description"></a>Scenariobeschrijving

Kleine datasets verwijzen naar gegevensformaten in de volgorde van GB's naar een paar tbs's. Lage tot matige netwerkbandbreedte impliceert 45 Mbps (T3-verbinding in datacenter) tot 1 Gbps.

- Als u slechts een handvol bestanden overdraagt en u hoeft de gegevensoverdracht niet te automatiseren, u de tools met een grafische interface overwegen.
- Als u vertrouwd bent met systeembeheer, overweeg dan opdrachtregel of programmatische/ scriptingtools.

## <a name="recommended-options"></a>Aanbevolen opties

De opties die in dit scenario worden aanbevolen zijn:

- **Grafische interfacetools** zoals Azure Storage Explorer en Azure Storage in Azure-portal. Deze bieden een eenvoudige manier om uw gegevens te bekijken en snel een paar bestanden over te dragen.

    - **Azure Storage Explorer** - Met dit hulpprogramma voor meerdere platforms u de inhoud van uw Azure-opslagaccounts beheren. Hiermee u blobs, bestanden, wachtrijen, tabellen en Azure Cosmos DB-entiteiten uploaden, downloaden en beheren. Gebruik het met Blob-opslag om blobs en mappen te beheren, evenals blobs uploaden en downloaden tussen uw lokale bestandssysteem en Blob-opslag, of tussen opslagaccounts.
    - **Azure portal** - Azure Storage in Azure portal biedt een webinterface om bestanden te verkennen en nieuwe bestanden één voor één te uploaden. Dit is een goede optie als u geen tools wilt installeren of opdrachten wilt geven om uw bestanden snel te verkennen, of om gewoon een handvol nieuwe te uploaden.

- **Scripting/programmatische hulpprogramma's** zoals AzCopy/PowerShell/Azure CLI en Azure Storage REST API's.

    - **AzCopy** - Gebruik dit opdrachtregelgereedschap om eenvoudig gegevens van en naar Azure Blobs-, Bestanden- en Tabelopslag te kopiëren met optimale prestaties. AzCopy ondersteunt gelijktijdigheid en parallellisme en de mogelijkheid om kopieerbewerkingen te hervatten wanneer deze worden onderbroken.
    - **Azure PowerShell** - Voor gebruikers die vertrouwd zijn met systeembeheer, gebruikt u de Azure Storage-module in Azure PowerShell om gegevens over te dragen.
    - **Azure CLI** - Gebruik deze tool voor meerdere platforms om Azure-services te beheren en gegevens te uploaden naar Azure Storage.
    - **Azure Storage REST API's/SDKs** – Bij het bouwen van een toepassing u de toepassing ontwikkelen tegen Azure Storage REST API's/SDKs en de Azure-clientbibliotheken gebruiken die in meerdere talen worden aangeboden.


## <a name="comparison-of-key-capabilities"></a>Vergelijking van de belangrijkste mogelijkheden

In de volgende tabel worden de verschillen in sleutelmogelijkheden samengevat.

| Functie | Azure Opslagverkenner | Azure Portal | AzCopy<br>Azure PowerShell<br>Azure-CLI | Azure Storage REST API's of SDKs |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Beschikbaarheid | Downloaden en installeren <br>Zelfstandig gereedschap | Webgebaseerde verkenningshulpprogramma's in Azure-portal | Opdrachtregelgereedschap |Programmeerbare interfaces in .NET, Java, Python, JavaScript, C++, Go, Ruby en PHP |
| Grafische interface | Ja | Ja | Nee | Nee |
| Ondersteunde platforms | Windows, Mac, Linux | Web-based |Windows, Mac, Linux |Alle platforms |
| Toegestane Blob-opslagbewerkingen<br>voor blobs en mappen | Uploaden<br>Download<br>Beheren | Uploaden<br>Download<br>Beheren |Uploaden<br>Download<br>Beheren | Ja, aanpasbaar |
| Toegestane Data Lake Gen1-opslag<br>bewerkingen voor bestanden en mappen | Uploaden<br>Download<br>Beheren | Nee |Uploaden<br>Download<br>Beheren                   | Nee |
| Toegestane bestandsopslagbewerkingen<br>voor dossiers en mappen | Uploaden<br>Download<br>Beheren | Uploaden<br>Download<br>Beheren   |Uploaden<br>Download<br>Beheren | Ja, aanpasbaar |
| Toegestane tabelopslagbewerkingen<br>voor tabellen |Beheren | Nee |Tabelondersteuning in AzCopy v7 |Ja, aanpasbaar|
| Toegestane wachtrijopslag | Beheren | Nee  |Nee | Ja, is aanpasbaar|


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [overbrengen van gegevens met Azure Storage Explorer](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Gegevens overdragen met AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

