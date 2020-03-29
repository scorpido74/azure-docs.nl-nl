---
title: Probleem met het configureren van federatieve enkele aanmelding voor een niet-galerietoepassing | Microsoft Documenten
description: Een aantal van de veelvoorkomende problemen aanpakken die u ondervinden bij het configureren van federatieve enkele aanmelding voor uw aangepaste SAML-toepassing die niet wordt vermeld in de Azure AD Application Gallery
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7894bfada4d363e89f526280e2925b4f4c6180a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76711891"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Probleem met het configureren van federatieve enkele aanmelding voor een niet-galerietoepassing

Als u een probleem ondervindt bij het configureren van een toepassing. Controleer of u alle stappen hebt gevolgd in het artikel [Eén aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory-toepassingsgalerie bevinden.](configure-federated-single-sign-on-non-gallery-applications.md)

## <a name="cant-add-another-instance-of-the-application"></a>Kan geen andere instantie van de toepassing toevoegen

Als u een tweede instantie van een toepassing wilt toevoegen, moet u het:

-   Een unieke id configureren voor de tweede instantie. U niet dezelfde id configureren die voor de eerste instantie wordt gebruikt.

-   Configureer een ander certificaat dan het certificaat dat in eerste instantie wordt gebruikt.

Als de toepassing geen van de voorgaande ondersteuning biedt, u geen tweede instantie configureren.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Waar stel ik de entiteits-id-indeling in

U de entiteits-id-indeling (User Identifier) die Azure AD naar de toepassing verzendt, niet selecteren in het antwoord na gebruikersverificatie.

Azure AD selecteert de indeling voor het kenmerk NameID (User Identifier) op basis van de geselecteerde waarde of de indeling die door de toepassing is aangevraagd in de SAML AuthRequest. Ga voor meer informatie naar het artikel [Single Sign-On SAML protocol](../develop/single-sign-on-saml-protocol.md#authnrequest) onder de sectie NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Waar haal ik de metagegevens of het certificaat van de toepassing vandaan van Azure AD

Voer de volgende stappen uit om de metagegevens of het certificaat van de toepassing te downloaden van Azure AD:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u hebt geconfigureerd voor eenmalige aanmelding.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Ga naar de sectie **SAML-ondertekeningscertificaat** en klik op **Kolomwaarde downloaden.** Afhankelijk van wat de toepassing vereist voor het configureren van één aanmelding, ziet u de optie om de XML met ametjes of het certificaat te downloaden.

Azure AD geeft geen URL om de metagegevens op te halen. De metagegevens kunnen alleen worden opgehaald als een XML-bestand.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Weet niet hoe saml-claims die naar een toepassing worden verzonden, aan te passen

Zie Claimtoewijzing in Azure Active Directory voor meer informatie voor meer informatie voor het aanpassen van de SAML-attribuutclaims die naar uw toepassing zijn [verzonden.](../develop/active-directory-claims-mapping.md)

## <a name="next-steps"></a>Volgende stappen
[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
