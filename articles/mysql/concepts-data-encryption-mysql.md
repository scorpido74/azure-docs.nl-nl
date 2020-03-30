---
title: Gegevensversleuteling met door de klant beheerde sleutel - Azure Database voor MySQL
description: Azure Database voor MySQL-gegevensversleuteling met een door de klant beheerde sleutel stelt u in staat om byok (Bring Your Own Key) in te brengen voor gegevensbescherming in rust. Het stelt organisaties ook in staat om scheiding van taken te implementeren in het beheer van sleutels en gegevens.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: a97fee619858aa024ff208b72d3b2594c30d2fd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299121"
---
# <a name="azure-database-for-mysql-data-encryption-with-a-customer-managed-key"></a>Azure Database voor MySQL-gegevensversleuteling met een door de klant beheerde sleutel

> [!NOTE]
> Op dit moment moet u toegang vragen om deze mogelijkheid te gebruiken. Neem hiervoor contact AskAzureDBforMySQL@service.microsoft.comop met .

Met gegevensversleuteling met door de klant beheerde sleutels voor Azure Database voor MySQL u uw eigen sleutel (BYOK) meenemen voor gegevensbescherming in rust. Het stelt organisaties ook in staat om scheiding van taken te implementeren in het beheer van sleutels en gegevens. Met door de klant beheerde versleuteling bent u verantwoordelijk voor en in een volledige controle over de levenscyclus van een sleutel, belangrijke gebruiksmachtigingen en controle van bewerkingen op sleutels.

Gegevensversleuteling met door de klant beheerde sleutels voor Azure Database voor MySQL, is ingesteld op serverniveau. Voor een bepaalde server wordt een door de klant beheerde sleutel, genaamd de sleutelversleutelingssleutel (KEK), gebruikt om de door de service gebruikte gegevensversleutelingssleutel (DEK) te versleutelen. De KEK is een asymmetrische sleutel die is opgeslagen in een azure [key vault-exemplaar](../key-vault/key-Vault-secure-your-key-Vault.md) dat eigendom is van een klant en door de klant wordt beheerd. De Sleutelsleutel (KEK) en Gegevensversleutelingssleutel (DEK) wordt later in dit artikel nader beschreven.

Key Vault is een cloudgebaseerd extern sleutelbeheersysteem. Het is zeer beschikbaar en biedt schaalbare, veilige opslag voor RSA-cryptografische sleutels, optioneel ondersteund door FIPS 140-2 Level 2 gevalideerde hardwarebeveiligingsmodules (HSM's). Het staat geen directe toegang tot een opgeslagen sleutel toe, maar biedt wel diensten van versleuteling en decryptie aan geautoriseerde entiteiten. Key Vault kan de sleutel genereren, importeren of [laten overzetten vanaf een on-premises HSM-apparaat.](../key-vault/key-Vault-hsm-protected-keys.md)

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for MySQL prijsniveaus 'Algemeen doel' en 'Geheugengeoptimaliseerd' ondersteunt.

## <a name="benefits"></a>Voordelen

Gegevensversleuteling voor Azure Database voor MySQL biedt de volgende voordelen:

* Toegang tot gegevens wordt volledig door u gecontroleerd door de mogelijkheid om de sleutel te verwijderen en de database ontoegankelijk te maken 
* Volledige controle over de sleutellevenscyclus, inclusief rotatie van de sleutel om af te stemmen op het bedrijfsbeleid
* Centraal beheer en organisatie van sleutels in Azure Key Vault
* Mogelijkheid om scheiding van taken tussen beveiligingsfunctionarissen en DBA en systeembeheerders te implementeren


## <a name="terminology-and-description"></a>Terminologie en beschrijving

**Gegevensversleutelingssleutel (DEK):** een symmetrische AES256-sleutel die wordt gebruikt om een partitie of blok gegevens te versleutelen. Het versleutelen van elk blok gegevens met een andere sleutel maakt crypto-analyseaanvallen moeilijker. Toegang tot DEKs is nodig door de resourceprovider of toepassingsinstantie die een specifiek blok versleutelt en ontsleutelt. Wanneer u een DEK vervangt door een nieuwe sleutel, moeten alleen de gegevens in het bijbehorende blok opnieuw worden versleuteld met de nieuwe sleutel.

**Sleutelversleutelingssleutel (KEK):** een encryptiesleutel die wordt gebruikt om de DEKs te versleutelen. Een KEK die Key Vault nooit verlaat, zorgt ervoor dat de DEKs zelf versleuteld en gecontroleerd worden. De entiteit die toegang heeft tot het KEK kan anders zijn dan de entiteit die de DEK nodig heeft. Aangezien de KEK nodig is om de DEKs te decoderen, is de KEK in feite een enkel punt waarmee DEKs effectief kunnen worden verwijderd door verwijdering van de KEK.

De DEKs, versleuteld met de KEKs, worden apart opgeslagen. Alleen een entiteit met toegang tot het KEK kan deze DEKs ontsleutelen. Zie [Beveiliging in de rust voor](../security/fundamentals/encryption-atrest.md)meer informatie .

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>Hoe gegevensversleuteling met een door de klant beheerde sleutel werkt

![Diagram met een overzicht van Bring Your Own Key](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

Als een MySQL-server door de klant beheerde sleutels die zijn opgeslagen in Key Vault, wilt gebruiken voor versleuteling van de DEK, geeft een Key Vault-beheerder de volgende toegangsrechten aan de server:

* **get:** Voor het ophalen van het openbare gedeelte en de eigenschappen van de sleutel in de sleutelkluis.
* **wrapKey**: Om de DEK te kunnen versleutelen.
* **uitpakkenSleutel:** Om de DEK te kunnen decoderen.

De key vault administrator kan ook [het loggen van Key Vault-controlegebeurtenissen inschakelen,](../azure-monitor/insights/azure-key-vault.md)zodat ze later kunnen worden gecontroleerd.

Wanneer de server is geconfigureerd om de door de klant beheerde sleutel te gebruiken die is opgeslagen in de sleutelkluis, stuurt de server de DEK naar de sleutelkluis voor versleutelingen. Key Vault retourneert de versleutelde DEK, die is opgeslagen in de gebruikersdatabase. Op dezelfde manier, wanneer dat nodig is, stuurt de server de beveiligde DEK naar de sleutelkluis voor decryptie. Auditors kunnen Azure Monitor gebruiken om logboeken van key vault-auditgebeurtenissen te controleren als logboekregistratie is ingeschakeld.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Vereisten voor het configureren van gegevensversleuteling voor Azure Database voor MySQL

De volgende vereisten voor het configureren van Key Vault:

* Key Vault en Azure Database voor MySQL moeten deel uitmaken van dezelfde Azure Active Directory (Azure AD)-tenant. Cross-tenant Key Vault en serverinteracties worden niet ondersteund. Als u daarna resources verplaatst, moet u de gegevensversleuteling opnieuw configureren.
* U moet de functie soft-delete in de sleutelkluis inschakelen om te beschermen tegen gegevensverlies als er een per ongeluke sleutel (of Key Vault) wordt verwijderd. Zachte verwijderde resources worden gedurende 90 dagen bewaard, tenzij de gebruiker ze in de tussentijd herstelt of zuivert. De herstel- en zuiveringsacties hebben hun eigen machtigingen die zijn gekoppeld aan een toegangsbeleid voor Key Vault. De functie soft-delete is standaard uitgeschakeld, maar u deze inschakelen via PowerShell of de Azure CLI (u deze niet inschakelen via de Azure-portal).
* Geef de Azure Database voor MySQL toegang tot de sleutelkluis met de machtigingen get, wrapKey en uitpakkenSleutel met behulp van de unieke beheerde identiteit. In de Azure-portal wordt de unieke identiteit automatisch gemaakt wanneer gegevensversleuteling is ingeschakeld in de MySQL. Zie [Gegevensversleuteling configureren voor MySQL](howto-data-encryption-portal.md) voor gedetailleerde, stapsgewijze instructies wanneer u de Azure-portal gebruikt.

* Wanneer u een firewall met Key Vault gebruikt, moet u de optie **Vertrouwde Microsoft-services toestaan om de firewall te omzeilen**inschakelen.

De volgende vereisten voor het configureren van de door de klant beheerde sleutel:

* De door de klant beheerde sleutel voor het versleutelen van de DEK kan alleen asymmetrisch zijn, RSA 2028.
* De sleutelactiveringsdatum (indien ingesteld) moet een datum en tijd in het verleden zijn. De vervaldatum (indien ingesteld) moet een toekomstige datum en tijd zijn.
* De sleutel moet zich in de *status Ingeschakeld* hebben.
* Als u een bestaande sleutel importeert in de sleutelkluis, moet u deze`.pfx` `.byok`in `.backup`de ondersteunde bestandsindelingen (, , ) verstrekken.

## <a name="recommendations"></a>Aanbevelingen

Wanneer u gegevensversleuteling gebruikt met behulp van een door de klant beheerde sleutel, vindt u hier aanbevelingen voor het configureren van Key Vault:

* Stel een bronvergrendeling in op Key Vault om te bepalen wie deze kritieke bron kan verwijderen en onbedoelde of ongeautoriseerde verwijdering te voorkomen.
* Controle en rapportage van alle versleutelingssleutels inschakelen. Key Vault biedt logboeken die gemakkelijk te injecteren zijn in andere beveiligingsinformatie en tools voor eventmanagement. Azure Monitor Log Analytics is een voorbeeld van een service die al is geïntegreerd.

* Zorg ervoor dat Key Vault en Azure Database voor MySQL zich in dezelfde regio bevinden, zodat u sneller toegang krijgt tot de dek-wrap- en uitpakbewerkingen.

Hier volgen aanbevelingen voor het configureren van een door de klant beheerde sleutel:

* Bewaar een kopie van de door de klant beheerde sleutel op een veilige plaats of borg deze aan de borgservice.

* Als Key Vault de sleutel genereert, maakt u een sleutelback-up voordat u de sleutel voor de eerste keer gebruikt. U de back-up alleen herstellen naar Key Vault. Zie [Back-up-AzKeyVaultKey voor](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)meer informatie over de back-upopdracht.

## <a name="inaccessible-customer-managed-key-condition"></a>Ontoegankelijke door de klant beheerde sleutelvoorwaarde

Wanneer u gegevensversleuteling configureert met een door de klant beheerde sleutel in Key Vault, is continue toegang tot deze sleutel vereist om de server online te laten blijven. Als de server geen toegang meer heeft tot de door de klant beheerde sleutel in Key Vault, begint de server alle verbindingen binnen 10 minuten te weigeren. De server geeft een bijbehorend foutbericht op en wijzigt de serverstatus *in Ontoegankelijk*. De enige actie die is toegestaan in een database in deze status, is het verwijderen ervan.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Onbedoelde toegang tot de sleutel uit Key Vault

Het kan voorkomen dat iemand met voldoende toegangsrechten tot Key Vault per ongeluk de toegang tot de server tot de sleutel uitschakelt door:

* Het intrekken van de sleutelkluis krijgt, wrapKey en uitpakkenSleutelmachtigingen van de server.
* De sleutel verwijderen.
* De sleutelkluis verwijderen.
* Het veranderen van de firewall regels van de sleutelkluis.

* De beheerde identiteit van de server in Azure AD verwijderen.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Controleer de door de klant beheerde sleutel in Key Vault

Als u de databasestatus wilt controleren en waarschuwingen voor het verlies van transparante toegang tot gegevensversleutelingsbeveiliging wilt inschakelen, configureert u de volgende Azure-functies:

* [Azure Resource Health:](../service-health/resource-health-overview.md)Een ontoegankelijke database die geen toegang meer heeft tot de klantsleutel, wordt weergegeven als 'Ontoegankelijk' nadat de eerste verbinding met de database is geweigerd.
* [Activiteitenlogboek:](../service-health/alerts-activity-log-service-notifications.md)Wanneer de toegang tot de klantsleutel in de door de klant beheerde sleutelkluis mislukt, worden items toegevoegd aan het activiteitenlogboek. U de toegang zo snel mogelijk herstellen als u waarschuwingen maakt voor deze gebeurtenissen.

* [Actiegroepen](../azure-monitor/platform/action-groups.md): Definieer deze om u meldingen en waarschuwingen te sturen op basis van uw voorkeuren.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Herstellen en repliceren met de beheerde sleutel van een klant in Key Vault

Nadat Azure Database for MySQL is versleuteld met de beheerde sleutel van een klant die is opgeslagen in Key Vault, wordt ook elke nieuw gemaakte kopie van de server versleuteld. U deze nieuwe kopie maken via een lokale of geoherstelbewerking of via gelezen replica's. De kopie kan echter worden gewijzigd om de beheerde sleutel van een nieuwe klant voor versleuteling weer te geven. Wanneer de door de klant beheerde sleutel wordt gewijzigd, worden oude back-ups van de server gebruikt met de nieuwste sleutel.

Om problemen te voorkomen tijdens het instellen van door de klant beheerde gegevensversleuteling tijdens het herstellen of lezen van replica's, is het belangrijk om deze stappen te volgen op de hoofd- en herstelde/replicaservers:

* Start het proces voor het maken van replica's of lees het proces voor het maken van replica's vanuit de hoofdazure-database voor MySQL.
* Bewaar de nieuw gemaakte server (hersteld/replica) in een ontoegankelijke status, omdat de unieke identiteit nog geen machtigingen heeft gekregen voor Key Vault.
* Op de herstelde/replicaserver moet u de door de klant beheerde sleutel opnieuw valideren in de instellingen voor gegevensversleuteling. Dit zorgt ervoor dat de nieuw gemaakte server wrap- en uitpakmachtigingen krijgt voor de sleutel die is opgeslagen in Key Vault.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [instellen van gegevensversleuteling met een door de klant beheerde sleutel voor uw Azure-database voor MySQL met behulp van de Azure-portal.](howto-data-encryption-portal.md)
