---
title: Toegang tot azure Data Box Gateway-apparaat, stroom en connectiviteitsmodus
description: Beschrijft hoe u de toegangs-, stroom- en connectiviteitsmodus beheert voor het Azure Data Box Gateway-apparaat waarmee gegevens naar Azure kunnen worden overgedragen
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: e4d85bd460c39964c9f42ac946e3522f5f129c1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474438"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>De toegangs-, stroom- en connectiviteitsmodus voor uw Azure Data Box-gateway beheren

In dit artikel wordt beschreven hoe u de toegangs-, stroom- en connectiviteitsmodus voor uw Azure Data Box Gateway beheert. Deze bewerkingen worden uitgevoerd via de lokale web-gebruikersinterface of de Azure-portal.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Apparaattoegang beheren
> * Connectiviteitsmodus beheren
> * Stroom beheren

## <a name="manage-device-access"></a>Apparaattoegang beheren

De toegang tot uw Data Box Gateway-apparaat wordt geregeld door het gebruik van een apparaatwachtwoord. U het wachtwoord wijzigen via de lokale webgebruikersinterface. U het apparaatwachtwoord ook opnieuw instellen in de Azure-portal.

### <a name="change-device-password"></a>Wachtwoord voor apparaat wijzigen

Volg deze stappen in de lokale gebruikersinterface om het apparaatwachtwoord te wijzigen.

1. Ga in de lokale webgebruikersinterface naar De wijziging **van het > wachtwoord .**
2. Voer het huidige wachtwoord in en vervolgens het nieuwe wachtwoord. Het opgegeven wachtwoord moet tussen de 8 en 16 tekens liggen. Het wachtwoord moet 3 van de volgende tekens bevatten: hoofdletters, kleine letters, numerieke tekens en speciale tekens. Bevestig het nieuwe wachtwoord.

    ![Wachtwoord wijzigen](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Klik **op Wachtwoord wijzigen**.
 
### <a name="reset-device-password"></a>Apparaatwachtwoord opnieuw instellen

De resetworkflow vereist niet dat de gebruiker het oude wachtwoord terugroept en is handig wanneer het wachtwoord verloren gaat. Deze werkstroom wordt uitgevoerd in de Azure-portal.

1. Ga in de Azure-portal naar **Overzicht > Beheer-beheerwachtwoord opnieuw instellen**.

    ![Wachtwoord opnieuw instellen](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Voer het nieuwe wachtwoord in en bevestig het. Het opgegeven wachtwoord moet tussen de 8 en 16 tekens liggen. Het wachtwoord moet 3 van de volgende tekens bevatten: hoofdletters, kleine letters, numerieke tekens en speciale tekens. Klik **op Opnieuw instellen**.

    ![Wachtwoord opnieuw instellen](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Toegang tot de bedrijfsresources

Als u uw Data Box Edge/Data Box Gateway, IoT Hub en Azure Storage-bron wilt maken, hebt u machtigingen nodig als inzender of hoger op resourcegroepniveau. U moet ook de bijbehorende resourceproviders registreren. Voor bewerkingen die activeringssleutel en referenties omvatten, zijn ook machtigingen voor Azure Active Directory Graph API vereist. Deze worden beschreven in de volgende secties.

### <a name="manage-microsoft-graph-api-permissions"></a>Machtigingen voor Microsoft Graph API beheren

Wanneer u de activeringssleutel voor het Data Box Edge-apparaat genereert of bewerkingen uitvoert waarvoor referenties nodig zijn, hebt u machtigingen nodig voor de Microsoft Graph API. De bewerkingen die referenties nodig hebben, kunnen zijn:

-  Een share maken met een gekoppeld opslagaccount.
-  Een gebruiker maken die toegang heeft tot de shares op het apparaat.

U moet `User` toegang hebben tot active directory-tenant, `Read all directory objects`omdat u dat moet kunnen. U geen gastgebruiker zijn omdat deze geen `Read all directory objects`machtigingen heeft voor. Als u een gast bent, kunnen bewerkingen als het genereren van een activeringssleutel, het maken van een share op uw Data Box Edge-apparaat of het maken van een gebruiker, niet worden uitgevoerd.

Zie [Microsoft Graph-machtigingenverwijzing voor](https://docs.microsoft.com/graph/permissions-reference)meer informatie over het bieden van toegang aan gebruikers tot microsoft graph API.

### <a name="register-resource-providers"></a>Resourceproviders registreren

Als u een resource wilt inrichten in Azure (in het Azure Resource Manager-model), hebt u een resourceprovider nodig die de creatie van die bron ondersteunt. Als u bijvoorbeeld een virtuele machine wilt inrichten, moet u een resourceprovider 'Microsoft.Compute' beschikbaar hebben in het abonnement.
 
Resourceproviders worden geregistreerd op het niveau van het abonnement. Nieuwe Azure-abonnementen worden standaard vooraf geregistreerd bij een lijst met veelgebruikte resourceproviders. De resourceprovider voor 'Microsoft.DataBoxEdge' is niet opgenomen in deze lijst.

U hoeft geen toegangsrechten toe te kennen tot het abonnementsniveau voor gebruikers om bronnen zoals 'Microsoft.DataBoxEdge' te kunnen maken binnen hun brongroepen waarop ze eigenaarrechten hebben, zolang de resourceproviders voor deze bronnen al Geregistreerd.

Voordat u probeert een resource te maken, moet u ervoor zorgen dat de resourceprovider is geregistreerd in het abonnement. Als de resourceprovider niet is geregistreerd, moet u ervoor zorgen dat de gebruiker die de nieuwe resource maakt, voldoende rechten heeft om de vereiste resourceprovider op abonnementsniveau te registreren. Als u dit niet zo goed hebt gedaan, ziet u de volgende fout:

*De \<naam van het abonnementAbonnement> heeft geen machtigingen om de bronprovider(s) te registreren: Microsoft.DataBoxEdge.*


Voer de volgende opdracht uit om een lijst met geregistreerde resourceproviders in het huidige abonnement te krijgen:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Voor het Apparaat `Microsoft.DataBoxEdge` Van de Rand van de Gegevensdoos, zou moeten worden geregistreerd. Als `Microsoft.DataBoxEdge`u zich wilt registreren, moet de abonnementsbeheerder de volgende opdracht uitvoeren:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Zie Fouten oplossen voor de registratie [van resources voor het](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors)registreren van een resourceprovider voor meer informatie over het registreren van een resourceprovider.

## <a name="manage-connectivity-mode"></a>Connectiviteitsmodus beheren

Naast de standaard normale modus kan uw apparaat ook in gedeeltelijk losgekoppelde of losgekoppelde modus worden uitgevoerd. Elk van deze modi wordt hieronder beschreven:

- **Gedeeltelijk losgekoppeld** – In deze modus kan het apparaat geen gegevens uploaden naar de shares, maar ze kunnen worden beheerd via de Azure-portal.

    Deze modus wordt meestal gebruikt wanneer op een satellietnetwerk met datadata en het doel is om het verbruik van netwerkbandbreedte te minimaliseren. Minimaal netwerkverbruik kan nog steeds optreden voor apparaatbewakingsbewerkingen.

- **Losgekoppeld** : in deze modus wordt het apparaat volledig losgekoppeld van de cloud en worden zowel de clouduploads als de downloads uitgeschakeld. Het apparaat kan alleen worden beheerd via de lokale web-gebruikersinterface.

    Deze modus wordt meestal gebruikt wanneer u uw apparaat offline wilt halen.

Voer de volgende stappen uit om de apparaatmodus te wijzigen:

1. Ga in de lokale webgebruikersinterface van uw apparaat naar **Configuratie > Cloud-instellingen.**
2. Schakel het **uploaden en downloaden van**de cloud uit.
3. Als u het apparaat in de gedeeltelijk losgekoppelde modus wilt uitvoeren, schakelt u **Azure-portalbeheer**in.

    ![Connectiviteitsmodus](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Als u het apparaat in de losgekoppelde modus wilt uitvoeren, schakelt u **Azure-portalbeheer**uit. Nu kan het apparaat alleen worden beheerd via de lokale web-gebruikersinterface.

    ![Connectiviteitsmodus](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Stroom beheren

U uw virtuele apparaat afsluiten of opnieuw opstarten met behulp van de lokale web-gebruikersinterface. We raden u aan de shares vóór de herstart offline te zetten op de host en vervolgens op het apparaat. Deze actie minimaliseert de kans op gegevensbeschadiging.

1. Ga in de lokale webgebruikersinterface naar **De instellingen voor onderhoud > energie**.
2. Klik op **Afsluiten** of **Opnieuw starten,** afhankelijk van wat u van plan bent te doen.

    ![Energie-instellingen](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Klik op **Ja** om door te gaan wanneer u om bevestiging wordt gevraagd.

> [!NOTE]
> Als u het virtuele apparaat uitschakelt, moet u het apparaat starten via het hypervisorbeheer.
