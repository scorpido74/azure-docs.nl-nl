---
title: 'Snelstartgids voor Azure Security Center: uw Linux-computers naar Security Center onboarden | Microsoft Docs'
description: Deze snelstartgids laat zien hoe u uw Linux-computers naar Security Center kunt onboarden.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2018
ms.author: memildin
ms.openlocfilehash: 82ce466f12acef529b5e45e5dd94c64b94be0f7e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202882"
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Quickstart: Linux-computers naar Azure Security Center onboarden
Nadat u uw Azure-abonnementen hebt opgedaan, kunt u Security Center inschakelen voor Linux-resources die buiten Azure worden uitgevoerd, bijvoorbeeld on-premises of in andere Clouds, door een agent in te richten. De agent wordt micro soft Monitoring Agent (MMA) genoemd, maar deze wordt ook wel de OMS-agent genoemd.

In deze Quick start ziet u hoe u de agent op een Linux-computer installeert.

## <a name="prerequisites"></a>Vereisten
U moet over een abonnement op Microsoft Azure beschikken om met Security Center aan de slag te gaan. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).

U moet u zich in de Standard-prijscategorie van Security Center bevinden voordat u aan deze snelstart kunt beginnen. Zie [Onboard your Azure subscription to Security Center Standard](security-center-get-started.md) (Uw Azure-abonnement registreren voor Security Center Standard) voor upgrade-instructies. U kunt Security Center Standard kosteloos uitproberen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie.

## <a name="add-new-linux-computer"></a>Nieuwe Linux-computer toevoegen

1. Meld u aan bij de [Azure Portal](https://azure.microsoft.com/features/azure-portal/).
2. Ga naar het **Microsoft Azure**-menu en selecteer **Security Center**. **Security Center - Overzicht** wordt geopend.

   ![Overzicht van Security Center][2]

3. Selecteer in het hoofdmenu van Security Center de optie **Aan de slag**.
4. Selecteer het tabblad **Aan de slag**. ![Aan de slag][3]

5. Klik op **Configureren** onder **Nieuwe niet-Azure-computers toevoegen**, waarna een lijst met uw Log Analytics-werkruimten wordt weergegeven. De lijst bevat, indien van toepassing, de standaardwerkruimte die is gemaakt door Security Center toen automatisch inrichten werd ingeschakeld. Selecteer deze werkruimte of een andere werkruimte die u wilt gebruiken.

    ![Niet-Azure-computer toevoegen](./media/quick-onboard-linux-computer/non-azure.png)

6. Selecteer op de pagina **Agent toewijzen** onder **AGENT VOOR LINUX DOWNLOADEN EN VRIJGEVEN** de knop **Kopiëren** om de opdracht *wget* te kopiëren.

7. Open Kladblok en plak deze opdracht. Sla dit bestand op een locatie op die toegankelijk is vanaf uw Linux-computer.

## <a name="install-the-agent"></a>De agent installeren

1. Open het bestand dat u hebt opgeslagen op uw Linux-computer. Selecteer de volledige inhoud, kopieer de inhoud, open een terminalconsole en plak de opdracht.
2. Zodra de installatie is voltooid, kunt u controleren of de *omsagent* is geïnstalleerd door de opdracht *pgrep* uit te voeren. De opdracht retourneert de PID (proces-id) *omsagent*, zoals hieronder wordt weergegeven:

   ![De agent installeren][5]

De logboeken voor de agent vindt u op: */var/opt/Microsoft/omsagent/\<werk ruimte-id >/log/*

  ![Logboeken voor agent][6]

Na enige tijd, maximaal 30 minuten, wordt de nieuwe Linux-computer in Security Center weergegeven.

U kunt nu uw Azure-VM's en niet-Azure-computers op één plek bewaken. Onder **Compute** vindt u een overzicht van alle VM's en computers, plus aanbevelingen. Elke kolom vertegenwoordigt een reeks aanbevelingen. De kleur vertegenwoordigt de huidige beveiligingsstatus van de VM's of computers voor die aanbeveling. Security Center geeft ook detecties voor deze computers in beveiligingswaarschuwingen weer.

  ![Blade Compute][7] Er worden twee soorten pictogrammen weergegeven op de blade **Compute**:

  ![pictogram1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Niet-Azure-computer

  ![pictogram2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer de agent niet langer nodig is, kunt u deze verwijderen van de Linux-computer.

De agent verwijderen:

1. Download de Linux-agent [universal script](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) naar de computer.
2. Voer het gebundelde .sh-bestand met het argument *--purge* uit op de computer, waardoor de agent en de bijbehorende configuratie volledig worden verwijderd.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>Volgende stappen
In deze Quick Start hebt u de agent op een Linux-computer ingericht. Voor meer informatie over het gebruik van Security Center gaat u verder met de zelfstudie voor het configureren van een beveiligingsbeleid en het beoordelen van de beveiliging van uw resources.

> [!div class="nextstepaction"]
> [Zelfstudie: Beveiligingsbeleid opstellen en beoordelen](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/get-started.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
