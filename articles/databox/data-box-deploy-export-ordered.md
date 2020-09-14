---
title: Zelf studie voor het exporteren van gegevens uit Azure Data Box | Microsoft Docs
description: Meer informatie over de implementatie vereisten en het exporteren van gegevens uit een Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 5494c2dd57220888ad846aaf69fde2f7a59353e4
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90053022"
---
# <a name="tutorial-create-export-order-for-azure-data-box-preview"></a>Zelf studie: export volgorde voor Azure Data Box maken (preview)

Azure Data Box is een hybride oplossing waarmee u gegevens uit Azure kunt verplaatsen naar uw locatie. In deze zelf studie wordt beschreven hoe u een export volgorde voor Azure Data Box maakt. De belangrijkste reden voor het maken van een export volgorde is voor herstel na nood gevallen, in geval van een on-premises opslag, waardoor er een back-up moet worden hersteld.

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Vereisten voor exporteren
> * Een Data Box voor het exporteren best Ellen
> * De export volgorde bijhouden
> * De export volgorde annuleren

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Vereisten

Voltooi de volgende configuratie vereisten voor Data Box Service en apparaat voordat u het apparaat bestelt.

### <a name="for-service"></a>Voor de service

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

* Zorg ervoor dat u een bestaande resource groep hebt die u met uw Azure Data Box kunt gebruiken.

* Zorg ervoor dat uw Azure Storage-account waaruit u gegevens wilt exporteren, een van de ondersteunde typen opslag accounts is, zoals wordt beschreven in [ondersteund opslag account voor data Box](data-box-system-requirements.md#supported-storage-accounts).

### <a name="for-device"></a>Voor het apparaat

Zorg voordat u begint voor het volgende:

* Er is een hostcomputer verbonden met het datacenternetwerk. U kopieert de gegevens van Azure Data Box naar deze computer. Uw hostcomputer moet een ondersteund besturingssysteem hebben, zoals beschreven in [Systeemvereisten voor Azure Data Box](data-box-system-requirements.md).

* Uw datacenter moet een netwerk met hoge snelheid hebben. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10 GbE-verbinding beschikbaar is, kan een 1 GbE-gegevenskoppeling worden gebruikt. Dit heeft echter wel invloed op de kopieersnelheid.

## <a name="order-data-box-for-export"></a>Data Box voor het exporteren van orders

Voer de volgende stappen uit in de Azure-portal om een apparaat te bestellen.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden op deze URL: [https://portal.azure.com](https://portal.azure.com).

2. Selecteer **+ Een resource maken** en zoek naar *Azure Data Box*. Selecteer **Azure Data Box**.

   ![Bron maken](media/data-box-deploy-export-ordered/azure-data-box-export-00b.png)

3. Selecteer **Maken**.

   ![Azure Data Box maken](media/data-box-deploy-export-ordered/azure-data-box-export-00c.png)

4. Controleer of de Azure Data Box-Service beschikbaar is in uw regio. Voer de volgende gegevens in of selecteer deze en selecteer **Toepassen**.

    |Instelling  |Waarde  |
    |---------|---------|
    |Type overdracht     | Selecteer **exporteren naar Azure**.        |
    |Abonnement     | Selecteer een EA-, CSP- of Azure Sponsorship-abonnement voor de Data Box-service. <br> Het abonnement is gekoppeld aan uw factureringsrekening.       |
    |Resourcegroep     |    Selecteer een bestaande resourcegroep. <br> Een resourcegroep is een logische container voor resources die samen kunnen worden beheerd of geïmplementeerd.         |
    |Azure-regio van bron    |    Selecteer de Azure-regio waarin uw gegevens zich momenteel bevinden.         |
    |Land van bestemming     |     Selecteer het land waar u het apparaat wilt verzenden.        |

   ![Uw Data Box-instellingen selecteren](media/data-box-deploy-export-ordered/azure-data-box-export-01.png)

5. Selecteer **Data Box**. De maximale bruikbare capaciteit voor één bestelling is 80 TB. U kunt meerdere bestellingen doen voor grotere gegevensgrootten.

   ![Data Box capaciteit selecteren](media/data-box-deploy-export-ordered/azure-data-box-export-02b.png)

6. In **volg orde**geeft u de details van de **basis** order op. Voer de volgende gegevens in of selecteer deze en selecteer **Volgende**.

    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement     | Het abonnement wordt automatisch ingevuld op basis van uw eerdere selectie.|
    |Resourcegroep | De resourcegroep die u eerder hebt geselecteerd. |
    |Naam van de export volgorde     |  Geef een beschrijvende naam op om de bestelling te volgen. <br> De naam kan tussen 3 en 24 tekens bevatten (letters, cijfers en afbreekstreepjes). <br> De naam moet beginnen en eindigen met een letter of cijfer.      |

    ![Basis principes van export volgorde](media/data-box-deploy-export-ordered/azure-data-box-export-03.png)

    Selecteer **volgende: gegevens selectie** om door te gaan.

7. Selecteer bij **gegevens selectie**de optie **opslag account en export type toevoegen**.

    ![Opslag account en export type toevoegen](media/data-box-deploy-export-ordered/azure-data-box-export-03b.png)

8. Geef bij **export optie selecteren**de optie Details van de export op. Voer de volgende informatie in of Selecteer deze en selecteer **toevoegen**.

    |Instelling  |Waarde  |
    |---------|---------|
    |Opslagaccount     | Het Azure Storage account van waaruit u gegevens wilt exporteren. |
    |Export type     | Hiermee geeft u het type gegevens op dat van **alle objecten** moet worden geëxporteerd en het **XML-bestand moet worden gebruikt**.<ul><li> **Alle objecten** : Hiermee geeft u op dat de taak alle gegevens exporteert, afhankelijk van uw selectie voor de **overdrachts opties**.</li><li> **XML-bestand gebruiken** : Hiermee geeft u een XML-bestand op dat een set paden en voor voegsels bevat voor blobs en/of bestanden die uit het opslag account moeten worden geëxporteerd. Het XML-bestand moet zich in de container van het geselecteerde opslag account bevinden en er wordt momenteel geen ondersteuning voor het selecteren van bestands shares ondersteund. Het bestand moet een niet-leeg XML-bestand zijn.</li></ul>        |
    |Overdrachts opties     |  Hiermee geeft u de opties voor gegevens overdracht van **Alles selecteren**, **alle blobs**en **alle bestanden**. <ul><li> **Alles selecteren** : Hiermee geeft u op dat alle blobs en Azure files worden geëxporteerd. Als u een opslag account gebruikt dat alleen blobs (Blob Storage-account) ondersteunt, kan de optie **alle bestanden** niet worden geselecteerd.</li><li> **Alle blobs** : Hiermee geeft u op dat alleen blok-en pagina-blobs worden geëxporteerd.</li><li> **Alle bestanden** : Hiermee geeft u op dat alle bestanden met uitzonde ring van blobs worden geëxporteerd. Het type opslag account dat u hebt (GPv1-en GPv2-, Premium-opslag of Blob-opslag) bepaalt de typen gegevens die u kunt exporteren. Zie [ondersteunde opslag accounts voor export](../storage/common/storage-import-export-requirements.md#supported-storage-types)voor meer informatie.</li></ul>         |
    |Uitgebreid logboek opnemen     | Hiermee wordt aangegeven of u een uitgebreid logboek bestand wilt met een lijst met alle bestanden die zijn geëxporteerd.        |

    > [!NOTE]
    >
    > Als u **XML-bestand gebruiken** voor de instelling **Export type** selecteert, moet u ervoor zorgen dat de XML geldige paden en/of voor voegsels bevat. U moet het XML-bestand maken en opgeven.  Als het bestand ongeldig is of als er geen gegevens overeenkomen met de opgegeven paden, wordt de volg orde beëindigd met gedeeltelijke gegevens of worden er geen gegevens geëxporteerd.

    Zie [order exporteren met XML-bestand](data-box-deploy-export-ordered.md#export-order-using-xml-file)voor meer informatie over het toevoegen van een XML-bestand aan een container.

   ![Selecteer de optie exporteren](media/data-box-deploy-export-ordered/azure-data-box-export-04b.png)

   Voor een voor beeld van de XML-invoer raadpleegt u [XML-voor beeld-invoer](data-box-deploy-export-ordered.md#sample-xml-file)

9. Controleer de instellingen in **gegevens selectie**en selecteer **volgende: beveiligings>**.

   ![Contactgegevens](media/data-box-deploy-export-ordered/azure-data-box-export-05.png)

1. In **beveiliging**, als u dubbele versleuteling op basis van software wilt inschakelen, selecteert u **dubbele versleuteling inschakelen voor de order**. 

   De op software gebaseerde versleuteling wordt uitgevoerd naast de AES-256-bits versleuteling van de gegevens op de Data Box.

   > [!NOTE]
   > Als u deze optie inschakelt, kunnen order verwerking en het kopiëren van gegevens langer duren. U kunt deze optie niet wijzigen nadat u uw bestelling hebt gemaakt.

   ![Beveiligings scherm voor het importeren van data box, dubbele versleuteling](media/data-box-deploy-export-ordered/azure-data-box-export-05b.png)

   Selecteer **Volgende: Contactgegevens** om verder te gaan.

10. Selecteer in de **contact gegevens** **+ Verzend adres toevoegen** om uw verzend gegevens in te voeren.

    ![Verzend adres toevoegen](media/data-box-deploy-export-ordered/azure-data-box-export-06.png)

11. Geef bij **Verzend adres toevoegen**uw voor-en achternaam, naam en post adres van het bedrijf en een geldig telefoon nummer op. Selecteer **Valideren**. De service controleert of de service beschikbaar is voor de regio van het verzendadres. Als de service beschikbaar is voor het opgegeven verzendadres, ontvangt u daarover een melding.

    ![Verzend adres valideren](media/data-box-deploy-export-ordered/azure-data-box-export-07.png)

    U kunt deze optie selecteren als u een regio wilt best Ellen waarvoor self-managed Shipping beschikbaar is. Zie [Zelfbeheerde verzending gebruiken](data-box-portal-customer-managed-shipping.md) voor meer informatie over zelfbeheerde verzendingen.

12. Selecteer **Verzend adres toevoegen** zodra de verzend gegevens zijn gevalideerd.

13. Controleer uw verzend adres en e-mail adres bij **contact gegevens**. De service stuurt e-mailmeldingen naar het opgegeven e-mailadres over updates van de bestelstatus.

    We raden u aan een e-mailadres van een groep te gebruiken, zodat u meldingen blijft ontvangen als een beheerder de groep verlaat.

    ![Bestellingsgegevens](media/data-box-deploy-export-ordered/azure-data-box-export-09.png)

14. Selecteer **volgende: controleren + volg orde>**. U moet de voor waarden accepteren om door te gaan met het maken van de order.

15. Selecteer **Bestellen**. Het duurt een paar minuten voordat de bestelling is gemaakt.

    ![Volg orde door voeren](media/data-box-deploy-export-ordered/azure-data-box-export-10.png)

## <a name="export-order-using-xml-file"></a>Bestelling exporteren met XML-bestand

Als u **XML-bestand gebruiken**selecteert, kunt u specifieke containers en blobs opgeven (pagina en blok keren) die u wilt exporteren. U moet de [XML-voorbeeld bestands tabel](#sample-xml-file) specificaties volgen voor het opmaken van uw XML. De volgende stappen laten zien hoe u een XML-bestand gebruikt voor het exporteren van uw gegevens:

1. Voor **Export type**selecteert **u XML-bestand gebruiken**. Dit is het XML-bestand waarin specifieke blobs en Azure-bestanden worden opgegeven die u wilt exporteren. Als u het XML-bestand wilt toevoegen, selecteert u **hier klikken om een XML-bestand te selecteren**.
     ![XML-bestand](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-01.png)

2. Selecteer **+ container** om een container te maken.
    ![XML-bestand](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-02.png)

3. Voeg op het tabblad **nieuwe container** die aan de rechter kant van de Azure Portal, een naam voor de container toe. De naam moet een kleine letter zijn en u kunt cijfers en streepjes '-' bevatten. Selecteer vervolgens het **niveau Public Access** in de vervolg keuzelijst. U wordt aangeraden **particuliere (niet-anonieme toegang)** te kiezen om te voor komen dat anderen toegang krijgen tot uw gegevens. Zie [toegangs machtigingen voor containers](../storage/blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)voor meer informatie over toegangs niveaus voor containers.

   ![XML-bestand](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-04.png)

4. Selecteer **Maken**.

   ![XML-bestand](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-07.png)

   Als uw container is gemaakt, wordt het volgende bericht weer gegeven:

   ![XML-bestand](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-09.png)

5. Selecteer de container die u hebt gemaakt en dubbel klik erop.

   ![XML-bestand](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-08.png)

6. Door te dubbel klikken op de container verschijnt de weer gave container eigenschappen. Nu wilt u het XML-bestand dat de lijst met blobs en/of Azure-bestanden bevat die u wilt exporteren, koppelen (of naar). Selecteer **Uploaden**.

   ![XML-bestand](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-10c.png)

7. Het XML-bestand is toegevoegd aan de container. Alleen blobs en Azure-bestanden die u in deze XML hebt opgegeven, worden geëxporteerd.

   ![XML-bestand](media/data-box-deploy-export-ordered/azure-data-box-export-sms-use-xml-file-12.png)

## <a name="track-the-order"></a>De bestelling volgen

Nadat u uw bestelling hebt geplaatst, kunt u de status van de bestelling volgen via de Azure-portal. Ga naar uw Data Box-bestelling en vervolgens naar **Overzicht** om de status te bekijken. In de portal ziet u dat de bestelling de status **Besteld** heeft.

Wanneer de voor bereiding van het apparaat is voltooid, begint de gegevens kopie van de geselecteerde opslag accounts. In de portal wordt de volg orde van de status van het kopiëren van de **gegevens** weer gegeven.

![Verwerkte export order Data Box](media/data-box-deploy-export-ordered/azure-data-box-export-15b.png)

Data Box kopieert gegevens uit de bron-Storage-account (s). Wanneer het kopiëren van de gegevens is voltooid, wordt Data Box vergrendeld en wordt de volg orde van de status **kopie voltooid** weer gegeven in de portal.

![Het kopiëren van de gegevens Data Box het exporteren is voltooid](media/data-box-deploy-export-ordered/azure-data-box-export-15c.png)

Als het apparaat niet beschikbaar is, wordt er een melding weer gegeven. Als het apparaat wel beschikbaar is, identificeert Microsoft het apparaat dat moet worden verzonden en bereidt Microsoft de verzending voor. Tijdens de voor bereiding van het apparaat worden de volgende acties uitgevoerd:

* Voor elk opslagaccount dat aan het apparaat is gekoppeld, worden SMB-shares gemaakt.
* Voor elke share worden toegangsreferenties zoals een gebruikersnaam en wachtwoord gegenereerd.
* Het apparaat is vergrendeld en kan alleen worden geopend met het wacht woord voor ontgrendelen van het apparaat. Als u het wacht woord wilt ophalen, moet u zich aanmelden bij uw Azure Portal-account en de details van het **apparaat**selecteren.

Micro soft bereidt vervolgens uw apparaat voor en verzendt dit via een regionale luchtvaart maatschappij. U ontvangt uw volgnummer zodra het apparaat is verzonden. In de portal wordt bestelling weergegeven met de status **Verzonden**.

![Data Box export order verzonden](media/data-box-deploy-export-ordered/azure-data-box-export-16.png)

Als self-managed Shipping is geselecteerd, ontvangt u een e-mail melding met de volgende stappen wanneer het apparaat gereed is om te worden opgenomen in het Data Center. Zie voor meer informatie over [zelf-beheerde](data-box-portal-customer-managed-shipping.md)verzen ding.

![Zelf-beheerde verzen ding gereed voor ophalen](media/data-box-deploy-export-ordered/azure-data-box-export-17.png)

## <a name="cancel-the-order"></a>De bestelling annuleren

Als u deze bestelling wilt annuleren, gaat u in de Azure-portal naar **Overzicht** en selecteert u **Annuleren** in de opdrachtbalk.

Nadat u een bestelling hebt geplaatst, kunt u deze op elk gewenst moment annuleren voordat de order wordt verwerkt.

Als u een geannuleerde bestelling wilt verwijderen, gaat u naar **Overzicht** en selecteert u **Verwijderen** in de opdrachtbalk.

## <a name="sample-xml-file"></a>XML-voorbeeld bestand

De volgende XML-code toont een voor beeld van BLOB-namen, blob-voor voegsels en Azure-bestanden in de XML-indeling die de export volgorde gebruikt wanneer u de optie **XML-bestand gebruiken** selecteert:

```xml
<?xml version="1.0" encoding="utf-8"?>
   <!-- BlobList/prefix/Container list for Blob storage for export  -->
   <BlobList>
      <BlobPath>/8tbpageblob/8tbpageblob/8tbpageblob</BlobPath>
      <BlobPathPrefix>/blockblob4dot75tbdata/</BlobPathPrefix>
      <BlobPathPrefix>/1tbfilepageblob</BlobPathPrefix>
      <BlobPathPrefix>/1tbfile/</BlobPathPrefix>
      <BlobPathPrefix>/8mbfiles/</BlobPathPrefix>
      <BlobPathPrefix>/64mbfiles/</BlobPathPrefix>
   </BlobList>
   <!-- FileList/prefix/Share list for Azure File storage for export  -->
   <AzureFileList>
      <FilePathPrefix>/64mbfiles/</FilePathPrefix>
      <FilePathPrefix>/4mbfiles/prefix2/subprefix</FilePathPrefix>
      <FilePathPrefix>/1tbfile/prefix</FilePathPrefix>
   </AzureFileList>
```

Enkele belang rijke punten ten opzichte van XML-bestanden:

* XML-tags zijn hoofdletter gevoelig en moeten exact overeenkomen zoals opgegeven in bovenstaand voor beeld.
* Openings-en afsluit Tags moeten overeenkomen.
* Onjuiste XML-codes of-opmaak kunnen leiden tot een fout bij het exporteren van gegevens.
* Er worden geen gegevens geëxporteerd als de BLOB en/of het voor voegsel van het bestand ongeldig zijn.

### <a name="examples-of-valid-blob-paths"></a>Voor beelden van geldige BLOB-paden

De volgende tabel bevat voor beelden van geldige BLOB-paden:

   | Kiezer | BLOB-pad | Beschrijving |
   | --- | --- | --- |
   | Begint met |/ |Exporteert alle blobs in het opslag account |
   | Begint met |/$root/ |Exporteert alle blobs in de basis container |
   | Begint met |/containers |Exporteert alle blobs in een container die begint met voorvoegsel **containers** |
   | Begint met |/container-name/ |Exporteert alle blobs in container **container-name** |
   | Begint met |/container-name/prefix |Exporteert alle blobs in container **container naam** die met het **voor voegsel beginnen** |
   | Gelijk aan |$root/logo.bmp |Exporteert BLOB- **logo.bmp** in de hoofd container |
   | Gelijk aan |8tbpageblob/mydata.txt |Exporteert BLOB- **mydata.txt** in container **8tbpageblob** |

## <a name="sample-log-files"></a>Voorbeeld logboek bestanden

In deze sectie vindt u voor beelden van logboek bestanden die tijdens het exporteren worden gegenereerd. De fouten logboeken worden automatisch gegenereerd. Als u het uitgebreide logboek bestand wilt genereren, moet u **uitgebreide logboek registratie** in azure Portal selecteren bij het configureren van de export order.
Zie [Logboeken kopiëren](data-box-deploy-export-copy-data.md#copy-data-from-data-box)voor meer informatie over kopie en uitgebreide Logboeken.

<!-- ### Verbose log

The following log files show examples of verbose logging when you select **Include verbose log**:

```xml
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454"></File>
<File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812"></File>
<File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820"></File>
```

### Copy logs

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box). -->

<!-- The following xml shows an example of the copy log when the export is successful:

```xml
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
    <TotalFiles_Blobs>27</TotalFiles_Blobs>
    <FilesErrored>0</FilesErrored>
</CopyLog>
```

For more information regarding copy logs, see [Copy logs](data-box-deploy-export-copy-data.md#copy-data-from-data-box).

The following xml shows an example of the copy log when the export has errors:

```xml
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>
``` -->

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
>
> * Vereisten voor exporteren
> * Een Data Box voor het exporteren best Ellen
> * De export volgorde bijhouden
> * De export volgorde annuleren

Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box instelt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box instellen](./data-box-deploy-set-up.md)
