---
title: Probleem met selfservice-toegang tot toepassingen | Microsoft Documenten
description: Problemen met betrekking tot selfservice-toepassingstoegang oplossen
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a981dfb1d72c21eccf2ad7119ea219114ed15aed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784284"
---
# <a name="problem-using-self-service-application-access"></a>Probleem met selfservice-toepassingstoegang

Selfservice-applicatietoegang is een geweldige manier om gebruikers in staat te stellen toepassingen zelf te ontdekken, zodat de bedrijfsgroep optioneel toegang tot die toepassingen kan goedkeuren. U de bedrijfsgroep toestaan de referenties te beheren die aan deze gebruikers zijn toegewezen voor toepassingen met een enkel wachtwoord met één melding vanaf hun toegangspanelen.

Voordat uw gebruikers zelf toepassingen kunnen ontdekken vanuit hun toegangspaneel, moet u **selfservice-toepassingen toegang** inschakelen tot toepassingen die u gebruikers zelf wilt laten ontdekken en toegang tot aanvragen.

## <a name="general-issues-to-check-first"></a>Algemene problemen om eerst te controleren

-   Zorg ervoor dat de toegang tot selfservicetoepassingen correct is geconfigureerd. Zie "Toegang tot selfservicetoepassingen configureren".

-   Controleer of de gebruiker of groep is ingeschakeld om selfservice-toepassingstoegang aan te vragen.

-   Zorg ervoor dat de gebruiker de juiste plaats bezoekt voor selfservice-toegang tot toepassingen. gebruikers kunnen naar het [toepassingstoegangspaneel](https://myapps.microsoft.com/) navigeren en op de knop **+Toevoegen** klikken om de apps te vinden waartoe u selfservicetoegang hebt ingeschakeld.

-   Als de toegang tot selfservicetoepassingen onlangs is geconfigureerd, probeert u zich na enkele minuten opnieuw aan en uit te melden bij het toegangspaneel van de gebruiker om te zien of de wijzigingen in de selfservicetoegang zijn weergegeven.

## <a name="how-to-configure-self-service-application-access"></a>Toegang tot selfservicetoepassingen configureren

Voer de onderstaande stappen uit om selfservice-toepassingstoegang tot een toepassing in te schakelen:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het navigatiemenu voor de linkerhoofd te klikken op Alle **services.**

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu met de linkerhand van Azure Active Directory.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing waartoe u selfservicetoegang wilt inschakelen in de lijst.

7. Zodra de toepassing wordt geladen, klikt u op **Selfservice** in het navigatiemenu aan de linkerkant van de toepassing.

8. Als u selfservice-toepassingstoegang voor deze toepassing wilt inschakelen, schakelt u **gebruikers toestaan om toegang tot deze toepassing aan te vragen?** **Yes.**

9. Klik vervolgens op de kiezer naast het label **Aan welke groep gebruikers moeten worden toegevoegd,** klikt u de groep waaraan gebruikers moeten worden toegewezen om de groep te selecteren waaraan gebruikers moeten worden toegevoegd.

10. **Optioneel:** Als u een bedrijfsgoedkeuring wilt vereisen voordat gebruikers toegang krijgen, stelt u de goedkeuring **Yes** **vereisen in voordat u toegang verleent tot deze toepassing?**

11. **Optioneel: Als toepassingen met wachtwoord alleen worden aangemeld,** u deze zakelijke fiatteurs toestaan de wachtwoorden op te geven die naar deze toepassing worden verzonden voor goedgekeurde gebruikers, de **appjes toestaan om de wachtwoorden van deze toepassing in te stellen?** **Yes**

12. **Optioneel:** Als u de zakelijke fiatteurs wilt opgeven die toegang tot deze toepassing mogen goedkeuren, klikt u op de kiezer naast het label **Wie mag toegang tot deze toepassing goedkeuren?**

    >[!NOTE]
    > Groepen worden niet ondersteund.
    >
    >

13. **Optioneel:** **Klik voor toepassingen die rollen blootstellen**, als u zelfservicegoedgekeurde gebruikers aan een rol wilt toewijzen, op de kiezer naast de rol waaraan gebruikers in deze toepassing moeten worden **toegewezen?**

14. Klik op de knop **Opslaan** boven aan het blad om te voltooien.

Zodra u de configuratie van de selfservicetoepassing hebt voltooid, kunnen gebruikers naar hun [toepassingstoegangspaneel](https://myapps.microsoft.com/) navigeren en op de knop **+Toevoegen** klikken om de apps te vinden waarvoor u selfservicetoegang hebt ingeschakeld. Zakelijke fiatteurs zien ook een melding in hun [Application Access Panel](https://myapps.microsoft.com/). U een e-mail inschakelen waarin u hen op de hoogte stelt wanneer een gebruiker toegang heeft gevraagd tot een toepassing waarvoor zijn goedkeuring vereist is. 

Deze goedkeuringen ondersteunen alleen afzonderlijke goedkeuringswerkstromen, wat betekent dat als u meerdere fiatteurs opgeeft, elke enkele goedkeurder de toegang tot de toepassing kan goedkeuren.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Als deze stappen voor het oplossen van problemen het probleem niet oplossen 

open een support ticket met de volgende informatie indien beschikbaar:

-   Correlatiefout-id

-   UPN (e-mailadres van de gebruiker)

-   TenantID

-   Browsertype

-   Tijdzone en tijd/tijdsbestek tijdens fout treedt op

-   Fiddler sporen

## <a name="next-steps"></a>Volgende stappen
[Azure Active Directory instellen voor groepsbeheer met self-service](../users-groups-roles/groups-self-service-management.md)
