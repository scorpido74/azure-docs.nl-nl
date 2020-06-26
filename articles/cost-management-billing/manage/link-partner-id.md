---
title: Een Azure-account koppelen aan een partner-id
description: Volg afspraken met Azure-klanten door een partner-id aan het gebruikersaccount dat u voor het beheer van de resources van de klant gebruikt, te koppelen.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 05/04/2020
ms.service: cost-management-billing
ms.topic: conceptual
ms.openlocfilehash: 77abfcf300decb3a19da4268d7feb7de1f41f3b5
ms.sourcegitcommit: 24f31287b6a526e23ff5b5469113522d1ccd4467
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84743912"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Een partner-id aan uw Azure-accounts koppelen

Microsoft-partners bieden services waarmee klanten bedrijfs- en missiedoelen kunnen bereiken met behulp van Microsoft-producten. Wanneer partnergebruikers namens de klant Azure-services beheren, configureren en ondersteunen, hebben ze toegang tot de omgeving van de klant nodig. Met behulp van PAL (Partner Admin Link) kunnen partners hun partnernetwerk-id koppelen aan de referenties die zijn gebruikt voor het leveren van de service.

PAL stelt Microsoft in staat om partners te identificeren en te herkennen die Azure-klanten helpen succes te boeken. Microsoft kan invloed en in Azure verbruikte omzet toerekenen aan uw organisatie, op basis van de machtigingen (RBAC-rol) en het bereik (abonnement, resourcegroep, resource) van het account.

## <a name="get-access-from-your-customer"></a>Toegang van uw klant krijgen

Voordat u uw partner-id koppelt, moet uw klant u toegang geven tot hun Azure-resources door een van de volgende opties te gebruiken:

- **Gastgebruiker**: Uw klant kan u als gastgebruiker toevoegen en elke willekeurige RBAC-rol (Role-Based Access Control) aan u toewijzen. Zie [Gastgebruikers uit een andere directory toevoegen](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) voor meer informatie.

- **Directoryaccount**: Uw klant kan een gebruikersaccount voor u maken in zijn eigen directory en elke willekeurige RBAC-rol aan u toewijzen.

- **Service-principal**: Uw klant kan een app of script uit uw organisatie in zijn directory toevoegen en elke willekeurige RBAC-rol aan u toewijzen. De identiteit van de app of het script staat bekend als een service-principal.

- **Azure Lighthouse**: Uw klant kan een abonnement (of resourcegroep) delegeren zodat uw gebruikers ermee kunnen werken vanuit de tenant. Zie [gedelegeerd resourcebeheer van Azure](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) voor meer informatie.

## <a name="link-to-a-partner-id"></a>Koppelen aan een partner-id

Wanneer u toegang hebt tot de resources van de klant, gebruikt u Azure Portal, PowerShell of de Azure CLI om uw Microsoft Partner Network-id (MPN ID) aan uw gebruikers-id of service-principal te koppelen. Koppel de partner-id in elke klanttenant.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Azure Portal gebruiken om een nieuwe partner-id te koppelen

1. Ga naar [Koppelen met een partner-id](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) in Azure Portal.

2. Meld u aan bij Azure Portal.

3. Voer de Microsoft partner-id in. De partner-id is de [Microsoft Partner Network-id](https://partner.microsoft.com/) voor uw organisatie.

   ![Schermopname met Koppelen met een partner-id](./media/link-partner-id/link-partner-id01.png)

4. Als u een partner-id voor een andere klant wilt koppelen, wisselt u van map. Onder **Schakelen tussen mappen** selecteert u uw map.

   ![Schermopname met Schakelen tussen mappen](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>PowerShell gebruiken om een nieuwe partner-id te koppelen

1. Installeer de PowerShell-module [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

2. Meld u bij de tenant van de klant aan met het gebruikersaccount of de service-principal. Zie [Aanmelden met PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps) voor meer informatie.

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Maak een koppeling met de nieuwe partner-id. De partner-id is de [Microsoft Partner Network-id](https://partner.microsoft.com/) voor uw organisatie.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>De gekoppelde partner-id ophalen
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>De gekoppelde partner-id bijwerken
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>De gekoppelde partner-id verwijderen
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>De Azure CLI gebruiken om een nieuwe partner-id te koppelen
1. Installeer de Azure CLI-extensie.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Meld u bij de tenant van de klant aan met het gebruikersaccount of de service-principal. Zie [Aanmelden met de Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) voor meer informatie.

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Maak een koppeling met de nieuwe partner-id. De partner-id is de [Microsoft Partner Network-id](https://partner.microsoft.com/) voor uw organisatie.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>De gekoppelde partner-id ophalen
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>De gekoppelde partner-id bijwerken
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>De gekoppelde partner-id verwijderen
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Volgende stappen

Neem deel aan de discussie in de [Microsoft Partner Community](https://aka.ms/PALdiscussion) om updates te krijgen of feedback te verzenden.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Wie kan een koppeling met de partner-id maken?**

Elke gebruiker van de partnerorganisatie die de Azure-resources van een klant beheert, kan de partner-id aan het account koppelen.

**Kan een partner-id worden gewijzigd nadat deze is gekoppeld?**

Ja. Een gekoppelde partner-id kan worden gewijzigd, toegevoegd of verwijderd.

**Wat gebeurt er als een gebruiker een account in meer dan één klanttenant heeft?**

De koppeling tussen de partner-id en het account wordt uitgevoerd voor elke klanttenant. Koppel de partner-id in elke klanttenant.

**Kunnen andere partners of klanten de koppeling met de partner-id bewerken of verwijderen?**

De koppeling is op gebruikersaccountniveau gekoppeld. Alleen u kunt de koppeling met de partner-id bewerken of verwijderen. De klant en andere partners kunnen de koppeling met de partner-id niet wijzigen.


**Welke MPN ID moet ik gebruikers als mijn bedrijf meerdere id's heeft?**

Partnerlocatieaccounts en de bijbehorende MPN id's moeten worden gebruikt voor het koppelen van partner-id's.  Meer informatie over [partneraccounts](https://docs.microsoft.com/partner-center/account-structure)

**Waar kan ik de beïnvloede omzetrapporten voor gekoppelde partner-id's vinden?**

Rapporten over de prestaties van cloudproducten zijn voor partners beschikbaar via het Partnercentrum in het [dashboard Mijn inzichten](https://partner.microsoft.com/membership/reports/myinsights). U moet Partnerbeheerkoppeling als partnerkoppelingstype selecteren.

**Waarom kan ik mijn klant niet in de rapporten vinden?**

Wanneer u de klant niet in de rapporten ziet, kan dit de volgende redenen hebben

1. Het gekoppelde gebruikersaccount biedt geen [op rollen gebaseerde toegang](https://docs.microsoft.com/azure/role-based-access-control/overview) op welk Azure-abonnement of welke Azure-resource van die klant ook.

2. Het Azure-abonnement waarbij de gebruiker [op rollen gebaseerde toegang](https://docs.microsoft.com/azure/role-based-access-control/overview) heeft, heeft geen gebruik.

**Werken gekoppelde partner-id's met Azure Stack?**

Ja, u kunt uw partner-id voor Azure Stack koppelen.

**Hoe koppel ik mijn partner-id, als mijn bedrijf [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) gebruikt voor toegang tot klantresources?**

Als u klanten onboardt voor gedelegeerd resourcebeheer van Azure door [een aanbieding voor beheerde services te publiceren in Azure Marketplace](https://docs.microsoft.com/azure/lighthouse/how-to/publish-managed-services-offers), wordt uw MPN-id automatisch gekoppeld. Als u [klanten onboardt door Azure Resource Manager-sjablonen te implementeren](https://docs.microsoft.com/azure/lighthouse/how-to/onboard-customer), moet u uw MPN-id (Microsoft Partner Network) koppelen met minstens één gebruikersaccount dat toegang heeft tot al uw abonnementen waarvoor onboarding is uitgevoerd. Let op: u moet dit doen in de tenant van de serviceprovider. Ter vereenvoudiging raden we u aan om in uw tenant een service-principal-account te maken, dat is gekoppeld aan uw MPN-id, en aan elke klant waarvoor u onboarding uitvoert, Lezerstoegang tot dit account te verlenen. In dit voorbeeld wordt de rol van de RBAC-lezer gebruikt en is dit een van de rollen die niet in aanmerking komen voor het verdiende tegoed van de partner. Zie [Rollen en machtigingen voor verdiend tegoed van partners](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3QuW2) voor meer informatie over rollen.


**Hoe kan ik PAL (Partner Admin Link) aan mijn klant uitleggen?**

Met Partner Admin Link (PAL) kan Microsoft die partners identificeren en herkennen die klanten helpen bedrijfsdoelstellingen te behalen en waarde te creëren in de cloud. Klanten moeten partners eerst toegang tot hun Azure-resource verlenen. Zodra de toegang is verleend, wordt de MPN-id (Microsoft Partner Network) gekoppeld. Door deze koppeling leert Microsoft het ecosysteem van IT-serviceproviders kennen en kunnen ze de hulpprogramma's verfijnen die nodig zijn om onze algemene klanten de beste ondersteuning te bieden.

**Welke gegevens worden door PAL verzameld?**

De PAL-koppeling met bestaande referenties bieden Microsoft geen nieuwe klantgegevens. Het biedt Microsoft simpelweg de telemetrie waar een partner actief is betrokken bij de Azure-omgeving van een klant. Microsoft kan invloed uitoefenen en in Azure verbruikte omzet uit de omgeving van de klant toerekenen aan de organisatie van een partner op basis van de machtigingen (RBAC-rol) en het bereik (beheergroep, abonnement, resourcegroep, resource) van het account dat de klant aan de partner heeft gegeven. 

**Heeft dit invloed op de beveiliging van een de Azure-omgeving van een klant?**

Door de koppeling met PAL wordt alleen het MPN-id van de partner toegevoegd aan de referenties die al zijn ingericht; eventuele machtigingen (RBAC-rol) worden niet gewijzigd en er worden geen aanvullende Azure-servicegegevens aan de partner of aan Microsoft gegeven. 

