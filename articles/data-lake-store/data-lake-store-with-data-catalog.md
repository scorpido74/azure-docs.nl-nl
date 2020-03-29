---
title: Gegevens registreren uit Azure Data Lake Storage Gen1 in Azure-gegevenscatalogus | Microsoft Documenten
description: Gegevens registreren uit Azure Data Lake Storage Gen1 in Azure-gegevenscatalogus
services: data-lake-store,data-catalog
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fd887560c0011fb1ec2141e33f02f7e3d8a39c81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60196537"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Gegevens registreren uit Azure Data Lake Storage Gen1 in Azure-gegevenscatalogus
In dit artikel leert u hoe u Azure Data Lake Storage Gen1 integreren met Azure Data Catalog om uw gegevens binnen een organisatie te ontdekken door deze te integreren met gegevenscatalogus. Zie [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md)voor meer informatie over het catalogiseren van gegevens. Zie Algemene scenario's voor Azure [Data Catalog](../data-catalog/data-catalog-common-scenarios.md)voor het begrijpen van scenario's waarin u gegevenscatalogus gebruiken.

## <a name="prerequisites"></a>Vereisten
Voordat u met deze zelfstudie begint, moet u het volgende hebben of hebben gedaan:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Schakel uw Azure-abonnement** in voor Data Lake Storage Gen1. Zie [Instructies](data-lake-store-get-started-portal.md).
* **Een Data Lake Storage Gen1-account**. Volg de instructies bij [Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure Portal.](data-lake-store-get-started-portal.md) Maak voor deze zelfstudie een Data Lake Storage Gen1-account genaamd **datacatalogstore**.

    Zodra u het account hebt gemaakt, uploadt u er een voorbeeldgegevensset naar. Voor deze zelfstudie uploaden we alle CSV-bestanden onder de map **AmbulanceData** in de [Azure Data Lake Git Repository.](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/) U verschillende clients, zoals [Azure Storage Explorer,](https://storageexplorer.com/)gebruiken om gegevens naar een blobcontainer te uploaden.
* **Azure-gegevenscatalogus**. Uw organisatie moet al een Azure Data Catalog hebben gemaakt voor uw organisatie. Voor elke organisatie is slechts één catalogus toegestaan.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Gegevens Lake Storage Gen1 registreren als bron voor gegevenscatalogus

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Ga `https://azure.microsoft.com/services/data-catalog`naar en klik op **Aan de slag**.
1. Meld u aan bij de portal van Azure Data Catalog en klik op **Gegevens publiceren**.

    ![Een gegevensbron registreren](./media/data-lake-store-with-data-catalog/register-data-source.png "Een gegevensbron registreren")
1. Klik op de volgende pagina op **Toepassing starten**. Hiermee wordt het bestand van het toepassingsmanifest op uw computer gedownload. Dubbelklik op het manifestbestand om de toepassing te starten.
1. Klik op de welkomstpagina op **Aanmelden**en voer uw referenties in.

    ![Welkomstscherm](./media/data-lake-store-with-data-catalog/welcome.screen.png "Welkomstscherm")
1. Selecteer op de pagina Een gegevensbron selecteren de optie **Azure Data Lake Store**en klik op **Volgende**.

    ![Gegevensbron selecteren](./media/data-lake-store-with-data-catalog/select-source.png "Gegevensbron selecteren")
1. Geef op de volgende pagina de naam van Data Lake Storage Gen1 op die u wilt registreren in gegevenscatalogus. Laat de andere opties als standaard staan en klik op **Verbinden**.

    ![Verbinding maken met gegevensbron](./media/data-lake-store-with-data-catalog/connect-to-source.png "Verbinding maken met gegevensbron")
1. De volgende pagina kan worden onderverdeeld in de volgende segmenten.

    a. Het vak **Serverhiërarchie** vertegenwoordigt de mapstructuur van De Gegevensmeeropslag Gen1-account. **$Root** de hoofdmap van Data Lake Storage Gen1 vertegenwoordigt en **AmbulanceData** vertegenwoordigt de map die is gemaakt in de hoofdmap van het Data Lake Storage Gen1-account.

    b. In het vak **Beschikbare objecten** worden de bestanden en mappen weergegeven onder de map **AmbulanceData.**

    c. **Vakobjecten die moeten worden geregistreerd,** bevat de bestanden en mappen die u wilt registreren in azure-gegevenscatalogus.

    ![Gegevensstructuur weergeven](./media/data-lake-store-with-data-catalog/view-data-structure.png "Gegevensstructuur weergeven")
1. Voor deze zelfstudie moet u alle bestanden in de map registreren. Klik daarvoor op de knop (![objecten verplaatsen](./media/data-lake-store-with-data-catalog/move-objects.png "Objecten verplaatsen")) om alle bestanden te verplaatsen naar objecten die moeten **worden geregistreerd.**

    Omdat de gegevens worden geregistreerd in een gegevenscatalogus voor de hele organisatie, is het een aanbevolen benadering om enkele metagegevens toe te voegen die u later gebruiken om de gegevens snel te lokaliseren. U bijvoorbeeld een e-mailadres toevoegen voor de eigenaar van de gegevens (bijvoorbeeld iemand die de gegevens uploadt) of een tag toevoegen om de gegevens te identificeren. De onderstaande schermopname toont een tag die u aan de gegevens toevoegt.

    ![Gegevensstructuur weergeven](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Gegevensstructuur weergeven")

    Klik **op Registreren**.
1. De volgende schermopname geeft aan dat de gegevens met succes zijn geregistreerd in de gegevenscatalogus.

    ![Registratie voltooid](./media/data-lake-store-with-data-catalog/registration-complete.png "Gegevensstructuur weergeven")
1. Klik **op Portal weergeven** om terug te gaan naar de portal Gegevenscatalogus en controleer of u nu toegang hebt tot de geregistreerde gegevens van de portal. Als u de gegevens wilt doorzoeken, u de tag gebruiken die u hebt gebruikt tijdens het registreren van de gegevens.

     ![Zoekgegevens in catalogus](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Zoekgegevens in catalogus")
1. U nu bewerkingen uitvoeren, zoals het toevoegen van aantekeningen en documentatie aan de gegevens. Zie de volgende links voor meer informatie.

    * [Gegevensbronnen annoteren in gegevenscatalogus](../data-catalog/data-catalog-how-to-annotate.md)
    * [Documentgegevensbronnen in gegevenscatalogus](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Zie ook
* [Gegevensbronnen annoteren in gegevenscatalogus](../data-catalog/data-catalog-how-to-annotate.md)
* [Documentgegevensbronnen in gegevenscatalogus](../data-catalog/data-catalog-how-to-documentation.md)
* [Data Lake Storage Gen1 integreren met andere Azure-services](data-lake-store-integrate-with-other-services.md)
