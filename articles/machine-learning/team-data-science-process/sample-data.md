---
title: Voorbeeldgegevens op verschillende Azure Storage-locaties - Team Data Science Process
description: Voorbeeldgegevens in Azure blob-containers, SQL Server- en Hive-tabellen om deze te beperken tot een kleinere, maar representatieveen en beter beheerbare grootte.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 22e91d50227fcb44c7b90478d76379c14161ae05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718600"
---
# <a name="sample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Voorbeeldgegevens in Azure Blob-containers, SQL Server en Hive-tabellen

In de volgende artikelen wordt beschreven hoe u gegevens samplen die zijn opgeslagen in een van de drie verschillende Azure-locaties:

* [**Azure blob container gegevens**](sample-data-blob.md) wordt bemonsterd door het te downloaden programmatisch en vervolgens bemonstering met monster Python-code.
* [**SQL Server-gegevens**](sample-data-sql-server.md) worden bemonsterd met zowel SQL als de Python-programmeertaal. 
* [**Hive-tabelgegevens**](sample-data-hive.md) worden bemonsterd met Hive-query's.

Deze bemonsteringstaak is een stap in het [Team Data Science Process (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

**Waarom voorbeeldgegevens?**

Als de gegevensset die u wilt analyseren groot is, is het meestal een goed idee om de gegevens te downsamplen om deze te beperken tot een kleinere, maar representatievere en beter beheerbare grootte. Inkrimping kan het begrijpen, verkennen en functieengineering van gegevens vergemakkelijken. Deze voorbeeldrol in het Cortana Analytics-proces is om snelle prototypen van de gegevensverwerkingsfuncties en machine learning-modellen mogelijk te maken.

