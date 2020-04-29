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
ms.openlocfilehash: a9e8574ea2d7222871c7f065383e6c0c62057dd3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81007820"
---
## <a name="generate-the-certificate-signing-request-file"></a>Het bestand met de aanvraag voor certificaat ondertekening genereren

De Apple Push Notification Service (APNs) gebruikt certificaten voor het verifiëren van uw push meldingen. Volg deze instructies om het pushcertificaat te maken dat vereist is om meldingen te verzenden en te ontvangen. Zie de officiële documentatie van de [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) voor meer informatie over deze concepten.

Genereer het CSR-bestand (Certificate Signing Request) dat door Apple wordt gebruikt voor het genereren van een ondertekend push certificaat.

1. Voer op uw Mac het hulpprogramma Sleutelhangertoegang uit. Het kan worden geopend vanuit de map **Utilities** of de **andere** map op het launchpad.

1. Selecteer **toegang tot sleutel hanger**, vouw de **certificaat assistent**uit en selecteer vervolgens **een certificaat aanvragen bij een certificerings instantie**.

    ![Toegang tot sleutelhanger gebruiken om een nieuw certificaat aan te vragen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Sleutel hanger toegang selecteert standaard het eerste item in de lijst. Dit kan een probleem zijn als u zich in de categorie **certificaten** bevindt en de **certificerings instantie van de Apple Worldwide Developer Relations** niet het eerste item in de lijst is. Zorg ervoor dat u een niet-sleutel item hebt of dat de **certificerings instantie sleutel voor Apple Worldwide Developer Relations** is geselecteerd, voordat u de CSR (aanvraag voor certificaat ondertekening) genereert.

1. Selecteer het **e-mail adres**van uw gebruiker, voer uw **algemene naam** waarde in, zorg ervoor dat u **op schijf opgeslagen**opgeeft en selecteer **door gaan**. Geef het **e-mail adres** van de CA leeg, omdat dit niet vereist is.

    ![Vereiste certificaatgegevens](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Voer een naam in voor het CSR-bestand in **Opslaan als**, selecteer de locatie in **waar**en selecteer vervolgens **Opslaan**.

    ![Een bestands naam voor het certificaat kiezen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Met deze actie wordt het CSR-bestand opgeslagen op de geselecteerde locatie. De standaard locatie is **bureau blad**. Onthoud de locatie die u voor het bestand hebt gekozen.

Registreer vervolgens uw app met Apple, schakel push meldingen in en upload de geëxporteerde CSR om een push certificaat te maken.

## <a name="register-your-app-for-push-notifications"></a>Uw app voor pushmeldingen registreren

Als u push meldingen naar een iOS-app wilt verzenden, registreert u uw toepassing met Apple en registreert u ook voor push meldingen.  

1. Als u uw app nog niet hebt geregistreerd, bladert u naar de [IOS-inrichtings Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) in het Apple Developer Center. Meld u aan bij de portal met uw Apple-ID en selecteer **id's**. Selecteer **+** vervolgens om een nieuwe app te registreren.

    ![App-id-pagina van iOS-inrichtingsportal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Selecteer op het scherm **een nieuwe id registreren** het keuze rondje **app-id's** . Selecteer vervolgens **Doorgaan**.

    ![nieuwe ID-pagina registreren voor iOS-inrichtings Portal](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Werk de volgende drie waarden bij voor uw nieuwe app en selecteer vervolgens **door gaan**:

   * **Beschrijving**: Typ een beschrijvende naam voor uw app.

   * **Bundel-id**: Voer een bundel-id in van de formulier organisatie-ID **. product naam** zoals vermeld in de [hand leiding voor app-distributie](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). De waarden voor de *organisatie-id* en *product naam* moeten overeenkomen met de organisatie-ID en product naam die u gebruikt wanneer u uw Xcode-project maakt. In de volgende scherm afbeelding wordt de waarde **Notification hubs** gebruikt als organisatie-ID en wordt de waarde **GetStarted** gebruikt als de product naam. Zorg ervoor dat de waarde van de **bundel-id** overeenkomt met de waarde in uw Xcode-project, zodat Xcode het juiste publicatie profiel gebruikt.

      ![iOS-inrichtings Portal-App-ID pagina registreren](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Push meldingen**: Controleer de optie **Push meldingen** in het gedeelte **mogelijkheden** .

      ![Formulier voor het registreren van een nieuwe app-id](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Met deze actie wordt uw app-ID gegenereerd en wordt u gevraagd de gegevens te bevestigen. Selecteer **door gaan**en selecteer vervolgens **registreren** om de nieuwe app-id te bevestigen.

      ![Nieuwe app-ID bevestigen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Nadat u **registreren**hebt geselecteerd, ziet u de nieuwe app-id als een regel item op de pagina **certificaten, Id's &-profielen** .

4. Zoek op de pagina **certificaten, id's & profielen** , onder **id's**, het regel item app-id dat u zojuist hebt gemaakt en selecteer de rij om het scherm **uw app-id bewerken** weer te geven.

## <a name="creating-a-certificate-for-notification-hubs"></a>Een certificaat maken voor Notification Hubs
Er is een certificaat vereist om de notification hub in te scha kelen voor gebruik met **APNS**. Dit kan op een van de volgende twee manieren worden gedaan:

1. Maak een **. p12** die rechtstreeks naar de notification hub kan worden geüpload.  
2. Maak een **. P8** die kan worden gebruikt voor [verificatie op basis van tokens](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification) (*de nieuwere benadering*).

De nieuwere benadering heeft een aantal voor delen (vergeleken met het gebruik van certificaten) zoals beschreven in [verificatie op basis van tokens (http/2) voor APNS](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). Er zijn echter stappen voor beide benaderingen. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>OPTIE 1: een. p12-push certificaat maken dat rechtstreeks naar de notification hub kan worden geüpload

1. Schuif omlaag naar de optie gecontroleerde **Push meldingen** en selecteer **configureren** om het certificaat te maken.

    ![Pagina app-id bewerken](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. Het venster SSL-certificaten voor de **Apple Push Notification-Service** wordt weer gegeven. Selecteer de knop **certificaat maken** in het gedeelte **SSL-certificaat voor ontwikkeling** .

    ![Certificaat voor app-id-knop maken](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Het scherm **een nieuw certificaat maken** wordt weer gegeven.

    > [!NOTE]
    > In deze zelfstudie wordt een ontwikkelingscertificaat gebruikt. Hetzelfde proces wordt gebruikt bij het registreren van een productiecertificaat. Zorg er wel voor dat u hetzelfde certificaattype gebruikt als u meldingen verzendt.

3. Selecteer **bestand kiezen**, blader naar de locatie waar u het CSR-bestand van de eerste taak hebt opgeslagen en dubbel klik vervolgens op de naam van het certificaat om het te laden. Selecteer vervolgens **Doorgaan**.

4. Nadat het certificaat is gemaakt door de portal, selecteert u de knop **downloaden** . Sla het certificaat op en onthoud de locatie waar het wordt opgeslagen.

    ![Download-pagina voor gemaakte certificaat](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Het certificaat wordt gedownload en opgeslagen op uw computer in de map **down loads** .

    ![Certificaatbestand zoeken in de map Downloads](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Het gedownloade ontwikkel certificaat heet standaard **aps_development. CER**.

5. Dubbelklik op het gedownloade pushcertificaat **aps_development.cer**. Nu wordt het nieuwe certificaat in de sleutelhanger geïnstalleerd, zoals op de volgende afbeelding wordt weergegeven:

    ![Certificatenlijst in Sleutelhangertoegang geeft nieuw certificaat weer](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Hoewel de naam in uw certificaat mogelijk anders is, wordt de naam voorafgegaan door **Apple Development IOS Push Services**.

6. In Sleutelhangertoegang klikt u met de rechtermuisknop op het nieuwe pushcertificaat dat u hebt gemaakt in de categorie **Certificaten**. Selecteer **exporteren**, geef het bestand een naam, selecteer de **. p12** -indeling en selecteer vervolgens **Opslaan**.

    ![Certificaat exporteren in p12-indeling](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    U kunt ervoor kiezen om het certificaat met een wacht woord te beveiligen, maar dit is optioneel. Klik op **OK** als u het maken van wacht woorden wilt overs Laan. Noteer de bestandsnaam en locatie van het geëxporteerde .p12-certificaat. Ze worden gebruikt om verificatie met APNs in te scha kelen.

    > [!NOTE]
    > De naam en locatie van uw. P12-bestand kunnen afwijken van wat er in deze zelf studie wordt afgebeeld.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>OPTIE 2: een. P8-certificaat maken dat kan worden gebruikt voor verificatie op basis van tokens

1. Noteer de volgende gegevens:

    - **App-ID-voor voegsel** (dit is een **Team-ID**)
    - **Bundel-id**
    
2. Terug in **certificaten, id's & profielen**, klikt u op **sleutels**.

   > [!NOTE]
   > Als u al een sleutel voor **APNS**hebt geconfigureerd, kunt u het. P8-certificaat dat u hebt gedownload, opnieuw gebruiken nadat het is gemaakt. Als dat het geval is, kunt u stap **3** tot en met **5**negeren.

3. Klik op **+** de knop (of op de knop **een sleutel maken** ) om een nieuwe sleutel te maken.
4. Geef een geschikte **sleutel naam** op en controleer vervolgens de optie **Apple Push Notification Service (APNs)** en klik vervolgens op **door gaan**, gevolgd door **registreren** op het volgende scherm.
5. Klik op **downloaden** en verplaats het **. P8** -bestand (voorafgegaan door *AuthKey_*) naar een beveiligde lokale map en klik vervolgens op **gereed**.

   > [!NOTE] 
   > Zorg ervoor dat u uw. P8-bestand op een veilige plaats blijft (en sla een back-up op). Nadat de sleutel is gedownload, kan deze niet opnieuw worden gedownload omdat de server kopie wordt verwijderd.
  
6. Op **sleutels**, klikt u op de sleutel die u zojuist hebt gemaakt (of een bestaande sleutel als u ervoor hebt gekozen om deze te gebruiken).
7. Noteer de waarde van de **sleutel-id** .
8. Open uw. P8-certificaat in een geschikte toepassing van uw keuze zoals [**Visual Studio code**](https://code.visualstudio.com) en noteer de sleutel waarde. Dit is de waarde tussen **-----begin persoonlijke sleutel-----** en **-----laatste persoonlijke sleutel-----** .

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Dit is de **token waarde** die later wordt gebruikt om **Notification hub**te configureren. 

Aan het einde van deze stappen moet u de volgende informatie hebben om later te kunnen gebruiken in [uw notification hub configureren met APNs-gegevens](#configure-your-notification-hub-with-apns-information):

- **Team-ID** (zie stap 1)
- **Bundel-id** (zie stap 1)
- **Sleutel-id** (zie stap 7)
- **Token waarde** , bijvoorbeeld de. P8-sleutel waarde (zie stap 8)

## <a name="create-a-provisioning-profile-for-the-app"></a>Een inrichtingsprofiel voor de app maken

1. Ga terug naar de [IOS-inrichtings Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecteer **certificaten, id's & profielen**, selecteer **profielen** in het menu links en selecteer **+** vervolgens om een nieuw profiel te maken. Het scherm **een nieuw inrichtings profiel registreren** wordt weer gegeven.

1. Selecteer **ontwikkeling van Ios-apps** onder **ontwikkeling** als het type van het inrichtings profiel en selecteer vervolgens **door gaan**.

    ![Lijst met inrichtingsprofielen](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Selecteer vervolgens de App-ID die u hebt gemaakt in de vervolg keuzelijst **App-ID** en selecteer **door gaan**.

    ![Selecteer de app-id](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Selecteer in het venster **certificaten selecteren** het ontwikkelings certificaat dat u gebruikt voor het ondertekenen van programma code en selecteer **door gaan**. Dit certificaat is niet het push certificaat dat u hebt gemaakt. Als er nog geen bestaat, moet u deze maken. Als er een certificaat bestaat, gaat u verder met de volgende stap. Een ontwikkelings certificaat maken als er geen bestaat:

    1. Als **er geen certificaten beschikbaar zijn**, selecteert u **certificaat maken**.
    2. Selecteer in de sectie **Software** de optie **Apple Development**. Selecteer vervolgens **Doorgaan**.
    3. Selecteer in het scherm **een nieuw certificaat maken** de optie **bestand kiezen**.
    4. Blader naar het certificaat voor **certificaat handtekening aanvragen** dat u eerder hebt gemaakt, selecteer dit en selecteer vervolgens **openen**.
    5. Selecteer **Doorgaan**.
    6. Down load het ontwikkel certificaat en onthoud de locatie waar het wordt opgeslagen.

1. Ga terug naar de pagina **certificaten, id's & profielen** , selecteer **profielen** in het menu links en selecteer **+** vervolgens om een nieuw profiel te maken. Het scherm **een nieuw inrichtings profiel registreren** wordt weer gegeven.

1. Selecteer in het venster **certificaten selecteren** het ontwikkelings certificaat dat u zojuist hebt gemaakt. Selecteer vervolgens **Doorgaan**.

1. Selecteer vervolgens de apparaten die u voor het testen wilt gebruiken en selecteer **door gaan**.

1. Kies ten slotte een naam voor het profiel in **inrichtings profiel naam**en selecteer **genereren**.

    ![Een naam kiezen voor het inrichtingsprofiel](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Wanneer het nieuwe inrichtings profiel is gemaakt, selecteert u **downloaden**. Onthoud de locatie waar het bestand is opgeslagen.

1. Blader naar de locatie van het inrichtings profiel en dubbel klik erop om het te installeren op uw Xcode-ontwikkel machine.

## <a name="create-a-notification-hub"></a>Een Notification Hub maken

In deze sectie maakt u een notification hub en configureert u verificatie met APNs met behulp van het. p12-push certificaat of de verificatie op basis van tokens. Als u een notification hub wilt gebruiken die u al hebt gemaakt, kunt u door gaan naar stap 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Uw notification hub configureren met APNs-gegevens

Onder **Notification Services**selecteert u **Apple (APNS)** en volgt u de juiste stappen op basis van de aanpak die u eerder hebt gekozen in de sectie [een certificaat maken voor notification hubs](#creating-a-certificate-for-notification-hubs) .  

> [!NOTE]
> Gebruik de modus **productie** voor **toepassing** alleen als u push meldingen wilt verzenden naar gebruikers die uw app uit de Store hebben gekocht.

### <a name="option-1-using-a-p12-push-certificate"></a>OPTIE 1: een. p12-push certificaat gebruiken

1. Selecteer **Certificaat**.

1. Selecteer het bestandspictogram.

1. Selecteer het. P12-bestand dat u eerder hebt geëxporteerd en selecteer vervolgens **openen**.

1. Geef, indien nodig, het juiste wacht woord op.

1. Selecteer de modus **Sandbox**.

    ![APNs-certificering in Azure Portal configureren](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Selecteer **Opslaan**.

### <a name="option-2-using-token-based-authentication"></a>OPTIE 2: verificatie op basis van tokens

1. Selecteer **token**.
1. Voer de volgende waarden in die u eerder hebt aangeschaft:

    - **Sleutel-ID**
    - **Bundel-id**
    - **Team-ID**
    - **Token** 

1. **Sandbox** kiezen
1. Selecteer **Opslaan**. 

U hebt nu uw notification hub geconfigureerd met APNs. U hebt ook de verbindings reeksen om uw app te registreren en push meldingen te verzenden.
