---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72065ce602c6d29255a3500e26d8c6f36a19ebed
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095311"
---
### <a name="register-your-ios-app-for-push-notifications"></a>Uw iOS-app voor pushmeldingen registreren

Registreer uw app bij Apple en registreer u voor pushmeldingen om pushmeldingen te verzenden naar een iOS-app.  

1. Als u uw app nog niet hebt geregistreerd, bladert u naar de [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) in het Apple Developer Center. Meld u aan bij de portal met uw Apple-id, navigeer naar **Certificaten, id's en profielen**en selecteer vervolgens **Id's**. Klik op **+** voor het registreren van een nieuwe app.

    ![App-id-pagina van iOS-inrichtingsportal](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Selecteer op het scherm **Een nieuwe id registreren** het keuzerondje **App-id's**. Selecteer vervolgens **Doorgaan**.

    ![Nieuwe id-pagina registreren voor iOS Provisioning Portal](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

1. Werk de volgende drie waarden voor uw nieuwe app bij en selecteer vervolgens **Doorgaan**:

   * **Beschrijving**: Typ een beschrijvende naam voor uw app.

   * **Bundel-id**: Voer een bundel-id in van het formulier **com.<organization_identifier>.<product_name>** zoals genoemd in de [App Distribution Guide](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). In de volgende schermopname wordt de waarde `mobcat` gebruikt als de organisatie-id en de waarde **PushDemo** als de productnaam.

      ![App-id-pagina registreren voor iOS Provisioning Portal](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Pushmeldingen**: Controleer de optie **Pushmeldingen** in het gedeelte **Mogelijkheden**.

      ![Formulier voor het registreren van een nieuwe app-id](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Met deze actie wordt uw app-id gegenereerd en wordt u gevraagd om de gegevens te bevestigen. Selecteer **Doorgaan** en selecteer vervolgens **Registreren** om de nieuwe app-id te bevestigen.

      ![Nieuwe app-id bevestigen](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Nadat u **Registreren** hebt geselecteerd, ziet u de nieuwe app-id als een regelitem in de pagina **Certificaten, id's en profielen**.

1. Ga in de pagina **Certificaten, Id's & profielen** onder **Id's**naar het regelitem app-id dat u hebt gemaakt. Selecteer vervolgens de rij voor het weergeven van het scherm **De configuratie van uw app-id bewerken**.

#### <a name="creating-a-certificate-for-notification-hubs"></a>Een certificaat maken voor Notification Hubs

Er is een certificaat vereist om de Notification Hub in te schakelen om **Apple Push Notification Services (APNS)** te gebruiken en dit kan op een van de volgende twee manieren kan worden opgegeven:

1. [Het maken van een P12-pushcertificaat dat rechtstreeks kan worden geüpload naar de Notification Hub](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub) (*de oorspronkelijke aanpak*)

1. [Het maken van een p8-certificaat dat kan worden gebruikt voor op tokens gebaseerde verificatie](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication) (*de nieuwe en aanbevolen aanpak*)

De nieuwere aanpak heeft een aantal voordelen zoals beschreven in [Verificatie op basis van tokens (HTTP/2) voor APNS-](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). Er zijn minder stappen nodig, maar deze zijn ook verplicht voor specifieke scenario's. Wij bieden echter de stappen voor beide aanpakken, aangezien deze beide voor de doeleinden van deze zelfstudie werken.

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>OPTIE 1: Het maken van een p12-pushcertificaat dat rechtstreeks kan worden geüpload naar de Notification Hub

1. Voer op uw Mac het hulpprogramma Sleutelhangertoegang uit. Dit kan worden gestart vanuit de map **Hulpprogramma's** of de map **Overige** in Launchpad.

1. Selecteer **Toegang tot sleutelhanger**, vouw **Certificaatassistent** uit en selecteer vervolgens **Een certificaat bij een certificaatautoriteit aanvragen**.

    ![Toegang tot sleutelhanger gebruiken om een nieuw certificaat aan te vragen](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Sleutelhangertoegang selecteert standaard het eerste item in de lijst. Dit kan een probleem zijn als u zich in de categorie **Certificaten** bevindt en **Apple Worldwide Developer Relations Certification Authority** niet het eerste item in de lijst is. Zorg ervoor dat u een item hebt dat geen sleutel is of dat de sleutel **Apple Worldwide Developer Relations Certification Authority** is geselecteerd, voordat u de CSR (Certificate Signing Request) genereert.

1. Selecteer uw **E-mailadres van de gebruiker**, voer uw waarde voor **Algemene naam** in, zorg ervoor dat u **Opgeslagen op schijf** hebt opgegeven en selecteer **Doorgaan**. Laat **E-mailadres van CA** leeg omdat dit niet vereist is.

    ![Verwachte certificaatgegevens](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Selecteer een naam voor het **Certificate Signing Request (CSR)-bestand** in **Opslaan als**, selecteer de locatie in **Waar** en selecteer vervolgens **Opslaan**.

    ![Kies een bestandsnaam voor het certificaat](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Met deze actie wordt de **CSR-bestand** opgeslagen op de geselecteerde locatie. De standaardlocatie is **Desktop**. Onthoud de locatie die u voor het bestand hebt gekozen.

1. Ga terug naar de pagina **Certificaten, id’s en profielen** in de [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456), Scrol omlaag naar de geselecteerde optie **Pushmeldingen** en selecteer vervolgens **Configureren** om het certificaat te maken.

    ![Pagina app-id bewerken](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Het venster **Apple Push Notification Service TLS/SSL-certificaten** wordt weergegeven. Selecteer de knop **Certificaat maken** in de sectie **Ontwikkelen van TLS/SSL-certificaat**.

    ![Certificaat voor app-id-knop maken](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Het scherm **Een nieuw certificaat maken** wordt weergegeven.

    > [!NOTE]
    > In deze zelfstudie wordt een ontwikkelingscertificaat gebruikt. Hetzelfde proces wordt gebruikt bij het registreren van een productiecertificaat. Zorg er wel voor dat u hetzelfde certificaattype gebruikt als u meldingen verzendt.

1. Selecteer **Bestand kiezen**, blader naar de locatie waar u het **CSR-bestand** hebt opgeslagen en dubbelklik vervolgens op de naam van het certificaat om het te laden. Selecteer vervolgens **Doorgaan**.

1. Nadat de portal het certificaat heeft gemaakt, selecteert u de knop **Downloaden**. Sla het certificaat op en onthoud de locatie waar het wordt opgeslagen.

    ![Download-pagina voor gemaakte certificaat](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Het certificaat is nu gedownload en op uw computer in de map **Downloads** opgeslagen.

    ![Certificaatbestand zoeken in de map Downloads](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Standaard krijgt het gedownloade ontwikkelingscertificaat de naam **aps_development.cer**.

1. Dubbelklik op het gedownloade pushcertificaat **aps_development.cer**. Nu wordt het nieuwe certificaat in de sleutelhanger geïnstalleerd, zoals op de volgende afbeelding wordt weergegeven:

    ![Certificatenlijst in Sleutelhangertoegang geeft nieuw certificaat weer](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Hoewel de naam in uw certificaat mogelijk anders is, wordt de naam voorafgegaan door **Apple Development iOS Push Services** en is de juiste bundel-id eraan gekoppeld.

1. In Sleutelhangertoegang **Beheren** + **Klik** op het nieuwe pushcertificaat dat u hebt gemaakt in de categorie **Certificaten**. Selecteer **Exporteren**, geef het bestand een naam, selecteer de indeling **p12** en klik vervolgens op **Opslaan**.

    ![Certificaat exporteren in p12-indeling](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    U kunt ervoor kiezen om het certificaat met een wachtwoord te beveiligen, maar een wachtwoord is optioneel. Klik op **OK** als u het maken van wachtwoorden wilt overslaan. Noteer de bestandsnaam en locatie van het geëxporteerde p12-certificaat. Ze worden gebruikt om verificatie met APN’s mogelijk te maken.

    > [!NOTE]
    > Uw p12-bestandsnaam en-locatie kunnen afwijken van wat er in deze zelfstudie wordt afgebeeld.

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>OPTIE 2: Het maken van een p8-certificaat dat kan worden gebruikt voor op tokens gebaseerde verificatie

1. Noteer de volgende details:

    * **App-id-voorvoegsel** (**Team-id**)
    * **Bundel-id**

1. Als u teruggaat naar **Certificaten, id's en profielen**, klikt u op **Sleutels**.

   > [!NOTE]
   > Als u al een sleutel voor **APNS** hebt geconfigureerd, kunt u het p8-certificaat dat u hebt gedownload opnieuw gebruiken nadat het is gemaakt. Als dat het geval is, kunt u de stappen **3** tot **5** negeren.

1. Klik op de knop **+** (of de knop **Een sleutel maken**) om een nieuwe sleutel te maken.
1. Geef een geschikte waarde voor **Sleutelnaam** op en controleer vervolgens de optie **Apple Push Notifications service (APNS)** en klik vervolgens op **Doorgaan**, gevolgd door **Registreren** op het volgende scherm.
1. Klik op **Downloaden** en verplaats het bestand **p8** (voorafgegaan door *AuthKey_* ) naar een veilige lokale map en klik vervolgens op **Klaar**.

   > [!NOTE]
   > Zorg ervoor dat uw p8-bestand op een veilige plaats is opgeslagen (en sla een back-up op). Nadat de sleutel is gedownload, kan deze niet opnieuw worden gedownload omdat de serverkopie wordt verwijderd.
  
1. Klik in **Sleutels** op de sleutel die u hebt gemaakt (of een bestaande sleutel als u ervoor hebt gekozen om deze te gebruiken).
1. Noteer de waarde van **Sleutel-id**.
1. Open uw p8-certificaat in een geschikte toepassing van uw keuze, zoals [**Visual Studio Code**](https://code.visualstudio.com). Noteer de sleutelwaarde (tussen **-----BEGIN PRIVATE KEY-----** en **-----END PRIVATE KEY-----** ).

    -----BEGIN PRIVATE KEY-----  
    <key_value>  
    -----END PRIVATE KEY-----

    > [!NOTE]
    > Dit is de **tokenwaarde** die later wordt gebruikt om **Notification Hub**te configureren.

Aan het einde van deze stappen moet u de volgende informatie gebruiken voor later in [Uw Notification Hub configureren met APNS-gegevens](#configure-your-notification-hub-with-apns-information):

* **Team-id** (zie stap 1)
* **Bundel-id** (zie stap 1)
* **Sleutel-id** (zie stap 7)
* **Tokenwaarde** (sleutelwaarde p8 verkregen in stap 8)

### <a name="create-a-provisioning-profile-for-the-app"></a>Een inrichtingsprofiel voor de app maken

1. Ga terug naar de [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456), selecteer **Certificaten, id's en profielen**, selecteer **Profielen** in het linkermenu en selecteer vervolgens **+** om een nieuw profiel te maken. Het scherm **Een nieuw inrichtingsprofiel registreren** wordt weergegeven.

1. Selecteer **Ontwikkeling iOS-app** onder **Ontwikkeling** als het inrichtingsprofieltype en selecteer vervolgens **Doorgaan**.

    ![Lijst met inrichtingsprofielen](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Selecteer vervolgens in de vervolgkeuzelijst **App-id** de app-id die u hebt gemaakt en selecteer **Doorgaan**.

    ![Selecteer de app-id](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Selecteer in het venster **Certificaten selecteren** het ontwikkelingscertificaat dat u gebruikt voor het ondertekenen bij programmacode en selecteer **Doorgaan**.

    > [!NOTE]
    > Dit certificaat is niet het pushcertificaat dat u hebt gemaakt in de [vorige stap](#creating-a-certificate-for-notification-hubs). Dit is uw ontwikkelingscertificaat. Als dit nog niet bestaat, moet u deze maken omdat dit een [vereiste](#prerequisites) voor deze zelfstudie is. Ontwikkelingscertificaten kunnen worden gemaakt in de [Apple Provisioning Portal](https://developer.apple.com) via [Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) of in [Visual Studio](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/).

1. Ga terug naar de pagina **Certificaten, id's en profielen**, selecteer **Profielen** in het linkermenu en selecteer vervolgens **+** om een nieuw profiel te maken. Het scherm **Een nieuw inrichtingsprofiel registreren** wordt weergegeven.

1. Selecteer in het venster **Certificaten selecteren** het ontwikkelingscertificaat dat u heeft gemaakt. Selecteer vervolgens **Doorgaan**.

1. Selecteer vervolgens de apparaten die u voor de tests wilt gebruiken en selecteer **Doorgaan**.

1. Kies ten slotte een naam voor het profiel in **Inrichtingsprofielnaam**en selecteer **Genereren**.

    ![Een naam kiezen voor het inrichtingsprofiel](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Wanneer het nieuwe inrichtingsprofiel is gemaakt, selecteert u **Downloaden**. Onthoud de locatie waar het wordt opgeslagen.

1. Blader naar de locatie van het inrichtingsprofiel en dubbelklik erop om het te installeren op uw ontwikkelingscomputer.
