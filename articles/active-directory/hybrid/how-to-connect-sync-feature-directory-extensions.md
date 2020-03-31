---
title: 'Azure AD Connect-synchronisatie: Directory-extensies | Microsoft Documenten'
description: In dit onderwerp wordt de functie directory-extensies in Azure AD Connect beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80438319a6337dd6f28f9bdca8a428829b6cb0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77917910"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Synchronisatie van Azure AD Connect: Directory-extensies
U directory-extensies gebruiken om het schema in Azure Active Directory (Azure AD) uit te breiden met uw eigen kenmerken van on-premises Active Directory. Met deze functie u LOB-apps bouwen door kenmerken te gebruiken die u on-premises blijft beheren. Deze kenmerken kunnen worden verbruikt via [extensies.](https://docs.microsoft.com/graph/extensibility-overview
) U de beschikbare kenmerken bekijken met [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). U deze functie ook gebruiken om dynamische groepen te maken in Azure AD.

Op dit moment verbruikt geen Enkele Office 365-werkbelasting deze kenmerken.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Aanpassen welke kenmerken moeten worden gesynchroniseerd met Azure AD

U configureert welke extra kenmerken u wilt synchroniseren in het aangepaste instellingenpad in de installatiewizard.

>[!NOTE]
>Het vak Beschikbare kenmerken is hoofdlettergevoelig.

![Wizard Schema-extensie](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

De installatie toont de volgende kenmerken, die geldige kandidaten zijn:

* Objecttypen voor gebruiker en groep
* Kenmerken met één waarde: tekenreeks, booleaan, geheel getal, binair
* Kenmerken met meerdere waarden: tekenreeks, binair


>[!NOTE]
> Hoewel Azure AD Connect het synchroniseren van multigewaardeerde Active Directory-kenmerken met Azure AD ondersteunt als multigewaardeerde directory-extensies, is er momenteel geen manier om de gegevens die zijn geüpload in kenmerk van de meergewaardeerde directoryextensie op te halen/te gebruiken.

De lijst met kenmerken wordt gelezen uit de schemacache die is gemaakt tijdens de installatie van Azure AD Connect. Als u het Active Directory-schema hebt uitgebreid met extra kenmerken, moet u [het schema vernieuwen](how-to-connect-installation-wizard.md#refresh-directory-schema) voordat deze nieuwe kenmerken zichtbaar zijn.

Een object in Azure AD kan maximaal 100 kenmerken hebben voor directory-extensies. De maximale lengte is 250 tekens. Als een kenmerkwaarde langer is, wordt deze door de synchronisatieengine afgekapt.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Configuratiewijzigingen in Azure AD die door de wizard zijn aangebracht

Tijdens de installatie van Azure AD Connect wordt een toepassing geregistreerd waar deze kenmerken beschikbaar zijn. U deze toepassing zien in de Azure-portal. De naam is altijd **Tenant Schema Extension App**.

![App voor schema-extensie](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Zorg ervoor dat u **Alle toepassingen** selecteert om deze app te zien.

De kenmerken zijn vooraf vastgelegd met **extensie \_{ApplicationId}\_**. ApplicationId heeft dezelfde waarde voor alle kenmerken in uw Azure AD-tenant. U hebt deze waarde nodig voor alle andere scenario's in dit onderwerp.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Kenmerken weergeven met de Microsoft Graph API

Deze kenmerken zijn nu beschikbaar via de Microsoft Graph API, met behulp van [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> In de Microsoft Graph API moet u vragen om de kenmerken terug te geven. Selecteer de kenmerken als volgt `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division`expliciet: .
>
> Zie [Microsoft Graph: Queryparameters gebruiken voor](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)meer informatie.

## <a name="use-the-attributes-in-dynamic-groups"></a>De kenmerken in dynamische groepen gebruiken

Een van de handigste scenario's is om deze kenmerken te gebruiken in dynamische beveiliging of Office 365-groepen.

1. Een nieuwe groep maken in Azure AD. Geef het een goede naam en zorg ervoor dat het **type lidmaatschap** **dynamische gebruiker**is.

   ![Schermafbeelding van een nieuwe groep](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Selecteer om **dynamische query toe**te voegen . Als je kijkt naar de eigenschappen, dan zie je niet deze uitgebreide attributen. Je moet ze eerst toevoegen. Klik **op Aangepaste extensie-eigenschappen opdoen,** voer de toepassings-id in en klik op **Eigenschappen vernieuwen**.

   ![Schermafbeelding van de waarde van directory-extensies](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Open de vervolgkeuzelijst voor de eigenschap en houd er rekening mee dat de kenmerken die u hebt toegevoegd, nu zichtbaar zijn.

   ![Schermafbeelding met nieuwe kenmerken die worden weergegeven in de gebruikersinterface](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Vul de uitdrukking aan uw wensen aan. In ons voorbeeld is de regel ingesteld op **(user.extension_9d98ed114c4840d298fad781915f27e4_division -eq "Sales and marketing")**.

4. Nadat de groep is gemaakt, geeft u Azure AD wat tijd om de leden te vullen en vervolgens de leden te controleren.

   ![Schermafbeelding met leden in de dynamische groep](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [synchronisatieconfiguratie van Azure AD Connect.](how-to-connect-sync-whatis.md)

Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
