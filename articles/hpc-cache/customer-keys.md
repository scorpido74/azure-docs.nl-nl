---
title: Door de klant manged sleutels gebruiken om gegevens te versleutelen in Azure HPC-cache
description: Azure Key Vault gebruiken met Azure HPC-cache om toegang tot versleutelingssleutels te beheren in plaats van de standaard door Microsoft beheerde versleutelingssleutels te gebruiken
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a31979763dd1ab5d8f289deef0e30cce27bb0df4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538867"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Door de klant beheerde versleutelingssleutels gebruiken voor Azure HPC-cache

U Azure Key Vault gebruiken om het eigendom van de sleutels te beheren die worden gebruikt om uw gegevens in Azure HPC-cache te versleutelen. In dit artikel wordt uitgelegd hoe u door de klant beheerde sleutels gebruiken voor versleuteling van cachegegevens.

> [!NOTE]
> Alle gegevens die in Azure zijn opgeslagen, ook op de cacheschijven, worden standaard versleuteld met door Microsoft beheerde sleutels. U hoeft alleen de stappen in dit artikel te volgen als u de sleutels wilt beheren die worden gebruikt om uw gegevens te versleutelen.

Deze functie is alleen beschikbaar in deze Azure-regio's:

* VS - oost
* VS - zuid-centraal
* VS - west 2

Er zijn drie stappen om door de klant beheerde sleutelversleuteling voor Azure HPC-cache in te schakelen:

1. Stel een Azure Key Vault in om de sleutels op te slaan.
1. Kies bij het maken van de Azure HPC-cache de sleutelversleuteling met de klant en geeft de sleutelkluis en -sleutelop.
1. Nadat de cache is gemaakt, moet u deze autoriseren om toegang te krijgen tot de sleutelkluis.

Versleuteling is pas volledig ingesteld nadat u deze hebt geautoriseerd vanuit de nieuw gemaakte cache (stap 3). Dit komt omdat u de identiteit van de cache moet doorgeven aan de sleutelkluis om deze een geautoriseerde gebruiker te maken. U dit niet doen voordat u de cache maakt, omdat de identiteit pas bestaat nadat de cache is gemaakt.

Nadat u de cache hebt gemaakt, u niet meer wijzigen tussen door de klant beheerde sleutels en door Microsoft beheerde sleutels. Als uw cache echter door de klant beheerde sleutels gebruikt, u de versleutelingssleutel, de sleutelversie en de sleutelkluis indien nodig [wijzigen.](#update-key-settings)

## <a name="understand-key-vault-and-key-requirements"></a>Inzicht in belangrijke kluis- en belangrijke vereisten

De sleutelkluis en -sleutel moeten aan deze vereisten voldoen om met Azure HPC-cache te kunnen werken.

Eigenschappen van sleutelkluis:

* **Abonnement** - Gebruik hetzelfde abonnement dat wordt gebruikt voor de cache.
* **Regio** - De sleutelkluis moet zich in dezelfde regio bevinden als de Azure HPC-cache.
* **Prijscategorie** - Standaardlaag is voldoende voor gebruik met Azure HPC-cache.
* **Soft delete** - Azure HPC Cache maakt soft delete mogelijk als deze nog niet is geconfigureerd op de sleutelkluis.
* **Zuiveringsbescherming** - Zuiveringsbeveiliging moet ingeschakeld zijn.
* **Toegangsbeleid** - Standaardinstellingen zijn voldoende.
* **Netwerkconnectiviteit** - Azure HPC-cache moet toegang hebben tot de sleutelkluis, ongeacht de eindpuntinstellingen die u kiest.

Belangrijkste eigenschappen:

* **Sleuteltype** - RSA
* **RSA-sleutelgrootte** - 2048
* **Ingeschakeld** - Ja

Toegangsmachtigingen voor sleutelkluizen:

* De gebruiker die de Azure HPC-cache maakt, moet machtigingen hebben die gelijkwaardig zijn aan de [rol Key Vault-bijdrager.](../role-based-access-control/built-in-roles.md#key-vault-contributor) Dezelfde machtigingen zijn nodig voor het instellen en beheren van Azure Key Vault.

  Lees [Beveiligde toegang tot een sleutelkluis](../key-vault/key-vault-secure-your-key-vault.md) voor meer informatie.

## <a name="1-set-up-azure-key-vault"></a>1. Azure Key Vault instellen

U een sleutelkluis en -sleutel instellen voordat u de cache maakt, of dit doen als onderdeel van het maken van cache. Zorg ervoor dat deze resources voldoen aan de [hierboven](#understand-key-vault-and-key-requirements)beschreven vereisten .

Bij het maken van cache moet u een kluis, sleutel en sleutelversie opgeven die u wilt gebruiken voor de versleuteling van de cache.

Lees de [Azure Key Vault-documentatie](../key-vault/key-vault-overview.md) voor meer informatie.

> [!NOTE]
> De Azure Key Vault moet hetzelfde abonnement gebruiken en zich in dezelfde regio bevinden als de Azure HPC-cache. Gebruik een van de ondersteunde regio's die aan het begin van dit artikel worden vermeld.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. De cache maken met door de klant beheerde sleutels ingeschakeld

U moet de bron van de versleutelingssleutel opgeven wanneer u uw Azure HPC-cache maakt. Volg de instructies in [Een Azure HPC-cache maken](hpc-cache-create.md)en geef de sleutelkluis en -sleutel op op de pagina **Schijfversleutelingssleutels.** U een nieuwe sleutelkluis en sleutel maken tijdens het maken van cache.

> [!TIP]
> Als de pagina **Schijfversleutelingssleutels** niet wordt weergegeven, controleert u of uw cache zich in een van de ondersteunde regio's bevindt.

De gebruiker die de cache maakt, moet bevoegdheden hebben die gelijk zijn aan de rol van de [sleutelkluisbijdrage](../role-based-access-control/built-in-roles.md#key-vault-contributor) of hoger.

1. Klik op de knop om privébeheersleutels in te schakelen. Nadat u deze instelling hebt gewijzigd, worden de instellingen voor de sleutelkluis weergegeven.

1. Klik **op Een sleutelkluis selecteren** om de pagina sleutelselectie te openen. Kies of maak de sleutelkluis en sleutel voor het versleutelen van gegevens op de schijven van deze cache.

   Als uw Azure Key Vault niet in de lijst wordt weergegeven, controleert u de volgende vereisten:

   * Zit de cache in hetzelfde abonnement als de sleutelkluis?
   * Is de cache in dezelfde regio als de sleutelkluis?
   * Is er netwerkconnectiviteit tussen de Azure-portal en de sleutelkluis?

1. Nadat u een kluis hebt geselecteerd, selecteert u de afzonderlijke sleutel in de beschikbare opties of maakt u een nieuwe sleutel. De sleutel moet een 2048-bit RSA-toets zijn.

1. Geef de versie op voor de geselecteerde sleutel. Meer informatie over versiebeheer in de [Azure Key Vault-documentatie](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning).

Ga verder met de rest van de specificaties en maak de cache zoals beschreven in [Een Azure HPC-cache maken.](hpc-cache-create.md)

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Azure Key Vault-versleuteling autoriseren vanuit de cache
<!-- header is linked from create article, update if changed -->

Na enkele minuten wordt de nieuwe Azure HPC-cache weergegeven in uw Azure-portal. Ga naar de **overzichtspagina** om deze te autoriseren om toegang te krijgen tot uw Azure Key Vault en sleutelversleuteling van klanten in te schakelen. (De cache kan worden weergegeven in de lijst met resources voordat de berichten 'implementatie onderweg' worden gewist.)

Dit proces in twee stappen is nodig omdat het exemplaar Azure HPC-cache een identiteit nodig heeft om door te geven aan de Azure Key Vault voor autorisatie. De cache-identiteit bestaat pas nadat de eerste creatiestappen zijn voltooid.

> [!NOTE]
> U moet versleuteling binnen 90 minuten na het maken van de cache autoriseren. Als u deze stap niet voltooit, wordt er een time-out en fail-out van de cache. Een mislukte cache moet opnieuw worden gemaakt, deze kan niet worden opgelost.

De cache toont de status **Wachten op sleutel**. Klik op de knop **Versleuteling inschakelen** boven aan de pagina om de cache te autoriseren om toegang te krijgen tot de opgegeven sleutelkluis.

![schermafbeelding van de overzichtspagina voor cache in portal, met markeringen op de knop Versleuteling inschakelen (bovenste rij) en Status: Wachten op sleutel](media/waiting-for-key.png)

Klik **op Versleuteling inschakelen** en klik vervolgens op de knop **Ja** om de cache te autoriseren om de versleutelingssleutel te gebruiken. Deze actie maakt ook soft-delete en purge bescherming (indien niet al ingeschakeld) op de sleutel kluis.

![schermafbeelding van de overzichtspagina van de cache in portal, met een bannerbericht bovenaan dat de gebruiker vraagt om versleuteling in te schakelen door op ja te klikken](media/enable-keyvault.png)

Nadat de cache toegang tot de sleutelkluis heeft gevraagd, kan deze de schijven maken en versleutelen die gegevens in de cache opslaan.

Nadat u versleuteling autorisert, gaat Azure HPC Cache nog enkele minuten door om de versleutelde schijven en gerelateerde infrastructuur te maken.

## <a name="update-key-settings"></a>Toetsinstellingen bijwerken

U de sleutelkluis, sleutel of sleutelversie voor uw cache wijzigen vanuit de Azure-portal. Klik op de koppeling **Versleutelingsinstellingen** in de cache om de pagina **Instellingen voor klanttoetsen** te openen. (U een cache tussen door de klant beheerde sleutels en door het systeem beheerde sleutels niet wijzigen.)

![schermafbeelding van de pagina 'Instellingen voor klantgegevens' bereikt door te klikken op Instellingen > Versleuteling vanaf de cachepagina in de Azure-portal](media/change-key-click.png)

Klik **op** de koppeling Sleutel wijzigen en klik vervolgens op **De sleutelkluis, -sleutel of -versie wijzigen** om de sleutelkiezer te openen.

![schermafbeelding van de pagina 'Sleutelkluis selecteren uit Azure Key Vault' met drie vervolgkeuzepunten om sleutelkluis, sleutel en versie te kiezen](media/select-new-key.png)

Sleutelkluizen in hetzelfde abonnement en dezelfde regio als deze cache worden weergegeven in de lijst.

Nadat u de nieuwe versleutelingssleutelwaarden hebt gekozen, klikt u op **Selecteren**. Er verschijnt een bevestigingspagina met de nieuwe waarden. Klik **op Opslaan** om de selectie af te ronden.

![schermafbeelding van bevestigingspagina met knop Opslaan linksboven](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Lees meer over door klanten beheerde sleutels in Azure

In deze artikelen wordt meer uitgelegd over het gebruik van Azure Key Vault en door de klant beheerde sleutels om gegevens in Azure te versleutelen:

* [Overzicht van Azure-opslagversleuteling](../storage/common/storage-service-encryption.md)
* [Schijfversleuteling met door de klant beheerde sleutels](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) - Documentatie voor het gebruik van Azure Key Vault en beheerde schijven, wat vergelijkbaar is met het proces dat wordt gebruikt met Azure HPC-cache

## <a name="next-steps"></a>Volgende stappen

Nadat u de Azure HPC-cache en geautoriseerde Key Vault-gebaseerde versleuteling hebt gemaakt, u uw cache blijven instellen door deze toegang te geven tot uw gegevensbronnen.

* [Opslagdoelen toevoegen](hpc-cache-add-storage.md)
