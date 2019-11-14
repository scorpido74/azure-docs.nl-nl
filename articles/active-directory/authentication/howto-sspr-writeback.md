---
title: Wacht woord terugschrijven configureren voor Azure AD SSPR-Azure Active Directory
description: Azure AD en Azure AD Connect gebruiken voor het terugschrijven van wacht woorden naar een on-premises Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e12fe38ba69f6ac8f27130e01baff0c358aa409
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74021763"
---
# <a name="how-to-configure-password-writeback"></a>Instructies: wacht woord terugschrijven configureren

Bij de volgende stappen wordt ervan uitgegaan dat u Azure AD Connect in uw omgeving al hebt geconfigureerd met behulp van de [snelle](../hybrid/how-to-connect-install-express.md) of [aangepaste](../hybrid/how-to-connect-install-custom.md) instellingen.

1. Als u het terugschrijven van wachtwoorden wilt configureren en inschakelen, meld u zich aan bij de Azure AD Connect-server en start u de configuratiewizard **Azure AD Connect**.
2. Selecteer **Configureren** op de **welkomstpagina**.
3. Op de pagina **Extra taken** selecteert u **Synchronisatieopties aanpassen** en vervolgens **Volgende**.
4. Op de pagina **Verbinding maken met Azure AD** voert u de referenties van een globale beheerder in en selecteert u **Volgende**.
5. Op de pagina's **Verbinding maken met directory´s** en **domein/OE filteren** selecteert u **Volgende**.
6. Op de pagina **Optionele functies** schakelt u het selectievakje in naast **Wachtwoord terugschrijven** en selecteert u **Volgende**.
   ![Wacht woord terugschrijven inschakelen in Azure AD Connect][Writeback]
7. Op de pagina **Gereed om te configureren** selecteert u **Configureren** wacht u tot het proces is voltooid.
8. Als u ziet dat de configuratie is voltooid, selecteert u **Afsluiten**.

Zie de sectie [problemen met het terugschrijven van wacht woorden oplossen](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) in het artikel over probleem oplossing voor algemene probleemoplossings taken met betrekking tot wacht woord terugschrijven.

> [!WARNING]
> Het terugschrijven van wacht woorden stopt met het gebruik van Azure AD Connect versies 1.0.8641.0 en ouder wanneer de [Azure Access Control service (ACS) wordt ingetrokken op 7 November 2018](../develop/active-directory-acs-migration.md). Azure AD Connect versies 1.0.8641.0 en ouder staan niet meer op dat moment het terugschrijven van wacht woorden meer toe, omdat ze afhankelijk zijn van ACS voor die functionaliteit.
>
> Als u een onderbreking van de service wilt voor komen, moet u een upgrade uitvoeren van een eerdere versie van Azure AD Connect naar een nieuwere versie. Raadpleeg het artikel [Azure AD Connect: upgraden van een eerdere versie naar de nieuwste](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>Licentie vereisten voor wacht woord terugschrijven

**Self-service voor wacht woord opnieuw instellen/wijzigen/ontgrendelen met on-premises terugschrijven is een Premium-functie van Azure AD**. Zie de [Azure Active Directory-prijs site](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie over licenties.

Als u wacht woord terugschrijven wilt gebruiken, moet er een van de volgende licenties zijn toegewezen aan uw Tenant:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 of a3
* Enterprise Mobility + Security E5 of A5
* Microsoft 365 E3 of a3
* Microsoft 365 E5 of A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Zelfstandige Office 365-licentie plannen *bieden geen ondersteuning voor ' selfservice voor wacht woord opnieuw instellen/wijzigen/ontgrendelen met on-premises terugschrijven '* en vereisen dat u een van de voor gaande plannen hebt om deze functionaliteit te kunnen gebruiken.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Active Directory machtigingen en on-premises beleid voor wachtwoord complexiteit 

Voor het account dat is opgegeven in het Azure AD Connect-hulp programma, moeten de volgende items zijn ingesteld als u binnen het bereik van SSPR wilt:

* **Wachtwoord opnieuw instellen** 
* **Wachtwoord wijzigen** 
* **Schrijf machtigingen** voor `lockoutTime`
* **Schrijf machtigingen** voor `pwdLastSet`
* **Uitgebreide rechten** op:
   * Het hoofd object van *elk domein* in het forest
   * De organisatie-eenheden (Ou's) van de gebruiker die u in het bereik van SSPR wilt hebben

Als u niet zeker weet met welk account het beschreven account verwijst, opent u de gebruikers interface voor Azure Active Directory Connect configuratie en selecteert u de optie **huidige configuratie weer geven** . Het account waaraan u machtigingen moet toevoegen, wordt vermeld onder **gesynchroniseerde mappen**.

Als u deze machtigingen instelt, kan het MA-service account voor elk forest wacht woorden beheren namens de gebruikers accounts in die forest. 

> [!IMPORTANT]
> Als u deze machtigingen niet toewijst, kunnen gebruikers fouten tegen komen wanneer ze hun on-premises wacht woorden proberen te beheren vanuit de Cloud, zelfs als write-back goed lijkt te zijn geconfigureerd.
>

> [!NOTE]
> Het kan een uur of langer duren voordat deze machtigingen worden gerepliceerd naar alle objecten in uw Directory.
>

Voer de volgende stappen uit om de juiste machtigingen voor het terugschrijven van wacht woorden in te stellen:

1. Open Active Directory gebruikers en computers met een account met de juiste machtigingen voor domein beheer.
2. Zorg ervoor dat in het menu **weer gave** de optie **geavanceerde functies** is ingeschakeld.
3. Klik in het linkerdeel venster met de rechter muisknop op het object dat staat voor de hoofdmap van het domein en selecteer **eigenschappen** > **beveiliging** > **Geavanceerd**.
4. Selecteer **toevoegen**op het tabblad **machtigingen** .
5. Kies het account waarop de machtigingen worden toegepast (via de Azure AD Connect-installatie).
6. Selecteer in de vervolg keuzelijst **van toepassing op** de optie **onderliggende gebruikers objecten**.
7. Schakel onder **machtigingen**de selectie vakjes in voor de volgende opties:
    * **Wachtwoord wijzigen**
    * **Wachtwoord opnieuw instellen**
8. Schakel onder **Eigenschappen**de selectie vakjes in voor de volgende opties:
    * **LockoutTime schrijven**
    * **PwdLastSet schrijven**
9. Selecteer **Toep assen/OK** om de wijzigingen toe te passen en geopende dialoog vensters te sluiten.

Omdat de bron van de autoriteit on-premises is, is het beleid voor wachtwoord complexiteit van toepassing op dezelfde verbonden gegevens bron. Zorg ervoor dat u het bestaande groeps beleid voor ' minimale wachtwoord duur ' hebt gewijzigd. Het groeps beleid mag niet worden ingesteld op 1, wat betekent dat het wacht woord mini maal een dag oud moet zijn voordat het kan worden bijgewerkt. U moet ervoor zorgen dat deze is ingesteld op 0. Deze instellingen vindt u in `gpmc.msc` onder **computer configuratie > beleid > Windows-instellingen > beveiligings instellingen > account beleid**. Voer `gpupdate /force` uit om ervoor te zorgen dat de wijzigingen van kracht worden. 

## <a name="next-steps"></a>Volgende stappen

[Wat is wacht woord terugschrijven?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Wacht woord terugschrijven inschakelen in Azure AD Connect"
