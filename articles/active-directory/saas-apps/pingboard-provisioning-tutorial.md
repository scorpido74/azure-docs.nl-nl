---
title: 'Zelfstudie: Gebruikersinrichting voor Pingboard - Azure AD'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen naar Pingboard.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c64c40d93f9b525ac6adeca276797df65f32ef3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061272"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Zelfstudie: Pingboard configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet volgen om automatische inrichting en devoorziening van gebruikersaccounts van Azure Active Directory (Azure AD) naar Pingboard mogelijk te maken.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

* Een Azure AD-tenant
* Een Pingboard tenant [Pro-account](https://pingboard.com/pricing)
* Een gebruikersaccount in Pingboard met beheerdersmachtigingen

> [!NOTE]
> Azure AD-integratie is gebaseerd op de [Pingboard-API,](https://pingboard.docs.apiary.io/#)die beschikbaar is voor uw account.

## <a name="assign-users-to-pingboard"></a>Gebruikers toewijzen aan Pingboard

Azure AD gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde toepassingen. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers die zijn toegewezen aan een toepassing in Azure AD gesynchroniseerd. 

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers in Azure AD toegang nodig hebben tot uw Pingboard-app. Vervolgens u deze gebruikers toewijzen aan uw Pingboard-app door de instructies hier te volgen:

[Een gebruiker toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Belangrijke tips voor het toewijzen van gebruikers aan Pingboard

We raden u aan één Azure AD-gebruiker aan Pingboard toe te wijzen om de inrichtingsconfiguratie te testen. Extra gebruikers kunnen later worden toegewezen.

## <a name="configure-user-provisioning-to-pingboard"></a>Gebruikersinrichting configureren op Pingboard 

In deze sectie u uw Azure AD verbinden met de Pingboard-gebruikersaccountinrichtings-API. U configureert ook de inrichtingsservice om toegewezen gebruikersaccounts in Pingboard te maken, bij te werken en uit te schakelen die zijn gebaseerd op gebruikerstoewijzingen in Azure AD.

> [!TIP]
> Als u SAML-gebaseerde aanmelding voor Pingboard wilt inschakelen, volgt u de instructies in de [Azure-portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Automatische gebruikersaccountvoorziening configureren voor Pingboard in Azure AD

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie**Alle toepassingen van** Azure Active **Directory** > **Enterprise Apps.** > 

1. Als u Pingboard al hebt geconfigureerd voor één aanmelding, zoekt u naar uw instantie van Pingboard met behulp van het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **Pingboard** in de toepassingsgalerie. Selecteer **Pingboard** in de zoekresultaten en voeg het toe aan uw lijst met toepassingen.

1. Selecteer uw instantie van Pingboard en selecteer vervolgens het tabblad **Inrichten.**

1. **Instelmodus instellen** op **Automatisch**.

    ![Pingboard Provisioning](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. Gebruik onder de sectie **Beheerdersreferenties** de volgende stappen:

    a. Voer in Tenant `https://your_domain.pingboard.com/scim/v2` **URL**'your_domain' in en vervang deze door uw echte domein.

    b. Meld je aan bij [Pingboard](https://pingboard.com/) met je beheerdersaccount.

    c. Selecteer Azure Active Directory **voor invoegtoepassingen-** > **invoegtoepassingen** > **Azure Active Directory**.

    d. Ga naar het tabblad **Configureren** en selecteer **Gebruikersvoorziening inschakelen vanuit Azure.**

    e. Kopieer het token in **OAuth Bearer Token**en voer het in **Secret Token**in.

1. Selecteer in de Azure-portal **testverbinding** om Azure AD te testen en maak verbinding met uw Pingboard-app. Als de verbinding mislukt, test u of uw Pingboard-account beheerdersmachtigingen heeft en probeert u de stap **Verbinding testen** opnieuw.

1. Voer het e-mailadres in van een persoon of groep die u wilt ontvangen voor het indienen van foutmeldingen in **de e-mail van de melding**. Schakel het selectievakje hieronder in.

1. Selecteer **Opslaan**.

1. Selecteer Azure **Active Directory-gebruikers synchroniseren met Pingboard**onder de sectie **Toewijzingen** .

1. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikerskenmerken die moeten worden gesynchroniseerd van Azure AD naar Pingboard. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Pingboard te matchen voor updatebewerkingen. Selecteer **Opslaan** om wijzigingen door te voeren. Zie [Toewijzingen voor gebruikersinrichting aanpassen voor](../app-provisioning/customize-application-attributes.md)meer informatie .

1. Als u de Azure AD-inrichtingsservice voor Pingboard wilt inschakelen, wijzigt u in de sectie **Instellingen** **de inrichtingsstatus** in **Aan**.

1. Selecteer **Opslaan** om de eerste synchronisatie te starten van gebruikers die aan Pingboard zijn toegewezen.

De initiële synchronisatie duurt langer dan het volgen van synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. Gebruik de sectie **Synchronisatiedetails** om de voortgang te controleren en koppelingen naar het inrichten van activiteitslogboeken te volgen. De logboeken beschrijven alle acties die door de inrichtingsservice op uw Pingboard-app worden uitgevoerd.

Zie [Rapport over automatische gebruikersaccountinrichting](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountinrichting voor bedrijfsapps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eenmalige aanmelding configureren](pingboard-tutorial.md)
