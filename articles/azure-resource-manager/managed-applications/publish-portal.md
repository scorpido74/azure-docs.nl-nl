---
title: Beheerde apps publiceren via portal
description: Hier ziet u hoe u de Azure-portal gebruikt om een azure-beheerde toepassing te maken die is bedoeld voor leden van uw organisatie.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 05302d92f2304be35a7b88fac6fabfc17b13c63e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651720"
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Een servicecatalogustoepassing publiceren via Azure-portal

U de Azure-portal gebruiken om [beheerde toepassingen](overview.md) te publiceren die zijn bedoeld voor leden van uw organisatie. Zo kan een IT-afdeling beheerde toepassingen publiceren die zorgen voor naleving van organisatiestandaarden. Deze beheerde toepassingen zijn beschikbaar via de servicecatalogus, niet Azure Marketplace.

## <a name="prerequisites"></a>Vereisten

Wanneer u een beheerde toepassing publiceert, geeft u een identiteit op om de resources te beheren. We raden u aan een Azure Active Directory-gebruikersgroep op te geven. Zie Een groep maken en [leden toevoegen in Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)als u een Gebruikersgroep voor Azure Active Directory wilt maken. 

Het .zip-bestand dat de beheerde toepassingsdefinitie bevat, moet beschikbaar zijn via een URI. We raden je aan je .zip-bestand te uploaden naar een opslagblob. 

## <a name="create-managed-application-with-portal"></a>Beheerde toepassing maken met portal

1. Selecteer + **Nieuw**in de linkerbovenhoek .

   ![Nieuwe service](./media/publish-portal/new.png)

1. Zoeken naar **servicecatalogus**.

1. Scrol in de resultaten totdat u **Service Catalog Managed Application Definition vindt.** Selecteer het.

   ![Zoeken naar beheerde toepassingsdefinities](./media/publish-portal/select-managed-apps-definition.png)

1. Selecteer **Maken** om het proces van het maken van de beheerde toepassingsdefinitie te starten.

   ![Beheerde toepassingsdefinitie maken](./media/publish-portal/create-definition.png)

1. Geef waarden op voor naam, weergavenaam, beschrijving, locatie, abonnement en resourcegroep. Geef voor het uri-bestand van het pakketbestand het pad op naar het zip-bestand dat u hebt gemaakt.

   ![Waarden opgeven](./media/publish-portal/fill-application-values.png)

1. Wanneer u de sectie Verificatie- en vergrendelingsniveau hebt bereikt, selecteert u **Autorisatie toevoegen**.

   ![Autorisatie toevoegen](./media/publish-portal/add-authorization.png)

1. Selecteer een Azure Active Directory-groep om de resources te beheren en selecteer **OK**.

   ![Autorisatiegroep toevoegen](./media/publish-portal/add-auth-group.png)

1. Wanneer u alle waarden hebt opgegeven, selecteert u **Maken**.

   ![Een beheerde toepassing maken](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van beheerde toepassingen](overview.md) voor een inleiding tot beheerde toepassingen.
* Zie [Voorbeeldprojecten voor beheerde toepassingen voor](sample-projects.md)beheerde toepassingen voor beheerde toepassingen .
* Zie [Aan de slag met CreateUiDefinition](create-uidefinition-overview.md) voor meer informatie over het maken van een UI-definitiebestand voor een beheerde toepassing.