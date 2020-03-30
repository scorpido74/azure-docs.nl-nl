---
title: 'Snelstart: Nieuwe beleidstoewijzing met portal'
description: In deze snelstart gebruikt u Azure-portal om een Azure-beleidstoewijzing te maken om niet-compatibele resources te identificeren.
ms.date: 03/24/2020
ms.topic: quickstart
ms.openlocfilehash: 3a514478f3cefa49c37c3431a80b578dff480584
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80240014"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>Snelstart: een beleidstoewijzing maken om niet-compatibele resources te identificeren

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources.
In deze quickstart gaat u een beleidstoewijzing maken voor het identificeren van virtuele machines die geen beheerde schijven gebruiken.

Als u dit proces helemaal hebt doorlopen, kunt u virtuele machines identificeren die geen beheerde schijven gebruiken. Ze zijn _niet-compatibel_ met de beleidstoewijzing.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze snelstart maakt u een beleidstoewijzing en wijst u de beleidsdefinitie _Virtuele machines zonder beheerde schijven controleren_ toe.

1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="Zoeken naar beleid in alle services" border="false":::

1. Selecteer **Toewijzingen** in het linkerdeelvenster van de Azure Policy-pagina. Een toewijzing is een beleid dat is toegewezen om te worden toegepast binnen een bepaald bereik.

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="Pagina Toewijzingen selecteren op de pagina Beleidsoverzicht" border="false":::

1. Selecteer **Beleid toewijzen** boven in de pagina **Beleidstoewijzingen**.

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="Een beleidsdefinitie toewijzen op de pagina Toewijzingen" border="false":::

1. Selecteer op de pagina **Beleid toewijzen** het **bereik** door te klikken op het beletselteken en een beheergroep of abonnement te selecteren. U kunt ook een resourcegroep selecteren. Het bereik bepaalt op welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Klik vervolgens op **Selecteren** aan de onderkant van de pagina **Bereik**.

   In dit voorbeeld wordt het abonnement **Contoso** gebruikt. U hebt waarschijnlijk een ander abonnement.

1. Resources kunnen worden uitgesloten op basis van het **bereik**. **Uitsluitingen** starten op één niveau lager dan het niveau van het **bereik**. **Uitsluitingen** zijn optioneel, dus laat dit veld nu nog leeg.

1. Selecteer het weglatingsteken **Beleidsdefinitie** om de lijst van beschikbare definities te openen. Azure Policy wordt geleverd met ingebouwde beleidsdefinities die u kunt gebruiken. Er zijn allerlei definities beschikbaar, zoals:

   - Tag en waarde afdwingen
   - Tag en waarde toepassen
   - Een tag overnemen van de resourcegroep als deze ontbreekt

   Zie [Azure Policy-voorbeelden](./samples/index.md)voor een gedeeltelijke lijst met beschikbare ingebouwde beleidsregels .

1. Doorzoek uw beleidsdefinities en zoek de definitie _Controleren van virtuele machines die geen beheerde schijven gebruiken_. Klik op dit beleid en vervolgens op **Selecteren**.

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="De juiste beleidsdefinitie vinden" border="false":::

1. De **Toewijzingsnaam** wordt automatisch ingevuld met de naam van het beleid dat u hebt geselecteerd, maar u kunt dit wijzigen. In dit geval gebruiken we _Controleren van virtuele machines die geen beheerde schijven gebruiken_. U kunt ook een optionele **Beschrijving** opgeven. De beschrijving bevat details over deze beleidstoewijzing.
   **Toegewezen door** wordt automatisch gevuld op basis van de persoon die is aangemeld. Dit veld is optioneel, dus u kunt aangepaste waarden invoeren.

1. Laat **Beheerde identiteit maken** uitgeschakeld. Deze optie _moet_ worden ingeschakeld wanneer het beleid of initiatief een beleidsregel bevat met het effect [deployIfNotExists](./concepts/effects.md#deployifnotexists). Omdat het beleid dat voor deze zelfstudie wordt gebruikt deze regel niet bevat, laat u deze optie uitgeschakeld. Zie voor meer informatie [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) en [hoe herstelbeveiliging werkt](./how-to/remediate-resources.md#how-remediation-security-works).

1. Klik op **Toewijzen**.

U bent nu klaar om niet-conforme resources te identificeren om inzicht te krijgen in de nalevingsstatus van uw omgeving.

## <a name="identify-non-compliant-resources"></a>Niet-compatibele resources identificeren

Selecteer **Naleving** links op de pagina. Zoek dan de beleidstoewijzing **Virtuele machines zonder beheerde schijven controleren** die u hebt gemaakt.

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="Nalevingsgegevens op de pagina Naleving van beleid" border="false":::

Als er bestaande resources zijn die niet conform deze nieuwe toewijzing zijn, worden deze weergegeven bij **Niet-conforme resources**.

Als een voorwaarde wordt geëvalueerd ten opzichte van uw bestaande resources en deze waar blijkt te zijn, worden deze resources gemarkeerd als niet-compatibel met het beleid. In de volgende tabel ziet u hoe verschillende beleidsacties werken met de evaluatie van voorwaarden voor de resulterende nalevingsstatus. Hoewel u de evaluatielogica niet ziet in de Azure-portal, worden de resultaten van de nalevingsstatus weergegeven. Het resultaat voor de nalevingsstatus is compatibel of niet-compatibel.

| **Resourcestatus** | **Effect** | **Beleidsevaluatie** | **Nalevingsstatus** |
| --- | --- | --- | --- |
| Bestaat | Weigeren, Controleren, Toevoegen\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Niet-compatibel |
| Bestaat | Weigeren, Controleren, Toevoegen\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Compatibel |
| Nieuw | Controleren, AuditIfNotExist\* | True | Niet-compatibel |
| Nieuw | Controleren, AuditIfNotExist\* | False | Compatibel |

\*Voor de acties Toevoegen, DeployIfNotExist en AuditIfNotExist moet de IF-instructie TRUE zijn.
De acties vereisen ook dat de bestaansvoorwaarde FALSE is om niet-compatibel te zijn. Indien TRUE, activeert de IF-voorwaarde de evaluatie van de bestaansvoorwaarde voor de gerelateerde resources.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gemaakte toewijzing wilt verwijderen, volgt u deze stappen:

1. Selecteer **Naleving** (of **Toewijzingen**) aan de linkerkant van de pagina Azure Policy en zoek de beleidstoewijzing **Controleren van virtuele machines die geen beheerde schijven gebruiken** die u hebt gemaakt.

1. Klik met de rechtermuisknop op **de beleidstoewijzing voor beheerde schijven en** selecteer Toewijzing **verwijderen**.

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="Een toewijzing verwijderen van de pagina Naleving" border="false":::

## <a name="next-steps"></a>Volgende stappen

In deze snelstart wijst u een beleidsdefinitie toe aan een bereik en evalueert u het rapport voor naleving.
De beleidsdefinitie controleert of alle resources in het bereik conform zijn en identificeert welke dit niet zijn.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)