---
title: Selfservice-toepassingstoewijzing configureren | Microsoft Documenten
description: Self-service applicatie toegang inschakelen om gebruikers in staat te stellen hun eigen applicaties te vinden
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
ms.date: 10/23/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcccc64e0c766164a06932e9b65a4459816f9deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409113"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Selfservice-toepassingstoewijzing configureren

Voordat uw gebruikers zelf toepassingen kunnen ontdekken vanuit hun toegangspaneel voor Mijn apps, moet u **selfservice-toepassingen toegang** inschakelen tot toepassingen die gebruikers zelf willen laten ontdekken en toegang tot aanvragen. Deze functionaliteit is beschikbaar voor toepassingen die zijn toegevoegd vanuit de [Azure AD Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app), Azure AD Application [Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) of zijn toegevoegd via toestemming van de gebruiker [of beheerder](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience). 

Deze functie is een geweldige manier om tijd en geld te besparen als IT-groep en wordt ten zeerste aanbevolen als onderdeel van een moderne implementatie van toepassingen met Azure Active Directory.

Met deze functie kunt u het volgende doen:

-   Laat gebruikers zelf toepassingen ontdekken vanuit het [toegangspaneel van Mijn apps](https://myapps.microsoft.com/) zonder de IT-groep lastig te vallen.

-   Voeg deze gebruikers toe aan een vooraf geconfigureerde groep, zodat u zien wie toegang heeft aangevraagd, de toegang verwijderen en de rollen beheren die aan hen zijn toegewezen.

-   Laat een zakelijke goedkeurder optioneel aanvragen voor toegang tot toepassingen goedkeuren, zodat de IT-groep dat niet hoeft te doen.

-   Configureer optioneel maximaal 10 personen die de toegang tot deze toepassing kunnen goedkeuren.

-   Laat een zakelijke goedkeurder optioneel de wachtwoorden instellen die gebruikers kunnen gebruiken om zich aan te melden bij de toepassing, direct vanuit het [Application Access Panel](https://myapps.microsoft.com/)van de bedrijfsinstantie.

-   Optioneel automatisch toewijzen self-service toegewezen gebruikers aan een toepassing rol rechtstreeks.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Self-service applicatie toegang inschakelen om gebruikers in staat te stellen hun eigen applicaties te vinden

Selfservice-applicatietoegang is een geweldige manier om gebruikers in staat te stellen toepassingen zelf te ontdekken en de bedrijfsgroep optioneel toegang tot die toepassingen te laten goedkeuren. Voor toepassingen met één wachtwoord voor één melding u de bedrijfsgroep ook toestaan de referenties te beheren die aan die gebruikers zijn toegewezen via hun eigen mijn apps-toegangspanelen.

Voer de onderstaande stappen uit om selfservice-toepassingstoegang tot een toepassing in te schakelen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als globale beheerder.

2. Selecteer **Azure Active Directory**. Selecteer **Enterprise-toepassingen**in het linkernavigatiemenu .

3. Selecteer de toepassing in de lijst. Als u de toepassing niet ziet, typt u de naam in het zoekvak. Of gebruik de filterbesturingselementen om het toepassingstype, de status of de zichtbaarheid te selecteren en selecteer **Vervolgens Toepassen**.

4. Selecteer **Selfservice**in het linkernavigatiemenu .

5. Als u selfservice-toepassingstoegang voor deze toepassing wilt inschakelen, schakelt u **gebruikers toestaan om toegang tot deze toepassing aan te vragen?** **Yes.**

6. Klik naast welke groep gebruikers moet worden **Select group** **toegewezen?** Kies een groep en klik op **Selecteren**. Wanneer het verzoek van een gebruiker wordt goedgekeurd, worden deze aan deze groep toegevoegd. Wanneer u het lidmaatschap van deze groep bekijkt, u zien wie toegang heeft gekregen tot de toepassing via selfservicetoegang.
  
    > [!NOTE]
    > Met deze instelling worden geen groepen ondersteund die zijn gesynchroniseerd vanuit on-premises.

7. **Optioneel:** Als u zakelijke goedkeuring wilt vereisen voordat gebruikers toegang krijgen, stelt u **Yes**de **goedkeuring vereisen in voordat u toegang verleent tot deze toepassing?**

8. **Optioneel: Stel voor toepassingen met alleen wachtwoordaanmelding aan** om zakelijke fiatteurs toe te staan de wachtwoorden op te geven die naar deze **Yes**toepassing worden verzonden voor goedgekeurde gebruikers, de approvers toestaan om de wachtwoorden van gebruikers voor deze toepassing in **te stellen?**

9. **Optioneel:** Als u de zakelijke fiatteurs wilt opgeven die toegang tot deze toepassing mogen goedkeuren, **naast Wie toegang tot deze toepassing mag goedkeuren,** klikt u op **Fiatjes selecteren**en selecteert u maximaal 10 individuele zakelijke fiatteurs. Klik vervolgens op **Selecteren**.

    >[!NOTE]
    >Groepen worden niet ondersteund. U maximaal 10 individuele zakelijke fiatteurs selecteren. Als u meerdere fiatteurs opgeeft, kan elke enkele goedkeurder een toegangsverzoek goedkeuren.

10. **Optioneel:** **Voor toepassingen die rollen blootstellen**om zelfservicegoedgekeurde gebruikers aan een rol toe te wijzen, **Select Role**naast de taak Waaraan gebruikers in deze toepassing moeten **worden toegewezen?** Klik vervolgens op **Selecteren**.

11. Klik op de knop **Opslaan** boven aan het deelvenster om te voltooien.

Zodra u de configuratie van de selfservice-toepassing hebt voltooid, kunnen gebruikers naar het [toegangspaneel mijn apps voor Mijn apps](https://myapps.microsoft.com/) navigeren en op de knop **Selfservice-apps toevoegen** klikken om de apps te vinden die zijn ingeschakeld met selfservicetoegang. Zakelijke fiatteurs zien ook een melding in hun [toegangspaneel mijn apps](https://myapps.microsoft.com/). U een e-mail inschakelen waarin u hen op de hoogte stelt wanneer een gebruiker toegang heeft gevraagd tot een toepassing waarvoor zijn goedkeuring vereist is.

## <a name="next-steps"></a>Volgende stappen
[Azure Active Directory instellen voor groepsbeheer met self-service](../users-groups-roles/groups-self-service-management.md)
