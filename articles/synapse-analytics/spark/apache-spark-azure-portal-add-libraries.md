---
title: Bibliotheken voor Apache Spark toevoegen en beheren in Azure Synapse Analytics
description: Meer informatie over het toevoegen en beheren van bibliotheken die worden gebruikt door Apache Spark in Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 83dfd1b4df37018329b5d7a707e9b65fdf0782a6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427833"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Bibliotheken voor Apache Spark toevoegen en beheren in Azure Synapse Analytics

Apache Spark is afhankelijk van veel bibliotheken om functionaliteit te bieden. Deze bibliotheken kunnen worden uitgebreid of vervangen door extra bibliotheken of bijgewerkte versies van oudere bibliotheken.

Python-pakketten kunnen worden toegevoegd op het niveau sparkpool (voorbeeld) en .jar-gebaseerde pakketten kunnen worden toegevoegd op het niveau van de taakdefinitie van Spark.

## <a name="adding-or-updating-python-libraries"></a>Python-bibliotheken toevoegen of bijwerken

Apache Spark in Azure Synapse Analytics heeft een volledige Anacondas-installatie plus extra bibliotheken. De volledige lijst met bibliotheken is te vinden op [de ondersteuning van de Apache Spark-versie.](apache-spark-version-support.md)

Wanneer een Spark-instantie wordt opgestart, wordt een nieuwe virtuele omgeving gemaakt met behulp van deze installatie als basis. Daarnaast kan een *requirements.txt-bestand* `pip freeze` (uitvoer van de opdracht) worden gebruikt om de virtuele omgeving te upgraden. De pakketten in dit bestand voor installatie of upgrade worden gedownload van PyPi op het moment van het opstarten van het cluster. Dit vereistenbestand wordt gebruikt telkens wanneer een Spark-instantie wordt gemaakt vanuit die Spark-groep.

> [!IMPORTANT]
>
> - Als het pakket dat u installeert groot is of veel tijd in beslag neemt om te installeren, heeft dit invloed op de opstarttijd van de Spark-instantie.
> - Pakketten waarvoor compilerondersteuning nodig is tijdens het installeren, zoals GCC, worden niet ondersteund.
> - Pakketten kunnen niet worden gedegradeerd, alleen worden toegevoegd of geüpgraded.

### <a name="requirements-format"></a>Indeling vereisten

In het volgende fragment wordt de indeling voor het vereistenbestand weergegeven. De naam van het PyPi-pakket wordt weergegeven samen met een exacte versie. Dit bestand volgt de indeling beschreven in de [pip freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) referentie documentatie. In dit voorbeeld wordt een specifieke versie vastgespeld. U in dit bestand ook niet groter dan en minder dan versies opgeven.

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Gebruikersinterface van python-bibliotheek

De gebruikersinterface voor het toevoegen van bibliotheken bevindt zich op het tabblad **Extra instellingen** van de **groeppagina Apache Spark maken** op de Azure-portal.

Upload het configuratiebestand van de omgeving met de bestandskiezer in het gedeelte **Pakketten** van de pagina.

![Python-bibliotheken toevoegen](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Python-bibliotheken toevoegen")

## <a name="next-steps"></a>Volgende stappen

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentatie apache vonk](https://spark.apache.org/docs/2.4.4/)
