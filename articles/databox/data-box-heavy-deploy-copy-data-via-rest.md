---
title: "Zelfstudie: Gegevens kopiëren naar Azure Data Box Blob-opslag via REST API's"
description: Meer informatie over het kopiëren van gegevens naar uw Azure Data Box Heavy Blob-opslag via REST API's
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 9f3ba0a7e9f7cf72b0eade16679d980fe2207f98
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80297216"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Zelfstudie: Gegevens kopiëren naar Azure Data Box Blob-opslag via REST API's  

In deze zelfstudie worden procedures beschreven voor het maken van een *http*- of *https*-verbinding met Azure Data Box-blobopslag via REST API's. Eenmaal verbonden, worden de stappen beschreven die nodig zijn om de gegevens naar De Blob-opslag van Gegevensbox te kopiëren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten
> * Verbinding maken met Data Box-blobopslag via *http* of *https*
> * Gegevens kopiëren naar Data Box Heavy

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de [zelfstudie: Azure Data Box Heavy instellen](data-box-heavy-deploy-set-up.md)voltooid.
2. U hebt de Data Box Heavy ontvangen en de orderstatus in de portal is **Geleverd**.
3. U hebt de [systeemvereisten voor Data Box-blobopslag](data-box-system-requirements-rest.md) bekeken en bent bekend met de ondersteunde versies van API's, SDK's en hulpprogramma's.
4. U hebt toegang tot een hostcomputer met de gegevens die u wilt kopiëren naar Data Box Heavy. Op uw hostcomputer moet
    - Voer een [ondersteund besturingssysteem uit](data-box-system-requirements.md).
    - Verbonden zijn met een netwerk met hoge snelheid. Voor de snelste kopieersnelheden kunnen twee verbindingen van 40 GbE (één per knooppunt) naast elkaar worden gebruikt. Als u niet beschikt over een 40-GbE-verbinding, is het advies dat u ten minste twee verbindingen van 10 GbE (één per knooppunt) gebruikt. 
5. [Download AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) op de hostcomputer. U gebruikt AzCopy om gegevens te kopiëren van uw hostcomputer naar Azure Data Box-blobopslag.


## <a name="connect-via-http-or-https"></a>Verbinding maken via http of https

U kunt verbinding maken met Data Box-blobopslag via *http* of *https*.

- *Https* is de veilige en aanbevolen manier om verbinding te maken met Data Box Blob-opslag.
- *Http* wordt gebruikt voor verbindingen via vertrouwde netwerken.

De stappen om verbinding te maken zijn anders wanneer u verbinding maakt met De Blob-opslag van Gegevensboxen via *http* of *https*.

## <a name="connect-via-http"></a>Verbinding maken via http

Voor een verbinding met Data Box-blobopslag via REST API's via *http* zijn de volgende stappen vereist:

- De apparaat-IP en het blobservice-eindpunt aan de externe host toevoegen
- Software van derden configureren en de verbinding controleren

Deze stappen worden afzonderlijk beschreven in de volgende gedeelten.

> [!IMPORTANT]
> Voor Data Box Heavy moet u alle verbindingsinstructies herhalen om verbinding te maken met het tweede knooppunt.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Ip-adres en eindpunt van blobservice toevoegen

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]



### <a name="configure-partner-software-and-verify-connection"></a>Partnersoftware configureren en verbinding controleren

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Verbinding maken via https

Voor een verbinding met Azure-blobopslag via REST API's via https zijn de volgende stappen vereist:

- Het certificaat downloaden van Azure Portal
- Het certificaat importeren op de client of externe host
- Het ip-adres van het apparaat en het eindpunt van de blobservice toevoegen aan de client of externe host
- Software van derden configureren en de verbinding controleren

Deze stappen worden afzonderlijk beschreven in de volgende gedeelten.

> [!IMPORTANT]
> Voor Data Box Heavy moet u alle verbindingsinstructies herhalen om verbinding te maken met het tweede knooppunt.

### <a name="download-certificate"></a>Certificaat downloaden

Gebruik Azure Portal om het certificaat te downloaden.

1. Meld u aan bij Azure Portal.
2. Ga naar uw Data Box-bestelling en ga vervolgens naar **Algemeen > Apparaatdetails**.
3. Ga onder **Apparaatreferenties** naar **API-toegang** tot het apparaat. Klik **op Downloaden**. Met deze actie wordt een ** \<>.cer-certificaatbestand** van uw bestelling gedownload. Sla dit bestand **op.** U installeert dit certificaat op de client- of hostcomputer waarmee u verbinding maakt met het apparaat.

    ![Certificaat downloaden in Azure Portal](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>Certificaat importeren 

Voor toegang tot Data Box Blob-opslag via HTTPS is een TLS/SSL-certificaat voor het apparaat vereist. De manier waarop dit certificaat beschikbaar wordt gesteld aan de clienttoepassing varieert van toepassing tot toepassing en tussen besturingssystemen en distributies. Sommige toepassingen hebben toegang tot het certificaat nadat het is geïmporteerd in het certificaatarchief van het systeem, terwijl andere toepassingen geen gebruik maken van dat mechanisme.

Specifieke informatie voor sommige toepassingen wordt in deze sectie vermeld. Raadpleeg voor meer informatie over andere toepassingen de documentatie voor de toepassing en het gebruikte besturingssysteem.

Volg deze stappen `.cer` om het bestand te importeren in de hoofdopslag van een Windows- of Linux-client. Op een Windows-systeem u Windows PowerShell of de Gebruikersinterface van Windows Server gebruiken om het certificaat op uw systeem te importeren en te installeren.

#### <a name="use-windows-powershell"></a>Windows PowerShell gebruiken

1. Start een Windows PowerShell-sessie als beheerder.
2. Typ het volgende na de opdrachtprompt:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Gebruikersinterface van Windows Server gebruiken

1.  Klik met `.cer` de rechtermuisknop op het bestand en selecteer **Certificaat installeren**. Met deze actie wordt de wizard Certificaat importeren gestart.
2.  Selecteer bij **Archieflocatie** de optie **Lokale computer** en klik op **Volgende**.

    ![Certificaat importeren met PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  Selecteer **Alle certificaten in het onderstaande archief opslaan** en klik op **Bladeren**. Navigeer naar het basisarchief van de externe host en klik vervolgens op **Volgende**.

    ![Certificaat importeren met PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  Klik op **Voltooien**. Er wordt een bericht weergegeven dat het importeren is geslaagd.

    ![Certificaat importeren met PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Een Linux-systeem gebruiken

De methode om een certificaat te importeren verschilt per distributie.

> [!IMPORTANT]
> Voor Data Box Heavy moet u alle verbindingsinstructies herhalen om verbinding te maken met het tweede knooppunt.

Verschillende, zoals Ubuntu en Debian, gebruiken de `update-ca-certificates` opdracht.  

- Wijzig de naam van het certificaatbestand Base64 om `.crt` een `/usr/local/share/ca-certificates directory`extensie te hebben en kopieer deze naar de .
- Voer de opdracht `update-ca-certificates` uit.

Recente versies van RHEL, Fedora en `update-ca-trust` CentOS gebruiken de opdracht.

- Kopieer het certificaatbestand `/etc/pki/ca-trust/source/anchors` naar de map.
- Voer `update-ca-trust` uit.

Raadpleeg de documentatie die specifiek is voor uw distributie voor meer informatie.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Ip-adres en eindpunt van blobservice toevoegen 

Volg dezelfde stappen om [het IP-adres van het *http*apparaat en het eindpunt van de blobservice toe](#add-device-ip-address-and-blob-service-endpoint)te voegen wanneer u verbinding maakt via http .

### <a name="configure-partner-software-and-verify-connection"></a>Partnersoftware configureren en verbinding controleren

Volg de stappen voor [het configureren van partnersoftware die u hebt gebruikt tijdens het maken via *http*](#configure-partner-software-and-verify-connection). Het enige verschil is dat u de optie *HTTP gebruiken* uitgeschakeld laat.

## <a name="copy-data-to-data-box-heavy"></a>Gegevens kopiëren naar Data Box Heavy

Nadat u verbinding met de Data Box-blobopslag hebt gemaakt, moet u de gegevens kopiëren. Neem de volgende overwegingen door voordat u gegevens kopieert:

-  Controleer tijdens het kopiëren van gegevens of de gegevensgrootte voldoet aan de groottelimieten die zijn beschreven in de [limieten voor Azure-opslag en Gegevensvak Zwaar.](data-box-limits.md)
- Als gegevens, die worden geüpload door Data Box Heavy, gelijktijdig worden geüpload door andere toepassingen buiten Data Box Heavy, kan dit leiden tot het uploaden van taakfouten en gegevensbeschadiging.

In deze zelfstudie wordt AzCopy gebruikt om gegevens te kopiëren naar Data Box-blobopslag. U kunt de gegevens ook kopiëren met Azure Storage Explorer (als u liever een GUI-hulpprogramma gebruikt) of met partnersoftware.

De kopieerprocedure bestaat uit de volgende stappen:

- Een container maken
- De inhoud van een map uploaden naar Data Box-blobopslag
- Gewijzigde bestanden uploaden naar Data Box-blobopslag


Deze stappen worden afzonderlijk in detail beschreven in de volgende gedeelten.

> [!IMPORTANT]
> Voor Data Box Heavy moet u alle kopieerinstructies herhalen om gegevens naar het tweede knooppunt te kopiëren.

### <a name="create-a-container"></a>Een container maken

De eerste stap is het maken van een container, omdat blobs altijd naar een container moeten worden geüpload. Met containers ordent u groepen blobs net zoals u bestanden in mappen op uw computer ordent. Volg deze stappen om een blobcontainer te maken.

1. Open Storage Explorer.
2. Vouw in het linkerdeelvenster het opslagaccount uit waarin u de blobcontainer wilt maken.
3. Klik met de rechtermuisknop op **Blob-containers** en selecteer **Blobcontainer maken**.

   ![Het contextmenu Blobcontainers maken](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Onder de map **Blobcontainers** wordt een tekstvak weergegeven. Voer een naam in voor de blobcontainer. Zie [De container maken en machtigingen instellen](../storage/blobs/storage-quickstart-blobs-dotnet.md) voor informatie over regels en beperkingen voor namen van blobcontainers.
5. Druk op **Enter** om de blobcontainer te maken of op **Esc** om te annuleren. Als de blobcontainer is gemaakt, wordt deze weergegeven in de map **Blobcontainers** voor het geselecteerde opslagaccount.

   ![Gemaakte blobcontainer](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>De inhoud van een map uploaden naar Data Box-blobopslag

U kunt AzCopy gebruiken om alle bestanden in een map te uploaden naar Blob-opslag in Windows of Linux. Als u alle blobs in een map wilt uploaden, voert u de volgende AzCopy-opdracht uit:

#### <a name="linux"></a>Linux

    azcopy \
        --source /mnt/myfolder \
        --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
        --dest-key <key> \
        --recursive

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S


Vervang `<key>` door uw accountsleutel. Ga naar uw opslagaccount om uw accountsleutel in Azure Portal op te halen. Ga naar **Instellingen > Toegangssleutels**, selecteer een sleutel en plak deze in de AzCopy-opdracht.

Als de opgegeven doelcontainer niet bestaat, wordt deze door AzCopy gemaakt en wordt het bestand erin geüpload. Werk het bronpad bij in uw gegevensmap en vervang `data-box-storage-account-name` in de doel-URL door de naam van het opslagaccount dat is gekoppeld aan uw Data Box.

Als u de inhoud van de opgegeven map recursief wilt uploaden naar Blob-opslag, geeft u de optie `--recursive` (Linux) of `/S` optie (Windows) op. Wanneer u AzCopy uitvoert met een van deze opties, worden alle submappen en de bijbehorende bestanden ook geüpload.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Gewijzigde bestanden uploaden naar Data Box-blobopslag

Gebruik AzCopy om bestanden te uploaden op basis van de datum/tijd waarop deze het laatst zijn gewijzigd. Als u dit wilt uitproberen, wijzigt u bestanden of maakt u nieuwe bestanden in uw bronmap voor testdoeleinden. Als u alleen bijgewerkte of nieuwe bestanden wilt uploaden, voegt u de parameter `--exclude-older` (Linux) of `/XO` (Windows) toe aan de AzCopy-opdracht.

Als u alleen resources wilt kopiëren die niet in het doel bestaan, geeft u zowel `--exclude-older` als `--exclude-newer` (Linux) of `/XO` als `/XN` (Windows) als parameters op in de AzCopy-opdracht. Door AzCopy worden alleen de bijgewerkte gegevens geüpload, op basis van het tijdstempel.

#### <a name="linux"></a>Linux
    azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive \
    --exclude-older

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO

Zie Problemen met de opslag van [De Blob voor gegevens oplossen](data-box-troubleshoot-rest.md)als er fouten zijn tijdens de bewerking verbinding of kopiëren.

In de laatste stap gaat u het apparaat voorbereiden voor verzending.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
> * Vereisten
> * Verbinding maken met Data Box-blobopslag via *http* of *https*
> * Gegevens kopiëren naar Data Box Heavy


Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box naar Microsoft verzendt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box Heavy verzenden naar Microsoft](./data-box-heavy-deploy-picked-up.md)
