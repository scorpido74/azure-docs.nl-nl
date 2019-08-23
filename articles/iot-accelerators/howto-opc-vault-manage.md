---
title: De OPC kluis Certificate Management-service beheren-Azure | Microsoft Docs
description: De OPC-kluis basis-CA-certificaten en-gebruikers machtigingen beheren.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6e53914b16bc126cdab7300d918dbb7b6b868728
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973803"
---
# <a name="how-to-manage-the-opc-ua-certificate-service"></a>De OPC UA-certificaat service beheren

Dit artikel bevat informatie over de beheer taken voor de OPC UA Certificate Management-service in azure, het vernieuwen van CA-certificaten van uitgevers, het vernieuwen van de certificaatintrekkingslijst (CRL) en het verlenen en intrekken van gebruikers toegang.

## <a name="create-or-renew-the-root-ca-certificate"></a>Het basis-CA-certificaat maken of vernieuwen

Als u het basis-CA-certificaat wilt maken, moet u na de implementatie een verplichte stap uitvoeren. Zonder een geldig CA-certificaat van de verlener kunnen geen toepassings certificaten worden ondertekend en uitgegeven.<br>Raadpleeg het hoofd stuk over de [levens duur van certificaten](howto-opc-vault-secure-ca.md#certificates) om uw certificaten te beheren met redelijke, veilige levens duur. Een CA-certificaat van de verlener moet worden vernieuwd na de helft van de levens duur, maar niet later dan de geconfigureerde levens duur van een nieuw ondertekend toepassings certificaat zou de levens duur van het certificaat van de verlener moeten overschrijden.<br>
> [!IMPORTANT]
> De rol beheerder is vereist voor het maken of vernieuwen van het CA-certificaat van de verlener.

1. Open uw certificaat service op `https://myResourceGroup-app.azurewebsites.net` en meld u aan.
2. Navigeer naar de `Certificate Groups` pagina.
3. Er wordt één `Default` certificaat groep vermeld. Klik op `Edit`.
4. In `Edit Certificate Group Details` kunt u de onderwerpnaam en de levens duur van uw CA-en toepassings certificaten wijzigen.<br>Het onderwerp en de levens duur mogen slechts eenmaal worden ingesteld voordat het eerste CA-certificaat wordt uitgegeven. Wijzigingen in de levens duur tijdens bewerkingen kunnen leiden tot inconsistente levens duur van uitgegeven certificaten en Crl's.
5. Voer een geldig onderwerp in, bijvoorbeeld `CN=My CA Root, O=MyCompany, OU=MyDepartment`.<br>
   > [!IMPORTANT]
   > Als u het onderwerp wilt wijzigen, moet u het certificaat van de verlener vernieuwen of kunnen toepassings certificaten niet worden ondertekend met de service. Het onderwerp van de configuratie is Sanity gecontroleerd op basis van het onderwerp van het actieve certificaat van de certificerings instantie. Als de onderwerpen niet overeenkomen, wordt het ondertekenen van certificaten geweigerd.
6. Klik op de `Save` knop.
7. Als u op dit moment een ' verboden ' fout aanraakt, zijn uw gebruikers referenties niet gemachtigd om een nieuw basis certificaat te wijzigen of te maken. De gebruiker die de service heeft geïmplementeerd, heeft standaard beheerders-en-handtekening rollen met de service. andere gebruikers moeten worden toegevoegd aan de rollen ' goed keurder ', ' schrijver ' of ' beheerder ', indien van toepassing in de registratie van de AzureAD.
8. Klik op de `Details` knop. De `View Certificate Group Details` bijgewerkte gegevens moeten worden weer gegeven.
9. Klik op de `Renew CA Certificate` knop om het eerste CA-certificaat van de verlener te verlenen of het certificaat van de verlener te vernieuwen. Druk `Ok` op om door te gaan.
10. Na een paar seconden worden `Certificate Details` de weer gegeven. Druk `Issuer` op `Crl` of down load het nieuwste CA-certificaat en de CRL voor distributie naar uw OPC UA-toepassingen.
11. De OPC UA Certificate Management-service is nu gereed voor het uitgeven van certificaten voor OPC UA-toepassingen.

## <a name="renew-the-crl"></a>De CRL vernieuwen

Verlenging van de certificaatintrekkingslijst (CRL) is een update die met regel matige tussen pozen moet worden gedistribueerd naar de toepassingen. OPC UA-apparaten, die ondersteuning bieden voor de x509-extensie van het CRL-distributie punt, kunnen de CRL rechtstreeks bijwerken vanuit het micro service-eind punt. Andere OPC UA-apparaten kunnen hand matig worden bijgewerkt of in het aanbevolen geval kunnen worden bijgewerkt met GDS server-push extensies (*) om de vertrouwens lijsten bij te werken met de certificaten en Crl's.

In de volgende werk stroom worden alle certificaat aanvragen in de verwijderde statussen ingetrokken in de Crl's, die overeenkomen met het CA-certificaat van de certificaat verlener waarvoor ze zijn uitgegeven. Het versie nummer van de CRL wordt verhoogd met 1. <br>
> [!NOTE]
> Alle uitgegeven Crl's zijn geldig tot de verval datum van het certificaat van de verlenende instantie, omdat de OPC UA-specificatie geen verplicht, deterministisch distributie model voor CRL vereist.

> [!IMPORTANT]
> De rol beheerder is vereist voor het vernieuwen van de CRL van de verlener.

1. Open uw certificaat service op `https://myResourceGroup.azurewebsites.net` en meld u aan.
2. Navigeer naar de `Certificate Groups` pagina.
3. Klik op de `Details` knop. De `View Certificate Group Details` moet het huidige certificaat en de CRL-informatie weer geven.
4. Klik op de `Update CRL Revocation List(CRL)` knop om een bijgewerkte CRL uit te geven voor alle actieve certificaat verlener in de OPC-kluis opslag.
5. Na een paar seconden worden `Certificate Details` de weer gegeven. Druk `Issuer` op `Crl` of down load het nieuwste CA-certificaat en de CRL voor distributie naar uw OPC UA-toepassingen.

## <a name="manage-user-roles"></a>Gebruikers rollen beheren

Gebruikers rollen voor de OPC-kluis micro service worden beheerd in de Azure Active Directory Enter prise-toepassing.

Raadpleeg de sectie [rollen](howto-opc-vault-secure-ca.md#roles) voor een gedetailleerde beschrijving van de roldefinities.

Standaard kan een geverifieerde gebruiker in de Tenant zich als ' lezer ' aanmelden bij de service. Voor rollen met een hogere bevoegdheid moet het Azure Portal hand matig worden beheerd of met behulp van Power shell.

### <a name="add-user"></a>Gebruiker toevoegen

1. Open de Azure Portal op `portal.azure.com`.
2. Navigeer naar `Azure Active Directory`/`Enterprise applications`.
3. Kies de registratie van de OPC-kluis micro service, standaard uw `resourceGroupName-service`.
4. Navigeer naar `Users and Groups`.
5. Klik op `Add User`.
6. De gebruiker selecteren of uitnodigen voor toewijzing aan een specifieke rol.
7. Selecteer de rol voor de gebruikers.
8. Druk op `Assign` de knop.
9. Ga verder met `Administrator` het `Approver` toevoegen van Azure Key Vault toegangs beleid voor gebruikers in of rol.

### <a name="remove-user"></a>Gebruiker verwijderen

1. Open de Azure Portal op `portal.azure.com`.
2. Navigeer naar `Azure Active Directory`/`Enterprise applications`.
3. Kies de registratie van de OPC-kluis micro service, standaard uw `resourceGroupName-service`.
4. Navigeer naar `Users and Groups`.
5. Selecteer een gebruiker met een rol die u wilt verwijderen.
6. Druk op `Remove` de knop.
7. Verwijderde beheerders en goed keurders ook verwijderen uit Azure Key Vault beleid.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Beleid voor gebruikers toegang toevoegen aan Azure Key Vault

Er zijn aanvullende toegangs beleidsregels vereist voor **goed keurders** en **beheerders**.

De service-identiteit heeft standaard slechts beperkte machtigingen om toegang te krijgen tot Key Vault om te voor komen dat er verhoogde bewerkingen of wijzigingen worden uitgevoerd zonder gebruikers imitatie. De basis-service machtigingen `Get` zijn `List` en voor zowel geheimen als certificaten. Voor geheimen is er slechts één uitzonde ring: de service `Delete` kan een persoonlijke sleutel uit het geheime archief hebben die door een gebruiker is geaccepteerd. Voor alle andere bewerkingen is door de gebruiker geïmiteerde machtigingen vereist.<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>Voor een **rol** van de goed keurder moeten de volgende machtigingen worden toegevoegd aan Key Vault:

1. Open de Azure Portal op `portal.azure.com`.
2. Navigeer naar uw OPC- `resourceGroupName`kluis die wordt gebruikt tijdens de implementatie.
3. Navigeer naar het Key Vault `resourceGroupName-xxxxx`.
4. Ga naar `Access Policies`.
5. Klik op `Add new`.
6. Sla de sjabloon over, er is geen sjabloon die voldoet aan de vereisten.
7. Klik op `Select Principal` en selecteer de gebruiker die u wilt toevoegen of als u een nieuwe gebruiker wilt uitnodigen voor de Tenant.
8. Controleer `Key permissions` :`Get`en belang rijker `Sign`. `List`
9. Controleren `Secret permissions`: `Get`, `List` en.`Delete` `Set`
10. Controleren `Certificate permissions`: `Get`en .`List`
11. Klik op `Ok`.
12. `Save`gewijzigde.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>Voor een **beheerdersrol** moeten de volgende machtigingen worden toegevoegd aan Key Vault:

1. Open de Azure Portal op `portal.azure.com`.
2. Navigeer naar uw OPC- `resourceGroupName`kluis die wordt gebruikt tijdens de implementatie.
3. Navigeer naar het Key Vault `resourceGroupName-xxxxx`.
4. Ga naar `Access Policies`.
5. Klik op `Add new`.
6. Sla de sjabloon over, er is geen sjabloon die voldoet aan de vereisten.
7. Klik op `Select Principal` en selecteer de gebruiker die u wilt toevoegen of als u een nieuwe gebruiker wilt uitnodigen voor de Tenant.
8. Controleer `Key permissions` :`Get`en belang rijker `Sign`. `List`
9. Controleren `Secret permissions`: `Get`, `List` en.`Delete` `Set`
10. Controleren `Certificate permissions`: `Get`, `List`, `Update`en.`Import` `Create`
11. Klik op `Ok`.
12. `Save`gewijzigde.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Gebruikers toegangs beleid verwijderen uit Azure Key Vault

1. Open de Azure Portal op `portal.azure.com`.
2. Navigeer naar uw OPC- `resourceGroupName`kluis die wordt gebruikt tijdens de implementatie.
3. Navigeer naar het Key Vault `resourceGroupName-xxxxx`.
4. Ga naar `Access Policies`.
5. Zoek de gebruiker die u wilt verwijderen en `... / Delete` Klik op om de gebruikers toegang te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u OPC-kluis certificaten en gebruikers kunt beheren, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Veilige communicatie van OPC-apparaten](howto-opc-vault-secure.md)
