---
title: De StorSimple Device Manager-service implementeren in Azure | Microsoft Documenten
description: In de Azure-portal wordt uitgelegd hoe u de StorSimple Device Manager-service maakt en verwijdert in de Azure-portal en wordt beschreven hoe u de serviceregistratiesleutel beheert.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 1e75acc03209fdd7e613801c9152f24aaecfa6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267779"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>De StorSimple Device Manager-service implementeren voor apparaten uit de StorSimple 8000-serie

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht

De StorSimple Device Manager-service wordt uitgevoerd in Microsoft Azure en maakt verbinding met meerdere StorSimple-apparaten. Nadat u de service hebt gemaakt, u deze gebruiken om alle apparaten te beheren die zijn verbonden met de StorSimple Device Manager-service vanaf één centrale locatie, waardoor de administratieve lasten worden geminimaliseerd.

In deze zelfstudie worden de stappen beschreven die nodig zijn voor het maken, verwijderen, migreren van de service en het beheer van de serviceregistratiesleutel. De informatie in dit artikel is alleen van toepassing op Apparaten uit de StorSimple 8000-serie. Voor meer informatie over StorSimple Virtual Arrays, ga naar [de implementatie van een StorSimple Device Manager-service voor uw StorSimple Virtual Array.](storsimple-virtual-array-manage-service.md)

> [!NOTE]
> -  De Azure-portal ondersteunt apparaten waarop Update 5.0 of hoger wordt uitgevoerd. Als uw apparaat niet up-to-date is, installeert u Update 5 onmiddellijk. Ga voor meer informatie naar [Update 5 installeren.](storsimple-8000-install-update-5.md) 
> - Als u een StorSimple Cloud Appliance (8010/8020) gebruikt, u een cloudtoestel niet bijwerken. Gebruik de nieuwste versie van software om een nieuw cloudtoestel te maken met Update 5.0 en vervolgens niet over te gaan naar het nieuwe cloudtoestel dat is gemaakt. 
> - Alle apparaten met Update 4.0 of eerder krijgen minder beheerfunctionaliteit. 

## <a name="create-a-service"></a>Een service maken
Als u een StorSimple Device Manager-service wilt maken, moet u het als:

* Een abonnement met een Enterprise Agreement
* Een actief Microsoft Azure-opslagaccount
* De factureringsgegevens die worden gebruikt voor toegangsbeheer

Alleen abonnementen met een Enterprise Agreement zijn toegestaan. U er ook voor kiezen om een standaardopslagaccount te genereren wanneer u de service maakt.

Eén service kan meerdere apparaten beheren. Een apparaat kan echter niet meerdere services omvatten. Een grote onderneming kan meerdere service-exemplaren hebben om te werken met verschillende abonnementen, organisaties of zelfs implementatielocaties. 

> [!NOTE]
> U hebt afzonderlijke exemplaren van de StorSimple Device Manager-service nodig om storSimple 8000-serieapparaten en StorSimple Virtual Arrays te beheren.

Voer de volgende stappen uit om een service te maken.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Voor elke StorSimple Device Manager-service bestaan de volgende kenmerken:

* **Naam** : de naam die is toegewezen aan uw StorSimple Device Manager-service toen deze werd gemaakt. **De servicenaam kan niet worden gewijzigd nadat de service is gemaakt. Dit geldt ook voor andere entiteiten, zoals apparaten, volumes, volumecontainers en back-upbeleid die niet kunnen worden hernoemd in de Azure-portal.**
* **Status** – De status van de service, die **actief**, **maken**of **online**kan zijn.
* **Locatie** – De geografische locatie waar het StorSimple-apparaat wordt ingezet.
* **Abonnement** : het factureringsabonnement dat aan uw service is gekoppeld.

## <a name="delete-a-service"></a>Een service verwijderen

Voordat u een service verwijdert, moet u ervoor zorgen dat er geen verbonden apparaten worden gebruikt. Als de service in gebruik is, schakelt u de verbonden apparaten uit. Met de deactiverenwordt de verbinding tussen het apparaat en de service verbroken, maar worden de apparaatgegevens in de cloud bewaard.

> [!IMPORTANT]
> Nadat een service is verwijderd, kan de bewerking niet worden teruggedraaid. Elk apparaat dat de service gebruikte, moet worden gereset naar fabrieksstandaardinstellingen voordat het met een andere service kan worden gebruikt. In dit scenario gaan de lokale gegevens op het apparaat en de configuratie verloren.

Voer de volgende stappen uit om een service te verwijderen.

### <a name="to-delete-a-service"></a>Een service verwijderen

1. Zoek naar de service die u wilt verwijderen. Klik op het pictogram **Resources** en voer de juiste termen in om te zoeken. Klik in de zoekresultaten op de service die u wilt verwijderen.

    ![Zoekservice die moet worden verwijderd](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Dit brengt u naar het StorSimple Device Manager serviceblade. Klik **op Verwijderen**.

    ![Service verwijderen](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Klik **op Ja** in de bevestigingsmelding. Het kan enkele minuten duren voordat de service is verwijderd.

    ![Verwijdering bevestigen](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>De serviceregistratiesleutel ophalen

Nadat u een service hebt gemaakt, moet u uw StorSimple-apparaat registreren bij de service. Om uw eerste StorSimple-apparaat te registreren, hebt u de serviceregistratiesleutel nodig. Als u extra apparaten wilt registreren bij een bestaande StorSimple-service, hebt u zowel de registratiesleutel als de versleutelingssleutel voor servicegegevens nodig (die tijdens de registratie op het eerste apparaat wordt gegenereerd). Zie [StorSimple security voor](storsimple-8000-security.md)meer informatie over de versleutelingssleutel voor servicegegevens. U de registratiesleutel krijgen door toegang te krijgen tot **Sleutels** op uw StorSimple Device Manager-mes.

Voer de volgende stappen uit om de serviceregistratiesleutel op te halen.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Bewaar de serviceregistratiesleutel op een veilige locatie. U hebt deze sleutel, evenals de versleutelingssleutel voor servicegegevens, nodig om extra apparaten bij deze service te registreren. Nadat u de serviceregistratiesleutel hebt verkregen, moet u uw apparaat configureren via de Windows PowerShell-interface voor StorSimple.

Zie [Stap 3: Het apparaat configureren en registreren via Windows PowerShell voor StorSimple voor](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)meer informatie over het gebruik van deze registratiesleutel.

## <a name="regenerate-the-service-registration-key"></a>De serviceregistratiesleutel opnieuw genereren
U moet een serviceregistratiesleutel regenereren als u sleutelrotatie moet uitvoeren of als de lijst met servicebeheerders is gewijzigd. Wanneer u de sleutel regenereert, wordt de nieuwe sleutel alleen gebruikt voor het registreren van volgende apparaten. De apparaten die al zijn geregistreerd, worden niet beïnvloed door dit proces.

Voer de volgende stappen uit om een serviceregistratiesleutel opnieuw te genereren.

### <a name="to-regenerate-the-service-registration-key"></a>De serviceregistratiesleutel regenereren
1. Ga in het **StorSimple Device Manager-blad** naar ** &gt; Management** **Keys.**
    
    ![De blade Sleutels](./media/storsimple-8000-manage-service/regenregkey2.png)

2. Klik **in** het blad Toetsen op **Regenereren**.

    ![Klik op regenereren](./media/storsimple-8000-manage-service/regenregkey3.png)
3. Controleer in het **sleutelblad regenereren serviceregistratie** de actie die nodig is wanneer de sleutels worden geregenereerd. Alle volgende apparaten die bij deze service zijn geregistreerd, gebruiken de nieuwe registratiesleutel. Klik **op Regenereren** om dit te bevestigen. U wordt hiervan op de hoogte gesteld nadat de regeneratie is voltooid.

    ![Regenereren bevestigen](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Er verschijnt een nieuwe serviceregistratiesleutel.

5. Kopieer deze sleutel en bewaar deze voor het registreren van nieuwe apparaten met deze service.



## <a name="change-the-service-data-encryption-key"></a>De versleutelingssleutel voor servicegegevens wijzigen
Versleutelingssleutels voor servicegegevens worden gebruikt om vertrouwelijke klantgegevens, zoals opslagaccountgegevens, te versleutelen die vanuit uw StorSimple Manager-service naar het StorSimple-apparaat worden verzonden. U moet deze sleutels periodiek wijzigen als uw IT-organisatie een sleutelrotatiebeleid heeft voor de opslagapparaten. Het sleutelveranderingsproces kan enigszins verschillen, afhankelijk van of er één apparaat of meerdere apparaten zijn die worden beheerd door de StorSimple Manager-service. Ga voor meer informatie naar [Beveiliging en gegevensbescherming van StorSimple.](storsimple-8000-security.md)

Het wijzigen van de versleutelingssleutel voor servicegegevens is een proces in drie stappen:

1. Met Windows PowerShell-scripts voor Azure Resource Manager moet u een apparaat autoriseren om de versleutelingssleutel voor servicegegevens te wijzigen.
2. Met Behulp van Windows PowerShell voor StorSimple, het initiëren van de service data encryptie sleutel te wijzigen.
3. Als u meer dan één StorSimple-apparaat hebt, werkt u de versleutelingssleutel voor servicegegevens op de andere apparaten bij.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Stap 1: Windows PowerShell-script gebruiken om een apparaat te autoriseren om de versleutelingssleutel voor servicegegevens te wijzigen
Doorgaans zal de apparaatbeheerder de servicebeheerder verzoeken een apparaat te autoriseren om versleutelingssleutels voor servicegegevens te wijzigen. De servicebeheerder geeft het apparaat vervolgens toestemming om de sleutel te wijzigen.

Deze stap wordt uitgevoerd met het op Azure Resource Manager gebaseerde script. De servicebeheerder kan een apparaat selecteren dat in aanmerking komt om te worden geautoriseerd. Het apparaat is dan gemachtigd om het proces voor het wijzigen van de versleutelingssleutel van servicegegevens te starten. 

Ga voor meer informatie over het gebruik van het script naar [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Welke apparaten kunnen worden geautoriseerd om versleutelingssleutels van servicegegevens te wijzigen?
Een apparaat moet aan de volgende criteria voldoen voordat het kan worden gemachtigd om wijzigingen in de versleutelingssleutel van servicegegevens te starten:

* Het apparaat moet online zijn om in aanmerking te komen voor service gegevens encryptie sleutel verandering autorisatie.
* U hetzelfde apparaat na 30 minuten opnieuw autoriseren als de sleutelwijziging niet is gestart.
* U een ander apparaat autoriseren, op voorwaarde dat de sleutelwijziging niet is geïnitieerd door het eerder geautoriseerde apparaat. Nadat het nieuwe apparaat is geautoriseerd, kan het oude apparaat de wijziging niet starten.
* U een apparaat niet autoriseren terwijl de rollover van de versleutelingssleutel voor servicegegevens aan de gang is.
* U een apparaat autoriseren wanneer sommige apparaten die bij de service zijn geregistreerd, over de versleuteling zijn gerold, terwijl andere dat niet hebben gedaan. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Stap 2: Windows PowerShell voor StorSimple gebruiken om de versleutelingssleutelwijziging van servicegegevens te starten
Deze stap wordt uitgevoerd in de Windows PowerShell voor StorSimple-interface op het geautoriseerde StorSimple-apparaat.

> [!NOTE]
> Er kunnen geen bewerkingen worden uitgevoerd in de Azure-portal van uw StorSimple Manager-service totdat de sleutelrollover is voltooid.


Als u de seriële console van het apparaat gebruikt om verbinding te maken met de Windows PowerShell-interface, voert u de volgende stappen uit.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>De versleutelingssleutelwijziging van de versleutelingssleutel van servicegegevens starten
1. Selecteer optie 1 om u aan te melden met volledige toegang.
2. Typ het volgende na de opdrachtprompt:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Nadat de cmdlet is voltooid, krijgt u een nieuwe service data encryptie sleutel. Kopieer en bewaar deze sleutel voor gebruik in stap 3 van dit proces. Deze sleutel wordt gebruikt om alle resterende apparaten bij te werken die zijn geregistreerd bij de StorSimple Manager-service.
   
   > [!NOTE]
   > Dit proces moet worden gestart binnen vier uur na het autoriseren van een StorSimple-apparaat.
   > 
   > 
   
   Deze nieuwe sleutel wordt vervolgens naar de service verzonden om naar alle apparaten te worden gepusht die bij de service zijn geregistreerd. Er verschijnt dan een waarschuwing op het servicedashboard. De service schakelt alle bewerkingen op de geregistreerde apparaten uit en de apparaatbeheerder moet vervolgens de versleutelingssleutel voor servicegegevens op de andere apparaten bijwerken. De I/O's (hosts die gegevens naar de cloud verzenden) worden echter niet verstoord.
   
   Als u één apparaat hebt geregistreerd op uw service, is het rolloverproces nu voltooid en u de volgende stap overslaan. Als u meerdere apparaten hebt geregistreerd op uw service, gaat u door naar stap 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Stap 3: Update de versleutelingssleutel voor servicegegevens op andere StorSimple-apparaten
Deze stappen moeten worden uitgevoerd in de Windows PowerShell-interface van uw StorSimple-apparaat als u meerdere apparaten hebt geregistreerd op uw StorSimple Manager-service. De sleutel die u in stap 2 hebt verkregen, moet worden gebruikt om al het resterende StorSimple-apparaat dat is geregistreerd bij de StorSimple Manager-service bij te werken.

Voer de volgende stappen uit om de versleuteling van servicegegevens op uw apparaat bij te werken.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>De versleutelingssleutel voor servicegegevens bijwerken op fysieke apparaten
1. Gebruik Windows PowerShell voor StorSimple om verbinding te maken met de console. Selecteer optie 1 om u aan te melden met volledige toegang.
2. Typ bij de opdrachtprompt:`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Geef de versleutelingssleutel voor servicegegevens die u hebt verkregen in [stap 2: Gebruik Windows PowerShell voor StorSimple om de versleutelingssleutelwijziging van servicegegevens te starten.](#to-initiate-the-service-data-encryption-key-change)

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>De versleutelingssleutel voor servicegegevens bijwerken op alle cloudapparaten van 8010/8020
1. Download en setup [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell script. 
2. Open PowerShell en typ bij de opdrachtprompt:`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Dit script zorgt ervoor dat de versleutelingssleutel voor servicegegevens is ingesteld op alle cloudapparaten van 8010/8020 onder de apparaatbeheerder.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Ondersteunde bewerkingen op apparaten waarop versies worden uitgevoerd vóór Update 5.0
In de Azure-portal worden alleen de StorSimple-apparaten met Update 5.0 en hoger ondersteund. De apparaten waarop oudere versies worden uitgevoerd, hebben beperkte ondersteuning. Nadat u naar de Azure-portal bent gemigreerd, gebruikt u de volgende tabel om te begrijpen welke bewerkingen worden ondersteund op apparaten waarop versies worden uitgevoerd vóór Update 5.0.

| Bewerking                                                                                                                       | Ondersteund      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Een apparaat registreren                                                                                                               | Ja            |
| Apparaatinstellingen configureren, zoals algemeen, netwerk en beveiliging                                                                | Ja            |
| Updates scannen, downloaden en installeren                                                                                             | Ja            |
| Apparaat deactiveren                                                                                                               | Ja            |
| Apparaat verwijderen                                                                                                                   | Ja            |
| Een volumecontainer maken, wijzigen en verwijderen                                                                                   | Nee             |
| Een volume maken, wijzigen en verwijderen                                                                                             | Nee             |
| Een back-upbeleid maken, wijzigen en verwijderen                                                                                      | Nee             |
| Neem een handmatige back-up                                                                                                            | Nee             |
| Neem een geplande back-up                                                                                                         | Niet van toepassing |
| Herstellen van een backupset                                                                                                        | Nee             |
| Klonen naar een apparaat met Update 3.0 en hoger <br> Het bronapparaat draait de versie vóór Update 3.0.                                | Ja            |
| Klonen naar een apparaat waarop versies worden uitgevoerd vóór Update 3.0                                                                          | Nee             |
| Failover als bronapparaat <br> (van een apparaat met versie voorafgaand aan Update 3.0 naar een apparaat met update 3.0 en hoger)                                                               | Ja            |
| Failover als doelapparaat <br> (naar een apparaat waarop de softwareversie wordt uitgevoerd vóór Update 3.0)                                                                                   | Nee             |
| Een waarschuwing wissen                                                                                                                  | Ja            |
| Back-upbeleid, back-upcatalogus, volumes, volumecontainers, controlegrafieken, taken en waarschuwingen die in een klassieke portal zijn gemaakt, weergeven | Ja            |
| Apparaatcontrollers in- en uitschakelen                                                                                              | Ja            |


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [Implementatieproces StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Meer informatie over [het beheren van uw StorSimple-opslagaccount](storsimple-8000-manage-storage-accounts.md).
* Meer informatie over het [gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)
