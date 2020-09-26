---
title: Verschillen van Azure Data Factory
description: Meer informatie over hoe de mogelijkheden voor gegevens integratie van Azure Synapse Analytics verschillen van die van Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343050"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Gegevens integratie in azure Synapse Analytics versus Azure Data Factory

In azure Synapse Analytics zijn de mogelijkheden voor gegevens integratie, zoals Synapse-pijp lijnen en gegevens stromen, gebaseerd op die van Azure Data Factory. Zie [Wat is Azure Data Factory](../../data-factory/introduction.md)voor meer informatie. Bijna alle mogelijkheden zijn identiek of vergelijkbaar en de documentatie wordt gedeeld tussen de twee services. In dit artikel worden de huidige verschillen tussen Azure Data Factory en Azure Synapse gemarkeerd en geïdentificeerd.

Als u wilt zien of een Azure Data Factory functie of artikel van toepassing is op Azure Synapse, controleert u de moniker boven aan het artikel.

![Is van toepassing op moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "Is van toepassing op moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Functies in Azure Data Factory niet gepland voor Azure Synapse

De volgende functies zijn beschikbaar in Azure Data Factory, maar zijn niet gepland voor Azure Synapse.

* De mogelijkheid om SSIS-pakketten te liften en te verplaatsen.
* Sneeuw als een sink in de Kopieer activiteit en toewijzing van gegevens stroom.
* De time-outwaarde voor de toewijzing van gegevens stroom voor Azure Integration runtime.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Azure Synapse-functies die niet worden ondersteund in Azure Data Factory

De volgende functies zijn beschikbaar in azure Synapse, maar zijn niet gepland voor Azure Data Factory.

* Spark-taak bewaking van gegevens stromen voor toewijzing is alleen beschikbaar in Synapse. In Synapse is de Spark-engine opgenomen in het abonnement van de gebruiker, zodat gebruikers gedetailleerde Spark-logboeken kunnen bekijken. In Azure Data Factory vindt taak uitvoering plaats op een door Azure Data Factory beheerd Spark-cluster. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory functies die zich anders gedragen in Synapse

De volgende functies gedragen zich op een andere manier of bestaan momenteel niet in azure Synapse. 

* Wrangling-gegevens stromen
* De galerie met oplossings sjablonen
* Git-integratie en een systeem eigen CI/CD-oplossing
* Integratie met Azure monitor
* De naam van resources na het publiceren wijzigen
* Configuratie van hybride integratie-runtime in een Synapse-werk ruimte. Een gebruiker kan geen beheerde VNet-IR en een Azure IR hebben.
* Integratie voor delen van runtime tussen Synapse-werk ruimten

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met gegevens integratie in uw Synapse-werk ruimte door te leren hoe u gegevens opneemt [in een Azure data Lake Storage Gen2-account](data-integration-data-lake.md).
