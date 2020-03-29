---
title: Azure Data Box Edge compute management | Microsoft Documenten
description: Beschrijft hoe u de randcompute-instellingen beheert, zoals trigger, modules, compute-configuratie bekijken en configuratie verwijderen via de Azure-portal op uw Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/20/2019
ms.author: alkohli
ms.openlocfilehash: a9daf1d59b03d283be999aaab559c6d60f6405dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65953118"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Compute beheren op uw Azure Data Box Edge

In dit artikel wordt beschreven hoe u rekenkracht beheert op uw Azure Data Box Edge. U de compute beheren via de Azure-portal of via de lokale web-gebruikersinterface. Gebruik de Azure-portal om modules, triggers en compute-configuratie te beheren en de lokale web-gebruikersinterface om rekeninstellingen te beheren.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Triggers beheren
> * Compute-configuratie beheren


## <a name="manage-triggers"></a>Triggers beheren

Gebeurtenissen zijn dingen die gebeuren binnen uw cloudomgeving of op uw apparaat waarop u mogelijk actie wilt ondernemen. Wanneer een bestand bijvoorbeeld in een share wordt gemaakt, is het een gebeurtenis. Triggers verhogen de gebeurtenissen. Voor uw Data Box Edge kunnen triggers reageren op bestandsgebeurtenissen of een planning.

- **Bestand:** deze triggers zijn een reactie op bestandsgebeurtenissen zoals het maken van een bestand, wijziging van een bestand.
- **Gepland:** deze triggers zijn in reactie op een schema dat u definiëren met een begindatum, begintijd en het herhalingsinterval.


### <a name="add-a-trigger"></a>Een trigger toevoegen

Neem de volgende stappen in de Azure-portal om een trigger te maken.

1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Edge compute > Trigger.** Selecteer **+ Trigger toevoegen** op de opdrachtbalk.

    ![Trigger toevoegen selecteren](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Geef in Trigger blade **toevoegen** een unieke naam voor uw trigger.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Selecteer een **type** voor de trigger. Kies **Bestand** wanneer de trigger een reactie heeft op een bestandsgebeurtenis. Selecteer **Gepland** wanneer u wilt dat de trigger op een bepaalde tijd begint en met een opgegeven herhalingsinterval wordt uitgevoerd. Afhankelijk van uw selectie wordt een andere set opties gepresenteerd.

    - **Bestandstrigger** - Kies uit de vervolgkeuzelijst een gemonteerd aandeel. Wanneer een bestandsgebeurtenis in dit aandeel wordt geactiveerd, wordt een Azure-functie aanroepen.

        ![SMB-share toevoegen](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **Geplande trigger** : geef de begindatum/-tijd en het herhalingsinterval op in uren, minuten of seconden. Voer ook de naam voor een onderwerp in. Een onderwerp geeft u de flexibiliteit om de trigger door te sturen naar een module die op het apparaat wordt geïmplementeerd.

        Een voorbeeld van `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`routetekenreeks is: .

        ![NFS-share toevoegen](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. Selecteer **Toevoegen** om de trigger te maken. Een melding laat zien dat de triggercreatie aan de gang is. Nadat de trigger is gemaakt, wordt het blad bijgewerkt om de nieuwe trigger weer te geven.
 
    ![Bijgewerkte triggerlijst](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Een trigger verwijderen

Neem de volgende stappen in de Azure-portal om een trigger te verwijderen.

1. Selecteer in de lijst met triggers de trigger die u wilt verwijderen.

    ![Trigger selecteren](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Klik met de rechtermuisknop en selecteer **Verwijderen**.

    ![Verwijderen selecteren](media/data-box-edge-manage-compute/add-trigger-1.png)

3. Klik op **Ja** als u om bevestiging wordt gevraagd.

    ![De verwijdering bevestigen](media/data-box-edge-manage-compute/add-trigger-1.png)

De lijst met triggers wordt bijgewerkt om de verwijdering weer te geven.

## <a name="manage-compute-configuration"></a>Compute-configuratie beheren

Gebruik de Azure-portal om de compute-configuratie weer te geven, een bestaande compute-configuratie te verwijderen of om de compute-configuratie te vernieuwen om toegangssleutels voor het IoT-apparaat en het IoT Edge-apparaat voor uw Data Box Edge te synchroniseren.

### <a name="view-compute-configuration"></a>Compute-configuratie weergeven

Neem de volgende stappen in de Azure-portal om de compute-configuratie voor uw apparaat weer te geven.

1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Edge compute > Modules**. Selecteer **Gegevens weergeven** op de opdrachtbalk.

    ![Compute weergeven selecteren](media/data-box-edge-manage-compute/view-compute-1.png)

2. Noteer de compute-configuratie op uw apparaat. Wanneer u compute hebt geconfigureerd, hebt u een IoT Hub-bron gemaakt. Onder die IoT Hub-bron zijn een IoT-apparaat en een IoT Edge-apparaat geconfigureerd. Alleen de Linux-modules worden ondersteund om op het IoT Edge-apparaat te draaien.

    ![Configuratie weergeven](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Compute-configuratie verwijderen

Neem de volgende stappen in de Azure-portal om de bestaande Edge-compute-configuratie voor uw apparaat te verwijderen.

1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Edge compute > Aan de slag.** Selecteer **Compute verwijderen** op de opdrachtbalk.

    ![Compute verwijderen selecteren](media/data-box-edge-manage-compute/remove-compute-1.png)

2. Als u de compute-configuratie verwijdert, moet u uw apparaat opnieuw configureren voor het geval u compute opnieuw moet gebruiken. Wanneer u om bevestiging wordt gevraagd, selecteert u **Ja**.

    ![Compute verwijderen selecteren](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>IoT-apparaat- en IoT Edge-apparaattoegangstoetsen synchroniseren

Wanneer u compute configureert op uw Data Box Edge, worden een IoT-apparaat en een IoT Edge-apparaat gemaakt. Deze apparaten krijgen automatisch symmetrische toegangssleutels toegewezen. Als best practice voor beveiliging worden deze toetsen regelmatig gedraaid via de IoT Hub-service.

Als u deze toetsen wilt roteren, u naar de IoT Hub-service gaan die u hebt gemaakt en het IoT-apparaat of het IoT Edge-apparaat selecteren. Elk apparaat heeft een primaire toegangssleutel en een secundaire toegangssleutels. Wijs de primaire toegangssleutel toe aan de secundaire toegangssleutel en regenereerde vervolgens de primaire toegangssleutel.

Als uw IoT-apparaat en IoT Edge-apparaatsleutels zijn gedraaid, moet u de configuratie op uw Data Box Edge vernieuwen om de nieuwste toegangssleutels te krijgen. De synchronisatie helpt het apparaat de nieuwste toetsen te krijgen voor uw IoT-apparaat en IoT Edge-apparaat. Data Box Edge gebruikt alleen de primaire toegangssleutels.

Volg de volgende stappen in de Azure-portal om de toegangssleutels voor uw apparaat te synchroniseren.

1. Ga in de Azure-portal naar uw Data Box Edge-bron en ga vervolgens naar **Edge compute > Aan de slag.** Selecteer **Configuratie vernieuwen** op de opdrachtbalk.

    ![Selecteer Configuratie vernieuwen](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. Selecteer **Ja** wanneer u om bevestiging wordt gevraagd.

     ![Ja selecteren wanneer u daarom wordt gevraagd](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. Sluit het dialoogvenster wanneer de synchronisatie is voltooid.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [beheren van edge-compute-netwerk via Azure-portal.](data-box-edge-extend-compute-access-modules.md)
