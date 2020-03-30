---
title: De OPC Vault-certificaatservice beheren - Azure | Microsoft Documenten
description: De OPC Vault-basisCA-certificaten en gebruikersmachtigingen beheren.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71203649"
---
# <a name="manage-the-opc-vault-certificate-service"></a>De OPC Vault-certificaatservice beheren

In dit artikel worden de beheertaken voor de OPC Vault-certificaatbeheerservice in Azure uitgelegd. Het bevat informatie over het vernieuwen van CA-certificaten van emittenten, het vernieuwen van de certificaatintrekkingslijst (CRL) en hoe u gebruikerstoegang verlenen en intrekken.

## <a name="create-or-renew-the-root-ca-certificate"></a>Het basis-CA-certificaat maken of vernieuwen

Nadat u OPC Vault hebt geïmplementeerd, moet u het basis-CA-certificaat maken. Zonder een geldig CA-certificaat van de uitgever u geen toepassingscertificaten ondertekenen of uitgeven. Raadpleeg [certificaten](howto-opc-vault-secure-ca.md#certificates) om uw certificaten met een redelijke, veilige levensduur te beheren. Een CA-certificaat voor uitgevende instellingen verlengen na de helft van zijn levensduur. Houd er bij het verlengen ook rekening mee dat de geconfigureerde levensduur van een nieuw ondertekend toepassingscertificaat de levensduur van het CA-certificaat van de uitgever niet mag overschrijden.
> [!IMPORTANT]
> De functie Administrator is vereist om het CA-certificaat van de uitgever te maken of te vernieuwen.

1. Open uw certificaatservice bij `https://myResourceGroup-app.azurewebsites.net`en meld u aan.
2. Ga naar **Certificaatgroepen**.
3. Er wordt één standaardcertificaatgroep weergegeven. Selecteer **Bewerken**.
4. In **Gegevens van certificaatgroep bewerken**u de onderwerpnaam en levensduur van uw CA- en toepassingscertificaten wijzigen. Het onderwerp en de levensduur mogen slechts één keer worden ingesteld voordat het eerste CA-certificaat wordt uitgegeven. Levenslange wijzigingen tijdens bewerkingen kunnen leiden tot inconsistente levensduur van uitgegeven certificaten en CRL's.
5. Voer een geldig onderwerp `CN=My CA Root, O=MyCompany, OU=MyDepartment`in (bijvoorbeeld).<br>
   > [!IMPORTANT]
   > Als u van onderwerp verandert, moet u het certificaat van de uitgever verlengen of wordt de service geen toepassingscertificaten ondertekend. Het onderwerp van de configuratie wordt gecontroleerd op basis van het onderwerp van het actieve Emittent certificaat. Als de onderwerpen niet overeenkomen, wordt certificaatondertekening geweigerd.
6. Selecteer **Opslaan**.
7. Als u op dit moment een 'verboden' fout tegenkomt, hebben uw gebruikersreferenties geen toestemming van de beheerder om een nieuw rootcertificaat te wijzigen of te maken. Standaard heeft de gebruiker die de service heeft geïmplementeerd, beheerders- en ondertekeningsrollen bij de service. Andere gebruikers moeten worden toegevoegd aan de rollen van de goedgekeurde, schrijver of beheerder, naar gelang van het geval in de Azure AD-toepassingsregistratie (Azure Directory).
8. Selecteer **Details**. Dit moet de bijgewerkte informatie weergeven.
9. Selecteer **CA-certificaat vernieuwen** om het eerste CA-certificaat van de uitgever uit te geven of om het certificaat van de uitgever te vernieuwen. Selecteer vervolgens **OK**.
10. Na een paar seconden ziet u **certificaatgegevens**. Als u het nieuwste CA-certificaat en CRL wilt downloaden voor distributie naar uw OPC UA-toepassingen, selecteert u **Uitgever** of **Crl.**

De OPC UA-certificaatbeheerservice is nu klaar om certificaten uit te geven voor OPC UA-toepassingen.

## <a name="renew-the-crl"></a>De CRL vernieuwen

Vernieuwing van het CRL is een update, die op regelmatige tijdstippen aan de toepassingen moet worden gedistribueerd. OPC UA-apparaten, die de CRL Distribution Point X509-extensie ondersteunen, kunnen de CRL rechtstreeks bijwerken vanaf het eindpunt van de microservice. Andere OPC UA-apparaten hebben mogelijk handmatige updates nodig of kunnen worden bijgewerkt met GDS-serverpush-extensies (*) om de vertrouwenslijsten bij te werken met de certificaten en CRL's.

In de volgende werkstroom worden alle certificaataanvragen in de verwijderde statusindesinde inde CRL's ingetrokken, die overeenkomen met het CA-certificaat voor uitgevende instellingen waarvoor ze zijn uitgegeven. Het versienummer van de CRL wordt verhoogd met 1. <br>
> [!NOTE]
> Alle uitgegeven CRL's zijn geldig tot het verstrijken van het ISSUER CA-certificaat. Dit komt omdat de OPC UA-specificatie geen verplicht, deterministisch distributiemodel voor CRL vereist.

> [!IMPORTANT]
> De rol administrator is vereist om de CRL van de emittent te vernieuwen.

1. Open uw certificaatservice bij `https://myResourceGroup.azurewebsites.net`en meld u aan.
2. Ga naar de pagina **Certificaatgroepen.**
3. Selecteer **Details**. Hierachter moeten de huidige certificaat- en CRL-informatie worden weergegeven.
4. Selecteer **CRL-intrekkingslijst (BIJWERKEN)** om een bijgewerkte CRL uit te geven voor alle actieve emittentcertificaten in de OPC Vault-opslag.
5. Na een paar seconden ziet u **certificaatgegevens**. Als u het nieuwste CA-certificaat en CRL wilt downloaden voor distributie naar uw OPC UA-toepassingen, selecteert u **Uitgever** of **Crl.**

## <a name="manage-user-roles"></a>Gebruikersrollen beheren

U beheert gebruikersrollen voor de OPC Vault-microservice in de Azure AD Enterprise-toepassing. Zie [Rollen voor](howto-opc-vault-secure-ca.md#roles)een gedetailleerde beschrijving van de roldefinities.

Standaard kan een geverifieerde gebruiker in de tenant zich aanmelden als reader. Voor hogere bevoorrechte rollen is handmatig beheer in de Azure-portal vereist of met PowerShell.

### <a name="add-user"></a>Gebruiker toevoegen

1. Open Azure Portal.
2. Ga naar **Azure Active Directory** > **Enterprise-toepassingen**.
3. Kies de registratie van de OPC Vault `resourceGroupName-service`microservice (standaard, uw).
4. Ga naar **Gebruikers en groepen**.
5. Selecteer **Gebruiker toevoegen**.
6. Selecteer of nodig de gebruiker uit voor toewijzing voor een specifieke rol.
7. Selecteer de rol voor de gebruikers.
8. Selecteer **Toewijzen**.
9. Voor gebruikers in de rol Administrator of Approver blijft u azure key vault-toegangsbeleid toevoegen.

### <a name="remove-user"></a>Gebruiker verwijderen

1. Open Azure Portal.
2. Ga naar **Azure Active Directory** > **Enterprise-toepassingen**.
3. Kies de registratie van de OPC Vault `resourceGroupName-service`microservice (standaard, uw).
4. Ga naar **Gebruikers en groepen**.
5. Selecteer een gebruiker met een rol die u wilt verwijderen en selecteer **Verwijderen**.
6. Verwijder ze ook uit azure key vault-beleid voor verwijderde gebruikers in de rol Administrator of Approver.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Gebruikerstoegangsbeleid toevoegen aan Azure Key Vault

Er is aanvullende toegangsbeleidsregels vereist voor fiatteurs en beheerders.

Standaard heeft de serviceidentiteit slechts beperkte machtigingen voor toegang tot Key Vault, om te voorkomen dat verhoogde bewerkingen of wijzigingen plaatsvinden zonder imitatie van gebruikers. De basisservicemachtigingen zijn Get en List, voor zowel geheimen als certificaten. Voor geheimen is er slechts één uitzondering: de service kan een privésleutel uit de geheime winkel verwijderen nadat deze door een gebruiker is geaccepteerd. Voor alle andere bewerkingen zijn door de gebruiker nagebootste machtigingen vereist.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Voor een rol goedkeurenmoet de volgende machtigingen worden toegevoegd aan Key Vault

1. Open Azure Portal.
2. Ga naar uw `resourceGroupName`OPC Vault, gebruikt tijdens de implementatie.
3. Ga naar de `resourceGroupName-xxxxx`Key Vault.
4. Ga naar **Toegangsbeleid**.
5. Selecteer **Nieuw toevoegen**.
6. Sla de sjabloon over. Er is geen sjabloon die aan de vereisten voldoet.
7. Kies **Principal selecteren**en selecteer de gebruiker die wilt worden toegevoegd of nodig een nieuwe gebruiker uit voor de tenant.
8. Selecteer de volgende **belangrijke machtigingen**: **Opvragen**, **Lijst**en **Ondertekenen**.
9. Selecteer de volgende **geheime**machtigingen **List**: **Opvragen,** **instellen**en **verwijderen**.
10. Selecteer de volgende **certificaatmachtigingen**: **Opvragen** en **aanbieden**.
11. Selecteer **OK**en selecteer **Opslaan**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Voor een beheerdersrol moeten de volgende machtigingen worden toegevoegd aan Key Vault

1. Open Azure Portal.
2. Ga naar uw `resourceGroupName`OPC Vault, gebruikt tijdens de implementatie.
3. Ga naar de `resourceGroupName-xxxxx`Key Vault.
4. Ga naar **Toegangsbeleid**.
5. Selecteer **Nieuw toevoegen**.
6. Sla de sjabloon over. Er is geen sjabloon die aan de vereisten voldoet.
7. Kies **Principal selecteren**en selecteer de gebruiker die wilt worden toegevoegd of nodig een nieuwe gebruiker uit voor de tenant.
8. Selecteer de volgende **belangrijke machtigingen**: **Opvragen**, **Lijst**en **Ondertekenen**.
9. Selecteer de volgende **geheime**machtigingen **List**: **Opvragen,** **instellen**en **verwijderen**.
10. Selecteer de volgende **certificaatmachtigingen**: **Opvragen**, **Lijst**, **Bijwerken**, **Maken**en **importeren**.
11. Selecteer **OK**en selecteer **Opslaan**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Gebruikerstoegangsbeleid verwijderen uit Azure Key Vault

1. Open Azure Portal.
2. Ga naar uw `resourceGroupName`OPC Vault, gebruikt tijdens de implementatie.
3. Ga naar de `resourceGroupName-xxxxx`Key Vault.
4. Ga naar **Toegangsbeleid**.
5. Zoek de gebruiker die u wilt verwijderen en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC Vault-certificaten en -gebruikers beheren, u:

> [!div class="nextstepaction"]
> [Veilige communicatie van OPC-apparaten](howto-opc-vault-secure.md)
