---
title: Azure-roltoewijzingen weer geven met behulp van de Azure Portal-Azure RBAC
description: Meer informatie over hoe u kunt bepalen welke resources gebruikers, groepen, service-principals of beheerde identiteiten hebben toegang tot het gebruik van de Azure Portal en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/24/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f7d56ecc7fc6bd850fced33c2c1cf20902bb2df4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361836"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Azure-roltoewijzingen weer geven met behulp van de Azure Portal

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]In dit artikel wordt beschreven hoe u roltoewijzingen kunt weer geven met behulp van de Azure Portal.

> [!NOTE]
> Als uw organisatie uitbestede beheer functies heeft voor een service provider die gebruikmaakt van [Azure delegated resource management](../lighthouse/concepts/azure-delegated-resource-management.md), worden roltoewijzingen die door die service provider worden toegestaan, hier niet weer gegeven.

## <a name="list-role-assignments-for-a-user-or-group"></a>Roltoewijzingen voor een gebruiker of groep weer geven

De eenvoudigste manier om de rollen weer te geven die zijn toegewezen aan een gebruiker of groep in een abonnement is het gebruik van het deel venster **toewijzingen van Azure-functie** .

1. Selecteer in het Azure Portal **alle services** in het menu Azure Portal.

1. Selecteer **Azure Active Directory** en selecteer vervolgens **gebruikers** of **groepen**.

1. Klik op de gebruiker of groep waarvoor u de roltoewijzingen wilt weer geven.

1. Klik op **Azure-roltoewijzingen**.

    U ziet een lijst met de rollen die zijn toegewezen aan de geselecteerde gebruiker of groep in verschillende bereiken, zoals de beheer groep, het abonnement, de resource groep of de resource. Deze lijst bevat alle roltoewijzingen waarvoor u lees machtigingen hebt.

    ![Roltoewijzingen voor een gebruiker](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. Als u het abonnement wilt wijzigen, klikt u op de lijst **abonnementen** .

## <a name="list-owners-of-a-subscription"></a>Eigen aren van een abonnement weer geven

Gebruikers aan wie de rol van [eigenaar](built-in-roles.md#owner) voor een abonnement is toegewezen, kunnen alles in het abonnement beheren. Volg deze stappen om de eigen aars van een abonnement weer te geven.

1. Klik in de Azure-portal op de optie **Alle services** en vervolgens op **Abonnementen**.

1. Klik op het abonnement waarvan u de eigen aren wilt vermelden.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen voor dit abonnement weer te geven.

1. Ga naar de sectie **eigen aren** om alle gebruikers weer te geven aan wie de rol van eigenaar voor dit abonnement is toegewezen.

   ![Toegangs beheer voor abonnementen-tabblad roltoewijzingen](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Roltoewijzingen in een bereik weer geven

1. Klik in de Azure Portal op **alle services** en selecteer vervolgens het bereik. U kunt bijvoorbeeld **beheer groepen**, **abonnementen**, **resource groepen**of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op **het tabblad roltoewijzingen om** alle roltoewijzingen in dit bereik weer te geven.

   ![Toegangs beheer-tabblad roltoewijzingen](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Op het tabblad roltoewijzingen kunt u zien wie toegang heeft tot dit bereik. U ziet dat voor sommige rollen het bereik is ingesteld op **Deze resource**, terwijl andere zijn **(Overgenomen)** uit een ander bereik. De toegang wordt specifiek aan deze resource toegewezen of overgenomen van een toewijzing aan het bovenliggende bereik.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Roltoewijzingen voor een gebruiker in een bereik weer geven

Als u de toegang wilt weer geven voor een gebruiker, groep, Service-Principal of beheerde identiteit, vermeldt u hun roltoewijzingen. Volg deze stappen om de roltoewijzingen voor één gebruiker, groep, Service-Principal of beheerde identiteit in een bepaald bereik weer te geven.

1. Klik in de Azure Portal op **alle services** en selecteer vervolgens het bereik. U kunt bijvoorbeeld **beheer groepen**, **abonnementen**, **resource groepen**of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op het tabblad **Toegang controleren**.

    ![Toegangsbeheer - Tabblad Toegang controleren](./media/role-assignments-list-portal/access-control-check-access.png)

1. Selecteer in de lijst **Zoeken** het type beveiligings-principal waarvoor u de toegang wilt controleren.

1. Voer in het zoekvak een tekenreeks in om de map te doorzoeken op weergavenamen, e-mailadressen of object-id's.

    ![De toegangsselectielijst controleren](./media/role-assignments-list-portal/check-access-select.png)

1. Klik op de beveiligings-principal om het deelvenster **Toewijzingen** te openen.

    ![Deelvenster Toewijzingen](./media/role-assignments-list-portal/check-access-assignments.png)

    In dit deelvenster ziet u de rollen die zijn toegewezen aan de geselecteerde beveiligings-principal en het bereik. Als er een in dit bereik toewijzingen zijn geweigerd of zijn overgenomen, worden deze weergegeven.

## <a name="list-role-assignments-for-a-managed-identity"></a>Roltoewijzingen voor een beheerde identiteit weer geven

U kunt roltoewijzingen voor door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteiten in een bepaald bereik weer geven met behulp van de Blade **toegangs beheer (IAM)** zoals eerder beschreven. In deze sectie wordt beschreven hoe u roltoewijzingen voor alleen de beheerde identiteit kunt weer geven.

### <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

1. Open een door het systeem toegewezen beheerde identiteit in het Azure Portal.

1. Klik in het linkermenu op **identiteit**.

    ![Door het systeem toegewezen beheerde identiteit](./media/shared/identity-system-assigned.png)

1. Klik onder **machtigingen**op **Azure-roltoewijzingen**.

    U ziet een lijst met rollen die zijn toegewezen aan de geselecteerde door het systeem toegewezen beheerde identiteit in verschillende bereiken, zoals beheer groep, abonnement, resource groep of resource. Deze lijst bevat alle roltoewijzingen waarvoor u lees machtigingen hebt.

    ![Roltoewijzingen voor een door het systeem toegewezen beheerde identiteit](./media/shared/role-assignments-system-assigned.png)

1. Als u het abonnement wilt wijzigen, klikt u op de lijst met **abonnementen** .

### <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

1. Open een door de gebruiker toegewezen beheerde identiteit in het Azure Portal.

1. Klik op **Azure-roltoewijzingen**.

    U ziet een lijst met rollen die zijn toegewezen aan de geselecteerde door de gebruiker toegewezen beheerde identiteit in verschillende bereiken, zoals beheer groep, abonnement, resource groep of resource. Deze lijst bevat alle roltoewijzingen waarvoor u lees machtigingen hebt.

    ![Roltoewijzingen voor een door het systeem toegewezen beheerde identiteit](./media/shared/role-assignments-user-assigned.png)

1. Als u het abonnement wilt wijzigen, klikt u op de lijst met **abonnementen** .

## <a name="list-number-of-role-assignments"></a>Aantal roltoewijzingen weer geven

U kunt Maxi maal **2000** roltoewijzingen in elk abonnement hebben. Deze limiet omvat roltoewijzingen voor het abonnement, de resource groep en de resource scopes. Het tabblad roltoewijzingen bevat een grafiek **waarin het aantal** roltoewijzingen voor het huidige abonnement wordt vermeld, zodat u deze limiet kunt bijhouden.

![Toegangs beheer-aantal roltoewijzingen grafiek](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Als u het maximum aantal krijgt en u probeert meer roltoewijzingen toe te voegen, wordt er een waarschuwing weer gegeven in het deel venster **roltoewijzing toevoegen** . Zie [problemen met Azure RBAC oplossen](troubleshooting.md#azure-role-assignments-limit)voor manieren waarop u het aantal roltoewijzingen kunt verlagen.

![Toegangs beheer-waarschuwing functie toewijzing toevoegen](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments-preview"></a>Roltoewijzingen downloaden (preview-versie)

U kunt roltoewijzingen in een bereik downloaden in CSV-of JSON-indelingen. Dit kan handig zijn als u de lijst in een werk blad wilt inspecteren of een inventarisatie wilt uitvoeren wanneer u een abonnement migreert.

> [!IMPORTANT]
> Het downloaden van roltoewijzingen is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Bij het downloaden van roltoewijzingen moet u rekening gehouden met de volgende criteria:

- Als u geen machtigingen hebt voor het lezen van de Directory, zoals de rol van Directory lezers, zijn de kolommen DisplayName, SignInName en object type leeg.
- Roltoewijzingen waarvan de beveiligingsprincipal is verwijderd, worden niet opgenomen.
- De toegang tot klassieke beheerders wordt niet opgenomen.

Volg deze stappen om roltoewijzingen in een bereik te downloaden.

1. Klik in de Azure Portal op **alle services** en selecteer vervolgens het bereik waar u de roltoewijzingen wilt downloaden. U kunt bijvoorbeeld **beheer groepen**, **abonnementen**, **resource groepen**of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)** .

1. Klik op Roltoewijzingen **downloaden (preview)** om het deel venster roltoewijzingen downloaden te openen.

    ![Toegangs beheer-roltoewijzingen downloaden](./media/role-assignments-list-portal/download-role-assignments.png)

1. Gebruik de selectie vakjes om de roltoewijzingen te selecteren die u wilt toevoegen aan het gedownloade bestand.

    - **Overgenomen** : Neem overgenomen roltoewijzingen op voor het huidige bereik.
    - **Bij Huidig bereik** : roltoewijzingen voor de huidige scope bevatten.
    - **Onderliggende items** : roltoewijzingen op niveaus onder het huidige bereik toevoegen. Dit selectie vakje is uitgeschakeld voor het bereik van de beheer groep.

1. Selecteer de bestands indeling, die door komma's gescheiden waarden (CSV) of JavaScript Object Notation (JSON) kan zijn.

1. Geef de bestands naam op.

1. Klik op **Start** om het downloaden te starten.

    Hieronder ziet u voor beelden van de uitvoer voor elke bestands indeling.

    ![Roltoewijzingen als CSV-bestand downloaden](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![Roltoewijzingen als CSV-bestand downloaden](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal](role-assignments-portal.md)
- [Problemen met Azure RBAC oplossen](troubleshooting.md)
