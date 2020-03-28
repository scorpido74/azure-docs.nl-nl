---
title: Zelfstudie voor het filteren, analyseren van gegevens voor geavanceerde implementatie met compute op Azure Data Box Edge | Microsoft Documenten
description: Lees hoe u de rekenrol configureert op Data Box Edge en deze gebruikt om gegevens te transformeren voor geavanceerde implementatiestromen voordat u naar Azure verzendt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge for advanced deployment flow so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: b446a3ebf92f6240d3bc02a148fbb8296efec926
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239040"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge-for-advanced-deployment-flow"></a>Zelfstudie: Gegevens transformeren met Azure Data Box Edge voor geavanceerde implementatiestroom

In deze zelfstudie wordt beschreven hoe u een rekenrol configureert voor een geavanceerde implementatiestroom op uw Azure Data Box Edge-apparaat. Wanneer u de rekenrol hebt geconfigureerd, kan Data Box Edge gegevens transformeren voordat deze naar Azure worden verzonden.

Compute kan worden geconfigureerd voor eenvoudige of geavanceerde implementatiestroom op uw apparaat.

|                  | Eenvoudige implementatie                                | Geavanceerde implementatie                   |
|------------------|--------------------------------------------------|---------------------------------------|
| Bestemd voor     | IT-beheerders                                | Ontwikkelaars                            |
| Type             | Data Box Edge-service gebruiken om modules te implementeren      | IoT Hub-service gebruiken om modules te implementeren |
| Modules geïmplementeerd | Enkel                                           | Geketende of meerdere modules           |


Deze procedure kan ongeveer 20 tot 30 minuten in beslag nemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Compute configureren
> * Aandelen toevoegen
> * Een trigger toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

 
## <a name="prerequisites"></a>Vereisten

Zorg dat aan deze voorwaarde wordt voldaan voordat u een rekenprocesrol configureert op uw Data Box Edge-apparaat:

- U hebt het Data Box Edge-apparaat geactiveerd zoals beschreven in [Azure Data Box Edge aansluiten, instellen en activeren](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Compute configureren

Als u compute wilt configureren op uw Data Box Edge, maakt u een IoT Hub-bron.

1. Ga in de Azure-portal van uw Data Box Edge-bron naar **Overzicht**. Selecteer aan de slag **in**het rechterdeelvenster op de tegel **Berekenen** .

    ![Aan de slag met compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-1.png)

2. Selecteer **berekenen configureren**op de **berekentegel Rand configureren** .

    ![Aan de slag met compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-2.png)

3. Voer op het **rekenblad Rand configureren** het volgende in:

   
    |Veld  |Waarde  |
    |---------|---------|
    |IoT Hub     | Kies uit **Nieuw** of **Bestaand**. <br> Standaard wordt een Standaardlaag (S1) gebruikt om een IoT-bron te maken. Als u een gratis IoT-bron wilt gebruiken, maakt u er een en selecteert u de bestaande resource. <br> In elk geval gebruikt de IoT Hub-bron dezelfde abonnements- en resourcegroep die wordt gebruikt door de Data Box Edge-bron.     |
    |Name     |Voer een naam in voor uw IoT Hub-bron.         |

    ![Aan de slag met compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-3.png)

4. Selecteer **Maken**. Het maken van iot-hubbronnen duurt een paar minuten. Nadat de IoT Hub-bron is gemaakt, worden de compute tile updates **configureren** om de compute-configuratie weer te geven. Als u wilt controleren of de bedienrol Edge is geconfigureerd, selecteert u **Config weergeven** op de **betegel Compute configureren.**
    
    ![Aan de slag met compute](./media/data-box-edge-deploy-configure-compute-advanced/configure-compute-4.png)

    Wanneer de Edge-rekenprocesrol wordt geconfigureerd op het Edge-apparaat, worden er twee apparaten aangemaakt: een IoT-apparaat en een IoT Edge-apparaat. Beide apparaten kunnen worden weergegeven in de IoT Hub-resource. Een IoT Edge Runtime wordt ook uitgevoerd op dit IoT Edge-apparaat.

    Op dit moment is alleen het Linux-platform beschikbaar voor uw IoT Edge-apparaat.


## <a name="add-shares"></a>Aandelen toevoegen

Voor de geavanceerde implementatie in deze zelfstudie hebt u twee shares nodig: een Edge-aandeel en een ander lokaal deel van Edge.

1. Voeg een Edge-aandeel toe aan het apparaat door de volgende stappen uit te voeren:

    1. Ga in uw Data Box Edge-bron naar **Edge compute > Aan de slag .**
    2. Selecteer Toevoegen op de tegel **Delen(en)** **toevoegen**.
    3. Geef op **het aandeelblad Toevoegen** de naam van het aandeel op en selecteer het sharetype.
    4. Als u het aandeel Rand wilt monteren, schakelt u het selectievakje Voor **Delen gebruiken met Edge compute in.**
    5. Selecteer het **opslagaccount**, **de opslagservice,** een bestaande gebruiker en selecteer **Vervolgens Maken**.

        ![Een Edge-aandeel toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-1.png)

    <!--If you created a local NFS share, use the following remote sync (rsync) command option to copy files onto the share:

    `rsync <source file path> < destination file path>`

    For more information about the rsync command, go to [Rsync documentation](https://www.computerhope.com/unix/rsync.htm).-->

    Nadat het Edge-aandeel is gemaakt, ontvangt u een melding voor het maken van een geslaagde creatie. De aandelenlijst wordt bijgewerkt om het nieuwe aandeel weer te geven.

2. Voeg een lokaal edgeaandeel toe op het Edge-apparaat door alle stappen in de vorige stap te herhalen en het selectievakje **configureren als lokaal aandeel Edge in**te schakelen. De gegevens in het lokale aandeel blijven op het apparaat.

    ![Een lokaal deel van Edge toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-2.png)

3. Op het **aandelenblad** ziet u de bijgewerkte lijst met aandelen.

    ![Bijgewerkte lijst met shares](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-3.png)

4. Als u de eigenschappen van het nieuw gecreëerde lokale aandeel wilt weergeven, selecteert u het aandeel in de lijst. Kopieer de waarde voor de share uit het vak **Lokaal koppelpunt voor Edge-rekenprocesmodules**.

    U gebruik dit lokale koppelpunt bij het implementeren van de module.

    ![Het vak Lokaal koppelpunt voor Edge-rekenprocesmodules](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-4.png)
 
5. Als u de eigenschappen van het edge-aandeel wilt weergeven dat u hebt gemaakt, selecteert u het aandeel in de lijst. Kopieer de waarde voor de share uit het vak **Lokaal koppelpunt voor Edge-rekenprocesmodules**.

    U gebruik dit lokale koppelpunt bij het implementeren van de module.

    ![Aangepaste module toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-edge-share-5.png)


## <a name="add-a-trigger"></a>Een trigger toevoegen

1. Ga naar **Edge compute > Triggers**. Selecteer **+ Trigger toevoegen**.

    ![Trigger toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-1.png)

2. Voer in het **triggerblad Toevoegen** de volgende waarden in.

    |Veld  |Waarde  |
    |---------|---------|
    |Triggernaam     | Een unieke naam voor uw trekker.         |
    |Type trigger     | Selecteer De trigger **van bestand.** Een bestandstrigger wordt geactiveerd wanneer een bestandsgebeurtenis optreedt, zoals een bestand wordt geschreven naar de invoershare. Een geplande trigger aan de andere kant, branden op basis van een schema dat door u is gedefinieerd. Voor dit voorbeeld hebben we een bestandstrigger nodig.    |
    |Invoeraandeel     | Selecteer een invoeraandeel. Het lokale aandeel Edge is in dit geval het invoeraandeel. De module die hier wordt gebruikt, verplaatst bestanden van het lokale edge-aandeel naar een Edge-share waar ze worden geüpload naar de cloud.        |

    ![Trigger toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-2.png)

3. U wordt hiervan op de hoogte gesteld nadat de trigger is gemaakt. De lijst met triggers wordt bijgewerkt om de nieuw gemaakte trigger weer te geven. Selecteer de trigger die u zojuist hebt gemaakt.

    ![Trigger toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-3.png)

4. Kopieer en sla de voorbeeldroute op. U wijzigt deze voorbeeldroute en gebruikt deze later in de IoT-hub.

    `"sampleroute": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/modulename/inputs/input1\")"`

    ![Trigger toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-trigger-4.png)

## <a name="add-a-module"></a>Een module toevoegen

Er zijn geen aangepaste modules op dit Edge-apparaat. U een aangepaste of een vooraf gebouwde module toevoegen. Ga naar [Een C#-module ontwikkelen voor uw Data Box Edge-apparaat](data-box-edge-create-iot-edge-module.md) voor meer informatie over het maken van een aangepaste module.

In dit gedeelte voegt u een aangepaste module toe aan het IoT Edge-apparaat dat u hebt gemaakt in [Een C#-module ontwikkelen voor uw Data Box Edge](data-box-edge-create-iot-edge-module.md). Deze aangepaste module neemt bestanden van een lokaal edge-aandeel op het Edge-apparaat en verplaatst ze naar een edge -aandeel (cloud) op het apparaat. De cloudshare pusht de bestanden vervolgens naar het Azure-opslagaccount dat is gekoppeld aan de cloudshare.

1. Ga naar **Edge compute > Aan de slag**. Selecteer op de tegel **Modules toevoegen** het scenariotype als **geavanceerd**. Selecteer **Ga naar IoT-hub**.

    ![Geavanceerde implementatie selecteren](./media/data-box-edge-deploy-configure-compute-advanced/add-module-1.png)

<!--2. In the **Configure and add module** blade, input the following values:  

    |Input share     | Select an input share. The Edge local share is the input share in this case. The module used here moves files from the Edge local share to an Edge share where they are uploaded into the cloud.        |
    |Output share     | Select an output share. The Edge share is the output share in this case.        |
-->

2. Ga in uw IoT Hub-bron naar **het IoT Edge-apparaat** en selecteer vervolgens uw IoT Edge-apparaat.

    ![Naar IoT Edge-apparaat gaan in IoT-hub](./media/data-box-edge-deploy-configure-compute-advanced/add-module-2.png)

3. Selecteer **Modules instellen** **op apparaatgegevens**.

    ![De koppeling Modules instellen](./media/data-box-edge-deploy-configure-compute-advanced/add-module-3.png)

4. Doe het volgende in **Modules toevoegen**:

    1. Geef in de Containerregisterinstellingen waarden op voor Naam, Adres, Gebruikersnaam en Wachtwoord voor de aangepaste module.
    De naam, het adres en de vermelde referenties worden gebruikt om modules met een overeenkomende URL op te halen. Als u deze module wilt implementeren, selecteert u **IoT Edge-module** onder **Implementatiemodules**. Deze IoT Edge-module is een Docker-container die u kunt implementeren op het IoT Edge-apparaat dat is gekoppeld aan uw Data Box Edge-apparaat.

        ![De pagina Modules instellen](./media/data-box-edge-deploy-configure-compute-advanced/add-module-4.png) 
 
    2. Geef de instellingen voor de aangepaste IoT Edge-module op. Voer de volgende waarden in.
     
        |Veld  |Waarde  |
        |---------|---------|
        |Name     | Een unieke naam voor de module. Deze module is een dockercontainer die u implementeren op het IoT Edge-apparaat dat is gekoppeld aan uw Data Box Edge.        |
        |Afbeelding URI     | De afbeelding URI voor de bijbehorende containerafbeelding voor de module.        |
        |Referenties vereist     | Indien aangevinkt, worden gebruikersnaam en wachtwoord gebruikt om modules met een overeenkomende URL op te halen.        |
    
        Voer in het vak **Opties voor containermaken** de lokale bevestigingspunten in voor de Edge-modules die u in de voorgaande stappen hebt gekopieerd voor het aandeel Edge en het lokale aandeel Edge.

        > [!IMPORTANT]
        > De paden die hier worden gebruikt, zijn in uw container gemonteerd, zodat ze moeten overeenkomen met wat de functionaliteit in uw container verwacht. Als u [Een aangepaste module](data-box-edge-create-iot-edge-module.md#update-the-module-with-custom-code)maken volgt, verwacht de code die in die module is opgegeven de gekopieerde paden. Wijzig deze paden niet.
    
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

        Zorg voor omgevingsvariabelen die voor uw module worden gebruikt. Omgevingsvariabelen bieden optionele informatie die helpt bij het definiëren van de omgeving waarin uw module wordt uitgevoerd.

        ![Het vak Opties Container maken](./media/data-box-edge-deploy-configure-compute-advanced/add-module-5.png) 
 
    4. Configureer indien nodig de geavanceerde runtime-instellingen voor Edge en klik dan op **Volgende**.

        ![Aangepaste module toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-module-6.png)
 
5.  Stel **onder Routes opgeven**routes in tussen modules.  
    
    ![De routes opgeven](./media/data-box-edge-deploy-configure-compute-advanced/add-module-7.png)

    U *route* vervangen door de volgende routetekenreeks die u eerder hebt gekopieerd. In dit voorbeeld geeft u de naam op van de lokale share die gegevens naar de cloudshare pusht. Vervang `modulename` de module door de naam van de module. Selecteer **Volgende**.
        
    ```
    "route": "FROM /* WHERE topic = 'mydbesmbedgelocalshare1' INTO BrokeredEndpoint(\"/modules/filemove/inputs/input1\")"
    ```

    ![Het gedeelte Routes opgeven](./media/data-box-edge-deploy-configure-compute-advanced/add-module-8.png)

6.  Controleer alle instellingen onder **Implementatie controleren**. Klik vervolgens op **Verzenden** om de module te verzenden voor implementatie.

    ![De pagina Modules instellen](./media/data-box-edge-deploy-configure-compute-advanced/add-module-9.png)
 
    Met deze actie wordt de implementatie van de module gestart. Nadat de implementatie is voltooid, wordt de **runtime-status** van de module **uitgevoerd.**

    ![Aangepaste module toevoegen](./media/data-box-edge-deploy-configure-compute-advanced/add-module-10.png)

## <a name="verify-data-transform-transfer"></a>Gegevenstransformatie verifiëren, overdragen

De laatste stap is controleren of de module is verbonden en wordt uitgevoerd zoals verwacht. De runtimestatus van de module moet 'Wordt uitgevoerd' zijn voor uw IoT Edge-apparaat in de IoT Hub-resource.

Volg de volgende stappen om gegevenstransformatie en overdracht naar Azure te verifiëren.
 
1.  Maak in Verkenner verbinding met zowel de lokale Edge- als edge-shares die u eerder hebt gemaakt.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-2.png)
 
1.  Voeg gegevens toe aan de lokale share.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-3.png)
 
    De gegevens worden verplaatst naar de cloudshare.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-4.png)  

    De gegevens worden vervolgens vanuit de cloudshare naar het opslagaccount gepusht. Als u de gegevens wilt bekijken, gaat u naar uw opslagaccount en selecteert u **Storage Explorer**. U de geüploade gegevens in uw opslagaccount bekijken.

    ![Gegevenstransformatie controleren](./media/data-box-edge-deploy-configure-compute-advanced/verify-data-5.png)
 
U hebt het validatieproces voltooid.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Compute configureren
> * Aandelen toevoegen
> * Een trigger toevoegen
> * Een rekenprocesmodule toevoegen
> * Gegevenstransformatie controleren en gegevens overdragen

Als u wilt weten hoe u uw Data Box Edge-apparaat beheert, gaat u naar:

> [!div class="nextstepaction"]
> [Lokale webgebruikersinterface gebruiken om Data Box Edge te beheren](data-box-edge-manage-access-power-connectivity-mode.md)
