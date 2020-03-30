---
title: De privacygegevens van uw organisatie toevoegen - Azure Active Directory | Microsoft Documenten
description: Instructies voor het toevoegen van de privacygegevens van uw organisatie aan het gebied Azure Active Directory Properties.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95f1877ae630919ba7da5a851b5f8291ade2d165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897577"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>De privacygegevens van uw organisatie toevoegen met Azure Active Directory
In dit artikel wordt uitgelegd hoe een tenantbeheerder privacygerelateerde informatie kan toevoegen aan de Azure AD-tenant (Azure Directory) van een organisatie via de Azure-portal.

We raden u ten zeerste aan om zowel uw wereldwijde privacycontactpersoon als de privacyverklaring van uw organisatie toe te voegen, zodat uw interne medewerkers en externe gasten uw beleid kunnen bekijken. Omdat privacyverklaringen uniek zijn gemaakt en op maat zijn gemaakt voor elk bedrijf, raden we u ten zeerste aan contact op te nemen met een advocaat voor hulp.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Uw privacygegevens toevoegen aan Azure AD
U voegt de privacygegevens van uw organisatie toe in het gebied **Eigenschappen** van Azure AD.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Toegang krijgen tot het gebied Eigenschappen en uw privacygegevens toevoegen

1.  Meld u aan bij de Azure-portal als tenantbeheerder.

2.  Selecteer op de linker navigatiebalk **Azure Active Directory**en selecteer **Eigenschappen**.

    Het gebied **Eigenschappen** wordt weergegeven.

    ![Azure AD Properties-gebied met het gebied privacyinfo](media/active-directory-properties-area/properties-area.png)

3.  Voeg uw privacygegevens toe voor uw medewerkers:

    - **Technisch contact.** Typ het e-mailadres waar de persoon contact mee moet opnemen voor technische ondersteuning binnen uw organisatie.
    
    - **Wereldwijd contact op het contact met de privacy.** Typ het e-mailadres waar de persoon contact mee kan opnemen voor vragen over de privacy van persoonsgegevens. Deze persoon is ook degene die Microsoft contacten als er een datalek. Als hier geen persoon wordt vermeld, neemt Microsoft contact op met uw globale beheerders.

    - **URL van de privacyverklaring.** Typ de koppeling naar het document van uw organisatie waarin wordt beschreven hoe uw organisatie omgaat met de gegevensprivacy van zowel interne als externe gasten.

        >[!Important]
        >Als u uw eigen privacyverklaring of uw privacycontactpersoon niet opneemt, zien uw externe gasten tekst in het vak **Machtigingen voor beoordeling** met de tekst, ** <uw oorspronkelijke _naam_> geen links naar hun voorwaarden heeft verstrekt om te bekijken.** Een gastgebruiker ziet dit bericht bijvoorbeeld wanneer hij of zij een uitnodiging ontvangt om toegang te krijgen tot een organisatie via B2B-samenwerking.

        ![Het vak Machtigingen voor B2B-samenwerkingcontroleren met bericht](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen
- [Inwisseling van Azure Active Directory B2B-uitnodigingvoor samenwerkingsuitnodiging](https://aka.ms/b2bredemption)
- [Profielgegevens voor een gebruiker toevoegen of wijzigen in Azure Active Directory](active-directory-users-profile-azure-portal.md)
