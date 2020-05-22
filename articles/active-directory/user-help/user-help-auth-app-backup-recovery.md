---
title: Back-ups van accounts maken en herstellen met de Microsoft Authenticator-app-Azure AD
description: Meer informatie over het maken van een back-up en het herstellen van uw back-upaccountgegevens met behulp van de app Microsoft Authenticator.
services: active-directory
author: curtand
manager: daveba
ms.subservice: user-help
ms.service: active-directory
ms.workload: identity
ms.topic: end-user-help
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: f78ef7ddf48612728c0409b0f904c0964e2272b5
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83741597"
---
# <a name="back-up-and-recover-account-credentials-using-the-microsoft-authenticator-app"></a>Back-up en herstel van account referenties met behulp van de app Microsoft Authenticator

**Van toepassing op:**

- iOS-apparaten met versie 5.7.0 en hoger

- Android-apparaten met versie 6.6.0 en hoger

De Microsoft Authenticator-app maakt een back-up van uw account referenties en gerelateerde app-instellingen, zoals de volg orde van uw accounts, naar de Cloud. Na het maken van een back-up kunt u de app ook gebruiken om uw gegevens op een nieuw apparaat te herstellen, waardoor het voor komen van vergrendelde of het opnieuw maken van accounts.

Voor elke opslag locatie voor back-ups moet u één persoonlijk Microsoft-account hebben, terwijl iOS ook een iCloud-account nodig heeft. U kunt meerdere accounts op die ene locatie opslaan. U kunt bijvoorbeeld een persoonlijk account, een werk-of school account hebben en een persoonlijk, niet-Microsoft-account zoals voor Facebook, Google, enzovoort.

> [!IMPORTANT]
> Alleen de referenties van uw persoonlijke en derde account worden opgeslagen, inclusief uw gebruikers naam en de verificatie code van de account die is vereist om uw identiteit te bewijzen. Er worden geen andere gegevens opgeslagen die zijn gekoppeld aan uw accounts, met inbegrip van e-mail berichten of bestanden. We koppelen of delen uw accounts ook niet op enigerlei wijze of met andere producten of services. En ten slotte krijgt de IT-beheerder geen informatie over een van deze accounts.

## <a name="back-up-your-account-credentials"></a>Back-up maken van uw account referenties

Voordat u een back-up van uw referenties kunt maken, hebt u het volgende nodig:

- Een persoonlijke [Microsoft-account](https://account.microsoft.com/account) om als uw herstel account te fungeren.

- **Alleen voor IOS** moet u een iCloud- [account](https://www.icloud.com/) hebben voor de werkelijke opslag locatie.

### <a name="to-turn-on-cloud-backup-for-ios-devices"></a>Cloud back-up inschakelen voor iOS-apparaten

- Selecteer op uw iOS-apparaat **instellingen**, selecteer **back-up**en schakel vervolgens **iCloud-back-up**in.

    Er wordt een back-up van uw account referenties gemaakt op uw iCloud-account.

    ![scherm iOS-instellingen, waarin de locatie van de iCloud-back-upinstellingen wordt weer gegeven](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on.png)

### <a name="to-turn-on-cloud-backup-for-android-devices"></a>Cloud back-up inschakelen voor Android-apparaten

- Selecteer op uw Android-apparaat **instellingen**, selecteer **back-up**en schakel vervolgens **Cloud back-up**in.

    Er wordt een back-up van uw account referenties gemaakt op uw Cloud account.

    ![Scherm Android-instellingen, waarin de locatie van de back-upinstellingen wordt weer gegeven](./media/user-help-auth-app-backup-recovery/backup-and-recovery-turn-on-android.png)

## <a name="recover-your-account-credentials-on-your-new-device"></a>Uw account referenties herstellen op het nieuwe apparaat

U kunt uw account referenties herstellen vanuit uw Cloud account, maar u moet er eerst voor zorgen dat het account dat u wilt herstellen, niet aanwezig is in de app Microsoft Authenticator. Als u bijvoorbeeld uw persoonlijke Microsoft-account herstelt, moet u ervoor zorgen dat u geen persoonlijke Microsoft-account hebt ingesteld in de verificator-app. Deze controle is van belang om ervoor te zorgen dat er niet per ongeluk een bestaand account wordt overschreven of gewist.

### <a name="to-recover-your-information"></a>Uw gegevens herstellen

1. Open op uw mobiele apparaat de Microsoft Authenticator-app en selecteer vervolgens **herstel starten** onder aan het scherm.

    ![App Microsoft Authenticator, waarin wordt weer gegeven waar u op herstel starten klikt](./media/user-help-auth-app-backup-recovery/backup-and-recovery-begin-recovery.png)

2. Meld u aan bij uw herstel account met dezelfde persoonlijke Microsoft-account die u hebt gebruikt tijdens het back-upproces.

    Uw account referenties worden hersteld naar het nieuwe apparaat.

Nadat u uw herstel hebt voltooid, ziet u mogelijk dat uw persoonlijke Microsoft-account verificatie codes in de Microsoft Authenticator-app verschillen tussen uw oude en nieuwe telefoons. De codes verschillen, omdat elk apparaat een eigen unieke referentie heeft, maar beide geldig zijn en werken tijdens het aanmelden met behulp van de bijbehorende telefoon.

## <a name="recover-additional-accounts-requiring-more-verification"></a>Extra accounts herstellen waarvoor meer verificatie is vereist

Als u push meldingen met uw persoonlijke werk-of school account gebruikt, ontvangt u een waarschuwing op het scherm met de melding dat u aanvullende verificatie moet opgeven voordat u uw gegevens kunt herstellen. Omdat push meldingen vereisen dat een referentie wordt gebruikt die is gekoppeld aan uw specifieke apparaat en nooit via het netwerk wordt verzonden, moet u uw identiteit bewijzen voordat de referentie op uw apparaat wordt gemaakt.

Voor persoonlijke micro soft-accounts kunt u uw identiteit bewijzen door uw wacht woord in te voeren en een alternatief e-mail adres of telefoon nummer. Voor werk-of school accounts moet u een QR-code scannen die u hebt ontvangen van uw account provider.

### <a name="to-provide-additional-verification-for-personal-accounts"></a>Aanvullende verificatie voor persoonlijke accounts opgeven

1. Selecteer in het scherm **accounts** van de Microsoft Authenticator-app het account dat u wilt herstellen. Selecteer op een Android-apparaat de pijl naast het account dat u wilt herstellen.

    ![Microsoft Authenticator app, waarbij de beschik bare accounts worden weer gegeven met de bijbehorende vervolg keuzelijst pijlen](./media/user-help-auth-app-backup-recovery/backup-and-recovery-arrow.png)

    Tik op een iOS-apparaat op het account dat u wilt herstellen om de weer gave volledig scherm van het account te openen.

    ![Microsoft Authenticator app, waarbij de beschik bare accounts worden weer gegeven met de bijbehorende vervolg keuzelijst pijlen](./media/user-help-auth-app-backup-recovery/ios-select-account-to-recover.png)

2. Meld u aan om het account te herstellen. Op een Android-apparaat selecteert **u aanmelden om te herstellen**.

    ![Microsoft Authenticator app om uw aanmeldings gegevens in Android in te voeren](./media/user-help-auth-app-backup-recovery/backup-and-recovery-sign-in.png)

    Tik op een iOS-apparaat op de tegel van het account dat u wilt herstellen en tik vervolgens op de optie om u aan te melden en te herstellen. Typ vervolgens uw wacht woord en bevestig uw e-mail adres of telefoon nummer als aanvullende verificatie. unt.

    ![Microsoft Authenticator app om uw aanmeldings gegevens in iOS in te voeren](./media/user-help-auth-app-backup-recovery/ios-sign-in-to-recover.png)

### <a name="to-provide-additional-verification-for-work-or-school-accounts"></a>Aanvullende verificatie voor werk-of school accounts opgeven

1. Meld u aan om het account te herstellen. Op een Android-apparaat selecteert **u aanmelden om te herstellen**.

    ![Een werk-of school account op Android herstellen met Microsoft Authenticator-app](./media/user-help-auth-app-backup-recovery/backup-and-recovery-additional-accts.png)

    Tik op een iOS-apparaat op het account dat u wilt herstellen om de weer gave volledig scherm van het account te openen.

    ![Een werk-of school account op iOS herstellen met Microsoft Authenticator-app](./media/user-help-auth-app-backup-recovery/ios-recover-work-or-school-account.png)

2. U kunt een QR-code gebruiken om een account te herstellen. Selecteer **QR-code scannen om te herstellen**en scan de QR-code.

    Op Android:

    ![Microsoft Authenticator app op Android, zodat u uw QR-code kunt scannen](./media/user-help-auth-app-backup-recovery/backup-and-recovery-scan-qr-code.png)

    Op iOS:

    ![Microsoft Authenticator app op iOS, zodat u uw QR-code kunt scannen](./media/user-help-auth-app-backup-recovery/ios-scan-qr-code.png)

    >[!NOTE]
    >Voor meer informatie over QR-codes en hoe u er een kunt krijgen, raadpleegt u aan [de slag met de Microsoft Authenticator-app](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) of [beveiligings gegevens instellen voor het gebruik van een verificator-app](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app), op basis van het feit of uw beheerder beveiligings gegevens heeft ingeschakeld.
    >
    >Als dit de eerste keer is dat u de Microsoft Authenticator-app instelt, wordt u mogelijk gevraagd of u de app toegang wilt geven tot uw camera (iOS) of dat de app afbeeldingen en record video (Android) mag maken. U moet **toestaan** inschakelen zodat de verificator-app toegang heeft tot uw camera om een foto van de QR-code in de volgende stap te maken. Als u de camera niet toestaat, kunt u nog steeds de verificator-app instellen, maar u moet de code gegevens hand matig toevoegen. Zie [hand matig een account toevoegen aan de app](user-help-auth-app-add-account-manual.md)voor meer informatie over het hand matig toevoegen van de code.

## <a name="troubleshoot-backup-and-recovery-problems"></a>Problemen met back-up en herstel oplossen

Er zijn een aantal redenen waarom uw back-up mogelijk niet beschikbaar is

- **Besturings systemen wijzigen**: uw back-up wordt opgeslagen in de ICloud voor IOS en in de Cloud-opslag provider van micro soft voor Android. Dit betekent dat uw back-up niet beschikbaar is als u overschakelt tussen Android-en iOS-apparaten. Als u de switch maakt, moet u uw accounts hand matig opnieuw maken binnen de app Microsoft Authenticator.

- **Netwerk problemen**: als u problemen met het netwerk ondervindt, controleert u of u verbinding hebt met het netwerk en of u bent aangemeld bij uw account.

- **Account problemen**: als u problemen ondervindt met het account, moet u ervoor zorgen dat u bent aangemeld bij uw account. Voor iOS betekent dit dat u moet zijn aangemeld bij iCloud met hetzelfde Apple-account als uw iPhone.

- **Onbedoeld verwijderen**: het is mogelijk dat u uw back-upaccount hebt verwijderd van het vorige apparaat of tijdens het beheren van uw opslag account voor de Cloud. In dit geval moet u uw account hand matig opnieuw in de app maken.

- **Bestaande Microsoft Authenticator accounts**: als u al accounts hebt ingesteld in de Microsoft Authenticator app, kan de app uw back-upaccount niet herstellen. Door herstel te voor komen, zorgt u ervoor dat uw account gegevens niet worden overschreven met verouderde informatie. In dit geval moet u de bestaande account gegevens verwijderen uit de bestaande accounts die in de verificator-app zijn ingesteld voordat u uw back-up kunt herstellen.

- De **back-up is verouderd**: als uw back-upgegevens verouderd zijn, wordt u mogelijk gevraagd om de gegevens te vernieuwen door u opnieuw aan te melden bij uw micro soft Recovery-account. Uw herstel account is de persoonlijke Microsoft-account die u in eerste instantie hebt gebruikt om uw back-up op te slaan. Als een aanmelding vereist is, ziet u een rode stip in uw menu of actie balk, of ziet u een uitroep teken waarin u wordt gevraagd om u aan te melden om het terugzetten van een back-up te volt ooien. Nadat u het pictogram hebt geselecteerd, wordt u gevraagd om u opnieuw aan te melden om uw gegevens bij te werken.

## <a name="next-steps"></a>Volgende stappen

Nu u een back-up hebt gemaakt en uw account referenties hebt hersteld naar uw nieuwe apparaat, kunt u de app Microsoft Authenticator blijven gebruiken om uw identiteit te verifiëren. Zie [Aanmelden bij uw accounts met behulp van de app Microsoft Authenticator](user-help-sign-in.md)voor meer informatie.

## <a name="related-articles"></a>Verwante artikelen:

- [Wat is de Microsoft Authenticator-app?](user-help-auth-app-overview.md)

- [Veelgestelde vragen over Microsoft Authenticator-app](user-help-auth-app-faq.md)

- [Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/)
