---
title: Wachtwoord terugschrijven inschakelen in Azure AD
description: In deze zelfstudie gaat u het terugschrijven van wachtwoorden inschakelen om door de cloud geïnitieerde wachtwoordwijzigingen terug te halen naar on-premises AD als onderdeel van Azure AD Connect.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1ec439696c1ebd9d5929a4c8b2d9925fc1a71cc
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846468"
---
# <a name="tutorial-enabling-password-writeback"></a>Zelfstudie: Wachtwoord terugschrijven inschakelen

In deze zelfstudie gaat u het terugschrijven van wachtwoorden voor uw hybride omgeving inschakelen. Wachtwoord terugschrijven wordt gebruikt om wachtwoordwijzigingen in Azure Active Directory (Azure AD) terug te synchroniseren met uw on-premises AD DS-omgeving (Active Directory Domain Services). Het terugschrijven van wachtwoorden is ingeschakeld als onderdeel van Azure AD Connect om te voorzien in een beveiligd mechanisme voor het terugzenden van wachtwoordwijzigingen naar een bestaande on-premises directory van Azure AD. Het artikel [Wat is het terugschrijven van wachtwoorden?](concept-sspr-writeback.md) bevat gedetailleerdere informatie over de interne mechanismen voor het terugschrijven van wachtwoorden.

> [!div class="checklist"]
> * De optie Wachtwoord terugschrijven inschakelen in Azure AD Connect
> * De optie Wachtwoord terugschrijven inschakelen in de selfservice voor opnieuw instellen van wachtwoorden (SSPR)

## <a name="prerequisites"></a>Vereisten

* Toegang tot een werkende Azure AD-tenant waaraan minimaal een proeflicentie is toegewezen.
* Een account met de bevoegdheden van een globale beheerder op de Azure AD-tenant.
* Een bestaande server die is geconfigureerd om er een actuele versie van [Azure AD Connect](../hybrid/how-to-connect-install-express.md) op te kunnen uitvoeren.
* Vorige zelfstudies over de selfservice voor het opnieuw instellen van wachtwoorden (SSPR) zijn voltooid.

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>De optie Wachtwoord terugschrijven inschakelen in Azure AD Connect

Als u het terugschrijven van wachtwoorden wilt inschakelen, moet deze functie eerst worden ingeschakeld voor de server waarop u Azure AD Connect hebt geïnstalleerd.

1. Als u het terugschrijven van wachtwoorden wilt configureren en inschakelen, meld u zich aan bij de Azure AD Connect-server en start u de configuratiewizard **Azure AD Connect**.
2. Selecteer **Configureren** op de **welkomstpagina**.
3. Op de pagina **Extra taken** selecteert u **Synchronisatieopties aanpassen** en vervolgens **Volgende**.
4. Op de pagina **Verbinding maken met Azure AD** voert u de referenties van een globale beheerder in en selecteert u **Volgende**.
5. Op de pagina's **Verbinding maken met directory´s** en **domein/OE filteren** selecteert u **Volgende**.
6. Op de pagina **Optionele functies** schakelt u het selectievakje in naast **Wachtwoord terugschrijven** en selecteert u **Volgende**.
7. Op de pagina **Gereed om te configureren** selecteert u **Configureren** wacht u tot het proces is voltooid.
8. Als u ziet dat de configuratie is voltooid, selecteert u **Afsluiten**.

## <a name="enable-password-writeback-option-in-sspr"></a>De optie Wachtwoord terugschrijven inschakelen in SSPR

Het inschakelen van de functie voor het terugschrijven van wachtwoorden in Azure AD Connect vormt slechts de helft van het verhaal. Door SSPR toe te staan wachtwoorden terug te schrijven wordt de cyclus voltooid omdat gebruikers die hun wachtwoord wijzigen of opnieuw instellen, wordt toegestaan om dat wachtwoord ook on-premises in te stellen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een globale-beheerdersaccount.
2. Blader naar **Azure Active Directory**, klik op **Wachtwoord opnieuw instellen** en kies vervolgens **On-premises integratie**.
3. Stel **Wachtwoorden terugschrijven naar uw on-premises directory?** in op **Ja**.
4. Stel de optie **Gebruikers toestaan accounts te ontgrendelen zonder hun wachtwoord opnieuw in te stellen?** in op **Ja**.
5. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het terugschrijven van wachtwoorden voor de selfservice voor het opnieuw instellen van wachtwoorden ingeschakeld. Laat het venster met Azure Portal geopend en ga verder met de volgende zelfstudie voor het configureren van extra instellingen die betrekking hebben op de selfservice voor het opnieuw instellen van wachtwoorden, voordat u de oplossing in een pilot gaat implementeren.

> [!div class="nextstepaction"]
> [Risico's beoordelen bij het aanmelden](tutorial-risk-based-sspr-mfa.md)
