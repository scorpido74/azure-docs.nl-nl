---
title: Sensorgegevens ophalen van de partners
description: In dit artikel wordt beschreven hoe u sensorgegevens van partners ophalen.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 916c828365c8f9f50f408bd6c51182bb6e89605f
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384191"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Sensorgegevens ophalen bij sensorpartners

Azure FarmBeats helpt u om streaminggegevens van uw IoT-apparaten en sensoren naar Datahub te brengen. Momenteel worden de volgende sensorapparaatpartners ondersteund.

  ![FarmBeats partners](./media/get-sensor-data-from-sensor-partner/partner-information-2.png)

Door apparaatgegevens te integreren met Azure FarmBeats u grondgegevens van de IoT-sensoren die in uw farm worden geïmplementeerd, naar de gegevenshub. De gegevens, zodra beschikbaar, kunnen worden gevisualiseerd via de FarmBeats-versneller. De gegevens kunnen worden gebruikt voor data fusion en machine learning/artificial intelligence (ML/AI) modelbuilding met FarmBeats.

Als u het streamen van sensorgegevens wilt starten, moet u het volgende garanderen:

-  U hebt FarmBeats in Azure Marketplace geïnstalleerd.
-  U hebt besloten over de sensoren en apparaten die u wilt installeren op uw boerderij.
-  Als u van plan bent bodemvochtsensoren te gebruiken, gebruikt u de FarmBeats Soil Moisture Sensor Placement-kaart om een aanbeveling te krijgen over het aantal sensoren en waar u ze precies moet plaatsen. Zie [Kaarten genereren](generate-maps-in-azure-farmbeats.md)voor meer informatie .
- U koopt en implementeert apparaten of sensoren van uw apparaatpartner op uw farm. Zorg ervoor dat u toegang hebt tot de sensorgegevens via de oplossing van uw apparaatpartners.

## <a name="enable-device-integration-with-farmbeats"></a>Apparaatintegratie inschakelen met FarmBeats

Nadat u het streamen van sensorgegevens hebt gestart, u beginnen met het proces om de gegevens in uw FarmBeats-systeem te krijgen. Geef de volgende informatie aan uw apparaatprovider om de integratie met FarmBeats mogelijk te maken:

 - API-eindpunt
 - Tenant-id
 - Client-id
 - Clientgeheim
 - EventHub-verbindingstekenreeks

U de bovenstaande informatie genereren door de volgende stappen te volgen: (Houd er rekening mee dat deze stappen op Azure moeten worden uitgevoerd, zodat u toegang nodig hebt tot het Azure-abonnement waarbij FarmBeats is geïmplementeerd)

1. Meld u aan bij https://portal.azure.com/.

2. **Als je op FarmBeats versie 1.2.7 of hoger bent, sla dan stap 2a, 2b en 2c over en ga naar stap 3.**. U de FarmBeats-versie controleren door te klikken op het pictogram Instellingen rechtsboven in de FarmBeats-gebruikersinterface.

2a. Ga naar Azure Active Directory ->-app-registraties

2b. Klik op de appregistratie die is gemaakt als onderdeel van uw FarmBeats-implementatie. Het heeft dezelfde naam als uw FarmBeats-gegevenshub.

2c. Klik op "Een API blootstellen" -> Klik op "Een clienttoepassing toevoegen" en voer **04b07795-8ddb-461a-bbee-02f9e1bf7b46** in en controleer "Scope autoriseren". Dit geeft toegang tot de Azure CLI (Cloud Shell) om de onderstaande stappen uit te voeren.

3. Open Cloud Shell. Deze optie is beschikbaar op de werkbalk in de rechterbovenhoek van de Azure-portal.

    ![Werkbalk Azure-portal](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

5. Controleer of de omgeving is ingesteld op **PowerShell.** Standaard is het ingesteld op Bash.

    ![PowerShell-werkbalkinstelling](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

6. Ga naar je thuisgids.

   ```azurepowershell-interactive 

    cd  

    ```

7. Voer de volgende opdracht uit. Hiermee wordt een script gedownload naar uw thuismap.

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1 

    ```

8. Voer het volgende script uit. Het script vraagt om de tenant-id die kan worden verkregen uit azure Active Directory -> overzichtspagina.

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

9. Volg de instructies op het scherm om de waarden vast te leggen voor **API-eindpunt,** **tenant-id,** **client-id,** **clientgeheim**en **EventHub-verbindingstekenreeks**.

### <a name="integrate-device-data-by-using-the-generated-credentials"></a>Apparaatgegevens integreren met behulp van de gegenereerde referenties

Nu hebt u de volgende informatie gegenereerd uit de vorige sectie.
 - API-eindpunt
 - EventHub-verbindingstekenreeks
 - Client-id
 - Clientgeheim
 - Tenant-id
 
U moet dit aan uw apparaatpartner verstrekken voor het koppelen van FarmBeats. Ga naar de portal van de apparaatpartner om hetzelfde te doen. Bijvoorbeeld, in het geval u gebruik maakt van apparaten van Davis Instruments, Teralytic of Pessl Instruments (Metos.at) ga dan naar de overeenkomstige pagina's zoals hieronder vermeld:

[Davis Instrumenten](https://weatherlink.github.io/azure-farmbeats/setup)

[Teralytic](https://app.teralytic.com/)

[Pessl-instrumenten](https://ng.fieldclimate.com/user-api-services)

 De apparaatprovider bevestigt een succesvolle integratie. Na bevestiging u alle apparaten en sensoren op Azure FarmBeats bekijken.

## <a name="view-devices-and-sensors"></a>Apparaten en sensoren bekijken

Gebruik de volgende sectie om de apparaten en sensoren voor uw bedrijf te bekijken.

### <a name="view-devices"></a>Apparaten weergeven

Momenteel ondersteunt FarmBeats de volgende apparaten:

- **Knooppunt**: Een apparaat waaraan een of meer sensoren zijn bevestigd.
- **Gateway:** een apparaat waaraan een of meer knooppunten zijn bevestigd.

Volg deze stappen.

1. Selecteer **Apparaten in** het menu op de startpagina.
  Op de pagina **Apparaten** wordt het apparaattype, het model, de status, de farm waarin het is geplaatst en de laatst bijgewerkte datum voor metagegevens weergegeven. Standaard is de farmkolom ingesteld op *NULL*. U ervoor kiezen om een apparaat aan een farm toe te wijzen. Zie [Apparaten toewijzen](#assign-devices)voor meer informatie .
2. Selecteer het apparaat om de eigenschappen van het apparaat, telemetrie en onderliggende apparaten weer te geven die op het apparaat zijn verbonden.

    ![Pagina Apparaten](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Sensoren bekijken

Volg deze stappen.

1. Selecteer **sensoren op** de startpagina in het menu.
  Op de pagina **Sensoren** worden details weergegeven over het type sensor, de farm waarop deze is verbonden, het bovenliggende apparaat, de poortnaam, het poorttype en de laatst bijgewerkte status.
2. Selecteer de sensor om sensoreigenschappen, actieve waarschuwingen en telemetrie van de sensor weer te geven.

    ![Pagina Sensoren](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Apparaten toewijzen  

Nadat de sensorgegevens binnenstromen, u deze toewijzen aan de boerderij waar u de sensoren hebt geïmplementeerd.

1. Selecteer Op de startpagina **Farms** in het menu. De **lijstpagina Boerderijen** wordt weergegeven.
2. Selecteer de farm waaraan u het apparaat wilt toewijzen en selecteer **Apparaten toevoegen**.
3. Het venster **Apparaten toevoegen** wordt weergegeven. Selecteer het apparaat dat u aan de farm wilt toewijzen.

    ![Venster Apparaten toevoegen](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecteer **Apparaten toevoegen**. U ook naar het menu **Apparaten** gaan, de apparaten selecteren die u aan een farm wilt toewijzen en **Apparaten koppelen**selecteren.
5. Selecteer **in** het venster Apparaten koppelen de farm in de vervolgkeuzelijst en selecteer **Toepassen op alles** om de farm aan alle geselecteerde apparaten te koppelen.

    ![Venster Apparaten koppelen](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Als u elk apparaat aan een andere farm wilt koppelen, selecteert u de vervolgkeuzepijl in de kolom **Toewijzen aan farm** en selecteert u een farm voor elke apparaatrij.
7. Selecteer **Toewijzen** om de apparaattoewijzing te voltooien.

### <a name="visualize-sensor-data"></a>Sensorgegevens visualiseren

Volg deze stappen.

1. Selecteer op de startpagina **Farms** in het menu om de pagina **Boerderijen** weer te geven.
2. Selecteer de **farm** waarvoor u de sensorgegevens wilt zien.
3. Op het dashboard **Farm** u telemetriegegevens weergeven. U live telemetrie bekijken of **Aangepast bereik** gebruiken om een specifiek datumbereik te bekijken.

    ![Farm-dashboard](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-a-sensor"></a>Een sensor verwijderen

Volg deze stappen.

1. Selecteer op de startpagina **Sensoren** in het menu om de pagina **Sensoren** weer te geven.
2. Selecteer het apparaat dat u wilt verwijderen en selecteer **Verwijderen** in het bevestigingsvenster.

    ![De knop Verwijderen](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Een bevestigingsbericht laat zien dat de sensor is verwijderd.

## <a name="delete-devices"></a>Apparaten verwijderen

Volg deze stappen.

1. Selecteer op de startpagina **Apparaten** in het menu om de pagina **Apparaten** weer te geven.
2. Selecteer het apparaat dat u wilt verwijderen en selecteer **Verwijderen** in het bevestigingsvenster.

    ![De knop Verwijderen](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Volgende stappen

U hebt nu sensorgegevens die naar uw Azure FarmBeats-exemplaar stromen. Leer nu hoe u kaarten voor uw boerderijen [genereren.](generate-maps-in-azure-farmbeats.md#generate-maps)
