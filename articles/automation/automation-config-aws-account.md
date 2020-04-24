---
title: Azure Automation-runbooks verifiëren met Amazon Web Services
description: In dit artikel wordt beschreven hoe u een AWS-referentie voor runbooks in Azure Automation maakt en valideert bij het beheren van AWS-resources.
keywords: AWS-verificatie, AWS configureren
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 1bf60d17af1d9866de6a62ac538fa0bd9a15ce52
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82113388"
---
# <a name="authenticate-azure-automation-runbooks-with-amazon-web-services"></a>Azure Automation-runbooks verifiëren met Amazon Web Services

Het automatiseren van algemene taken met resources in Amazon Web Services (AWS) kan worden bewerkstelligd met Automation-runbooks in Azure. U kunt vele taken in AWS automatiseren met Automation-runbooks, net zoals u dit kunt met resources in Azure. U hebt slechts twee dingen nodig:

* Een AWS-abonnement en een set referenties. Het gaat dan met name om uw AWS-toegangssleutel en de geheime sleutel. Raadpleeg het artikel [AWS-referenties gebruiken](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) voor meer informatie.
* Een Azure-abonnement en een Automation-account.

Als u wilt verifiëren met AWS, moet u een set AWS-referenties opgeven om uw runbooks te verifiëren die vanuit Azure Automation worden uitgevoerd. Als u al een Automation-account hebt gemaakt en u wilt dit account gebruiken om te verifiëren met AWS, kunt u de stappen in de volgende sectie uitvoeren. Als u een account wilt toewijzen voor runbooks die gericht zijn op AWS-resources, moet u eerst een nieuw [Automation-account](automation-create-standalone-account.md) maken en de stap overs Laan om een uitvoeren als-account te maken. Nadat u het account hebt gemaakt, volgt u de onderstaande stappen om de configuratie te volt ooien.

## <a name="configure-automation-account"></a>Automation-account configureren

Voor communicatie tussen Azure Automation en AWS moet u eerst uw AWS-referenties ophalen en deze opslaan als assets in Azure Automation. Voer de volgende stappen uit die in het AWS-document [Toegangssleutels voor uw AWS-account beheren](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) zijn gedocumenteerd om een toegangssleutel te maken en kopieer de **toegangssleutel-id** en **geheime toegangssleutel** (download desgewenst uw sleutelbestand om het ergens veilig op te slaan).

Nadat u uw AWS-beveiligingssleutels hebt gemaakt en gekopieerd, moet u een referentieasset met een Azure Automation-account maken om deze sleutels veilig op te slaan en ernaar te verwijzen met uw runbooks. Volg de stappen in de sectie: **voor het maken van een nieuwe referentie** in het artikel [referentie-assets in azure Automation](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) en voer de volgende gegevens in:

1. Voer in het vak **Naam****AWScred** in of een geschikte waarde die voldoet aan uw naamgevingsstandaarden.
2. Typ in het vak **gebruikers naam** uw **toegangs-id** en uw **geheime toegangs sleutel** in het vak **wacht woord** en **wacht woord bevestigen** .

## <a name="next-steps"></a>Volgende stappen

* Lees het oplossingsartikel [Implementatie van een virtuele machine in Amazon Web Services automatiseren](automation-scenario-aws-deployment.md) voor informatie over het maken van runbooks om taken in AWS te automatiseren.
