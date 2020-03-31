---
title: Gegevens uit gebeurtenishubs vastleggen in Azure Data Lake Storage Gen1 | Microsoft Documenten
description: Azure Data Lake Storage Gen1 gebruiken om gegevens uit gebeurtenishubs vast te leggen
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bb67c1769510710b368bef4dc0b501f939b3427e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265660"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Azure Data Lake Storage Gen1 gebruiken om gegevens uit gebeurtenishubs vast te leggen

Meer informatie over het gebruik van Azure Data Lake Storage Gen1 om gegevens vast te leggen die zijn ontvangen door Azure Event Hubs.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

* **Een Azure Data Lake Storage Gen1-account**. Zie [Aan de slag met Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)voor instructies over het maken van een account.

*  **Een naamruimte voor gebeurtenishubs**. Zie [Naamruimte voor gebeurtenishubs maken](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace)voor instructies. Zorg ervoor dat het Data Lake Storage Gen1-account en de naamruimte van Gebeurtenishubs in hetzelfde Azure-abonnement zitten.


## <a name="assign-permissions-to-event-hubs"></a>Machtigingen toewijzen aan gebeurtenishubs

In deze sectie maakt u een map in het account waarin u de gegevens uit gebeurtenishubs wilt vastleggen. U kent ook machtigingen toe aan gebeurtenishubs, zodat deze gegevens kunnen schrijven naar een Data Lake Storage Gen1-account. 

1. Open het Data Lake Storage Gen1-account waar u gegevens uit gebeurtenishubs wilt vastleggen en klik vervolgens op **Data Explorer.**

    ![Gegevensmeeropslag Gen1-gegevensverkenner](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Gegevensmeeropslag Gen1-gegevensverkenner")

1.  Klik **op Nieuwe map** en voer een naam in voor de map waarin u de gegevens wilt vastleggen.

    ![Een nieuwe map maken in Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Een nieuwe map maken in Data Lake Storage Gen1")

1. Machtigingen toewijzen aan de basis van Data Lake Storage Gen1. 

    a. Klik **op Gegevensverkenner,** selecteer de hoofdmap van het Gen1-account gegevensopslagopslag en klik op **Access**.

    ![Machtigingen toewijzen voor de basis van Gegevensmeeropslag Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Machtigingen toewijzen voor de basis van Gegevensmeeropslag Gen1")

    b. Klik **onder Openen**op **Toevoegen,** klik op Gebruiker of groep **selecteren**en zoek `Microsoft.EventHubs`vervolgens naar . 

    ![Machtigingen toewijzen voor de basis van Gegevensmeeropslag Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Machtigingen toewijzen voor de basis van Gegevensmeeropslag Gen1")
    
    Klik **op Selecteren**.

    c. Klik **onder Machtigingen toewijzen**op Machtigingen **selecteren**. **Machtigingen** instellen om uit te **voeren**. **Voeg Toevoegen aan** deze map en alle kinderen **in.** **Voeg Toevoegen in op** een **toegangsmachtigingsvermelding en een standaardmachtigingsvermelding**.

    > [!IMPORTANT]
    > Wanneer u een nieuwe maphiërarchie maakt voor het vastleggen van gegevens die zijn ontvangen door Azure Event Hubs, is dit een eenvoudige manier om toegang tot de doelmap te garanderen.  Het toevoegen van machtigingen aan alle kinderen van een map op het hoogste niveau met veel onderliggende bestanden en mappen kan echter lang duren.  Als uw hoofdmap een groot aantal bestanden en mappen bevat, kan `Microsoft.EventHubs` het sneller zijn om **Uitvoermachtigingen** voor elke map afzonderlijk toe te voegen aan elke map in het pad naar uw uiteindelijke bestemmingsmap. 

    ![Machtigingen toewijzen voor de basis van Gegevensmeeropslag Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Machtigingen toewijzen voor de basis van Gegevensmeeropslag Gen1")

    Klik op **OK**.

1. Wijs machtigingen toe voor de map onder het Data Lake Storage Gen1-account waar u gegevens wilt vastleggen.

    a. Klik **op Gegevensverkenner,** selecteer de map in het Gen1-account gegevensopslagopslag en klik vervolgens op **Access**.

    ![Machtigingen toewijzen voor de map Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Machtigingen toewijzen voor de map Data Lake Storage Gen1")

    b. Klik **onder Openen**op **Toevoegen,** klik op Gebruiker of groep **selecteren**en zoek `Microsoft.EventHubs`vervolgens naar . 

    ![Machtigingen toewijzen voor de map Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Machtigingen toewijzen voor de map Data Lake Storage Gen1")
    
    Klik **op Selecteren**.

    c. Klik **onder Machtigingen toewijzen**op Machtigingen **selecteren**. **Machtigingen** instellen voor **lezen, schrijven** en **uitvoeren.** **Voeg Toevoegen aan** deze map en alle kinderen **in.** Tot slot de optie **Toevoegen als** **een toegangsmachtigingsvermelding en een standaardmachtigingsvermelding instellen**.

    ![Machtigingen toewijzen voor de map Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Machtigingen toewijzen voor de map Data Lake Storage Gen1")
    
    Klik op **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Gebeurtenishubs configureren om gegevens vast te leggen op Data Lake Storage Gen1

In deze sectie maakt u een gebeurtenishub in de naamruimte van een gebeurtenishubs. U configureert ook de gebeurtenishub om gegevens vast te leggen op een Azure Data Lake Storage Gen1-account. In deze sectie wordt ervan uitgegaan dat u al een naamruimte voor gebeurtenishubs hebt gemaakt.

1. Klik in het **deelvenster Overzicht** van de naamruimte van gebeurtenishubs op **+ gebeurtenishub**.

    ![Event Hub maken](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Event Hub maken")

1. Geef de volgende waarden op om gebeurtenishubs te configureren om gegevens vast te leggen op Data Lake Storage Gen1.

    ![Event Hub maken](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Event Hub maken")

    a. Geef een naam op voor de Gebeurtenishub.
    
    b. Stel voor deze zelfstudie **het aantal partities** en **het behoud van berichten** in op de standaardwaarden.
    
    c. **Vastleggen** instellen **op aan**. Stel het **tijdvenster** (hoe vaak vast te leggen) en **Groottevenster** (gegevensgrootte in om vast te leggen). 
    
    d. Selecteer Azure **Data Lake Store** voor Capture **Provider**en selecteer vervolgens het Data Lake Storage Gen1-account dat u eerder hebt gemaakt. Voer voor **Data Lake Path**de naam in van de map die u hebt gemaakt in het Data Lake Storage Gen1-account. U hoeft alleen het relatieve pad naar de map op te geven.

    e. Laat de **bestandsnaamnotaties van het voorbeeld vastleggen** aan de standaardwaarde. Deze optie regelt de mapstructuur die onder de opnamemap wordt gemaakt.

    f. Klik **op Maken**.

## <a name="test-the-setup"></a>De installatie testen

U de oplossing nu testen door gegevens naar de Azure Event Hub te verzenden. Volg de instructies bij [Gebeurtenissen verzenden naar Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Zodra u de gegevens begint te verzenden, ziet u de gegevens die worden weergegeven in Data Lake Storage Gen1 met behulp van de door u opgegeven mapstructuur. U ziet bijvoorbeeld een mapstructuur, zoals weergegeven in de volgende schermafbeelding, in uw Data Lake Storage Gen1-account.

![Voorbeeld van EventHub-gegevens in Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Voorbeeld van EventHub-gegevens in Data Lake Storage Gen1")

> [!NOTE]
> Zelfs als er geen berichten binnenkomen in Gebeurtenishubs, schrijft Event Hubs lege bestanden met alleen de kopteksten in het Data Lake Storage Gen1-account. De bestanden worden tegelijkertijd geschreven met het interval dat u hebt opgegeven tijdens het maken van de gebeurtenishubs.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Gegevens analyseren in Data Lake Storage Gen1

Zodra de gegevens zich in Data Lake Storage Gen1 begeven, u analytische taken uitvoeren om de gegevens te verwerken en te kraken. Zie [USQL Avro Voorbeeld](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) over hoe u dit doen met Azure Data Lake Analytics.
  

## <a name="see-also"></a>Zie ook
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Gegevens uit Azure Storage Blobs kopiëren naar Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
