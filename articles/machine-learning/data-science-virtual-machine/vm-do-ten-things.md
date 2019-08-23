---
title: Gegevens verkennen en modelleren met Data Science Virtual Machine
titleSuffix: Azure
description: Verschillende gegevens verkennen en modelleren taak uitvoeren op de Data science Virtual Machine.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
editor: cgronlun
ms.custom: seodec18
ms.assetid: 145dfe3e-2bd2-478f-9b6e-99d97d789c62
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: vijetaj
ms.openlocfilehash: cd4acdd4009a94b51807a536c28b138b86d2aced
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971819"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Tien dingen die u dit op de Windows Data Science Virtual Machine doen kunt

De Windows Data Science Virtual Machine (DSVM) is een krachtige data science-ontwikkelomgeving waarmee u verschillende gegevens verkennen en modelleren taken uit te voeren. De omgeving komt al gebouwd en bundel met verschillende populaire hulpprogramma's voor gegevensanalyse gemakkelijk snel aan de slag met uw analyse voor On-premises, Cloud of hybride implementaties. De DSVM werkt nauw samen met veel Azure-services en kan lezen en te verwerken van gegevens die al zijn opgeslagen in Azure, in Azure SQL Data Warehouse, Azure Data Lake, Azure Storage of Azure Cosmos DB. Het kan ook gebruikmaken van andere analytics-hulpprogramma's zoals Azure Machine Learning en Azure Data Factory.

In dit artikel leert u hoe u uw DSVM voor verschillende data science-taken uitvoeren en communiceren met andere Azure-services. Hier volgen enkele dingen die u op de DSVM kunt doen:

1. Gegevens verkennen en ontwikkelen van modellen lokaal op de DSVM met behulp van Microsoft ML Server, Python
2. Een Jupyter-notebook gebruikt om te experimenteren met uw gegevens in een browser met behulp van Python 2, 3, Python, R Microsoft een gereed enterprise-versie van R die is ontworpen voor prestaties
3. Modellen die zijn gemaakt met R en Python op Azure Machine Learning, zodat clienttoepassingen hebben toegang tot uw modellen met behulp van een eenvoudige web service-interface implementeren
4. Beheren van uw Azure-resources met behulp van Azure portal of Powershell
5. Uw opslagruimte uitbreiden en delen van grootschalige gegevenssets / code met uw hele team door het maken van een Azure File storage als een koppelbare station op uw DSVM
6. Deel code met uw team met behulp van GitHub en toegang tot uw gebruik van de vooraf geïnstalleerde Git-clients - Git Bash, GUI Git-opslagplaats.
7. Toegang tot verschillende Azure data en analyse-services zoals Azure blob-opslag, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse & databases
8. Maak rapporten en dashboard met de Power BI Desktop die vooraf zijn geïnstalleerd op de DSVM en deze implementeren in de cloud
9. Dynamisch schalen van uw DSVM om te voldoen aan de projectbehoeften van uw
10. Aanvullende hulpprogramma's installeren op uw virtuele machine   

> [!NOTE]
> Extra gebruik in rekening gebracht voor veel extra opslag en analyse van de gegevensservices van die in dit artikel worden vermeld. Raadpleeg de [Azure-prijzen](https://azure.microsoft.com/pricing/) pagina voor meer informatie.
> 
> 

**Vereisten**

* U moet een Azure-abonnement. U kunt zich aanmelden voor een gratis proefversie [hier](https://azure.microsoft.com/free/).
* Instructies voor het inrichten van een Data Science Virtual Machine in Azure portal zijn beschikbaar op [het maken van een virtuele machine](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="1-explore-data--develop-models-with-microsoft-ml-server"></a>1. Gegevens verkennen & modellen ontwikkelen met micro soft ML Server
Talen als R en Python kunt u uw gegevens analyseren op de DSVM.

Voor R, kunt u een IDE zoals RStudio die kan worden gevonden in het startmenu of het bureaublad of R-hulpprogramma's voor Visual Studio. Microsoft heeft opgegeven, aanvullende bibliotheken boven op de Open-source/CRAN-R om schaalbare analyses en de mogelijkheid voor het analyseren van gegevens die groter is dan de geheugengrootte die is toegestaan door het uitvoeren van parallelle gesegmenteerde analyse te schakelen. 

Voor Python, kunt u een IDE zoals Visual Studio Community Edition, met Python Tools for Visual Studio (PTVS)-extensie die vooraf zijn geïnstalleerd. De hoofdmap conda-omgeving is standaard alleen Python 3.6, geconfigureerd op PTVS. Om in te schakelen Anaconda Python 2.7, moet u de volgende stappen uit:

* Aangepaste omgevingen voor elke versie maken door te navigeren naar **extra** -> **Python Tools** -> **Python-omgevingen** en vervolgens te klikken op ' **+ Aangepast**'in de Visual Studio Community-versie
* Geef een beschrijving en het pad van het voorvoegsel omgeving als *c:\anaconda\envs\python2* voor Anaconda Python 2.7
* Klik op **automatisch detecteren** en vervolgens **toepassen** om op te slaan van de omgeving.

Zie de [documentatie bij PTVS](https://aka.ms/ptvsdocs) voor meer informatie over het maken van de Python-omgevingen.

Nu u worden ingesteld om een nieuwe Python-project te maken. Navigeer naar **bestand** -> **nieuw** -> **Project** -> **Python** en selecteer het type Python-toepassing die u wilt maken. U kunt de Python-omgeving voor het huidige project instellen op de gewenste versie (Python 2.7 of 3.6) met de rechtermuisknop te klikken op de **Python-omgevingen**, Klik daarvoor **Python-omgevingen toevoegen/verwijderen**, en vervolgens de gewenste omgeving verzamelen. U vindt meer informatie over het werken met PTVS in het product [documentatie](https://aka.ms/ptvsdocs).

## <a name="2-use-jupyter-notebooks"></a>2. Jupyter-notebooks gebruiken
De Jupyter-Notebook is een krachtige omgeving die voorziet in een browser gebaseerde "IDE' gegevens verkennen en modelleren. U kunt Python 2, 3 van Python of R (Open Source en het Microsoft R Server) gebruiken in een Jupyter-Notebook.

Als u wilt de Jupyter-Notebook start, klik op het pictogram van het menu start / pictogram op het bureaublad met de titel **Jupyter-Notebook**. In de opdrachtprompt DSVM kunt u ook de opdracht uitvoeren ```jupyter notebook``` uit de map waar u bestaande laptops hebt of wilt maken van nieuwe notitieblokken.  

Zodra u Jupyter hebt gestart, ziet u een map met een paar voorbeeld laptops die vooraf verpakte in de DSVM zijn. U kunt nu het volgende doen:

* Klik op het notitieblok om de code te zien.
* Elke cel uitvoeren door te drukken **SHIFT + ENTER**.
* Het hele notitieblok uitvoeren door te klikken op **cel** -> **uitvoeren**
* Een nieuwe notebook maken door te klikken op het pictogram met Jupyter (links bovenhoek) en vervolgens te klikken op **nieuw** knop aan de rechterkant en kies de notebook-taal (ook wel bekend als kernels).   

> [!NOTE]
> Python 2.7, Python 3.6, R, Julia en PySpark-kernels in Jupyter worden momenteel ondersteund. De kernel van R biedt ondersteuning voor programmeren in zowel Open source R, evenals de krachtige Microsoft-R.   
> 
> 

Wanneer u de notebook die u kunt verkennen worden uw gegevens, het model te bouwen, testen van het model met behulp van uw keuze van bibliotheken.

## <a name="3-train--deploy-models-with-azure-machine-learning"></a>3. Modellen trainen & implementeren met Azure Machine Learning
Nadat u hebt gemaakt en gevalideerd, het model is de volgende stap meestal te implementeren in productie. Hierdoor kan uw client toepassingen om aan te roepen van het modelvoorspellingen op een realtime of op basis van een batch-modus. Azure Machine Learning biedt een mechanisme om een ingebouwd in R of Python-model operationeel te maken.

Wanneer u een operationeel maken van uw model in Azure Machine Learning, wordt een webservice waarmee clients voor REST-aanroepen die invoer parameters doorgeven en voorspellingen uit het model als uitvoer wordt weergegeven.   

> [!NOTE]
> Als u hebt nog niet aangemeld voor Azure Machine Learning, kunt u een gratis werkruimte of een standard-werkruimte verkrijgen door naar de pagina de [Azure Machine Learning Studio](https://studio.azureml.net/) startpagina en op te klikken op 'aan de slag."   
> 
> 

### <a name="build-and-operationalize-python-models"></a>Build- en Python operationeel maken van modellen
Hier volgt een codefragment in een Python-Jupyter-Notebook die een eenvoudig model met behulp van de bibliotheek SciKit meer ontwikkeld.

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

De methode gebruikt om uw python-modellen implementeren in Azure Machine Learning wordt de voorspelling van het model in een functie en wordt het verfraaid met kenmerken die worden geleverd door de vooraf geïnstalleerde Azure Machine Learning python-bibliotheek, op basis van uw Azure Machine Learning werkruimte-ID, API-sleutel en de invoer- en -parameters.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Een client kan nu aanroepen naar de webservice. Er zijn gemak wrappers die samenstellen van de REST-API-aanvragen. Hier volgt een voorbeeldcode om de webservice gebruiken.

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
> De Azure Machine Learning-bibliotheek wordt alleen ondersteund op Python 2.7 op dit moment.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Build- en operationeel maken van R-modellen
U kunt R-modellen die zijn gebouwd op de Data Science Virtual Machine of ergens anders op Azure Machine Learning op een manier die lijkt op hoe dit werkt voor Python kunt implementeren. Dit zijn de stappen:

* Maak een bestand settings.json voor uw werkruimte-ID en auth-token 
* Schrijf een wrapper voor de functie van het model te voorspellen.
* Bel ```publishWebService``` in de Azure Machine Learning-bibliotheek om door te geven in de functie-wrapper.  

Hier volgt de procedure en code-codefragmenten die u instelt, bouwen, publiceren en een model gebruiken als een webservice in Azure Machine Learning kunnen worden gebruikt.

#### <a name="setup"></a>Instellen

* Maak een bestand settings.json in een map met de naam ```.azureml``` onder de basismap en voert u de parameters van uw Azure Machine Learning-werkruimte:

structuur van bestand Settings.JSON:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Een model in R bouwen en deze publiceren in Azure Machine Learning

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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Het model is geïmplementeerd in Azure Machine Learning gebruiken
Als u wilt het model van een clienttoepassing gebruiken, gebruiken we de Azure Machine Learning-bibliotheek om te controleren of de gepubliceerde webservice met behulp van de naam van de `services` API-aanroep om te bepalen het eindpunt. Dan Roep je de `consume` werken en door te geven in het kader van de gegevens om te worden voorspeld.
De volgende code wordt gebruikt om het model dat is gepubliceerd als een Azure Machine Learning-webservice gebruiken.

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# OK, try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Meer informatie over de Azure Machine Learning, R-bibliotheek vindt [hier](https://cran.r-project.org/web/packages/AzureML/AzureML.pdf).

## <a name="4-manage-azure-resources"></a>4. Azure-resources beheren
De DSVM wordt niet alleen kunt u het bouwen van uw analyseoplossing lokaal op de virtuele machine, maar er wordt een ook kunt u toegang tot de cloud van Microsoft Azure-services. Azure biedt diverse compute, storage, data analytics-services en andere services die u kunt beheren en openen vanuit uw DSVM.

Als u uw Azure-abonnement en cloud resources wilt beheren, hebt u twee opties:
+ **Azure Portal**: gebruik uw browser en wijs de [Azure Portal](https://portal.azure.com)

+ **Azure Power shell**: Windows Power shell-scripts gebruiken. Voer Azure Power shell uit vanuit een snelkoppeling op het bureau blad of vanuit het menu Start met de titel Microsoft Azure Power shell. Raadpleeg de [Microsoft Azure Power shell-documentatie](../../powershell-azure-resource-manager.md) voor volledige informatie. 

## <a name="5-extend-storage-with-shared-file-systems"></a>5. Opslag uitbreiden met gedeelde bestands systemen
Gegevenswetenschappers kunnen delen grote gegevenssets, code of andere resources in het team. De DSVM zelf heeft ongeveer 45GB beschikbare ruimte. Om uit te breiden van uw opslag, kunt u de Azure File-Service en een op een of meer exemplaren van de DSVM koppelen of openen via een REST-API.  U kunt ook [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) gebruiken of gebruikmaken van [Azure Power shell](../../virtual-machines/windows/attach-disk-ps.md) om extra toegewezen gegevens schijven toe te voegen. 

> [!NOTE]
> De maximale ruimte van de share Azure File-Service is 5 TB en afzonderlijke maximumbestandsgrootte is 1 TB. 

U kunt Azure Powershell gebruiken om een Azure File Service-share te maken. Dit is het script moet worden uitgevoerd in Azure PowerShell voor het maken van een Azure-service-bestandsshare.

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you wish.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure File Service Share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the FIle share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Nu dat u een Azure-bestandsshare hebt gemaakt, kunt u deze koppelen aan elke virtuele machine in Azure. Het is raadzaam dat de virtuele machine zich in dezelfde Azure-Datacenter als het opslagaccount om latentie en data transfer-kosten te voorkomen. Hier vindt u de opdrachten voor het koppelen van het station op de DSVM die u op Azure Powershell uitvoeren kunt.

```powershell
# Get storage key of the storage account that has the Azure file share from Azure portal. Store it securely on the VM to avoid prompted in next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure file share as Z: drive on the VM. You can chose another drive letter if you wish
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Nu kunt u dit station openen als een normale station op de virtuele machine.

## <a name="6-share-code-in-github"></a>6. Code delen in GitHub
GitHub is een opslagplaats waar u veel voorbeeldcode en bronnen voor verschillende hulpprogramma's met behulp van verschillende technologieën die worden gedeeld door de community van ontwikkelaars kunt vinden. Deze Git gebruikt als de technologie bij te houden en versies van de bestanden opslaat. GitHub is ook een platform waar u uw eigen opslagplaats opslaan van de gedeelde code en de documentatie van uw team, versiebeheer te implementeren en ook beheren die toegang hebben tot het weergeven en indienen code kunt maken. Ga naar de [GitHub help-pagina's](https://help.github.com/) voor meer informatie over het gebruik van Git. U kunt GitHub gebruiken als een van de manieren om samen te werken met uw team, gebruikt u code die door de community zijn ontwikkeld en code terug naar de community bijdragen.

De DSVM is al geladen met clienthulpprogramma's op zowel opdrachtregelprogramma als goed GUI voor toegang tot de GitHub-opslagplaats. Het opdrachtregelprogramma voor het werken met Git en GitHub heet Git Bash. Visual Studio is geïnstalleerd op de DSVM heeft de Git-extensies. U kunt opstarten pictogrammen vinden voor deze hulpprogramma's in het startmenu en het bureaublad.

Als u wilt downloaden code vanuit een GitHub-opslagplaats, gebruikt u de ```git clone``` opdracht. Bijvoorbeeld, voor het downloaden van data science-opslagplaats is uitgegeven door Microsoft in de huidige map u kunt uitvoeren met de volgende opdracht in ```git-bash```.

    git clone https://github.com/Azure/DataScienceVM.git

In Visual Studio, kunt u de dezelfde kloonbewerking doen. In de volgende scherm afbeelding ziet u hoe u de hulpprogram ma's Git en GitHub in Visual Studio kunt openen.

![Scherm opname van Visual Studio waarbij de GitHub-verbinding wordt weer gegeven](./media/vm-do-ten-things/VSGit.PNG)

U vindt meer informatie over het werken met uw GitHub-opslagplaats van verschillende resources beschikbaar op github.com met behulp van Git. De [overzichtskaart](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) is een nuttig verwijzing.

## <a name="7-access-many-azure-data--analytics-services"></a>7. Toegang tot veel Azure Data & Analytics-Services
### <a name="azure-blob"></a>Azure Blob
Azure-blob is een betrouwbare, betaalbare cloudopslag voor gegevens, groot en klein. Deze sectie wordt beschreven hoe u gegevens kunt verplaatsen naar Azure BLOB Storage en toegang tot gegevens die zijn opgeslagen in een Azure-Blob.

**Vereiste**

* **Maken van uw Azure Blob storage-account van [Azure-portal](https://portal.azure.com).**

![Schermopname van het proces voor het Storage-Account maken in Azure portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Bevestig dat de vooraf geïnstalleerde AzCopy-opdrachtregelprogramma is gevonden op ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. De map met de azcopy.exe is al in uw padomgevingsvariabele bevindt om te voorkomen dat het pad van de volledige opdracht te typen bij het uitvoeren van dit hulpprogramma. Raadpleeg voor meer informatie over het hulpprogramma AzCopy [documentatie van AzCopy](../../storage/common/storage-use-azcopy.md)
* Start het hulpprogramma Azure Storage Explorer. Deze kan worden gedownload vanaf [Microsoft Azure Storage Explorer](https://storageexplorer.com/). 

![Schermafbeelding van de toegang tot een Storage-Account van Azure Storage Explorer](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

**Gegevens verplaatsen van VM naar Azure-Blob: AzCopy**

Om gegevens te verplaatsen tussen uw lokale bestanden en blob-opslag, kunt u AzCopy gebruiken in de opdrachtregel of PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Vervang **C:\myfolder** naar het pad waar het bestand wordt opgeslagen, **mystorageaccount** naar uw blob storage-accountnaam **mycontainer** aan de containernaam **opslagaccountsleutel** naar de toegangssleutel voor uw blob-opslag. U vindt uw opslagaccountreferenties in [Azure-portal](https://portal.azure.com).

AzCopy-opdracht uitvoeren in PowerShell of vanaf een opdrachtprompt. Hier volgt voorbeeldgebruik van AzCopy-opdracht:

```powershell
# Copy *.sql from local machine to an Azure Blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from Azure Blob container to Local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Nadat u de AzCopy-opdracht om te kopiëren naar een Azure-blob hebt uitgevoerd, ziet u dat het bestand wordt weergegeven in Azure Storage Explorer binnenkort.

![Schermafbeelding van de Storage-Account, het geüploade CSV-bestand weergeven](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

**Gegevens verplaatsen van VM naar Azure-Blob: Azure Storage Explorer**

U kunt ook gegevens uit het lokale bestand uploaden in uw virtuele machine met behulp van Azure Storage Explorer:

* Als u wilt gegevens uploaden naar een container, selecteert u de doelcontainer en klik op de **uploaden** knop.![ Schermafbeelding van de knop voor uploaden in Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Klik op de **...**  aan de rechterkant van de **bestanden** Selecteer een of meerdere bestanden voor het uploaden van het bestandssysteem en klikt u op **uploaden** om te beginnen met uploaden van de bestanden.![ Schermopname van het dialoogvenster van de bestanden uploaden](./media/vm-do-ten-things/upload-files-to-blob.png)

**Gegevens lezen uit Azure-Blob: Module Machine Learning lezer**

In Azure Machine Learning Studio, kunt u een **module gegevens importeren** gegevens lezen uit uw blob.

![Schermafbeelding van de module gegevens importeren in Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

**Gegevens lezen uit Azure-Blob: Python ODBC**

U kunt **BlobService** bibliotheek lezen van gegevens rechtstreeks vanuit de blob in een Jupyter-Notebook of Python-programma.

Importeer eerst vereiste pakketten:

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

Daarna sluit u de referenties van uw Azure Blob-account en gegevens uit de Blob gelezen:

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

#unzipping downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

De gegevens worden gelezen als een gegevensframe:

![Schermafbeelding van de eerste 10 rijen met gegevens](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage is een zeer grote opslagruimte voor big data analytics-workloads en compatibel is met Hadoop Distributed File System (HDFS). Het werkt met Hadoop, Spark- en Azure Data Lake Analytics. In deze sectie leert u hoe u gegevens kunt verplaatsen in de Azure Data Lake Store en uitvoeren van analyses met Azure Data Lake Analytics.

**Vereiste**

* Maken van uw Azure Data Lake Analytics in [Azure-portal](https://portal.azure.com).

![Schermopname van het maken van Data Lake Analytics vanuit Azure portal](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* De **Azure Data Lake Tools** in **Visual Studio** gevonden op deze [koppeling](https://www.microsoft.com/download/details.aspx?id=49504) al is geïnstalleerd op de Visual Studio Community Edition die zich op de virtuele machine. Na het starten van Visual Studio en logboekregistratie in uw Azure-abonnement, ziet u uw Azure Data Analytics-account en de opslag in het linkerdeelvenster van Visual Studio.

![Schermopname van het Data Lake Tools in Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

**Verplaats gegevens van VM naar Data Lake: Azure Data Lake Explorer**

U kunt **Azure data Lake Explorer** gebruiken om [gegevens te uploaden van de lokale bestanden in uw virtuele machine naar Data Lake opslag](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

U kunt ook een pijplijn om uw verplaatsing van gegevens naar of van het gebruik van Azure Data Lake operationeel te maken de [Azure gegevens Factory(ADF)](https://azure.microsoft.com/services/data-factory/). Verwijzen naar dit [artikel](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) pijplijnen om u te begeleiden u bij de stappen voor het bouwen van de gegevens.

**Gegevens lezen van Azure-Blob naar Data Lake: U-SQL**

Als uw gegevens zich in Azure Blob-opslag bevinden, kunt u gegevens rechtstreeks lezen van Azure storage-blob in U-SQL-query. Controleer of dat uw blob storage-account is gekoppeld aan uw Azure Data Lake voordat het samenstellen van uw U-SQL-query. Ga naar **Azure-portal**, zoeken van uw Azure Data Lake Analytics-dashboard, klikt u op **gegevensbron toevoegen**, selecteert u opslagtype u moet **Azure Storage** en sluit in uw Azure Storage-Account Naam en sleutel. U bent vervolgens kunnen verwijzen naar de gegevens die zijn opgeslagen in de storage-account.

![Schermopname van het dialoogvenster gegevensbron toevoegen](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

In Visual Studio, kunt u gegevens lezen van blob-opslag, doen enkele gegevens manipuleren, functie-engineering en voert u de resulterende gegevens naar Azure Data Lake of Azure Blob Storage. Wanneer u verwijst naar de gegevens in blob-opslag, gebruikt u **wasb: / /** ; wanneer u verwijst naar de gegevens in Azure Data Lake, gebruik **swbhdfs: / /**

U kunt de volgende U-SQL-query's in Visual Studio:

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

Nadat de query wordt verzonden naar de server, wordt een diagram van de status van uw taak weergegeven.

![Schermopname van het dialoogvenster taakstatus](./media/vm-do-ten-things/USQL_Job_Status.PNG)

**Gegevens opvragen in Data Lake: U-SQL**

Nadat de gegevensset in Azure Data Lake wordt opgenomen, kunt u [U-SQL-taal](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) query's uitvoeren en de gegevens verkennen. U-SQL-taal is vergelijkbaar met T-SQL, maar sommige functies van C# worden gecombineerd, zodat gebruikers aangepaste modules, de gebruiker gedefinieerde functies en enzovoort schrijven kunnen. U kunt de scripts in de vorige stap.

Nadat de query wordt verzonden naar tripdata_summary-server. CSV kan worden gevonden binnenkort in **Azure Data Lake Explorer**, u mogelijk voorbeeld van de gegevens door met de rechtermuisknop op het bestand.

![Schermopname van het csv-bestand in Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

Het om bestandsinformatie te bekijken:

![Schermafbeelding van de samenvatting bestandsgegevens](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>HDInsight Hadoop-Clusters
Azure HDInsight is een beheerde Apache Hadoop, Spark, HBase en Storm-service in de cloud. U kunt eenvoudig werken met Azure HDInsight-clusters van de virtuele machine voor datatechnologie.

**Vereiste**

* Maken van uw Azure Blob storage-account van [Azure-portal](https://portal.azure.com). Dit opslagaccount wordt gebruikt voor het opslaan van gegevens voor HDInsight-clusters.

![Schermopname van het maken van HDInsight van Azure portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Azure HDInsight Hadoop-Clusters aanpassen vanuit [Azure-portal](../team-data-science-process/customize-hadoop-cluster.md)
  
  * Koppel het storage-account met uw HDInsight-cluster gemaakt wanneer deze wordt gemaakt. Dit opslagaccount wordt gebruikt voor toegang tot gegevens die kunnen worden verwerkt binnen het cluster.

![Koppeling naar het opslagaccount dat is gemaakt met HDInsight-cluster](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Schakel **RAS** met het hoofdknooppunt van het cluster nadat deze is gemaakt. Vergeet niet de RAS-referenties die u hier opgeeft, u ze in de volgende procedure moet.

![Externe toegang tot het HDInsight-cluster](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Een Azure Machine Learning-werkruimte maken. Uw Machine Learning-experimenten worden opgeslagen in deze Machine Learning-werkruimte. Selecteer de gemarkeerde opties in de portal, zoals wordt weer gegeven in de volgende scherm afbeelding:

![Een Azure Machine Learning-werkruimte maken](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Voer vervolgens de parameters voor uw werkruimte

![Parameters voor Machine Learning-werkruimte invoeren](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Uploaden van gegevens met behulp van IPython Notebook. Importeer eerst de vereiste pakketten, plug-referenties, een database maken in uw storage-account en gegevens laden in HDI-clusters.

```python
# Import required Packages
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


# Create the connection to Hive using ODBC
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


# Create Hive database and tables
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


# Upload data from blob storage to HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

* U kunt ook kunt u volgen dit [scenario](../team-data-science-process/hive-walkthrough.md) NYC Taxi gegevens te uploaden naar HDI-cluster. Belangrijke stappen omvatten:
  
  * AzCopy: gezipte CSV van een openbare blob downloaden naar uw lokale map
  * AzCopy: uploaden uitgepakte CSV van vanaf lokale map voor het HDI-cluster
  * Meld u aan bij het hoofdknooppunt van Hadoop-cluster en bereid voor experimentele data-analyse

Nadat de gegevens zijn geladen met HDI-cluster, kunt u uw gegevens in Azure Storage Explorer kunt controleren. En u hebt een database nyctaxidb in HDI-cluster gemaakt.

**Gegevens verkennen: Hive-Query's in python**

Omdat de gegevens zich in de Hadoop-cluster, kunt u het pakket pyodbc verbinding maken met Hadoop-Clusters en query-database met behulp van Hive voor exploratie en functie-engineering. Hier vindt u de bestaande tabellen die in de vereiste stap is gemaakt.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Bestaande tabellen weergeven](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Bekijk het aantal records in elke maand en de frequentie van de Gekantelde of niet in de tabel trip:

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

![Diagram van het aantal records in elke maand](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

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

![Diagram van de frequenties tip](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

U kunt ook de afstand tussen de ophalen locatie en dropoff locatie compute en vervolgens vergelijken met de afstand van de fietstocht.

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

![Bovenste rijen van de tabel ophalen en dropoff](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Diagram van ophalen/dropoff afstand tot reis afstand](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Nu gaan we een omlaag steekproef (% 1) set gegevens voorbereiden voor modellen. U kunt deze gegevens gebruiken in Machine Learning reader-module.

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

Inhoud van de koppeling nu invoegen in de voor gaande interne tabel

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

Na enige tijd ziet u dat de gegevens zijn geladen in Hadoop-clusters:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Bovenste rijen met gegevens uit de tabel](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

**Lezen van gegevens van HDI met Machine Learning: reader-module**

U kunt ook de **lezer** module in Machine Learning Studio om de database in Hadoop-cluster. Sluit de referenties van uw HDI-clusters en Azure Storage-Account om in te schakelen gebouw machine learning-modellen met behulp van de database in HDI-clusters.

![Eigenschappen van de module Reader](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

De beoordeelde gegevensset kan vervolgens worden weergegeven:

![Beoordeelde gegevensset weergeven](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse--databases"></a>Azure SQL Data Warehouse & databases
Azure SQL Data Warehouse is een elastische datawarehouse als service met geavanceerde SQL Server-ervaring.

U kunt uw Azure SQL Data Warehouse inrichten door de instructies in deze [artikel](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Nadat u uw Azure SQL Data Warehouse inricht, kunt u dit [scenario](../team-data-science-process/sqldw-walkthrough.md) voor het uploaden van gegevens, verkennen en modelleren met behulp van gegevens in de SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB is een NoSQL-database in de cloud. Dit kunt u werken met documenten, zoals JSON en kunt u opslaan en opvragen van de documenten.

Hier volgen de stappen van de per-vereisten voor toegang tot Azure Cosmos DB van de DSVM:

1. De Python-SDK van Azure Cosmos DB is al geïnstalleerd op de DSVM (uitvoeren ```pip install pydocumentdb --upgrade``` vanaf de opdrachtprompt om bij te werken)
2. Maak een Azure Cosmos DB-account en een database van [Azure-portal](https://portal.azure.com)
3. 'Azure Cosmos DB hulpprogramma voor migratie' downloaden van [hier](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) en uitpakken naar een map van uw keuze
4. Importeren van JSON-gegevens (gegevens Vulkaan) die zijn opgeslagen op een [openbare blob](https://cahandson.blob.core.windows.net/samples/volcano.json) in Cosmos DB met de volgende opdrachtparameters aan het hulpprogramma voor migratie (dtui.exe uit de map waarin u het hulpprogramma voor migratie van Cosmos DB hebt geïnstalleerd). Geef de locatie van de bron en doel met de volgende parameters:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Nadat u de gegevens importeert, kunt u naar Jupyter en open het notitieblok met de titel *DocumentDBSample* die python-code voor toegang tot Azure Cosmos DB en voer enkele eenvoudige query's bevat. U meer informatie over Cosmos DB door naar de service te [documentatiepagina](https://docs.microsoft.com/azure/cosmos-db/).

## <a name="8-power-bi-reports--dashboards"></a>8. Power BI rapporten &-dash boards 
U kunt het JSON-bestand van Volcano visualiseren vanuit het voor gaande Cosmos DB voor beeld in Power BI bureau blad om visuele inzichten in de gegevens te krijgen. Gedetailleerde stappen zijn beschikbaar in de [Power BI-artikel](../../cosmos-db/powerbi-visualize.md). Hier volgen de hoofdstappen:

1. Open Power BI Desktop en 'gegevens ophalen." Geef de URL als: https://cahandson.blob.core.windows.net/samples/volcano.json
2. U ziet de JSON-records worden geïmporteerd als een lijst
3. De lijst converteren naar een tabel zodat Power BI met dezelfde werken kunt
4. Vouw de kolommen door te klikken op het uitvouwpictogram (het account met het pictogram "pijl-links en een pijl naar rechts" aan de rechterkant van de kolom)
5. U ziet dat de locatie van een recordveld '' is. Vouw de record en selecteert u alleen de coördinaten. Coördinaat is een lijstkolom
6. Een nieuwe kolom voor de lijst met coördinaat kolom converteren naar een afzonderlijke LatLong-kolom van met door komma's, samenvoegen van de twee elementen in de coördinaat velden met behulp van de formule toevoegen ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Ten slotte converteert de ```Elevation``` kolom decimale en selecteer de **sluiten** en **toepassen**.

In plaats van de vorige stappen, kunt u de volgende code of scripts naar buiten de stappen in de geavanceerde Editor in Power BI kunt u de gegevenstransformaties schrijven in een querytaal voor plakken.

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

U hebt nu de gegevens in uw Power BI-gegevensmodel. Uw Power BI desktop moet er als volgt uitzien:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

U kunt beginnen het bouwen van rapporten en visualisaties met behulp van het gegevensmodel. U kunt de stappen in deze [Power BI-artikel](../../cosmos-db/powerbi-visualize.md#build-the-reports) om een rapport te maken.

## <a name="9-dynamic-dsvm-scaling"></a>9. Dynamische DSVM-schaling 
U kunt de schaal omhoog en omlaag de DSVM om te voldoen aan de projectbehoeften van uw. Als u niet nodig hebt voor het gebruik van de virtuele machine in het weekend of 's avonds, u kunt alleen de virtuele machine af van de [Azure-portal](https://portal.azure.com).

> [!NOTE]
> U compute-kosten als u alleen met de knop Afsluiten besturingssysteem op de virtuele machine.  
> 
> 

Als u wilt verwerken van een grootschalige analyse en meer CPU en/of geheugen en/of schijf capaciteit nodig hebt, kunt u een grote keuze van de VM-grootten in termen van CPU-kernen, op basis van GPU-exemplaren vinden voor deep learning, geheugencapaciteit en schijftypen (met inbegrip van de SSD-schijven) die voldoen aan uw compute- en budgettaire behoeften. De volledige lijst met virtuele machines samen met de prijzen per uur compute vindt u op de [prijzen voor Azure Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) pagina.

Op dezelfde manier als uw behoeften voor de verwerkingscapaciteit van de virtuele machine wordt verkleind (bijvoorbeeld: u hebt een grote workload verplaatst naar een Hadoop- of een Spark-cluster), kunt u de schaal in het cluster op basis van de [Azure-portal](https://portal.azure.com) en Ga naar de instellingen van uw VM-exemplaar. 

## <a name="10-add-more-tools"></a>10. Meer hulp middelen toevoegen
Er zijn verschillende hulpprogramma's die vooraf zijn ingebouwd in de DSVM die veel van de algemene gegevens analytics moet kunt oplossen. Dit bespaart u tijd doordat hoeft te installeren en configureren uw omgevingen één voor één en u geld besparen door betaalt alleen voor resources die u gebruikt.

U kunt gebruikmaken van andere Azure data en analyse-services bevatten in dit artikel voor het verbeteren van uw omgeving voor analyse. In sommige gevallen mogelijk wens extra hulpprogramma's, met inbegrip van sommige bedrijfseigen externe hulpprogramma's. U hebt volledige beheerderstoegang op de virtuele machine voor het installeren van nieuwe hulpprogramma's die u nodig hebt. U kunt ook extra pakketten installeren in Python / R die niet vooraf zijn geïnstalleerd. Voor Python kunt u een ```conda``` of ```pip```. Voor R kunt u de ```install.packages()``` in de R-console of gebruik de IDE en kies "**pakketten** -> **pakketten installeren...** ".

## <a name="deep-learning"></a>Deep learning

Naast de voorbeelden framework op basis van is een set van uitgebreide scenario's ook beschikbaar die zijn gevalideerd op de DLVM. Deze scenario's kunt u snel uw ontwikkeling van deep learning-toepassingen in domeinen, zoals afbeeldingen en tekst/language understanding. Meer zelfstudies voor end-to-end in verschillende domeinen en -technologie blijft worden toegevoegd.   


- Het [uitvoeren van Neural-netwerken in verschillende Frameworks](https://github.com/ilkarman/DeepLearningFrameworks): Een uitgebreid overzicht waarin wordt uitgelegd hoe u code van het ene naar het andere Framework migreert. Ook zien hoe om te vergelijken model en de prestaties van de tijd van frameworks uitvoeren. 

- [Een hand leiding met instructies voor het bouwen van een end-to-end oplossing voor het detecteren van producten in installatie kopieën](https://github.com/Azure/cortana-intelligence-product-detection-from-images): Image detectie is een techniek waarmee objecten binnen afbeeldingen kunnen worden gevonden en geclassificeerd. Deze technologie is de mogelijkheid om enorme voordelen in veel echte leven domeinen. Bijvoorbeeld, kunnen detailhandelaren deze techniek gebruiken om te bepalen welk product een klant is opgehaald uit de plank. Deze informatie helpt op zijn beurt winkels productinventaris beheren. 

- [Deep learning voor audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) in deze zelfstudie leert u hoe u met het trainen van een deep learning-model voor detectie audio gebeurtenis op de [stedelijke geluiden gegevensset](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) en biedt een overzicht van hoe u met audiogegevens werkt.

- [Classificatie van tekst documenten](https://github.com/anargyri/lstm_han): In dit scenario ziet u hoe u twee verschillende Neural-netwerk architecturen bouwt en traint: Hiërarchische aandacht voor netwerk en LSTM-netwerk (Long Short Term Memory). Deze neurale netwerken voor het gebruik van de Keras-API voor deep learning voor het classificeren van documenten. Keras is een front-end tot drie van de populairste diep gaande lessen: Microsoft Cognitive Toolkit, tensor flow en Theano.

## <a name="summary"></a>Samenvatting
Hieronder volgen slechts enkele van de dingen die u dit op de Microsoft Data Science Virtual Machine doen kunt. Er zijn veel meer dingen die u doen kunt om deze een effectieve analytics-omgeving.

