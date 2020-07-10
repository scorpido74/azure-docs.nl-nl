---
title: Resources beveiligen met Azure MFA en ADFS-Azure Active Directory
description: Dit is de pagina Azure Multi-Factor Authentication waarop wordt beschreven hoe u aan de slag kunt met Azure MFA en AD FS in de cloud.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dac17c8592530c06dd761914e7f556b35c3674b
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86202994"
---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>Cloudresources beveiligen met Azure Multi-Factor Authentication en AD FS

Als uw organisatie is gefedereerd met behulp van Azure Active Directory, kunt u Azure Multi-Factor Authentication of Active Directory Federation Services (AD FS) gebruiken om resources te beveiligen die door Azure AD worden gebruikt. Gebruik de volgende procedures voor het beveiligen van Azure Active Directory-resources met ofwel Azure Multi-Factor Authentication of Active Directory Federation Services.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Azure AD-resources beveiligen met behulp van AD FS

Voor de beveiliging van uw cloudresource stelt u een claimregel in die ervoor zorgt dat Active Directory Federation Services de multipleauthn-claim verstuurt wanneer een gebruiker de verificatie in twee stappen voltooit. Deze claim wordt doorgegeven aan Azure AD. Volg deze procedure om de stappen te doorlopen:

1. Open AD FS-beheer.
2. Selecteer aan de linkerkant de **vertrouwens relatie van de relying**Party.
3. Klik met de rechtermuisknop op **Identiteitsplatform van Microsoft Office 365** en selecteer **Claimregels bewerken**.

   ![ADFS-console-vertrouwens relaties van Relying Party](./media/howto-mfa-adfs/trustedip1.png)

4. Klik bij Uitgifte transformatieregels op**Regel toevoegen**.

   ![Regels voor uitgifte transformatie bewerken](./media/howto-mfa-adfs/trustedip2.png)

5. Selecteer in de wizard Transformatieclaimregels toevoegen **Passthrough of Een binnenkomende claim filteren** in de vervolgkeuzelijst en klik op **Volgende**.

   ![Wizard Claimregel voor transformatie toevoegen](./media/howto-mfa-adfs/trustedip3.png)

6. Geef de regel een naam. 
7. Selecteer **Authenticatiemethodereferenties** als het type voor binnenkomende claims.
8. Selecteer **Alle claimwaarden doorgeven**.
    ![Wizard Claimregel voor transformatie toevoegen](./media/howto-mfa-adfs/configurewizard.png)
9. Klik op **Voltooien**. Sluit de AD FS-beheerconsole.

## <a name="trusted-ips-for-federated-users"></a>Goedgekeurde IP-adressen voor federatieve gebruikers

Met goedgekeurde IP-adressen zijn beheerders in staat om verificatie in twee stappen te omzeilen voor bepaalde IP-adressen of voor federatieve gebruikers met verzoeken die afkomstig zijn uit hun eigen intranet. In de volgende secties wordt beschreven hoe goedgekeurde IP-adressen van Azure Multi-Factor Authentication bij federatieve gebruikers moeten worden geconfigureerd en hoe verificatie in twee stappen kan worden omzeild als een verzoek afkomstig is uit het intranet van een federatieve gebruiker. Dit wordt bereikt door AD FS zo te configureren dat deze gebruikmaakt van een passthrough of een binnenkomende claimsjabloon filtert met het claimtype Binnen bedrijfsnetwerk.

In dit voorbeeld wordt Office 365 gebruikt voor onze Relying Party-vertrouwensrelaties.

### <a name="configure-the-ad-fs-claims-rules"></a>De claimregels van AD FS configureren

Het eerste wat we moeten doen is de AD FS-claims configureren. U maakt twee claimregels: één voor het claimtype Binnen bedrijfsnetwerk en een extra regel om de gebruikers aangemeld te houden.

1. Open AD FS-beheer.
2. Selecteer aan de linkerkant de **vertrouwens relatie van de relying**Party.
3. Klik met de rechter muisknop op **Microsoft Office 365-identiteits platform** en selecteer **claim regels bewerken...** 
    ![ ADFS-console-claim regels bewerken](./media/howto-mfa-adfs/trustedip1.png)
4. Klik bij uitgifte transformatie regels op **regel toevoegen.** 
    ![ Een claim regel toevoegen](./media/howto-mfa-adfs/trustedip2.png)
5. Selecteer in de wizard Transformatieclaimregels toevoegen **Passthrough of Een binnenkomende claim filteren** in de vervolgkeuzelijst en klik op **Volgende**.
   ![Wizard Claimregel voor transformatie toevoegen](./media/howto-mfa-adfs/trustedip3.png)
6. Geef de claimregel een naam in het vak bij Naam claimregel. Bijvoorbeeld: BinnenBedrijfsNet.
7. In de vervolgkeuzelijst naast Binnenkomend claimtype, selecteert u **Binnen bedrijfsnetwerk**.
   ![Toevoegen binnen bedrijfs netwerk claim](./media/howto-mfa-adfs/trustedip4.png)
8. Klik op **Voltooien**.
9. Klik bij Uitgifte transformatieregels op**Regel toevoegen**.
10. Selecteer in de wizard Transformatieclaimregels toevoegen **Claim verzenden met een aangepaste regel** in de vervolgkeuzelijst en klik op **Volgende**.
11. In het vak onder Naam claimregel typt u *Gebruikers aangemeld houden*.
12. In het vak Aangepaste regel typt u:

```ad-fs-claim-rule
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Create custom claim to keep users signed in](./media/howto-mfa-adfs/trustedip5.png)
```

13. Klik op **Voltooien**.
14. Klik op **Toepassen**.
15. Klik op **OK**.
16. Sluit AD FS-beheer.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Goedgekeurde IP-adressen van Azure Multi-Factor Authentication configureren bij federatieve gebruikers

Nu de claims zijn gemaakt, kunnen we goedgekeurde IP-adressen gaan configureren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**met de  >  **naam locaties**.
3. Selecteer op de Blade **voorwaardelijke toegang-benoemde locaties** de optie **MFA-vertrouwde IP-adressen configureren**

   ![Voorwaardelijke toegang voor Azure AD benoemde locaties geconfigureerde MFA-IP-adressen configureren](./media/howto-mfa-adfs/trustedip6.png)

4. Selecteer op de pagina Service-instellingen onder **Vertrouwde IP's** de optie **Meervoudige verificatie overslaan voor aanvragen van federatieve gebruikers op mijn intranet**.  
5. Klik op **Opslaan**.

Dat is alles. Vanaf dit moment hoeven Office 365-gebruikers alleen MFA te gebruiken wanneer een claim afkomstig is van buiten het bedrijfsintranet.
