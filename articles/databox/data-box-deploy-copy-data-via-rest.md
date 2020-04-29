---
title: "Zelf studie: REST-Api's gebruiken om te kopiëren naar Blob Storage"
titleSuffix: Azure Data Box
description: In deze zelfstudie leest u hoe u gegevens kopieert naar uw Azure Data Box-blobopslag via REST API's
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: alkohli
ms.openlocfilehash: 7642c009a5bcd1d00efb432975fff5a65c7ba340
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80297203"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Zelf studie: gegevens kopiëren naar Azure Data Box Blob-opslag via REST-Api's  

In deze zelfstudie worden procedures beschreven voor het maken van een *http*- of *https*-verbinding met Azure Data Box-blobopslag via REST API's. Als de verbinding tot stand is gebracht, worden ook de benodigde stappen voor het kopiëren van de gegevens naar Data Box-blobopslag en het voor verzending voorbereiden van Data Box beschreven.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Vereisten
> * Verbinding maken met Data Box-blobopslag via *http* of *https*
> * Gegevens kopiëren naar Data Box

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de [zelf studie voltooid: stel Azure data Box](data-box-deploy-set-up.md)in.
2. U hebt uw Data Box ontvangen en de bestelstatus in de portal is **Geleverd**.
3. U hebt de [systeemvereisten voor Data Box-blobopslag](data-box-system-requirements-rest.md) bekeken en bent bekend met de ondersteunde versies van API's, SDK's en hulpprogramma's.
4. U hebt toegang tot een hostcomputer waarop de gegevens staan die u naar Data Box wilt kopiëren. Op uw hostcomputer moet
    - Een [ondersteund besturings systeem](data-box-system-requirements.md)uitvoeren.
    - Verbonden zijn met een netwerk met hoge snelheid. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10 GbE-verbinding beschikbaar is, kan een 1 GbE-gegevenskoppeling worden gebruikt, maar dit heeft invloed op de kopieersnelheden.
5. [Download AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) op de hostcomputer. U gebruikt AzCopy om gegevens te kopiëren van uw hostcomputer naar Azure Data Box-blobopslag.


## <a name="connect-via-http-or-https"></a>Verbinding maken via http of https

U kunt verbinding maken met Data Box-blobopslag via *http* of *https*.

- *Https* is de veilige en aanbevolen manier om verbinding te maken met data Box Blob-opslag.
- *Http* wordt gebruikt voor verbindingen via vertrouwde netwerken.

De stappen om verbinding te maken wijken af wanneer u verbinding maakt met Data Box Blob-opslag via *http* of *https*.

## <a name="connect-via-http"></a>Verbinding maken via http

Voor een verbinding met Data Box-blobopslag via REST API's via *http* zijn de volgende stappen vereist:

- De apparaat-IP en het blobservice-eindpunt aan de externe host toevoegen
- Software van derden configureren en de verbinding controleren

Deze stappen worden afzonderlijk beschreven in de volgende gedeelten.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>IP-adres van apparaat en BLOB service-eind punt toevoegen

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]

### <a name="configure-partner-software-and-verify-connection"></a>Partnersoftware configureren en verbinding controleren

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Verbinding maken via https

Voor een verbinding met Azure-blobopslag via REST API's via https zijn de volgende stappen vereist:

- Het certificaat downloaden van Azure Portal
- Het certificaat op de client of de externe host importeren
- Het IP-adres van het apparaat en het BLOB-service-eind punt toevoegen aan de client of externe host
- Software van derden configureren en de verbinding controleren

Deze stappen worden afzonderlijk beschreven in de volgende gedeelten.

### <a name="download-certificate"></a>Certificaat downloaden

Gebruik Azure Portal om het certificaat te downloaden.

1. Meld u aan bij Azure Portal.
2. Ga naar uw Data Box-bestelling en ga vervolgens naar **Algemeen > Apparaatdetails**.
3. Ga onder **Apparaatreferenties** naar **API-toegang** tot het apparaat. Klik op **downloaden**. Met deze actie wordt ** \<de naam van uw order>. CER** -certificaat bestand gedownload. **Sla** dit bestand op. U installeert dit certificaat op de client- of hostcomputer waarmee u verbinding maakt met het apparaat.

    ![Certificaat downloaden in Azure Portal](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>Certificaat importeren 

Voor toegang tot Data Box Blob-opslag via HTTPS is een TLS/SSL-certificaat vereist voor het apparaat. De manier waarop dit certificaat beschikbaar wordt gesteld aan de client toepassing, is afhankelijk van de toepassing en andere besturings systemen en distributies. Sommige toepassingen hebben toegang tot het certificaat nadat het is geïmporteerd in het certificaat archief van het systeem, terwijl andere toepassingen geen gebruik maken van dat mechanisme.

In deze sectie wordt specifieke informatie voor sommige toepassingen vermeld. Raadpleeg de documentatie voor de toepassing en het gebruikte besturings systeem voor meer informatie over andere toepassingen.

Volg deze stappen om het `.cer` bestand te importeren in het basis archief van een Windows-of Linux-client. Op een Windows-systeem kunt u Windows Power shell of de Windows Server-gebruikers interface gebruiken om het certificaat te importeren en te installeren op uw systeem.

#### <a name="use-windows-powershell"></a>Windows PowerShell gebruiken

1. Start een Windows PowerShell-sessie als beheerder.
2. Typ het volgende na de opdrachtprompt:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Windows Server-gebruikers interface gebruiken

1.   Klik met de rechter `.cer` muisknop op het bestand en selecteer **certificaat installeren**. Met deze actie wordt de wizard Certificaat importeren gestart.
2.   Selecteer bij **Archieflocatie** de optie **Lokale computer** en klik op **Volgende**.

    ![Certificaat importeren met PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.   Selecteer **Alle certificaten in het onderstaande archief opslaan** en klik op **Bladeren**. Navigeer naar het basisarchief van de externe host en klik vervolgens op **Volgende**.

    ![Certificaat importeren met PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.   Klik op **Voltooien**. Er wordt een bericht weergegeven dat het importeren is geslaagd.

    ![Certificaat importeren met PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Een Linux-systeem gebruiken

De methode voor het importeren van een certificaat varieert per distributie.

Verschillende, zoals Ubuntu en Debian, gebruikt u de `update-ca-certificates` opdracht.  

- Wijzig de naam van het certificaat bestand met `.crt` base64-code ring en kopieer het naar `/usr/local/share/ca-certificates directory`de.
- Voer de opdracht `update-ca-certificates` uit.

Recente versies van RHEL, Fedora en CentOS gebruiken de `update-ca-trust` opdracht.

- Kopieer het certificaat bestand naar de `/etc/pki/ca-trust/source/anchors` map.
- Voer `update-ca-trust` uit.

Raadpleeg de documentatie die specifiek is voor uw distributie voor meer informatie.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>IP-adres van apparaat en BLOB service-eind punt toevoegen 

Volg dezelfde stappen om het [IP-adres van het apparaat en het eind punt van de BLOB-service toe te voegen bij het verbinden via *http*](#add-device-ip-address-and-blob-service-endpoint)

### <a name="configure-partner-software-and-verify-connection"></a>Partnersoftware configureren en verbinding controleren

Volg de stappen voor het [configureren van partner software die u hebt gebruikt bij het maken van verbinding via *http*](#configure-partner-software-and-verify-connection). Het enige verschil is dat u de optie *HTTP gebruiken* uitgeschakeld laat.

## <a name="copy-data-to-data-box"></a>Gegevens kopiëren naar Data Box

Nadat u verbinding met de Data Box-blobopslag hebt gemaakt, moet u de gegevens kopiëren. Neem de volgende overwegingen door voordat u gegevens kopieert:

* Zorg er tijdens het kopiëren van gegevens voor dat de gegevensgrootte voldoet aan de limieten die staan beschreven in de [limieten voor Azure-opslag en Data Box](data-box-limits.md).
* Als de gegevens die door Data Box worden geüpload gelijktijdig door andere toepassingen buiten Data Box worden geüpload, kan dit leiden tot uploadfouten en beschadigde gegevens.
* Zorg ervoor dat u een kopie van de bron gegevens bewaart totdat u kunt bevestigen dat de Data Box uw gegevens heeft overgebracht naar Azure Storage.

In deze zelfstudie wordt AzCopy gebruikt om gegevens te kopiëren naar Data Box-blobopslag. U kunt de gegevens ook kopiëren met Azure Storage Explorer (als u liever een GUI-hulpprogramma gebruikt) of met partnersoftware.

De kopieerprocedure bestaat uit de volgende stappen:

- Een container maken
- De inhoud van een map uploaden naar Data Box-blobopslag
- Gewijzigde bestanden uploaden naar Data Box-blobopslag

Deze stappen worden afzonderlijk in detail beschreven in de volgende gedeelten.

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
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive \
    --exclude-older

#### <a name="windows"></a>Windows

    AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO

Zie problemen [met data Box Blobopslag oplossen](data-box-troubleshoot-rest.md)als er fouten zijn opgetreden tijdens de bewerking voor maken of kopiëren.

In de laatste stap gaat u het apparaat voorbereiden voor verzending.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
> * Vereisten
> * Verbinding maken met Data Box-blobopslag via *http* of *https*
> * Gegevens kopiëren naar Data Box


Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box naar Microsoft verzendt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box verzenden naar Microsoft](./data-box-deploy-picked-up.md)
