---
title: 'Zelfstudie: Gebruikersinrichting voor Asana - Azure AD'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Asana.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: abeac030db419f7fb7d561df5dcd407684f20ca2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058904"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Zelfstudie: Asana configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in Asana en Azure Active Directory (Azure AD) om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar Asana.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

* Een Azure AD-tenant
* Een Asana-tenant met een [Enterprise-abonnement](https://www.asana.com/pricing) of beter ingeschakeld
* Een gebruikersaccount in Asana met beheerdersmachtigingen

> [!NOTE]
> Azure AD-integratie is gebaseerd op de [Asana-API](https://asana.com/developers/api-reference/users), die beschikbaar is voor Asana.

## <a name="assign-users-to-asana"></a>Gebruikers toewijzen aan Asana

Azure AD gebruikt een concept genaamd *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers in Azure AD toegang nodig hebben tot uw Asana-app. Vervolgens u deze gebruikers toewijzen aan uw Asana-app door de instructies hier te volgen:

[Een gebruiker toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Belangrijke tips voor het toewijzen van gebruikers aan Asana

We raden u aan één Azure AD-gebruiker aan Asana toe te wijzen om de inrichtingsconfiguratie te testen. Extra gebruikers kunnen later worden toegewezen.

## <a name="configure-user-provisioning-to-asana"></a>Gebruikersvoorziening configureren voor Asana

In deze sectie u uw Azure AD verbinden met de API voor het inrichten van Asana-gebruikersaccounts. U configureert ook de inrichtingsservice om toegewezen gebruikersaccounts in Asana te maken, bij te werken en uit te schakelen op basis van gebruikerstoewijzingen in Azure AD.

> [!TIP]
> Als u SAML-gebaseerde aanmelding voor Asana wilt inschakelen, volgt u de instructies in de [Azure-portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Automatische gebruikersaccountvoorziening configureren voor Asana in Azure AD

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie**Alle toepassingen van** Azure Active **Directory** > **Enterprise Apps.** > 

1. Als u Asana al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van Asana met behulp van het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **Asana** in de toepassingsgalerie. Selecteer **Asana** in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

1. Selecteer uw instantie van Asana en selecteer het tabblad **Inrichten.**

1. **Instelmodus instellen** op **Automatisch**.

    ![Asana Provisioning](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Volg onder de sectie **Beheerdersreferenties** deze instructies om het token te genereren en voer het in **Secret Token**in:

    a. Meld je aan bij [Asana](https://app.asana.com) met je beheerdersaccount.

    b. Selecteer de profielfoto op de bovenste balk en selecteer uw huidige instellingen voor organisatienamen.

    c. Ga naar het tabblad **Serviceaccounts.**

    d. Selecteer **Serviceaccount toevoegen**.

    e. Update **Naam** en **Over** en de profielfoto indien nodig. Kopieer het token in **Token**en selecteer het in **Wijzigingen opslaan**.

1. Selecteer in de Azure-portal **Testverbinding** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Asana-app. Als de verbinding mislukt, moet u ervoor zorgen dat uw Asana-account beheerdersmachtigingen heeft en probeert u de stap **Verbinding testen** opnieuw.

1. Voer het e-mailadres in van een persoon of groep die u wilt ontvangen voor het indienen van foutmeldingen in **de e-mail van de melding**. Schakel het selectievakje hieronder in.

1. Selecteer **Opslaan**.

1. Selecteer Azure **Active Directory-gebruikers synchroniseren met Asana**onder de sectie **Toewijzingen** .

1. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikerskenmerken die moeten worden gesynchroniseerd van Azure AD naar Asana. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Asana te matchen voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen door te voeren. Zie [Toewijzingen voor kenmerktoewijzingen voor gebruikersbepaling aanpassen](../app-provisioning/customize-application-attributes.md)voor meer informatie .

1. Als u de Azure AD-inrichtingsservice voor Asana wilt inschakelen, wijzigt u in **de** sectie Instellingen **de inrichtingsstatus** in **Aan**.

1. Selecteer **Opslaan**.

Nu wordt de eerste synchronisatie gestart voor alle gebruikers die aan Asana zijn toegewezen in de sectie **Gebruikers.** De eerste synchronisatie duurt langer om uit te voeren dan de daaropvolgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. Gebruik de sectie **Synchronisatiedetails** om de voortgang te controleren en koppelingen naar het inrichten van activiteitslogboeken te volgen. De controlelogboeken beschrijven alle acties die door de inrichtingsservice op uw Asana-app worden uitgevoerd.

Zie [Rapport over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eenmalige aanmelding configureren](asana-tutorial.md)
