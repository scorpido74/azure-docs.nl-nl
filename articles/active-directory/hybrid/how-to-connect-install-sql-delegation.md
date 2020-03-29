---
title: Azure AD Connect installeren met SQL-machtigingen voor gedelegeerde beheerders | Microsoft Documenten
description: In dit onderwerp wordt een update voor Azure AD Connect beschreven waarmee u worden geïnstalleerd met een account dat alleen SQL dbo-machtigingen heeft.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6269d00c9a6a8f827a4e31044d9d20efb0f8471b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60243521"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Azure AD Connect installeren met SQL-gedelegeerde beheerdersmachtigingen
Voorafgaand aan de nieuwste Azure AD Connect-build werd de administratieve delegatie bij het implementeren van configuraties die SQL vereist, niet ondersteund.  Gebruikers die Azure AD Connect wilden installeren, moesten serverbeheerders (SA)-machtigingen op de SQL-server hebben.

Met de nieuwste versie van Azure AD Connect kan het inrichten van de database nu buiten de band worden uitgevoerd door de SQL-beheerder en vervolgens worden geïnstalleerd door de Azure AD Connect-beheerder met database-eigenaarrechten.

## <a name="before-you-begin"></a>Voordat u begint
Als u deze functie wilt gebruiken, moet u zich realiseren dat er verschillende bewegende onderdelen zijn en dat elk van deze onderdelen een andere beheerder in uw organisatie kan betrekken.  In de volgende tabel worden de afzonderlijke rollen en hun respectieve taken bij het implementeren van Azure AD Connect met deze functie samengevat.

|Rol|Beschrijving|
|-----|-----|
|Ad-beheerder domein of forest|Hiermee maakt u het serviceaccount op domeinniveau dat door Azure AD Connect wordt gebruikt om de synchronisatieservice uit te voeren.  Zie [Accounts en machtigingen](reference-connect-accounts-permissions.md)voor meer informatie over serviceaccounts.
|SQL-beheerder|Hiermee maakt u de ADSync-database en verleent u aanmelding + dbo-toegang tot de Azure AD Connect-beheerder en het serviceaccount dat is gemaakt door de domein-/forestbeheerder.|
Azure AD Connect-beheerder|Installeert Azure AD Connect en geeft het serviceaccount op tijdens de aangepaste installatie.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Stappen voor het installeren van Azure AD Connect met SQL-gedelegeerde machtigingen
Als u de database uit de band wilt inrichten en Azure AD Connect wilt installeren met machtigingen voor database-eigenaren, gebruikt u de volgende stappen.

>[!NOTE]
>Hoewel het niet nodig is, wordt het **ten zeerste aanbevolen** dat de Latin1_General_CI_AS collatie wordt geselecteerd bij het maken van de database.


1. Laat de SQL Administrator de ADSync-database maken met een hoofdletter ongevoelige collatiereeks **(Latin1_General_CI_AS).**  De database moet **ADSync**heten.  Het herstelmodel, het compatibiliteitsniveau en het type containment worden bijgewerkt naar de juiste waarden wanneer Azure AD Connect is geïnstalleerd.  De collatiereeks moet echter correct worden ingesteld door de SQL-beheerder, anders blokkeert Azure AD Connect de installatie.  Als u de SA wilt herstellen, moet de database worden verwijderd en opnieuw worden gemaakt.
 
   ![Sortering](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Geef de Azure AD Connect-beheerder en het domeinserviceaccount de volgende machtigingen:
   - SQL-aanmelding 
   - **database eigenaar (dbo)** rechten.
 
   ![Machtigingen](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Azure AD Connect biedt geen ondersteuning voor aanmeldingen met een genest lidmaatschap.  Dit betekent dat uw Azure AD Connect-beheerdersaccount en domeinserviceaccount moeten worden gekoppeld aan een aanmelding waaraan dbo-rechten zijn verleend.  Het kan niet zomaar het lid zijn van een groep die is toegewezen aan een login met dbo-rechten.

3. Stuur een e-mail naar de Azure AD Connect-beheerder met vermelding van de SQL-server en de instantienaam die moeten worden gebruikt bij het installeren van Azure AD Connect.

## <a name="additional-information"></a>Aanvullende informatie
Zodra de database is ingericht, kan de Azure AD Connect-beheerder on-premises synchronisatie installeren en configureren wanneer deze voor hun gemak beschikbaar is.

Als de SQL-beheerder de ADSync-database heeft hersteld van een eerdere Azure AD Connect-back-up, moet u de nieuwe Azure AD Connect-server installeren met behulp van een bestaande database. Zie Azure AD Connect installeren met een bestaande ADSync-database voor meer informatie over het installeren van Azure AD Connect met een bestaande [ADSync-database.](how-to-connect-install-existing-database.md)

## <a name="next-steps"></a>Volgende stappen
- [Aan de slag met Azure AD Connect met expresinstellingen](how-to-connect-install-express.md)
- [Aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md)
- [Azure AD Connect installeren met behulp van een bestaande ADSync-database](how-to-connect-install-existing-database.md)  
