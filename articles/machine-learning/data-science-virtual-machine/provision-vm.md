---
title: Maken van een Windows Data Science Virtual Machine
titleSuffix: Azure
description: Configureren en een Data Science Virtual Machine in Azure maken voor analyse en machine learning.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 2b328ccf63259c7faf4debdc07313d24381313a5
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70047679"
---
# <a name="provision-a-windows-data-science-virtual-machine-on-azure"></a>Een Windows-Data Science Virtual Machine inrichten in azure

Micro soft Windows Data Science Virtual Machine (DSVM) is een installatie kopie van een virtuele machine (VM) van Windows Server 2016 in Azure. Het is vooraf geïnstalleerd en geconfigureerd met hulpprogram ma's voor gegevens analyse en machine learning.

## <a name="included-data-science-tools"></a>Inbegrepen data Science-hulpprogram ma's

De volgende hulpprogram ma's zijn opgenomen in een DSVM:

* Python-SDK voor de [Azure machine learning-service](../service/index.yml).
* [Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/index) Developer edition.
* Anaconda Python-distributie.
* Jupyter-Notebook met R, Python en PySpark-kernels.
* Microsoft Visual Studio Community.
* Microsoft Power BI Desktop.
* Microsoft SQL Server 2017 Developer edition.
* Een zelfstandig Apache Spark-exemplaar voor lokale ontwikkeling en testen.
* [JuliaPro](https://juliacomputing.com/products/juliapro.html).
* Machine learning en data analytics-hulpprogramma's:
  * Uitgebreide leer raamwerken: [Tensor flow](https://www.tensorflow.org/), [Chainer](https://chainer.org/), MXNet en Keras AI frameworks zijn opgenomen op de VM.
  * [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): Een snelle machine learning-systeem die ondersteuning biedt voor technieken zoals online hashing, allreduce, kortingen, learning2search en actieve en interactieve training.
  * [XGBoost](https://xgboost.readthedocs.org/en/latest/): Een hulpprogramma waarmee u snel en nauwkeurige boosted structuur-implementatie.
  * [Rattle](https://togaware.com/rattle/). Het hulp programma R Analytical waarmee u aan de slag gaat met gegevens analyse en machine learning in R. Het bevat de mogelijkheid om gegevens op basis van een gebruikers interface te verkennen en te model leren met automatische R-code generatie.
  * [Fridge](https://www.cs.waikato.ac.nz/ml/weka/): Visual gegevensanalyse en machine learning-software in Java.
  * [Apache-detail weergave](https://drill.apache.org/): Een schema's SQL query-engine voor Apache Hadoop-, NoSQL- en opslag in de cloud. ODBC- en JDBC-interfaces worden ondersteund voor het uitvoeren van query's NoSQL en bestanden van standard BI-hulpprogramma's zoals Power BI, Microsoft Excel en Tableau.
* Bibliotheken in R en Python voor gebruik in Azure Machine Learning en andere Azure-services.
* Git, inclusief Git bash, om te werken met broncode opslagplaatsen zoals GitHub en Azure DevOps. Git biedt verschillende populaire Linux-opdracht regel Programma's die toegankelijk zijn in Git-Bash en in een opdracht prompt. Voorbeelden zijn awk, sed, perl, grep, zoeken, wget en curl.
* Ontwikkelingsprogram ma's en editors (RStudio, PyCharm).

### <a name="about-data-science"></a>Over gegevens wetenschap

Wetenschappelijke gegevens omvat het doorlopen van een reeks taken:

1. Zoeken, laden en gegevens voorverwerken.
1. Bouwen en testen van modellen.
1. De modellen voor gebruik in intelligente toepassingen te implementeren.

Gegevenswetenschappers gebruiken verschillende hulpprogramma's voor deze taken. Kan het zijn tijdrovend zijn om de juiste versies van software en download en installeer vervolgens deze. De DSVM bespaart tijd door een kant-en-klare installatie kopie op te geven die kan worden ingericht op Azure, met verschillende populaire hulpprogram ma's die vooraf zijn geïnstalleerd en geconfigureerd.

Met DSVM jump-start u uw analyse project. U kunt werken met taken in verschillende talen, waaronder R, Python, SQL en C#. Visual Studio biedt een eenvoudig te gebruiken geïntegreerde ontwikkelomgeving (IDE) voor het ontwikkelen en testen van uw code. De Azure SDK is opgenomen in de virtuele machine, zodat u uw toepassingen kunt bouwen met behulp van services op het micro soft-Cloud platform.

Er zijn geen software kosten voor deze DSVM-installatie kopie. U betaalt alleen de kosten van het gebruik van Azure. Ze zijn afhankelijk van de grootte van de virtuele machine die u inricht. Zie de [pagina Data Science virtual machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows)voor meer informatie.

### <a name="other-dsvm-versions"></a>Andere DSVM-versies

* Een [Ubuntu](dsvm-ubuntu-intro.md) installatiekopie. Het bevat hulpprogram ma's die vergelijkbaar zijn met de DSVM, plus een paar diep gaande lessen.
* Een [Linux CentOS](linux-dsvm-intro.md) installatiekopie.
* De [edition van Windows Server 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-windows) van de Data Science Virtual Machine. Een paar hulpprogramma's zijn alleen beschikbaar op de editie van Windows Server 2016. In dit artikel geldt in alle andere gevallen ook voor de editie van Windows Server 2012.

## <a name="prerequisite"></a>Vereiste

Voor het maken van een Microsoft Data Science Virtual Machine, moet u een Azure-abonnement hebben. Zie [Gratis proefversie van Azure ophalen](https://azure.com/free).

## <a name="create-your-dsvm"></a>Uw DSVM maken

Een DSVM-exemplaar maken:

1. Ga naar de vermelding van de virtuele machine op de [Azure Portal](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016). U wordt mogelijk gevraagd om u aan te melden bij uw Azure-account als u nog niet bent aangemeld.
1. Selecteer de knop **maken** onderaan.

   ![De lijst met virtuele machines op de Azure Portal, met de knop maken](./media/provision-vm/configure-data-science-virtual-machine.png)

1. Voer de volgende informatie in voor het configureren van elk van de stappen die worden weer gegeven in het rechterdeel venster van de scherm opname:

   1. **Grondbeginselen van**:
      * **Naam**: Voer de naam van de DSVM in.
      * **VM-schijf type**: Selecteer **SSD** of **HDD**. Kies **HDD** als schijf type voor een NC_v1 GPU-instantie zoals Nvidia Tesla K80.
      * **Gebruikersnaam**: Voer de ID van het beheerders account in.
      * **Wachtwoord**: Voer het wacht woord voor het beheerders account in.
      * **Abonnement**: Als u meer dan één abonnement hebt, selecteert u de computer waarop de machine wordt gemaakt en gefactureerd.
      * **Resourcegroep**: Een nieuwe groep maken of een bestaand item gebruiken.
      * **Locatie**: Selecteer het Data Center dat het meest geschikt is. Voor de snelste netwerk toegang is het het Data Center dat het meren deel van uw gegevens heeft of zich het dichtst bij uw fysieke locatie bevindt.
   1. **Grootte**: Selecteer het server type dat voldoet aan de functionele vereisten en kosten beperkingen. Voor meer opties van VM-grootten, selecteer **Alles weergeven**.
   1. **Instellingen voor**:  
      * **Beheerde schijven gebruiken**. Kies **beheerde** als u wilt dat Azure voor het beheren van de schijven voor de virtuele machine. Als dat niet het geval is, moet u een nieuwe of bestaande storage-account opgeven.  
      * **Andere parameters**. U kunt de standaardwaarden gebruiken. Als u niet-standaard waarden wilt gebruiken, houdt u de muis aanwijzer boven de koppeling voor informatie over de specifieke velden.
   1. **Samenvatting**: Controleer of alle informatie die u hebt ingevoerd juist is. Selecteer **Maken**.

> [!NOTE]
> * De virtuele machine heeft geen extra kosten als gevolg van de reken kosten voor de server grootte die u in de stap **grootte** hebt gekozen.
> * Het inrichten duurt 10 tot 20 minuten. U kunt de status van uw virtuele machine weer geven op de Azure Portal.

## <a name="access-the-dsvm"></a>Toegang tot de DSVM

Nadat de virtuele machine is gemaakt en ingericht, kunt u deze openen via een verbinding met een extern bureau blad. Gebruik de referenties van het beheerders account die u hebt geconfigureerd in de **basis** stappen voor het maken van een virtuele machine. 

U bent klaar om te beginnen met de hulpprogramma's die zijn geïnstalleerd en geconfigureerd op de virtuele machine. Veel van de hulpprogram ma's zijn toegankelijk via tegels en bureaublad pictogrammen in het **Start** menu.

U kunt ook een DSVM aan Azure Notebooks koppelen om Jupyter-notebooks op de virtuele machine uit te voeren en de beperkingen van de gratis servicelaag over te slaan. Zie [notebook-projecten beheren en configureren](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)voor meer informatie.

<a name="tools"></a>

## <a name="tools-installed-on-the-dvsm"></a>Hulpprogram ma's geïnstalleerd op de DVSM

In de volgende secties vindt u meer informatie over de hulpprogram ma's die op het Data Science Virtual Machine zijn geïnstalleerd.

### <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

U kunt micro soft Enter prise-bibliotheek voor uw analyse gebruiken omdat Machine Learning Server Developer Edition is geïnstalleerd op de VM. Machine Learning Server is voorheen bekend als Microsoft R Server, is een algemeen implementeerbaar analyse platform. Het is schaalbaar en commercieel ondersteund.

Machine Learning-Server biedt ondersteuning voor verschillende big data-statistieken, voorspellende modellen en machine learning-taken. Deze biedt ondersteuning voor het volledige aanbod van analytics: verkenning, analyse, visualisatie en modellering. Door en uitbreiden van open-source R en Python, is de Machine Learning Server compatibel met R en Python-scripts en functies. Het is ook compatibel met CRAN, pip en Conda-pakketten voor het analyseren van gegevens op de grote schaal.

Machine Learning Server lost de geheugenbeperkingen van open-source R door het toevoegen van parallelle en bloksgewijze verwerking van gegevens. Dit betekent dat u analyses kunt uitvoeren op veel grotere gegevens dan wat past in het hoofd geheugen. 

Visual Studio Community is opgenomen op de virtuele machine. Het bevat de R-hulpprogram ma's voor Visual Studio-en Python Tools for Visual Studio-uitbrei dingen (PTVS) die een volledige IDE bieden voor het werken met R of python. We bieden ook andere Ide's zoals [RStudio](https://www.rstudio.com) en [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) op de VM.

### <a name="python"></a>Python

Voor de ontwikkeling met behulp van python worden Anaconda python-distributies 2,7 en 3,6 geïnstalleerd. Deze distributies hebben de basis Python samen met ongeveer 300 van de meest populaire math, engineering en data analytics-pakketten. U kunt PTVS gebruiken, dat is geïnstalleerd in Visual Studio Community 2017. U kunt ook een van de Ide's gebruiken die is gebundeld met Anaconda, zoals inactief of Spyder. Zoek en open een van deze pakketten (Windows-logo toets + S).

> [!NOTE]
> Om te verwijzen de Python-Tools voor Visual Studio op Anaconda Python 2.7, die u wilt maken van aangepaste omgevingen voor elke versie. Als u deze omgevings paden wilt instellen in de Visual Studio 2017-Community, gaat u naar **hulpprogram ma's** > **python tools** > **python-omgevingen**. Selecteer vervolgens **+ aangepast**.

Anaconda python 3,6 is geïnstalleerd onder C:\Anaconda. Anaconda python 2,7 is geïnstalleerd onder C:\Anaconda\envs\python2. Zie de [PTVS-documentatie](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters)voor gedetailleerde stappen.

### <a name="the-jupyter-notebook"></a>De Jupyter-Notebook

Anaconda-distributie wordt ook geleverd met de Jupyter Notebook, een omgeving om code en analyse te delen. De Jupyter-Notebook-server is vooraf geconfigureerd met Python 2.7, Python 3.x PySpark, Julia en R-kernels. Als u de Jupyter-server wilt starten en de browser voor toegang tot de notebook server wilt openen, gebruikt u het pictogram **Jupyter notebook** bureau blad.

We het pakket verschillende voorbeeldnotitieblokken in Python en R. Nadat u toegang Jupyter tot, geeft de notebooks laten zien hoe om te werken met de volgende technologieën:

* Machine Learning Server
* SQL Server Machine Learning Services, analyses in-data base
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Andere Azure-technologieën

U vindt de koppeling naar de voor beelden op de start pagina van het notitie blok nadat u zich hebt geverifieerd voor de Jupyter Notebook met behulp van het wacht woord dat u eerder hebt gemaakt.

### <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

De DSVM omvat Visual Studio community. Dit is een gratis versie van de populaire IDE van micro soft die u kunt gebruiken voor evaluatie doeleinden en kleine teams. Zie de [licentie voorwaarden voor micro soft-software](https://www.visualstudio.com/support/legal/mt171547).

Open Visual Studio met behulp van het bureaublad pictogram of het menu **Start** . Zoek naar Program ma's (Windows-logo toets + S), gevolgd door **Visual Studio**. Van daaruit kunt u projecten maken in talen zoals C#python, R en node. js. Geïnstalleerde invoegtoepassingen maken het handig om te werken met de volgende Azure-services:

* Azure Data Catalog
* Azure HDInsight voor Hadoop en Spark
* Azure Data Lake

Een invoeg toepassing met de naam Azure Machine Learning voor Visual Studio code integreert ook met Azure Machine Learning en helpt u snel AI-toepassingen te bouwen.

> [!NOTE]
> Mogelijk is, krijgt u een bericht dat uw evaluatieperiode is verlopen. Voer de referenties van uw Microsoft-account. Of maak een nieuwe gratis account voor toegang tot Visual Studio Community.

### <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

De DSVM wordt geleverd met een ontwikkelaars versie van SQL Server 2017 met Machine Learning Services. Deze SQL Server Edition bevindt zich in R of python en kan in-data base-analyses uitvoeren. 

Machine Learning-Services biedt een platform voor het ontwikkelen en implementeren van intelligente toepassingen. U kunt deze talen en veel pakketten van de community om te maken van modellen en voorspellingen voor uw SQL Server-gegevens genereren. Omdat de Machine Learning-Services, in de database, de R- en Python talen in SQL Server is geïntegreerd, kunt u analytics dicht bij de gegevens bewaren. Deze integratie elimineert de kosten en beveiligingsrisico's die zijn gekoppeld aan de verplaatsing van gegevens.

> [!NOTE]
> De editie van SQL Server Developer is alleen voor ontwikkelings- en testdoeleinden. U moet een licentie voor het in productie uitvoeren.

U hebt toegang tot SQL Server door Microsoft SQL Server Management Studio te openen. De naam van uw virtuele machine is ingevuld als **Server naam**. Windows-verificatie gebruiken wanneer u zich aanmelden als de beheerder van Windows. Wanneer u zich in SQL Server Management Studio, kunt u maken van andere gebruikers, databases maken, gegevens importeren en uitvoeren van SQL-query's.

Als u in-data base analyses wilt inschakelen met behulp van SQL Server Machine Learning Services, voert u de volgende opdracht uit als eenmalige actie in SQL Server Management Studio nadat u zich hebt aangemeld als server beheerder:

```
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS
```

Vervang `%COMPUTERNAME%` door de naam van uw virtuele machine.

### <a name="azure"></a>Azure

Verschillende Azure-hulpprogramma's zijn geïnstalleerd op de virtuele machine:

* Een snelkoppeling op het bureaublad gaat u naar de Azure SDK-documentatie.
* Gebruik AzCopy om gegevens in en uit uw Azure Storage-account te kopiëren. Als u wilt zien gebruik, voer **Azcopy** achter de opdrachtprompt.
* Gebruik Azure Storage Explorer om te bladeren door de objecten die u in uw Azure Storage-account opslaat. Er worden ook gegevens gekopieerd van en naar Azure Storage. Als u dit hulp programma wilt openen, voert u **Storage Explorer** in het **Zoek** veld in. Of vinden op de Windows **Start** menu.
* AdlCopy kopieert gegevens naar Azure Data Lake. Als u wilt zien gebruik, voer **adlcopy** in een opdrachtprompt.
* Het hulp programma dtui kopieert gegevens van en naar Azure Cosmos DB, een NoSQL-data base in de Cloud. Voer **dtui** in een opdrachtprompt.
* De Integration runtime kopieert gegevens tussen on-premises gegevens bronnen en de Cloud. Het wordt gebruikt in hulpprogramma's zoals Azure Data Factory.
* Gebruik Azure PowerShell om uw Azure-resources te beheren in de Power shell-script taal. Het ook geïnstalleerd op de virtuele machine.

### <a name="power-bi"></a>Power BI

De DSVM wordt geleverd met Power BI Desktop geïnstalleerd om u te helpen bij het bouwen van Dash boards en visualisaties. Met dit hulpprogramma kunt gegevens ophalen uit verschillende bronnen, uw dashboards en rapporten maken en deze publiceren naar de cloud. Zie de [Power bi-site](https://powerbi.microsoft.com)voor meer informatie. U kunt Power BI Desktop vinden in het menu **Start** .

> [!NOTE]
> U hebt een Microsoft Office 365-account voor toegang tot Power BI nodig.

### <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK voor python

Gegevens wetenschappers en AI-ontwikkel aars gebruiken de Azure Machine Learning SDK voor python om machine learning werk stromen te bouwen en uit te voeren met de [Azure machine learning service](../service/overview-what-is-azure-ml.md). U kunt communiceren met de service in Jupyter-notebooks of een andere python IDE met behulp van open-source frameworks zoals tensor flow en scikit-learn.

De Python-SDK is vooraf geïnstalleerd op de Microsoft Data Science Virtual Machine. Zie [python gebruiken om](../service/quickstart-create-workspace-with-python.md)aan de slag te gaan met Azure machine learning om de PYTHON-SDK te gebruiken.

## <a name="more-microsoft-development-tools"></a>Meer Microsoft-ontwikkelprogramma 's

U kunt het [installatie programma van het micro soft](https://www.microsoft.com/web/downloads/platform.aspx) -webplatform gebruiken om andere ontwikkel Hulpprogramma's van micro soft te zoeken en te downloaden. Er is ook een snelkoppeling naar het hulp programma op het bureau blad van micro soft Data Science Virtual Machine.  

## <a name="important-directories-on-the-vm"></a>Belangrijke mappen op de virtuele machine

| Item | Directory |
| --- | --- |
| Jupyter-Notebook server-configuraties | C:\ProgramData\jupyter |
| Jupyter-Notebook voorbeelden basismap | C:\dsvm\notebooks en c:\Users\\< gebruikers\>naam \notebooks |
| Andere voorbeelden | C:\dsvm\samples |
| Anaconda, standaard: Python 3,6 | C:\Anaconda |
| Anaconda Python 2.7 omgeving | C:\Anaconda\envs\python2 |
| Microsoft Machine Learning Server (zelfstandig) voor python | C:\Program Files\Microsoft\ML Server\PYTHON_SERVER |
| Standaard instantie R, Machine Learning Server (zelfstandig) | C:\Program Files\Microsoft\ML Server\R_SERVER |
| SQL Server Machine Learning Services in de data base-instantie Directory | C:\Program Files\Microsoft SQL Server\MSSQL14.MSSQLSERVER |
| Diverse hulpprogramma 's | C:\dsvm\tools |

> [!NOTE]
> Op de Windows Server 2012-versie van de DSVM en de Windows Server 2016 Edition vóór maart 2018 is de standaard Anaconda-omgeving python 2,7. De secundaire omgeving is python 3,5, te vinden op C:\Anaconda\envs\py35.

## <a name="next-steps"></a>Volgende stappen

* Bekijk de hulpprogram ma's op de DSVM door het menu **Start** te openen.
* Meer informatie over de Azure Machine Learning-service vindt u in [Wat is Azure machine learning service? en hoe](../service/overview-what-is-azure-ml.md) u Quick starts [en zelf studies](../service/index.yml)probeert te lezen.
* Ga in Verkenner naar C:\Program Files\Microsoft\ML Server\R_SERVER\library\RevoScaleR\demoScripts voor voor beelden die gebruikmaken van de RevoScaleR-bibliotheek in R die ondersteuning biedt voor gegevens analyse op ondernemings schaal. 
* Lees het artikel [tien dingen die u op het data Science virtual machine kunt doen](https://aka.ms/dsvmtenthings).
* Informatie over het bouwen van analytische oplossingen voor end-to-end systematisch met behulp van de [Team Data Science Process](../team-data-science-process/index.yml).
* Ga naar de [Azure AI Gallery](https://gallery.cortanaintelligence.com) services voor machine learning en data analytics-voorbeelden die gebruikmaken van Azure Machine Learning en gerelateerde gegevens in Azure. Daarnaast hebt u in het menu **Start** en op het bureau blad van de virtuele machine een pictogram voor deze galerie gegeven.
