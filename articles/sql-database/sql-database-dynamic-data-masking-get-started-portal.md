---
title: 'Azure Portal: dynamische gegevens maskering'
description: Hoe u aan de slag gaat met SQL Database dynamische gegevens maskering in de Azure Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
ms.date: 03/04/2018
ms.openlocfilehash: 0053958c0681e98ee225232249b050583d4e13b2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826570"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Aan de slag met SQL Database dynamische gegevens maskering met de Azure Portal

In dit artikel wordt beschreven hoe u [dynamische gegevens maskering](sql-database-dynamic-data-masking-get-started.md) implementeert met de Azure Portal. U kunt ook dynamische gegevens maskering implementeren met behulp van [Azure SQL database-cmdlets](https://docs.microsoft.com/powershell/module/az.sql/) of de [rest API](https://docs.microsoft.com/rest/api/sql/).

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Dynamische gegevens maskering instellen voor uw data base met behulp van de Azure Portal

1. Start de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Ga naar de pagina instellingen van de data base die de gevoelige gegevens bevat die u wilt maskeren.
3. Klik op de tegel **dynamische gegevens maskering** voor het starten van de configuratie pagina voor **dynamische gegevens maskering** .

   * U kunt ook omlaag schuiven naar het gedeelte **bewerkingen** en op **dynamische gegevens maskering**klikken.

     ![Navigatievenster](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. In de configuratie pagina voor **dynamische gegevens maskering** worden mogelijk enkele database kolommen weer gegeven die door de aanbevelingen engine voor maskering zijn gemarkeerd. Als u de aanbevelingen wilt accepteren, klikt u op **masker toevoegen** voor een of meer kolommen en wordt er een masker gemaakt op basis van het standaard type voor deze kolom. U kunt de maskerings functie wijzigen door te klikken op de maskerings regel en de indeling van het masker veld te bewerken naar een andere indeling van uw keuze. Zorg ervoor dat u op **Opslaan** klikt om uw instellingen op te slaan.

    ![Navigatievenster](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Als u een masker voor een kolom in uw Data Base wilt toevoegen, klikt u boven aan de configuratie pagina voor **dynamische gegevens maskeren** op **masker toevoegen** om de configuratie pagina **maskerings regel toevoegen** te openen.

    ![Navigatievenster](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Selecteer het **schema**, de **tabel** en de **kolom** om het aangewezen veld voor maskering te definiëren.
7. Kies een **masker veld notatie** in de lijst met gevoelige gegevens maskerings categorieën.

    ![Navigatievenster](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Klik op **Opslaan** op de pagina gegevens maskerings regel om de set maskerings regels in het beleid voor dynamische gegevens maskering bij te werken.
9. Typ de SQL-gebruikers of AAD-identiteiten die moeten worden uitgesloten van maskering en toegang hebben tot de niet-gemaskeerde gevoelige gegevens. Dit moet een door punt komma's gescheiden lijst met gebruikers zijn. Gebruikers met beheerders bevoegdheden hebben altijd toegang tot de oorspronkelijke niet-gemaskeerde gegevens.

    ![Navigatievenster](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Om ervoor te zorgen dat de toepassingslaag gevoelige gegevens kan weer geven voor gebruikers met toepassings bevoegdheden, voegt u de SQL-gebruiker of AAD-identiteit toe die door de toepassing wordt gebruikt om een query uit te voeren op de data base. Het wordt ten zeerste aanbevolen dat deze lijst een mini maal aantal bevoegde gebruikers bevat om de bloot stelling van gevoelige gegevens te minimaliseren.

10. Klik op **Opslaan** op de configuratie pagina voor gegevens maskering om het nieuwe of bijgewerkte maskerings beleid op te slaan.

## <a name="next-steps"></a>Volgende stappen

* Zie [dynamische gegevens maskering](sql-database-dynamic-data-masking-get-started.md)voor een overzicht van dynamische gegevens maskering.
* U kunt ook dynamische gegevens maskering implementeren met behulp van [Azure SQL database-cmdlets](https://docs.microsoft.com/powershell/module/az.sql/) of de [rest API](https://docs.microsoft.com/rest/api/sql/).
