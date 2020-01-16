---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021108"
---
1. Meld u aan bij de [Azure Portal][Azure portal].
1. Selecteer **Een resource maken**. Selecteer vervolgens **Integratie** > **Relay**. Als u **Relay** niet in de lijst ziet staan, selecteert u rechtsboven **Alles weergeven**.
1. Selecteer **maken**en voer een naam voor de naam ruimte in het veld **naam** in. Azure Portal controleert of de naam beschikbaar is.
1. Kies een Azure-abonnement waarin u de naam ruimte wilt maken.
1. Kies voor [resource groep](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)een bestaande resource groep waarin de naam ruimte moet worden geplaatst of maak een nieuwe.  
1. Selecteer het land of de regio waarin uw naam ruimte moet worden gehost.

    ![Een naamruimte maken][create-namespace]

1. Selecteer **Maken**. Met de Azure Portal wordt uw naam ruimte gemaakt en ingeschakeld. Na enkele minuten stelt het systeem resources ter beschikking voor uw account.

### <a name="get-management-credentials"></a>Beheerreferenties ophalen

1. Selecteer **alle resources**en kies vervolgens de naam van de zojuist gemaakte naam ruimte.
1. Selecteer **beleid voor gedeelde toegang**.  
1. Selecteer onder **Beleid voor gedeelde toegang** de optie **RootManageSharedAccessKey**.
1. Selecteer onder **SAS-beleid: RootManageSharedAccessKey**de knop **kopiëren** naast **primaire verbindings reeks**. Met deze actie wordt de connection string naar het klem bord gekopieerd voor later gebruik. Plak deze waarde in Kladblok of een andere tijdelijke locatie.
1. Herhaal de vorige stap om de waarde voor de **Primaire sleutel** te kopiëren en plakken naar een tijdelijke locatie zodat u deze later kunt gebruiken.  

    ![verbinding-reeks][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
