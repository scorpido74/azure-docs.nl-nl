---
title: Bedreigingen voor resources en gegevens beheren
titleSuffix: Azure AD B2C
description: Meer informatie over detectie- en mitigatietechnieken voor denial-of-service-aanvallen en wachtwoordaanvallen in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a438363b054361420222804dffac7973470e82e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183598"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Bedreigingen voor resources en gegevens beheren in Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) heeft ingebouwde functies waarmee u beschermen tegen bedreigingen voor uw resources en gegevens. Deze bedreigingen omvatten denial-of-service-aanvallen en wachtwoordaanvallen. Denial-of-service-aanvallen kunnen resources niet beschikbaar maken voor beoogde gebruikers. Wachtwoordaanvallen leiden tot ongeautoriseerde toegang tot bronnen.

## <a name="denial-of-service-attacks"></a>Denial-of-service-aanvallen

Azure AD B2C verdedigt zich tegen SYN flood-aanvallen met behulp van een SYN-cookie. Azure AD B2C beschermt ook tegen denial-of-service-aanvallen door limieten te gebruiken voor tarieven en verbindingen.

## <a name="password-attacks"></a>Wachtwoordaanvallen

Wachtwoorden die door gebruikers zijn ingesteld, moeten redelijk complex zijn. Azure AD B2C heeft mitigatietechnieken voor wachtwoordaanvallen. Mitigatie omvat detectie van brute-force wachtwoord aanvallen en woordenboek wachtwoord aanvallen. Door verschillende signalen te gebruiken, analyseert Azure AD B2C de integriteit van aanvragen. Azure AD B2C is ontworpen om beoogde gebruikers intelligent te onderscheiden van hackers en botnets.

Azure AD B2C gebruikt een geavanceerde strategie om accounts te vergrendelen. De accounts zijn vergrendeld op basis van het IP-adres van het verzoek en de ingevoerde wachtwoorden. De duur van de uitsluiting neemt ook toe op basis van de kans dat het een aanval is. Nadat een wachtwoord 10 keer zonder succes is geprobeerd (de standaarddrempel voor de poging), vindt een uitsluiting van één minuut plaats. De volgende keer dat een login niet succesvol is nadat het account is ontgrendeld (dat wil zeggen, nadat het account automatisch is ontgrendeld door de service zodra de lock-outperiode is verstreken), vindt nog een lock-out van een minuut plaats en gaat verder voor elke mislukte login. Het herhaaldelijk invoeren van hetzelfde wachtwoord telt niet als meerdere mislukte aanmeldingen.

De eerste 10 lock-out periodes zijn een minuut lang. De volgende 10 lock-out periodes zijn iets langer en toename in duur na elke 10 lockout periodes. De uitsluitingsteller wordt teruggezet naar nul na een succesvolle aanmelding wanneer het account niet is vergrendeld. Uitsluitingsperioden kunnen tot vijf uur duren.

## <a name="manage-password-protection-settings"></a>Instellingen voor wachtwoordbeveiliging beheren

Ga als u de instellingen voor wachtwoordbeveiliging beheren, inclusief de drempel voor uitsluiting:

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Gebruik het **filter Directory + abonnement** in het bovenste menu om de map te selecteren die uw Azure AD B2C-tenant bevat.
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer **onder Beveiliging**de optie **Verificatiemethoden (Voorbeeld)** en selecteer **Vervolgens Wachtwoordbeveiliging**.
1. Voer de gewenste instellingen voor wachtwoordbeveiliging in en selecteer **Opslaan**.

    ![Azure-portal Wachtwoordbeveiligingspagina in Azure AD-instellingen](./media/threat-management/portal-02-password-protection.png)
    <br />*De vergrendelingsdrempel instellen op 5 in **instellingen voor wachtwoordbeveiliging** *.

## <a name="view-locked-out-accounts"></a>Vergrendelde accounts weergeven

Als u informatie wilt verkrijgen over vergrendelde accounts, u het [aanmeldingsrapport](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)van Active Directory controleren. Selecteer **Onder Status**de optie **Mislukt**. Mislukte aanmeldingspogingen met een **aanmeldingsfoutcode** van `50053` een vergrendeld account aangeven:

![Sectie van het aanmeldingsrapport van Azure AD met een vergrendeld account](./media/threat-management/portal-01-locked-account.png)

Zie Foutcodes voor [aanmeldingsactiviteitsrapport](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)voor meer informatie over het weergeven van het aanmeldingsrapport in Azure Active Directory.
