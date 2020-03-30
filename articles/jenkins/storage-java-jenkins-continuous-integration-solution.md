---
title: Azure Storage gebruiken met een Jenkins-oplossing voor continue integratie
description: In deze zelfstudie ziet u hoe u de Azure blob-service gebruiken als opslagplaats voor buildartefacten die zijn gemaakt door een jenkins-oplossing voor continue integratie.
keywords: jenkins, azure, devops, opslag, cicd
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: df1d59c40fd09fb055db9d7622d86ff9c82991b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624681"
---
# <a name="using-azure-storage-with-a-jenkins-continuous-integration-solution"></a>Azure Storage gebruiken met een Jenkins-oplossing voor continue integratie

In dit artikel wordt uitgelegd hoe blobopslag kan worden gebruikt als opslagplaats van buildartefacten die zijn gemaakt door een CI-oplossing (Jenkins continuous integration) of als een bron van downloadbare bestanden die in een buildproces kunnen worden gebruikt. Een van de scenario's waar u deze oplossing nuttig zou vinden, is wanneer u codeert in een flexibele ontwikkelomgeving (met Java of andere talen), builds worden uitgevoerd op basis van continue integratie en u een opslagplaats nodig hebt voor uw buildartefacten, zodat u ze bijvoorbeeld delen met andere leden van de organisatie, uw klanten of een archief bijhouden. Een ander scenario is wanneer uw buildtaak zelf andere bestanden vereist, bijvoorbeeld afhankelijkheden die moeten worden gedownload als onderdeel van de build-invoer.

In deze zelfstudie gebruikt u de Azure Storage Plugin voor Jenkins CI die door Microsoft beschikbaar is gesteld.

## <a name="jenkins-overview"></a>Jenkins overzicht

Jenkins maakt continue integratie van een softwareproject mogelijk door dat ontwikkelaars hun codewijzigingen eenvoudig kunnen integreren en builds automatisch en vaak hebben geproduceerd, waardoor de productiviteit van de ontwikkelaars wordt verhoogd. Builds zijn versieen, en bouwen artefacten kunnen worden geüpload naar verschillende repositories. In dit artikel ziet u hoe u Azure blob-opslag gebruiken als opslagplaats van de buildartefacten. Het zal ook laten zien hoe afhankelijkheden te downloaden van Azure blob opslag.

Meer informatie over Jenkins is te vinden op [Meet Jenkins](https://wiki.jenkins-ci.org/display/JENKINS/Meet+Jenkins).

## <a name="benefits-of-using-the-blob-service"></a>Voordelen van het gebruik van de Blob-service

Voordelen van het gebruik van de Blob-service om uw agile ontwikkelbuildartefacten te hosten, zijn:

* Hoge beschikbaarheid van uw buildartefacten en/of downloadbare afhankelijkheden.
* Prestaties wanneer uw Jenkins CI-oplossing uw buildartefacten uploadt.
* Prestaties wanneer uw klanten en partners uw buildartefacten downloaden.
* Controle over het beleid voor gebruikerstoegang, met een keuze tussen anonieme toegang, toegang tot gedeelde toegang tot gedeelde toegang tot delen, privétoegang, enz.

## <a name="prerequisites"></a>Vereisten

* Een jenkins continue integratie oplossing.
  
    Als u momenteel geen Jenkins CI-oplossing hebt, u een Jenkins CI-oplossing uitvoeren met behulp van de volgende techniek:
  
  1. Download jenkins.war op een door Java <https://jenkins-ci.org>ingeschakelde machine van .
  2. Op een opdrachtprompt die wordt geopend voor de map die jenkins.war bevat, voer je uit:
     
      `java -jar jenkins.war`

  3. Open in uw `http://localhost:8080/` browser het Jenkins-dashboard, waarmee u de plug-in Azure Storage installeert en configureert.
     
      Terwijl een typische Jenkins CI-oplossing zou worden ingesteld om te draaien als een service, het uitvoeren van de Jenkins oorlog op de command line zal voldoende zijn voor deze tutorial.
* Een Azure-account. U zich aanmelden voor <https://www.azure.com>een Azure-account op.
* Een Azure Storage-account. Als u nog geen opslagaccount hebt, u er een maken met de stappen bij [Een opslagaccount maken.](../storage/common/storage-account-create.md)
* Vertrouwdheid met de Jenkins CI-oplossing wordt aanbevolen, maar niet vereist, omdat de volgende inhoud een basisvoorbeeld gebruikt om u de stappen te laten zien die nodig zijn bij het gebruik van de Blob-service als opslagplaats voor Jenkins CI-buildartefacten.

## <a name="how-to-use-the-blob-service-with-jenkins-ci"></a>De Blob-service gebruiken met Jenkins CI
Als u de Blob-service met Jenkins wilt gebruiken, moet u de plug-in Azure Storage installeren, de plug-in configureren om uw opslagaccount te gebruiken en vervolgens een actie na het bouwen maken die uw buildartefacten uploadt naar uw opslagaccount. Deze stappen worden beschreven in de volgende secties.

## <a name="how-to-install-the-azure-storage-plugin"></a>De plug-in Azure Storage installeren
1. Selecteer **Jenkins beheren**in het Jenkins-dashboard .
2. Selecteer **op** de pagina Jenkins beheren de optie **Plug-ins beheren**.
3. Selecteer het tabblad **Beschikbaar**.
4. Schakel in de sectie **Artefact-uploaders** **de plug-in Microsoft Azure Storage in**.
5. Selecteer **Installeren zonder opnieuw opstarten** of **Nu downloaden en installeren na opnieuw opstarten.**
6. Herstart Jenkins.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>De plug-in Azure Storage configureren om uw opslagaccount te gebruiken
1. Selecteer **Jenkins beheren**in het Jenkins-dashboard .
2. Selecteer **Op** de pagina Jenkins beheren de optie **Systeem configureren**.
3. Ga als een volgende in de sectie **Microsoft Azure Storage Account Configuration:**
   1. Voer de naam van uw opslagaccount in, die u verkrijgen via de [Azure-portal.](https://portal.azure.com)
   2. Voer uw opslagaccountsleutel in, ook verkrijgbaar via de [Azure-portal.](https://portal.azure.com)
   3. Gebruik de standaardwaarde voor **de URL van Blob Service Endpoint** als u de globale Azure-cloud gebruikt. Als u een andere Azure-cloud gebruikt, gebruikt u het eindpunt zoals opgegeven in de [Azure-portal](https://portal.azure.com) voor uw opslagaccount. 
   4. Selecteer **Opslagreferenties valideren** om uw opslagaccount te valideren. 
   5. [Optioneel] Als u extra opslagaccounts hebt die u beschikbaar wilt stellen aan uw Jenkins CI, selecteert u **Meer opslagaccounts toevoegen**.
   6. Selecteer **Opslaan** om uw instellingen op te slaan.

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Een actie na het bouwen maken waarmee je buildartefacten naar je opslagaccount wordt geüpload
Voor educatieve doeleinden moet u eerst een taak maken die meerdere bestanden maakt en vervolgens de actie na het bouwen toevoegen om de bestanden naar uw opslagaccount te uploaden.

1. Selecteer Nieuw **item**in het Jenkins-dashboard .
2. Geef de functie **MyJob**een naam, selecteer **Een softwareproject in vrije stijl**bouwen en selecteer VERVOLGENS **OK**.
3. Selecteer in het gedeelte **Bouwen** van de taakconfiguratie de optie **Buildstap toevoegen** en selecteer **De opdracht Windows-batch uitvoeren**.
4. Gebruik in **Opdracht**de volgende opdrachten:

    ```   
    md text
    cd text
    echo Hello Azure Storage from Jenkins > hello.txt
    date /t > date.txt
    time /t >> date.txt
    ```

5. Selecteer in de sectie Acties na het bouwen van de taakconfiguratie de optie Actie na het bouwen toevoegen en selecteer **Artefacten uploaden naar Azure** **Post-build Actions** **Blob-opslag** .
6. Selecteer **voor de naam van het opslagaccount**het opslagaccount dat u wilt gebruiken.
7. Geef **voor Containernaam**de containernaam op. (De container wordt gemaakt als deze nog niet bestaat wanneer de buildartefacten worden geüpload.) U omgevingsvariabelen gebruiken, dus `${JOB_NAME}` voer in dit voorbeeld de containernaam in.
   
    **Tip**
   
    Onder de sectie **Command** waarin u een script hebt ingevoerd voor **de batchopdracht Windows uitvoeren,** is een koppeling naar de omgevingsvariabelen die door Jenkins worden herkend. Selecteer die koppeling om de omgevingsvariabele namen en beschrijvingen te leren. Omgevingsvariabelen die speciale tekens bevatten, zoals de **BUILD_URL** omgevingsvariabele, zijn niet toegestaan als containernaam of algemeen virtueel pad.
8. Selecteer Voor dit voorbeeld **standaard nieuwe container openbaar maken.** (Als u een privécontainer wilt gebruiken, moet u een handtekening voor gedeelde toegang maken om toegang toe te staan, die buiten het bereik van dit artikel valt. Meer informatie over handtekeningen voor gedeelde toegang vindt u [bij Het gebruik van Gedeelde Toegangshandtekeningen (SAS) (SAS).](../storage/common/storage-sas-overview.md)
9. [Optioneel] Selecteer Container schoonmaken voordat u **uploadt** als u wilt dat de container wordt gewist van de inhoud voordat bouwartefacten worden geüpload (laat deze ongecontroleerd achter als u de inhoud van de container niet wilt schoonmaken).
10. Voer **voor Lijst met artefacten in**om te uploaden `text/*.txt`.
11. Voer **voor gemeenschappelijke virtuele pad voor geüploade artefacten**voor het gebruik van deze zelfstudie . `${BUILD\_ID}/${BUILD\_NUMBER}`
12. Selecteer **Opslaan** om uw instellingen op te slaan.
13. Selecteer in het Jenkins-dashboard **Build Now** om **MyJob**uit te voeren . Controleer de console-uitvoer op status. Statusberichten voor Azure-opslag worden opgenomen in de console-uitvoer wanneer de actie na de build wordt gestart met het uploaden van buildartefacten.
14. Na succesvolle voltooiing van de taak, u de artefacten bouwen onderzoeken door het openen van de openbare blob.
    1. Meld u aan bij [Azure Portal](https://portal.azure.com).
    2. Selecteer **Opslag**.
    3. Selecteer de naam van het opslagaccount die u voor Jenkins hebt gebruikt.
    4. Selecteer **Containers**.
    5. Selecteer de container met de naam **myjob**, de kleine versie van de taaknaam die u hebt toegewezen toen u de Jenkins-taak hebt gemaakt. Containernamen en blobnamen zijn kleine letters (en hoofdlettergevoelig) in Azure-opslag. In de lijst met blobs voor de container met de naam **myjob,** moet u **hello.txt** en **date.txt**zien. Kopieer de URL voor een van deze items en open deze in uw browser. U ziet het tekstbestand dat is geüpload als een build-artefact.

Per taak kan slechts één actie na de build worden gemaakt die artefacten uploadt naar Azure blob-opslag. De actie na het bouwen om artefacten te uploaden naar Azure blob-opslag kan verschillende bestanden (inclusief jokertekens) en paden naar bestanden binnen Lijst met artefacten opgeven **om te uploaden** met behulp van een puntkomma als scheidingsteken. Als uw Jenkins-build bijvoorbeeld JAR-bestanden en TXT-bestanden produceert in de **buildmap** van uw werkruimte en u beide wilt uploaden naar `build/\*.jar;build/\*.txt`Azure blob-opslag, gebruikt u de volgende waarde voor de optie Lijst met **artefacten om te uploaden:** . U ook syntaxis met dubbele dubbele dubbele punten gebruiken om een pad op te geven dat in de blobnaam moet worden gebruikt. Als u bijvoorbeeld wilt dat de JARs worden geüpload met **binaire bestanden** in het blobpad en de TXT-bestanden die worden geüpload met behulp van **berichten** in het blobpad, gebruikt u de volgende waarde voor de optie Lijst **met artefacten uploaden:** `build/\*.jar::binaries;build/\*.txt::notices`.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Een buildstap maken die wordt gedownload van Azure blob-opslag
De volgende stappen illustreren om een buildstap te configureren om items uit Azure blob-opslag te downloaden, wat handig is als u items in uw build wilt opnemen. Een voorbeeld van het gebruik van dit patroon zijn JARs die u mogelijk wilt blijven gebruiken in Azure blob-opslag.

1. Selecteer in het gedeelte **Bouwen** van de taakconfiguratie de optie **Buildstap toevoegen** en selecteer **Downloaden uit Azure Blob-opslag**.
2. Selecteer **voor de naam van het opslagaccount**het opslagaccount dat u wilt gebruiken.
3. Geef **voor Containernaam**de naam op van de container met de blobs die u wilt downloaden. U omgevingsvariabelen gebruiken.
4. Geef voor **blobnaam**de blobnaam op. U omgevingsvariabelen gebruiken. U ook een sterretje gebruiken als een wildcard nadat u de eerste letter(en) van de blobnaam hebt opgegeven. **Project\\*** geeft bijvoorbeeld alle blobs op waarvan de namen met **project**beginnen .
5. [Optioneel] Geef **voor Pad downloaden**het pad op op de Jenkins-machine waar u bestanden wilt downloaden uit Azure blob-opslag. Omgevingsvariabelen kunnen ook worden gebruikt. (Als u geen waarde voor **downloadpad**geeft, worden de bestanden uit Azure blob-opslag gedownload naar de werkruimte van de taak.)

Als u extra items hebt die u wilt downloaden van Azure blob-opslag, u extra buildstappen maken.

Nadat u een build hebt uitgevoerd, u de uitvoer van de buildgeschiedenisconsole controleren of naar uw downloadlocatie kijken om te zien of de blobs die u verwacht, zijn gedownload.  

## <a name="components-used-by-the-blob-service"></a>Componenten die worden gebruikt door de Blob-service
In deze sectie vindt u een overzicht van de klodderservicecomponenten.

* **Opslagaccount:** alle toegang tot Azure Storage gebeurt via een opslagaccount. Een opslagaccount is het hoogste niveau van de naamruimte voor toegang tot blobs. Een account kan een onbeperkt aantal containers bevatten, zolang de totale grootte onder de 100 TB ligt.
* **Container:** een container biedt een groepering van een reeks blobs. Alle blobs moeten zich in een container bevinden. Een account kan een onbeperkt aantal containers bevatten. Een container kan een onbeperkt aantal blobs bevatten.
* **Blob:** een bestand van elk type en elke grootte. Er zijn twee typen blobs die kunnen worden opgeslagen in Azure Storage: blok- en paginablobs. De meeste bestanden zijn blokblobs. Een blob met één blok kan maximaal 200 GB groot zijn. Deze zelfstudie maakt gebruik van blokblobs. Paginablobs, een ander blobtype, kunnen maximaal 1 TB groot zijn en zijn efficiënter wanneer bereiken van bytes in een bestand vaak worden gewijzigd. Zie [Blobs blokkeren, Blobs en Paginablobs begrijpen](https://msdn.microsoft.com/library/azure/ee691964.aspx)voor meer informatie over blobs.
* **URL-indeling**: Blobs zijn adresseerbaar met de volgende URL-indeling:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    (De bovenstaande indeling is van toepassing op de wereldwijde Azure-cloud. Als u een andere Azure-cloud gebruikt, gebruikt u het eindpunt in de [Azure-portal](https://portal.azure.com) om uw URL-eindpunt te bepalen.)
  
    In de bovenstaande `storageaccount` indeling staat de naam `container_name` van uw opslagaccount, `blob_name` vertegenwoordigt de naam van uw container en vertegenwoordigt de naam van uw blob, respectievelijk. Binnen de containernaam u meerdere paden hebben, **/** gescheiden door een voorwaartse slash,. De voorbeeldcontainernaam die voor deze zelfstudie wordt gebruikt, was **MyJob**en **${BUILD\_\_ID}/${BUILD NUMBER}** werd gebruikt voor het algemene virtuele pad, wat resulteert in een URL van het volgende formulier:
  
    `http://example.blob.core.windows.net/myjob/2014-04-14_23-57-00/1/hello.txt`

## <a name="troubleshooting-the-jenkins-plugin"></a>Problemen met de Jenkins-invoegtoepassing oplossen

Als u bugs tegenkomt met de Jenkins-plug-ins, dient u een probleem op in de [Jenkins JIRA](https://issues.jenkins-ci.org/) voor de specifieke component.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Jenkins op Azure](/azure/Jenkins/)