---
title: Het hulpprogramma Voorwaardelijke toegang wat als - Azure Active Directory
description: Ontdek hoe u de impact van uw beleid voor voorwaardelijke toegang op uw omgeving begrijpen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc87b434664ba12cefeb233972e749f631d8a2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620688"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>Problemen oplossen met het gereedschap Wat als in Voorwaardelijke toegang

[Voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md) is een mogelijkheid van Azure Active Directory (Azure AD) waarmee u bepalen hoe geautoriseerde gebruikers toegang hebben tot uw cloud-apps. Hoe weet u wat u verwachten van het beleid voor voorwaardelijke toegang in uw omgeving? Als u deze vraag wilt beantwoorden, u het **gereedschap Voorwaardelijke toegang wat als gebruiken.**

In dit artikel wordt uitgelegd hoe u deze tool gebruiken om uw beleid voor voorwaardelijke toegang te testen.

## <a name="what-it-is"></a>Wat is het?

Met het **beleid Voorwaardelijke toegang wat als** u inzicht krijgen in de impact van uw beleid voor voorwaardelijke toegang op uw omgeving. In plaats van het testen van uw beleid door meerdere aanmeldingen handmatig uit te voeren, u met deze tool een gesimuleerde aanmelding van een gebruiker evalueren. De simulatie schat de impact van deze aanmelding op uw beleid in en genereert een simulatierapport. In het rapport worden niet alleen de toegepaste beleid voor voorwaardelijke toegang vermeld, maar ook [het klassieke beleid](policy-migration.md#classic-policies) als deze bestaan.    

Het gereedschap **Wat als** biedt een manier om snel het beleid te bepalen dat van toepassing is op een specifieke gebruiker. U de informatie bijvoorbeeld gebruiken als u een probleem moet oplossen.    

## <a name="how-it-works"></a>Hoe werkt het?

In het **gereedschap Voorwaardelijke toegang wat als**moet u eerst de instellingen configureren van het aanmeldingsscenario dat u wilt simuleren. Deze instellingen omvatten:

- De gebruiker die u wilt testen 
- De cloud-apps die de gebruiker zou proberen te openen
- De voorwaarden waaronder de toegang tot de geconfigureerde cloud-apps wordt uitgevoerd
     
Als volgende stap u een simulatierun starten die uw instellingen evalueert. Alleen beleid dat is ingeschakeld, maakt deel uit van een evaluatieuitgevoerd.

Wanneer de evaluatie is voltooid, genereert de tool een rapport over het betrokken beleid.

## <a name="running-the-tool"></a>Het gereedschap uitvoeren

U vindt het gereedschap **Wat als** op de pagina Voorwaardelijke toegang **[- Beleid](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** in de Azure-portal.

Als u het gereedschap wilt starten, klikt u op de werkbalk boven aan de lijst met beleidsregels op **Wat als**.

![What If](./media/what-if-tool/01.png)

Voordat u een evaluatie uitvoeren, moet u de instellingen configureren.

## <a name="settings"></a>Instellingen

In deze sectie vindt u informatie over de instellingen van de simulatierun.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>Gebruiker

U slechts één gebruiker selecteren. Dit is het enige vereiste veld.

### <a name="cloud-apps"></a>Cloud-apps

De standaardinstelling voor deze instelling is **Alle cloud-apps**. Met de standaardinstelling wordt een evaluatie uitgevoerd van alle beschikbare beleidsregels in uw omgeving. U het bereik beperken tot beleid dat van invloed is op specifieke cloud-apps.

### <a name="ip-address"></a>IP-adres

Het IP-adres is één IPv4-adres om de [locatievoorwaarde](location-condition.md)na te bootsen. Het adres vertegenwoordigt het adres van het apparaat dat door uw gebruiker wordt gebruikt om zich aan te melden. U het IP-adres van een apparaat verifiëren door bijvoorbeeld te navigeren naar [Wat is mijn IP-adres.](https://whatismyipaddress.com)    

### <a name="device-platforms"></a>Apparaatplatformen

Deze instelling bootst de toestand van de [apparaatplatforms](concept-conditional-access-conditions.md#device-platforms) na en vertegenwoordigt het equivalent van **alle platforms (inclusief niet-ondersteunde)**. 

### <a name="client-apps"></a>Client-apps

Met deze instelling wordt de [voorwaarde client-apps nagebootst.](concept-conditional-access-conditions.md#client-apps-preview)
Standaard zorgt deze instelling voor een evaluatie van alle beleidsregels waarbij **browser-** of **mobiele apps en desktopclients** afzonderlijk of beide zijn geselecteerd. Het detecteert ook beleid dat **Exchange ActiveSync (EAS) afdwingt.** U deze instelling beperken door het selecteren van:

- **Browser** om alle beleidsregels te evalueren met ten minste **browser** geselecteerd. 
- **Mobiele apps en desktopclients** om alle beleidsregels te evalueren met ten minste **mobiele apps en desktopclients** geselecteerd. 

### <a name="sign-in-risk"></a>Aanmeldingsrisico

Met deze instelling wordt de [aanmeldingsrisicoconditie nagebootst.](concept-conditional-access-conditions.md#sign-in-risk)   

## <a name="evaluation"></a>Evaluatie 

U start een evaluatie door op **Wat als te**klikken. Het evaluatieresultaat biedt u een rapport dat bestaat uit: 

![What If](./media/what-if-tool/03.png)

- Een indicator of er klassiek beleid bestaat in uw omgeving
- Beleid dat van toepassing is op uw gebruiker
- Beleidsregels die niet van toepassing zijn op uw gebruiker

Als er [klassieke beleidsregels](policy-migration.md#classic-policies) bestaan voor de geselecteerde cloud-apps, wordt een indicator aan u gepresenteerd. Door op de indicator te klikken, wordt u doorgestuurd naar de pagina klassiek beleid. Op de pagina klassiek beleid u een klassiek beleid migreren of gewoon uitschakelen. U terugkeren naar uw evaluatieresultaat door deze pagina te sluiten.

In de lijst met beleidsregels die van toepassing zijn op uw geselecteerde gebruiker, u ook een lijst met [subsidiebesturingselementen](concept-conditional-access-grant.md) en [sessiebesturingselementen](concept-conditional-access-session.md) vinden waaraan uw gebruiker moet voldoen.

In de lijst met beleidsregels die niet van toepassing zijn op uw gebruiker, u en ook de redenen vinden waarom dit beleid niet van toepassing is. Voor elk opgenomen beleid vertegenwoordigt de reden de eerste voorwaarde die niet is voldaan. Een mogelijke reden voor een beleid dat niet wordt toegepast, is een uitgeschakeld beleid omdat ze niet verder worden geëvalueerd.   

## <a name="next-steps"></a>Volgende stappen

- Zie MFA vereisen voor specifieke apps met [Voorwaardelijke toegang met Azure Active Directory Conditional Access](app-based-mfa.md)als u wilt weten hoe u een beleid voor voorwaardelijke toegang configureren.
- Als u klaar bent om beleid voor voorwaardelijke toegang voor uw omgeving te configureren, raadpleegt u de [aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory](best-practices.md). 
- Als u klassiek beleid wilt migreren, [raadpleegt u Klassiek beleid migreren in de Azure-portal](policy-migration.md)  
