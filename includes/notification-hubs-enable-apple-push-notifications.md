---
title: bestand opnemen
description: bestand opnemen
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bf2596f5a8e287799285f97f3d1be9f3fe10f644
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77123134"
---
## <a name="generate-the-certificate-signing-request-file"></a>Het aanvraagbestand voor certificaatondertekening genereren

De Apple Push Notification Service (APNs) gebruikt certificaten om uw pushmeldingen te verifiëren. Volg deze instructies om het pushcertificaat te maken dat vereist is om meldingen te verzenden en te ontvangen. Zie de officiële documentatie van de [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) voor meer informatie over deze concepten.

Het CSR-bestand (Certificate Signing Request) genereren, dat Apple gebruikt om een ondertekend pushcertificaat te genereren.

1. Voer op uw Mac het hulpprogramma Sleutelhangertoegang uit. Het kan worden geopend vanuit de map **Hulpprogramma's** of de **andere** map op het Launchpad.

1. Selecteer **Sleutelhangertoegang,** vouw **certificaatassistent**uit en selecteer **Vervolgens Een certificaat aanvragen bij een certificaatautoriteit**.

    ![Toegang tot sleutelhanger gebruiken om een nieuw certificaat aan te vragen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Keychain Access selecteert standaard het eerste item in de lijst. Dit kan een probleem zijn als u zich in de categorie **Certificaten** bevindt en **apple Worldwide Developer Relations Certification Authority** niet het eerste item in de lijst is. Zorg ervoor dat u een niet-sleutelitem hebt of dat de apple **worldwide developer relations-certificeringsinstantie** is geselecteerd, voordat u de CSR (Certificate Signing Request) genereert.

1. Selecteer uw **gebruikerse-mailadres,** voer de waarde **van de algemene naam** in, zorg ervoor dat u Opgeslagen op **schijf**opgeeft en selecteer **Doorgaan**. Laat **het CA-e-mailadres** leeg staan omdat dit niet nodig is.

    ![Vereiste certificaatgegevens](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Voer een naam in voor het CSR-bestand in **Opslaan als,** selecteer de locatie in **Waar**en selecteer **Opslaan**.

    ![Een bestandsnaam voor het certificaat kiezen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Met deze actie slaat u het MVO-bestand op de geselecteerde locatie op. De standaardlocatie is **Desktop**. Onthoud de locatie die u voor het bestand hebt gekozen.

Registreer vervolgens uw app bij Apple, schakel pushmeldingen in en upload de geëxporteerde CSR om een pushcertificaat te maken.

## <a name="register-your-app-for-push-notifications"></a>Uw app voor pushmeldingen registreren

Als u pushmeldingen naar een iOS-app wilt verzenden, registreert u uw toepassing bij Apple en registreert u zich ook voor pushmeldingen.  

1. Als u uw app nog niet hebt geregistreerd, bladert u naar de [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) in het Apple Developer Center. Meld u aan bij de portal met uw Apple ID en selecteer **Id.Sign**in to the portal with your Apple ID, and select Identifiers . Selecteer **+** vervolgens om een nieuwe app te registreren.

    ![App-id-pagina van iOS-inrichtingsportal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Selecteer **in het** scherm Een nieuwe id registreren de **keuzerondje app-id's.** Selecteer vervolgens **Doorgaan**.

    ![IOS Provisioning Portal registreert nieuwe ID-pagina](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Werk de volgende drie waarden voor uw nieuwe app bij en selecteer **Doorgaan:**

   * **Beschrijving**: Typ een beschrijvende naam voor uw app.

   * **Bundel-id:** voer een bundel-id in van het formulier **Organisatie Identifier.Product name** zoals vermeld in de [App Distribution Guide](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). De *waarden organisatie-id* en *productnaam* moeten overeenkomen met de organisatie-id en productnaam die u gebruikt bij het maken van uw Xcode-project. In de volgende schermafbeelding wordt de waarde **NotificationHubs** gebruikt als organisatie-id en wordt de **waarde GetStarted** gebruikt als productnaam. Zorg ervoor dat de **bundel-id-waarde** overeenkomt met de waarde in uw Xcode-project, zodat Xcode het juiste publicatieprofiel gebruikt.

      ![PAGINA iOS Provisioning Portal register app ID-pagina](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Pushmeldingen:** schakel de optie **Pushmeldingen** in de **sectie Mogelijkheden in.**

      ![Formulier voor het registreren van een nieuwe app-id](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Met deze actie genereert u uw app-id en wordt u verzocht de informatie te bevestigen. Selecteer **Doorgaan**en selecteer **Vervolgens Registreren** om de nieuwe App-id te bevestigen.

      ![Nieuwe App-id bevestigen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Nadat u **Register**hebt geselecteerd, ziet u de nieuwe App-id als regelitem op de pagina **Certificaten, Id's & profielen.**

4. Zoek in de pagina **Certificaten, Id-& profielen** onder **Id,,** het app-id-regelitem dat u zojuist hebt gemaakt en selecteer de rij om het scherm **Configuratie van uw app-id-configuratie** weer te geven.

5. Schuif omlaag naar de optie Getoetste **pushmeldingen** en selecteer **Configureren** om het certificaat te maken.

    ![Pagina app-id bewerken](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. Het venster **SSL-certificaten van de Apple Push Notification-service** wordt weergegeven. Selecteer de knop **Certificaat maken** onder de sectie **Ontwikkel SSL-certificaat.**

    ![Certificaat voor app-id-knop maken](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Het scherm **Een nieuw certificaat maken** wordt weergegeven.

    > [!NOTE]
    > In deze zelfstudie wordt een ontwikkelingscertificaat gebruikt. Hetzelfde proces wordt gebruikt bij het registreren van een productiecertificaat. Zorg er wel voor dat u hetzelfde certificaattype gebruikt als u meldingen verzendt.

1. Selecteer **Bestand kiezen,** blader naar de locatie waar u het CSR-bestand van de eerste taak hebt opgeslagen en dubbelklik op de certificaatnaam om het te laden. Selecteer vervolgens **Doorgaan**.

1. Nadat de portal het certificaat hebt gemaakt, selecteert u de knop **Downloaden.** Sla het certificaat op en onthoud de locatie waarop het is opgeslagen.

    ![Download-pagina voor gemaakte certificaat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Het certificaat wordt gedownload en opgeslagen op uw computer in de map **Downloads.**

    ![Certificaatbestand zoeken in de map Downloads](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Standaard heeft het gedownloade ontwikkelingscertificaat de naam **aps_development.cer**.

1. Dubbelklik op het gedownloade pushcertificaat **aps_development.cer**. Nu wordt het nieuwe certificaat in de sleutelhanger geïnstalleerd, zoals op de volgende afbeelding wordt weergegeven:

    ![Certificatenlijst in Sleutelhangertoegang geeft nieuw certificaat weer](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Hoewel de naam in uw certificaat mogelijk anders is, wordt de naam vooraf vastgelegd met **Apple Development iOS Push Services.**

1. In Sleutelhangertoegang klikt u met de rechtermuisknop op het nieuwe pushcertificaat dat u hebt gemaakt in de categorie **Certificaten**. Selecteer **Exporteren,** geef het bestand een naam, selecteer de **.p12-notatie** en selecteer **Opslaan**.

    ![Certificaat exporteren in p12-indeling](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    U ervoor kiezen om het certificaat te beveiligen met een wachtwoord, maar dit is optioneel. Klik op **OK** als u het maken van wachtwoorden wilt omzeilen. Noteer de bestandsnaam en locatie van het geëxporteerde .p12-certificaat. Ze worden gebruikt om verificatie met APNs in te schakelen.

    > [!NOTE]
    > Uw .p12-bestandsnaam en -locatie kunnen anders zijn dan wat in deze zelfstudie wordt afgebeeld.

## <a name="create-a-provisioning-profile-for-the-app"></a>Een inrichtingsprofiel voor de app maken

1. Ga terug naar de [iOS Provisioning Portal,](https://go.microsoft.com/fwlink/p/?LinkId=272456)selecteer **Certificaten, id's & profielen,** selecteer **Profielen** in het linkermenu en selecteer **+** vervolgens om een nieuw profiel te maken. Het scherm **Een nieuw inrichtingsprofiel registreren** wordt weergegeven.

1. Selecteer **iOS App Development** onder **Ontwikkeling** als het type inrichtingsprofiel en selecteer **Doorgaan**.

    ![Lijst met inrichtingsprofielen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Selecteer vervolgens de app-id die u hebt gemaakt in de vervolgkeuzelijst **App-id** en selecteer **Doorgaan**.

    ![Selecteer de app-id](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Selecteer **in** het venster Certificaten selecteren het ontwikkelingscertificaat dat u gebruikt voor het ondertekenen van code en selecteer **Doorgaan**. Dit certificaat is niet het pushcertificaat dat u hebt gemaakt. Als men niet bestaat, moet u het maken. Als er een certificaat bestaat, gaat u naar de volgende stap. Ga als u een ontwikkelingscertificaat maakt als er geen bestaat:

    1. Als er **geen certificaten beschikbaar zijn,** selecteert u Certificaat **maken**.
    2. Selecteer **apple development**in de sectie **Software** . Selecteer vervolgens **Doorgaan**.
    3. Selecteer **Bestand kiezen**in het scherm Een nieuw **certificaat maken** .
    4. Blader naar het certificaat **Certificaatondertekeningaanvragen** dat u eerder hebt gemaakt, selecteer het certificaat en selecteer **Openen**.
    5. Selecteer **Doorgaan**.
    6. Download het ontwikkelingscertificaat en onthoud de locatie waar het is opgeslagen.

1. Ga terug naar de pagina **Certificaten, Id's & profielen,** selecteer **Profielen** in het linkermenu en selecteer **+** vervolgens om een nieuw profiel te maken. Het scherm **Een nieuw inrichtingsprofiel registreren** wordt weergegeven.

1. Selecteer **in** het venster Certificaten selecteren het ontwikkelcertificaat dat u zojuist hebt gemaakt. Selecteer vervolgens **Doorgaan**.

1. Selecteer vervolgens de apparaten die u wilt gebruiken voor het testen en selecteer **Doorgaan**.

1. Kies ten slotte een naam voor het profiel in **Inrichtingsprofielnaam**en selecteer **Genereren**.

    ![Een naam kiezen voor het inrichtingsprofiel](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Wanneer het nieuwe inrichtingsprofiel is gemaakt, selecteert u **Downloaden**. Denk aan de locatie waar het is opgeslagen.

1. Blader naar de locatie van het inrichtingsprofiel en dubbelklik erop om het op uw Xcode-ontwikkelingsmachine te installeren.

## <a name="create-a-notification-hub"></a>Een Notification Hub maken

In deze sectie maakt u een meldingshub en configureert u verificatie met APN's met behulp van het .p12-pushcertificaat dat u eerder hebt gemaakt. Als u een meldingshub wilt gebruiken die u al hebt gemaakt, u doorgaan naar stap 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Uw meldingshub configureren met APN-gegevens

1. Selecteer **Apple (APNS)** onder **Meldingsservices**.

1. Selecteer **Certificaat**.

1. Selecteer het bestandspictogram.

1. Selecteer het .p12-bestand dat u eerder hebt geëxporteerd en selecteer **Openen**.

1. Geef indien nodig het juiste wachtwoord op.

1. Selecteer de modus **Sandbox**. Gebruik de modus **Productie** alleen als u pushmeldingen wilt verzenden naar gebruikers die uw app in de Store hebben gekocht.

    ![APNs-certificering in Azure Portal configureren](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Selecteer **Opslaan**.

U hebt nu uw meldingshub geconfigureerd met APN's. Je hebt ook de verbindingstekenreeksen om je app te registreren en pushmeldingen te verzenden.
