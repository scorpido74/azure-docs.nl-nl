---
title: Zelf studie voor het filteren en analyseren van gegevens voor geavanceerde implementatie met Compute op Azure Data Box Edge | Microsoft Docs
description: Meer informatie over het configureren van een compute-functie op Data Box Edge en het gebruik ervan om gegevens te transformeren voor een geavanceerde implementatie stroom voordat ze naar Azure worden verzonden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78384731"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Zelf studie: gegevens transformeren met Azure Data Box Edge voor geavanceerde implementatie stroom

In deze zelf studie wordt beschreven hoe u een compute-functie kunt configureren voor een geavanceerde implementatie stroom op uw Azure Data Box Edge-apparaat. Wanneer u de rekenrol hebt geconfigureerd, kan Data Box Edge gegevens transformeren voordat deze naar Azure worden verzonden.

Compute kan worden geconfigureerd voor eenvoudige of geavanceerde implementatie stroom op het apparaat.

|                  | Eenvoudige implementatie                                | Geavanceerde implementatie                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Bedoeld voor     | IT-beheerders                                | Ontwikkelaars                            |
| Type             | Data Box Edge-service gebruiken voor het implementeren van modules      | IoT Hub-service gebruiken voor het implementeren van modules |
| Geïmplementeerde modules | Enkelvoudig                                           | Gekoppelde of meerdere modules           |


Deze procedure kan ongeveer 20 tot 30 minuten duren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Reken kracht configureren
> * Shares toevoegen
> * Een trigger toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

 
## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarde wordt voldaan voordat u een rekenprocesrol configureert op uw Data Box Edge-apparaat:

- U hebt het Data Box Edge-apparaat geactiveerd zoals beschreven in [Azure Data Box Edge aansluiten, instellen en activeren](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Reken kracht configureren

Als u de reken kracht op uw Data Box Edge wilt configureren, maakt u een IoT Hub resource.

1. Ga in de Azure Portal van uw Data Box Edge resource naar **overzicht**. Selecteer aan de **slag**in het rechterdeel venster op de tegel **Compute** .

    ![Aan de slag met Compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Selecteer op de tegel **Edge Compute** configureren de optie **reken kracht configureren**.

    ![Aan de slag met Compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Voer de volgende gegevens in op de Blade **Edge Compute configureren** :

   
    |Veld  |Waarde  |
    |---------|---------|
    |IoT Hub     | Kies een **nieuwe** of **bestaande**. <br> Standaard wordt er een standaard-laag (S1) gebruikt voor het maken van een IoT-resource. Als u een IoT-resource in een gratis laag wilt gebruiken, maakt u er een en selecteert u vervolgens de bestaande resource. <br> In elk geval gebruikt de IoT Hub resource hetzelfde abonnement en dezelfde resource groep die wordt gebruikt door de Data Box Edge-resource.     |
    |Naam     |Voer een naam in voor de IoT Hub resource.         |

    ![Aan de slag met Compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Selecteer **Maken**. Het maken van de IoT Hub resource duurt enkele minuten. Nadat de IoT Hub resource is gemaakt, wordt de tegel updates van **Edge Compute configureren** weer gegeven voor de compute-configuratie. Als u wilt bevestigen dat de rol Edge Compute is geconfigureerd, selecteert u **configuratie weer geven** op de tegel **Compute configureren** .
    
    ![Aan de slag met Compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Wanneer de Edge-rekenprocesrol wordt geconfigureerd op het Edge-apparaat, worden er twee apparaten aangemaakt: een IoT-apparaat en een IoT Edge-apparaat. Beide apparaten kunnen worden weergegeven in de IoT Hub-resource. Er wordt ook een IoT Edge-runtime op dit IoT Edge apparaat uitgevoerd.

    Op dit moment is alleen het Linux-platform beschikbaar voor uw IoT Edge-apparaat.


## <a name="add-shares"></a>Shares toevoegen

Voor de geavanceerde implementatie in deze zelf studie hebt u twee shares nodig: één rand share en een andere lokale share.

1. Voeg een rand share op het apparaat toe door de volgende stappen uit te voeren:

    1. Ga in uw Data Box Edge-resource naar **Edge compute >** aan de slag.
    2. Selecteer **toevoegen**op de tegel **share (s) toevoegen** .
    3. Geef op de Blade **share toevoegen** de share naam op en selecteer het share type.
    4. Als u de Edge-share wilt koppelen, schakelt u het selectie vakje in voor **het gebruik van de share met Edge Compute**.
    5. Selecteer het **opslag account**, de **opslag service**, een bestaande gebruiker en selecteer vervolgens **maken**.

        ![Een Edge-share toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Nadat de Edge-share is gemaakt, ontvangt u een melding over een geslaagde aanmaak. De lijst met shares wordt bijgewerkt op basis van de nieuwe share.

2. Voeg een Edge-lokale share op het edge-apparaat toe door alle stappen in de vorige stap te herhalen en het selectie vakje voor **configureren als Edge-lokale share**in te scha kelen. De gegevens op de lokale share blijven op het apparaat.

    ![Een lokale share toevoegen aan de rand](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Op de Blade **shares** ziet u de bijgewerkte lijst met shares.

    ![Bijgewerkte lijst met shares](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Als u de eigenschappen van de zojuist gemaakte lokale share wilt weer geven, selecteert u de share in de lijst. Kopieer de waarde voor de share uit het vak **Lokaal koppelpunt voor Edge-rekenprocesmodules**.

    U gebruik dit lokale koppelpunt bij het implementeren van de module.

    ![Het vak Lokaal koppelpunt voor Edge-rekenprocesmodules](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Als u de eigenschappen wilt weer geven van de Edge-share die u hebt gemaakt, selecteert u de share in de lijst. Kopieer de waarde voor de share uit het vak **Lokaal koppelpunt voor Edge-rekenprocesmodules**.

    U gebruik dit lokale koppelpunt bij het implementeren van de module.

    ![Aangepaste module toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Een trigger toevoegen

1. Ga naar **Edge compute > triggers**. Selecteer **+ trigger toevoegen**.

    ![Trigger toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Voer de volgende waarden in op de Blade **trigger toevoegen** .

    |Veld  |Waarde  |
    |---------|---------|
    |Naam van trigger     | Een unieke naam voor de trigger.         |
    |Trigger type     | Selecteer **Bestands** trigger. Een bestands trigger wordt geactiveerd wanneer een bestands gebeurtenis plaatsvindt, zoals een bestand wordt geschreven naar de invoer share. Een geplande trigger voor de andere kant, wordt geactiveerd op basis van een schema dat door u is gedefinieerd. Voor dit voor beeld hebben we een bestands trigger nodig.    |
    |Invoer share     | Selecteer een invoer share. De rand van de lokale share is in dit geval de invoer share. De module die hier wordt gebruikt, verplaatst bestanden van de lokale Edge-share naar een Edge-share waarnaar ze in de cloud worden geüpload.        |

    ![Trigger toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. U wordt gewaarschuwd nadat de trigger is gemaakt. De lijst met triggers wordt bijgewerkt om de zojuist gemaakte trigger weer te geven. Selecteer de trigger die u zojuist hebt gemaakt.

    ![Trigger toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Kopieer de voorbeeld route en sla deze op. U gaat deze voorbeeld route wijzigen en later in de IoT Hub gebruiken.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Trigger toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Een module toevoegen

Er zijn geen aangepaste modules op dit edge-apparaat. U kunt een aangepaste of vooraf gemaakte module toevoegen. Ga naar [Een C#-module ontwikkelen voor uw Data Box Edge-apparaat](data-box-edge-create-iot-edge-module.md) voor meer informatie over het maken van een aangepaste module.

In dit gedeelte voegt u een aangepaste module toe aan het IoT Edge-apparaat dat u hebt gemaakt in [Een C#-module ontwikkelen voor uw Data Box Edge](data-box-edge-create-iot-edge-module.md). Deze aangepaste module neemt bestanden van een lokale share op het apparaat aan de rand en verplaatst deze naar een Edge-share (Cloud) op het apparaat. De cloudshare pusht de bestanden vervolgens naar het Azure-opslagaccount dat is gekoppeld aan de cloudshare.

1. Ga naar **Edge compute > aan de slag**. Selecteer op de tegel **modules toevoegen** het scenario type als **Geavanceerd**. Selecteer **Ga naar IOT hub**.

    ![Geavanceerde implementatie selecteren](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. Ga in uw IoT Hub-resource naar **IOT edge apparaat** en selecteer vervolgens uw IOT edge apparaat.

    ![Ga naar IoT Edge apparaat in IoT Hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Selecteer **modules instellen**op de details van het **apparaat**.

    ![De koppeling Modules instellen](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. Doe het volgende in **Modules toevoegen**:

    1. Geef in de Containerregisterinstellingen waarden op voor Naam, Adres, Gebruikersnaam en Wachtwoord voor de aangepaste module.
    De naam, het adres en de vermelde referenties worden gebruikt om modules met een overeenkomende URL op te halen. Als u deze module wilt implementeren, selecteert u **IoT Edge-module** onder **Implementatiemodules**. Deze IoT Edge-module is een Docker-container die u kunt implementeren op het IoT Edge-apparaat dat is gekoppeld aan uw Data Box Edge-apparaat.

        ![De pagina Modules instellen](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Geef de instellingen voor de aangepaste IoT Edge-module op. Voer de volgende waarden in.
     
        |Veld  |Waarde  |
        |---------|---------|
        |Naam     | Een unieke naam voor de module. Deze module is een docker-container die u kunt implementeren op het IoT Edge apparaat dat is gekoppeld aan uw Data Box Edge.        |
        |Afbeeldings-URI     | De afbeeldings-URI voor de bijbehorende container installatie kopie voor de module.        |
        |Referenties vereist     | Als u dit selectie vakje inschakelt, worden gebruikers naam en wacht woord gebruikt om modules op te halen met een overeenkomende URL.        |
    
        Voer in het vak **Opties voor container maken** de lokale koppel punten in voor de Edge-modules die u hebt gekopieerd in de voor gaande stappen voor de Edge-share en Edge-lokale share.

        > [!IMPORTANT]
        > De hier gebruikte paden worden gekoppeld in uw container, zodat ze moeten overeenkomen met wat de functionaliteit in uw container verwacht. Als u [een aangepaste module maakt](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code), worden de gekopieerde paden verwacht door de code die in die module is opgegeven. Wijzig deze paden niet.
    
        In het vak **Opties Container maken** plakt u het volgende voorbeeld:
    
        ```
        {
          "HostConfig": 
          {
           "Binds": 
            [
             "/home/hcsshares/mydbesmbedgelocalshare1:/home/input",
             "/home/hcsshares/mydbesmbedgeshare1:/home/output"
            ]
           }
        }
        ```

        Geef alle omgevings variabelen op die worden gebruikt voor uw module. Omgevings variabelen bieden optionele informatie die u helpt bij het definiëren van de omgeving waarin uw module wordt uitgevoerd.

        ![Het vak Opties Container maken](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Configureer indien nodig de geavanceerde runtime-instellingen voor Edge en klik dan op **Volgende**.

        ![Aangepaste module toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  Onder **Routes opgeven** kunt u routes tussen modules instellen.  
    
    ![De routes opgeven](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    U kunt de *route* vervangen door de volgende route teken reeks die u eerder hebt gekopieerd. In dit voorbeeld geeft u de naam op van de lokale share die gegevens naar de cloudshare pusht. Vervang de `modulename` door de naam van de module. Selecteer **Next**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Het gedeelte Routes opgeven](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  Controleer alle instellingen onder **Implementatie controleren**. Klik vervolgens op **Verzenden** om de module te verzenden voor implementatie.

    ![De pagina Modules instellen](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Met deze actie wordt de implementatie van de module gestart. Nadat de implementatie is voltooid, wordt de **runtime status** van de module **uitgevoerd**.

    ![Aangepaste module toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Gegevens transformatie, overdracht controleren

De laatste stap is controleren of de module is verbonden en wordt uitgevoerd zoals verwacht. De runtimestatus van de module moet 'Wordt uitgevoerd' zijn voor uw IoT Edge-apparaat in de IoT Hub-resource.

Voer de volgende stappen uit om de gegevens transformatie te controleren en over te dragen naar Azure.
 
1.  Maak in Verkenner verbinding met zowel de rand lokale als de Edge-shares die u eerder hebt gemaakt.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Voeg gegevens toe aan de lokale share.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    De gegevens worden verplaatst naar de cloudshare.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    De gegevens worden vervolgens vanuit de cloudshare naar het opslagaccount gepusht. Als u de gegevens wilt bekijken, gaat u naar uw opslag account en selecteert u **Storage Explorer**. U kunt de geüploade gegevens weer geven in uw opslag account.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
U hebt het validatieproces voltooid.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Reken kracht configureren
> * Shares toevoegen
> * Een trigger toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

Als u wilt weten hoe u uw Data Box Edge-apparaat beheert, gaat u naar:

> [!div class="nextstepaction"]
> [Lokale webgebruikersinterface gebruiken om Data Box Edge te beheren](data-box-edge-manage-access-power-connectivity-mode.md)
