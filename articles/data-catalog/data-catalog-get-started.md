---
title: Een Azure Data Catalog maken
description: In deze Quick Start wordt beschreven hoe u een Azure Data Catalog maakt met behulp van de Azure Portal.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 08/01/2019
ms.openlocfilehash: 854760ea0158d356cc5449c9f1fdab6161588640
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68976862"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Snelstartgids: een Azure Data Catalog maken

Azure Data Catalog is een volledig beheerde cloudservice die als een registratie- en detectiesysteem voor gegevensassets van ondernemingen fungeert. Zie [Wat is Azure Data Catalog?](overview.md) voor een gedetailleerd overzicht.

Deze Snelstartgids helpt u aan de slag te gaan met het maken van een Azure Data Catalog.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een [Microsoft Azure](https://azure.microsoft.com/) -abonnement.
* U moet uw eigen Azure Active Directory- [Tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)hebben.

Als u Data Catalog wilt instellen, moet u de eigenaar of mede-eigenaar van een Azure-abonnement zijn.

## <a name="create-a-data-catalog"></a>Een gegevens catalogus maken

U kunt per organisatie (Azure Active Directory-domein) slechts één gegevenscatalogus inrichten. Als de eigenaar of mede-eigenaar van een Azure-abonnement dat deel uitmaakt van dit Azure Active Directory domein al een catalogus heeft gemaakt, kunt u de catalogus dus niet opnieuw maken, zelfs als u meerdere Azure-abonnementen hebt. Als u wilt testen of er al een gegevenscatalogus is gemaakt door een gebruiker in uw Azure Active Directory-domein, gaat u naar de [startpagina van Azure Data Catalog](http://azuredatacatalog.com) en controleert u of u de catalogus ziet. Als er al een catalogus voor u is gemaakt, slaat u de volgende procedure over en gaat u naar de volgende sectie.

1. Ga naar de [Azure Portal](https://portal.azure.com) > **een resource maken** en selecteer **Data Catalog**.

    ![Knop Azure Data Catalog maken](media/data-catalog-get-started/data-catalog-create.png)

2. Geef een **naam** op voor de gegevens catalogus, het **abonnement** dat u wilt gebruiken, de **locatie** voor de catalogus en de **prijs categorie**. Selecteer vervolgens **maken**.

3. Ga naar de [startpagina van Azure Data Catalog](http://azuredatacatalog.com) en klik op **Gegevens publiceren**.

   ![Azure Data Catalog - de knop Gegevens publiceren](media/data-catalog-get-started/data-catalog-publish-data.png)

   U kunt ook naar de Data Catalog start pagina op de [pagina Data Catalog service](https://azure.microsoft.com/services/data-catalog) gaan door **aan de slag**te selecteren.

   ![Azure Data Catalog - marketingstartpagina](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Ga naar de pagina **instellingen** .

    ![Azure Data Catalog - gegevenscatalogus inrichten](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Vouw **prijzen** uit en controleer uw Azure Data Catalog- **editie** (gratis of standaard).

    ![Azure Data Catalog - editie selecteren](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Als u *Standard* Edition kiest als prijs categorie, kunt u **beveiligings groepen** uitvouwen en Active Directory beveiligings groepen toestaan om toegang te krijgen tot Data Catalog en automatische aanpassing van facturering in te scha kelen.

    ![Azure Data Catalog beveiligings groepen](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Vouw **Catalogusgebruikers** uit en klik op **Toevoegen** om gebruikers toe te voegen aan de gegevenscatalogus. U wordt automatisch aan deze groep toegevoegd.

    ![Azure Data Catalog - gebruikers](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Als u *Standard* Edition kiest als prijs categorie, kunt u de **verklarende woorden lijst beheerders** uitvouwen en op **toevoegen** klikken om gebruikers met een woorden lijst beheerder toe te voegen. U wordt automatisch aan deze groep toegevoegd.

    ![Beheerders van Azure Data Catalog woorden lijst](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Vouw **Catalogusbeheerders** uit en klik op **Toevoegen** om meer beheerders toe te voegen aan de gegevenscatalogus. U wordt automatisch aan deze groep toegevoegd.

    ![Azure Data Catalog - beheerders](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Vouw de titel van de **Portal** uit en voeg extra tekst toe die wordt weer gegeven in de titel van de portal.

    ![Azure Data Catalog-Portal titel](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Zodra u de pagina **instellingen** hebt voltooid, gaat u naar de pagina **publiceren** .

    ![Azure Data Catalog - gemaakt](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Zoeken naar een gegevenscatalogus in de Azure Portal

1. Ga op een afzonderlijk tabblad in de webbrowser of in een afzonderlijke geopend browservenster naar de [Azure Portal](https://portal.azure.com) en meld u aan met hetzelfde account dat u in de vorige stap hebt gebruikt voor het maken van de gegevenscatalogus.

2. Selecteer **alle services** en klik vervolgens op **Data Catalog**.

    ![Azure Data Catalog--bladeren door Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    U ziet de gegevens catalogus die u hebt gemaakt.

    ![Azure Data Catalog - catalogus in lijst bekijken](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Klik op de catalogus die u hebt gemaakt. U ziet de blade **Gegevenscatalogus** in de portal.

   ![Azure Data Catalog - blade in portal](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. U kunt de eigenschappen van de gegevenscatalogus bekijken en bijwerken. Klik bijvoorbeeld op **Prijscategorie** en wijzig de editie.

    ![Azure Data Catalog - prijscategorie](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Azure Data Catalog maakt voor uw organisatie. U kunt nu gegevens bronnen registreren in uw Data Catalog.

> [!div class="nextstepaction"]
> [Gegevens bronnen registreren in Azure Data Catalog](data-catalog-how-to-register.md)
