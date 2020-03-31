---
title: 'Snelstart: maak een Geo AI Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Een Geo AI Data Science Virtual Machine op Azure configureren en maken voor geospatiale analyses en machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: f3ff9bd64f54d8f83fd1889078e8a4c01827d135
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77525886"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Snelstart: een Geo Artificial Intelligence Virtual Machine instellen op Azure 

De Geo AI Data Science Virtual Machine (Geo-DSVM) is een uitbreiding van de populaire [Azure Data Science Virtual Machine](https://aka.ms/dsvm) die speciaal is geconfigureerd om AI en geospatiale analyses te combineren. De geospatiale analyses in de VM worden aangedreven door [ArcGIS Pro.](https://www.arcgis.com/features/index.html) De Data Science Virtual Machine (DSVM) maakt het mogelijk om machine learning en zelfs deep learning modellen snel te trainen. Om deze modellen te ontwikkelen, gebruikt het gegevens die zijn verrijkt met geografische informatie. De Geo-DSVM wordt alleen ondersteund op Windows 2016 DSVM. 

De AI-tools die zijn opgenomen in de Geo-DSVM omvatten de volgende:

- GPU-edities van populaire deep-learning frameworks zoals Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 en Chainer
- Hulpmiddelen voor het verkrijgen en vooraf verwerken van afbeeldings- en tekstuele gegevens
- Tools voor ontwikkelingsactiviteiten zoals Microsoft Machine Learning Server Developer Edition, Anaconda Python, Jupyter-notebooks voor Python en R, IdEs voor Python en R en SQL-databases
- ArcGIS Pro desktopsoftware van ESRI, samen met Python- en R-interfaces die kunnen werken met de georuimtelijke gegevens van uw AI-toepassingen
 

## <a name="create-your-geo-ai-data-science-vm"></a>Maak uw Geo AI Data Science VM

Voer de volgende stappen uit om een instantie van de GEO AI Data Science VM te maken:

1. Ga naar de virtuele machinevermelding op de [Azure-portal.](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows)
1. Selecteer **Onderaan maken** om een wizard te genereren:

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. De wizard vereist invoer voor elk van de vier stappen. Zie de volgende sectie voor gedetailleerde informatie over deze invoer.

### <a name="wizard-details"></a>Wizard-gegevens ###

**Basics**:

- **Naam:** de naam van de data science-server die u maakt.
    
- **Gebruikersnaam:** aanmeldings-id voor beheerdersaccounts.
    
- **Wachtwoord**: Wachtwoord voor beheerdersaccount.
    
- **Abonnement:** Als u meer dan één abonnement hebt, selecteert u het abonnement waarop de machine moet worden gemaakt en gefactureerd.
    
- **Resourcegroep:** u een nieuwe maken of een **lege** bestaande Azure-brongroep in uw abonnement gebruiken.
    
- **Locatie**: Selecteer het datacenter dat het meest geschikt is. Meestal is het degene die de meeste van uw gegevens heeft of die het dichtst bij uw fysieke locatie voor de snelste netwerktoegang. Als u van plan bent deep learning uit te voeren op een GPU, moet u een van de locaties in Azure kiezen met GPU VM-exemplaren uit de NC-serie. Momenteel zijn deze locaties: **Oost-VS, North Central US, South Central US, West US 2, Noord-Europa, West-Europa**. Raadpleeg voor de laatste lijst de pagina [Azure Products by Region](https://azure.microsoft.com/regions/services/) en zoek naar **NC-series** onder **Compute**. 
    
    
**Instellingen:** Selecteer een van de GPU-formaten van de NC-serie als u van plan bent om deep learning op een GPU uit te voeren op uw Geo DSVM. Anders u een van de op CPU gebaseerde exemplaren kiezen. Maak een opslagaccount voor uw vm. 
       
**Overzicht**: Controleer of alle ingevoerde gegevens juist zijn.
    
**Kopen:** Als u het inrichtingsproces wilt starten, klikt u op **Kopen**. Er wordt een link gegeven naar de voorwaarden van de service. De VM heeft geen extra kosten die verder gaan dan de rekenkosten voor de servergrootte die u in de stap **Grootte hebt** gekozen. 
 
 >[!NOTE]
 > De inrichting duurt ongeveer 20 tot 30 minuten. De status van de inrichting wordt weergegeven op de Azure-portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Toegang krijgen tot de Virtual Machine van Geo AI Data Science

 Nadat uw vm is gemaakt, u de hulpprogramma's gebruiken die erop zijn geïnstalleerd en vooraf zijn geconfigureerd. Er zijn menutegels en bureaubladpictogrammen voor veel van de gereedschappen. U hebt toegang tot de VM op extern bureaublad met behulp van de beheerdersaccountreferenties die u hebt geconfigureerd in de sectie **Basisbeginselen.**

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>ArcGIS Pro gebruiken dat is geïnstalleerd in de VM

Op de Geo-DSVM is arcgis pro-desktop vooraf geïnstalleerd en is de omgeving vooraf geconfigureerd om te werken met alle tools in de DSVM. Wanneer u ArcGIS start, wordt u gevraagd om referenties naar uw ArcGIS-account. Als u al een ArcGIS-account hebt en licenties voor de software hebt, u uw bestaande referenties gebruiken.  

![Arc-GIS-Aanmelding](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Anders u zich aanmelden voor een nieuw ArcGIS-account en -licentie, of een [gratis proefperiode](https://www.arcgis.com/features/free-trial.html)krijgen. 

![ArcGIS-gratis-trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Nadat u zich hebt aangemeld voor een standaard ArcGIS-account of een gratis proefperiode, u ArcGIS Pro autoriseren voor uw account door de instructies te volgen bij [Aan de slag met ArcGIS Pro.](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf)

Nadat u zich hebt aangemeld bij de ArcGIS Pro-desktop via uw ArcGIS-account, u de data science-tools gebruiken die op de VM zijn geïnstalleerd en geconfigureerd voor uw geospatiale analyses en machine learning-projecten.

## <a name="next-steps"></a>Volgende stappen

Gebruik de Geo AI Data Science VM met richtlijnen van de volgende bron:

* [De Geo AI Data Science VM gebruiken](use-geo-ai-dsvm.md)
