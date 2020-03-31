---
title: Back-ups maken en accounts herstellen met de Microsoft Authenticator-app - Azure AD
description: Meer informatie over het maken van back-ups en het herstellen van uw back-upaccountgegevens met behulp van de Microsoft Authenticator-app.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 2ac761355fc528fe3e4b4b5f05ee0a6e317ff99a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78297965"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Een back-up maken en accountreferenties herstellen met de Microsoft Authenticator-app

**Van toepassing op:**

- iOS-apparaten, met versie 5.7.0 en hoger

- Android-apparaten, met versie 6.6.0 en hoger

De Microsoft Authenticator-app maakt een back-up van uw accountreferenties en gerelateerde app-instellingen, zoals de volgorde van uw accounts, naar de cloud. Na een back-up u de app ook gebruiken om uw gegevens op een nieuw apparaat te herstellen, waardoor u mogelijk niet wordt vergrendeld of accounts opnieuw moet maken.

Elke back-upopslaglocatie vereist dat u één persoonlijk Microsoft-account hebt, terwijl u in iOS ook een iCloud-account nodig hebt. U meerdere accounts op die ene locatie opslaan. U bijvoorbeeld een persoonlijk account, een werk- of schoolaccount en een persoonlijk, niet-Microsoft-account hebben, zoals voor Facebook, Google, enzovoort.

> [!IMPORTANT]
> Alleen uw persoonlijke en 3rd-party accountreferenties worden opgeslagen, waaronder uw gebruikersnaam en de accountverificatiecode die nodig is om uw identiteit te bewijzen. We slaan geen andere informatie op die aan uw accounts is gekoppeld, waaronder e-mails of bestanden. We koppelen of delen uw accounts ook op geen enkele manier of met een ander product of dienst. En tot slot krijgt uw IT-beheerder geen informatie over een van deze accounts.

## <a name="back-up-your-account-credentials"></a>Een back-up maken van uw accountgegevens

Voordat u een back-up van uw referenties maken, moet u het:

- Een persoonlijk [Microsoft-account](https://account.microsoft.com/account) om op te treden als uw herstelaccount.

- **Alleen voor iOS** moet je een [iCloud-account](https://www.icloud.com/) hebben voor de werkelijke opslaglocatie.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Cloudback-up voor iOS-apparaten inschakelen

- Selecteer op uw iOS-apparaat **Instellingen,** selecteer **Back-up**en schakel **vervolgens iCloud-back-up**in.

    Er wordt een back-up van je account gemaakt van je iCloud-account.

    ![iOS-instellingenscherm, met de locatie van de iCloud-back-upinstellingen](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Cloudback-up voor Android-apparaten inschakelen

- Selecteer op uw Android-apparaat **Instellingen,** selecteer **Back-up**en schakel **vervolgens cloudback-up**in.

    Er wordt een back-up van uw accountgegevens gemaakt van uw cloudaccount.

    ![Android-instellingenscherm, met de locatie van de back-upinstellingen](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Uw accountgegevens herstellen op uw nieuwe apparaat

U uw accountgegevens herstellen van uw cloudaccount, maar u moet er eerst voor zorgen dat het account dat u herstelt niet bestaat in de Microsoft Authenticator-app. Als u bijvoorbeeld uw persoonlijke Microsoft-account herstelt, moet u ervoor zorgen dat u nog geen persoonlijk Microsoft-account hebt ingesteld in de authenticator-app. Deze controle is belangrijk, zodat we er zeker van kunnen zijn dat we een bestaande account niet per ongeluk overschrijven of wissen.

### <a name="to-recover-your-information"></a>Uw gegevens herstellen

1. Open op uw mobiele apparaat de Microsoft Authenticator-app en selecteer **Herstel beginnen** vanaf de onderkant van het scherm.

    ![Microsoft Authenticator-app, die aangeeft waar u klikken op Herstel beginnen](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Meld u aan bij uw herstelaccount met hetzelfde persoonlijke Microsoft-account dat u tijdens het back-upproces hebt gebruikt.

    Uw accountgegevens worden hersteld naar het nieuwe apparaat.

Nadat u klaar bent met het herstel, merkt u mogelijk dat uw persoonlijke Microsoft-accountverificatiecodes in de Microsoft Authenticator-app verschillen tussen uw oude en nieuwe telefoons. De codes zijn verschillend omdat elk apparaat zijn eigen unieke referentie heeft, maar beide zijn geldig en werken tijdens het aanmelden met behulp van de bijbehorende telefoon.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Extra accounts herstellen die meer verificatie vereisen

Als u pushmeldingen gebruikt met uw persoonlijke of werk- of schoolaccounts, ontvangt u een waarschuwing op het scherm waarin staat dat u extra verificatie moet bieden voordat u uw gegevens herstellen. Omdat pushmeldingen een referentie moeten gebruiken die is gekoppeld aan uw specifieke apparaat en nooit via het netwerk wordt verzonden, moet u uw identiteit bewijzen voordat de referentie op uw apparaat wordt gemaakt.

Voor persoonlijke Microsoft-accounts u uw identiteit bewijzen door uw wachtwoord in te voeren, samen met een alternatief e-mailadres of telefoonnummer. Voor werk- of schoolaccounts moet u een QR-code scannen die door uw accountprovider aan u is gegeven.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Aanvullende verificatie voor persoonlijke accounts bieden

1. Selecteer **in** het scherm Accounts van de Microsoft Authenticator-app het account dat u wilt herstellen. Selecteer op een Android-apparaat de pijl naast het account dat u wilt herstellen.

    ![Microsoft Authenticator-app, met de beschikbare accounts met de bijbehorende vervolgkeuzepijlen](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    Tik op een iOS-apparaat op het account dat u wilt herstellen om de volledige schermweergave van het account te openen.

    ![Microsoft Authenticator-app, met de beschikbare accounts met de bijbehorende vervolgkeuzepijlen](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. Meld u aan om het account te herstellen. Selecteer Aanmelden om op een Android-apparaat **aan te melden om te herstellen.**

    ![Microsoft Authenticator-app om uw aanmeldingsgegevens op Android in te voeren](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    Tik op een iOS-apparaat op de accounttegel die u wilt herstellen en tik vervolgens op de optie om u aan te melden en te herstellen. Typ vervolgens uw wachtwoord en bevestig vervolgens uw e-mailadres of telefoonnummer als extra verificatie.unt.

    ![Microsoft Authenticator-app om uw aanmeldingsgegevens in te voeren op iOS](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Aanvullende verificatie voor werk- of schoolaccounts bieden

1. Meld u aan om het account te herstellen. Selecteer Aanmelden om op een Android-apparaat **aan te melden om te herstellen.**

    ![Microsoft Authenticator-app herstelt een werk- of schoolaccount op Android](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    Tik op een iOS-apparaat op het account dat u wilt herstellen om de volledige schermweergave van het account te openen.

    ![Microsoft Authenticator-app herstelt een werk- of schoolaccount op iOS](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. U een QR-code gebruiken om een account te herstellen. Selecteer **QR-code scannen om te herstellen**en scan vervolgens de QR-code.

    Op Android:

    ![Microsoft Authenticator-app op Android, zodat u uw QR-code scannen](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    Op iOS:

    ![Microsoft Authenticator-app op iOS, waarmee u uw QR-code scannen](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >Zie [Aan de slag met de Microsoft Authenticator-app](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) of [Beveiligingsgegevens instellen om een authenticator-app te gebruiken,](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app)op basis van de vraag of uw beheerder beveiligingsgegevens heeft ingeschakeld, voor meer informatie over QR-codes en hoe u er een krijgen.
    >
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, ontvangt u mogelijk een prompt met de vraag of u de app toegang wilt geven tot uw camera (iOS) of dat de app foto's kan maken en video (Android) opnemen. U moet **Toestaan** selecteren, zodat de authenticator-app toegang heeft tot uw camera om in de volgende stap een foto van de QR-code te maken. Als u de camera niet toestaat, u de authenticator-app nog steeds instellen, maar u moet de codegegevens handmatig toevoegen. Zie [Handmatig een account toevoegen aan de app voor](user-help-auth-app-add-account-manual.md)informatie over het handmatig toevoegen van de code.

## <a name="troubleshoot-backup-and-recovery-problems"></a>Back-up- en herstelproblemen oplossen

Er zijn een aantal redenen waarom uw back-up mogelijk niet beschikbaar is

- **Veranderende besturingssystemen**: Uw back-up wordt opgeslagen in de iCloud voor iOS en in de cloudopslagprovider van Microsoft voor Android. Dit betekent dat uw back-up niet beschikbaar is als u schakelt tussen Android- en iOS-apparaten. Als u overstapt, moet u uw accounts handmatig opnieuw maken in de Microsoft Authenticator-app.

- **Netwerkproblemen:** als u netwerkgerelateerde problemen ondervindt, controleert u of u bent verbonden met het netwerk en dat u zich correct hebt aangemeld bij uw account.

- **Accountproblemen:** als je accountgerelateerde problemen ondervindt, moet je ervoor zorgen dat je goed bent aangemeld bij je account. Voor iOS betekent dit dat u moet zijn aangemeld bij iCloud met hetzelfde AppleID-account als uw iPhone.

- **Per ongeluk verwijderen:** het is mogelijk dat u uw back-upaccount hebt verwijderd van uw vorige apparaat of tijdens het beheren van uw cloudopslagaccount. In deze situatie moet u uw account handmatig opnieuw maken in de app.

- **Bestaande Microsoft Authenticator-accounts:** Als u al accounts hebt ingesteld in de Microsoft Authenticator-app, kan de app uw back-upaccounts niet herstellen. Het voorkomen van herstel zorgt ervoor dat uw accountgegevens niet worden overschreven met verouderde informatie. In deze situatie moet u bestaande accountgegevens verwijderen uit de bestaande accounts die zijn ingesteld in uw Authenticator-app voordat u uw back-up herstellen.

- **Back-up is verouderd:** als uw back-upgegevens verouderd zijn, wordt u mogelijk gevraagd de gegevens te vernieuwen door u opnieuw aan te melden bij uw Microsoft Recovery-account. Uw herstelaccount is het persoonlijke Microsoft-account dat u in eerste instantie hebt gebruikt om uw back-up op te slaan. Als een aanmelding vereist is, ziet u een rode stip op uw menu of actiebalk, of ziet u een uitroepteken waarin u wordt gevraagd zich aan te melden om de back-up te herstellen. Nadat u het pictogram hebt geselecteerd, wordt u gevraagd zich opnieuw aan te melden om uw gegevens bij te werken.

## <a name="next-steps"></a>Volgende stappen

Nu u een back-up hebt gemaakt en uw accountgegevens op uw nieuwe apparaat hebt hersteld, u de Microsoft Authenticator-app blijven gebruiken om uw identiteit te verifiëren. Zie [Aanmelden bij uw accounts met de Microsoft Authenticator-app voor](user-help-sign-in.md)meer informatie.

## <a name="related-articles"></a>Verwante artikelen:

- [Wat is de Microsoft Authenticator-app?](user-help-auth-app-overview.md)

- [Veelgestelde vragen over Microsoft Authenticator-app](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
