---
title: Uw Synapse-werkruimte beveiligen (voorbeeld)
description: In dit artikel leert u hoe u rollen en toegangsbeheer gebruiken om activiteiten en toegang tot gegevens in synapsewerkruimten te beheren.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: ae8be848b5d12e01865fe6bd3b394b460252aa3e
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606013"
---
# <a name="secure-your-synapse-workspace-preview"></a>Uw Synapse-werkruimte beveiligen (voorbeeld)

In dit artikel leert u hoe u rollen en toegangscontrole gebruiken om activiteiten en toegang tot gegevens te beheren. Door deze instructies op te volgen, wordt het toegangscontrolebeheer in Azure Synapse Analytics vereenvoudigd. U hoeft alleen gebruikers toe te voegen en te verwijderen aan een van de drie beveiligingsgroepen.

## <a name="overview"></a>Overzicht

Als u een Synapse-werkruimte (voorbeeld) wilt beveiligen, volgt u een patroon van het configureren van de volgende items:

- Azure-rollen (zoals de ingebouwde rollen zoals eigenaar, inzender, enz.)
- Synapserollen – deze rollen zijn uniek voor Synapse en zijn niet gebaseerd op Azure-rollen. Er zijn drie van deze rollen:
  - Beheerder synapsenwerkruimte
  - SynapsSQL-beheerder
  - Synapsische Spark-beheerder
- Toegangsbeheer voor gegevens in Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Toegangscontrole voor Synapse SQL- en Spark-databases

## <a name="steps-to-secure-a-synapse-workspace"></a>Stappen om een Synapse-werkruimte te beveiligen

Dit document gebruikt standaardnamen om de instructies te vereenvoudigen. Vervang ze door alle namen van uw keuze.

|Instelling | Voorbeeldwaarde | Beschrijving |
| :------ | :-------------- | :---------- |
| **Synapse-werkruimte** | WS1 |  De naam die de Synapse-werkruimte zal hebben. |
| **ADLSGEN2-account** | STG1 STG1 | Het ADLS-account dat u gebruiken met uw werkruimte. |
| **Container** | CNT1 (CNT1) | De container in STG1 die de werkruimte standaard gebruikt. |
| **Active directory tenant** | contoso | de naam van de active directory-tenant.|
||||

## <a name="step-1-set-up-security-groups"></a>STAP 1: Beveiligingsgroepen instellen

Drie beveiligingsgroepen voor uw werkruimte maken en invullen:

- **WS1\_WSAdmins** – voor gebruikers die volledige controle over de werkruimte nodig hebben
- **WS1\_SparkAdmins** – voor gebruikers die volledige controle nodig hebben over de Spark-aspecten van de werkruimte
- **WS1\_SQLAdmins** – voor gebruikers die volledige controle nodig hebben over de SQL-aspecten van de werkruimte
- **WS1\_WSAdmins** toevoegen aan **WS1\_SQLAdmins**
- **WS1\_WSAdmins** toevoegen aan **WS1\_SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>STAP 2: Uw Data Lake Storage Gen2-account voorbereiden

Identificeer deze informatie over uw opslag:

- Het ADLSGEN2-account dat u gebruiken voor uw werkruimte. Dit document noemt het STG1.  STG1 wordt beschouwd als het "primaire" opslagaccount voor uw werkruimte.
- De container in WS1 die uw Synapse-werkruimte standaard gebruikt. Dit document noemt het CNT1.  Deze container wordt gebruikt voor:
  - De back-upgegevensbestanden voor Spark-tabellen opslaan
  - Uitvoeringslogboeken voor Spark-taken

- Wijs de beveiligingsgroepen met de Azure-portal de volgende rollen toe op CNT1

  - **WS1\_WSAdmins** toewijzen aan de rol **Opslagblob-gegevensbijdrage**
  - **WS1\_SparkAdmins** toewijzen aan de rol **Opslagblob-gegevensbijdrager**
  - **WS1\_SQLAdmins** toewijzen aan de rol **Opslagblob-gegevensbijdrage**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>STAP 3: Uw Synapse Workspace maken en configureren

Maak in de Azure-portal een Synapse-werkruimte:

- De naam van de werkruimte WS1
- Kies STG1 voor het opslagaccount
- Kies CNT1 voor de container die wordt gebruikt als het "bestandssysteem".
- Open WS1 in Synapse Studio
-  > Selecteer **Manage****Toegangsbeheer beheren** en de beveiligingsgroepen toewijzen aan de volgende Synapserollen.
  - **WS1\_WSAdmins** toewijzen aan beheerders van Synapse Workspace
  - **WS1\_SparkAdmins** toewijzen aan Synapse Spark-beheerders
  - **WS1\_SQLAdmins** toewijzen aan Synapse SQL-beheerders

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>STAP 4: Data Lake Storage Gen2 configureren voor gebruik door Synapse-werkruimte

De Synapse-werkruimte heeft toegang nodig tot STG1 en CNT1, zodat deze pijplijnen kan uitvoeren en systeemtaken kan uitvoeren.

- De Azure-portal openen
- STG1 zoeken
- Navigeren naar CNT1
- Controleren of de MSI (Managed Service Identity) voor WS1 is toegewezen aan de rol **Azure Blob Data Contributor** op CNT1
  - Als u het niet toegewezen ziet, wijst u het toe.
  - De MSI heeft dezelfde naam als de werkruimte. In dit geval zou &quot;het&quot;WS1 .

## <a name="step-5-configure-admin-access-for-sql-pools"></a>STAP 5: Beheerderstoegang configureren voor SQL-groepen

- De Azure-portal openen
- Navigeren naar WS1
- Klik **onder Instellingen**op SQL Active **Directory-beheerder**
- Klik **op Beheerder instellen** \_en kies WS1 SQLAdmins

## <a name="step-6-maintaining-access-control"></a>STAP 6: Toegangscontrole behouden

De configuratie is voltooid.

Nu u gebruikers toevoegen en verwijderen aan de drie beveiligingsgroepen om de toegang voor gebruikers te beheren.

Hoewel u gebruikers handmatig toewijzen aan Synapse-rollen, zal het, als u dat wel doet, de zaken niet consistent configureren. Voeg in plaats daarvan alleen gebruikers toe aan de beveiligingsgroepen.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>STAP 7: Toegang verifiëren voor gebruikers in de rollen

Gebruikers in elke rol moeten de volgende stappen uitvoeren:

|   | Stap | Werkruimtebeheerders | Spark-beheerders | SQL-beheerders |
| --- | --- | --- | --- | --- |
| 1 | Een parketbestand uploaden naar CNT1 | JA | JA | JA |
| 2 | Lees het parketbestand met SQL on demand | JA | NO | JA |
| 3 | Een Spark-pool maken | JA [1] | JA [1] | NO  |
| 4 | Leest het parketbestand met een notitieblok | JA | JA | NO |
| 5 | Een pijplijn maken vanuit het notitieblok en de pijplijn activeren om nu uit te voeren | JA | NO | NO |
| 6 | Een SQL-groep maken en een &quot;SQL-script uitvoeren, zoals SELECT 1&quot; | JA [1] | NO | JA[1] |

> [!NOTE]
> [1] Om SQL- of Spark-pools te maken, moet de gebruiker ten minste een bijdrager rol hebben in de Synapse-werkruimte.
> [!TIP]
>
> - Sommige stappen zullen bewust niet worden toegestaan, afhankelijk van de rol.
> - Houd er rekening mee dat sommige taken kunnen mislukken als de beveiliging niet volledig is geconfigureerd. Deze taken worden vermeld in de tabel.

## <a name="step-8-network-security"></a>STAP 8: Netwerkbeveiliging

De firewall van de werkruimte, het virtuele netwerk en [de privékoppeling configureren.](../../sql-database/sql-database-private-endpoint-overview.md)

## <a name="step-9-completion"></a>STAP 9: Voltooiing

Uw werkruimte is nu volledig geconfigureerd en beveiligd.

## <a name="how-roles-interact-with-synapse-studio"></a>Hoe rollen interageren met Synapse Studio

Synapse Studio zal zich anders gedragen op basis van gebruikersrollen. Sommige items kunnen worden verborgen of uitgeschakeld als een gebruiker niet is toegewezen aan rollen die de juiste toegang geven. In de volgende tabel wordt een overzicht van het effect op Synapse Studio.

| Taak | Werkruimtebeheerders | Spark-beheerders | SQL-beheerders |
| --- | --- | --- | --- |
| Open Synapse Studio | JA | JA | JA |
| Home-hub weergeven | JA | JA | JA |
| Gegevenshub weergeven | JA | JA | JA |
| Data Hub / Bekijk gekoppelde ADLSGen2-accounts en -containers | JA [1] | JA[1] | JA[1] |
| Gegevenshub / Databases bekijken | JA | JA | JA |
| Gegevenshub / Objecten weergeven in databases | JA | JA | JA |
| Gegevenshub / Toegangsgegevens in SQL-pooldatabases | JA   | NO   | JA   |
| Data Hub / Access-gegevens in SQL on-demand databases | JA [2]  | NO  | JA [2]  |
| Gegevenshub / Toegangsgegevens in Spark-databases | JA [2] | JA [2] | JA [2] |
| De hub Ontwikkelen gebruiken | JA | JA | JA |
| Hub / auteur SQL Scripts ontwikkelen | JA | NO | JA |
| Hub / auteur Spark Job Definities ontwikkelen | JA | JA | NO |
| Hub/ auteur notitieblokken ontwikkelen | JA | JA | NO |
| Hub / auteur Dataflows ontwikkelen | JA | NO | NO |
| De hub Orchestrate gebruiken | JA | JA | JA |
| Hub orkestreren / Pijplijnen gebruiken | JA | NO | NO |
| De hub beheren gebruiken | JA | JA | JA |
| Hub/ SQL-pools beheren | JA | NO | JA |
| Hub- / Spark-pools beheren | JA | JA | NO |
| Hub / Triggers beheren | JA | NO | NO |
| Hub / Gekoppelde services beheren | JA | JA | JA |
| Hub/ Toegangscontrole beheren (gebruikers toewijzen aan Synapse-werkruimterollen) | JA | NO | NO |
| Runtimes voor hub/ integratie beheren | JA | JA | JA |

> [!NOTE]
> [1] Toegang tot gegevens in containers is afhankelijk van het toegangscontrolein ADLSGen2 [2] SQL OD-tabellen en Spark-tabellen slaan hun gegevens op in ADLSGen2 en toegang vereist de juiste machtigingen voor ADLSGen2.

## <a name="next-steps"></a>Volgende stappen

Een [Synapse-werkruimte maken](../quickstart-create-workspace.md)
