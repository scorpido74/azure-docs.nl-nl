---
title: Factureringsmodel voor Azure Active Directory B2C
description: Meer informatie over het mau-factureringsmodel (Monthly Active Users) van Azure AD B2C en hoe u facturering voor een specifiek Azure-abonnement inschakelen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30ecb1e6faa29482a8d69dd1d08e4f127f515596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190004"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Factureringsmodel voor Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) wordt gefactureerd aan een gekoppeld Azure-abonnement en maakt gebruik van een mau-factureringsmodel (monthly active users). Meer informatie over het koppelen van een Azure AD B2C-bron aan een abonnement en hoe het MAU-factureringsmodel werkt in de volgende secties.

> [!IMPORTANT]
> Dit artikel bevat geen prijsinformatie. Zie [Azure Active Directory B2C-prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)voor de meest recente informatie over facturering en prijzen voor gebruiksfacturering en -prijzen.

## <a name="monthly-active-users-mau-billing"></a>Maandelijksactieve gebruikers (MAU) facturering

Azure AD B2C-facturering wordt gemeten op het aantal unieke gebruikers met verificatieactiviteit binnen een kalendermaand, bekend als mau-facturering (monthly active users).

Vanaf **1 november 2019**worden alle nieuw gemaakte Azure AD B2C-tenants gefactureerd per maandelijkse actieve gebruikers (MAU). Bestaande huurders die op of na 1 november 2019 [zijn gekoppeld aan een abonnement,](#link-an-azure-ad-b2c-tenant-to-a-subscription) worden per maand actieve gebruikers (MAU) gefactureerd.

Als u een bestaande Azure AD B2C-tenant hebt die vóór 1 november 2019 aan een abonnement is gekoppeld, u ervoor kiezen om een van de volgende handelingen uit te voeren:

* Upgrade naar het mau-factureringsmodel (monthly active users), of
* Blijf op het factureringsmodel per verificatie

### <a name="upgrade-to-monthly-active-users-billing-model"></a>Upgraden naar het factureringsmodel voor maandelijksactieve gebruikers

Eigenaren van Azure-abonnementen die beheerderstoegang hebben tot de Azure AD B2C-bron, kunnen overschakelen naar het MAU-factureringsmodel. Factureringsopties zijn geconfigureerd in uw Azure AD B2C-bron.

De overstap naar maandelijkse actieve gebruikers (MAU) facturering is **onomkeerbaar**. Zodra u een Azure AD B2C-bron converteert naar het factureringsmodel op basis van MAU, u die bron niet terugzetten naar het factureringsmodel per verificatie.

U als u de overstap maken naar MAU-facturering voor een bestaande Azure AD B2C-bron:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als eigenaar van het abonnement.
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de Azure AD B2C-map die u wilt upgraden naar MAU-facturering.<br/>
    ![Directory- en abonnementsfilter in Azure-portal](./media/billing/portal-mau-01-select-b2c-directory.png)
1. Selecteer Azure AD **B2C**in het linkermenu . Selecteer Ook **Alle services** en zoek naar Azure AD **B2C**en selecteer deze .
1. Selecteer op de pagina **Overzicht** van de Azure AD B2C-tenant de koppeling onder **Resourcenaam**. U wordt doorverwezen naar de Azure AD B2C-bron in uw Azure AD-tenant.<br/>
    ![Azure AD B2C-bronkoppeling gemarkeerd in Azure-portal](./media/billing/portal-mau-02-b2c-resource-link.png)
1. Selecteer op de **overzichtspagina** van de Azure AD B2C-bron onder **Factureerbare eenheden**de koppeling **Per verificatie (Wijzigen in MAU).**<br/>
    ![Wijzigen in MAU-koppeling die is gemarkeerd in Azure-portal](./media/billing/portal-mau-03-change-to-mau-link.png)
1. Selecteer **Bevestigen** om de upgrade naar MAU-facturering te voltooien.<br/>
    ![Dialoogvenster factureringsbevestiging op basis van MAU in Azure-portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)

### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Wat u verwachten wanneer u overstapt op MAU-facturering vanaf facturering per verificatie

Mau-gebaseerde meting is ingeschakeld zodra u, de eigenaar van het abonnement/bron, de wijziging bevestigt. Uw maandelijkse factuur weerspiegelt de eenheden van authenticatie gefactureerd tot de wijziging, en nieuwe eenheden van MAU te beginnen met de wijziging.

Gebruikers worden niet dubbel geteld tijdens de overgangsmaand. Unieke actieve gebruikers die voorafgaand aan de wijziging verifiëren, betalen een tarief per verificatie in een kalendermaand. Diezelfde gebruikers worden niet opgenomen in de MAU-berekening voor de rest van de factureringscyclus van het abonnement. Bijvoorbeeld:

* De Contoso B2C huurder heeft 1.000 gebruikers. 250 gebruikers zijn actief in een bepaalde maand. De abonnementsbeheerder verandert op de 10e van de maand van per-authenticatie naar maandelijks actieve gebruikers (MAU).
* Facturering voor 1e-10e wordt gefactureerd met behulp van het per-authenticatiemodel.
  * Als 100 gebruikers zich tijdens deze periode (1e-10e) aanmelden, worden deze gebruikers getagd als *betaald voor de maand*.
* Facturering vanaf de 10e (het effectieve tijdstip van overgang) wordt gefactureerd tegen het MAU-tarief.
  * Als er in deze periode nog eens 150 gebruikers zich aanmelden (10e-30e), worden alleen de extra 150 gefactureerd.
  * De voortgezette activiteit van de eerste 100 gebruikers heeft geen invloed op facturering voor de rest van de kalendermaand.

Tijdens de factureringsperiode van de overgang ziet de eigenaar van het abonnement waarschijnlijk vermeldingen voor beide methoden (per verificatie en per MAU) in hun Azure-abonnementsfactureringsoverzicht:

* Een vermelding voor het gebruik tot de datum/tijd van wijziging die per verificatie weergeeft.
* Een vermelding voor het gebruik na de wijziging die maandelijks actieve gebruikers (MAU) weerspiegelt.

Zie [Azure Active Directory B2C-prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)voor de meest recente informatie over facturering en prijzen voor gebruiksfacturering en -prijzen voor Azure AD B2C.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Een Azure AD B2C-tenant koppelen aan een abonnement

Gebruikskosten voor Azure Active Directory B2C (Azure AD B2C) worden gefactureerd aan een Azure-abonnement. Wanneer een Azure AD B2C-tenant wordt gemaakt, moet de tenantbeheerder de Azure AD B2C-tenant expliciet koppelen aan een Azure-abonnement.

De abonnementskoppeling wordt bereikt door een *resource* Azure AD B2C-bron te maken binnen het doelAzure-abonnement. Er kunnen verschillende Azure AD B2C-resources worden gemaakt in één Azure-abonnement, samen met andere Azure-bronnen zoals virtuele machines, opslagaccounts en Logische apps. U alle bronnen binnen een abonnement bekijken door naar de Azure AD-tenant (Azure Directory) te gaan waaraan het abonnement is gekoppeld.

Een abonnement dat is gekoppeld aan een Azure AD B2C-tenant, kan worden gebruikt voor de facturering van Azure AD B2C-gebruik of andere Azure-resources, waaronder extra Azure AD B2C-resources. Het kan niet worden gebruikt om andere Azure-licentiegebaseerde services of Office 365-licenties toe te voegen aan de Azure AD B2C-tenant.

### <a name="prerequisites"></a>Vereisten

* [Azure-abonnement](https://azure.microsoft.com/free/)
* [Azure AD B2C-tenant](tutorial-create-tenant.md) die u aan een abonnement wilt koppelen
  * U moet een tenantbeheerder zijn
  * De huurder mag nog niet zijn gekoppeld aan een abonnement

### <a name="create-the-link"></a>De koppeling maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Directory + abonnement** in het bovenste menu en selecteer vervolgens de map met het Azure-abonnement dat u wilt gebruiken *(niet* de map met de Azure AD B2C-tenant).
1. Selecteer Een resource `Active Directory B2C` **maken,** voer het veld Zoeken in de **marketplace** in en selecteer vervolgens Azure Active **Directory B2C**.
1. Selecteer **Maken**
1. Selecteer **Een bestaande Azure AD B2C-tenant koppelen aan mijn Azure-abonnement**.
1. Selecteer een **Azure AD B2C-tenant** in de vervolgkeuzelijst. Alleen huurders waarvoor u een globale beheerder bent en die nog niet zijn gekoppeld aan een abonnement, worden weergegeven. Het azure **AD B2C-bronnaamveld** wordt gevuld met de domeinnaam van de Azure AD B2C-tenant die u selecteert.
1. Selecteer een actief **Azure-abonnement** waarvan u beheerder bent.
1. Selecteer **onder Resourcegroep** **Nieuw maken**en geef vervolgens de locatie van de **resourcegroep**op . De instellingen voor resourcegroepen hier hebben geen invloed op de locatie, prestaties of factureringsstatus van uw Azure AD B2C-tenant.
1. Selecteer **Maken**.
    ![De pagina azure AD B2C-resourcecreatie in Azure-portal](./media/billing/portal-01-create-b2c-resource-page.png)

Nadat u deze stappen hebt voltooid voor een Azure AD B2C-tenant, wordt uw Azure-abonnement gefactureerd in overeenstemming met uw Azure Direct- of Enterprise-overeenkomstgegevens, indien van toepassing.

### <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Uw Azure AD B2C-tenantbronnen beheren

Nadat u de Azure AD B2C-bron in een Azure-abonnement hebt gemaakt, moet u een nieuwe bron van het type 'B2C-tenant' zien verschijnen met uw andere Azure-resources.

U deze bron gebruiken om:

* Naar het abonnement navigeren om factureringsgegevens te bekijken
* De tenant-id van de Azure AD B2C-tenant in de GUID-indeling aanschaffen
* Ga naar uw Azure AD B2C-tenant
* Een ondersteuningsverzoek indienen
* Uw Azure AD B2C-tenantbron verplaatsen naar een ander Azure-abonnement of -brongroep

![Pagina B2C-broninstellingen in de Azure-portal](./media/billing/portal-02-b2c-resource-overview.png)

### <a name="regional-restrictions"></a>Regionale beperkingen

Als u in uw abonnement regionale beperkingen hebt ingesteld voor het maken van Azure-resources, u met die beperking de Azure AD B2C-bron maken.

Om dit probleem te beperken, u uw regionale beperkingen versoepelen.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>CSP-abonnementen (Azure Cloud Solution Providers)

CSP-abonnementen (Azure Cloud Solution Providers) worden ondersteund in Azure AD B2C. De functionaliteit is beschikbaar met API's of de Azure-portal voor Azure AD B2C en voor alle Azure-resources. CSP-abonnementsbeheerders kunnen relaties met Azure AD B2C koppelen, verplaatsen en verwijderen, zoals met andere Azure-bronnen.

Het beheer van Azure AD B2C met behulp van op rollen gebaseerd toegangscontrole wordt niet beïnvloed door de koppeling tussen de Azure AD B2C-tenant en een Azure CSP-abonnement. Op rollen gebaseerd toegangsbeheer wordt bereikt door tenantrollen te gebruiken, niet op abonnementen gebaseerde rollen.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Het factureringsabonnement voor Azure AD B2C-tenant wijzigen

Azure AD B2C-tenants kunnen naar een ander abonnement worden verplaatst als de bron- en doelabonnementen binnen dezelfde Azure Active Directory-tenant bestaan.

Zie Resources verplaatsen [naar een nieuwe brongroep of -abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)voor meer informatie over het verplaatsen van Azure-resources zoals uw Azure AD B2C-tenant naar een ander abonnement.

Voordat u de verhuizing start, moet u het hele artikel lezen om de beperkingen en vereisten voor een dergelijke verhuizing volledig te begrijpen. Naast instructies voor het verplaatsen van resources, bevat het kritieke informatie zoals een checklist voor het verplaatsen van vooraf en het valideren van de verplaatsingsbewerking.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Active Directory B2C-prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)voor de meest recente prijsinformatie.
