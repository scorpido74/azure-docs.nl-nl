---
title: Gegevens versleuteling Azure Database for MySQL met door de klant beheerde sleutel
description: Azure Database for MySQL gegevens versleuteling met door de klant beheerde sleutel, kunt u Bring Your Own Key (BYOK) voor gegevens bescherming in rust en organisaties toestaan schei ding van taken te implementeren in het beheer van sleutels en gegevens.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 12e9ab9066449e8928d937d9c3f9f7f1522b6c60
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942098"
---
# <a name="azure-database-for-mysql-data-encryption-with-customer-managed-key"></a>Gegevens versleuteling Azure Database for MySQL met door de klant beheerde sleutel

> [!NOTE]
> Op dit moment moet u toegang aanvragen om deze mogelijkheid te gebruiken. Als u dit wilt doen, neemt u contact op met AskAzureDBforMySQL@service.microsoft.com.

Azure Database for MySQL gegevens versleuteling met door de klant beheerde sleutel, kunt u Bring Your Own Key (BYOK) voor gegevens bescherming in rust en organisaties toestaan schei ding van taken te implementeren in het beheer van sleutels en gegevens. Met door de klant beheerde versleuteling bent u verantwoordelijk voor en in een volledig beheer van de levens cyclus van een sleutel (sleutel maken, uploaden, draaien, verwijderen), machtigingen voor sleutel gebruik en controle van bewerkingen op sleutels.

Voor Azure Database for MySQL wordt de gegevens versleuteling ingesteld op server niveau. Met deze vorm van gegevens versleuteling wordt de sleutel gebruikt voor het versleutelen van de database versleutelings sleutel (DEK), een door de klant beheerde asymmetrische sleutel die is opgeslagen in een door de klant en door de klant beheerde [Azure Key Vault (Azure)](../key-vault/key-Vault-secure-your-key-Vault.md), een op de cloud gebaseerd extern sleutel beheersysteem. Azure is Maxi maal beschikbaar en biedt schaal bare beveiligde opslag voor RSA cryptografische sleutels, optioneel ondersteund door FIPS 140-2 level 2 gevalideerde hardware security modules (Hsm's). Het biedt geen rechtstreekse toegang tot een opgeslagen sleutel, maar voorziet in Services voor versleuteling/ontsleuteling met behulp van de sleutel voor de gemachtigde entiteiten. De sleutel kan worden gegenereerd door de Key Vault, geïmporteerd of [overgedragen naar de Key Vault van een on-premises HSM-apparaat](../key-vault/key-Vault-hsm-protected-keys.md).

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for MySQL de prijs categorieën voor Algemeen en geoptimaliseerd voor geheugen ondersteunt.

## <a name="benefits"></a>Voordelen

Gegevens versleuteling voor Azure Database for MySQL biedt de volgende voor delen:

* Grotere transparantie, gedetailleerdere controle en beheer voor de versleutelings sleutel.
* Centraal beheer en de organisatie van sleutels door ze te hosten in Azure Key Vault.
* De mogelijkheid om schei ding van taken te implementeren in het beheer van sleutels en gegevens binnen de organisatie.
* Gescheiden sleutel beheer van gegevens beheer binnen een organisatie, zodat Key Vault beheerder de belangrijkste toegangs machtigingen kan intrekken om de versleutelde data base ontoegankelijk te maken.
* Meer vertrouwen van uw eind klanten, omdat Azure Key Vault zodanig is ontworpen dat micro soft geen versleutelings sleutels kan zien of extra heren.

## <a name="terminology-and-description"></a>Terminologie en beschrijving

**Gegevens versleutelings sleutel (dek)** : een SYMMETRISCHe AES256-sleutel die wordt gebruikt om een partitie of gegevens blok te versleutelen. Het versleutelen van elk gegevens blok met een andere sleutel maakt crypto-analyse aanvallen lastiger. Toegang tot DEKs is vereist voor de resource provider of het toepassings exemplaar dat een specifiek blok versleutelt en ontsleutelt. Wanneer een DEK wordt vervangen door een nieuwe sleutel, moeten alleen de gegevens in het bijbehorende blok opnieuw worden versleuteld met de nieuwe sleutel.

Sleutel versleutelings **sleutel (KEK)** : een versleutelings sleutel die wordt gebruikt om de gegevens versleutelings sleutels te versleutelen. Gebruik van een versleutelings sleutel die nooit Key Vault verlaat, zodat de gegevens versleutelings sleutels zelf kunnen worden versleuteld en beheerd. De entiteit die toegang heeft tot de KEK kan afwijken van de entiteit waarvoor de DEK is vereist. Omdat de KEK is vereist voor het ontsleutelen van de DEKs, is de KEK een specifiek punt waarmee DEKs effectief kan worden verwijderd door het verwijderen van de KEK.

De gegevens versleutelings sleutels, versleuteld met de sleutel versleutelings sleutels, worden afzonderlijk opgeslagen en alleen een entiteit met toegang tot de sleutel coderings sleutel kan deze versleutelings sleutels ontsleutelen. Zie [beveiliging in versleuteling op rest](../security/fundamentals/encryption-atrest.md)voor meer informatie.

## <a name="how-data-encryption-with-customer-managed-key-works"></a>Hoe gegevens versleuteling met door de klant beheerde sleutel werkt

![Uw eigen sleutel overzicht geven](media/concepts-data-access-and-security-data-encryption/mysqloverview.png)

Een MySQL-server kan alleen door de klant beheerde sleutels gebruiken die zijn opgeslagen in azure voor versleuteling van de DEK, maar een Key Vault beheerder moet de volgende toegangs rechten geven voor de server met behulp van de unieke identiteit:

* **Get** -voor het ophalen van het open bare onderdeel en de eigenschappen van de sleutel in de Key Vault
* **wrapKey** : om te kunnen beveiligen (versleutelen) dek
* **sleutel uitpakken** : voor het opheffen van de beveiliging (ontsleutelen) dek

Key Vault beheerder kan [logboek registratie van Key Vault controle gebeurtenissen ook inschakelen](../azure-monitor/insights/azure-key-vault.md), zodat ze later kunnen worden gecontroleerd.

Wanneer de server is geconfigureerd voor het gebruik van de door de klant beheerde sleutel die is opgeslagen in de Key Vault, stuurt de server de DEK naar de Key Vault voor versleuteling. Key Vault retourneert de versleutelde DEK, die wordt opgeslagen in de gebruikers database. Zo nodig verzendt de server beveiligde DEK naar de Key Vault voor ontsleuteling. Audi tors kunnen Azure Monitor gebruiken om Key Vault audit event-logboeken te controleren als logboek registratie is ingeschakeld.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-mysql"></a>Vereisten voor het configureren van gegevens versleuteling voor Azure Database for MySQL

### <a name="requirements-for-configuring-akv"></a>Vereisten voor het configureren van Azure

* Key Vault en Azure Database for MySQL moeten deel uitmaken van dezelfde Azure Active Directory (AAD)-Tenant. Cross-Tenant Key Vault en server interacties worden niet ondersteund. Als u later resources wilt verplaatsen, moet u de gegevens versleuteling opnieuw configureren. Meer informatie over het verplaatsen van resources.
* De functie voor voorlopig verwijderen moet zijn ingeschakeld op de Key Vault om te beschermen tegen onbedoelde sleutel (of Key Vault) voor het verwijderen van gegevens verlies. Voorlopig verwijderde bronnen worden 90 dagen bewaard, tenzij de klant in de tussen tijd is hersteld of verwijderd. De herstel-en opschoon acties hebben hun eigen machtigingen die zijn gekoppeld aan een Key Vault toegangs beleid. De functie voor voorlopig verwijderen is standaard uitgeschakeld en kan worden ingeschakeld via Power shell of CLI. Deze kan niet worden ingeschakeld via Azure Portal.
* Verleen de Azure Database for MySQL toegang tot de Key Vault met de machtigingen **Get, wrapKey, sleutel uitpakken** met behulp van de unieke beheerde identiteit. Wanneer u Azure Portal gebruikt, wordt de unieke identificeren automatisch gemaakt wanneer gegevens versleuteling is ingeschakeld op de MySQL. Zie [Configure Data Encryption for MySQL](howto-data-encryption-portal.md) voor gedetailleerde stapsgewijze instructies voor het gebruik van Azure Portal.

* Wanneer u Firewall met Azure gebruikt, moet u *de optie vertrouwde micro soft-Services toestaan om de firewall te omzeilen*, in te scha kelen.

### <a name="requirements-for-configuring-customer-key"></a>Vereisten voor het configureren van de klant sleutel

* De door de klant beheerde sleutel die moet worden gebruikt voor het versleutelen van de DEK kan alleen asymmetrisch zijn: RSA 2028.
* De datum en tijd waarop de sleutel wordt geactiveerd, moeten in het verleden liggen. De verval datum (indien ingesteld) moet een datum en tijd in de toekomst zijn.
* De sleutel moet de *ingeschakelde* status hebben.
* Als u een bestaande sleutel importeert in de Key Vault, moet u deze opgeven in de ondersteunde bestands indelingen (`.pfx`, `.byok`, `.backup`).

## <a name="recommendations-when-using-data-encryption-using-customer-managed-key"></a>Aanbevelingen voor het gebruik van gegevens versleuteling met door de klant beheerde sleutel

### <a name="recommendation-for-configuring-akv"></a>Aanbeveling voor het configureren van Azure

* Stel een resource vergrendeling in voor de Key Vault om te bepalen wie deze kritieke resource kan verwijderen en om onbedoelde of ongeoorloofde verwijdering te voor komen. Meer informatie over resource vergrendelingen.
* Controle en rapportage inschakelen voor alle versleutelings sleutels: Key Vault biedt logboeken die eenvoudig kunnen worden geïnjecteerd in andere hulpprogram ma's voor beveiligings informatie en beheer van gebeurtenissen. Azure Monitor Log Analytics is een voor beeld van een service die al is geïntegreerd.

* Zorg ervoor dat de Key Vault en de Azure Database for MySQL zich in dezelfde regio bevinden, zodat u snel toegang hebt tot DEK-terugloop/onverpakte bewerkingen.

### <a name="recommendation-for-configuring-customer-managed-key"></a>Aanbeveling voor het configureren van door de klant beheerde sleutel

* Bewaar een kopie van de door de klant beheerde sleutel (KEK) op een veilige plaats of borg aan de borg service.

* Als de sleutel wordt gegenereerd in de Key Vault, moet u een sleutel back-up maken voordat u de sleutel voor de eerste keer in azure gebruikt. U kunt back-ups alleen herstellen naar een Azure Key Vault. Meer informatie over de [back-up-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey) -opdracht.

## <a name="inaccessible-customer-managed-key-condition"></a>Niet-toegankelijke door de klant beheerde sleutel voorwaarde

Wanneer gegevens versleuteling is geconfigureerd met door de klant beheerde sleutel in de Azure Key Vault (Azure), is voortdurende toegang tot deze sleutel vereist voor de server om online te blijven. Als de-server de toegang tot de door de klant beheerde sleutel in azure kwijtraakt, wordt de server binnen tien minuten door de verbinding met het bijbehorende fout bericht geweigerd en wordt de server status gewijzigd in niet **toegankelijk**. De enige actie die is toegestaan voor een Data Base met de status unaccessible, wordt deze verwijderd.

### <a name="accidental-key-access-revocation-from-the-azure-key-vault-akv"></a>Het intrekken van onbedoelde sleutel toegang van de Azure Key Vault (Azure)

Het kan gebeuren dat iemand met voldoende toegangs rechten voor de Key Vault per ongeluk server toegang tot de sleutel uitschakelt door:

* Get-, wrapKey-, sleutel uitpakken-machtigingen van de server intrekken Key Vault
* de sleutel verwijderen
* de Key Vault verwijderen
* de firewall regels van Key Vault wijzigen

* de beheerde identiteit van de server in Azure Active Directory verwijderen

## <a name="monitoring-of-the-customer-managed-key-in-the-key-vault"></a>Bewaking van de door de klant beheerde sleutel in de Key Vault

Als u de status van de Data Base wilt bewaken en waarschuwingen wilt inschakelen voor het verlies van toegang tot TDE-Protector, configureert u de volgende Azure-functies:

* [Azure resource Health](../service-health/resource-health-overview.md) -een niet-toegankelijke data base die de toegang tot de klant sleutel heeft verloren, wordt weer gegeven als ' niet toegankelijk ' nadat de eerste verbinding met de data base is geweigerd.
* [Activiteiten logboek](../service-health/alerts-activity-log-service-notifications.md) : wanneer toegang tot de klant sleutel in de door de klant beheerde Key Vault mislukt, worden de gegevens toegevoegd aan het activiteiten logboek. Als u waarschuwingen voor deze gebeurtenissen maakt, kunt u zo snel mogelijk toegang herstellen.

* [Actie groepen](../azure-monitor/platform/action-groups.md) kunnen worden gedefinieerd om u meldingen en waarschuwingen te sturen op basis van uw voor keuren, bijvoorbeeld E-mail/SMS/push/Voice, Logic app, webhook, ITSM of Automation Runbook.

## <a name="restore-and-replica-with-customers-managed-key-in-the-key-vault"></a>Herstel en replica met de beheerde sleutel van de klant in de Key Vault

Zodra een Azure Database for MySQL is versleuteld met de door de klant beheerde sleutel die is opgeslagen in de Key Vault, wordt een nieuw gemaakte kopie van de server (Hoewel lokale of geo-restore-bewerking of via Lees replica's) ook versleuteld met de beheerde sleutel van dezelfde klant. Ze kunnen echter worden gewijzigd in overeenstemming met de nieuwe beheerde sleutel van de klant voor versleuteling. Wanneer de door de klant beheerde sleutel wordt gewijzigd, worden de oude back-ups van de server met de meest recente sleutel gestart.

Als u problemen wilt voor komen bij het instellen van door de klant beheerde gegevens versleuteling tijdens het terugzetten of het maken van een replica, is het belang rijk dat u deze stappen volgt op de Master-en herstel-of replica server:

* Start het herstel-of het maken van de replica van de hoofd Azure Database for MySQL.
* De nieuwe server (hersteld/replica) wordt een niet-toegankelijke status behouden, omdat de unieke identiteit nog geen machtigingen heeft gekregen voor de Azure Key Vault (Azure)
* Valideer op de herstelde/replica-server de door de klant beheerde sleutel opnieuw in de instellingen voor gegevens versleuteling om ervoor te zorgen dat de zojuist gemaakte Server machtigingen voor verpakken/uitpakken heeft voor de sleutel die is opgeslagen in Azure.

* Beide stappen moeten worden uitgevoerd om ervoor te zorgen dat de gegevens versleuteling op de Master wordt bewaard, evenals de herstelde/replica-server.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het instellen van gegevens versleuteling met door de klant beheerde sleutel voor uw Azure-Data Base voor MySQL met behulp van de Azure Portal](howto-data-encryption-portal.md).
