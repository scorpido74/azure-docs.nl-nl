---
title: Een Azure Lab Services Lab uit teams verwijderen
description: Meer informatie over het verwijderen van een Azure Lab Services Lab uit teams.
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 1d2fe73d33a88e595f42b47c1e7122dcbdfe1063
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094452"
---
# <a name="delete-labs-within-teams"></a>Laboratoria verwijderen in teams

In dit artikel wordt beschreven hoe u een Lab verwijdert uit de app **Azure Lab Services** .

## <a name="prerequisites"></a>Vereisten

* [Maak een Lab Services-account](tutorial-setup-lab-account.md#create-a-lab-account) in de Azure Portal.
* [Ga aan de slag en maak een Lab service Lab in teams](how-to-get-started-create-lab-within-teams.md).

## <a name="delete-labs"></a>Labs verwijderen

Een lab dat is gemaakt in teams kan worden verwijderd op de website van de [test services](https://labs.azure.com) door het lab rechtstreeks te verwijderen, zoals wordt beschreven in [klassikale labs beheren in Azure Lab Services](how-to-manage-classroom-labs.md). 

Het verwijderen van het lab wordt ook geactiveerd wanneer het team wordt verwijderd. Als het team waarin het lab is gemaakt wordt verwijderd, wordt het lab automatisch 24 uur na de synchronisatie van de automatische gebruikers lijst geactiveerd. 

> [!IMPORTANT]
> Als u het tabblad verwijdert of de app verwijdert, wordt het lab niet verwijderd. 

Als het tabblad wordt verwijderd, hebben gebruikers in de lijst team lidmaatschap nog steeds toegang tot de virtuele machines op de [website van de test services](https://labs.azure.com) , tenzij het verwijderen van het lab expliciet wordt geactiveerd door het lab op de website te verwijderen of het team te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van het gebruik van Azure Lab Services in teams](lab-services-within-teams-overview.md)
- [Test gebruikers lijsten in teams beheren](how-to-manage-user-lists-within-teams.md)
- [De VM-groep van het lab in teams beheren](how-to-manage-vm-pool-within-teams.md)
- [Lab-schema's maken en beheren in teams](how-to-create-schedules-within-teams.md)
- [Toegang tot een virtuele machine in teams – student View](how-to-access-vm-for-students-within-teams.md)

