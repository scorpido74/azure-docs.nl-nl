---
title: Een Azure Data Catalog maken
description: In deze snelstart wordt beschreven hoe u een Azure-gegevenscatalogus maakt met behulp van de Azure-portal.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 08/01/2019
ms.openlocfilehash: 854760ea0158d356cc5449c9f1fdab6161588640
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "68976862"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Snelstart: een Azure-gegevenscatalogus maken

Azure Data Catalog is een volledig beheerde cloudservice die als een registratie- en detectiesysteem voor gegevensassets van ondernemingen fungeert. Zie [Wat is Azure Data Catalog?](overview.md) voor een gedetailleerd overzicht.

Met deze snelle start u aan de slag met het maken van een Azure-gegevenscatalogus.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Om te beginnen moet je het:

* Een [Microsoft Azure-abonnement.](https://azure.microsoft.com/)
* U moet uw eigen [Azure Active Directory-tenant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)hebben.

Als u gegevenscatalogus wilt instellen, moet u de eigenaar of mede-eigenaar zijn van een Azure-abonnement.

## <a name="create-a-data-catalog"></a>Een gegevenscatalogus maken

U kunt per organisatie (Azure Active Directory-domein) slechts één gegevenscatalogus inrichten. Als de eigenaar of mede-eigenaar van een Azure-abonnement dat deel uitmaakt van dit Azure Active Directory-domein al een catalogus heeft gemaakt, u daarom geen catalogus opnieuw maken, zelfs niet als u meerdere Azure-abonnementen hebt. Als u wilt testen of er al een gegevenscatalogus is gemaakt door een gebruiker in uw Azure Active Directory-domein, gaat u naar de [startpagina van Azure Data Catalog](http://azuredatacatalog.com) en controleert u of u de catalogus ziet. Als er al een catalogus voor u is gemaakt, slaat u de volgende procedure over en gaat u naar de volgende sectie.

1. Ga naar de [Azure-portal](https://portal.azure.com) > **Maak een bron** en selecteer **Gegevenscatalogus**.

    ![Knop Maken van Azure-gegevenscatalogus](media/data-catalog-get-started/data-catalog-create.png)

2. Geef een **naam** op voor de gegevenscatalogus, het **abonnement** dat u wilt gebruiken, de **locatie** voor de catalogus en de **prijscategorie**. Selecteer vervolgens **Maken**.

3. Ga naar de [startpagina van Azure Data Catalog](http://azuredatacatalog.com) en klik op **Gegevens publiceren**.

   ![Azure Data Catalog - de knop Gegevens publiceren](media/data-catalog-get-started/data-catalog-publish-data.png)

   U ook naar de startpagina van de gegevenscatalogus gaan op de [servicepagina Gegevenscatalogus](https://azure.microsoft.com/services/data-catalog) door **Aan de slag te gaan.**

   ![Azure Data Catalog - marketingstartpagina](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Ga naar de pagina **Instellingen.**

    ![Azure Data Catalog - gegevenscatalogus inrichten](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Verruim **prijzen** en verifieer de **editie** van uw Azure Data Catalog (Gratis of Standaard).

    ![Azure Data Catalog - editie selecteren](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Als u *De standaardeditie* als prijscategorie kiest, u **beveiligingsgroepen** uitbreiden en Active Directory-beveiligingsgroepen toestaan om toegang te krijgen tot gegevenscatalogus en automatische aanpassing van facturering mogelijk te maken.

    ![Beveiligingsgroepen voor Azure-gegevenscatalogus](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Vouw **Catalogusgebruikers** uit en klik op **Toevoegen** om gebruikers toe te voegen aan de gegevenscatalogus. Je wordt automatisch aan deze groep toegevoegd.

    ![Azure Data Catalog - gebruikers](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Als u *De standaardeditie* als prijscategorie kiest, u **woordenlijstbeheerders** uitbreiden en op **Toevoegen** klikken om gebruikers van woordenlijstbeheerders toe te voegen. Je wordt automatisch aan deze groep toegevoegd.

    ![Azure-gegevenscataloguswoordenbeheerders](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Vouw **Catalogusbeheerders** uit en klik op **Toevoegen** om meer beheerders toe te voegen aan de gegevenscatalogus. Je wordt automatisch aan deze groep toegevoegd.

    ![Azure Data Catalog - beheerders](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Vouw **portaltitel** uit en voeg extra tekst toe die wordt weergegeven in de portaltitel.

    ![Titel azure-gegevenscatalogusportal](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Zodra u de pagina **Instellingen** hebt voltooid, navigeert u vervolgens naar de **pagina Publiceren.**

    ![Azure Data Catalog - gemaakt](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Zoeken naar een gegevenscatalogus in de Azure Portal

1. Ga op een afzonderlijk tabblad in de webbrowser of in een afzonderlijke geopend browservenster naar de [Azure Portal](https://portal.azure.com) en meld u aan met hetzelfde account dat u in de vorige stap hebt gebruikt voor het maken van de gegevenscatalogus.

2. Selecteer **Alle services** en klik op **Gegevenscatalogus**.

    ![Azure-gegevenscatalogus: blader door Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    U ziet de gegevenscatalogus die u hebt gemaakt.

    ![Azure Data Catalog - catalogus in lijst bekijken](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Klik op de catalogus die u hebt gemaakt. U ziet de blade **Gegevenscatalogus** in de portal.

   ![Azure Data Catalog - blade in portal](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. U kunt de eigenschappen van de gegevenscatalogus bekijken en bijwerken. Klik bijvoorbeeld op **Prijscategorie** en wijzig de editie.

    ![Azure Data Catalog - prijscategorie](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Volgende stappen

In deze snelle start hebt u geleerd hoe u een Azure-gegevenscatalogus voor uw organisatie maken. U nu gegevensbronnen registreren in uw gegevenscatalogus.

> [!div class="nextstepaction"]
> [Gegevensbronnen registreren in Azure-gegevenscatalogus](data-catalog-how-to-register.md)
