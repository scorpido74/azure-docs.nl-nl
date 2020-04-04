---
title: Aanvallen voorkomen met behulp van slimme uitsluiting - Azure Active Directory
description: Azure Active Directory smart lockout helpt uw organisatie te beschermen tegen brute-force-aanvallen die wachtwoorden proberen te raden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61ae942ed189dc4245a9a0b282daf4cad5323536
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652585"
---
# <a name="azure-active-directory-smart-lockout"></a>Slimme vergrendeling van Azure Active Directory

Smart lockout helpt bij het vergrendelen van slechte acteurs die proberen om wachtwoorden van uw gebruikers raden of brute-force methoden gebruiken om binnen te komen. Het kan aanmeldingen van geldige gebruikers herkennen en ze anders behandelen dan die van aanvallers en andere onbekende bronnen. Smart lockout vergrendelt de aanvallers, terwijl uw gebruikers toegang blijven houden tot hun accounts en productief zijn.

Standaard vergrendelt smart lock-out het account gedurende één minuut na tien mislukte pogingen. De rekening vergrendelt opnieuw na elke volgende mislukte aanmeldingspoging, gedurende een minuut in eerste instantie en langer in latere pogingen.

Smart lockout tracks de laatste drie slechte wachtwoord hashes om te voorkomen dat de verhoging van de lockout teller voor hetzelfde wachtwoord. Als iemand meerdere keren hetzelfde slechte wachtwoord invoert, zorgt dit gedrag er niet voor dat het account wordt vergrendeld.

 > [!NOTE]
 > Hash tracking-functionaliteit is niet beschikbaar voor klanten met pass-through authenticatie ingeschakeld als authenticatie gebeurt on-premises niet in de cloud.

Federated-implementaties met AD FS 2016 en AF FS 2019 kunnen vergelijkbare voordelen mogelijk maken met [AD FS Extranet Lockout en Extranet Smart Lockout.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)

Smart lockout is altijd ingeschakeld voor alle Azure AD-klanten met deze standaardinstellingen die de juiste mix van beveiliging en bruikbaarheid bieden. Voor aanpassing van de slimme vergrendelingsinstellingen, met waarden die specifiek zijn voor uw organisatie, zijn betaalde Azure AD-licenties voor uw gebruikers vereist.

Het gebruik van smart lock-out garandeert niet dat een echte gebruiker nooit zal worden vergrendeld. Wanneer smart lockout een gebruikersaccount vergrendelt, doen we ons best om de echte gebruiker niet te vergrendelen. De lock-out service probeert ervoor te zorgen dat slechte acteurs geen toegang kunnen krijgen tot een echt gebruikersaccount.  

* Elk Azure Active Directory-datacenter houdt de uitsluiting onafhankelijk bij. Een gebruiker heeft (threshold_limit * datacenter_count) aantal pogingen, als de gebruiker elk datacenter raakt.
* Smart Lockout maakt gebruik van vertrouwde locatie vs onbekende locatie om onderscheid te maken tussen een slechte acteur en de echte gebruiker. Onbekende en bekende locaties hebben beide aparte lockout tellers.

Slimme uitsluiting kan worden geïntegreerd met hybride implementaties, met behulp van wachtwoordhashsynchronisatie of pass-through-verificatie om te voorkomen dat on-premises Active Directory-accounts worden vergrendeld door aanvallers. Door slim vergrendelingsbeleid in Azure AD op de juiste manier in te stellen, kunnen aanvallen worden uitgefilterd voordat ze on-premises Active Directory bereiken.

Bij het gebruik van [pass-through authenticatie,](../hybrid/how-to-connect-pta.md)moet u ervoor zorgen dat:

* De drempelwaarde voor azure AD-uitsluiting is **kleiner** dan de drempelwaarde voor de uitsluiting van Active Directory-account. Stel de waarden zo in dat de drempelwaarde voor de uitsluiting van Active Directory-account ten minste twee of drie keer langer is dan de drempelwaarde voor azure-uitsluiting. 
* De azure AD-uitsluitingsduur moet langer zijn ingesteld dan de vergrendelingsteller voor het opnieuw instellen van het account na duur van het account. Houd er rekening mee dat de aazure-ad-duur in enkele seconden is ingesteld, terwijl de AD-duur in enkele minuten is ingesteld. 

Als u bijvoorbeeld wilt dat uw Azure AD-teller hoger is dan AD, bedraagt Azure AD 120 seconden (2 minuten) terwijl uw on-premises AD is ingesteld op 1 minuut (60 seconden).

> [!IMPORTANT]
> Momenteel kan een beheerder de cloudaccounts van de gebruikers niet ontgrendelen als deze zijn vergrendeld door de Smart Lockout-mogelijkheid. De beheerder moet wachten tot de vergrendelingsduur is verlopen. De gebruiker kan echter ontgrendelen door selfservice wachtwoordreset (SSPR) te gebruiken vanaf een vertrouwd apparaat of locatie.

## <a name="verify-on-premises-account-lockout-policy"></a>On-premises vergrendelingsbeleid voor accounts verifiëren

Gebruik de volgende instructies om uw on-premises Active Directory-accountvergrendelingsbeleid te verifiëren:

1. Open de tool Groepsbeleidsbeheer.
2. Bewerk het groepsbeleid dat het accountuitsluitingsbeleid van uw organisatie bevat, bijvoorbeeld het **standaarddomeinbeleid**.
3. Blader naar het beleid voor **computerconfiguratie** >  >  > **Windows-instellingen** > **Accountbeleid****Policies****Accountvergrendelingsbeleid** > **Account Lockout Policy**voor instellingen .
4. Controleer de **vergrendelingsdrempel van** uw account en **stel de uitsluitingsteller van uw account na** waarden opnieuw in.

![Het on-premises active directory-accountvergrendelingsbeleid wijzigen](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Azure AD-waarden voor slimme uitsluiting beheren

Op basis van uw organisatorische vereisten moeten slimme lock-outwaarden mogelijk worden aangepast. Voor aanpassing van de slimme vergrendelingsinstellingen, met waarden die specifiek zijn voor uw organisatie, zijn betaalde Azure AD-licenties voor uw gebruikers vereist.

Als u de slimme uitsluitingswaarden voor uw organisatie wilt controleren of wijzigen, gebruikt u de volgende stappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar *Azure Active Directory* en selecteer deze optie. Selecteer > **Beveiligingsverificatiemethoden** > **Wachtwoordbeveiliging**. **Security**
1. Stel de **vergrendelingsdrempel**in op basis van het aantal mislukte aanmeldingen op een account voordat de eerste uitsluiting is ingesteld. De standaardinstelling is 10.
1. Stel de **vergrendelingsduur in seconden in,** op de lengte in seconden van elke uitsluiting. De standaardinstelling is 60 seconden (één minuut).

> [!NOTE]
> Als de eerste aanmelding na een uitsluiting ook mislukt, wordt de account opnieuw vergrendeld. Als een account herhaaldelijk wordt vergrendeld, neemt de vergrendelingsduur toe.

![Het azure AD-beleid voor slimme uitsluiting aanpassen in de Azure-portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Bepalen of de functie Smart lockout werkt of niet

Wanneer de drempel voor slimme uitsluiting wordt geactiveerd, ontvangt u het volgende bericht terwijl het account is vergrendeld:

**Uw account is tijdelijk vergrendeld om onbevoegd gebruik te voorkomen. Probeer het later nog eens en als je nog steeds problemen hebt, neem dan contact op met je beheerder.**

## <a name="next-steps"></a>Volgende stappen

* [Ontdek hoe u slechte wachtwoorden in uw organisatie verbieden met Azure AD.](howto-password-ban-bad.md)
* [Configureer selfservice wachtwoordreset zodat gebruikers hun eigen accounts kunnen ontgrendelen.](quickstart-sspr.md)
