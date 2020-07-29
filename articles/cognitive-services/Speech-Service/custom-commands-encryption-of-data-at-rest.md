---
title: Aangepaste opdrachten service versleuteling van data-at-rest
titleSuffix: Azure Cognitive Services
description: Aangepaste opdrachten voor het versleutelen van gegevens in rust.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2020
ms.author: sausin
ms.openlocfilehash: 83b6e6be8764a86c41bd9156cc96f8a594dbe1e9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294308"
---
# <a name="custom-commands-encryption-of-data-at-rest"></a>Versleuteling van data-at-rest met aangepaste opdrachten

Met aangepaste opdrachten worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Met de Custom commands service-versleuteling worden uw gegevens beveiligd en kunt u voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

> [!NOTE]
> Met de service voor aangepaste opdrachten wordt versleuteling niet automatisch ingeschakeld voor de LUIS-resources die zijn gekoppeld aan uw toepassing. Indien nodig moet u de versleuteling van uw LUIS-resource inschakelen [.](./../LUIS/luis-encryption-of-data-at-rest.md)

## <a name="about-cognitive-services-encryption"></a>Over Cognitive Services versleuteling
Gegevens worden versleuteld en ontsleuteld met behulp van [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) [-compatibele 256-bits AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) versleuteling. Versleuteling en ontsleuteling zijn transparant, wat betekent dat versleuteling en toegang voor u worden beheerd. Uw gegevens zijn standaard beveiligd en u hoeft uw code of toepassingen niet te wijzigen om te kunnen profiteren van versleuteling.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

Wanneer u aangepaste opdrachten gebruikt, slaat de spraak service de volgende gegevens op in de Cloud:
* Configuratie-JSON achter de toepassing voor aangepaste opdrachten
* LUIS-ontwerp en Voorspellings sleutel

Uw abonnement maakt standaard gebruik van door micro soft beheerde versleutelings sleutels. U kunt uw abonnement echter ook beheren met uw eigen coderings sleutels. Door de klant beheerde sleutels (CMK), ook wel bekend als het nemen van uw eigen sleutel (BYOK), bieden meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelings sleutels controleren die worden gebruikt voor het beveiligen van uw gegevens.


> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschik bare resources die zijn gemaakt na 27 juni 2020. Als u CMK met spraak Services wilt gebruiken, moet u een nieuwe spraak resource maken. Zodra de resource is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

Als u de mogelijkheid wilt om door de klant beheerde sleutels te gebruiken, vult u het aanvraag formulier voor door de klant beheerde sleutel in. Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Nadat u hebt goedgekeurd voor het gebruik van CMK met spraak Services, moet u een nieuwe spraak resource maken op basis van de Azure Portal.
   > [!NOTE]
   > **Door de klant beheerde sleutels (CMK) worden alleen ondersteund voor aangepaste opdrachten.**
   >
   >  **Custom Speech en aangepaste spraak bieden alleen ondersteuning voor uw eigen opslag (BYOS).**  [Meer informatie](speech-encryption-of-data-at-rest.md)
   >
   > Als u de gegeven spraak resource gebruikt voor toegang tot deze service, moet aan de nalevings vereisten worden voldaan door expliciet BYOS te configureren.


## <a name="customer-managed-keys-with-azure-key-vault"></a>Door de klant beheerde sleutels met Azure Key Vault

U moet Azure Key Vault gebruiken om door de klant beheerde sleutels op te slaan. U kunt uw eigen sleutels maken en deze opslaan in een sleutelkluis of u kunt de Azure Key Vault API's gebruiken om sleutels te genereren. De spraak bron en de sleutel kluis moeten zich in dezelfde regio en in dezelfde Azure Active Directory-Tenant (Azure AD) bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Wat is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)voor meer informatie over Azure Key Vault.

Wanneer een nieuwe spraak bron wordt gemaakt en gebruikt om aangepaste opdrachten in te richten, worden gegevens altijd versleuteld met door micro soft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te scha kelen op het moment dat de resource wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutel kluis moet worden ingericht met toegangs beleid waarmee sleutel machtigingen worden verleend aan de beheerde identiteit die aan de Cognitive Services-resource is gekoppeld. De beheerde identiteit is alleen beschikbaar nadat de resource is gemaakt met behulp van de prijs categorie die is vereist voor CMK.

Door door de klant beheerde sleutels in te scha kelen, wordt ook een door het systeem toegewezen [beheerde identiteit](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), een functie van Azure AD, ingeschakeld. Zodra de door het systeem toegewezen beheerde identiteit is ingeschakeld, wordt deze bron geregistreerd bij Azure Active Directory. Na registratie krijgt de beheerde identiteit toegang tot de Key Vault geselecteerd tijdens de installatie van de door de klant beheerde sleutel. 

> [!IMPORTANT]
> Als u door het systeem toegewezen beheerde identiteiten uitschakelt, wordt de toegang tot de sleutel kluis verwijderd en worden alle gegevens die zijn versleuteld met de klant sleutels niet meer toegankelijk. Alle functies, afhankelijk van deze gegevens, werken niet meer.

> [!IMPORTANT]
> Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. Wanneer u door de klant beheerde sleutels in de Azure Portal configureert, wordt er automatisch een beheerde identiteit toegewezen onder de voor vallen. Als u het abonnement, de resource groep of de resource van een Azure AD-Directory vervolgens naar een andere verplaatst, wordt de beheerde identiteit die aan de resource is gekoppeld, niet overgezet naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **een abonnement overdragen tussen Azure AD-mappen** in [Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.  

## <a name="configure-azure-key-vault"></a>Azure Key Vault configureren

Voor het gebruik van door de klant beheerde sleutels moeten twee eigenschappen worden ingesteld in de sleutel kluis, **zacht verwijderen** en **niet leeg**worden gemaakt. Deze eigenschappen zijn niet standaard ingeschakeld, maar kunnen worden ingeschakeld met Power shell of Azure CLI in een nieuwe of bestaande sleutel kluis.

> [!IMPORTANT]
> Als u niet de opties **voorlopig verwijderen** en **niet wissen** hebt ingeschakeld en u de sleutel verwijdert, kunt u de gegevens in uw cognitieve service resource niet herstellen.

Voor meer informatie over het inschakelen van deze eigenschappen voor een bestaande sleutelkluis raadpleegt u de secties **Voorlopig verwijderen inschakelen** en **Beveiliging tegen leegmaken inschakelen** in een van de volgende artikelen:

- [Voorlopig verwijderen gebruiken met Power shell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Voorlopig verwijderen gebruiken met CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Alleen RSA-sleutels met een grootte van 2048 worden ondersteund met Azure Storage versleuteling. Zie **Key Vault sleutels** in [over Azure Key Vault sleutels, geheimen en certificaten](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)voor meer informatie over sleutels.

## <a name="enable-customer-managed-keys-for-your-speech-resource"></a>Door de klant beheerde sleutels voor uw spraak resource inschakelen

Voer de volgende stappen uit om door de klant beheerde sleutels in te scha kelen in de Azure Portal:

1. Navigeer naar uw spraak bron.
1. Klik op de Blade **instellingen** voor uw spraak bron op **versleuteling**. Selecteer de optie door de **klant beheerde sleutels** , zoals wordt weer gegeven in de volgende afbeelding.

 ![Scherm afbeelding die laat zien hoe u door de klant beheerde sleutels selecteert](media/custom-commands/select-cmk.png)

## <a name="specify-a-key"></a>Een sleutel opgeven

Nadat u door de klant beheerde sleutels hebt ingeschakeld, hebt u de mogelijkheid om een sleutel op te geven die u wilt koppelen aan de Cognitive Services resource.

### <a name="specify-a-key-as-a-uri"></a>Een sleutel opgeven als een URI

Voer de volgende stappen uit om een sleutel als URI op te geven:

1. Als u de sleutel-URI in de Azure Portal wilt zoeken, navigeert u naar uw sleutel kluis en selecteert u de instelling **sleutels** . Selecteer de gewenste sleutel en klik vervolgens op de sleutel om de versies ervan weer te geven. Selecteer een sleutel versie om de instellingen voor die versie weer te geven.
1. Kopieer de waarde van het veld **sleutel-id** , dat de URI levert.

    ![Scherm opname van sleutel kluis sleutel-URI](../media/cognitive-services-encryption/key-uri-portal.png)

1. Kies in de **versleutelings** instellingen voor uw spraak service de optie **sleutel-URI opgeven** .
1. Plak de URI die u hebt gekopieerd in het veld **sleutel-URI** .

1. Geef het abonnement op dat de sleutel kluis bevat.
1. Sla uw wijzigingen op.

### <a name="specify-a-key-from-a-key-vault"></a>Een sleutel uit een sleutel kluis opgeven

Als u een sleutel van een sleutel kluis wilt opgeven, moet u eerst controleren of u een sleutel kluis hebt die een sleutel bevat. Voer de volgende stappen uit om een sleutel van een sleutel kluis op te geven:

1. Kies de optie **selecteren uit Key Vault** .
1. Selecteer de sleutel kluis met de sleutel die u wilt gebruiken.
1. Selecteer de sleutel in de sleutel kluis.

   ![Scherm afbeelding met door de klant beheerde sleutel optie](media/custom-commands/configure-cmk-fromKV.png)

1. Sla uw wijzigingen op.

## <a name="update-the-key-version"></a>De sleutel versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, werkt u de spraak resource bij om de nieuwe versie te gebruiken. Volg deze stappen:

1. Navigeer naar uw spraak bron en geef de **versleutelings** instellingen weer.
1. Voer de URI in voor de nieuwe sleutel versie. U kunt ook de sleutel kluis en de sleutel opnieuw selecteren om de versie bij te werken.
1. Sla uw wijzigingen op.

## <a name="use-a-different-key"></a>Een andere sleutel gebruiken

Voer de volgende stappen uit om de sleutel te wijzigen die wordt gebruikt voor versleuteling:

1. Navigeer naar uw spraak bron en geef de **versleutelings** instellingen weer.
1. Voer de URI in voor de nieuwe sleutel. U kunt ook de sleutel kluis selecteren en een nieuwe sleutel kiezen.
1. Sla uw wijzigingen op.

## <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels draaien

U kunt een door de klant beheerde sleutel in Azure Key Vault draaien volgens uw nalevings beleid. Wanneer de sleutel wordt geroteerd, moet u de spraak resource bijwerken om de nieuwe sleutel-URI te gebruiken. Zie [de sleutel versie bijwerken](#update-the-key-version)voor meer informatie over het bijwerken van de resource voor het gebruik van een nieuwe versie van de sleutel in de Azure Portal.

Als u de sleutel roteert, wordt het opnieuw versleutelen van gegevens in de bron niet geactiveerd. Er is geen verdere actie vereist van de gebruiker.

## <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u Power shell of Azure CLI. Zie [Azure Key Vault Power shell](https://docs.microsoft.com/powershell/module/az.keyvault//) of [Azure Key Vault cli](https://docs.microsoft.com/cli/azure/keyvault)(Engelstalig) voor meer informatie. Toegang intrekken effectief blokkeert de toegang tot alle gegevens in de Cognitive Services resource, omdat de versleutelings sleutel niet toegankelijk is voor Cognitive Services.

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Wanneer u door de klant beheerde sleutels uitschakelt, wordt uw spraak bron vervolgens versleuteld met door micro soft beheerde sleutels. Voer de volgende stappen uit om door de klant beheerde sleutels uit te scha kelen:

1. Navigeer naar uw spraak bron en geef de **versleutelings** instellingen weer.
1. Schakel het selectie vakje naast de instelling **uw eigen sleutel gebruiken** uit.

## <a name="next-steps"></a>Volgende stappen

* [Spraak formulier voor door de klant beheerde sleutel aanvragen](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Wat zijn beheerde identiteiten?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)



