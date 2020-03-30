---
title: Wachtwoordbeleid maken en gebruiken in Azure AD Domain Services | Microsoft Documenten
description: Meer informatie over het gebruik van fijnmazig wachtwoordbeleid om accountwachtwoorden in een door Azure AD DS beheerd domein te beveiligen en te beheren.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: c4402c1ce2f051c8d1911e7c0332d4cac787ce1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613190"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Beleid voor het vergrendelen van wachtwoorden en wachtwoorden voor beheerde domeinen

Als u gebruikersbeveiliging wilt beheren in Azure Active Directory Domain Services (Azure AD DS), u fijnmazige wachtwoordbeleidsregels definiëren die de instellingen voor accountuitsluiting of de minimale wachtwoordlengte en complexiteit beheren. Er wordt een standaard wachtwoordbeleid voor fijnkorrelige waarden gemaakt en toegepast op alle gebruikers in een door Azure AD DS beheerd domein. Om gedetailleerde controle te bieden en te voldoen aan specifieke zakelijke of nalevingsbehoeften, kunnen aanvullende beleidsregels worden gemaakt en toegepast op specifieke groepen gebruikers.

In dit artikel ziet u hoe u een fijnkorrelig wachtwoordbeleid in Azure AD DS maakt en configureert met behulp van het Active Directory-beheercentrum.

> [!NOTE]
> Wachtwoordbeleid is alleen beschikbaar voor door Azure AD DS beheerde domeinen die zijn gemaakt met behulp van het implementatiemodel Resource Beheer. Voor oudere beheerde domeinen die met Classic zijn gemaakt, [migreert u van het virtuele netwerkmodel Classic naar Resource Manager][migrate-from-classic].

## <a name="before-you-begin"></a>Voordat u begint

Als u dit artikel wilt voltooien, hebt u de volgende bronnen en bevoegdheden nodig:

* Een actief Azure-abonnement.
  * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)aan .
* Een Azure Active Directory-tenant die is gekoppeld aan uw abonnement, gesynchroniseerd met een on-premises directory of een map met alleen wolken.
  * Maak indien nodig [een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account.][associate-azure-ad-tenant]
* Een beheerd azure Directory Domain Services-domein is ingeschakeld en geconfigureerd in uw Azure AD-tenant.
  * Vul indien nodig de zelfstudie in om [een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance]te maken en te configureren.
  * Het Azure AD DS-exemplaar moet zijn gemaakt met behulp van het implementatiemodel Resourcebeheer. Indien nodig [migreren van het virtuele netwerkmodel Classic naar Resource Manager][migrate-from-classic].
* Een Windows Server-beheer-VM die is verbonden met het beheerde Azure AD DS-domein.
  * Vul indien nodig de zelfstudie in om [een beheer-vm][tutorial-create-management-vm]te maken.
* Een gebruikersaccount dat lid is van de Azure *AD DC-beheerdersgroep* in uw Azure AD-tenant.

## <a name="default-password-policy-settings"></a>Standaardwachtwoordbeleidsinstellingen

Met fijnmazig wachtwoordbeleid (FGPP's) u specifieke beperkingen toepassen voor het beleid voor wachtwoord- en accountvergrendeling op verschillende gebruikers in een domein. Om bijvoorbeeld bevoegde accounts te beveiligen, u strengere instellingen voor accountuitsluiting toepassen dan gewone niet-bevoorrechte accounts. U meerdere FGPP's maken binnen een door Azure AD DS beheerd domein en de volgorde van prioriteit opgeven om ze toe te passen op gebruikers.

Beleidsregels worden gedistribueerd via groepskoppeling in een door Azure AD DS beheerd domein en alle wijzigingen die u aanbrengt, worden toegepast bij de volgende aanmelding van de gebruiker. Als u het beleid wijzigt, wordt een gebruikersaccount dat al is vergrendeld, niet ontgrendeld.

Wachtwoordbeleid gedraagt zich een beetje anders, afhankelijk van hoe het gebruikersaccount waarop ze zijn toegepast is gemaakt. Er zijn twee manieren waarop een gebruikersaccount kan worden gemaakt in Azure AD DS:

* Het gebruikersaccount kan worden gesynchroniseerd vanuit Azure AD. Dit omvat cloud-only gebruikersaccounts die rechtstreeks in Azure zijn gemaakt en hybride gebruikersaccounts die zijn gesynchroniseerd vanuit een on-premises AD DS-omgeving met Azure AD Connect.
    * De meeste gebruikersaccounts in Azure AD DS worden gemaakt via het synchronisatieproces van Azure AD.
* Het gebruikersaccount kan handmatig worden gemaakt in een door Azure AD DS beheerd domein en bestaat niet in Azure AD.

Alle gebruikers, ongeacht hoe ze zijn gemaakt, hebben het volgende beleid voor accountuitsluiting toegepast door het standaardwachtwoordbeleid in Azure AD DS:

* **Accountvergrendelingsduur:** 30
* **Aantal mislukte aanmeldingspogingen toegestaan:** 5
* **Aantal mislukte aanmeldingspogingen opnieuw instellen na:** 30 minuten
* **Maximale wachtwoordleeftijd (levensduur):** 90 dagen

Met deze standaardinstellingen worden gebruikersaccounts gedurende 30 minuten vergrendeld als vijf ongeldige wachtwoorden binnen 2 minuten worden gebruikt. Accounts worden na 30 minuten automatisch ontgrendeld.

Accountuitsluitingen vinden alleen plaats binnen het beheerde domein. Gebruikersaccounts zijn alleen vergrendeld in Azure AD DS en alleen vanwege mislukte aanmeldingspogingen tegen het beheerde domein. Gebruikersaccounts die zijn gesynchroniseerd vanuit Azure AD of on-premises, worden niet vergrendeld in hun bronmappen, alleen in Azure AD DS.

Als u een Azure AD-wachtwoordbeleid hebt dat een maximale wachtwoordleeftijd van meer dan 90 dagen opgeeft, wordt die leeftijd voor wachtwoorden toegepast op het standaardbeleid in Azure AD DS. U een aangepast wachtwoordbeleid configureren om een andere maximumwachtwoordleeftijd in Azure AD DS te definiëren. Let erop dat u een kortere maximumwachtwoordleeftijd hebt geconfigureerd in een Azure AD DS-wachtwoordbeleid dan in Azure AD of een on-premises AD DS-omgeving. In dat scenario kan het wachtwoord van een gebruiker verlopen in Azure AD DS voordat het wordt gevraagd om te worden gewijzigd in Azure AD of een on-premises AD DS-omgeving.

Voor gebruikersaccounts die handmatig zijn gemaakt in een door Azure AD DS beheerd domein, worden ook de volgende aanvullende wachtwoordinstellingen toegepast vanuit het standaardbeleid. Deze instellingen zijn niet van toepassing op gebruikersaccounts die zijn gesynchroniseerd vanuit Azure AD, omdat een gebruiker zijn wachtwoord niet rechtstreeks kan bijwerken in Azure AD DS.

* **Minimale wachtwoordlengte (tekens):** 7
* **Wachtwoorden moeten voldoen aan complexiteitsvereisten**

U de accountuitsluiting of wachtwoordinstellingen niet wijzigen in het standaardwachtwoordbeleid. In plaats daarvan kunnen leden van de groep *AAD DC-beheerders* aangepaste wachtwoordbeleidsregels maken en configureren om het standaardingebouwde beleid te overschrijven (voorrang te geven) zoals in de volgende sectie wordt weergegeven.

## <a name="create-a-custom-password-policy"></a>Een aangepast wachtwoordbeleid maken

Terwijl u toepassingen in Azure bouwt en uitvoert, u een aangepast wachtwoordbeleid configureren. U bijvoorbeeld een beleid maken om verschillende beleidsinstellingen voor accountuitsluiting in te stellen.

Aangepaste wachtwoordbeleidsregels worden toegepast op groepen in een door Azure AD DS beheerd domein. Deze configuratie overschrijft effectief het standaardbeleid.

Als u een aangepast wachtwoordbeleid wilt maken, gebruikt u de Active Directory-beheerhulpprogramma's van een vm die is verbonden met een domein. Met het Active Directory-beheercentrum u resources weergeven, bewerken en maken in een door Azure AD DS beheerd domein, inclusief Ok's.

> [!NOTE]
> Als u een aangepast wachtwoordbeleid wilt maken in een door Azure AD DS beheerd domein, moet u zijn aangemeld bij een gebruikersaccount dat lid is van de groep *AAD DC-beheerders.*

1. Selecteer In het startscherm de optie **Systeembeheer**. Er wordt een lijst met beschikbare beheerhulpprogramma's weergegeven die in de zelfstudie zijn geïnstalleerd om [een beheer-VM][tutorial-create-management-vm]te maken.
1. Als u Ok's wilt maken en beheren, selecteert u **Active Directory-beheercentrum** in de lijst met beheergereedschappen.
1. Kies in het linkerdeelvenster uw door Azure AD DS beheerde domein, zoals *aaddscontoso.com*.
1. Open de **systeemcontainer** en vervolgens de **stop met wachtwoordinstellingen**.

    Er wordt een ingebouwd wachtwoordbeleid voor het beheerde Azure AD DS-domein weergegeven. U dit ingebouwde beleid niet wijzigen. Maak in plaats daarvan een aangepast wachtwoordbeleid om het standaardbeleid te overschrijven.

    ![Een wachtwoordbeleid maken in het Active Directory-beheercentrum](./media/password-policy/create-password-policy-adac.png)

1. Selecteer in het deelvenster **Taken** aan de rechterkant de optie **Nieuwe > wachtwoordinstellingen**.
1. Voer in het dialoogvenster **Wachtwoordinstellingen maken** een naam voor het beleid in, zoals *MyCustomFGPP*.
1. Wanneer er meerdere wachtwoordbeleidsregels bestaan, wordt het beleid met de hoogste prioriteit of prioriteit toegepast op een gebruiker. Hoe lager het getal, hoe hoger de prioriteit. Het standaardwachtwoordbeleid heeft een prioriteit van *200*.

    Stel de prioriteit in voor uw aangepaste wachtwoordbeleid om de standaardwaarde te overschrijven, zoals *1*.

1. Bewerk naar wens andere wachtwoordbeleidsinstellingen. Onthoud de volgende belangrijke punten:

    * Instellingen zoals complexiteit van wachtwoorden, leeftijd of vervaldatum alleen voor gebruikers die handmatig zijn gemaakt in een door Azure AD DS beheerd domein.
    * Instellingen voor accountuitsluiting zijn van toepassing op alle gebruikers, maar worden alleen van kracht binnen het beheerde domein en niet in Azure AD zelf.

    ![Een aangepast fijnkorrelig wachtwoordbeleid maken](./media/how-to/custom-fgpp.png)

1. Schakel **de**controle Protect from accidental deletion uit. Als deze optie is geselecteerd, u de FGPP niet opslaan.
1. Selecteer **in** de sectie Direct van toepassing op de knop **Toevoegen.** Selecteer **in het** dialoogvenster Gebruikers of groepen selecteren de knop **Locaties.**

    ![Selecteer de gebruikers en groepen om het wachtwoordbeleid toe te passen op](./media/how-to/fgpp-applies-to.png)

1. Wachtwoordbeleid kan alleen worden toegepast op groepen. Vouw in het dialoogvenster **Locaties** de domeinnaam uit, zoals *aaddscontoso.com,* en selecteer vervolgens een organisatie-eenheid, zoals **AADDC-gebruikers**. Als u een aangepaste organisatie-eenheid hebt die een groep gebruikers bevat die u wilt toepassen, selecteert u die organisatie-eenheid.

    ![Selecteer de organisatie-eenheid waartoe de groep behoort](./media/how-to/fgpp-container.png)

1. Typ de naam van de groep waarop u het beleid wilt toepassen en selecteer **Namen controleren** om te valideren of de groep bestaat.

    ![Zoeken naar en selecteer de groep die FGPP wilt toepassen](./media/how-to/fgpp-apply-group.png)

1. Als u de naam van de groep die u nu hebt geselecteerd, nu weergeeft in de sectie **Direct van toepassing op,** selecteert u **OK** om uw aangepaste wachtwoordbeleid op te slaan.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over wachtwoordbeleid en het gebruik van het Active Directory Administration Center:

* [Meer informatie over fijnmazige wachtwoordbeleidsregels](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Fijnmazige wachtwoordbeleidsregels configureren met AD-beheercentrum](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
