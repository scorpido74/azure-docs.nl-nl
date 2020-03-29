---
title: 'Azure-portal: dynamische gegevensmaskering'
description: Aan de slag met SQL Database dynamic data masking in de Azure portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: a8098b31c6b389b640fc03e756da44c70d9f3a70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722115"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Aan de slag met SQL Database dynamic data masking met de Azure portal

In dit artikel ziet u hoe u [dynamische gegevensmaskering implementeert](sql-database-dynamic-data-masking-get-started.md) met de Azure-portal. U ook dynamische gegevensmaskering implementeren met [Azure SQL Database-cmdlets](https://docs.microsoft.com/powershell/module/az.sql/) of de [REST API.](https://docs.microsoft.com/rest/api/sql/)

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Dynamische gegevensmaskering voor uw database instellen met behulp van de Azure-portal

1. Start de Azure-portal op [https://portal.azure.com](https://portal.azure.com).
2. Navigeer naar de instellingenpagina van de database met de gevoelige gegevens die u wilt maskeren.
3. Klik op de tegel **Dynamische gegevensmaskering** waarmee de configuratiepagina **Dynamische gegevensmaskering** wordt gestart.

   * U ook omlaag scrollen naar de sectie **Bewerkingen** en op **Dynamisch maskeren klikken.**

     ![Navigatievenster](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. Op de configuratiepagina **Dynamische gegevensmaskering** ziet u mogelijk enkele databasekolommen die de engine voor het maskeren heeft gemarkeerd. Als u de aanbevelingen wilt accepteren, klikt u op **Masker toevoegen** voor een of meer kolommen en wordt er een masker gemaakt op basis van het standaardtype voor deze kolom. U de maskerfunctie wijzigen door op de maskerregel te klikken en de indeling van het maskerende veld te bewerken naar een andere indeling naar keuze. Klik op **Opslaan** om uw instellingen op te slaan.

    ![Navigatievenster](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Als u een masker wilt toevoegen voor een kolom in uw database, klikt u boven aan de **configuratiepagina Dynamische gegevensmaskering** op **Masker toevoegen** om de configuratiepagina **Maskerregel toevoegen** te openen.

    ![Navigatievenster](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Selecteer het **schema**, **de tabel** en **de kolom** om het aangewezen veld voor maskeren te definiëren.
7. Kies een **maskingveldindeling** in de lijst met categorieën voor het maskeren van gevoelige gegevens.

    ![Navigatievenster](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Klik **op Opslaan** op de pagina regels voor gegevensmaskeren om de set maskerregels in het dynamische gegevensmaskerbeleid bij te werken.
9. Typ de SQL-gebruikers of AAD-identiteiten die moeten worden uitgesloten van maskeren en toegang hebben tot de ontmaskerde gevoelige gegevens. Dit moet een puntkomma-gescheiden lijst van gebruikers. Gebruikers met beheerdersbevoegdheden hebben altijd toegang tot de oorspronkelijke ongemaskerde gegevens.

    ![Navigatievenster](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Als u dit zo wilt maken dat de toepassingslaag gevoelige gegevens kan weergeven voor gebruikers met een bevoorrechte toepassing, voegt u de SQL-gebruiker of AAD-identiteit toe die de toepassing gebruikt om de database op te vragen. Het wordt ten zeerste aanbevolen dat deze lijst een minimaal aantal bevoorrechte gebruikers bevat om de blootstelling van de gevoelige gegevens te minimaliseren.

10. Klik **op Opslaan** op de configuratiepagina voor gegevensmaskering om het nieuwe of bijgewerkte maskerbeleid op te slaan.

## <a name="next-steps"></a>Volgende stappen

* Zie [dynamische gegevensmaskering](sql-database-dynamic-data-masking-get-started.md)voor een overzicht van dynamische gegevensmaskering.
* U ook dynamische gegevensmaskering implementeren met [Azure SQL Database-cmdlets](https://docs.microsoft.com/powershell/module/az.sql/) of de [REST API.](https://docs.microsoft.com/rest/api/sql/)
