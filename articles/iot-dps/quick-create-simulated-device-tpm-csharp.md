---
title: 'Quick Start: een gesimuleerd TPM-apparaat inrichten voor Azure IoT Hub met behulp vanC#'
description: 'Quick Start: een gesimuleerd TPM-apparaat maken C# en inrichten met behulp van de apparaat-SDK voor Azure IOT hub Device Provisioning Service. In deze snelstart wordt gebruikgemaakt van afzonderlijke registraties.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 51249a8ac2f0fd0ed00d63ab60a2ab88b56e4304
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276451"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Snelstartgids: een gesimuleerd TPM-apparaat maken en C# inrichten met behulp van de apparaat-SDK voor IOT hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Deze stappen laten u zien hoe u de [Azure IoT-voorbeelden voor C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp) gebruikt om een TPM-apparaat te simuleren op een ontwikkelcomputer met het Windows-besturingssysteem. In het voorbeeld wordt het gesimuleerde apparaat ook verbonden met een IoT Hub met behulp van een apparaatinrichtingsservice. 

De voorbeeldcode gebruikt de Windows TPM-simulator als de [HSM (Hardware Security Module)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) van het apparaat. 

Als u niet bekend bent met het proces van automatisch inrichten, bekijk dan ook de [Concepten voor automatische inrichting](concepts-auto-provisioning.md). Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat. 

Azure IoT Device Provisioning Service ondersteunt twee typen registraties:

- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om een enkel apparaat in te schrijven.

In dit artikel worden afzonderlijke registraties gedemonstreerd.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden 

1. Zorg ervoor dat u de [.net Core 2,1 SDK of hoger](https://www.microsoft.com/net/download/windows) op uw computer hebt geïnstalleerd. 

1. Zorg ervoor dat `git` op de computer wordt geïnstalleerd en toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

1. Open een opdrachtprompt of Git Bash. Kloon de Azure IoT-voorbeelden GitHub-opslagplaats voor C#:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>Het gesimuleerde apparaat inrichten

1. Meld u aan bij Azure Portal. Selecteer de knop **alle resources** in het menu links en open de Device Provisioning-Service. Noteer de waarde van het **_id-bereik_** op de Blade **overzicht** .

    ![Bereik-id van provisioning-service kopiëren van portal-blade](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

1. Ga op een opdrachtprompt naar de projectmap met de voorbeeldcode voor het inrichten van het de TPM-apparaat.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

1. Typ de volgende opdracht om de voorbeeldcode voor het TPM-apparaat te bouwen en uit te voeren. Vervang de waarde voor `<IDScope>` door het id-bereik voor uw provisioning-service. 

    ```cmd
    dotnet run <IDScope>
    ```

    Met deze opdracht wordt de TPM-chip-simulator in een aparte opdrachtprompt gestart. In Windows kunt u een beveiligings waarschuwing van Windows tegen komen waarin u wordt gevraagd of u Simulator. exe wilt toestaan om te communiceren op open bare netwerken. Voor de doel einden van dit voor beeld kunt u de aanvraag annuleren.

1. In het oorspronkelijke opdracht venster ziet u de **_goedkeurings sleutel_** , de **_registratie-id_** en een voorgestelde **_apparaat-id_** die nodig is voor de registratie van het apparaat. Noteer deze waarden. U gebruikt deze waarde om een afzonderlijke inschrijving te maken in uw Device Provisioning Service-instantie. 
   > [!NOTE]
   > Verwar het venster met opdrachtuitvoer niet met het venster dat uitvoer van de TPM-simulator bevat. Mogelijk moet u het oorspronkelijke opdracht venster selecteren om het naar de voor grond te brengen.

    ![Uitvoer van het opdrachtvenster](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

1. In de Azure Portal, in het menu Device Provisioning Service, selecteert u **inschrijvingen beheren**. Selecteer het tabblad **afzonderlijke inschrijvingen** en selecteer bovenaan de knop **afzonderlijke registratie toevoegen** . 

1. Voer in het deel venster **registratie toevoegen** de volgende gegevens in:
   - Selecteer **TPM** als *mechanisme* voor identiteitscontrole.
   - Voer de *registratie-id* en *goedkeurings sleutel* voor het TPM-apparaat in van de waarden die u eerder hebt genoteerd.
   - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
   - Desgewenst kunt u de volgende informatie verstrekken:
       - Voer een unieke *apparaat-id* in (u kunt het voorgestelde certificaat gebruiken of een eigen naam geven). Vermijd gevoelige gegevens bij het benoemen van uw apparaat. Als u ervoor kiest om er geen op te geven, wordt de registratie-ID gebruikt om het apparaat te identificeren.
       - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Als u klaar bent, drukt u op de knop **Opslaan** . 

     ![Gegevens van apparaatinschrijving invoeren in de portalblade](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Als het apparaat is ingeschreven, wordt de *Registration ID* ervan weergegeven in de lijst onder het tabblad *Individual Enrollments*. 

1. Druk op *Enter* in het opdracht venster (de versie die de **_goedkeurings sleutel_** , **_registratie-ID_** en voorgestelde **_apparaat-id_** weer gegeven) om het gesimuleerde apparaat in te schrijven. De gegevens van uw IoT-hub leest u in de berichten die het opstarten van het apparaat en het verbinding maken met Device Provisioning Service simuleren. 

1. Controleer of het apparaat is ingericht. Wanneer het gesimuleerde apparaat is ingericht met de IoT-hub die is gekoppeld aan uw provisioning-service, wordt de apparaat-ID weer gegeven op de Blade **IOT-apparaten** van de hub. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaatdubbels begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met het voor beeld van de apparaatclient, moet u de resources die u in deze Quick Start hebt gemaakt, niet opschonen. Als u niet wilt door gaan, gebruikt u de volgende stappen om alle resources te verwijderen die door deze Quick start zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Sluit het TPM-simulatorvenster op de computer.
1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw Device Provisioning Service. Klik boven aan de Blade **overzicht** op **verwijderen** boven aan het deel venster.  
1. Selecteer in het menu aan de linkerkant in het Azure Portal **alle resources** en selecteer vervolgens uw IOT-hub. Klik boven aan de Blade **overzicht** op **verwijderen** boven aan het deel venster.  

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een door de TPM gesimuleerd apparaat op de computer gemaakt en dit ingericht voor uw IoT-hub met behulp van de IoT Hub Device Provisioning Service. Als u wilt weten hoe u uw TPM-apparaat programmatisch kunt registreren, gaat u verder met de Quick start voor programmatische inschrijving van een TPM-apparaat. 

> [!div class="nextstepaction"]
> [Azure Quick Start-TPM-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-csharp.md)
