---
title: Gegevens en model verkennen op Windows
titleSuffix: Azure Data Science Virtual Machine
description: Voer taken voor gegevensverkenning en modellering uit op de Virtuele Machine van Windows Data Science.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 147f43148c0f804adf70f1a792ba1b8a772fdae4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294492"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Tien dingen die u doen op de Virtuele Windows Data Science-machine

De Windows Data Science Virtual Machine (DSVM) is een krachtige ontwikkelomgeving voor data science waar u taken voor gegevensverkenning en modellering uitvoeren. De omgeving is al gebouwd en gebundeld met verschillende populaire tools voor gegevensanalyse waarmee u eenvoudig aan de slag met uw analyse voor on-premises, cloud- of hybride implementaties. 

De DSVM werkt nauw samen met Azure-services. Het kan gegevens lezen en verwerken die al zijn opgeslagen op Azure, in Azure SQL Data Warehouse, Azure Data Lake, Azure Storage of Azure Cosmos DB. Het kan ook profiteren van andere analysetools, zoals Azure Machine Learning en Azure Data Factory.

In dit artikel leert u hoe u uw DSVM gebruiken om gegevenswetenschapstaken uit te voeren en te communiceren met andere Azure-services. Hier zijn enkele van de dingen die je doen op de DSVM:

- Verken gegevens en ontwikkel lokaal modellen op de DSVM met Microsoft Machine Learning Server en Python.
- Gebruik een Jupyter-notitieblok om te experimenteren met uw gegevens in een browser met python 2, Python 3 en Microsoft R. (Microsoft R is een bedrijfsklare versie van R die is ontworpen voor prestaties.)
- Implementeer modellen die zijn gebouwd via R en Python op Azure Machine Learning, zodat clienttoepassingen toegang hebben tot uw modellen met behulp van een eenvoudige webservice-interface.
- Beheer uw Azure-resources met behulp van de Azure-portal of PowerShell.
- Breid uw opslagruimte uit en deel grootschalige gegevenssets/code over uw hele team door een Azure Files-share te maken als een monteerbaar station op uw DSVM.
- Deel code met je team met GitHub. Toegang tot uw repository met behulp van de vooraf geïnstalleerde Git clients: Git Bash en Git GUI.
- Toegang tot Azure-gegevens- en analyseservices zoals Azure Blob-opslag, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse en Azure SQL Database.
- Maak rapporten en een dashboard met behulp van het Power BI-bureaublad-exemplaar dat vooraf is geïnstalleerd op de DSVM en implementeer ze in de cloud.
- Schaal uw DSVM dynamisch om aan de behoeften van uw project te voldoen.
- Installeer extra hulpprogramma's op uw virtuele machine.   

> [!NOTE]
> Voor veel van de in dit artikel vermelde services voor gegevensopslag en -analyse gelden extra gebruikskosten. Zie de [prijspagina van Azure voor](https://azure.microsoft.com/pricing/) meer informatie.
> 
> 

## <a name="prerequisites"></a>Vereisten

* U hebt een Azure-abonnement nodig. U [zich aanmelden voor een gratis proefperiode.](https://azure.microsoft.com/free/)
* Instructies voor het inrichten van een Virtuele Data Science-machine op de Azure-portal zijn beschikbaar in [Het maken van een virtuele machine.](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Gegevens verkennen en modellen ontwikkelen met Microsoft Machine Learning Server
U talen als R en Python gebruiken om uw gegevensanalyse direct op de DSVM uit te brengen.

Voor R u een IDE zoals RStudio gebruiken die te vinden is in het startmenu of op het bureaublad. Of u R Tools voor Visual Studio gebruiken. Microsoft heeft aanvullende bibliotheken bovenop de open-source CRAN R beschikbaar gesteld om schaalbare analyses mogelijk te maken en de mogelijkheid om gegevens te analyseren die groter zijn dan de geheugengrootte die is toegestaan in parallelle chunked-analyse. 

Voor Python kun je een IDE gebruiken zoals Visual Studio Community Edition, waarbij de Python Tools for Visual Studio (PTVS) extensie vooraf is geïnstalleerd. Standaard is alleen Python 3.6, de hoofd-Conda-omgeving, geconfigureerd op PTVS. Als u Anaconda Python 2.7 wilt inschakelen, neemt u de volgende stappen:

1. Maak aangepaste omgevingen voor elke versie door naar > **Python-omgevingen voor****Tools-tools** **Tools** > te gaan en vervolgens + **Aangepast** te selecteren in Visual Studio Community Edition.
1. Geef een beschrijving en stel het voorvoegselpad voor de omgeving in als **c:\anaconda\envs\python2** voor Anaconda Python 2.7.
1. Selecteer **Automatisch detecteren** > **toepassen** om de omgeving op te slaan.

Zie de [PTVS-documentatie](https://aka.ms/ptvsdocs) voor meer informatie over het maken van Python-omgevingen.

Nu bent u klaar om een nieuw Python-project te maken. Ga naar **File** > **New** > **Project** > **Python** en selecteer het type Python-toepassing dat u bouwt. U de Python-omgeving voor het huidige project instellen op de gewenste versie (Python 2.7 of 3.6) door met de rechtermuisknop op **Python-omgevingen** te klikken en vervolgens **Python-omgevingen toevoegen/verwijderen te**selecteren. Meer informatie over het werken met PTVS vindt u in de [productdocumentatie.](https://aka.ms/ptvsdocs)

## <a name="use-jupyter-notebooks"></a>Jupyter Notebook gebruiken
De Jupyter Notebook biedt een browser-gebaseerde IDE voor data-exploratie en modellering. U Python 2, Python 3 of R (zowel open source als Microsoft R Server) gebruiken in een Jupyter-laptop.

Als u het Jupyter-notitieblok wilt starten, selecteert u het pictogram **Jupyter-notitieblok** in het menu **Start** of op het bureaublad. In de opdrachtprompt DSVM u ```jupyter notebook``` de opdracht ook uitvoeren vanuit de map waar u bestaande notitieblokken hebt of waar u nieuwe notitieblokken wilt maken.  

Nadat u Jupyter hebt gestart, ziet u een map met een paar voorbeeldnotitieblokken die vooraf zijn verpakt in de DSVM. U kunt nu:

* Selecteer het notitieblok om de code te zien.
* Voer elke cel uit door Shift+Enter te selecteren.
* Voer het hele notitieblok uit door **Celrun** > **te**selecteren.
* Maak een nieuw notitieblok door het pictogram Jupyter (linkerbovenhoek) te selecteren, de knop **Nieuw** aan de rechterkant te selecteren en vervolgens de notitiebloktaal te kiezen (ook wel kernels genoemd).   

> [!NOTE]
> Momenteel worden Python 2.7, Python 3.6, R, Julia en PySpark kernels in Jupyter ondersteund. De R-kernel ondersteunt programmeren in zowel open-source R als Microsoft R.   
> 
> 

Wanneer u zich in het notitieblok bevindt, u uw gegevens verkennen, het model bouwen en het model testen met behulp van uw keuze van bibliotheken.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Modellen trainen en implementeren met Azure Machine Learning
Nadat u uw model hebt gebouwd en gevalideerd, is de volgende stap meestal om het in productie te nemen. Met deze stap kunnen uw clienttoepassingen de modelvoorspellingen in realtime of batchmodus aanroepen. Azure Machine Learning biedt een mechanisme voor het operationaliseren van een model dat is ingebouwd in R of Python.

Wanneer u uw model operationeel maakt in Azure Machine Learning, wordt een webservice weergegeven. Hiermee kunnen clients REST-aanroepen die invoerparameters doorgeven en voorspellingen van het model als uitvoer ontvangen.

### <a name="build-and-operationalize-python-models"></a>Python-modellen bouwen en operationaliseren
Hier is een stukje code ontwikkeld in een Python Jupyter notebook die een eenvoudig model bouwt met behulp van de Scikit-learn bibliotheek:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

De methode die wordt gebruikt om uw Python-modellen te implementeren in Azure Machine Learning, verpakt de voorspelling van het model in een functie en versiert het met kenmerken die worden geleverd door de vooraf geïnstalleerde Azure Machine Learning Python-bibliotheek. De kenmerken duiden uw Azure Machine Learning workspace ID, de API-sleutel en de invoer- en retourparameters aan.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Een klant kan nu bellen naar de webservice. Gemakswikkels construeren de REST API-aanvragen. Hier vindt u de voorbeeldcode om de webservice te gebruiken:

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> Momenteel wordt de Azure Machine Learning-bibliotheek alleen ondersteund op Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>R-modellen bouwen en operationaliseren
U R-modellen die zijn gebouwd op de Virtual Machine Data Science of elders implementeren op Azure Machine Learning op een manier die vergelijkbaar is met hoe het voor Python wordt gedaan. Dit zijn de stappen:

1. Maak een bestand settings.json om uw werkruimte-id en verificatietoken op te geven. 
2. Schrijf een wrapper voor de voorspellende functie van het model.
3. Bel ```publishWebService``` de Azure Machine Learning-bibliotheek om door te geven in de functiewrapper.  

Gebruik de volgende procedure en codefragmenten om een model als webservice in Azure Machine Learning in te stellen, te bouwen, te publiceren en te gebruiken.

#### <a name="set-up"></a>Instellen

Maak een bestand settings.json ```.azureml``` onder een map die onder uw startmap wordt aangeroepen. Voer de parameters in van uw Azure Machine Learning-werkruimte.

Hier is de settings.json bestandsstructuur:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Een model in R bouwen en publiceren in Azure Machine Learning

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Het model gebruiken dat is geïmplementeerd in Azure Machine Learning
Als u het model wilt gebruiken vanuit een clienttoepassing, gebruikt u de Azure Machine Learning-bibliotheek om de gepubliceerde webservice op naam op te zoeken. Gebruik `services` de API-aanroep om het eindpunt te bepalen. Dan belt u `consume` gewoon de functie en geef je door in het te voorspellen dataframe.

Gebruik de volgende code om het model te gebruiken dat is gepubliceerd als een Azure Machine Learning-webservice:

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Zie meer informatie over [R-pakketten in Machine Learning Studio.](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning)

## <a name="manage-azure-resources"></a>Azure-resources beheren
Met de DSVM u uw analyseoplossing niet alleen lokaal op de virtuele machine bouwen. Het stelt u ook in staat om toegang te krijgen tot services op het Azure-cloudplatform. Azure biedt verschillende compute-, storage-, data-analyses en andere services die u vanaf uw DSVM beheren en openen.

Als u uw Azure-abonnement en cloudresources wilt beheren, hebt u twee opties:
+ Gebruik uw browser en ga naar de [Azure-portal.](https://portal.azure.com)

+ PowerShell-scripts gebruiken. Voer Azure PowerShell uit via een snelkoppeling op het bureaublad of in het menu **Start.** Zie de [Microsoft Azure PowerShell-documentatie](../../powershell-azure-resource-manager.md) voor alle details. 

## <a name="extend-storage-by-using-shared-file-systems"></a>Opslag uitbreiden met gedeelde bestandssystemen
Gegevenswetenschappers kunnen grote gegevenssets, code of andere bronnen binnen het team delen. De DSVM heeft ongeveer 45 GB ruimte beschikbaar. Als u uw opslag wilt uitbreiden, u Azure Files gebruiken en deze op een of meer DSVM-exemplaren monteren of openen via een REST-API. U de [Azure-portal](../../virtual-machines/windows/attach-managed-disk-portal.md) ook gebruiken of [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) gebruiken om extra speciale gegevensschijven toe te voegen. 

> [!NOTE]
> De maximale ruimte op het aandeel Azure Files is 5 TB. De groottelimiet voor elk bestand is 1 TB. 

U dit script in Azure PowerShell gebruiken om een Azure Files-share te maken:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Nu u een Azure Files-share hebt gemaakt, u deze in elke virtuele machine in Azure monteren. We raden u aan de VM in hetzelfde Azure-datacenter als het opslagaccount te plaatsen, om latentie en kosten voor gegevensoverdracht te voorkomen. Hier volgen de Azure PowerShell-opdrachten om het station op de DSVM te monteren:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Nu u toegang tot dit station als je zou elke normale schijf op de VM.

## <a name="share-code-in-github"></a>Code delen in GitHub
GitHub is een code repository waar u code samples en bronnen voor verschillende tools vinden met behulp van technologieën die worden gedeeld door de ontwikkelaarsgemeenschap. Het gebruikt Git als de technologie om versies van de codebestanden te volgen en op te slaan. GitHub is ook een platform waar u uw eigen opslagplaats maken om de gedeelde code en documentatie van uw team op te slaan, versiebeheer te implementeren en te bepalen wie toegang heeft tot het bekijken en bijdragen van code. 

Ga naar de [GitHub help-pagina's](https://help.github.com/) voor meer informatie over het gebruik van Git. Je GitHub gebruiken als een van de manieren om samen te werken met je team, code te gebruiken die door de community is ontwikkeld en code terug te dragen aan de community.

De DSVM wordt geleverd met clienttools op de opdrachtregel en op de GUI om toegang te krijgen tot de GitHub-repository. Het command-line tool dat werkt met Git en GitHub heet Git Bash. Visual Studio is geïnstalleerd op de DSVM en heeft de Git extensies. U vindt pictogrammen voor deze hulpprogramma's in het **menu Start** en op het bureaublad.

Als u code wilt downloaden uit een ```git clone``` GitHub-opslagplaats, gebruikt u de opdracht. Als u bijvoorbeeld de door Microsoft gepubliceerde repository voor gegevenswetenschap wilt downloaden in de huidige map, u de volgende opdracht in Git Bash uitvoeren:

    git clone https://github.com/Azure/DataScienceVM.git

In Visual Studio u dezelfde kloonbewerking uitvoeren. In de volgende schermafbeelding ziet u hoe u toegang krijgt tot de tools Van Git en GitHub in Visual Studio:

![Schermafbeelding van Visual Studio met de GitHub-verbinding weergegeven](./media/vm-do-ten-things/VSGit.PNG)

U meer informatie vinden over het gebruik van Git om met uw GitHub-repository te werken vanuit bronnen die beschikbaar zijn op github.com. Het [spiekbriefje](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) is een nuttige referentie.

## <a name="access-azure-data-and-analytics-services"></a>Toegang tot Azure-gegevens- en analyseservices
### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob-opslag is een betrouwbare, economische cloudopslagservice voor groot en klein data. In deze sectie wordt beschreven hoe u gegevens verplaatsen naar Blob-opslag en toegang krijgen tot gegevens die zijn opgeslagen in een Azure-blob.

#### <a name="prerequisites"></a>Vereisten

* Maak uw Azure Blob-opslagaccount vanuit de [Azure-portal.](https://portal.azure.com)

   ![Schermafbeelding van het proces voor het maken van opslagaccount in de Azure-portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Controleer of het azcopy-gereedschap van de ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```opdrachtregel vooraf is geïnstalleerd: . De map met azcopy.exe staat al op de path-omgevingsvariabele, zodat u voorkomen dat u het volledige opdrachtpad typt wanneer u dit gereedschap uitvoert. Zie de [AzCopy-documentatie](../../storage/common/storage-use-azcopy.md)voor meer informatie over het azcopy-hulpprogramma.
* Start het hulpprogramma Azure Storage Explorer. U het downloaden via de [webpagina Van Storage Explorer.](https://storageexplorer.com/) 

   ![Schermafbeelding van Azure Storage Explorer die toegang heeft tot een opslagaccount](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Gegevens van een VM naar een Azure-blob verplaatsen: AzCopy

Als u gegevens wilt verplaatsen tussen uw lokale bestanden en Blob-opslag, u AzCopy gebruiken op de opdrachtregel of in PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Vervang **C:\myfolder** door het pad waar uw bestand is opgeslagen, **mystorageaccount** met uw Blob-opslagaccountnaam, **mycontainer** met de containernaam en **opslagaccountsleutel** door uw Blob-opslagsleutel. U uw opslagaccountreferenties vinden in de [Azure-portal.](https://portal.azure.com)

Voer de opdracht AzCopy uit in PowerShell of via een opdrachtprompt. Hier is een voorbeeld gebruik van de AzCopy, opdracht:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Nadat u de opdracht AzCopy hebt uitgevoerd om naar een Azure-blob te kopiëren, wordt het bestand weergegeven in Azure Storage Explorer.

![Schermafbeelding van het opslagaccount, het geüploade CSV-bestand weergeven](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Gegevens van een virtuele machine verplaatsen naar een Azure-blob: Azure Storage Explorer

U ook gegevens uit het lokale bestand in uw vm uploaden met Azure Storage Explorer:

* Als u gegevens wilt uploaden naar een container, selecteert u de doelcontainer en selecteert u de knop **Uploaden.** ![Schermafbeelding van de knop Uploaden in Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Selecteer de ellips (**...**) rechts van het vak **Bestanden,** selecteer een of meerdere bestanden die u vanuit het bestandssysteem wilt uploaden en selecteer **Uploaden** om te beginnen met het uploaden van de bestanden. ![Schermafbeelding van het dialoogvenster Bestanden uploaden](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Gegevens uit een Azure-blob lezen: machine learning-lezermodule

In Azure Machine Learning Studio u de module Gegevens importeren gebruiken om gegevens uit uw blob te lezen.

![Schermafbeelding van de module Gegevens importeren in Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Gegevens lezen van een Azure-blob: Python ODBC

U de BlobService-bibliotheek gebruiken om gegevens rechtstreeks te lezen vanuit een blob in een Jupyter-notitieblok of in een Python-programma.

Importeer eerst de vereiste pakketten:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Sluit vervolgens de referenties van uw Blob-opslagaccount aan en lees gegevens uit de blob:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

De gegevens worden gelezen als een gegevenskader:

![Schermafbeelding van de eerste 10 rijen met gegevens](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage is een hyperscale repository voor big data analytics workloads en is compatibel met Hadoop Distributed File System (HDFS). Het werkt met Hadoop, Spark en Azure Data Lake Analytics. In deze sectie leert u hoe u gegevens verplaatsen naar Azure Data Lake Storage en analyses uitvoeren met Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Vereisten

* Maak uw Azure Data Lake Analytics-exemplaar in de [Azure-portal.](https://portal.azure.com)

   ![Schermafbeelding van het maken van een Instantie Data Lake Analytics vanuit de Azure-portal](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* De [Azure Data Lake- en Stream Analytics-hulpprogramma's voor Visual Studio-plug-in](https://www.microsoft.com/download/details.aspx?id=49504) zijn al geïnstalleerd in Visual Studio Community Edition op de virtuele machine. Nadat u Visual Studio hebt gestart en u hebt aangemeld bij uw Azure-abonnement, moet u uw Azure Data Analytics-account en -opslag zien in het linkerdeelvenster van Visual Studio.

   ![Schermafbeelding van de plug-in voor Data Lake-hulpprogramma's in Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Gegevens verplaatsen van een vm naar Data Lake: Azure Data Lake Explorer

U Azure Data Lake Explorer gebruiken om [gegevens van de lokale bestanden in uw virtuele machine te uploaden naar Data Lake Storage.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

U ook een gegevenspijplijn maken om uw gegevensverplaatsing van of naar Azure Data Lake te operationaliseren met [Azure Data Factory.](https://azure.microsoft.com/services/data-factory/) [In dit artikel](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) u de stappen doorlopen om de gegevenspijplijnen te maken.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Gegevens lezen van een Azure-blob naar Data Lake: U-SQL

Als uw gegevens zich in Azure Blob-opslag bevinden, u rechtstreeks gegevens uit een Azure-blob lezen in een U-SQL-query. Voordat u uw U-SQL-query samenstelt, controleert u of uw Blob-opslagaccount is gekoppeld aan uw Azure Data Lake-exemplaar. Ga naar de Azure-portal, zoek uw Azure Data Lake Analytics-dashboard, selecteer **Gegevensbron toevoegen,** selecteer een opslagtype **Azure-opslag**en sluit de naam en sleutel van uw Azure-opslagaccount aan. Vervolgens u verwijzen naar de gegevens die zijn opgeslagen in het opslagaccount.

![Schermafbeelding van het dialoogvenster Gegevensbron toevoegen](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

In Visual Studio u gegevens uit blob-opslag lezen, gegevens manipuleren, functies engineeren en de resulterende gegevens verzenden naar Azure Data Lake- of Azure Blob-opslag. Wanneer u naar de gegevens in blob-opslag verwijst, gebruikt u **wasb://**. Wanneer u naar de gegevens in Azure Data Lake verwijst, gebruikt u **swbhdfs://**.

U de volgende U-SQL-query's gebruiken in Visual Studio:

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

Nadat uw query naar de server is verzonden, wordt in een diagram de status van uw taak weergegeven.

![Schermafbeelding van het diagram met de functiestatus](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Querygegevens in Data Lake: U-SQL

Nadat de gegevensset is ingenomen in Azure Data Lake, u [U-SQL-taal](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) gebruiken om de gegevens op te vragen en te verkennen. U-SQL-taal is vergelijkbaar met T-SQL, maar combineert een aantal functies van C# zodat gebruikers aangepaste modules en door de gebruiker gedefinieerde functies kunnen schrijven. U de scripts in de vorige stap gebruiken.

Nadat de query naar de server is verzonden, tripdata_summary. CSV wordt weergegeven in Azure Data Lake Explorer. U een voorbeeld van de gegevens bekijken door met de rechtermuisknop op het bestand te klikken.

![Schermafbeelding van het CSV-bestand in Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

De bestandsinformatie wordt weergegeven:

![Schermafbeelding van de bestandsoverzichtsgegevens](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop clusters
Azure HDInsight is een beheerde Apache Hadoop-, Spark-, HBase- en Storm-service in de cloud. U eenvoudig werken met Azure HDInsight-clusters vanuit de Data Science Virtual Machine.

#### <a name="prerequisites"></a>Vereisten

* Maak uw Azure Blob-opslagaccount vanuit de [Azure-portal.](https://portal.azure.com) Dit opslagaccount wordt gebruikt om gegevens op te slaan voor HDInsight-clusters.

   ![Schermafbeelding van het maken van een opslagaccount vanuit de Azure-portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Azure HDInsight Hadoop-clusters aanpassen vanuit de [Azure-portal.](../team-data-science-process/customize-hadoop-cluster.md)
  
   Koppel het opslagaccount dat is gemaakt aan uw HDInsight-cluster wanneer het wordt gemaakt. Dit opslagaccount wordt gebruikt voor toegang tot gegevens die binnen het cluster kunnen worden verwerkt.

   ![Selecties voor het koppelen van het opslagaccount dat is gemaakt met een HDInsight-cluster](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Extern bureaublad toegang tot het hoofdknooppunt van het cluster inschakelen nadat het is gemaakt. Onthoud de externe toegangsreferenties die u hier opgeeft, omdat u ze in de volgende procedure nodig hebt.

   ![Knop Extern bureaublad voor het inschakelen van externe toegang tot het HDInsight-cluster](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Maak een Azure Machine Learning-werkruimte. Uw Machine Learning-experimenten worden opgeslagen in deze Machine Learning-werkruimte. Selecteer de gemarkeerde opties in de portal, zoals weergegeven in de volgende schermafbeelding:

   ![Een Azure Machine Learning-werkruimte maken](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Voer de parameters voor uw werkruimte in.

   ![Parameters voor machine learning-werkruimte invoeren](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Gegevens uploaden met IPython Notebook. Importeer vereiste pakketten, sluit referenties aan, maak een database in uw opslagaccount en laad gegevens in HDI-clusters.

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

U deze [walkthrough](../team-data-science-process/hive-walkthrough.md) ook volgen om NYC Taxi-gegevens te uploaden naar het HDI-cluster. Belangrijke stappen zijn:
  
* Gebruik AzCopy om gezipte Cv's van de openbare blob naar uw lokale map te downloaden.
* Gebruik AzCopy om uitgepakte CSV's vanuit de lokale map te uploaden naar een HDI-cluster.
* Log in op het hoofdknooppunt van het Hadoop-cluster en bereid u voor op verkennende gegevensanalyse.

Nadat de gegevens in het HDI-cluster zijn geladen, u uw gegevens controleren in Azure Storage Explorer. En de nyctaxidb database is gemaakt in het HDI cluster.

#### <a name="data-exploration-hive-queries-in-python"></a>Gegevensverkenning: Hive Queries in Python

Omdat de gegevens zich in een Hadoop-cluster bevinden, u het pyodbc-pakket gebruiken om verbinding te maken met Hadoop-clusters en querydatabases door Hive te gebruiken om verkennings- en functie-engineering te doen. U de bestaande tabellen bekijken die u in de vereiste stap hebt gemaakt.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Bestaande tabellen weergeven](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Laten we eens kijken naar het aantal records in elke maand en de frequenties van getipt of niet in de reistabel:

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Plot van het aantal records in elke maand](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![Perceel van tipfrequenties](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

U ook de afstand tussen de ophaallocatie en de inleverlocatie berekenen en deze vervolgens vergelijken met de reisafstand.

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![Bovenste rijen van de ophaal- en inlevertabel](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Plot van pick-up/ drop-off afstand tot reisafstand](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Laten we nu een downsampled (1 procent) set gegevens voorbereiden voor modellering. U deze gegevens gebruiken in de machine learning-lezermodule.

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

Voeg nu de inhoud van de join in de vorige interne tabel in.

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

Na een tijdje u zien dat de gegevens zijn geladen in Hadoop-clusters:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Bovenste rijen met gegevens uit de tabel](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

#### <a name="read-data-from-hdi-by-using-azure-machine-learning-studio-classic-reader-module"></a>Gegevens van HDI lezen met Azure Machine Learning Studio (klassiek): leesmodule

U de lezermodule in Azure Machine Learning Studio (klassiek) ook gebruiken om toegang te krijgen tot de database in een Hadoop-cluster. Sluit de referenties van uw HDI-clusters en Azure-opslagaccount aan om machine learning-modellen te bouwen met behulp van een database in HDI-clusters.

![Eigenschappen van lezermodule](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

U vervolgens de gescoorde gegevensset bekijken:

![Gegevensset gescoorde gegevensset weergeven](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse en databases
Azure SQL Data Warehouse is een elastisch datawarehouse als een service met een SQL Server-ervaring van bedrijfsklasse.

U uw Azure SQL-gegevensmagazijn inrichten door de instructies in dit artikel te [volgen.](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) Nadat u uw SQL-gegevensmagazijn hebt ingericht, u [deze walkthrough](../team-data-science-process/sqldw-walkthrough.md) gebruiken om gegevens uploaden, verkennen en modelleren uit te brengen met behulp van gegevens in het SQL-gegevensmagazijn.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB is een NoSQL-database in de cloud. U het gebruiken om te werken met documenten zoals JSON, en om de documenten op te slaan en op te vragen.

Gebruik de volgende vereiste stappen om toegang te krijgen tot Azure Cosmos DB vanuit de DSVM:

1. De Azure Cosmos DB Python SDK is al geïnstalleerd op de DSVM. Als u deze ```pip install pydocumentdb --upgrade``` wilt bijwerken, voert u uit vanaf een opdrachtprompt.
2. Maak een Azure Cosmos DB-account en -database vanuit de [Azure-portal.](https://portal.azure.com)
3. Download het Azure Cosmos DB Data Migration Tool uit het [Microsoft Download center](https://www.microsoft.com/download/details.aspx?id=53595) en haal naar een map naar keuze.
4. Json-gegevens (vulkaangegevens) importeren die zijn opgeslagen in een [openbare blob](https://cahandson.blob.core.windows.net/samples/volcano.json) in Azure Cosmos DB met de volgende opdrachtparameters naar het migratiehulpprogramma. (Gebruik dtui.exe uit de map waar u het Azure Cosmos DB Data Migration Tool hebt geïnstalleerd.) Voer de bron- en doellocatie in met de volgende parameters:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Nadat u de gegevens hebt geïmporteerd, u naar Jupyter gaan en het notitieblok met de titel *DocumentDBSample openen.* Het bevat Python-code om toegang te krijgen tot Azure Cosmos DB en een aantal basisquery's uit te brengen. U meer informatie over Azure Cosmos DB krijgen door de [documentatiepagina](https://docs.microsoft.com/azure/cosmos-db/)van de service te bezoeken.

## <a name="use-power-bi-reports-and-dashboards"></a>Power BI-rapporten en dashboards gebruiken 
U het VOLCANO JSON-bestand visualiseren vanuit het vorige Azure Cosmos DB-voorbeeld in Power BI Desktop om visuele inzichten in de gegevens te verkrijgen. Gedetailleerde stappen zijn beschikbaar in het [Power BI-artikel](../../cosmos-db/powerbi-visualize.md). Hier zijn de stappen op hoog niveau:

1. Open Power BI Desktop en selecteer **Gegevens ophalen**. Geef de URL `https://cahandson.blob.core.windows.net/samples/volcano.json`op als: .
2. U ziet de JSON-records geïmporteerd als een lijst. Converteer de lijst naar een tabel, zodat Power BI ermee kan werken.
4. Vouw de kolommen uit door het pictogram Uitvouwen (pijl) te selecteren.
5. Houd er van dat de locatie **een** recordveld is. Vouw de record uit en selecteer alleen de coördinaten. **Coördinaat** is een lijstkolom.
6. Voeg een nieuwe kolom toe om de kolom lijstcoördinaat om te zetten in een door komma's gescheiden **LatLong-kolom.** De twee elementen in het veld coördinatenlijst ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```met elkaar in delen met behulp van de formule .
7. **Converteer** de kolom Hoogte naar de cimaal en selecteer de knoppen **Sluiten** en **Toepassen.**

In plaats van vooraf te gaan aan stappen, u de volgende code plakken. Hierin worden de stappen uitgeschreven die worden gebruikt in de geavanceerde editor in Power BI om de gegevenstransformaties in een querytaal te schrijven.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

U hebt nu de gegevens in uw Power BI-gegevensmodel. Uw Power BI-bureaublad-instantie moet als volgt worden weergegeven:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

U met het bouwen van rapporten en visualisaties beginnen met behulp van het gegevensmodel. U de stappen in [dit Power BI-artikel](../../cosmos-db/powerbi-visualize.md#build-the-reports) volgen om een rapport te maken.

## <a name="scale-the-dsvm-dynamically"></a>Schaal de DSVM dynamisch 
U de DSVM op en neer schalen om aan de behoeften van uw project te voldoen. Als u de VM 's avonds of in het weekend niet hoeft te gebruiken, u de VM uitschakelen via de [Azure-portal.](https://portal.azure.com)

> [!NOTE]
> U brengt rekenkosten met zich mee als u alleen de afsluitknop voor het besturingssysteem op de VM gebruikt.  
> 
> 

Mogelijk moet u een grootschalige analyse uitvoeren en meer CPU-, geheugen- of schijfcapaciteit nodig hebben. Als dat het geval is, u een keuze vinden van VM-formaten in termen van CPU-cores, GPU-gebaseerde exemplaren voor deep learning, geheugencapaciteit en schijftypen (inclusief solid-state drives) die voldoen aan uw reken- en budgetbehoeften. De volledige lijst met VM's en hun rekenprijzen per uur zijn beschikbaar op de prijspagina van [Azure Virtual Machines.](https://azure.microsoft.com/pricing/details/virtual-machines/)

Op dezelfde manier kan uw behoefte aan VM-verwerkingscapaciteit afnemen. (U hebt bijvoorbeeld een grote werkbelasting verplaatst naar een Hadoop- of Spark-cluster.) U het cluster vervolgens schalen vanuit de [Azure-portal](https://portal.azure.com) en naar de instellingen van uw VM-exemplaar gaan. 

## <a name="add-more-tools"></a>Meer hulpprogramma's toevoegen
Tools die vooraf zijn ingebouwd in de DSVM kunnen voldoen aan veel voorkomende behoeften op het gebied van gegevensanalyse. Dit bespaart u tijd omdat u uw omgevingen niet één voor één hoeft te installeren en configureren. Het bespaart u ook geld, omdat u betaalt voor alleen middelen die u gebruikt.

U andere Azure-gegevens- en analyseservices gebruiken die in dit artikel zijn geprofileerd om uw analyseomgeving te verbeteren. In sommige gevallen hebt u mogelijk extra tools nodig, waaronder enkele eigen partnertools. U hebt volledige administratieve toegang op de virtuele machine om nieuwe tools te installeren die u nodig hebt. U ook aanvullende pakketten in Python en R installeren die niet vooraf zijn geïnstalleerd. Voor Python u ```conda``` ```pip```een van beide of. Voor R ```install.packages()``` u de IDE-console gebruiken of de IDE gebruiken en **pakketten** > **installeren selecteren.**

## <a name="deep-learning"></a>Deep learning

Naast de framework-gebaseerde samples, u een set uitgebreide walkthroughs krijgen die zijn gevalideerd op de DSVM. Deze walkthroughs helpen u bij het opstarten van uw ontwikkeling van deep-learning toepassingen in domeinen zoals beeld- en tekst/taalbegrip.   


- [Neurale netwerken uitvoeren in verschillende frameworks](https://github.com/ilkarman/DeepLearningFrameworks): Deze walkthrough laat zien hoe u code van het ene framework naar het andere migreert. Het laat ook zien hoe modellen en runtime prestaties tussen frameworks te vergelijken. 

- [Een handleiding om een end-to-end oplossing te bouwen om producten in afbeeldingen te detecteren:](https://github.com/Azure/cortana-intelligence-product-detection-from-images)Beelddetectie is een techniek die objecten in afbeeldingen kan lokaliseren en classificeren. Deze technologie heeft het potentieel om enorme beloningen te brengen in vele real-life zakelijke domeinen. Retailers kunnen deze techniek bijvoorbeeld gebruiken om te bepalen welk product een klant uit het schap heeft gehaald. Deze informatie helpt winkels op hun beurt bij het beheren van productvoorraad. 

- [Deep learning voor audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Deze tutorial laat zien hoe je een deep-learning model traint voor audio-event detectie op de [stedelijke geluiden dataset.](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) Het biedt ook een overzicht van hoe te werken met audiogegevens.

- [Classificatie van tekstdocumenten](https://github.com/anargyri/lstm_han): Deze walkthrough laat zien hoe je twee neurale netwerkarchitecturen bouwen en trainen: Hierarchical Attention Network en Long Short Term Memory (LSTM) network. Deze neurale netwerken gebruiken de Keras API voor deep learning om tekstdocumenten te classificeren. Keras is een front-end van drie van de meest populaire deep-learning frameworks: Microsoft Cognitive Toolkit, TensorFlow en Theano.

## <a name="summary"></a>Samenvatting
In dit artikel wordt een aantal van de dingen beschreven die u doen op de Microsoft Data Science Virtual Machine. Er zijn nog veel meer dingen die u doen om van de DSVM een effectieve analyseomgeving te maken.

