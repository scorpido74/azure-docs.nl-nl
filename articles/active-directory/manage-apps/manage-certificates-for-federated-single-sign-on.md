---
title: Federatiecertificaten beheren in Azure AD | Microsoft Documenten
description: Meer informatie over het aanpassen van de vervaldatum voor uw federatiecertificaten en hoe u certificaten vernieuwen die binnenkort verlopen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mimart
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de60dc5095ce4ab4d0219a388c445b08f544e1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159026"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Certificaten beheren voor federatieve enkele aanmelding in Azure Active Directory

In dit artikel behandelen we veelgestelde vragen en informatie met betrekking tot certificaten die Azure Active Directory (Azure AD) maakt om een malige aanmelding (SSO) vast te stellen aan uw software as a service (SaaS)-toepassingen. Voeg toepassingen toe vanuit de Azure AD-app-galerie of met behulp van een niet-galerietoepassingssjabloon. Configureer de toepassing met de optie Federatieve SSO.

Dit artikel is alleen relevant voor apps die zijn geconfigureerd om Azure AD SSO te gebruiken via de SAML-federatie [(Security Assertion Markup Language).](https://wikipedia.org/wiki/Security_Assertion_Markup_Language)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatisch gegenereerd certificaat voor galerie- en niet-galerietoepassingen

Wanneer u een nieuwe toepassing toevoegt uit de galerie en een op SAML gebaseerde aanmelding configureert (door**SAML** **eenmalig aanmelden** > te selecteren op de pagina met toepassingsoverzicht), genereert Azure AD een certificaat voor de toepassing die drie jaar geldig is. Als u het actieve certificaat wilt downloaden als een bestand voor beveiligingscertificaat **(.cer),** gaat u terug naar die pagina **(SAML-gebaseerde aanmelding)** en selecteert u een downloadkoppeling in de kop **SAML Signing Certificate.** U kiezen tussen het raw (binary) certificaat of het Base64 (base 64-gecodeerde tekst) certificaat. Voor galerietoepassingen kan in deze sectie ook een koppeling worden weergegeven om het certificaat te downloaden als xml-federatiemetagegevens (een **.xml-bestand),** afhankelijk van de vereiste van de toepassing.

![SAML-opties voor het downloaden van actieve ondertekeningscertificaten](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

U ook een actief of inactief certificaat downloaden door het pictogram **Bewerken** van de **SAML-ondertekeningscertificaatkop** (een potlood) te selecteren, waarin de pagina **SAML-ondertekeningscertificaat** wordt weergegeven. Selecteer de ellips (**...**) naast het certificaat dat u wilt downloaden en kies vervolgens welke certificaatindeling u wilt gebruiken. U hebt de extra optie om het certificaat te downloaden in privacy-enhanced mail (PEM) formaat. Deze indeling is identiek aan Base64, maar met een **extensie voor .pem-bestandsnaam,** die niet wordt herkend in Windows als certificaatindeling.

![SAML-downloadopties voor certificaatcertificaten (actief en inactief)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>De vervaldatum van uw federatiecertificaat aanpassen en doorrollen naar een nieuw certificaat

Azure configureert standaard een certificaat dat na drie jaar verloopt wanneer het automatisch wordt gemaakt tijdens saml-eenmalige aanmeldingsconfiguratie. Omdat u de datum van een certificaat niet wijzigen nadat u het hebt opgeslagen, moet u:

1. Maak een nieuw certificaat met de gewenste datum.
1. Sla het nieuwe certificaat op.
1. Download het nieuwe certificaat in de juiste indeling.
1. Upload het nieuwe certificaat naar de toepassing.
1. Maak het nieuwe certificaat actief in de Azure Active Directory-portal.

Met de volgende twee secties u deze stappen uitvoeren.

### <a name="create-a-new-certificate"></a>Een nieuw certificaat maken

Maak en sla eerst een nieuw certificaat op met een andere vervaldatum:

1. Meld u aan bij de [Azure Active Directory-portal](https://aad.portal.azure.com/). De pagina **Azure Active Directory-beheercentrum** wordt weergegeven.
1. Selecteer in het linkerdeelvenster **Enterprise-toepassingen**. Er wordt een lijst weergegeven met de bedrijfstoepassingen in uw account.
1. Selecteer de betreffende toepassing. Er verschijnt een overzichtspagina voor de toepassing.
1. Selecteer In het linkerdeelvenster van de pagina toepassingsoverzicht de optie **Eén aanmelding**.
1. Als de pagina **Selecteer een enkele aanmeldingsmethode** wordt weergegeven, selecteert u **SAML**.
1. Zoek in de pagina **Eén aanmelding instellen met SAML - Voorbeeld** de kop **SAML-ondertekeningscertificaat** en selecteer het pictogram **Bewerken** (een potlood). De pagina **SAML-ondertekeningscertificaat** wordt weergegeven, waarin de status **(Actief** of **Inactief),** de vervaldatum en de duimafdruk (een hash-tekenreeks) van elk certificaat worden weergegeven.
1. Selecteer **Nieuw certificaat**. Een nieuwe rij wordt weergegeven onder de certificaatlijst, waarbij de vervaldatum standaard is tot precies drie jaar na de huidige datum. (Uw wijzigingen zijn nog niet opgeslagen, dus u de vervaldatum nog steeds wijzigen.)
1. Plaats in de rij met het nieuwe certificaat de plaats boven de kolom vervaldatum en selecteer het pictogram **Datum selecteren** (een agenda). Er wordt een agendabesturingselement weergegeven waarin de dagen van een maand van de huidige vervaldatum van de nieuwe rij worden weergegeven.
1. Gebruik het agendabesturingselement om een nieuwe datum in te stellen. U elke datum instellen tussen de huidige datum en drie jaar na de huidige datum.
1. Selecteer **Opslaan**. Het nieuwe certificaat wordt nu weergegeven met de status **Inactief,** de vervaldatum die u hebt gekozen en een duimafdruk.
1. Selecteer de **X** om terug te keren naar de pagina **Eén aanmelding instellen met SAML - Voorbeeld.**

### <a name="upload-and-activate-a-certificate"></a>Een certificaat uploaden en activeren

Download vervolgens het nieuwe certificaat in de juiste indeling, upload het naar de toepassing en maak het actief in Azure Active Directory:

1. Bekijk de aanvullende SAML-aanmeldingsinstructies van de toepassing door:

   - De **koppeling configuratiegids** selecteren om in een afzonderlijk browservenster of tabblad weer te geven, of
   - Ga naar de **instelkop** en selecteer **Stapsgewijze instructies weergeven** om in een zijbalk weer te geven.

1. Noteer in de instructies de coderingsindeling die nodig is voor het uploaden van het certificaat.
1. Volg de instructies in de sectie [Automatisch gegenereerd certificaat voor galerij- en niet-galerietoepassingen](#auto-generated-certificate-for-gallery-and-non-gallery-applications) eerder. Met deze stap wordt het certificaat gedownload in de coderingsindeling die vereist is voor het uploaden door de toepassing.
1. Wanneer u wilt overzetten naar het nieuwe certificaat, gaat u terug naar de pagina **SAML-ondertekeningscertificaat** en selecteert u in de nieuw opgeslagen certificaatrij de ellips (**... )** en selecteert **u Certificaat actief maken**. De status van het nieuwe certificaat verandert in **Actief**en het eerder actieve certificaat verandert in de status **Inactief**.
1. Ga verder met het volgen van de SAML-aanmeldingsinstructies van de toepassing die u eerder hebt weergegeven, zodat u het SAML-ondertekeningscertificaat in de juiste coderingsindeling uploaden.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>E-mailmeldingsadressen toevoegen voor het verlopen van certificaten

Azure AD stuurt 60, 30 en 7 dagen voordat het SAML-certificaat verloopt een e-mailmelding. U meer dan één e-mailadres toevoegen om meldingen te ontvangen. Als u het e-mailadres(en) wilt opgeven, wilt u dat de meldingen worden verzonden naar:

1. Ga op de pagina **SAML Signing Certificate** naar de kop **e-mailadressen van de melding.** Standaard gebruikt deze kop alleen het e-mailadres van de beheerder die de toepassing heeft toegevoegd.
1. Typ onder het uiteindelijke e-mailadres het e-mailadres dat de vervaldatum van het certificaat moet ontvangen en druk op Enter.
1. Herhaal de vorige stap voor elk e-mailadres dat u wilt toevoegen.
1. Selecteer voor elk e-mailadres dat u wilt verwijderen het pictogram **Verwijderen** (een vuilnisbak) naast het e-mailadres.
1. Selecteer **Opslaan**.

U ontvangt de e-mail met de melding van aadnotification@microsoft.com. Voeg deze e-mail toe aan je contactpersonen om te voorkomen dat de e-mail naar je spamlocatie gaat.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Een certificaat verlengen dat binnenkort verloopt

Als een certificaat bijna verloopt, u het verlengen met een procedure die resulteert in geen significante downtime voor uw gebruikers. Een verlopend certificaat verlengen:

1. Volg de instructies in de sectie [Een nieuw certificaat](#create-a-new-certificate) maken eerder met behulp van een datum die overlapt met het bestaande certificaat. Die datum beperkt de hoeveelheid downtime die wordt veroorzaakt door het verlopen van het certificaat.
1. Als de toepassing automatisch een certificaat kan doorrollen, stelt u het nieuwe certificaat in op actief door de volgende stappen te volgen:
   1. Ga terug naar de **pagina SAML Signing Certificate.**
   1. Selecteer in de rij nieuw opgeslagen certificaten de ellips (**... )** en selecteer Vervolgens Certificaat **actief maken**.
   1. Sla de volgende twee stappen over.

1. Als de app slechts één certificaat tegelijk kan verwerken, kiest u een tijdsinterval voor downtime om de volgende stap uit te voeren. (Als de toepassing anders niet automatisch het nieuwe certificaat ophaalt, maar meer dan één ondertekeningscertificaat kan verwerken, u de volgende stap op elk gewenst moment uitvoeren.)
1. Volg voordat het oude certificaat verloopt de instructies in de [sectie Uploaden en activeer eerder een certificaatsectie.](#upload-and-activate-a-certificate)
1. Meld u aan bij de aanvraag om er zeker van te zijn dat het certificaat correct werkt.

## <a name="related-articles"></a>Verwante artikelen:

- [Tutorials for integrating SaaS applications with Azure Active Directory](../saas-apps/tutorial-list.md) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)
- [Toepassingsbeheer met Azure Active Directory](what-is-application-management.md)
- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](what-is-single-sign-on.md)
- [Foutopsporing op SAML-gebaseerde enkele aanmelding voor toepassingen in Azure Active Directory](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
