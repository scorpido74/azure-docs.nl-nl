---
title: 'Quickstart: Een Geo AI-DSVM maken'
description: Configureer en maak een geo AI-Data Science Virtual Machine op Azure voor georuimtelijke analyse en machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 6b32d1f76091d7bbb8f870402020d0ac247d425b
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170719"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Quickstart: Een geografische, kunst matige intelligentie virtuele machine instellen op Azure 

De geo AI-Data Science Virtual Machine (geo-DSVM) is een uitbrei ding van de populaire [Azure-data Science virtual machine](https://aka.ms/dsvm) die speciaal is geconfigureerd voor het combi NEREN van AI-en georuimtelijke analyses. De georuimtelijke analyses in de virtuele machine worden aangestuurd door [ArcGIS Pro](https://www.arcgis.com/features/index.html). Met de Data Science Virtual Machine (DSVM) kunt u snel training krijgen over machine learning en zelfs diep gaande modellen. Voor het ontwikkelen van deze modellen maakt het gebruik van gegevens die zijn verrijkt met geografische informatie. De geo-DSVM wordt alleen ondersteund in Windows 2016 DSVM. 

De AI-hulpprogram ma's die deel uitmaken van de geo-DSVM zijn onder andere:

- GPU-edities van populaire diep gaande frameworks zoals Microsoft Cognitive Toolkit, tensor flow, Keras, Caffe2 en Chainer
- Hulpprogram ma's voor het verkrijgen en voorverwerken van afbeeldings-en tekstuele gegevens
- Hulpprogram ma's voor ontwikkelings activiteiten zoals Microsoft Machine Learning Server Developer Edition, Anaconda python, Jupyter-notebooks voor python en R, Ide's voor python en R, en SQL-data bases
- ArcGIS Pro Desktop-software van ESRI, samen met python-en R-interfaces die kunnen werken met de georuimtelijke gegevens van uw AI-toepassingen
 

## <a name="create-your-geo-ai-data-science-vm"></a>Uw Geo AI Data Science VM maken

Voer de volgende stappen uit om een exemplaar van de geo AI-Data Science VM te maken:

1. Ga naar de vermelding van de virtuele machine op de [Azure Portal](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Selecteer onderaan **maken** om een wizard te genereren:

   ![Create-geo-AI-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. De wizard vereist invoer voor elk van de vier stappen. Zie de volgende sectie voor meer informatie over deze invoer.

### <a name="wizard-details"></a>Wizard Details ###

**Grondbeginselen van**:

- **Naam**: De naam van de data science-server die u maakt.
    
- **Gebruikers naam**: Aanmeldings-ID van beheerders account.
    
- **Wachtwoord**: Wacht woord voor beheerders account.
    
- **Abonnement**: Als u meer dan één abonnement hebt, selecteert u een waar de machine zich moet worden gemaakt en worden kosten in rekening gebracht.
    
- **Resourcegroep**: U kunt een nieuw item maken of een **lege** bestaande Azure-resource groep in uw abonnement gebruiken.
    
- **Locatie**: Selecteer het Data Center dat het meest geschikt is. Normaal gesp roken is het het meren deel van uw gegevens of het zich het dichtst bij uw fysieke locatie voor snelle toegang tot het netwerk. Als u van plan bent om diepere lessen uit te voeren op een GPU, moet u een van de locaties in azure kiezen die GPU VM-exemplaren van de NC-serie hebben. Op dit moment zijn de volgende locaties: VS- **Oost, Noord-Centraal VS, Zuid-Centraal VS, VS-West 2, Europa-Noord, Europa-West**. Ga voor de meest recente lijst naar de pagina [Azure-producten per regio](https://azure.microsoft.com/regions/services/) en zoek naar **NC-serie** onder **Compute**. 
    
    
**Instellingen voor**: Selecteer een van de VM-grootten van de NC-Series GPU als u van plan bent om diepere lessen uit te voeren op een GPU op uw Geo-DSVM. Anders kunt u een van de op CPU gebaseerde exemplaren kiezen. Maak een opslagaccount voor uw virtuele machine. 
       
**Samenvatting**: Controleer of alle informatie die u hebt ingevoerd juist is.
    
**Kopen**: Als u het inrichtings proces wilt starten, klikt u op **kopen**. Een koppeling wordt met de voorwaarden van de service geleverd. Er zijn geen extra kosten in rekening gebracht voor de virtuele machine die u in de stap **grootte** hebt gekozen voor de grootte van de server. 
 
 >[!NOTE]
 > Het inrichten moet ongeveer 20 tot 30 minuten duren. De status van de inrichting wordt weergegeven op de Azure-portal.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Toegang tot de Geo AI Data Science Virtual Machine

 Nadat de VM is gemaakt, bent u klaar om te beginnen met het gebruik van de hulpprogram ma's die zijn geïnstalleerd en vooraf zijn geconfigureerd. Voor veel van de hulpprogram ma's zijn er tegels en bureaublad pictogrammen voor het menu Start. U kunt de virtuele machine via extern bureau blad openen door de referenties van het beheerders account te gebruiken die u in de sectie **basis beginselen** hebt geconfigureerd.

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>In de virtuele machine met behulp van ArcGIS Pro geïnstalleerd

Op de geo-DSVM is ArcGIS Pro Desktop vooraf geïnstalleerd en is de omgeving vooraf geconfigureerd om te werken met alle hulpprogram ma's in de DSVM. Wanneer u ArcGIS start, wordt u gevraagd om referenties voor uw ArcGIS-account. Als u al een ArcGIS-account hebt en licenties voor de software hebt, kunt u uw bestaande referenties gebruiken.  

![Boog-GIS-aanmelding](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Anders kunt u zich aanmelden voor een nieuw ArcGIS-account en een nieuwe licentie, of een [gratis proef versie](https://www.arcgis.com/features/free-trial.html)downloaden. 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Nadat u zich hebt aangemeld voor een standaard ArcGIS-account of een gratis proef versie, kunt u ArcGIS Pro voor uw account autoriseren door de instructies te volgen in aan de [slag met ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Nadat u zich hebt aangemeld bij de ArcGIS Pro Desktop via uw ArcGIS-account, bent u klaar om te beginnen met het gebruik van de data Science-hulpprogram ma's die zijn geïnstalleerd en geconfigureerd op de virtuele machine voor uw georuimtelijke analyse en machine learning-projecten.

## <a name="next-steps"></a>Volgende stappen

Begin met het gebruik van de geo AI-Data Science VM met hulp van de volgende resource:

* [De Geo AI Data Science VM gebruiken](use-geo-ai-dsvm.md)
