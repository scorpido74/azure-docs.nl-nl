---
title: Terugschrijven van Azure Active Directory-wachtwoord inschakelen
description: In deze zelfstudie leert u hoe u opnieuw schrijven voor azure AD-wachtwoorden inschakelt met Azure AD Connect om wijzigingen te synchroniseren naar een on-premises Active Directory Domain Services-omgeving.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3578cb1326ebd701c3f00618c19a501a1476372
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332128"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Zelfstudie: Zelflezen van Azure Active Directory selfservice-wachtwoord terugschrijven naar een on-premises omgeving inschakelen

Met Azure Active Directory (Azure AD) selfservice wachtwoordreset (SSPR) kunnen gebruikers hun wachtwoord bijwerken of hun account ontgrendelen via een webbrowser. In een hybride omgeving waarin Azure AD is verbonden met een on-premises AD DS-omgeving (Active Directory Domain Services), kan dit scenario ervoor zorgen dat wachtwoorden verschillen tussen de twee mappen.

Wachtwoordterugschrijven kan worden gebruikt om wachtwoordwijzigingen in Azure AD terug te synchroniseren naar uw on-premises AD DS-omgeving. Azure AD Connect biedt een beveiligd mechanisme om deze wachtwoordwijzigingen terug te sturen naar een bestaande on-premises directory van Azure AD.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De vereiste machtigingen configureren voor terugschrijven met wachtwoorden
> * De optie voor terugschrijven van wachtwoorden inschakelen in Azure AD Connect
> * Terugschrijven van wachtwoorden inschakelen in Azure AD SSPR

## <a name="prerequisites"></a>Vereisten

Als u deze zelfstudie wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een werkende Azure AD-tenant waarop minimaal een proeflicentie is ingeschakeld.
    * Maak er indien nodig [gratis een.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
    * Zie [Licentievereisten voor Azure AD SSPR voor](concept-sspr-licensing.md)meer informatie.
* Een account met *algemene beheerdersbevoegdheden.*
* Azure AD geconfigureerd voor het opnieuw instellen van selfservicewachtwoorden.
    * Voer indien nodig [de vorige zelfstudie in om Azure AD SSPR in te schakelen.](tutorial-enable-sspr.md)
* Een bestaande on-premises AD DS-omgeving die is geconfigureerd met een huidige versie van Azure AD Connect.
    * Configureer indien nodig Azure AD Connect met de instellingen [Express](../hybrid/how-to-connect-install-express.md) of [Aangepast.](../hybrid/how-to-connect-install-custom.md)
    * Als u Wachtwoord Writeback wilt gebruiken, moeten uw domeincontrollers Windows Server 2008 R2 of hoger zijn.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Accountmachtigingen configureren voor Azure AD Connect

Met Azure AD Connect u gebruikers, groepen en referenties synchroniseren tussen een on-premises AD DS-omgeving en Azure AD. U installeert Azure AD Connect doorgaans op een Windows Server 2012- of latere computer die is verbonden met het on-premises AD DS-domein.

Om correct te kunnen werken met SSPR-terugschrijfbewerking, moet het account dat is opgegeven in Azure AD Connect de juiste machtigingen en opties hebben ingesteld. Als u niet zeker weet welk account momenteel in gebruik is, opent u Azure AD Connect en selecteert u de optie **Huidige configuratie weergeven.** Het account waaraan u machtigingen moet toevoegen, wordt vermeld onder **Gesynchroniseerde mappen**. De volgende machtigingen en opties moeten worden ingesteld op het account:

* **Wachtwoord opnieuw instellen**
* **Machtigingen schrijven** op`lockoutTime`
* **Machtigingen schrijven** op`pwdLastSet`
* **Uitgebreide rechten** voor 'Wachtwoord zonder verlopen' op:
   * Het hoofdobject van *elk domein* in dat forest
   * De gebruikersorganisatie-eenheden (OE's) die u wilt hebben in het bereik van SSPR

Als u deze machtigingen niet toewijst, lijkt writeback correct te zijn geconfigureerd, maar gebruikers ondervinden fouten wanneer ze hun on-premises wachtwoorden vanuit de cloud beheren.

Voer de volgende stappen uit om de juiste machtigingen in te stellen voor het terugschrijven van wachtwoorden:

1. Open **Active Directory-gebruikers en computers** in uw on-premises AD DS-omgeving met een account met de juiste machtigingen voor *domeinbeheerders.*
1. Controleer in het menu **Weergave** of **geavanceerde functies** zijn ingeschakeld.
1. Selecteer in het linkerdeelvenster met de rechtermuisknop het object dat de hoofdmap van het domein vertegenwoordigt en selecteer **Eigenschappen** > **beveiliging** > **geavanceerd**.
1. Selecteer Op het tabblad **Machtigingen** de optie **Toevoegen**.
1. Selecteer **voor Principal**het account waarop machtigingen moeten worden toegepast (het account dat wordt gebruikt door Azure AD Connect).
1. Selecteer in de vervolgkeuzelijst **Van toepassing op** de optie **Afstammelingengebruiker .**
1. Selecteer *onder Machtigingen*de vakken voor de volgende opties:
    * **Wachtwoord opnieuw instellen**
1. Selecteer *onder Eigenschappen*de vakken voor de volgende opties. U moet door de lijst bladeren om deze opties te vinden, die mogelijk al standaard zijn ingesteld:
    * **LockoutTime schrijven**
    * **PwdLastSet schrijven**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Wanneer u klaar bent, selecteert u **Toepassen / OK** om de wijzigingen toe te passen en alle geopende dialoogvensters af te sluiten.

Wanneer u machtigingen bijwerkt, kan het tot een uur of langer duren voordat deze machtigingen worden gerepliceerd naar alle objecten in uw map.

Wachtwoordbeleid in de on-premises AD DS-omgeving kan voorkomen dat wachtwoordresets correct worden verwerkt. Om het schrijven van wachtwoorden het meest efficiënt te laten werken, moet het groepsbeleid voor *minimale wachtwoordleeftijd* worden ingesteld op 0. Deze instelling is te vinden onder **>-beleid voor computerconfiguratie > Windows-instellingen > beveiligingsinstellingen > accountbeleid** binnen `gpedit.msc`. 

Als u het groepsbeleid bijwerkt, wacht u tot `gpupdate /force` het bijgewerkte beleid is gerepliceerd of gebruikt u de opdracht.

> [!Note]
> Om wachtwoorden onmiddellijk te kunnen wijzigen, moet het terugschrijven van wachtwoorden worden ingesteld op 0. Als gebruikers zich echter houden aan het on-premises beleid en de *minimumleeftijd voor wachtwoorden* is ingesteld op een waarde groter dan nul, werkt wachtwoordafschrijving nog steeds nadat het on-premises beleid is geëvalueerd. 

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Terugschrijven van wachtwoorden inschakelen in Azure AD Connect

Een van de configuratieopties in Azure AD Connect is voor het terugschrijven van wachtwoorden. Wanneer deze optie is ingeschakeld, zorgen gebeurtenissen voor wachtwoordwijzigingen ervoor dat Azure AD Connect de bijgewerkte referenties weer synchroniseert met de on-premises AD DS-omgeving.

Als u het opnieuw terugschrijven van selfservicewachtwoorden wilt inschakelen, schakelt u eerst de terugschrijfoptie in Azure AD Connect in. Voer vanaf uw Azure AD Connect-server de volgende stappen uit:

1. Meld u aan bij uw Azure AD Connect-server en start de wizard **Azure AD Connect-configuratie.**
1. Selecteer **Configureren** op de **welkomstpagina**.
1. Op de pagina **Extra taken** selecteert u **Synchronisatieopties aanpassen** en vervolgens **Volgende**.
1. Voer op de pagina **Verbinding maken met Azure AD** een globale beheerdersreferentie in voor uw Azure-tenant en selecteer **Volgende**.
1. Op de pagina's **Verbinding maken met directory´s** en **domein/OE filteren** selecteert u **Volgende**.
1. Op de pagina **Optionele functies** schakelt u het selectievakje in naast **Wachtwoord terugschrijven** en selecteert u **Volgende**.

    ![Azure AD Connect configureren voor terugschrijven met wachtwoorden](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Op de pagina **Gereed om te configureren** selecteert u **Configureren** wacht u tot het proces is voltooid.
1. Als u ziet dat de configuratie is voltooid, selecteert u **Afsluiten**.

## <a name="enable-password-writeback-for-sspr"></a>Wachtwoordterugschrijven inschakelen voor SSPR

Als wachtwoordterugschrijftijd is ingeschakeld in Azure AD Connect, configureert u azure AD SSPR nu voor terugschrijven. Wanneer u SSPR inschakelt om wachtwoordterugschrijfinformatie te gebruiken, hebben gebruikers die hun wachtwoord wijzigen of opnieuw instellen dat bijgewerkte wachtwoord ook gesynchroniseerd met de on-premises AD DS-omgeving.

Voer de volgende stappen uit om terugte schrijven voor wachtwoorden in SSPR in te schakelen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een algemeen beheerdersaccount.
1. Zoeken naar en selecteer **Azure Active Directory**, selecteer Wachtwoord opnieuw **instellen**en kies **vervolgens On-premises integratie**.
1. De optie voor Wachtwoorden terugschrijven instellen op *Yes* **uw on-premises directory?**
1. Stel de optie in voor **Gebruikers toestaan om accounts te ontgrendelen zonder hun wachtwoord opnieuw in** *te*stellen?

    ![Azure AD selfservice wachtwoord opnieuw instellen inschakelen voor terugschrijven van wachtwoorden](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Wanneer u klaar bent, selecteert **u Opslaan**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de SSPR-terugschrijffunctionaliteit die u als onderdeel van deze zelfstudie hebt geconfigureerd, niet meer wilt gebruiken, voert u de volgende stappen uit:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoeken naar en selecteer **Azure Active Directory**, selecteer Wachtwoord opnieuw **instellen**en kies **vervolgens On-premises integratie**.
1. De optie voor Wachtwoorden terugschrijven instellen op *No* **uw on-premises directory?**
1. De optie instellen voor **Gebruikers toestaan om accounts te ontgrendelen zonder hun wachtwoord opnieuw in** *te*stellen?

Als u geen wachtwoordfunctionaliteit meer wilt gebruiken, voert u de volgende stappen uit vanaf uw Azure AD Connect-server:

1. Meld u aan bij uw Azure AD Connect-server en start de wizard **Azure AD Connect-configuratie.**
1. Selecteer **Configureren** op de **welkomstpagina**.
1. Op de pagina **Extra taken** selecteert u **Synchronisatieopties aanpassen** en vervolgens **Volgende**.
1. Voer op de pagina **Verbinding maken met Azure AD** een globale beheerdersreferentie in voor uw Azure-tenant en selecteer **Volgende**.
1. Op de pagina's **Verbinding maken met directory´s** en **domein/OE filteren** selecteert u **Volgende**.
1. Schakel op de pagina **Optionele functies** het selectievakje naast **Terugschrijftekst voor wachtwoorden** uit en selecteer **Volgende**.
1. Op de pagina **Gereed om te configureren** selecteert u **Configureren** wacht u tot het proces is voltooid.
1. Als u ziet dat de configuratie is voltooid, selecteert u **Afsluiten**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure AD SSPR-terugschrijven ingeschakeld naar een on-premises AD DS-omgeving. U hebt geleerd hoe u:

> [!div class="checklist"]
> * De vereiste machtigingen configureren voor terugschrijven met wachtwoorden
> * De optie voor terugschrijven van wachtwoorden inschakelen in Azure AD Connect
> * Terugschrijven van wachtwoorden inschakelen in Azure AD SSPR

> [!div class="nextstepaction"]
> [Risico's beoordelen bij het aanmelden](tutorial-risk-based-sspr-mfa.md)
