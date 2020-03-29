---
title: Nieuwe verificatie voor StorSimple Virtual Arrays
description: Hier wordt uitgelegd hoe u op AAD gebaseerde verificatie voor uw service gebruiken, nieuwe registratiesleutel genereren en handmatige registratie van de apparaten uitvoeren.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 89f367e866c1a794f4359c76b8b8a8a9cfefd50d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273813"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Gebruik de nieuwe verificatie voor uw StorSimple

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

De StorSimple Device Manager-service wordt uitgevoerd in Microsoft Azure en maakt verbinding met meerdere StorSimple Virtual Arrays. Tot op heden heeft de StorSimple Device Manager-service een Access Control-service (ACS) gebruikt om de service te verifiëren op uw StorSimple-apparaat. Het ACS-mechanisme wordt binnenkort afgeschaft en vervangen door een Azure Active Directory (AAD)-verificatie.

De informatie in dit artikel is alleen van toepassing op beide StorSimple 1200 Series Virtual Arrays. In dit artikel worden de details van de AAD-verificatie en de bijbehorende nieuwe serviceregistratiesleutel en wijzigingen in de firewallregels beschreven die van toepassing zijn op de StorSimple-apparaten.

De AAD-verificatie vindt plaats in StorSimple Virtual Arrays (model 1200) met Update 1 of hoger.

Als gevolg van de invoering van de AAD-authenticatie vinden wijzigingen plaats in:

- URL-patronen voor firewallregels.
- Serviceregistratiesleutel.

Deze wijzigingen worden in de volgende secties in detail besproken.

## <a name="url-changes-for-aad-authentication"></a>URL-wijzigingen voor AAD-verificatie

Om ervoor te zorgen dat de service op AAD gebaseerde verificatie gebruikt, moeten alle gebruikers de nieuwe verificatie-URL's opnemen in hun firewallregels.

Als u StorSimple Virtual Array gebruikt, moet u ervoor zorgen dat de volgende URL is opgenomen in de firewallregels:

| URL-patroon                         | Cloud | Component/functionaliteit         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Openbare Azure-peering |AAD-verificatieservice      |
| `https://login.microsoftonline.us` | Amerikaanse overheid |AAD-verificatieservice      |

Ga voor een volledige lijst met URL-patronen voor StorSimple Virtual Arrays naar [URL-patronen voor firewallregels.](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules)

Als de verificatie-URL niet is opgenomen in de firewallregels na de afschrijvingsdatum, zien de gebruikers een kritieke waarschuwing dat hun StorSimple-apparaat zich niet kan verifiëren met de service. De service kan niet met het apparaat communiceren. Als de gebruikers deze waarschuwing zien, moeten ze de nieuwe verificatie-URL toevoegen. Ga voor meer informatie over de waarschuwing naar [Waarschuwingen gebruiken om uw StorSimple-apparaat te controleren.](storsimple-virtual-array-manage-alerts.md#networking-alerts)

## <a name="device-version-and-authentication-changes"></a>Wijzigingen in apparaatversie en verificatie

Als u een Virtuele Array van StorSimple gebruikt, gebruikt u de volgende tabel om te bepalen welke actie u moet ondernemen op basis van de apparaatsoftwareversie die u uitvoert.

| Als uw apparaat actief is  | De volgende actie ondernemen                                    |
|----------------------------|--------------------------------------------------------------|
| Update 1.0 of hoger en is offline. <br> U ziet een waarschuwing dat de URL niet op de witte lijst staat.| 1. Wijzig de firewallregels om de url van de verificatie op te nemen. Zie [verificatie-URL's](#url-changes-for-aad-authentication). <br> 2. [Haal de AAD-registratiesleutel van de service](#aad-based-registration-keys). <br> 3. Stap 1-5 uitvoeren om [verbinding te maken met de Windows PowerShell-interface van de virtuele array.](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor)<br> 4. `Invoke-HcsReRegister` Gebruik cmdlet om het apparaat te registreren via de Windows PowerShell. Lever de sleutel die je in de vorige stap hebt.|
| Update 1.0 of hoger en het apparaat is online.| Geen actie vereist.                                       |
| Update 0.6 of eerder en het apparaat is offline. | 1. [Download Update 1.0 via de catalogusserver](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Update 1.0 toepassen via de lokale webgebruikersinterface](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Haal de AAD-registratiesleutel van de service](#aad-based-registration-keys). <br>4. Stap 1-5 uitvoeren om [verbinding te maken met de Windows PowerShell-interface van de virtuele array.](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor)<br>5. `Invoke-HcsReRegister` Gebruik cmdlet om het apparaat te registreren via de Windows PowerShell. Lever de sleutel die je in de vorige stap hebt.|
| Update 0.6 of eerder en het apparaat is online | Wijzig de firewallregels om de url van de verificatie op te nemen.<br> Installeer Update 1.0 via de Azure-portal. |

## <a name="aad-based-registration-keys"></a>Aad-gebaseerde registratiesleutels

Beginupdate 1.0 voor StorSimple Virtual Arrays worden nieuwe AAD-gebaseerde registratiesleutels gebruikt. U gebruikt de registratiesleutels om uw StorSimple Device Manager-service met het apparaat te registreren.

U de nieuwe AAD-serviceregistratiesleutels niet gebruiken als u een StorSimple Virtual Arrays gebruikt met Update 0.6 of eerder. U moet de serviceregistratiesleutel regenereren. Zodra u de sleutel regenereert, wordt de nieuwe sleutel gebruikt voor het registreren van alle volgende apparaten. De oude sleutel is niet langer geldig.

- De nieuwe AAD-registratiesleutel vervalt na 3 dagen.
- De AAD-registratiesleutels werken alleen met virtuele arrays uit de StorSimple 1200-serie met Update 1 of hoger. De AAD-registratiesleutel van een StorSimple 8000-serie apparaat werkt niet.
- De AAD-registratiesleutels zijn langer dan de bijbehorende ACS-registratiesleutels.

Voer de volgende stappen uit om een AAD-serviceregistratiesleutel te genereren.

#### <a name="to-generate-the-aad-service-registration-key"></a>De AAD-serviceregistratiesleutel genereren

1. Ga **in StorSimple Device Manager**naar ** &gt; Management** **Keys.**
    
    ![Ga naar Toetsen](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Klik **op Toets genereren**.

    ![Klik op regenereren](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopieer de nieuwe sleutel. De oudere sleutel werkt niet meer.

    ![Regenereren bevestigen](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het implementeren van [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
