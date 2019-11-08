---
title: Sensor gegevens ophalen van de partners
description: Hierin wordt beschreven hoe u sensor gegevens van partners kunt ophalen
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 380c67e5aeaba9be60e016f173e4da127e4e5f14
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73798178"
---
# <a name="get-sensor-data-from-sensor-partners"></a>Sensor gegevens ophalen van sensor partners

Met Azure FarmBeats kunt u streaming-gegevens van uw IoT-apparaten en Sens oren naar Data hub halen. Op dit moment worden de volgende partners voor sensor apparaten ondersteund:

  ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/partner-information-1.png)

Het integreren van apparaatgegevens met Azure FarmBeats helpt u bij het ophalen van de basis gegevens van de IoT-Sens oren die in uw farm zijn geïmplementeerd, naar de data hub. De gegevens kunnen worden gevisualiseerd via de FarmBeats-Accelerator en kunnen worden gebruikt voor het bouwen van gegevens fusie en AI/ML-modellen met behulp van FarmBeats.

Controleer het volgende om te beginnen met het streamen van sensor gegevens:

-  U hebt FarmBeats geïnstalleerd vanuit Azure Marketplace.
-  U hebt besloten over de Sens oren en apparaten die u wilt installeren op uw farm.
-  Als u van plan bent om bodem sensoren te gebruiken, kunt u de plaatsings kaart van de bodem vocht sensor gebruiken om een aanbeveling te krijgen over het aantal Sens oren en waar exact de Sens oren moeten worden geplaatst. Zie [genereren Maps](generate-maps.md)voor meer informatie.

- Koop en implementeer apparaat/Sens oren van de partner van uw apparaat in uw farm. Zorg ervoor dat u toegang hebt tot de sensor gegevens via de oplossing van uw apparaat partners.

### <a name="enable-device-integration-with-farmbeats"></a>Integratie van apparaten met FarmBeats inschakelen   

Zodra u het streamen van sensor gegevens hebt gestart, kunt u het proces voor het ophalen van de gegevens in uw FarmBeats-systeem starten. U moet de volgende informatie opgeven voor de provider van uw apparaat om de integratie met FarmBeats in te scha kelen:  

 - API-eindpunt  
 - Tenant-id  
 - Client-id  
 - Client geheim  
 - EventHub-verbindings reeks

De bovenstaande informatie wordt door uw systeem integrator verstrekt. Neem contact op met uw systeem integrator voor problemen bij het inschakelen van de integratie van het apparaat.

U kunt ook de referenties genereren door dit script uit te voeren vanuit de Azure Cloud Shell. Volg de onderstaande stappen:

1. Down load het [zip-bestand](https://aka.ms/farmbeatspartnerscript) en pak het uit naar uw lokale station. U vindt er twee bestanden in het ZIP-bestand.
2. Meld u aan bij https://portal.azure.com/ en open Cloud Shell (deze optie is beschikbaar in de rechter balk van de portal)  

    ![Maten van project-Farm](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Zorg ervoor dat de omgeving is ingesteld op **Power shell** -standaard is ingesteld op bash.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

4. Upload de twee bestanden (uit stap 1 hierboven) in uw Cloud Shell.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/power-shell-two-1.png)

5. Ga naar de map waarin de bestanden zijn geüpload (standaard wordt deze geüpload naar de basismap > gebruikers naam).
6. Voer het volgende script uit:

    ```azurepowershell-interactive 

    PS> ./generateCredentials.ps1   

    ```
7. Volg de instructies op het scherm voor het vastleggen van de waarden. (API-eind punt, Tenant-ID, client-ID, client geheim en EventHub-verbindings reeks).

**Apparaatgegevens integreren met de gegenereerde referenties**

Ga naar de partner portal van het apparaat om FarmBeats te koppelen met behulp van de set met referenties die u hebt gegenereerd in de vorige sectie.

 - API-eindpunt  
 - EventHub-verbindings reeks  
 - Client-id  
 - Client geheim  
 - Tenant-id  

 De provider van het apparaat bevestigt een geslaagde integratie. Na de bevestiging kunt u alle apparaten en Sens oren in azure FarmBeats bekijken.

## <a name="view-devices-and-sensors"></a>Apparaten en Sens oren weer geven

Gebruik de volgende sectie om de apparaten en Sens oren in uw farm weer te geven.

### <a name="view-devices"></a>Apparaten weergeven

Momenteel FarmBeats ondersteunt de volgende apparaten:

- **Knoop punt**: een apparaat waaraan een of meer Sens oren zijn gekoppeld.
- **Gateway**: een apparaat waaraan een of meer Sens oren zijn gekoppeld.

Voer de volgende stappen uit:

1. Op de start pagina selecteert u **apparaten** in het menu.
  Op de pagina apparaten wordt het type apparaat, het model, de status, de farm waarin het is geplaatst en de datum waarop de meta gegevens voor het laatst zijn bijgewerkt weer gegeven. De kolom Farm is standaard ingesteld op NULL. U kunt ervoor kiezen om een apparaat toe te wijzen aan een farm. Zie [apparaten toewijzen](#assign-devices)voor meer informatie.
2. Selecteer het apparaat om de apparaateigenschappen, telemetrie en onderliggende apparaten weer te geven die zijn verbonden met het apparaat.  

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/view-devices-1.png)

### <a name="view-sensors"></a>Sens oren weer geven

Voer de volgende stappen uit:

1. Selecteer op de start pagina **Sens oren** in het menu.
  De pagina Sens oren bevat details over het type sensor, de farm waarmee deze is verbonden, het bovenliggende apparaat, de poort naam, het poort type en de laatst bijgewerkte status.
2. Selecteer de sensor om de sensor eigenschappen, actieve waarschuwingen en telemetrie van de sensor weer te geven.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/view-sensors-1.png)

## <a name="assign-devices"></a>Apparaten toewijzen  

Zodra u de sensor gegevens in hebt geplaatst, kunt u deze toewijzen aan de farm waarin u de Sens oren hebt geïmplementeerd.

1. Selecteer op de start pagina **Farms** in het menu, de lijst pagina **Farms** wordt weer gegeven.  
2. Selecteer de farm waaraan u het apparaat wilt toewijzen en selecteer **apparaten toevoegen**.  
3. Het venster **apparaten toevoegen** wordt weer gegeven. Selecteer het apparaat dat u wilt toewijzen aan de farm.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. Selecteer **apparaten toevoegen**. U kunt ook naar het menu **apparaten** gaan, de apparaten selecteren die u wilt toewijzen aan een farm en **apparaten koppelen**selecteren.  
5. Selecteer in het venster **apparaten koppelen** de optie Farm in de vervolg keuzelijst en selecteer **Toep assen op alles** om de farm aan alle geselecteerde apparaten te koppelen.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/associate-devices-1.png)

6. Als u elk apparaat aan een andere farm wilt koppelen, selecteert u de vervolg keuzelijst in de kolom **toewijzen aan Farm** en selecteert u een farm voor elke rij van het apparaat.  
7. Selecteer **toewijzen** om de apparaattoewijzing te volt ooien.

### <a name="visualize-sensor-data"></a>Sensor gegevens visualiseren

Voer de volgende stappen uit:

1. Selecteer op de start pagina **Farms** in het menu om de pagina **Farms** weer te geven.  
2. Selecteer de **Farm** waarvan u de sensor gegevens wilt bekijken.  
3. Op het dash board van de **Farm** kunt u telemetrie-gegevens weer geven. U kunt ervoor kiezen om live-telemetrie weer te geven of een **aangepast bereik** te gebruiken om in een bepaald datum bereik weer te geven.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/telemetry-data-1.png)

## <a name="delete-sensor"></a>Sensor verwijderen

Volg deze stappen:

1. Op de start pagina selecteert u **Sens oren** in het menu om de pagina **Sens oren** weer te geven.  
2. Selecteer het apparaat dat u wilt verwijderen en selecteer **verwijderen** uit bevestigings venster.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/delete-sensors-1.png)

Er wordt een bevestigings bericht weer gegeven dat de sensor is verwijderd.  

## <a name="delete-devices"></a>Apparaten verwijderen

Volg deze stappen:

1. Op de start pagina selecteert u **apparaten** in het menu om de pagina apparaten weer te geven.  
2. Selecteer het apparaat dat u wilt verwijderen en selecteer **verwijderen** in het bevestigings venster.

    ![Maten van project-Farm](./media/get-sensor-data-from-sensor-partner/delete-device-1.png)

## <a name="next-steps"></a>Volgende stappen

U hebt nu sensor gegevens die in uw Azure FarmBeats-exemplaar worden geplaatst. Nu leert u hoe u [kaarten](generate-maps.md#generate-maps) voor uw Farms kunt genereren.
