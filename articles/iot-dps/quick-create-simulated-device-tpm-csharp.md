---
title: 'Snelstart - Een gesimuleerd TPM-apparaat inrichten op Azure IoT Hub met C #'
description: Snelstart - Een gesimuleerd TPM-apparaat maken en inrichten met C# device SDK voor Azure IoT Hub Device Provisioning Service (DPS). In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 82bd284ede23e8880f79c614f4a6e2f588a4293c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74976992"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Snelstart: een gesimuleerd TPM-apparaat maken en inrichten met C# device SDK voor IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Deze stappen laten u zien hoe u de [Azure IoT-voorbeelden voor C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp) gebruikt om een TPM-apparaat te simuleren op een ontwikkelcomputer met het Windows-besturingssysteem. In het voorbeeld wordt het gesimuleerde apparaat ook verbonden met een IoT Hub met behulp van de Device Provisioning Service. 

De voorbeeldcode gebruikt de Windows TPM-simulator als de [HSM (Hardware Security Module)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) van het apparaat. 

Als u niet bekend bent met het proces van automatisch inrichten, bekijk dan ook de [Concepten voor automatische inrichting](concepts-auto-provisioning.md). Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat. 

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om een enkel apparaat in te schrijven.

In dit artikel worden afzonderlijke inschrijvingen gedemonstreerd.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden 

1. Zorg ervoor dat u de [.NET Core 2.1 SDK of hoger](https://www.microsoft.com/net/download/windows) op uw machine hebt geïnstalleerd. 

1. Zorg ervoor dat `git` op de computer wordt geïnstalleerd en toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

1. Open een opdrachtprompt of Git Bash. Kloon de Azure IoT-voorbeelden GitHub-opslagplaats voor C#:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>Het gesimuleerde apparaat inrichten

1. Meld u aan bij Azure Portal. Selecteer de knop **Alle bronnen** in het linkermenu en open de service Apparaatvoorziening. Noteer in het **blad Overzicht** de waarde **_ID-bereik._**

    ![Bereik-id van provisioning-service kopiëren van portal-blade](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

1. Ga op een opdrachtprompt naar de projectmap met de voorbeeldcode voor het inrichten van het de TPM-apparaat.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

1. Typ de volgende opdracht om de voorbeeldcode voor het TPM-apparaat te bouwen en uit te voeren. Vervang de waarde voor `<IDScope>` door het id-bereik voor uw provisioning-service. 

    ```cmd
    dotnet run <IDScope>
    ```

    Met deze opdracht wordt de TPM-chip-simulator in een aparte opdrachtprompt gestart. In Windows u een Windows Security Alert tegenkomen waarin wordt gevraagd of u Simulator.exe wilt toestaan om te communiceren op openbare netwerken. Voor de toepassing van dit voorbeeld u het verzoek annuleren.

1. In het oorspronkelijke opdrachtvenster worden de **_goedkeuringssleutel_**, de **_registratie-id_** en een voorgestelde **_apparaat-id_** weergegeven die nodig is voor apparaatinschrijving. Noteer deze waarden. U gebruikt deze waarde om een afzonderlijke inschrijving te maken in uw Device Provisioning Service-instantie. 
   > [!NOTE]
   > Verwar het venster met opdrachtuitvoer niet met het venster dat uitvoer van de TPM-simulator bevat. Mogelijk moet u het oorspronkelijke opdrachtvenster selecteren om het naar de voorgrond te brengen.

    ![Uitvoer van het opdrachtvenster](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

1. Selecteer in de Azure-portal in het menu Apparaatinrichtingsservice de optie **Inschrijvingen beheren**. Selecteer het tabblad **Individuele inschrijvingen** en selecteer bovenaan de knop **Afzonderlijke inschrijving toevoegen.** 

1. Voer **in** het deelvenster Inschrijving toevoegen de volgende gegevens in:
   - Selecteer **TPM** als *mechanisme* voor identiteitscontrole.
   - Voer de *registratie-id* en *goedkeuringssleutel* voor uw TPM-apparaat in op basis van de eerder aangenomen waarden.
   - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
   - Desgewenst kunt u de volgende informatie verstrekken:
       - Voer een unieke *apparaat-id* in (u de voorgestelde id gebruiken of uw eigen gegevens verstrekken). Vermijd gevoelige gegevens bij het benoemen van uw apparaat. Als u ervoor kiest er geen aan te geven, wordt de registratie-id gebruikt om het apparaat te identificeren.
       - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Druk op de knop **Opslaan** nadat u bent voltooid. 

     ![Gegevens van apparaatinschrijving invoeren in de portalblade](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Als het apparaat is ingeschreven, wordt de *Registration ID* ervan weergegeven in de lijst onder het tabblad *Individual Enrollments*. 

1. Druk op *Enter* in het opdrachtvenster (het venster waarop de **_goedkeuringssleutel_**, **_registratie-id_** en voorgestelde **_apparaat-id_** is weergegeven) om het gesimuleerde apparaat in te schrijven. De gegevens van uw IoT-hub leest u in de berichten die het opstarten van het apparaat en het verbinding maken met Device Provisioning Service simuleren. 

1. Controleer of het apparaat is ingericht. Bij een succesvolle inrichting van het gesimuleerde apparaat naar de IoT-hub die is gekoppeld aan uw inrichtingsservice, wordt de apparaat-id weergegeven op het **Iot-apparaatblad** van de hub. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaattweelingen begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent verder te werken aan en het voorbeeld van de apparaatclient te verkennen, moet u de bronnen die in deze quickstart zijn gemaakt, niet opschonen. Als u niet van plan bent door te gaan, gebruikt u de volgende stappen om alle bronnen die door deze quickstart zijn gemaakt, te verwijderen.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Sluit het TPM-simulatorvenster op de computer.
1. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens de service Apparaatvoorziening. Druk boven aan het **overzichtsblad** op **Delete** boven aan het deelvenster.  
1. Selecteer **alle bronnen** in het linkermenu in de Azure-portal en selecteer vervolgens uw IoT-hub. Druk boven aan het **overzichtsblad** op **Delete** boven aan het deelvenster.  

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een TPM-gesimuleerd apparaat op uw machine gemaakt en deze in gerichte tijd in uw IoT-hub met behulp van de IoT Hub Device Provisioning Service. Ga door met de snelle start voor programmatische inschrijving van een TPM-apparaat om uw TPM-apparaat programmatisch in te schrijven. 

> [!div class="nextstepaction"]
> [Azure quickstart - TPM-apparaat inschrijven voor Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-csharp.md)
