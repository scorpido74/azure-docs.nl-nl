---
title: 'Referentie: Windows-DSVM'
description: Details over hulpprogram ma's die zijn opgenomen in de Windows-Data Science VM
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: be4397de477891f2a698c9f7dcb131da79479529
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200011"
---
# <a name="reference-windows-data-science-virtual-machine"></a>Referentie: Windows Data Science Virtual Machine

Hieronder vindt u een lijst met beschik bare hulpprogram ma's op uw Windows-Data Science Virtual Machine. 

## <a name="microsoft-machine-learning-server-developer-edition"></a>Microsoft Machine Learning Server Developer edition

U kunt micro soft Enter prise-bibliotheek voor uw analyse gebruiken omdat Machine Learning Server Developer Edition is geïnstalleerd op de VM. Machine Learning Server is voorheen bekend als Microsoft R Server, is een algemeen implementeerbaar analyse platform. Het is schaalbaar en commercieel ondersteund.

Machine Learning-Server biedt ondersteuning voor verschillende big data-statistieken, voorspellende modellen en machine learning-taken. Deze biedt ondersteuning voor het volledige aanbod van analytics: verkenning, analyse, visualisatie en modellering. Door en uitbreiden van open-source R en Python, is de Machine Learning Server compatibel met R en Python-scripts en functies. Het is ook compatibel met CRAN, pip en Conda-pakketten voor het analyseren van gegevens op de grote schaal.

Machine Learning Server lost de geheugenbeperkingen van open-source R door het toevoegen van parallelle en bloksgewijze verwerking van gegevens. Dit betekent dat u analyses kunt uitvoeren op veel grotere gegevens dan wat past in het hoofd geheugen. 

Visual Studio Community is opgenomen op de virtuele machine. Het bevat de R-hulpprogram ma's voor Visual Studio-en Python Tools for Visual Studio-uitbrei dingen (PTVS) die een volledige IDE bieden voor het werken met R of python. We bieden ook andere Ide's zoals [RStudio](https://www.rstudio.com) en [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) op de VM.

## <a name="python"></a>Python

Voor de ontwikkeling met behulp van python worden Anaconda python-distributies 2,7 en 3,6 geïnstalleerd. Deze distributies hebben de basis Python samen met ongeveer 300 van de meest populaire math, engineering en data analytics-pakketten. U kunt PTVS gebruiken, dat is geïnstalleerd in Visual Studio Community 2017. U kunt ook een van de Ide's gebruiken die is gebundeld met Anaconda, zoals inactief of Spyder. Zoek en open een van deze pakketten (Windows-logo toets + S).

> [!NOTE]
> Om te verwijzen de Python-Tools voor Visual Studio op Anaconda Python 2.7, die u wilt maken van aangepaste omgevingen voor elke versie. Als u deze omgevings paden wilt instellen in de Visual Studio 2017-Community, gaat u naar **hulpprogram ma's** > **python tools** > **python-omgevingen**. Selecteer vervolgens **+ aangepast**.

Anaconda python 3,6 is geïnstalleerd onder C:\Anaconda. Anaconda python 2,7 is geïnstalleerd onder C:\Anaconda\envs\python2. Zie de [PTVS-documentatie](https://docs.microsoft.com/visualstudio/python/installing-python-interpreters)voor gedetailleerde stappen.

## <a name="the-jupyter-notebook"></a>De Jupyter-Notebook

Anaconda-distributie wordt ook geleverd met de Jupyter Notebook, een omgeving om code en analyse te delen. De Jupyter-Notebook-server is vooraf geconfigureerd met Python 2.7, Python 3.x PySpark, Julia en R-kernels. Als u de Jupyter-server wilt starten en de browser voor toegang tot de notebook server wilt openen, gebruikt u het pictogram **Jupyter notebook** bureau blad.

We het pakket verschillende voorbeeldnotitieblokken in Python en R. Nadat u toegang Jupyter tot, geeft de notebooks laten zien hoe om te werken met de volgende technologieën:

* Machine Learning Server
* SQL Server Machine Learning Services, analyses in-data base
* Python
* Microsoft Cognitive Toolkit
* TensorFlow
* Andere Azure-technologieën

U vindt de koppeling naar de voor beelden op de start pagina van het notitie blok nadat u zich hebt geverifieerd voor de Jupyter Notebook met behulp van het wacht woord dat u eerder hebt gemaakt.

## <a name="visual-studio-community-2017"></a>Visual Studio Community 2017

De DSVM omvat Visual Studio community. Dit is een gratis versie van de populaire IDE van micro soft die u kunt gebruiken voor evaluatie doeleinden en kleine teams. Zie de [licentie voorwaarden voor micro soft-software](https://www.visualstudio.com/support/legal/mt171547).

Open Visual Studio met behulp van het bureaublad pictogram of het menu **Start** . Zoek naar Program ma's (Windows-logo toets + S), gevolgd door **Visual Studio**. Van daaruit kunt u projecten maken in talen zoals C#python, R en node. js. Geïnstalleerde invoegtoepassingen maken het handig om te werken met de volgende Azure-services:

* Azure Data Catalog
* Azure HDInsight voor Hadoop en Spark
* Azure Data Lake

Een invoeg toepassing met de naam Azure Machine Learning voor Visual Studio code integreert ook met Azure Machine Learning en helpt u snel AI-toepassingen te bouwen.

> [!NOTE]
> Mogelijk is, krijgt u een bericht dat uw evaluatieperiode is verlopen. Voer de referenties van uw Microsoft-account. Of maak een nieuwe gratis account voor toegang tot Visual Studio Community.

## <a name="sql-server-2017-developer-edition"></a>SQL Server 2017 Developer edition

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

## <a name="azure"></a>Azure

Verschillende Azure-hulpprogramma's zijn geïnstalleerd op de virtuele machine:

* Een snelkoppeling op het bureaublad gaat u naar de Azure SDK-documentatie.
* Gebruik AzCopy om gegevens in en uit uw Azure Storage-account te kopiëren. Als u wilt zien gebruik, voer **Azcopy** achter de opdrachtprompt.
* Gebruik Azure Storage Explorer om te bladeren door de objecten die u in uw Azure Storage-account opslaat. Er worden ook gegevens gekopieerd van en naar Azure Storage. Als u dit hulp programma wilt openen, voert u **Storage Explorer** in het **Zoek** veld in. Of vinden op de Windows **Start** menu.
* AdlCopy kopieert gegevens naar Azure Data Lake. Als u wilt zien gebruik, voer **adlcopy** in een opdrachtprompt.
* Het hulp programma dtui kopieert gegevens van en naar Azure Cosmos DB, een NoSQL-data base in de Cloud. Voer **dtui** in een opdrachtprompt.
* De Integration runtime kopieert gegevens tussen on-premises gegevens bronnen en de Cloud. Het wordt gebruikt in hulpprogramma's zoals Azure Data Factory.
* Gebruik Azure PowerShell om uw Azure-resources te beheren in de Power shell-script taal. Het ook geïnstalleerd op de virtuele machine.

## <a name="power-bi"></a>Power BI

De DSVM wordt geleverd met Power BI Desktop geïnstalleerd om u te helpen bij het bouwen van Dash boards en visualisaties. Met dit hulpprogramma kunt gegevens ophalen uit verschillende bronnen, uw dashboards en rapporten maken en deze publiceren naar de cloud. Zie de [Power bi-site](https://powerbi.microsoft.com)voor meer informatie. U kunt Power BI Desktop vinden in het menu **Start** .

> [!NOTE]
> U hebt een Microsoft Office 365-account voor toegang tot Power BI nodig.

## <a name="azure-machine-learning-sdk-for-python"></a>Azure Machine Learning SDK voor python

Gegevens wetenschappers en AI-ontwikkel aars gebruiken de Azure Machine Learning SDK voor python om machine learning werk stromen te bouwen en uit te voeren met de [Azure machine learning service](../service/overview-what-is-azure-ml.md). U kunt communiceren met de service in Jupyter-notebooks of een andere python IDE met behulp van open-source frameworks zoals tensor flow en scikit-learn.

De Python-SDK is vooraf geïnstalleerd op de Microsoft Data Science Virtual Machine. Zie [python gebruiken om](../service/quickstart-create-workspace-with-python.md)aan de slag te gaan met Azure machine learning om de PYTHON-SDK te gebruiken.

## <a name="more-microsoft-development-tools"></a>Meer ontwikkel Hulpprogramma's van micro soft

U kunt het [installatie programma van het micro soft-webplatform](https://www.microsoft.com/web/downloads/platform.aspx) gebruiken om andere ontwikkel Hulpprogramma's van micro soft te zoeken en te downloaden. Er is ook een snelkoppeling naar het hulp programma op het bureau blad van micro soft Data Science Virtual Machine.  

## <a name="important-directories-on-the-virtual-machine"></a>Belang rijke mappen op de virtuele machine

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

Hebt u nog meer vragen? Overweeg een [ondersteunings ticket](https://azure.microsoft.com/support/create-ticket/)te maken.
