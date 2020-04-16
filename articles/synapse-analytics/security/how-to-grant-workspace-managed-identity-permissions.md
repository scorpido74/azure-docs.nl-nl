---
title: Machtigingen verlenen voor beheerde identiteit in Azure Synapse-werkruimte
description: Een artikel waarin wordt uitgelegd hoe u machtigingen voor beheerde identiteit configureert in azure Synapse-werkruimte.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428015"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Machtigingen verlenen aan door de werkruimte beheerde identiteit (voorbeeld)

In dit artikel leert u hoe u machtigingen verlenen voor de beheerde identiteit in Azure synapsenwerkruimte. Machtigingen bieden op hun beurt toegang tot SQL-groepen in de werkruimte en het ADLS gen2-opslagaccount via de Azure-portal.

>[!NOTE]
>Deze door de werkruimte beheerde identiteit wordt via de rest van dit document als beheerde identiteit aangeduid.

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>De beheerde identiteitsmachtigingen toekennen aan de SQL-groep

De beheerde identiteit verleent machtigingen aan de SQL-groepen in de werkruimte. Met machtigingen u pijplijnen orkestreren die SQL-poolgerelateerde activiteiten uitvoeren. Wanneer u een Azure Synapse-werkruimte maakt met Azure-portal, u de machtigingen voor beheerde identiteitsbeheer op SQL-groepen verlenen.

Selecteer **Beveiliging + netwerken** wanneer u uw Azure Synapse-werkruimte maakt. Selecteer **vervolgens Grant CONTROL in de beheerde identiteit van de werkruimte in SQL-groepen.**

![CONTROL-machtigingen voor SQL-groepen](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>De beheerde identiteitsmachtigingen toekennen aan adls gen2-opslagaccount

Een ADLS gen2-opslagaccount is vereist om een Azure Synapse-werkruimte te maken. Om Spark-pools in Azure Synapse-werkruimte succesvol te starten, heeft de azure Synapse beheerde identiteit de rol *Storage Blob Data Contributor* voor dit opslagaccount nodig. Ook pijplijnorkestratie in Azure Synapse profiteert van deze rol.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Machtigingen verlenen aan beheerde identiteit tijdens het maken van werkruimtes

Azure Synapse probeert de rol Storage Blob Data Contributor toe te kennen aan de beheerde identiteit nadat u de Azure Synapse-werkruimte hebt gemaakt met Azure-portal. U geeft de gegevens van het ADLS gen2-opslagaccount op het tabblad **Basics.**

![Tabblad Basisbeginselen in de werkruimtestroom](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Kies het ADLS gen2-opslagaccount en bestandssysteem in **accountnaam** en **bestandssysteemnaam**.

![Het verstrekken van een ADLS gen2-opslagaccountgegevens](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Als de maker van de werkruimte ook **eigenaar** is van het ADLS gen2-opslagaccount, wijst Azure Synapse de rol *Opslagblob-gegevensbijdrage toe* aan de beheerde identiteit. Je ziet het volgende bericht onder de opslagaccountgegevens die je hebt ingevoerd.

![Toewijzing voor toewijzing voor winnende opslagblobgegevensbijdrager](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Als de maker van de werkruimte niet de eigenaar is van het ADLS gen2-opslagaccount, wijst Azure Synapse de rol *opslagblobgegevensbijdrager* niet toe aan de beheerde identiteit. In het bericht onder de opslagaccountgegevens wordt de maker van de werkruimte ervan op de hoogte gesteld dat deze niet voldoende machtigingen heeft om de rol *van opslagblobgegevensbijdrager* toe te kennen aan de beheerde identiteit.

![Toewijzing voor niet-mislukte opslagblobgegevensbijdrager](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Zoals in het bericht staat, u geen Spark-groepen maken, tenzij de *opslagblobgegevensbijdrager* is toegewezen aan de beheerde identiteit.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Machtigingen verlenen aan beheerde identiteit na het maken van werkruimtes

Als u tijdens het maken van de werkruimte de *bijdrage opslagblobgegevens* niet aan de beheerde identiteit toewijst, wijst de **eigenaar** van het ADLS gen2-opslagaccount die rol handmatig toe aan de identiteit. De volgende stappen helpen u om handmatige toewijzing uit te voeren.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Stap 1: Navigeren naar het ADLS gen2-opslagaccount in Azure-portal

Open in Azure-portal het ADLS gen2-opslagaccount en selecteer **Overzicht** in de linkernavigatie. U hoeft alleen de rol van de *opslagblobgegevensbijdrager* toe te wijzen op container- of bestandssysteemniveau. Selecteer **Containers**.  
![Overzicht van ADLS gen2-opslagaccount](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Stap 2: De container selecteren

De beheerde identiteit moet toegang hebben tot de container (bestandssysteem) die is verstrekt toen de werkruimte werd gemaakt. U deze container of bestandssysteem vinden in azure portal. Open de Azure Synapse-werkruimte in Azure-portal en selecteer het tabblad **Overzicht** op de linkernavigatie.
![ADLS gen2-opslagaccountcontainer](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Selecteer dezelfde container of bestandssysteem om de rol *Opslagblob-gegevensbijdrage ropdragers* toe te kennen aan de beheerde identiteit.
![ADLS gen2-opslagaccountcontainerselectie](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Stap 3: Navigeren naar toegangscontrole

Selecteer **Toegangsbeheer (IAM)**.

![Toegangscontrole(IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Stap 4: Een nieuwe roltoewijzing toevoegen

Selecteer **+ Toevoegen**.

![Nieuwe roltoewijzing toevoegen](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>Stap 5: Selecteer de Rol RBAC

Selecteer de rol **Opslagblobgegevensbijdrager.**

![De RBAC-rol selecteren](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Stap 6: De azure AD-beveiligingsprincipal selecteren

Selecteer **Azure AD-gebruiker, -groep of serviceprincipal** in de **vervolgkeuzelijst Toegang toewijzen.**

![Selecteer AAD-beveiligingsprincipal](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Stap 7: Zoeken naar de beheerde identiteit

De naam van de beheerde identiteit is ook de naam van de werkruimte. Zoek naar uw beheerde identiteit door de naam van uw Azure Synapse-werkruimte in Select in **te**voeren. U moet de beheerde identiteit weergegeven.

![De beheerde identiteit zoeken](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Stap 8: De beheerde identiteit selecteren

Selecteer de beheerde identiteit aan de **geselecteerde leden**. Selecteer **Opslaan** om de roltoewijzing toe te voegen.

![De beheerde identiteit selecteren](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Stap 9: controleren of de rol Opslagblob-gegevensbijdrage ropdragers is toegewezen aan de beheerde identiteit

Selecteer **Toegangsbeheer(IAM)** en selecteer **vervolgens Roltoewijzingen**.

![Roltoewijzing verifiëren](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

U moet uw beheerde identiteit zien onder de sectie **Opslagblobgegevensbijdrager** met de rol *Opslagblobgegevensbijdrager* die eraan is toegewezen. 
![ADLS gen2-opslagaccountcontainerselectie](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [de beheerde identiteit van Workspace](./synapse-workspace-managed-identity.md)
