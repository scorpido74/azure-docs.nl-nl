---
title: Opties voor Azure-gegevens overdracht voor kleine gegevens sets met lage tot gemiddeld netwerk bandbreedte | Microsoft Docs
description: Meer informatie over het kiezen van een Azure-oplossing voor gegevens overdracht wanneer u weinig netwerk bandbreedte in uw omgeving hebt en u van plan bent om kleine gegevens sets over te brengen.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 4f21e7f64338b7d50ca401081bf73ca0c1a1c88f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85504300"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Gegevensoverdracht voor kleine gegevenssets met weinig tot gemiddelde netwerkbandbreedte
 
Dit artikel bevat een overzicht van de oplossingen voor gegevens overdracht wanneer u weinig netwerk bandbreedte in uw omgeving hebt en u van plan bent om kleine gegevens sets over te brengen. In het artikel worden ook de aanbevolen opties voor gegevens overdracht en de bijbehorende matrix voor de belangrijkste functionaliteit voor dit scenario beschreven.

Voor een overzicht van alle beschik bare opties voor gegevens overdracht gaat u naar [een Azure-oplossing voor gegevens overdracht kiezen](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Scenariobeschrijving

Kleine gegevens sets verwijzen naar de grootte van het GB naar een paar TBs. Laag tot gemiddeld netwerk bandbreedte impliceert 45 Mbps (T3-verbinding in Data Center) tot 1 Gbps.

- Als u slechts een aantal bestanden overbrengt en u de gegevens overdracht niet meer nodig hebt, kunt u de hulpprogram ma's overwegen met een grafische interface.
- Als u vertrouwd bent met systeem beheer, overweeg dan opdracht regel-of programmeer-en script hulpprogramma's.

## <a name="recommended-options"></a>Aanbevolen opties

De opties die worden aanbevolen in dit scenario zijn:

- **Grafische interface-hulpprogram ma's** zoals Azure Storage Explorer en Azure Storage in azure Portal. Deze bieden een eenvoudige manier om uw gegevens weer te geven en snel een paar bestanden over te dragen.

    - **Azure Storage Explorer** -dit hulp programma voor meerdere platformen biedt u de mogelijkheid om de inhoud van uw Azure-opslag accounts te beheren. Hiermee kunt u blobs, bestanden, wacht rijen, tabellen en Azure Cosmos DB entiteiten uploaden, downloaden en beheren. Gebruik het met Blob Storage voor het beheren van blobs en mappen, en voor het uploaden en downloaden van blobs tussen uw lokale bestands systeem en Blob-opslag of tussen opslag accounts.
    - **Azure Portal** -Azure Storage in azure Portal biedt een webinterface om bestanden te verkennen en nieuwe bestanden een voor een te uploaden. Dit is een goede optie als u geen hulp middelen wilt installeren of opdrachten uit te voeren om uw bestanden snel te kunnen verkennen of als u gewoon een aantal nieuwe berichten wilt uploaden.

- **Scripting/programmatische hulpprogram ma's** zoals AzCopy/Power shell/Azure CLI en Azure Storage rest api's.

    - **AzCopy** : gebruik dit opdracht regel programma om eenvoudig gegevens te kopiëren van en naar Azure-blobs,-bestanden en-tabel opslag met optimale prestaties. AzCopy ondersteunt gelijktijdigheid en parallellisme, en de mogelijkheid om Kopieer bewerkingen te hervatten wanneer deze worden onderbroken.
    - **Azure PowerShell** -voor gebruikers die vertrouwd zijn met systeem beheer, gebruikt u de module Azure Storage in azure PowerShell om gegevens over te dragen.
    - **Azure cli** : gebruik dit platformoverschrijdende hulp programma voor het beheren van Azure-Services en het uploaden van gegevens naar Azure Storage.
    - **Azure Storage rest api's/sdk's** : wanneer u een toepassing bouwt, kunt u de toepassing ontwikkelen op Azure Storage rest Api's/sdk's en de Azure-client bibliotheken gebruiken die in meerdere talen worden aangeboden.


## <a name="comparison-of-key-capabilities"></a>Vergelijking van de belangrijkste mogelijkheden

De volgende tabel bevat een overzicht van de verschillen in de belangrijkste mogelijkheden.

| Functie | Azure Opslagverkenner | Azure Portal | AzCopy<br>Azure PowerShell<br>Azure CLI | Azure Storage REST Api's of Sdk's |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Beschikbaarheid | Downloaden en installeren <br>Zelfstandig hulp programma | Webgebaseerde hulpprogram ma's voor onderzoek in Azure Portal | Opdracht regel programma |Programmeer bare interfaces in .NET, Java, Python, java script, C++, go, Ruby en PHP |
| Grafische interface | Ja | Ja | Nee | Nee |
| Ondersteunde platforms | Windows, Mac, Linux | Op internet gebaseerde |Windows, Mac, Linux |Alle platforms |
| Toegestane bewerkingen voor Blob-opslag<br>voor blobs en mappen | Uploaden<br>Downloaden<br>Beheren | Uploaden<br>Downloaden<br>Beheren |Uploaden<br>Downloaden<br>Beheren | Ja, aanpasbaar |
| Data Lake gen1-opslag toegestaan<br>bewerkingen voor bestanden en mappen | Uploaden<br>Downloaden<br>Beheren | No |Uploaden<br>Downloaden<br>Beheren                   | No |
| Toegestane bewerkingen voor bestands opslag<br>voor bestanden en mappen | Uploaden<br>Downloaden<br>Beheren | Uploaden<br>Downloaden<br>Beheren   |Uploaden<br>Downloaden<br>Beheren | Ja, aanpasbaar |
| Toegestane bewerkingen voor tabel opslag<br>voor tabellen |Beheren | No |Tabel ondersteuning in AzCopy V7 |Ja, aanpasbaar|
| Toegestane wachtrij opslag | Beheren | Nee  |Nee | Ja, is aanpasbaar|


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [overdragen van gegevens met Azure Storage Explorer](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Gegevens overdragen met AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

