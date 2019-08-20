---
title: Wachtwoord beleid maken en gebruiken in Azure AD Domain Services | Microsoft Docs
description: Meer informatie over hoe en waarom u een nauw keurig wachtwoord beleid kunt gebruiken voor het beveiligen en beheren van wacht woorden in een Azure AD DS beheerd domein.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 08/08/2019
ms.author: iainfou
ms.openlocfilehash: 45fb2daaeaf9ee788207d43d805e070320372ca0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617128"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Wacht woord-en account vergrendelings beleid in beheerde domeinen

Als u de beveiliging van accounts in Azure Active Directory Domain Services (Azure AD DS) wilt beheren, kunt u verfijnde wachtwoord beleidsregels definiëren voor het beheren van instellingen, zoals minimale wachtwoord lengte, verval tijd van wacht woord of wachtwoord complexiteit. Een standaard wachtwoord beleid wordt toegepast op alle gebruikers in een door Azure AD DS beheerd domein. Om nauw keurige controle te bieden en te voldoen aan specifieke bedrijfs-of nalevings behoeften, kunnen extra beleids regels worden gemaakt en toegepast op specifieke groepen gebruikers.

In dit artikel wordt beschreven hoe u een nauw keurig wachtwoord beleid maakt en configureert met behulp van de Active Directory-beheercentrum.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
  * Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory Tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
  * Als dat nodig is, [maakt u een Azure Active Directory-Tenant][create-azure-ad-tenant] of [koppelt u een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een Azure Active Directory Domain Services beheerd domein ingeschakeld en geconfigureerd in uw Azure AD-Tenant.
  * Als dat nodig is, voltooit u de zelf studie voor het [maken en configureren van een Azure Active Directory Domain Services-exemplaar][create-azure-ad-ds-instance].
* Een Windows Server Management-VM die deel uitmaakt van het door Azure AD DS beheerde domein.
  * Als dat nodig is, voltooit u de zelf studie voor het [maken van een beheer-VM][tutorial-create-management-vm].
* Een gebruikers account dat lid is van de groep *Azure AD DC* -Administrators in uw Azure AD-Tenant.

## <a name="fine-grained-password-policies-fgpp-overview"></a>Overzicht van verfijnde wachtwoord beleid (FGPP)

Met een verfijnd wachtwoord beleid (FGPPs) kunt u specifieke beperkingen voor wacht woord-en account vergrendelings beleid Toep assen op verschillende gebruikers in een domein. Als u bijvoorbeeld accounts met verhoogde bevoegdheden wilt beveiligen, kunt u strengere wachtwoord instellingen Toep assen dan gewone accounts zonder privileges. U kunt meerdere FGPPs maken om wachtwoord beleidsregels op te geven binnen een door Azure AD DS beheerd domein.

De volgende wachtwoord instellingen kunnen worden geconfigureerd met behulp van FGPP:

* Minimale wachtwoord lengte
* Wachtwoord geschiedenis
* Wacht woorden moeten voldoen aan complexiteits vereisten
* Minimale wachtwoord duur
* Maximale wachtwoord duur
* Account vergrendelings beleid
  * Account vergrendelings duur
  * Aantal mislukte aanmeldings pogingen toegestaan
  * Aantal mislukte aanmeldings pogingen opnieuw instellen na

FGPP is alleen van invloed op gebruikers die zijn gemaakt in azure AD DS. Cloud gebruikers en domein gebruikers die zijn gesynchroniseerd met het door Azure AD DS beheerde domein vanuit Azure AD, hebben geen invloed op het wachtwoord beleid.

Beleids regels worden gedistribueerd via een groeps koppeling in het Azure AD DS Managed Domain en alle wijzigingen die u aanbrengt, worden toegepast bij de volgende aanmelding van de gebruiker. Als u het beleid wijzigt, wordt een gebruikers account dat al is vergrendeld, niet ontgrendeld.

## <a name="default-fine-grained-password-policy-settings"></a>Standaard instellingen voor het wachtwoord beleid

In een door Azure AD DS beheerd domein worden de volgende wachtwoord beleidsregels standaard geconfigureerd en toegepast op alle gebruikers:

* **Minimale wachtwoord lengte (tekens):** 7
* **Maximale wachtwoord duur (levens duur):** 90 dagen
* **Wacht woorden moeten voldoen aan complexiteits vereisten**

De volgende beleids regels voor account vergrendeling worden standaard geconfigureerd:

* **Account vergrendelings duur:** 30
* **Aantal mislukte aanmeldings pogingen toegestaan:** 5
* **Aantal mislukte aanmeldings pogingen opnieuw instellen na:** 30 minuten

Met deze standaard instellingen worden gebruikers accounts gedurende 30 minuten geblokkeerd als vijf ongeldige wacht woorden binnen twee minuten worden gebruikt. Accounts worden na 30 minuten automatisch ontgrendeld.

U kunt het standaard ingebouwde wachtwoord beleid niet wijzigen of verwijderen. In plaats daarvan kunnen leden van de groep *Aad DC* -Administrators een aangepaste FGPP maken en configureren voor onderdrukking (prioriteit hebben boven) de standaard ingebouwde FGPP, zoals wordt weer gegeven in de volgende sectie.

## <a name="create-a-custom-fine-grained-password-policy"></a>Een aangepast beleid voor een verfijnd wacht woord maken

Wanneer u bouwt en toepassingen in azure, wilt u mogelijk een aangepaste FGPP configureren. Een voor beeld van het maken van een aangepaste FGPP is het instellen van een ander account vergrendelings beleid of voor het configureren van een standaard instelling voor de levens duur van wacht woorden voor het beheerde domein.

U kunt een aangepaste FGPP maken en deze Toep assen op specifieke groepen in uw Azure AD DS beheerde domein. Deze configuratie vervangt de standaard FGPP. U kunt ook aangepaste beleids regels voor een verfijnd wacht woord maken en deze Toep assen op alle aangepaste organisatie-eenheden die u in het door Azure AD DS beheerde domein hebt gemaakt.

Als u een nauw keurig wachtwoord beleid wilt maken, gebruikt u de Active Directory-beheer Programma's van een VM die is gekoppeld aan een domein. Met de Active Directory-beheercentrum kunt u resources weer geven, bewerken en maken in een beheerd domein van Azure AD DS, met inbegrip van organisatie-eenheden.

> [!NOTE]
> Als u een nauw keurig wachtwoord beleid wilt maken in een door Azure AD DS beheerd domein, moet u zijn aangemeld bij een gebruikers account dat lid is van de groep *Aad DC* -Administrators.

1. Selecteer in het Start scherm de optie **systeem beheer**. Er wordt een lijst met beschik bare beheer hulpprogramma's weer gegeven die in de zelf studie zijn geïnstalleerd om [een beheer-VM te maken][tutorial-create-management-vm].
1. Als u organisatie-eenheden wilt maken en beheren, selecteert u **Active Directory-beheercentrum** in de lijst met beheer Programma's.
1. Kies in het linkerdeel venster uw door Azure AD DS beheerde domein, zoals *contoso.com*.
1. Selecteer in het deel venster **taken** aan de rechter kant **nieuwe > wachtwoord instellingen**.
1. Voer in het dialoog venster **wacht woord instellingen maken** een naam in voor het beleid, zoals *MyCustomFGPP*. Stel de prioriteit in op de juiste manier om de standaard FGPP ( *200*), zoals *1*, te overschrijven.

    Bewerk andere wachtwoord beleids instellingen naar wens, zoals **wachtwoord geschiedenis afdwingen** om te vereisen dat de gebruiker een wacht woord maakt dat afwijkt van de vorige *24* wacht woorden.

    ![Een aangepast beleid voor een verfijnd wacht woord maken](./media/how-to/custom-fgpp.png)

1. Schakel **beveiligen tegen onopzettelijk verwijderen uit**. Als deze optie is geselecteerd, kunt u de FGPP niet opslaan.
1. Selecteer in de sectie **direct van toepassing op** de knop **toevoegen** . Klik in het dialoog venster **gebruikers of groepen selecteren** op de knop **locaties** .

    ![Selecteer de gebruikers en groepen waarop u het wachtwoord beleid wilt Toep assen](./media/how-to/fgpp-applies-to.png)

1. Nauw keurig wachtwoord beleid kan alleen worden toegepast op groepen. Vouw in het dialoog venster **locaties** de domein naam uit, zoals *contoso.com*, en selecteer vervolgens een OE, zoals **AADDC-gebruikers**. Als u een aangepaste OE hebt die een groep gebruikers bevat die u wilt Toep assen, selecteert u die organisatie-eenheid.

    ![Selecteer de organisatie-eenheid waarvan de groep deel uitmaakt](./media/how-to/fgpp-container.png)

1. Typ de naam van de groep waarop u het beleid wilt Toep assen en selecteer vervolgens **Namen controleren** om te controleren of de groep bestaat.

    ![Zoek en selecteer de groep waarop u FGPP wilt Toep assen](./media/how-to/fgpp-apply-group.png)

1. Selecteer op **OK** de naam van de groep die u hebt geselecteerd in het vak **rechtstreeks van toepassing op** om uw aangepaste wachtwoord beleid op te slaan.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het verfijnen van het wachtwoord beleid en het gebruik van het Active Directory-beheer centrum:

* [Meer informatie over het beleid voor een verfijnd wacht woord](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Verfijnd wachtwoord beleid configureren met het AD-beheer centrum](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
