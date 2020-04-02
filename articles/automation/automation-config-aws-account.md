---
title: Verificatie configureren met Amazon Web Services
description: In dit artikel wordt beschreven hoe u een AWS-referentie voor runbooks in Azure Automation maakt en valideert bij het beheren van AWS-resources.
keywords: AWS-verificatie, AWS configureren
services: automation
ms.subservice: process-automation
ms.date: 04/17/2018
ms.topic: conceptual
ms.openlocfilehash: 891db5d17ddde681f75845fdca6c9252c5b32b86
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520821"
---
# <a name="authenticate-runbooks-with-amazon-web-services"></a>Runbooks met Amazon Web Services verifiëren

Het automatiseren van algemene taken met resources in Amazon Web Services (AWS) kan worden bewerkstelligd met Automation-runbooks in Azure. U kunt vele taken in AWS automatiseren met Automation-runbooks, net zoals u dit kunt met resources in Azure. U hebt slechts twee dingen nodig:

* Een AWS-abonnement en een set referenties. Het gaat dan met name om uw AWS-toegangssleutel en de geheime sleutel. Raadpleeg het artikel [AWS-referenties gebruiken](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html) voor meer informatie.
* Een Azure-abonnement en een Automation-account.

Als u wilt verifiëren met AWS, moet u een set AWS-referenties opgeven om uw runbooks te verifiëren die vanuit Azure Automation worden uitgevoerd. Als u al een Automation-account hebt gemaakt en u dat wilt gebruiken om te verifiëren met AWS, u de stappen in de volgende sectie volgen: Als u een account wilt wijden voor runbooks die aws-bronnen targeten, moet u eerst een nieuw [Automatiseringsaccount](automation-offering-get-started.md) maken (de optie overslaan om een serviceprincipal te maken) en de volgende stappen gebruiken:

## <a name="configure-automation-account"></a>Automation-account configureren

Voor communicatie tussen Azure Automation en AWS moet u eerst uw AWS-referenties ophalen en deze opslaan als assets in Azure Automation. Voer de volgende stappen uit die in het AWS-document [Toegangssleutels voor uw AWS-account beheren](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) zijn gedocumenteerd om een toegangssleutel te maken en kopieer de **toegangssleutel-id** en **geheime toegangssleutel** (download desgewenst uw sleutelbestand om het ergens veilig op te slaan).

Nadat u uw AWS-beveiligingssleutels hebt gemaakt en gekopieerd, moet u een referentieasset met een Azure Automation-account maken om deze sleutels veilig op te slaan en ernaar te verwijzen met uw runbooks. Volg de stappen in de sectie: **Maak een nieuwe referentie** in het artikel Credential assets in Azure [Automation](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) en voer de volgende gegevens in:

1. Voer in het vak **Naam****AWScred** in of een geschikte waarde die voldoet aan uw naamgevingsstandaarden.
2. Typ **in** het vak Gebruikersnaam uw **Toegangs-id** en uw **geheime toegangssleutel** in het vak **Wachtwoord** en **Wachtwoord bevestigen.**

## <a name="next-steps"></a>Volgende stappen

* Lees het oplossingsartikel [Implementatie van een virtuele machine in Amazon Web Services automatiseren](automation-scenario-aws-deployment.md) voor informatie over het maken van runbooks om taken in AWS te automatiseren.
