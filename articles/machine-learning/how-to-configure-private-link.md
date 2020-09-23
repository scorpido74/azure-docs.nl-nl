---
title: Een persoonlijk eind punt configureren (preview-versie)
titleSuffix: Azure Machine Learning
description: Persoonlijke Azure-koppeling gebruiken om veilig toegang te krijgen tot uw Azure Machine Learning-werk ruimte vanuit een virtueel netwerk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/03/2020
ms.openlocfilehash: 83927c9df9a4f1a6ab32c15c481898ec68f53c4c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898142"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Een persoonlijke Azure-koppeling configureren voor een Azure Machine Learning-werk ruimte (preview)

In dit document leert u hoe u een persoonlijke Azure-koppeling kunt gebruiken met uw Azure Machine Learning-werk ruimte. Zie [overzicht van virtuele netwerken en privacy](how-to-network-security-overview.md) voor meer informatie over het instellen van een virtueel netwerk voor Azure machine learning.

> [!IMPORTANT]
> Het gebruik van een persoonlijke Azure-koppeling met Azure Machine Learning werk ruimte bevindt zich momenteel in de open bare preview. Deze functionaliteit is alleen beschikbaar in de regio's **VS-Oost**, **VS Zuid-Centraal** en **VS West 2** . Deze preview-versie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de persoonlijke Azure-koppeling kunt u verbinding maken met uw werk ruimte met behulp van een persoonlijk eind punt. Het persoonlijke eind punt is een reeks privé-IP-adressen in uw virtuele netwerk. Vervolgens kunt u de toegang tot uw werk ruimte beperken tot alleen de privé-IP-adressen. Een persoonlijke koppeling helpt het risico van gegevens exfiltration te verminderen. Zie het artikel over een [persoonlijke Azure-koppeling](/azure/private-link/private-link-overview) voor meer informatie over privé-eind punten.

> [!IMPORTANT]
> Persoonlijke Azure-koppeling heeft geen invloed op Azure Control-vlak (beheer bewerkingen), zoals het verwijderen van de werk ruimte of het beheren van reken resources. Bijvoorbeeld maken, bijwerken of verwijderen van een berekenings doel. Deze bewerkingen worden normaal gesp roken uitgevoerd via het open bare Internet. Gegevenslaag bewerkingen, zoals het gebruik van Azure Machine Learning Studio, Api's (inclusief gepubliceerde pijp lijnen) of de SDK gebruiken het persoonlijke eind punt.
>
> Er kunnen problemen optreden bij het openen van het persoonlijke eind punt voor uw werk ruimte als u gebruikmaakt van Mozilla Firefox. Dit probleem kan betrekking hebben op DNS via HTTPS in Mozilla. We raden u aan micro soft Edge van Google Chrome als tijdelijke oplossing te gebruiken.

> [!TIP]
> Azure Machine Learning Compute-exemplaar kan worden gebruikt met een werk ruimte en een persoonlijk eind punt. Deze functie is momenteel beschikbaar in de open bare preview in de regio's **VS-Oost**, **VS Zuid-Centraal** en **VS West 2** .

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Een werk ruimte maken die gebruikmaakt van een persoonlijk eind punt

> [!IMPORTANT]
> Op dit moment ondersteunen we alleen het inschakelen van een persoonlijk eind punt bij het maken van een nieuwe Azure Machine Learning-werk ruimte.

De sjabloon op [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) kan worden gebruikt om een werk ruimte met een persoonlijk eind punt te maken.

Zie [een Azure Resource Manager sjabloon gebruiken om een werk ruimte voor Azure machine learning te maken](how-to-create-workspace-template.md)voor meer informatie over het gebruik van deze sjabloon, met inbegrip van persoonlijke eind punten.

## <a name="using-a-workspace-over-a-private-endpoint"></a>Een werk ruimte gebruiken via een persoonlijk eind punt

Omdat de communicatie met de werk ruimte alleen is toegestaan vanuit het virtuele netwerk, moeten ontwikkel omgevingen die gebruikmaken van de werk ruimte lid zijn van het virtuele netwerk. Bijvoorbeeld een virtuele machine in het virtuele netwerk.

> [!IMPORTANT]
> Om te voor komen dat de verbinding tijdelijk wordt verbroken, raadt micro soft aan de DNS-cache te wissen op computers die verbinding maken met de werk ruimte nadat een persoonlijke koppeling is ingeschakeld. 

Raadpleeg de [virtual machines documentatie](/azure/virtual-machines/)voor meer informatie over Azure virtual machines.


## <a name="next-steps"></a>Volgende stappen

Zie het artikel [overzicht van virtuele netwerken en privacy](how-to-network-security-overview.md) voor meer informatie over het beveiligen van uw Azure machine learning-werk ruimte.
