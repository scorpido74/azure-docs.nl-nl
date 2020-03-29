---
title: Azure AD-verificatie voor StorSimple 8000 in Apparaatbeheer
description: Hier wordt uitgelegd hoe u op AAD gebaseerde verificatie voor uw service gebruiken, nieuwe registratiesleutel genereren en handmatige registratie van de apparaten uitvoeren.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b16132c24d35ee2c9902fa2b21c44416d8376b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470901"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Gebruik de nieuwe verificatie voor uw StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht

De StorSimple Device Manager-service wordt uitgevoerd in Microsoft Azure en maakt verbinding met meerdere StorSimple-apparaten. Tot op heden heeft de StorSimple Device Manager-service een Access Control-service (ACS) gebruikt om de service te verifiëren op uw StorSimple-apparaat. Het ACS-mechanisme wordt binnenkort afgeschaft en vervangen door een Azure Active Directory (AAD)-verificatie. Ga voor meer informatie naar de volgende aankondigingen voor ACS-afschrijving en het gebruik van AAD-verificatie.

- [De toekomst van Azure ACS is Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Komende wijzigingen in de Microsoft Access Control Service](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

In dit artikel worden de details van de AAD-verificatie en de bijbehorende nieuwe serviceregistratiesleutel en wijzigingen in de firewallregels beschreven die van toepassing zijn op de StorSimple-apparaten. De informatie in dit artikel is alleen van toepassing op apparaten uit de StorSimple 8000-serie.

De AAD-verificatie vindt plaats in het apparaat uit de StorSimple 8000-serie met Update 5 of hoger. Als gevolg van de invoering van de AAD-authenticatie vinden wijzigingen plaats in:

- URL-patronen voor firewallregels.
- Serviceregistratiesleutel.

Deze wijzigingen worden in de volgende secties in detail besproken.

## <a name="url-changes-for-aad-authentication"></a>URL-wijzigingen voor AAD-verificatie

Om ervoor te zorgen dat de service op AAD gebaseerde verificatie gebruikt, moeten alle gebruikers de nieuwe verificatie-URL's opnemen in hun firewallregels.

Als u storSimple 8000-reeksen gebruikt, moet u ervoor zorgen dat de volgende URL is opgenomen in de firewallregels:

| URL-patroon                         | Cloud | Component/functionaliteit         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Openbare Azure-peering |AAD-verificatieservice      |
| `https://login.microsoftonline.us` | Amerikaanse overheid |AAD-verificatieservice      |

Ga voor een volledige lijst met URL-patronen voor StorSimple 8000-serieapparaten naar [URL-patronen voor firewallregels.](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules)

Als de url van de verificatie niet is opgenomen in de firewallregels na de afschrijvingsdatum en het apparaat Update 5 uitvoert, zien de gebruikers een URL-waarschuwing. De gebruikers moeten de nieuwe verificatie-URL opnemen. Als het apparaat een versie uitvoert voorafgaand aan Update 5, zien de gebruikers een hartslagwaarschuwing. In elk geval kan het StorSimple-apparaat niet verifiëren met de service en kan de service niet met het apparaat communiceren.

## <a name="device-version-and-authentication-changes"></a>Wijzigingen in apparaatversie en verificatie

Als u een apparaat uit de StorSimple 8000-serie gebruikt, gebruikt u de volgende tabel om te bepalen welke actie u moet ondernemen op basis van de apparaatsoftwareversie die u uitvoert.

| Als uw apparaat actief is| De volgende actie ondernemen                                    |
|--------------------------|------------------------|
| Update 5 of hoger en het apparaat is offline. <br> U ziet een waarschuwing dat de URL niet op de witte lijst staat.|1. Wijzig de firewallregels om de url van de verificatie op te nemen. Zie [verificatie-URL's](#url-changes-for-aad-authentication).<br>2. [Haal de AAD-registratiesleutel van de service](#aad-based-registration-keys).<br>3. [Maak verbinding met de Windows PowerShell-interface van het storSimple 8000-serie apparaat](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. `Redo-DeviceRegistration` Gebruik cmdlet om het apparaat te registreren via de Windows PowerShell. Lever de sleutel die je in de vorige stap hebt.|
| Update 5 of hoger en het apparaat online.| Geen actie vereist.                                       |
| Update 4 of eerder en het apparaat is offline. |1. Wijzig de firewallregels om de url van de verificatie op te nemen.<br>2. [Download Update 5 via de catalogusserver](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Update 5 toepassen via de hotfix-methode](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Haal de AAD-registratiesleutel van de service](#aad-based-registration-keys).<br>5. [Maak verbinding met de Windows PowerShell-interface van het storSimple 8000-serie apparaat](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. `Redo-DeviceRegistration` Gebruik cmdlet om het apparaat te registreren via de Windows PowerShell. Lever de sleutel die je in de vorige stap hebt.|
| Update 4 of eerder en het apparaat is online. |Wijzig de firewallregels om de url van de verificatie op te nemen.<br> Installeer Update 5 via de Azure-portal.              |
| Factory opnieuw ingesteld op een versie vóór Update 5.      |De portal toont een op AAD gebaseerde registratiesleutel terwijl het apparaat oudere software uitvoert. Volg de stappen in het vorige scenario voor wanneer het apparaat Update 4 of eerder uitvoert.              |

## <a name="aad-based-registration-keys"></a>Aad-gebaseerde registratiesleutels

Begin update 5 voor StorSimple 8000-serie apparaten, nieuwe AAD-gebaseerde registratiesleutels worden gebruikt. U gebruikt de registratiesleutels om uw StorSimple Device Manager-service met het apparaat te registreren.

U de nieuwe AAD-serviceregistratiesleutels niet gebruiken als u een apparaat uit de StorSimple 8000-serie gebruikt met Update 4 of eerder (inclusief een ouder apparaat dat nu wordt geactiveerd).
In dit scenario moet u de serviceregistratiesleutel opnieuw genereren. Zodra u de sleutel regenereert, wordt de nieuwe sleutel gebruikt voor het registreren van alle volgende apparaten. De oude sleutel is niet langer geldig.

- De nieuwe AAD-registratiesleutel vervalt na 3 dagen.
- De AAD-registratiesleutels werken alleen met apparaten uit de StorSimple 8000-serie met Update 5 of hoger.
- De AAD-registratiesleutels zijn langer dan de bijbehorende ACS-registratiesleutels.

Voer de volgende stappen uit om een AAD-serviceregistratiesleutel te genereren.

#### <a name="to-generate-the-aad-service-registration-key"></a>De AAD-serviceregistratiesleutel genereren

1. Ga **in StorSimple Device Manager**naar ** &gt; Management** **Keys.** U ook de zoekbalk gebruiken om naar _sleutels_te zoeken.
    
2. Klik **op Toets genereren**.

    ![Klik op regenereren](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopieer de nieuwe sleutel. De oudere sleutel werkt niet meer.

    ![Regenereren bevestigen](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Als u een StorSimple Cloud Appliance maakt op de service die is geregistreerd op uw StorSimple 8000-serieapparaat, genereert u geen registratiesleutel terwijl de creatie aan de gang is. Wacht tot de creatie is voltooid en genereer vervolgens de registratiesleutel.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het implementeren van [storSimple 8000-serie apparaten](storsimple-8000-deployment-walkthrough-u2.md).

