---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 7700f1c92aecab76dbc347814b7b161bc3d822a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176091"
---
### <a name="to-configure-and-register-the-device"></a>Het apparaat configureren en registreren
1. Open de Windows PowerShell-interface op de seriële console van het StorSimple-apparaat. Zie [PuTTY gebruiken om verbinding te maken met de seriële console van het apparaat](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#use-putty-to-connect-to-the-device-serial-console) voor instructies. **Volg de procedure exact, omdat u anders geen toegang hebt tot de console.**
2. Druk in de geopende sessie eenmaal op **Enter** om een opdrachtprompt weer te geven.
3. U wordt gevraagd de taal te kiezen die u voor uw apparaat wilt instellen. Geef de taal op en druk op **Enter**.
   
    ![Apparaat 1 configureren en registreren met StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice1-gov-include.png)
4. Kies in het weergegeven menu van de seriële console optie 1 om **u aan te melden met volledige toegang**.
   
    ![Apparaat 2 registreren met StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice2-gov-include.png)
5. Voer de volgende stappen uit om de minimaal vereiste netwerkinstellingen voor uw apparaat te configureren.
   
   > [!IMPORTANT]
   > Deze configuratiestappen moeten worden uitgevoerd op de actieve controller van het apparaat. In het menu van de seriële console wordt de controllerstatus in het bannerbericht aangegeven. Als u geen verbinding maakt met de actieve controller, koppelt u de verbinding en maakt u verbinding met de actieve controller.
   
   1. Typ uw wachtwoord bij de opdrachtprompt. Het standaardapparaatwachtwoord is **Password1**.
   2. Typ de volgende opdracht:
      
        `Invoke-HcsSetupWizard`
   3. Er wordt een instellingenwizard weergegeven om u te helpen bij het configureren van de netwerkinstellingen voor het apparaat. Geef de volgende informatie op:
      
      * IP-adres voor data 0-netwerkinterface
      * Subnetmasker
      * Gateway
      * IP-adres voor de primaire DNS-server
      * IP-adres voor de primaire NTP-server
      
      > [!NOTE]
      > Het kan zijn dat u enkele minuten moet wachten tot de subnetmasker- en DNS-instellingen worden toegepast.
    
   4. Configureer de optie de webserver.
      
      > [!IMPORTANT]
      > De configuratie van uw webproxy is weliswaar optioneel, maar houd er rekening mee dat als u een webproxy gebruikt, deze alleen hier kan worden geconfigureerd. Zie [Configure web proxy for your device](../articles/storsimple/storsimple-configure-web-proxy.md) (Webproxy voor uw apparaat configureren) voor meer informatie.
     
6. Druk op Ctrl + C om de wizard Setup af te sluiten.
8. Voer de volgende cmdlet uit om het apparaat naar de Microsoft Azure Government-portal te wijzen (omdat het standaard naar de openbare klassieke Azure-portal verwijst). Hierdoor starten beide controllers opnieuw. We raden u aan twee PuTTY-sessies te gebruiken om tegelijkertijd verbinding te maken met beide controllers, zodat u zien wanneer elke controller opnieuw wordt opgestart.
   
    `Set-CloudPlatform -AzureGovt_US`
   
   U ziet een bevestigingsbericht. Accepteer de standaardinstelling **(Y).**
9. Voer de volgende cmdlet uit om de installatie te hervatten:
   
    `Invoke-HcsSetupWizard`
   
    ![Wizard Setup hervatten](./media/storsimple-configure-and-register-device-gov-u2/HCS_ResumeSetup-gov-include.png)
   
10. Accepteer de netwerkinstellingen. U ziet een validatiebericht nadat u elke instelling hebt geaccepteerd.
11. Uit veiligheidsoverwegingen is het beheerderswachtwoord van het apparaat na de eerste sessie verlopen en moet u het nu wijzigen. Als daarom wordt gevraagd, geeft u een beheerderswachtwoord voor het apparaat op. Een geldig beheerderswachtwoord voor een apparaat moet 8 tot 15 tekens bevatten. Het wachtwoord moet drie van de volgende elementen bevatten: kleine letters, hoofdletters, cijfers en speciale tekens.
    
    <br/>![Apparaat 5 registreren met StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice5_gov-include.png)
12. Bij de laatste stap in de instellingenwizard wordt uw apparaat geregistreerd met de StorSimple-apparaatbeheerfunctie. Hiervoor hebt u de serviceregistratiesleutel nodig die u hebt verkregen in [stap 2: Haal de serviceregistratiesleutel op.](../articles/storsimple/storsimple-8000-deployment-walkthrough-gov-u2.md#step-2-get-the-service-registration-key) Nadat u de registratiesleutel hebt opgegeven, moet u mogelijk twee tot drie minuten wachten voordat het apparaat is geregistreerd.
    
    > [!NOTE]
    > U kunt op elk gewenst moment op Ctrl+C drukken om de instellingenwizard af te sluiten. Als u alle netwerkinstellingen (IP-adres voor Data 0, subnetmasker en gateway) hebt ingevoerd, blijven uw gegevens behouden.
    
    ![StorSimple registratie voortgang](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegistrationProgress-gov-include.png)
13. Nadat het apparaat is geregistreerd, wordt er een gegevensversleutelingssleutel van de service weergegeven. Kopieer deze sleutel en bewaar deze op een veilige plaats. **Deze sleutel is vereist met de serviceregistratiesleutel om extra apparaten te registreren bij de StorSimple Device Manager-service.** Zie [StorSimple security](../articles/storsimple/storsimple-8000-security.md) (StorSimple-beveiliging) voor meer informatie over deze sleutel.
    
    ![Apparaat 7 registreren met StorSimple](./media/storsimple-configure-and-register-device-gov-u2/HCS_RegisterYourDevice7_gov-include.png)
    > [!IMPORTANT]
    > Kopieer de tekst uit het venster van de seriële console gewoon door de tekst te selecteren. U kunt de tekst vervolgens op het klembord of in een teksteditor plakken.
    > 
    > Gebruik **Ctrl + C** NIET om de versleutelingssleutel voor servicegegevens te kopiëren. Als u **Ctrl + C gebruikt,** wordt de wizard Setup afgesloten. Als gevolg hiervan wordt het beheerderswachtwoord van het apparaat niet gewijzigd en wordt het standaardwachtwoord opnieuw ingesteld.
    
14. Sluit de seriële console af.
15. Ga terug naar de Azure Government Portal en voer de volgende stappen uit:
    
    1. Ga naar uw StorSimple-apparaatbeheerservice.
    2. Klik op **Apparaten**. Identificeer in de lijst met apparaten het apparaat dat u implementeert. Controleer of het apparaat is verbonden met de service door de status op te zoeken. Het apparaat moet de status **Online** hebben.
            
        Als de status van het apparaat **Offline** is, moet u een paar minuten wachten totdat het apparaat online is.
       
        Als het apparaat na een paar minuten nog steeds offline is, moet u ervoor zorgen dat uw firewallnetwerk is geconfigureerd zoals wordt beschreven in [Netwerkvereisten voor uw StorSimple-apparaat](../articles/storsimple/storsimple-8000-system-requirements.md).
       
        Controleer of poort 9354 open is voor uitgaande communicatie, omdat de servicebus deze poort gebruikt voor de communicatie van de StorSimple-apparaatbeheerfunctie naar het apparaat.

