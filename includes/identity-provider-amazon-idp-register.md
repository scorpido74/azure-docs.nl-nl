---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900373"
---
1. Meld u aan bij de [Amazon-ontwikkelaars console](https://developer.amazon.com/dashboard) met uw Amazon-account referenties.
1. Als u dit nog niet hebt gedaan, klikt u op registreren, volgt **u**de registratie stappen voor de ontwikkelaar en accepteert u het beleid.
1. Selecteer in het dash board **Aanmelden bij Amazon**.
1. Selecteer **een nieuw beveiligings profiel maken**.
1. Voer een **naam voor het beveiligings profiel**, de beschrijving van het **beveiligings profiel**en de URL voor `https://www.contoso.com/privacy` de privacyverklaring van de **toestemming**in, bijvoorbeeld de URL van de privacyverklaring is een pagina die u beheert en die privacy-informatie verstrekt aan gebruikers. Klik vervolgens op **Opslaan**.
1. Selecteer in de sectie **Aanmelden met Amazon-configuraties** de **naam van het beveiligings profiel** dat u hebt gemaakt, klik op het pictogram **beheren** en selecteer **Webinstellingen**.
1. Kopieer de waarden van de **client-id**in het gedeelte **Web Settings** . Selecteer **geheim weer geven** om het client geheim te ontvangen en kopieer het vervolgens. U hebt beide nodig om een Amazon-account te configureren als een id-provider in uw Tenant. **Client geheim** is een belang rijke beveiligings referentie.
1. Selecteer in de sectie **Webinstellingen** de optie **bewerken**. In **toegestane oorsprongen** en **toegestane retour-url's**voert u de juiste url's in (hierboven aangegeven). 
1. Klik op **Opslaan**.
