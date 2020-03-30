---
title: Azure-informatiebeveiliging verbinden met Azure Sentinel
description: Meer informatie over het verbinden van Azure Information Protection-gegevens in Azure Sentinel.
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: 396fd7c4289c9d02d451b26f5fb6299acd31e2a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588549"
---
# <a name="connect-data-from-azure-information-protection"></a>Gegevens verbinden vanuit Azure Information Protection

> [!IMPORTANT]
> De Azure Information Protection-gegevensconnector in Azure Sentinel bevindt zich momenteel in een openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

U logboekregistratiegegevens van [Azure Information Protection](https://azure.microsoft.com/services/information-protection/) streamen naar Azure Sentinel door de Azure Information Protection-gegevensconnector te configureren. Azure Information Protection helpt u bij het beheren en beveiligen van uw gevoelige gegevens, of deze nu in de cloud of on-premises zijn opgeslagen.

Als [de centrale rapportage voor Azure Information Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) al is geconfigureerd, zodat logboekregistratiegegevens van deze service worden opgeslagen in dezelfde Log Analytics-werkruimte als u momenteel hebt geselecteerd voor Azure Sentinel, u de configuratie van deze gegevensconnector overslaan. De logboekinformatie van Azure Information Protection is al beschikbaar voor Azure Sentinel.

Als u echter informatie van Azure Information Protection naar een andere log-analysewerkruimte gaat dan de werkruimte die u momenteel hebt geselecteerd voor Azure Sentinel, voert u een van de volgende handelingen uit:

- Wijzig de werkruimte die is geselecteerd in Azure Sentinel.

- Wijzig de werkruimte voor Azure Information Protection, wat u doen door deze gegevensconnector te configureren.
    
    Als u de werkruimte wijzigt, worden nieuwe rapportagegegevens voor Azure Information Protection nu opgeslagen in de werkruimte die u gebruikt voor Azure Sentinel en zijn historische gegevens niet beschikbaar voor Azure Sentinel. Als de vorige werkruimte is geconfigureerd voor aangepaste query's, waarschuwingen of REST-API's, moeten deze bovendien opnieuw worden geconfigureerd voor de Azure Sentinel-werkruimte als u ze wilt blijven gebruiken voor Azure Information Protection. Er is geen herconfiguratie nodig voor clients en services die Azure Information Protection gebruiken.

## <a name="prerequisites"></a>Vereisten

- Een van de volgende Azure AD-beheerdersrollen voor uw tenant: 
    - Azure Information Protection-beheerder
    - Beveiligingsbeheerder
    - Compliancebeheerder
    - Beheerder van nalevingsgegevens
    - Globale beheerder
    
    > [!NOTE]
    > U de beheerderrol Azure Information Protection niet gebruiken als uw tenant zich op het [unified labeling-platform bevindt.](/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
    
    Deze beheerdersrollen zijn alleen vereist voor het configureren van de Azure Information Protection-connector en zijn niet vereist wanneer Azure Sentinel is verbonden met Azure Information Protection.

- Machtigingen om te lezen en te schrijven naar de Log Analytics-werkruimte die u gebruikt voor Azure Sentinel en Azure Information Protection.

- Azure Information Protection is toegevoegd aan de Azure-portal. Zie [Azure Information Protection toevoegen aan de Azure-portal](https://docs.microsoft.com/azure/information-protection/quickstart-viewpolicy#add-azure-information-protection-to-the-azure-portal)als u hulp nodig hebt bij deze stap.

## <a name="connect-to-azure-information-protection"></a>Verbinding maken met Azure Information Protection

Gebruik de volgende instructies als u geen Log Analytics-werkruimte hebt geconfigureerd voor Azure Information Protection of als u de werkruimte moet wijzigen waarmee de logboekregistratiegegevens voor Azure Information Protection worden opgeslagen.

1. Selecteer in Azure Sentinel **gegevensconnectors**en vervolgens **Azure Information Protection (Preview).**

2. Selecteer **Connectorpagina openen**.

3. Selecteer in het blade **Analytics (Preview)** configureren de werkruimte die u momenteel gebruikt voor Azure Sentinel. Als u een andere werkruimte selecteert, zijn de rapportagegegevens van Azure Information Protection niet beschikbaar voor Azure Sentinel.

4. Wanneer u een werkruimte hebt geselecteerd, selecteert u **OK** en moet de **status van** de connector nu worden gewijzigd in **Verbonden**.

5. De rapportagegegevens van Azure Information Protection worden opgeslagen in de **InformationProtectionLogs_CL** tabel binnen de geselecteerde werkruimte. 
    
    Als u het relevante schema in Azure Monitor wilt gebruiken voor deze rapportagegegevens, zoekt u naar **InformationProtectionEvents**. Zie de sectie [Vriendelijk schemavoor gebeurtenisfuncties](https://docs.microsoft.com/azure/information-protection/reports-aip#friendly-schema-reference-for-event-functions) van de azure information protection-documentatie voor informatie over deze gebeurtenisfuncties.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Azure Information Protection verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats-built-in.md)
