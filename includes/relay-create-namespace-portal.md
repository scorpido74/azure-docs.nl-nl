---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76021108"
---
1. Meld u aan bij [Azure Portal][Azure portal].
1. Selecteer **Een resource maken**. Selecteer vervolgens **Integratierelais** > **Relay**. Als u **Relay** niet in de lijst ziet staan, selecteert u rechtsboven **Alles weergeven**.
1. Selecteer **Maken**en voer een naamruimtenaam in het veld **Naam** in. Azure-portal controleert of de naam beschikbaar is.
1. Kies een Azure-abonnement waarin u de naamruimte wilt maken.
1. Kies [voor resourcegroep](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)een bestaande resourcegroep waarin u de naamruimte wilt plaatsen of maak een nieuwe groep.  
1. Selecteer het land of de regio waarin uw naamruimte moet worden gehost.

    ![Een naamruimte maken][create-namespace]

1. Selecteer **Maken**. De Azure-portal maakt uw naamruimte en maakt deze in. Na enkele minuten stelt het systeem resources ter beschikking voor uw account.

### <a name="get-management-credentials"></a>Beheerreferenties ophalen

1. Selecteer **Alle resources**en kies vervolgens de nieuw gemaakte naamruimtenaam.
1. Selecteer **Beleid voor gedeelde toegang**.  
1. Selecteer onder **Beleid voor gedeelde toegang** de optie **RootManageSharedAccessKey**.
1. Selecteer onder **SAS-beleid: RootManageSharedAccessKey**de knop **Kopiëren** naast **primaire verbindingstekenreeks**. Met deze actie wordt de verbindingstekenreeks naar uw klembord overgenomen voor later gebruik. Plak deze waarde in Kladblok of een andere tijdelijke locatie.
1. Herhaal de vorige stap om de waarde voor de **Primaire sleutel** te kopiëren en plakken naar een tijdelijke locatie zodat u deze later kunt gebruiken.  

    ![verbinding-reeks][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
