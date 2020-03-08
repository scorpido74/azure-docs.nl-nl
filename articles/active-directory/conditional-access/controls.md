---
title: Aangepaste besturings elementen in voorwaardelijke toegang voor Azure AD
description: Meer informatie over het werken met aangepaste besturings elementen in Azure Active Directory voorwaardelijke toegang.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fff08690eb2807fbbd50f297761c57d3fef88fe
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671849"
---
# <a name="custom-controls-preview"></a>Aangepaste besturings elementen (preview-versie)

Aangepaste besturings elementen zijn een functie van de Azure Active Directory Premium P1-editie. Wanneer u aangepaste besturings elementen gebruikt, worden uw gebruikers omgeleid naar een compatibele service om te voldoen aan aanvullende vereisten buiten Azure Active Directory. Om aan dit besturings element te voldoen, wordt de browser van een gebruiker omgeleid naar de externe service, worden eventuele vereiste verificatie-of validatie activiteiten uitgevoerd en wordt vervolgens teruggeleid naar Azure Active Directory. Azure Active Directory controleert het antwoord en, als de gebruiker is geverifieerd of gevalideerd, wordt de gebruiker in de stroom voor voorwaardelijke toegang voortgezet.

Met deze besturings elementen wordt het gebruik van bepaalde externe of aangepaste services als besturings elementen voor voorwaardelijke toegang toegestaan en worden de mogelijkheden van voorwaardelijke toegang in het algemeen uitgebreid.

Providers die momenteel een compatibele service aanbieden, zijn onder andere:

- [Duo-beveiliging](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Ping-identiteit](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Neem rechtstreeks contact op met de provider voor meer informatie over deze services.

## <a name="creating-custom-controls"></a>Aangepaste besturings elementen maken

Als u een aangepast besturings element wilt maken, moet u eerst contact opnemen met de provider die u wilt gebruiken. Elke niet-micro soft-provider heeft een eigen proces en vereisten om zich aan te melden, te abonneren of anderszins deel te nemen aan de service, en om aan te geven dat u wilt integreren met voorwaardelijke toegang. Op dat moment geeft de provider u een blok gegevens in JSON-indeling. Met deze gegevens kan de provider en de voorwaardelijke toegang samen werken voor uw Tenant, wordt het nieuwe besturings element gemaakt en wordt gedefinieerd hoe voorwaardelijke toegang kan zien of uw gebruikers verificatie hebben uitgevoerd met de provider.

Aangepaste besturings elementen kunnen niet worden gebruikt met automatisering van identiteits beveiliging waarvoor multi-factor Authentication is vereist of voor het verhogen van rollen in privileged Identity Manager (PIM).

Kopieer de JSON-gegevens en plak deze in het bijbehorende tekstvak. Breng geen wijzigingen aan in de JSON, tenzij u de wijziging die u aanbrengt expliciet begrijpt. Als u een wijziging aanbrengt, kan de verbinding tussen de provider en micro soft worden verbroken en kunnen u en uw gebruikers uw accounts vergren delen.

De optie voor het maken van een aangepast besturings element bevindt zich in de sectie **beheren** van de pagina **voorwaardelijke toegang** .

![Beheer](./media/controls/82.png)

Als u op **nieuw aangepast besturings element**klikt, wordt een Blade geopend met een tekstvak voor de JSON-gegevens van uw besturings element.  

![Beheer](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Aangepaste besturings elementen verwijderen

Als u een aangepast besturings element wilt verwijderen, moet u er eerst voor zorgen dat het niet wordt gebruikt in een beleid voor voorwaardelijke toegang. Eenmaal voltooid:

1. Ga naar de lijst met aangepaste besturings elementen
1. Klik op...  
1. Selecteer **Verwijderen**.

## <a name="editing-custom-controls"></a>Aangepaste besturings elementen bewerken

Als u een aangepast besturings element wilt bewerken, moet u het huidige besturings element verwijderen en een nieuw besturings element maken met de bijgewerkte gegevens.

## <a name="next-steps"></a>Volgende stappen

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

- [Modus alleen rapport](concept-conditional-access-report-only.md)

- [Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
