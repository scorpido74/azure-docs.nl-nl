---
title: Zelfstudie voor het filteren, analyseren van gegevens met compute op Azure Data Box Edge | Microsoft Documenten
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239019"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Zelfstudie: Gegevens transformeren met Azure Data Box Edge

In deze zelfstudie wordt beschreven hoe u een rekenprocesrol configureert op uw Azure Data Box Edge-apparaat. Wanneer u de rekenrol hebt geconfigureerd, kan Data Box Edge gegevens transformeren voordat deze naar Azure worden verzonden.

Deze procedure kan ongeveer 10 tot 15 minuten in beslag nemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Compute configureren
> * Aandelen toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

 
## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarde wordt voldaan voordat u een rekenprocesrol configureert op uw Data Box Edge-apparaat:

- U hebt het Data Box Edge-apparaat geactiveerd zoals beschreven in [Azure Data Box Edge aansluiten, instellen en activeren](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Compute configureren

Als u compute wilt configureren op uw Data Box Edge, maakt u een IoT Hub-bron.

1. Ga in de Azure-portal van uw Data Box Edge-bron naar Overzicht. Selecteer aan de slag **in**het rechterdeelvenster op de tegel **Berekenen** .

    ![Aan de slag met compute](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. Selecteer **berekenen configureren**op de **berekentegel Rand configureren** .
3. Voer op het **rekenblad Rand configureren** het volgende in:

   
    |Veld  |Waarde  |
    |---------|---------|
    |IoT Hub     | Kies uit **Nieuw** of **Bestaand**. <br> Standaard wordt een Standaardlaag (S1) gebruikt om een IoT-bron te maken. Als u een gratis IoT-bron wilt gebruiken, maakt u er een en selecteert u de bestaande resource. <br> In elk geval gebruikt de IoT Hub-bron dezelfde abonnements- en resourcegroep die wordt gebruikt door de Data Box Edge-bron.     |
    |Name     |Voer een naam in voor uw IoT Hub-bron.         |

    ![Aan de slag met compute](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Selecteer **Maken**. Het maken van iot-hubbronnen duurt een paar minuten. Nadat de IoT Hub-bron is gemaakt, worden **de compute tile-updates configureren** om de compute-configuratie weer te geven. Als u wilt controleren of de bedienrol Edge is geconfigureerd, selecteert u **Compute weergeven** op de **betegel Compute configureren.**
    
    ![Aan de slag met compute](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    > [!NOTE]
    > Als het dialoogvenster **Compute configureren** wordt gesloten voordat de IoT-hub is gekoppeld aan het Data Box Edge-apparaat, wordt de IoT-hub gemaakt, maar wordt deze niet weergegeven in de compute-configuratie. 
    
    Wanneer de Edge-rekenprocesrol wordt geconfigureerd op het Edge-apparaat, worden er twee apparaten aangemaakt: een IoT-apparaat en een IoT Edge-apparaat. Beide apparaten kunnen worden weergegeven in de IoT Hub-resource. Een IoT Edge Runtime wordt ook uitgevoerd op dit IoT Edge-apparaat. Op dit moment is alleen het Linux-platform beschikbaar voor uw IoT Edge-apparaat.


## <a name="add-shares"></a>Aandelen toevoegen

Voor de eenvoudige implementatie in deze zelfstudie heb je twee shares nodig: een Edge-aandeel en een ander lokaal deel van Edge.

1. Voeg een Edge-aandeel toe aan het apparaat door de volgende stappen uit te voeren:

    1. Ga in uw Data Box Edge-bron naar **Edge compute > Aan de slag .**
    2. Selecteer Toevoegen op de tegel **Delen(en)** **toevoegen**.
    3. Geef op **het aandeelblad Toevoegen** de naam van het aandeel op en selecteer het sharetype.
    4. Als u het aandeel Rand wilt monteren, schakelt u het selectievakje Voor **Delen gebruiken met Edge compute in.**
    5. Selecteer het **opslagaccount**, **de opslagservice,** een bestaande gebruiker en selecteer **Vervolgens Maken**.

        ![Een Edge-aandeel toevoegen](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Als u een lokale NFS-share hebt gemaakt, gebruikt u de volgende optie van de opdracht rsync (op afstand synchroniseren) om bestanden naar de share te kopiëren:

    `rsync <source file path> < destination file path>`

    Zie de [Rsync-documentatie](https://www.computerhope.com/unix/rsync.htm) voor meer informatie over de opdracht rsync.

    Het Edge-aandeel wordt gemaakt en u ontvangt een melding voor een succesvolle creatie. De lijst shares wordt mogelijk bijgewerkt. U moet echter wachten tot het maken van de share is voltooid.

2. Voeg een lokaal edgeaandeel toe op het Edge-apparaat door alle stappen in de vorige stap te herhalen en het selectievakje **configureren als lokaal aandeel Edge in**te schakelen. De gegevens in het lokale aandeel blijven op het apparaat.

    ![Een lokaal deel van Edge toevoegen](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Selecteer **Aandelen(en) toevoegen** om de bijgewerkte lijst met aandelen te bekijken.

    ![Bijgewerkte lijst met shares](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Een module toevoegen

U een aangepaste of een vooraf gebouwde module toevoegen. Er zijn geen aangepaste modules op dit Edge-apparaat. Ga naar [Een C#-module ontwikkelen voor uw Data Box Edge-apparaat](data-box-edge-create-iot-edge-module.md) voor meer informatie over het maken van een aangepaste module.

In dit gedeelte voegt u een aangepaste module toe aan het IoT Edge-apparaat dat u hebt gemaakt in [Een C#-module ontwikkelen voor uw Data Box Edge](data-box-edge-create-iot-edge-module.md). Deze aangepaste module neemt bestanden van een lokaal edge-aandeel op het Edge-apparaat en verplaatst ze naar een edge -aandeel (cloud) op het apparaat. De cloudshare pusht de bestanden vervolgens naar het Azure-opslagaccount dat is gekoppeld aan de cloudshare.

1. Ga naar **Edge compute > Aan de slag**. Selecteer op de tegel **Modules toevoegen** het scenariotype **als eenvoudig**. Selecteer **Toevoegen**.
2. Voer in het blad Van de **module configureren en toevoegen** de volgende waarden in:

    
    |Veld  |Waarde  |
    |---------|---------|
    |Name     | Een unieke naam voor de module. Deze module is een dockercontainer die u implementeren op het IoT Edge-apparaat dat is gekoppeld aan uw Data Box Edge.        |
    |Afbeelding URI     | De afbeelding URI voor de bijbehorende containerafbeelding voor de module.        |
    |Referenties vereist     | Indien aangevinkt, worden gebruikersnaam en wachtwoord gebruikt om modules met een overeenkomende URL op te halen.        |
    |Invoeraandeel     | Selecteer een invoeraandeel. Het lokale aandeel Edge is in dit geval het invoeraandeel. De module die hier wordt gebruikt, verplaatst bestanden van het lokale edge-aandeel naar een Edge-share waar ze worden geüpload naar de cloud.        |
    |Uitvoeraandeel     | Selecteer een uitvoeraandeel. Het aandeel Edge is in dit geval het uitvoeraandeel.        |
    |Type trigger     | Selecteer uit **Bestand** of **Planning**. Een bestandstrigger wordt geactiveerd wanneer een bestandsgebeurtenis optreedt, zoals een bestand wordt geschreven naar de invoershare. Een geplande trigger wordt geactiveerd op basis van een schema dat door u is gedefinieerd.         |
    |Triggernaam     | Een unieke naam voor uw trekker.         |
    |Omgevingsvariabelen| Optionele informatie die helpt bij het definiëren van de omgeving waarin uw module wordt uitgevoerd.   |

    ![Module toevoegen en configureren](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Selecteer **Toevoegen**. De module wordt toegevoegd. De **moduletegelupdates toevoegen** om aan te geven dat de module is geïmplementeerd. 

    ![Module geïmplementeerd](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Gegevenstransformatie controleren en gegevens overdragen

De laatste stap is controleren of de module is verbonden en wordt uitgevoerd zoals verwacht. De runtimestatus van de module moet 'Wordt uitgevoerd' zijn voor uw IoT Edge-apparaat in de IoT Hub-resource.

Doe het volgende om te controleren of de module wordt uitgevoerd:

1. Selecteer de **tegel Module toevoegen.** Dit brengt u naar de **Modules** blade. Identificeer in de lijst met modules de module die u hebt geïmplementeerd. De runtime-status van de module die u hebt toegevoegd, moet *worden uitgevoerd*.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  Maak in Verkenner verbinding met zowel de lokale Edge- als edge-shares die u eerder hebt gemaakt.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Voeg gegevens toe aan de lokale share.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    De gegevens worden verplaatst naar de cloudshare.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    De gegevens worden vervolgens vanuit de cloudshare naar het opslagaccount gepusht. Ga naar de Storage Explorer om de gegevens te bekijken.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
U hebt het validatieproces voltooid.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Compute configureren
> * Aandelen toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

Als u wilt weten hoe u uw Data Box Edge-apparaat beheert, gaat u naar:

> [!div class="nextstepaction"]
> [Lokale webgebruikersinterface gebruiken om Data Box Edge te beheren](data-box-edge-manage-access-power-connectivity-mode.md)
