---
title: Bibliotheken voor Apache Spark toevoegen en beheren in azure Synapse Analytics
description: Meer informatie over het toevoegen en beheren van bibliotheken die worden gebruikt door Apache Spark in azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 5eb67752a57ce7745b1128f5f5eb36414bfe993c
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392604"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Bibliotheken voor Apache Spark toevoegen en beheren in azure Synapse Analytics

Apache Spark is afhankelijk van veel bibliotheken om functionaliteit te bieden. Deze bibliotheken kunnen worden uitgebreid of vervangen door extra bibliotheken of bijgewerkte oudere versies.

Python-pakketten kunnen worden toegevoegd op het niveau van de Spark-groep (preview-versie) en. jar-pakketten kunnen worden toegevoegd op het niveau van de Spark-taak definitie.

## <a name="adding-or-updating-python-libraries"></a>Python-bibliotheken toevoegen of bijwerken

Apache Spark in azure Synapse Analytics beschikt over een volledige Anacondas-installatie plus extra bibliotheken. De lijst met volledige bibliotheken vindt u op [Apache Spark-versie ondersteuning](apache-spark-version-support.md).

Wanneer een Spark-exemplaar wordt gestart, wordt een nieuwe virtuele omgeving gemaakt met behulp van deze installatie als basis. Daarnaast kan een *requirements.txt* -bestand (uitvoer van de `pip freeze` opdracht) worden gebruikt voor het bijwerken van de virtuele omgeving. De pakketten die in dit bestand worden vermeld voor installatie of upgrade, worden gedownload van PyPi op het moment dat het cluster wordt gestart. Dit vereisten bestand wordt gebruikt wanneer een Spark-exemplaar wordt gemaakt vanuit die Spark-groep.

> [!IMPORTANT]
>
> - Als het pakket dat u installeert groot is of veel tijd nodig heeft om te worden geïnstalleerd, is dit van invloed op de start tijd van de Spark-instantie.
> - Pakketten waarvoor compiler ondersteuning is vereist op het moment van installatie, zoals GCC, worden niet ondersteund.
> - Pakketten kunnen niet worden gedowngraded, alleen toegevoegd of geüpgraded.

### <a name="requirements-format"></a>Indeling van vereisten

Het volgende code fragment toont de indeling voor het vereisten bestand. De naam van het PyPi-pakket wordt samen met een exacte versie weer gegeven. Dit bestand heeft de indeling die wordt beschreven in de hand leiding voor het [blok keren](https://pip.pypa.io/en/stable/reference/pip_freeze/) van de PIP. In dit voor beeld wordt een specifieke versie gespeld. U kunt ook de versies ' niet groter dan ' en ' kleiner dan ' opgeven in dit bestand.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Gebruikers interface van python-bibliotheek

De gebruikers interface voor het toevoegen van bibliotheken bevindt zich op het tabblad **extra instellingen** van de pagina **Apache Spark groep maken** op de Azure Portal.

Upload het omgevings configuratie bestand met behulp van de bestands kiezer in het gedeelte **pakketten** van de pagina.

![Python-bibliotheken toevoegen](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Python-bibliotheken toevoegen")

### <a name="verifying-installed-libraries"></a>Geïnstalleerde bibliotheken controleren

Voer de volgende code uit om te controleren of de juiste versies van de juiste bibliotheken zijn geïnstalleerd

```python
import pip #needed to use the pip functions
for i in pip.get_installed_distributions(local_only=True):
    print(i)
```

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentatie voor Apache Spark](https://spark.apache.org/docs/2.4.4/)
