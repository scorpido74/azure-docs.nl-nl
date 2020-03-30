---
title: Foutbericht wordt weergegeven op de app-pagina nadat u zich hebt aangemeld | Microsoft Documenten
description: Problemen met Azure AD oplossen, meld u aan wanneer de app een foutbericht retourneert.
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185483"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Op een app-pagina wordt een foutbericht weergegeven nadat de gebruiker zich heeft aanmeldt

In dit scenario wordt de gebruiker in Azure Ad (Azure AD) in- en aanmeld. Maar de toepassing geeft een foutbericht weer en laat de gebruiker de aanmeldingsstroom niet voltooien. Het probleem is dat de app niet de reactie heeft geaccepteerd die Azure AD heeft uitgegeven.

Er zijn verschillende mogelijke redenen waarom de app de reactie van Azure AD niet heeft geaccepteerd. Als in het foutbericht niet duidelijk wordt aangegeven wat er ontbreekt in het antwoord, probeert u het volgende:

-   Als de app de Azure AD-galerie is, controleert u of u de stappen hebt gevolgd in [Het opsporen van SAML-gebaseerde eenmalige aanmelding voor toepassingen in Azure AD.](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)

-   Gebruik een tool zoals [Fiddler](https://www.telerik.com/fiddler) om de SAML-aanvraag, het antwoord en het token vast te leggen.

-   Stuur het SAML-antwoord naar de app-leverancier en vraag hen wat er ontbreekt.

## <a name="attributes-are-missing-from-the-saml-response"></a>Kenmerken ontbreken in de SAML-respons

Voer de volgende stappen uit om een kenmerk toe te voegen aan de Azure AD-configuratie die in de Azure AD-respons wordt verzonden:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer boven aan het navigatiedeelvenster aan de linkerkant **Alle services** om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het zoekvak van het filter en selecteer vervolgens Azure Active **Directory**.

4. Selecteer **Bedrijfstoepassingen** in het Azure AD-navigatiedeelvenster.

5. Selecteer **Alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de gewenste app niet ziet, gebruikt u het **besturingselement Filter** boven aan de **lijst Alle toepassingen**. Stel de optie **Weergeven** in op 'Alle toepassingen'.

6. Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

7. Nadat de app is geladen, selecteert u **Eén aanmelding** in het navigatiedeelvenster.

8. Selecteer in de sectie **Gebruikerskenmerken** de optie **Alle andere gebruikerskenmerken weergeven en bewerken.** Hier u wijzigen welke kenmerken u naar de app wilt verzenden in het SAML-token wanneer gebruikers zich aanmelden.

   Een kenmerk toevoegen:

   1. Selecteer **Kenmerk toevoegen**. Voer de **naam**in en selecteer de **waarde** in de vervolgkeuzelijst.

   1.  Selecteer **Opslaan**. U ziet het nieuwe kenmerk in de tabel.

9. Sla de configuratie op.

   De volgende keer dat de gebruiker zich aanmeldt bij de app, stuurt Azure AD het nieuwe kenmerk in de SAML-reactie.

## <a name="the-app-doesnt-identify-the-user"></a>De app identificeert de gebruiker niet

Aanmelden bij de app mislukt omdat de SAML-reactie een kenmerk zoals een rol mist. Of het mislukt omdat de app een andere indeling of waarde verwacht voor het kenmerk **NameID** (User Identifier).

Als u [geautomatiseerde gebruikersinrichting van Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) gebruikt om gebruikers in de app te maken, te onderhouden en te verwijderen, controleert u of de gebruiker is ingericht in de SaaS-app. Zie [Geen gebruikers worden ingericht voor een Azure AD Gallery-toepassing voor](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)meer informatie.

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Een kenmerk toevoegen aan de configuratie van de Azure AD-app

Voer de volgende stappen uit om de waarde van de gebruikers-id te wijzigen:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer **Alle services** boven aan het navigatiedeelvenster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het zoekvak van het filter en selecteer vervolgens Azure Active **Directory**.

4. Selecteer **Bedrijfstoepassingen** in het Azure AD-navigatiedeelvenster.

5. Selecteer **Alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de gewenste app niet ziet, gebruikt u het **besturingselement Filter** boven aan de **lijst Alle toepassingen**. Stel de optie **Weergeven** in op 'Alle toepassingen'.

6. Selecteer de app die u voor SSO wilt configureren.

7. Nadat de app is geladen, selecteert u **Eén aanmelding** in het navigatiedeelvenster.

8. Selecteer **onder Gebruikerskenmerken**de unieke id voor de gebruiker in de vervolgkeuzelijst **Gebruikers-id.**

## <a name="change-the-nameid-format"></a>De nameid-indeling wijzigen

Zie [NaamID bewerken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) om de nameid-indeling te wijzigen als de toepassing een andere indeling voor het kenmerk **NameID** (User Identifier) verwacht.

Azure AD selecteert de indeling voor het **kenmerk NameID** (User Identifier) op basis van de geselecteerde waarde of de indeling die door de app wordt aangevraagd in de SAML AuthRequest. Zie de sectie 'NameIDPolicy' van [het SAML-protocol voor één aanmelding](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)voor meer informatie.

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>De app verwacht een andere handtekeningmethode voor de SAML-respons

Voer de volgende stappen uit om te wijzigen welke delen van het SAML-token digitaal zijn ondertekend door Azure AD:

1. Open de [Azure-portal](https://portal.azure.com/) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer **Alle services** boven aan het navigatiedeelvenster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het zoekvak van het filter en selecteer vervolgens Azure Active **Directory**.

4. Selecteer **Bedrijfstoepassingen** in het Azure AD-navigatiedeelvenster.

5. Selecteer **Alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de gewenste toepassing niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen**. Stel de optie **Weergeven** in op 'Alle toepassingen'.

6. Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

7. Nadat de toepassing is geladen, selecteert u **Eén aanmelding** in het navigatiedeelvenster.

8. Selecteer **onder SAML-ondertekeningscertificaat**de optie **Geavanceerde certificeringsinstellingen voor certificaten weergeven**.

9. Selecteer de **ondertekeningsoptie** die de app van deze opties verwacht:

   * **Saml-antwoord ondertekenen**
   * **SAML-antwoord en -bewering ondertekenen**
   * **Saml-bewering ondertekenen**

   De volgende keer dat de gebruiker zich aanmeldt bij de app, ondertekent Azure AD het deel van het SAML-antwoord dat u hebt geselecteerd.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>De app verwacht dat het SHA-1-ondertekeningsalgoritme

Azure AD ondertekent standaard het SAML-token met behulp van het meest veilige algoritme. We raden u aan het ondertekeningsalgoritme niet te wijzigen in *SHA-1,* tenzij de app SHA-1 vereist.

Voer de volgende stappen uit om het ondertekeningsalgoritme te wijzigen:

1. Open de [Azure-portal](https://portal.azure.com/) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer **Alle services** boven aan het navigatiedeelvenster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het zoekvak van het filter en selecteer vervolgens Azure Active **Directory**.

4. Selecteer **Bedrijfstoepassingen** in het Azure AD-navigatiedeelvenster.

5. Selecteer **Alle toepassingen** om een lijst met uw toepassingen weer te geven.

   > [!NOTE]
   > Als u de gewenste toepassing niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen**. Stel de optie **Weergeven** in op 'Alle toepassingen'.

6. Selecteer de app die u wilt configureren voor eenmalige aanmelding.

7. Nadat de app is geladen, selecteert u **Eén aanmelding** in het navigatiedeelvenster aan de linkerkant van de app.

8. Selecteer **onder SAML-ondertekeningscertificaat**de optie **Geavanceerde certificeringsinstellingen voor certificaten weergeven**.

9. Selecteer **SHA-1** als **ondertekeningsalgoritme**.

   De volgende keer dat de gebruiker zich aanmeldt bij de app, ondertekent Azure AD het SAML-token met behulp van het SHA-1-algoritme.

## <a name="next-steps"></a>Volgende stappen
[SamL-gebaseerde enkele aanmelding voor toepassingen in Azure AD debuggen.](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
