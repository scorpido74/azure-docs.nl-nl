---
title: Analyse van agentloze afhankelijkheid instellen in Azure Migrate Server Assessment
description: Analyse van agentloze afhankelijkheid instellen in Azure Migrate Server Assessment.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455703"
---
# <a name="set-up-agentless-dependency-visualization"></a>Visualisatie van agentloze afhankelijkheid instellen 

In dit artikel wordt beschreven hoe u een afhankelijkheidsanalyse zonder agent in Azure-migratie:Serverbeoordeling instellen. [Afhankelijkheidsanalyse](concepts-dependency-visualization.md) helpt u bij het identificeren en begrijpen van afhankelijkheden tussen machines die u wilt beoordelen en migreren naar Azure.


> [!IMPORTANT]
> Agentless dependency visualisatie is momenteel in preview voor VMware VM's alleen, ontdekt met de Azure Migrate:Server Assessment tool.
> Functies kunnen beperkt of onvolledig zijn.
> Deze preview wordt gedekt door klantenondersteuning en kan worden gebruikt voor productieworkloads.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.



## <a name="before-you-start"></a>Voordat u begint

- [Meer informatie over](concepts-dependency-visualization.md#agentless-analysis) analyse van agentloze afhankelijkheid.
- [Bekijk](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) de vereisten en ondersteuningsvereisten voor het instellen van agentless afhankelijkheidsvisualisatie voor VMware VM's
- Zorg ervoor dat u een Azure Migrate-project hebt [gemaakt.](how-to-add-tool-first-time.md)
- Als u al een project hebt gemaakt, controleert u of u het hulpprogramma Azure Migreren:Serverbeoordeling hebt [toegevoegd.](how-to-assess.md)
- Zorg ervoor dat u een [Azure Migrate-toestel](migrate-appliance.md) hebt ingesteld om uw on-premises machines te ontdekken. Meer informatie over het instellen van een toestel voor Vm's van [VMware.](how-to-set-up-appliance-vmware.md) Het toestel detecteert on-premises machines en stuurt metagegevens en prestatiegegevens naar Azure Migrate:Server Assessment.


## <a name="current-limitations"></a>Huidige beperkingen

- Op dit moment u geen server uit een groep toevoegen of verwijderen in de afhankelijkheidsanalyseweergave.
- Een afhankelijkheidskaart voor een groep servers is momenteel niet beschikbaar.
- Momenteel kunnen de afhankelijkheidsgegevens niet in tabelindeling worden gedownload.

## <a name="create-a-user-account-for-discovery"></a>Een gebruikersaccount maken voor detectie

Stel een gebruikersaccount in zodat Server Assessment toegang heeft tot de VM voor detectie. [Meer informatie](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) over accountvereisten.


## <a name="add-the-user-account-to-the-appliance"></a>Het gebruikersaccount toevoegen aan het toestel

Voeg het gebruikersaccount toe aan het toestel.

1. Open de app voor toestelbeheer. 
2. Navigeer naar het deelvenster **VCenter-details opgeven.**
3. Klik in **Toepassing ontdekken en afhankelijkheden van VM's**op **Referenties toevoegen**
3. Kies het **besturingssysteem,** geef een vriendelijke naam op voor het account en het **wachtwoord voor**/**Password** de gebruikersnaam
6. Klik op **Opslaan**.
7. Klik **op Detectie opslaan en starten**.

    ![Vm-gebruikersaccount toevoegen](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Afhankelijkheidsdetectie starten

Kies de machines waarop u afhankelijkheidsdetectie wilt inschakelen.

1. Klik in **Azure Migrate: Serverbeoordeling**op **Gedetecteerde servers**.
2. Klik op het pictogram **Afhankelijkheidsanalyse.**
3. Klik **op Servers toevoegen**.
3. Kies **op** de pagina Servers toevoegen het toestel dat de relevante machines ontdekt.
4. Selecteer de machines in de machinelijst.
5. Klik **op Servers toevoegen**.

    ![Afhankelijkheidsdetectie starten](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

U afhankelijkheden ongeveer zes uur na het starten van afhankelijkheidsdetectie visualiseren.

## <a name="visualize-dependencies"></a>Afhankelijkheden visualiseren

1. Klik in **Azure Migrate: Serverbeoordeling**op **Gedetecteerde servers**.
2. Zoek naar de machine die u wilt bekijken.
3. Klik in de kolom **Afhankelijkheden** op **Afhankelijkheden weergeven**
4. Wijzig de periode waarvoor u de kaart wilt bekijken met de vervolgkeuzelijst **Tijdduur.**
5. Vouw de **groep Client** uit om de machines weer te geven met een afhankelijkheid van de geselecteerde machine.
6. Vouw de groep **Poort** uit om de machines weer te geven die afhankelijk zijn van de geselecteerde machine.
7. Als u naar de kaartweergave van een van de afhankelijke machines wilt navigeren, klikt u op de machinenaam **>-serverkaart laden**

    ![Serverpoortgroep en laadserverkaart uitbreiden](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Clientgroep uitbreiden ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Vouw de geselecteerde machine uit om details op procesniveau voor elke afhankelijkheid weer te geven.

    ![Server uitbreiden om processen weer te geven](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Procesinformatie voor een afhankelijkheid is niet altijd beschikbaar. Als het niet beschikbaar is, wordt de afhankelijkheid weergegeven met het proces dat is gemarkeerd als 'Onbekend proces'.

## <a name="stop-dependency-discovery"></a>Afhankelijkheidsdetectie stoppen

Kies de machines waarop u de afhankelijkheidsdetectie wilt stoppen.

1. Klik in **Azure Migrate: Serverbeoordeling**op **Gedetecteerde servers**.
2. Klik op het pictogram **Afhankelijkheidsanalyse.**
3. Klik **op Servers verwijderen**.
3. Kies op de pagina **Servers verwijderen** het **toestel** dat de VM's ontdekt waarop u de afhankelijkheidsdetectie wilt stoppen.
4. Selecteer de machines in de machinelijst.
5. Klik **op Servers verwijderen**.


## <a name="next-steps"></a>Volgende stappen

[Groepeer de machines](how-to-create-a-group.md) voor beoordeling.
