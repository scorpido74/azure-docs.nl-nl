---
title: Machine Learning Services in Azure SQL Managed instance (preview-versie)
description: Dit artikel bevat een overzicht of Machine Learning Services in Azure SQL Managed instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 06/03/2020
ms.openlocfilehash: b5daf283df1ef5d6b42da5bf0a4652aedf2f6284
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84708736"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance-preview"></a>Machine Learning Services in Azure SQL Managed instance (preview-versie)

Machine Learning Services is een functie van Azure SQL Managed instance (preview) dat in-data base-machine learning biedt en zowel python-als R-scripts ondersteunt. De functie bevat micro soft python en R-pakketten voor predictive analytics met hoge prestaties en machine learning. De relationele gegevens kunnen worden gebruikt in scripts via opgeslagen procedures, een T-SQL-script met python-of R-instructies of python-of R-code met T-SQL.

> [!IMPORTANT]
> Machine Learning Services is een functie van een Azure SQL Managed instance die momenteel beschikbaar is als open bare preview.
> Deze preview-functie is in eerste instantie beschikbaar in een beperkt aantal regio's in de VS, Azië en Australië, met extra regio's die later worden toegevoegd.
>
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>
> [Meld u aan voor de preview-versie](#signup) hieronder.

## <a name="what-is-machine-learning-services"></a>Wat is Machine Learning Services?

Met Machine Learning Services in Azure SQL Managed Instance kunt u python-en R-scripts uitvoeren in de-data base. U kunt dit gebruiken om gegevens voor te bereiden en op te schonen, functie techniek uit te voeren en machine learning modellen in een Data Base te trainen, te evalueren en te implementeren. De functie voert uw scripts uit waarin de gegevens zich bevinden en elimineert de overdracht van de gegevens over het netwerk naar een andere server.

Gebruik Machine Learning Services met R/python-ondersteuning in Azure SQL Managed instance voor het volgende:

- **Voer r-en python-scripts uit om gegevens voorbereiding en gegevens verwerking voor algemeen gebruik** uit te voeren. u kunt nu uw R/python-scripts overbrengen naar Azure SQL Managed instance waar uw gegevens zich bevinden, in plaats van gegevens naar een andere server te verplaatsen om R-en python-scripts uit te voeren. U kunt voor komen dat gegevens verplaatsing en gerelateerde problemen met betrekking tot latentie, beveiliging en naleving overbodig zijn.

- **Machine learning modellen trainen in data base** : u kunt modellen trainen met behulp van open-source-algoritmen. U kunt uw training eenvoudig schalen naar de hele gegevensset, in plaats van te vertrouwen op voorbeeld gegevens sets die uit de Data Base worden gehaald.

- **Implementeer uw modellen en scripts in productie in opgeslagen procedures** : de scripts en getrainde modellen kunnen eenvoudigweg worden operationeel door ze in de opgeslagen T-SQL-procedures in te sluiten. Apps die verbinding maken met een beheerd exemplaar van Azure SQL kunnen profiteren van voor spellingen en intelligentie in deze modellen door alleen een opgeslagen procedure aan te roepen. U kunt ook de native T-SQL-functie voors PELLEn gebruiken om modellen te operationeel maken, zodat u snel kunt scoren in zeer gelijktijdige Score scenario's voor realtime.

Basis distributies van python en R zijn opgenomen in Machine Learning Services. U kunt open source-pakketten en-frameworks installeren en gebruiken, zoals PyTorch, tensor flow en scikit-learn, naast de micro soft-pakketten [revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-revoscalepy) en [microsoftml](https://docs.microsoft.com/sql/advanced-analytics/python/ref-py-microsoftml) voor python, en [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler), [Microsoftml](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml), [olapr](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-olapr)en [sqlrutils](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-sqlrutils) voor R.

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Registreren voor de preview-versie

Deze beperkte open bare preview is onderhevig aan de voor [waarden van Azure preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Als u geïnteresseerd bent in het toevoegen van het preview-programma en deze voor waarden accepteert, kunt u de inschrijving aanvragen door een ondersteunings ticket voor Azure te maken op [https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) . Voor ' probleem type ' selecteert u ' Technical ' voor ' service ' selecteert u ' SQL Database Managed instance ' en selecteert u ' Overig '. In uw aanvraag is dit de status die u wilt inschrijven voor een beperkte open bare preview van Machine Learning voor een SQL Managed instance met de volgende details: naam, regio en abonnements-ID van de logische server.

Zodra u bent Inge schreven in het programma, wordt u door micro soft naar de open bare preview gewerkt en Machine Learning Services voor uw bestaande of nieuwe data base in te scha kelen.

Machine Learning Services in SQL Managed instance wordt niet aanbevolen voor productie werkbelastingen tijdens de open bare preview.

## <a name="next-steps"></a>Volgende stappen

- Bekijk de belangrijkste verschillen ten opzichte [van SQL Server machine learning Services](machine-learning-services-differences.md).
- Zie [python-scripts uitvoeren](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)voor meer informatie over het gebruik van python in Machine Learning Services.
- Zie [r-scripts uitvoeren](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)voor meer informatie over het gebruik van r in Machine Learning Services.
- Zie de [documentatie voor sql machine learning](https://docs.microsoft.com/sql/machine-learning/)voor meer informatie over machine learning op andere SQL-platforms.
