---
title: Azure Storage gebruiken met een Jenkins-oplossing voor continue integratie
description: In deze zelf studie ziet u hoe u de Azure Blob-service gebruikt als opslag plaats voor het bouwen van artefacten die zijn gemaakt met een Jenkins-oplossing voor continue integratie.
keywords: Jenkins, azure, devops, Storage, cicd
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: df1d59c40fd09fb055db9d7622d86ff9c82991b8
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624681"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Azure Storage gebruiken met een Jenkins-oplossing voor continue integratie

In dit artikel wordt beschreven hoe u Blob Storage kunt gebruiken als opslag plaats voor constructie artefacten die zijn gemaakt met een CI-oplossing (Jenkins Continuous Integration) of als bron van Download bare bestanden die moeten worden gebruikt in een bouw proces. Een van de scenario's waarin deze oplossing nuttig zou zijn, is wanneer u codeert in een flexibele ontwikkel omgeving (met behulp van Java of andere talen), builds worden uitgevoerd op basis van doorlopende integratie en u hebt een opslag plaats nodig voor uw build-artefacten. u kunt ze bijvoorbeeld delen met andere organisatie leden, uw klanten of een archief onderhouden. Een ander scenario is wanneer uw bouw taak zelf andere bestanden vereist, zoals afhankelijkheden die moeten worden gedownload als onderdeel van de build-invoer.

In deze zelf studie maakt u gebruik van de Azure Storage-invoeg toepassing voor Jenkins CI die beschikbaar is gesteld door micro soft.

## <a name="jenkins-overview"></a>Overzicht van Jenkins

Jenkins maakt continue integratie van een software project mogelijk door ontwikkel aars in staat te stellen hun code wijzigingen eenvoudig te integreren en gebouwde builds automatisch en regel matig te laten produceren, waardoor de productiviteit van de ontwikkel aars wordt verhoogd. Builds zijn versie en het bouwen van artefacten kan worden geüpload naar verschillende opslag plaatsen. In dit artikel wordt beschreven hoe u Azure Blob Storage kunt gebruiken als opslag plaats voor de build-artefacten. Er wordt ook weer gegeven hoe u afhankelijkheden downloadt van Azure Blob-opslag.

Meer informatie over Jenkins vindt u op [Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Voor delen van het gebruik van de Blob service

Voor delen van het gebruik van de Blob service om uw flexibele ontwikkel artefacten te hosten, zijn onder andere:

* Hoge Beschik baarheid van uw build-artefacten en/of download bare afhankelijkheden.
* Prestaties wanneer uw Jenkins CI-oplossing uw build-artefacten uploadt.
* Prestaties wanneer uw klanten en partners uw build-artefacten downloaden.
* Controle over beleid voor gebruikers toegang, met een keuze tussen anonieme toegang, op verloop van gedeelde toegangs handtekeningen, persoonlijke toegang, enzovoort.

## <a name="prerequisites"></a>Vereisten

* Een Jenkins-oplossing voor continue integratie.
  
    Als u momenteel geen Jenkins CI-oplossing hebt, kunt u een Jenkins CI-oplossing uitvoeren met behulp van de volgende techniek:
  
  1. Down load Jenkins. war van <https://jenkins-ci.org>op een Java-computer.
  2. Voer bij een opdracht prompt die is geopend in de map die Jenkins. War bevat:
     
      `java -jar jenkins.war`

  3. Open in uw browser `http://localhost:8080/` om het Jenkins-dash board te openen. dit wordt gebruikt voor het installeren en configureren van de Azure Storage-invoeg toepassing.
     
      Hoewel een typische Jenkins CI-oplossing zo is ingesteld dat deze als een service wordt uitgevoerd, is het uitvoeren van de Jenkins War op de opdracht regel voldoende voor deze zelf studie.
* Een Azure-account. U kunt zich op <https://www.azure.com>registreren voor een Azure-account.
* Een Azure Storage-account. Als u nog geen opslag account hebt, kunt u er een maken met behulp van de stappen in [een opslag account maken](../storage/common/storage-account-create.md).
* De vertrouwdheid van de Jenkins CI-oplossing wordt aanbevolen, maar is niet vereist, omdat de volgende inhoud een basis voorbeeld gebruikt om u te laten zien welke stappen nodig zijn bij het gebruik van de Blob service als opslag plaats voor Jenkins CI-build-artefacten.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>Het Blob service gebruiken met Jenkins CI
Als u de Blob service wilt gebruiken met Jenkins, moet u de Azure Storage-invoeg toepassing installeren, de invoeg toepassing configureren voor het gebruik van uw opslag account en vervolgens een actie na het bouwen maken waarmee uw build-artefacten worden geüpload naar uw opslag account. Deze stappen worden beschreven in de volgende secties.

## <a name="how-to-install-the-azure-storage-plugin"></a>De Azure Storage-invoeg toepassing installeren
1. Selecteer in het Jenkins-dash board **Jenkins beheren**.
2. Selecteer op de pagina **Jenkins beheren** de optie **invoeg toepassingen beheren**.
3. Selecteer het tabblad **Beschikbaar**.
4. Controleer **Microsoft Azure Storage-invoeg toepassing**in de sectie **uploaders van artefacten** .
5. Selecteer **installeren zonder opnieuw** op te starten of **down load nu en installeer na opnieuw opstarten**.
6. Start Jenkins opnieuw.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>De Azure Storage-invoeg toepassing configureren voor het gebruik van uw opslag account
1. Selecteer in het Jenkins-dash board **Jenkins beheren**.
2. Selecteer op de pagina **Jenkins beheren** de optie **systeem configureren**.
3. In het gedeelte **configuratie van Microsoft Azure Storage-account** :
   1. Voer de naam van uw opslag account in, die u kunt verkrijgen via de [Azure Portal](https://portal.azure.com).
   2. Voer de sleutel voor het opslag account in, die ook kan worden opgehaald van de [Azure Portal](https://portal.azure.com).
   3. Gebruik de standaard waarde voor de URL van het **eind punt** van de BLOB-service als u de wereld wijde Azure-Cloud gebruikt. Als u een andere Azure-Cloud gebruikt, gebruikt u het eind punt zoals opgegeven in de [Azure Portal](https://portal.azure.com) voor uw opslag account. 
   4. Selecteer **opslag referenties valideren** om uw opslag account te valideren. 
   5. Beschrijving Als u extra opslag accounts hebt die u beschikbaar wilt maken voor uw Jenkins CI, selecteert u **meer opslag accounts toevoegen**.
   6. Selecteer **Opslaan** om uw instellingen op te slaan.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Het maken van een actie na het bouwen waarmee uw build-artefacten worden geüpload naar uw opslag account
Voor instructie doeleinden moet u eerst een taak maken die verschillende bestanden maakt, en vervolgens toevoegen aan de actie na het maken van de bewerking om de bestanden te uploaden naar uw opslag account.

1. Selecteer in het Jenkins-dash board **Nieuw item**.
2. Geef de taak de naam **MyJob**, selecteer **een gratis software project bouwen**en selecteer vervolgens **OK**.
3. Selecteer in de sectie **Build** van de taak configuratie de optie **Build-stap toevoegen** en selecteer **Windows batch-opdracht uitvoeren**.
4. Gebruik de volgende opdrachten in de **opdracht**:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. Selecteer in de sectie **acties na het maken** van de taak configuratie de **actie achteraf maken** en selecteer **artefacten uploaden naar Azure Blob-opslag**.
6. Selecteer bij **naam van opslag account**het opslag account dat u wilt gebruiken.
7. Geef voor **container name**de naam van de container op. (De container wordt gemaakt als deze nog niet bestaat wanneer de build-artefacten worden geüpload.) U kunt omgevings variabelen gebruiken, dus geef `${JOB_NAME}` als container naam op in dit voor beeld.
   
    **Tip**
   
    Onder de **opdracht** sectie waar u een script voor de **opdracht Windows Batch uitvoeren** hebt opgegeven, is een koppeling naar de omgevings variabelen die worden herkend door Jenkins. Selecteer deze koppeling voor meer informatie over de namen en beschrijvingen van omgevings variabelen. Omgevings variabelen die speciale tekens bevatten, zoals de omgevings variabele **BUILD_URL** , zijn niet toegestaan als container naam of algemeen virtueel pad.
8. Selecteer **nieuwe container standaard openbaar maken** voor dit voor beeld. (Als u een persoonlijke container wilt gebruiken, moet u een gedeelde toegangs handtekening maken om toegang toe te staan. dit valt buiten het bereik van dit artikel. Meer informatie over gedeelde toegangs handtekeningen kunt u vinden [met behulp van Shared Access signatures (SAS)](../storage/common/storage-sas-overview.md).)
9. Beschrijving Selecteer **container opschonen voordat u deze uploadt** als u wilt dat de container wordt gewist voordat er artefacten worden geüpload (laat dit selectie vakje uitgeschakeld als u de inhoud van de container niet wilt opschonen).
10. Voer `text/*.txt`in voor een **lijst met artefacten die u wilt uploaden**.
11. Voor het **algemene virtuele pad voor geüploade artefacten**voert u in het kader van deze zelf studie `${BUILD\_ID}/${BUILD\_NUMBER}`in.
12. Selecteer **Opslaan** om uw instellingen op te slaan.
13. Selecteer **nu samen stellen** in het Jenkins-dash board om **MyJob**uit te voeren. Bekijk de console-uitvoer voor status. Status berichten voor Azure Storage worden opgenomen in de uitvoer van de console wanneer de actie na het maken van builds artefacten begint te uploaden.
14. Wanneer de taak is voltooid, kunt u de constructie artefacten bekijken door de open bare BLOB te openen.
    1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
    2. Selecteer **Opslag**.
    3. Selecteer de naam van het opslag account dat u hebt gebruikt voor Jenkins.
    4. Selecteer **containers**.
    5. Selecteer de container met de naam **MyJob**. Dit is de kleine versie van de naam van de taak die u hebt toegewezen toen u de Jenkins-taak maakte. Container namen en BLOB-namen zijn kleine letters (en hoofdletter gevoelig) in azure Storage. In de lijst met blobs voor de container met de naam **MyJob**, moet **Hello. txt** en **date. txt**worden weer geven. Kopieer de URL voor een van deze items en open deze in uw browser. Het tekst bestand dat is geüpload als een build-artefact, wordt weer geven.

Er kan slechts één actie na het bouwen worden gemaakt waarmee artefacten naar Azure Blob-opslag kunnen worden geüpload per taak. De enkelvoudige actie na het maken van het uploaden van artefacten naar Azure Blob-opslag kan verschillende bestanden (inclusief joker tekens) en paden naar bestanden in een **lijst met artefacten opgeven die** met een punt komma als scheidings teken moeten worden geüpload. Als uw Jenkins-build bijvoorbeeld JAR-bestanden en TXT-bestanden in de map **Build** van uw werk ruimte produceert en u wilt uploaden naar Azure Blob Storage, gebruikt u de volgende waarde voor de **lijst met artefacten die u kunt uploaden** : `build/\*.jar;build/\*.txt`. U kunt ook de syntaxis met een dubbel punt gebruiken om een pad op te geven dat u wilt gebruiken in de naam van de blob. Als u bijvoorbeeld wilt dat de potten worden geüpload met behulp van **binaire bestanden** in het BLOB-pad en de txt-bestanden om te worden geüpload met behulp van **meldingen** in het BLOB-pad, gebruikt u de volgende waarde voor de **lijst met artefacten die u wilt uploaden** : `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Een build-stap maken die wordt gedownload vanuit Azure Blob-opslag
De volgende stappen laten zien hoe u een build-stap kunt configureren voor het downloaden van items uit Azure Blob-opslag. Dit is handig als u items in uw build wilt toevoegen. Een voor beeld van het gebruik van dit patroon is potten die u in Azure Blob-opslag wilt behouden.

1. Selecteer in de sectie **Build** van de taak configuratie de optie **Build-stap toevoegen** en selecteer **downloaden vanuit Azure Blob-opslag**.
2. Selecteer bij **naam van opslag account**het opslag account dat u wilt gebruiken.
3. Geef bij **container naam**de naam op van de container met de blobs die u wilt downloaden. U kunt omgevings variabelen gebruiken.
4. Geef de naam van de BLOB op voor de **blobnaam**. U kunt omgevings variabelen gebruiken. U kunt ook een asterisk gebruiken als Joker teken nadat u de eerste letter (s) van de naam van de BLOB hebt opgegeven. **Project\\** * geeft bijvoorbeeld alle blobs aan waarvan de namen beginnen met **project**.
5. Beschrijving Voor het **downloadmap**geeft u het pad op de Jenkins-computer op waar u bestanden wilt downloaden van Azure Blob-opslag. Omgevings variabelen kunnen ook worden gebruikt. (Als u geen waarde opgeeft voor het **Download traject**, worden de bestanden van Azure Blob-opslag gedownload naar de werk ruimte van de taak.)

Als er extra items zijn die u wilt downloaden vanuit Azure Blob-opslag, kunt u aanvullende build-stappen maken.

Nadat u een build hebt uitgevoerd, kunt u de uitvoer van de console build-geschiedenis controleren of de download locatie bekijken om te zien of de blobs die u verwacht, zijn gedownload.  

## <a name="components-used-by-the-blob-service"></a>Onderdelen die worden gebruikt door de Blob service
In deze sectie vindt u een overzicht van de Blob service-onderdelen.

* **Opslagaccount**: alle toegang tot Azure Storage vindt plaats via een opslagaccount. Een opslag account is het hoogste niveau van de naam ruimte voor toegang tot blobs. Een account kan een onbeperkt aantal containers bevatten, zolang de totale grootte minder is dan 100 TB.
* **Container**: een container bevat een groepering van een set blobs. Alle blobs moeten zich in een container bevinden. Een account kan een onbeperkt aantal containers bevatten. Een container kan een onbeperkt aantal blobs bevatten.
* **BLOB**: een bestand van elk type en elke grootte. Er zijn twee typen blobs die kunnen worden opgeslagen in Azure Storage: blok-en pagina-blobs. De meeste bestanden zijn blok-blobs. Eén blok-Blob kan Maxi maal 200 GB groot zijn. In deze zelf studie wordt gebruikgemaakt van blok-blobs. Pagina-blobs, een ander BLOB-type, kunnen Maxi maal 1 TB groot zijn, en zijn efficiënter wanneer een bereik van de bytes in een bestand regel matig wordt gewijzigd. Zie voor meer informatie over blobs ' [blok-blobs, toevoeg-blobs en pagina-blobs](https://msdn.microsoft.com/library/azure/ee691964.aspx)'.
* **URL-indeling**: blobs zijn adresseerbaar met behulp van de volgende URL-indeling:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (De bovenstaande notatie is van toepassing op de wereld wijde Azure-Cloud. Als u een andere Azure-Cloud gebruikt, gebruikt u het eind punt binnen de [Azure Portal](https://portal.azure.com) om het URL-eind punt te bepalen.)
  
    In de bovenstaande notatie geeft `storageaccount` de naam van uw opslag account, `container_name` de naam van de container en `blob_name` de naam van uw BLOB vertegenwoordigt. In de naam van de container kunt u meerdere paden hebben, gescheiden door een slash, **/** . De voor beeld-container naam die voor deze zelf studie wordt gebruikt, is **MyJob**en **$ {Build\_id}/$ {build\_Number}** is gebruikt voor het algemene virtuele pad, wat resulteert in de blob met een URL van de volgende vorm:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Problemen met de Jenkins-invoegtoepassing oplossen

Als u problemen ondervindt met de Jenkins-invoegtoepassingen, kunt u in [Jenkins JIRA](https://issues.jenkins-ci.org/) een ticket openen voor het specifieke onderdeel.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Jenkins op Azure](/azure/Jenkins/)