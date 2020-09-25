---
title: Verbinding maken met een Synapse-werk ruimte met behulp van persoonlijke koppelingen
description: In dit artikel leert u hoe u verbinding kunt maken met uw Azure Synapse-werk ruimte met behulp van persoonlijke koppelingen
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ea791e4fc1031c0a5c041119c409f8623ce7aee9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91260373"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Verbinding maken met uw Azure Synapse-werk ruimte met behulp van persoonlijke koppelingen (preview-versie)

In dit artikel leert u hoe u een persoonlijk eind punt maakt in de Azure Synapse-werk ruimte. Zie [persoonlijke koppelingen en persoonlijke eind punten](https://docs.microsoft.com/azure/private-link/) voor meer informatie.

## <a name="step-1-register-network-resource-provider"></a>Stap 1: netwerk resource provider registreren

Als u dit nog niet hebt gedaan, registreert u de netwerk resource provider. Als u een resourceprovider registreert, wordt uw abonnement zo geconfigureerd dat dit kan worden gebruikt met de resourceprovider. Kies *Microsoft.Network* in de lijst met resourceproviders tijdens het [registreren](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types). Als de netwerk resource provider al is geregistreerd, gaat u verder met stap 2.


## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>Stap 2: Open de Azure Synapse-werk ruimte in Azure Portal

Selecteer **particuliere endpoint-verbinding** onder **beveiliging** en selecteer vervolgens **+ persoonlijk eind punt**.
![Open de Azure Synapse-werk ruimte in Azure Portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>Stap 3: Selecteer uw abonnement en de regio Details

Kies op het tabblad **basis beginselen** in het venster **een persoonlijk eind punt maken** de optie uw **abonnement** en **resource groep**. Geef een **naam** op voor het persoonlijke eind punt dat u wilt maken. Selecteer de **regio** waar u het persoonlijke eind punt wilt maken.

Privé-eind punten worden in een subnet gemaakt. Het abonnement, de resource groep en de geselecteerde regio filteren de subnetten met het persoonlijke eind punt. Selecteer **volgende: Resource >** wanneer dit is voltooid.
![Abonnement en regio Details selecteren 1](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>Stap 4: de details van uw Azure Synapse-werk ruimte selecteren

Selecteer **verbinding maken met een Azure-resource in mijn Directory** op het tabblad **resource** . Selecteer het **abonnement** met de Azure Synapse-werk ruimte. Het **resource type** voor het maken van persoonlijke eind punten in een Azure Synapse-werk ruimte is *micro soft. Synapse/werk ruimten*.

Selecteer uw Azure Synapse-werk ruimte als de **resource**. Elke Azure Synapse-werk ruimte heeft drie **doel-subbronnen** waarmee u een persoonlijk eind punt kunt maken: SQL, SqlOnDemand en dev.

Selecteer **volgende: configuratie>** om door te gaan naar het volgende deel van de installatie.
![Abonnement en regio-informatie selecteren 2](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Selecteer op het tabblad **configuratie** het **virtuele netwerk** en het **subnet** waarin het persoonlijke eind punt moet worden gemaakt. U moet ook een DNS-record maken die is toegewezen aan het persoonlijke eind punt.

Selecteer **Ja** om te **integreren met een particuliere DNS-zone** om uw persoonlijke eind punt te integreren met een privé-DNS-zone. Als u geen privé-DNS-zone hebt die is gekoppeld aan uw Microsoft Azure Virtual Network, wordt er een nieuwe privé-DNS-zone gemaakt. Selecteer **controleren + maken** wanneer u klaar bent.

![Selecteer abonnement en regio Details 3](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Wanneer de implementatie is voltooid, opent u de Azure Synapse-werk ruimte in Azure Portal en selecteert u verbindingen met een **privé-eind punt**. Het nieuwe persoonlijke eind punt en de verbindings naam van het persoonlijke eind punt die aan het persoonlijke eind punt zijn gekoppeld, worden weer gegeven.

![Details van abonnement en regio selecteren 4](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Managed workspace Virtual Network](./synapse-workspace-managed-vnet.md)

Meer informatie over [beheerde privé-eindpunten](./synapse-workspace-managed-private-endpoints.md)

[Create Managed private endpoint to your data sources](./how-to-create-managed-private-endpoints.md) (Beheerde privé-eindpunten naar uw gegevensbronnen maken)
