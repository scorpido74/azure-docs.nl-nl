---
title: 'Quickstart: Een Geo AI Data Science Virtual Machine maken'
titleSuffix: Azure Data Science Virtual Machine
description: Configureer en maak een Geo AI Data Science Virtual Machine op Azure voor georuimtelijke analyse en machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 61d401a543032b1a206a4477f04d7e8e209c0f2a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83646611"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Quickstart: Een Geo Artificial Intelligence Virtual Machine instellen op Azure 

De Geo AI Data Science Virtual Machine (Geo-DSVM) is een uitbreiding van de populaire [Azure Data Science Virtual Machine](https://aka.ms/dsvm) die speciaal is geconfigureerd voor het combineren van AI en georuimtelijke analyses. De georuimtelijke analyse in de VM wordt aangestuurd door [ArcGIS Pro](https://www.arcgis.com/features/index.html). Met de Data Science Virtual Machine (DSVM) maakt snelle training mogelijk voor machine learning en zelfs deep-learning-modellen. Voor het ontwikkelen van deze modellen maakt het gebruik van gegevens die zijn verrijkt met geografische informatie. De Geo-DSVM wordt alleen ondersteund in Windows 2016 DSVM. 

De AI-hulpprogram ma's die deel uitmaken van de Geo-DSVM zijn onder andere:

- GPU-edities van populaire deep learning-frameworks zoals Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 en Chainer
- Hulpprogramma's voor het verkrijgen en voorverwerken van afbeeldings- en tekstuele gegevens
- Hulpprogramma's voor ontwikkelingsactiviteiten zoals Microsoft Machine Learning Server Developer Edition, Anaconda python, Jupyter-notebooks voor Python en R, IDE's voor Python en R, en SQL-databases
- ArcGIS Pro Desktop-software van ESRI, samen met Python-en R-interfaces die kunnen werken met de georuimtelijke gegevens uit uw AI-toepassingen
 

## <a name="create-your-geo-ai-data-science-vm"></a>Uw Geo AI Data Science VM maken

Voer de volgende stappen uit om een exemplaar van de Geo AI Data Science VM te maken:

1. Ga naar de lijst met virtuele machines in de [Azure-portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Selecteer **Maken** onderaan om een wizard te genereren:

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. De wizard vereist invoer voor elk van de vier stappen. Lees de volgende onderwerpen voor gedetailleerde informatie over deze invoer.

### <a name="wizard-details"></a>Details wizard ###

**Basisprincipes**:

- **Naam**: De naam van de Data Science-server die u wilt maken.
    
- **Gebruikersnaam**: Aanmeldings-id van het beheerdersaccount.
    
- **Wachtwoord**: Wachtwoord voor het beheerdersaccount.
    
- **Abonnement**: Als u meer dan één abonnement hebt, selecteert u datgene waarop de machine moet worden gemaakt en gefactureerd.
    
- **Resourcegroep**: U kunt een nieuw item maken of een **lege** bestaande Azure-resourcegroep in uw abonnement gebruiken.
    
- **Locatie**: Selecteer het meest geschikte datacentrum. Doorgaans is dit het datacenter waarop de meeste van uw gegevens zijn opgeslagen, of het datacenter dat fysiek het dichtst bij u in de buurt staat voor de snelste netwerktoegang. Als u van plan bent om deep learning uit te voeren op een GPU, moet u een van de locaties in Azure kiezen die GPU VM-exemplaren uit de NC-serie hebben. Op dit moment betreft het de volgende locaties: **US - oost, VS - noord-centraal, VS - zuid-centraal, US - west 2, Europa - noord, Europa - west**. Ga voor de meest recente lijst naar de pagina [Azure-producten per regio](https://azure.microsoft.com/regions/services/) en zoek naar **NC-serie** onder **Berekenen**. 
    
    
**Instellingen**: Selecteer een van de VM-grootten van de NC-Series GPU als u van plan bent om deep learning uit te voeren op een GPU op uw Geo DSVM. Anders kunt u een van de op CPU gebaseerde exemplaren kiezen. Maak een opslagaccount voor uw VM. 
       
**Summary**: Controleer of alle informatie die u hebt ingevoerd, correct is.
    
**Kopen**: Selecteer **Kopen** om het inrichtingsproces te starten. Er wordt een koppeling gegeven naar de servicevoorwaarden. Voor de VM worden geen andere kosten in rekening gebracht dan de rekenkracht voor de servergrootte die u bij de stap **Grootte** hebt ingevoerd. 
 
 >[!NOTE]
 > Het inrichten zal ongeveer 20 tot 30 minuten duren. De status van de inrichting wordt weergegeven in de Azure-portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Toegang tot de Geo AI Data Science Virtual Machine

 Nadat de VM is gemaakt, bent u klaar om te beginnen met het gebruik van de hulpprogramma's die zijn geïnstalleerd en vooraf zijn geconfigureerd. Voor veel van de hulpprogramma's zijn er tegels en bureaubladpictogrammen voor het menu Start. U kunt de VM openen via een extern bureaublad door de referenties van het beheerdersaccount te gebruiken die u hebt geconfigureerd in het gedeelte **Basis**.

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>ArcGIS Pro die is geïnstalleerd in de VM gebruiken

Op de Geo-DSVM is ArcGIS Pro Desktop vooraf geïnstalleerd en is de omgeving vooraf geconfigureerd om te werken met alle hulpprogramma's in de DSVM. Wanneer u ArcGIS start, wordt u gevraagd naar de aanmeldingsgegevens voor uw ArcGIS-account. Als u al een ArcGIS-account hebt en licenties voor de software hebt, kunt u uw bestaande aanmeldingsgegevens gebruiken.  

![Arc-GIS-Aanmelden](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Anders kunt u zich aanmelden voor een nieuw ArcGIS-account en een nieuwe licentie, of een [gratis proefversie verkrijgen](https://www.arcgis.com/features/free-trial.html). 

![ArcGIS-Gratis-Proefversie](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Nadat u zich hebt aangemeld voor een standaard ArcGIS-account of een gratis proefversie, kunt u ArcGIS Pro voor uw account autoriseren door de instructies te volgen op [Aan de slag met ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Nadat u zich hebt aangemeld bij het ArcGIS Pro-bureaublad via uw ArcGIS-account, bent u klaar om te beginnen met het gebruik van de Data Science-hulpprogramma's die zijn geïnstalleerd en geconfigureerd op de VM voor uw georuimtelijke analyse en machine learning-projecten.

## <a name="next-steps"></a>Volgende stappen

Begin met het gebruik van de Geo AI Data Science VM met hulp van de volgende resource:

* [De Geo AI Data Science VM gebruiken](use-geo-ai-dsvm.md)
