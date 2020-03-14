---
title: Zelf studie voor het filteren en analyseren van gegevens met Compute op Azure Data Box Edge | Microsoft Docs
description: Leer hoe u een rekenprocesrol configureert in Data Box Edge en deze gebruikt om gegevens te transformeren voordat ze naar Azure worden verzonden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b641ae62ba6e0cdacaeb46b1ffee2f02c7544763
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239019"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Zelf studie: gegevens transformeren met Azure Data Box Edge

In deze zelfstudie wordt beschreven hoe u een rekenprocesrol configureert op uw Azure Data Box Edge-apparaat. Wanneer u de rekenrol hebt geconfigureerd, kan Data Box Edge gegevens transformeren voordat deze naar Azure worden verzonden.

Deze procedure kan ongeveer tien tot 15 minuten duren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Reken kracht configureren
> * Shares toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

 
## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarde wordt voldaan voordat u een rekenprocesrol configureert op uw Data Box Edge-apparaat:

- U hebt het Data Box Edge-apparaat geactiveerd zoals beschreven in [Azure Data Box Edge aansluiten, instellen en activeren](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Reken kracht configureren

Als u de reken kracht op uw Data Box Edge wilt configureren, maakt u een IoT Hub resource.

1. Ga in de Azure Portal van uw Data Box Edge resource naar overzicht. Selecteer aan de **slag**in het rechterdeel venster op de tegel **Compute** .

    ![Aan de slag met Compute](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Selecteer op de tegel **Edge Compute** configureren de optie **reken kracht configureren**.
3. Voer de volgende gegevens in op de Blade **Edge Compute configureren** :

   
    |Veld  |Waarde  |
    |---------|---------|
    |IoT Hub     | Kies een **nieuwe** of **bestaande**. <br> Standaard wordt er een standaard-laag (S1) gebruikt voor het maken van een IoT-resource. Als u een IoT-resource in een gratis laag wilt gebruiken, maakt u er een en selecteert u vervolgens de bestaande resource. <br> In elk geval gebruikt de IoT Hub resource hetzelfde abonnement en dezelfde resource groep die wordt gebruikt door de Data Box Edge-resource.     |
    |Naam     |Voer een naam in voor de IoT Hub resource.         |

    ![Aan de slag met Compute](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Selecteer **Maken**. Het maken van de IoT Hub resource duurt enkele minuten. Nadat de IoT Hub bron is gemaakt, wordt de update tegel updates **configureren** om de compute-configuratie weer te geven. Als u wilt bevestigen dat de rol Edge Compute is geconfigureerd, selecteert u **Compute weer geven** op de tegel **Compute configureren** .
    
    ![Aan de slag met Compute](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Als het dialoog venster **Compute configureren** wordt gesloten voordat de IOT hub is gekoppeld aan het data Box edge apparaat, wordt de IOT hub gemaakt, maar wordt deze niet weer gegeven in de compute-configuratie. 
    
    Wanneer de Edge-rekenprocesrol wordt geconfigureerd op het Edge-apparaat, worden er twee apparaten aangemaakt: een IoT-apparaat en een IoT Edge-apparaat. Beide apparaten kunnen worden weergegeven in de IoT Hub-resource. Er wordt ook een IoT Edge-runtime op dit IoT Edge apparaat uitgevoerd. Op dit moment is alleen het Linux-platform beschikbaar voor uw IoT Edge-apparaat.


## <a name="add-shares"></a>Shares toevoegen

Voor de eenvoudige implementatie in deze zelf studie hebt u twee shares nodig: één rand share en een andere lokale share.

1. Voeg een rand share op het apparaat toe door de volgende stappen uit te voeren:

    1. Ga in uw Data Box Edge-resource naar **Edge compute >** aan de slag.
    2. Selecteer **toevoegen**op de tegel **share (s) toevoegen** .
    3. Geef op de Blade **share toevoegen** de share naam op en selecteer het share type.
    4. Als u de Edge-share wilt koppelen, schakelt u het selectie vakje in voor **het gebruik van de share met Edge Compute**.
    5. Selecteer het **opslag account**, de **opslag service**, een bestaande gebruiker en selecteer vervolgens **maken**.

        ![Een Edge-share toevoegen](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Als u een lokale NFS-share hebt gemaakt, gebruikt u de volgende optie van de opdracht rsync (op afstand synchroniseren) om bestanden naar de share te kopiëren:

    `rsync <source file path> < destination file path>`

    Zie de [Rsync-documentatie](https://www.computerhope.com/unix/rsync.htm) voor meer informatie over de opdracht rsync.

    De Edge-share wordt gemaakt en u ontvangt een geslaagde melding. De lijst shares wordt mogelijk bijgewerkt. U moet echter wachten tot het maken van de share is voltooid.

2. Voeg een Edge-lokale share op het edge-apparaat toe door alle stappen in de vorige stap te herhalen en het selectie vakje voor **configureren als Edge-lokale share**in te scha kelen. De gegevens op de lokale share blijven op het apparaat.

    ![Een lokale share toevoegen aan de rand](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Selecteer **share (s) toevoegen** om de bijgewerkte lijst met shares weer te geven.

    ![Bijgewerkte lijst met shares](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Een module toevoegen

U kunt een aangepaste of vooraf gemaakte module toevoegen. Er zijn geen aangepaste modules op dit edge-apparaat. Ga naar [Een C#-module ontwikkelen voor uw Data Box Edge-apparaat](data-box-edge-create-iot-edge-module.md) voor meer informatie over het maken van een aangepaste module.

In dit gedeelte voegt u een aangepaste module toe aan het IoT Edge-apparaat dat u hebt gemaakt in [Een C#-module ontwikkelen voor uw Data Box Edge](data-box-edge-create-iot-edge-module.md). Deze aangepaste module neemt bestanden van een lokale share op het apparaat aan de rand en verplaatst deze naar een Edge-share (Cloud) op het apparaat. De cloudshare pusht de bestanden vervolgens naar het Azure-opslagaccount dat is gekoppeld aan de cloudshare.

1. Ga naar **Edge compute > aan de slag**. Selecteer op de tegel **modules toevoegen** het scenario type zo **eenvoudig**. Selecteer **Toevoegen**.
2. Voer op de Blade **module configureren en toevoegen** de volgende waarden in:

    
    |Veld  |Waarde  |
    |---------|---------|
    |Naam     | Een unieke naam voor de module. Deze module is een docker-container die u kunt implementeren op het IoT Edge apparaat dat is gekoppeld aan uw Data Box Edge.        |
    |Afbeeldings-URI     | De afbeeldings-URI voor de bijbehorende container installatie kopie voor de module.        |
    |Referenties vereist     | Als u dit selectie vakje inschakelt, worden gebruikers naam en wacht woord gebruikt om modules op te halen met een overeenkomende URL.        |
    |Invoer share     | Selecteer een invoer share. De rand van de lokale share is in dit geval de invoer share. De module die hier wordt gebruikt, verplaatst bestanden van de lokale Edge-share naar een Edge-share waarnaar ze in de cloud worden geüpload.        |
    |Uitvoer share     | Selecteer een uitvoer share. De Edge-share is in dit geval de uitvoer share.        |
    |Trigger type     | Selecteer een **bestand** of **schema**. Een bestands trigger wordt geactiveerd wanneer een bestands gebeurtenis plaatsvindt, zoals een bestand wordt geschreven naar de invoer share. Een geplande trigger wordt geactiveerd op basis van een schema dat door u is gedefinieerd.         |
    |Naam van trigger     | Een unieke naam voor de trigger.         |
    |Omgevingsvariabelen| Optionele informatie die helpt bij het definiëren van de omgeving waarin uw module wordt uitgevoerd.   |

    ![Module toevoegen en configureren](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Selecteer **Toevoegen**. De module wordt toegevoegd. De tegel **module toevoegen** wordt bijgewerkt om aan te geven dat de module is geïmplementeerd. 

    ![Module geïmplementeerd](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Gegevenstransformatie controleren en gegevens overdragen

De laatste stap is controleren of de module is verbonden en wordt uitgevoerd zoals verwacht. De runtimestatus van de module moet 'Wordt uitgevoerd' zijn voor uw IoT Edge-apparaat in de IoT Hub-resource.

Doe het volgende om te controleren of de module wordt uitgevoerd:

1. Selecteer de tegel **module toevoegen** . Hiermee gaat u naar de Blade **modules** . Zoek in de lijst met modules de module die u hebt geïmplementeerd. De runtime status van de module die u hebt toegevoegd, moet worden *uitgevoerd*.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  Maak in Verkenner verbinding met zowel de rand lokale als de Edge-shares die u eerder hebt gemaakt.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Voeg gegevens toe aan de lokale share.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    De gegevens worden verplaatst naar de cloudshare.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    De gegevens worden vervolgens vanuit de cloudshare naar het opslagaccount gepusht. Ga naar de Storage Explorer om de gegevens te bekijken.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
U hebt het validatieproces voltooid.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Reken kracht configureren
> * Shares toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

Als u wilt weten hoe u uw Data Box Edge-apparaat beheert, gaat u naar:

> [!div class="nextstepaction"]
> [Lokale webgebruikersinterface gebruiken om Data Box Edge te beheren](data-box-edge-manage-access-power-connectivity-mode.md)
