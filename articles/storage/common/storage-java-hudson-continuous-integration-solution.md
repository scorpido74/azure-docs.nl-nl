---
title: Hoe hudson te gebruiken met Blob-opslag | Microsoft Documenten
description: Beschrijft hoe je Hudson gebruiken met Azure Blob-opslag als opslagplaats voor buildartefacten.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: a89439f49dd53f09d5cd40be0bf2e4981e9235d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201382"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Azure Storage gebruiken met een Hudson Continuous Integration-oplossing
## <a name="overview"></a>Overzicht
De volgende informatie laat zien hoe blob-opslag te gebruiken als een opslagplaats van buildartefacten die zijn gemaakt door een CI-oplossing (Hudson Continuous Integration) of als een bron van downloadbare bestanden die in een buildproces kunnen worden gebruikt. Een van de scenario's waar je dit nuttig zou vinden is wanneer je codeert in een flexibele ontwikkelomgeving (met Java of andere talen), builds worden uitgevoerd op basis van continue integratie en je een repository nodig hebt voor je build artefacten, zodat je zou kunnen, Deel ze bijvoorbeeld met andere leden van de organisatie, uw klanten of onderhoud een archief.  Een ander scenario is wanneer uw buildtaak zelf andere bestanden vereist, bijvoorbeeld afhankelijkheden die moeten worden gedownload als onderdeel van de build-invoer.

In deze zelfstudie gebruikt u de Azure Storage-plug-in voor Hudson CI die door Microsoft beschikbaar is gesteld.

## <a name="introduction-to-hudson"></a>Inleiding tot Hudson
Hudson maakt continue integratie van een softwareproject mogelijk door dat ontwikkelaars hun codewijzigingen eenvoudig kunnen integreren en builds automatisch en vaak hebben geproduceerd, waardoor de productiviteit van de ontwikkelaars wordt verhoogd. Builds zijn versieen, en bouwen artefacten kunnen worden geüpload naar verschillende repositories. In dit artikel wordt uitgelegd hoe u Azure Blob-opslag gebruiken als opslagplaats van de buildartefacten. Het toont ook hoe u afhankelijkheden downloaden van Azure Blob-opslag.

Meer informatie over Hudson is te vinden bij [Meet Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Voordelen van het gebruik van de Blob-service
Voordelen van het gebruik van de Blob-service om uw agile ontwikkelbuildartefacten te hosten, zijn:

* Hoge beschikbaarheid van uw buildartefacten en/of downloadbare afhankelijkheden.
* Prestaties wanneer uw Hudson CI-oplossing uw buildartefacten uploadt.
* Prestaties wanneer uw klanten en partners uw buildartefacten downloaden.
* Controle over het beleid voor gebruikerstoegang, met een keuze tussen anonieme toegang, toegang tot gedeelde toegang tot gedeelde toegang tot delen, privétoegang, enz.

## <a name="prerequisites"></a>Vereisten
U hebt het volgende nodig om de Blob-service te gebruiken met uw Hudson CI-oplossing:

* Een Hudson Continuous Integration oplossing.
  
    Als u momenteel geen Hudson CI-oplossing hebt, u een Hudson CI-oplossing uitvoeren met behulp van de volgende techniek:
  
  1. Download op een door Java ingeschakelde machine [het Hudson WAR-bestand.](https://www.eclipse.org/hudson/download.php)
  2. Op een commando prompt die wordt geopend voor de map die de Hudson WAR bevat, voer de Hudson WAR. Als u bijvoorbeeld versie 3.1.2 hebt gedownload:
     
      `java -jar hudson-3.1.2.war`

  3. Open in `http://localhost:8080/`uw browser . Dit opent het Hudson dashboard.
  4. Bij het eerste gebruik van Hudson, voltooi de eerste setup op `http://localhost:8080/`.
  5. Nadat u de eerste installatie hebt voltooid, annuleert u de lopende instantie van de `http://localhost:8080/`Hudson WAR, start u de Hudson WAR opnieuw en opent u het Hudson-dashboard opnieuw, waarmee u de Plug-in Azure Storage-plug-in installeert en configureert.
     
      Terwijl een typische Hudson CI oplossing zou worden opgezet om te draaien als een dienst, het runnen van de Hudson oorlog op de command line zal voldoende zijn voor deze tutorial.
* Een Azure-account. U zich aanmelden voor <https://www.azure.com>een Azure-account op.
* Een Azure Storage-account. Als u nog geen opslagaccount hebt, u er een maken met de stappen bij [Een opslagaccount maken.](../common/storage-account-create.md)
* Vertrouwdheid met de Hudson CI-oplossing wordt aanbevolen, maar niet vereist, omdat de volgende inhoud een basisvoorbeeld zal gebruiken om u de stappen te laten zien die nodig zijn bij het gebruik van de Blob-service als opslagplaats voor Hudson CI-buildartefacten.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Hoe de Blob-service te gebruiken met Hudson CI
Als u de Blob-service met Hudson wilt gebruiken, moet u de plug-in Azure Storage installeren, de plug-in configureren om uw opslagaccount te gebruiken en vervolgens een actie na het bouwen maken die uw buildartefacten uploadt naar uw opslagaccount. Deze stappen worden beschreven in de volgende secties.

## <a name="how-to-install-the-azure-storage-plugin"></a>De plug-in Azure Storage installeren
1. Klik in het Hudson-dashboard op **Hudson beheren**.
2. Klik op de pagina **Hudson beheren** op **Plug-ins beheren**.
3. Klik op het tabblad **Beschikbaar.**
4. Klik op **Anderen**.
5. Selecteer microsoft **Azure Storage-plug-in**in de sectie **Artefact-uploaders** .
6. Klik **op Installeren**.
7. Nadat de installatie is voltooid, start Hudson opnieuw op.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>De plug-in Azure Storage configureren om uw opslagaccount te gebruiken
1. Klik in het Hudson-dashboard op **Hudson beheren**.
2. Klik **op** de pagina Hudson beheren op **Systeem configureren**.
3. Ga als een volgende in de sectie **Microsoft Azure Storage Account Configuration:**
   
    a. Voer de naam van uw opslagaccount in, die u verkrijgen via de [Azure-portal.](https://portal.azure.com)
   
    b. Voer uw opslagaccountsleutel in, ook verkrijgbaar via de [Azure-portal.](https://portal.azure.com)
   
    c. Gebruik de standaardwaarde voor **de URL van Blob Service Endpoint** als u de globale Azure-cloud gebruikt. Als u een andere Azure-cloud gebruikt, gebruikt u het eindpunt zoals opgegeven in de [Azure-portal](https://portal.azure.com) voor uw opslagaccount.
   
    d. Klik **op Opslagreferenties valideren** om uw opslagaccount te valideren.
   
    e. [Optioneel] Als u extra opslagaccounts hebt die u beschikbaar wilt stellen aan uw Hudson CI, klikt u op **Meer opslagaccounts toevoegen**.
   
    f. Klik **op Opslaan** om uw instellingen op te slaan.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Een actie na het bouwen maken waarmee je buildartefacten naar je opslagaccount wordt geüpload
Voor instructiedoeleinden moeten we eerst een taak maken die meerdere bestanden maakt en vervolgens de actie na het bouwen toevoegen om de bestanden naar uw opslagaccount te uploaden.

1. Klik in het Dashboard van Hudson op **Nieuwe taak**.
2. Geef de functie **MyJob**een naam, klik op **Een softwaretaak in vrije stijl maken**en klik vervolgens op **OK**.
3. Klik in het gedeelte **Bouwen** van de taakconfiguratie op **Buildstap toevoegen** en kies **Windows batchopdracht uitvoeren**.
4. Gebruik in **Opdracht**de volgende opdrachten:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. Klik in het gedeelte Acties na het bouwen van de taakconfiguratie op **Artefacten uploaden naar Microsoft Azure** **Blob-opslag** .
6. Selecteer **voor Accountnaam opslag,** het opslagaccount dat u wilt gebruiken.
7. Geef **voor Containernaam**de containernaam op. (De container wordt gemaakt als deze nog niet bestaat wanneer de buildartefacten worden geüpload.) U omgevingsvariabelen gebruiken, dus voer in dit voorbeeld **${JOB_NAME}** in als containernaam.
   
    **Tip**
   
    Onder de sectie **Command** waarin u een script hebt ingevoerd voor **de batchopdracht Windows uitvoeren,** is een koppeling naar de omgevingsvariabelen die door Hudson worden herkend. Klik op die link om de omgevingsvariabele namen en beschrijvingen te leren. Omgevingsvariabelen die speciale tekens bevatten, zoals de **BUILD_URL** omgevingsvariabele, zijn niet toegestaan als containernaam of algemeen virtueel pad.
8. Klik standaard op **Nieuwe container openbaar maken** voor dit voorbeeld. (Als u een privécontainer wilt gebruiken, moet u een handtekening voor gedeelde toegang maken om toegang te verlenen. Dat valt buiten het toepassingsgebied van dit artikel. Meer informatie over handtekeningen voor gedeelde toegang vindt u [bij Het gebruik van Gedeelde Toegangshandtekeningen (SAS) (SAS).](storage-sas-overview.md)
9. [Optioneel] Klik op Container wissen voordat u **uploadt** als u wilt dat de container wordt gewist van de inhoud voordat bouwartefacten worden geüpload (laat deze ongecontroleerd achter als u de inhoud van de container niet wilt schoonmaken).
10. Als **u Lijst met artefacten wilt uploaden,** voert u **tekst/*.txt in**.
11. Voer voor **gemeenschappelijke virtuele pad voor geüploade artefacten** **${BUILD\_ID}/${BUILD\_NUMBER}** in .
12. Klik **op Opslaan** om uw instellingen op te slaan.
13. Klik in het Dashboard Van Hudson op **Nu bouwen** om **MyJob**uit te voeren. Controleer de console-uitvoer op status. Statusberichten voor Azure Storage worden opgenomen in de console-uitvoer wanneer de actie na de build wordt gestart met het uploaden van buildartefacten.
14. Na succesvolle voltooiing van de taak, u de artefacten bouwen onderzoeken door het openen van de openbare blob.
    
    a. Meld u aan bij [Azure Portal](https://portal.azure.com).
    
    b. Klik **op Opslag**.
    
    c. Klik op de naam van het opslagaccount die u voor Hudson hebt gebruikt.
    
    d. Klik op **Containers**.
    
    e. Klik op de container met de naam **myjob**, de kleine versie van de taaknaam die u hebt toegewezen toen u de Hudson-taak hebt gemaakt. Containernamen en blobnamen zijn kleine letters (en hoofdlettergevoelig) in Azure Storage. In de lijst met blobs voor de container met de naam **myjob** moet u **hello.txt** en **date.txt**zien. Kopieer de URL voor een van deze items en open deze in uw browser. U ziet het tekstbestand dat is geüpload als een build-artefact.

Per taak kan slechts één actie na de build worden gemaakt die artefacten uploadt naar Azure Blob-opslag. De actie na het bouwen om artefacten te uploaden naar Azure Blob-opslag kan verschillende bestanden (inclusief jokertekens) en paden naar bestanden binnen Lijst met artefacten opgeven **die moeten worden geüpload** met behulp van een puntkomma als scheidingsteken. Als uw Hudson-build bijvoorbeeld JAR-bestanden en TXT-bestanden produceert in de **buildmap** van uw werkruimte en u beide wilt uploaden naar Azure Blob-opslag, gebruikt u het volgende voor de lijst met artefacten om waarde **te uploaden:** **build/\*.jar;build/\*.txt**. U ook syntaxis met dubbele dubbele dubbele punten gebruiken om een pad op te geven dat in de blobnaam moet worden gebruikt. Als u bijvoorbeeld wilt dat de JARs worden geüpload met **binaries** in het blobpad en de TXT-bestanden die worden geüpload met behulp van **berichten** in het blobpad, gebruikt u het volgende voor de lijst met artefacten om waarde **te uploaden:** **build/\*.jar:binaries;build/\*.txt:notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Een buildstap maken die wordt gedownload van Azure Blob-opslag
In de volgende stappen wordt uitgelegd hoe u een buildstap configureert om items uit Azure Blob-opslag te downloaden. Dit is handig als u items in uw build wilt opnemen, bijvoorbeeld JARs die u in Azure Blob-opslag bewaart.

1. Klik in het gedeelte **Bouwen** van de taakconfiguratie op **Buildstap toevoegen** en kies **Downloaden uit Azure Blob-opslag**.
2. Selecteer **voor de naam van het opslagaccount**het opslagaccount dat u wilt gebruiken.
3. Geef **voor Containernaam**de naam op van de container met de blobs die u wilt downloaden. U omgevingsvariabelen gebruiken.
4. Geef voor **blobnaam**de blobnaam op. U omgevingsvariabelen gebruiken. U ook een sterretje gebruiken als een wildcard nadat u de eerste letter(en) van de blobnaam hebt opgegeven. **Project\\*** geeft bijvoorbeeld alle blobs op waarvan de namen met **project**beginnen .
5. [Optioneel] Geef **voor Downloadpad**het pad op op de Hudson-machine waar u bestanden wilt downloaden uit Azure Blob-opslag. Omgevingsvariabelen kunnen ook worden gebruikt. (Als u geen waarde voor **downloadpad**geeft, worden de bestanden uit Azure Blob-opslag gedownload naar de werkruimte van de taak.)

Als u extra items hebt die u wilt downloaden uit Azure Blob-opslag, u extra buildstappen maken.

Nadat u een build hebt uitgevoerd, u de uitvoer van de buildgeschiedenisconsole controleren of naar uw downloadlocatie kijken om te zien of de blobs die u verwacht, zijn gedownload.

## <a name="components-used-by-the-blob-service"></a>Componenten die worden gebruikt door de Blob-service
Hieronder ziet u een overzicht van de klodderservicecomponenten.

* **Opslagaccount:** Alle toegang tot Azure Storage gebeurt via een opslagaccount. Dit is het hoogste niveau van de naamruimte voor toegang tot blobs. Een account kan een onbeperkt aantal containers bevatten, zolang de totale grootte onder de 100 TB ligt.
* **Container:** een container biedt een groepering van een reeks blobs. Alle blobs moeten zich in een container bevinden. Een account kan een onbeperkt aantal containers bevatten. Een container kan een onbeperkt aantal blobs bevatten.
* **Blob:** een bestand van elk type en elke grootte. Er zijn twee typen blobs die kunnen worden opgeslagen in Azure Storage: blok- en paginablobs. De meeste bestanden zijn blokblobs. Een blob met één blok kan maximaal 200 GB groot zijn. Deze zelfstudie maakt gebruik van blokblobs. Paginablobs, een ander blobtype, kunnen maximaal 1 TB groot zijn en zijn efficiënter wanneer bereiken van bytes in een bestand vaak worden gewijzigd. Zie [Blobs blokkeren, Blobs en Paginablobs begrijpen](https://msdn.microsoft.com/library/azure/ee691964.aspx)voor meer informatie over blobs.
* **URL-indeling**: Blobs zijn adresseerbaar met de volgende URL-indeling:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (De bovenstaande indeling is van toepassing op de wereldwijde Azure-cloud. Als u een andere Azure-cloud gebruikt, gebruikt u het eindpunt in de [Azure-portal](https://portal.azure.com) om uw URL-eindpunt te bepalen.)
  
    In de bovenstaande `storageaccount` indeling staat de naam `container_name` van uw opslagaccount, `blob_name` vertegenwoordigt de naam van uw container en vertegenwoordigt de naam van uw blob, respectievelijk. Binnen de containernaam u meerdere paden hebben, **/** gescheiden door een voorwaartse slash,. De naam van de voorbeeldcontainer in deze zelfstudie was **MyJob**en **${BUILD\_\_ID}/${BUILD NUMBER}** werd gebruikt voor het gemeenschappelijke virtuele pad, wat resulteerde in een URL van het volgende formulier:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Volgende stappen
* [Maak kennis met Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Azure Storage SDK voor Java](https://github.com/azure/azure-storage-java)
* [Azure Storage Client SDK-referentie](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [REST-API voor Azure Storage-services](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog van azure-opslagteam](https://blogs.msdn.com/b/windowsazurestorage/)

Voor meer informatie gaat u naar [Azure voor Java-ontwikkelaars](/java/azure).
