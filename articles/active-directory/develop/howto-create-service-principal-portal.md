---
title: Een Azure AD-app & serviceprincipal maken in de portal
titleSuffix: Microsoft identity platform
description: Maak een nieuwe Azure Active Directory-app & serviceprincipal om toegang tot bronnen met op rollen gebaseerd toegangsbeheer te beheren in Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: c5f65adfe401f2f6e99234d08b8e8dabeff7d5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264113"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>How to: Gebruik de portal om een Azure AD-toepassing en serviceprincipal te maken die toegang heeft tot bronnen

In dit artikel ziet u hoe u een nieuwe Azure Active Directory -toepassing en serviceprincipal maakt die kunnen worden gebruikt met het op rollen gebaseerde toegangscontrolebeheer. Wanneer u code hebt die toegang nodig heeft tot bronnen of deze moet wijzigen, u een identiteit voor de app maken. Deze identiteit staat bekend als een service-principal. U vervolgens de vereiste machtigingen toewijzen aan de serviceprincipal. In dit artikel ziet u hoe u de portal gebruiken om de serviceprincipal te maken. Het richt zich op een single-tenant applicatie waarbij de toepassing is bedoeld om te draaien binnen slechts een organisatie. U gebruikt meestal toepassingen met één tenant voor line-of-business toepassingen die binnen uw organisatie worden uitgevoerd.

> [!IMPORTANT]
> In plaats van een serviceprincipal te maken, u overwegen beheerde identiteiten te gebruiken voor Azure-bronnen voor uw toepassingsidentiteit. Als uw code wordt uitgevoerd op een service die beheerde identiteiten ondersteunt en toegang krijgt tot bronnen die Azure AD-verificatie ondersteunen, zijn beheerde identiteiten een betere optie voor u. Zie [Wat is beheerde identiteiten voor Azure-resources voor Azure-bronnen voor Azure-bronnen.](../managed-identities-azure-resources/overview.md)

## <a name="create-an-azure-active-directory-application"></a>Een Azure Active Directory-toepassing maken

Laten we meteen de identiteit creëren. Als u een probleem tegenkomt, controleert u de [vereiste machtigingen](#required-permissions) om te controleren of uw account de identiteit kan maken.

1. Meld u aan bij uw Azure-account via de [Azure-portal.](https://portal.azure.com)
1. Selecteer **Azure Active Directory**.
1. Selecteer **App-registraties**.
1. Selecteer **Nieuwe registratie**.
1. Geef de toepassing een naam. Selecteer een ondersteund accounttype, dat bepaalt wie de toepassing kan gebruiken. Selecteer **onder URI omleiden**de optie **Web** voor het type toepassing dat u wilt maken. Voer de URI in waar naar het toegangstoken wordt verzonden. U geen referenties maken voor een [native toepassing.](../manage-apps/application-proxy-configure-native-client-application.md) U dat type niet gebruiken voor een geautomatiseerde toepassing. Selecteer Register na het instellen **van**de waarden .

   ![Een naam voor uw toepassing typen](./media/howto-create-service-principal-portal/create-app.png)

U hebt uw Azure AD-toepassing en serviceprincipal gemaakt.

## <a name="assign-a-role-to-the-application"></a>Een rol toewijzen aan de toepassing

Als u toegang wilt krijgen tot bronnen in uw abonnement, moet u een rol aan de toepassing toewijzen. Bepaal welke rol de juiste machtigingen voor de toepassing biedt. Zie [RBAC: Ingebouwde rollen voor](../../role-based-access-control/built-in-roles.md)meer informatie over de beschikbare rollen.

U het bereik instellen op het niveau van het abonnement, de resourcegroep of de resource. Machtigingen worden overgenomen naar lagere niveaus van bereik. Als u bijvoorbeeld een toepassing toevoegt aan de leesrol voor een resourcegroep, kan deze de brongroep en de resources die deze bevat, lezen.

1. Selecteer in de Azure-portal het scopeniveau waaraan u de toepassing wilt toewijzen. Als u bijvoorbeeld een rol wilt toewijzen aan het abonnementsbereik, zoekt en selecteert u **Abonnementen**of selecteert u **Abonnementen** op de **startpagina.**

   ![Wijs bijvoorbeeld een rol toe aan het abonnementsbereik](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecteer het specifieke abonnement waaraan u de toepassing wilt toewijzen.

   ![Abonnement selecteren voor toewijzing](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Als u het abonnement dat u zoekt niet ziet, selecteert u **het filter Globale abonnementen**. Controleer of het gewenste abonnement is geselecteerd voor de portal.

1. Selecteer **Toegangsbesturingselement (IAM)**.
1. Selecteer **Roltoewijzing toevoegen**.
1. Selecteer de rol die u aan de toepassing wilt toewijzen. Als u de toepassing bijvoorbeeld wilt toestaan acties uit te voeren zoals **opnieuw opstarten,** instanties **starten** en **stoppen,** selecteert u de rol **Inzender.**  Lees meer over de [beschikbare rollen](../../role-based-access-control/built-in-roles.md) Standaard worden Azure AD-toepassingen niet weergegeven in de beschikbare opties. Als u uw toepassing wilt vinden, zoekt u naar de naam en selecteert u deze.

   ![De rol selecteren die aan de toepassing moet worden toegewezen](./media/howto-create-service-principal-portal/select-role.png)

1. Selecteer **Opslaan** om de rol toe te wijsen. U ziet uw toepassing in de lijst met gebruikers met een rol voor dat bereik.

Uw service principal is ingesteld. U het gebruiken om uw scripts of apps uit te voeren. In de volgende sectie ziet u hoe u waarden krijgen die nodig zijn bij het programmatisch aanmelden.

## <a name="get-values-for-signing-in"></a>Waarden voor aanmelden opvragen

Wanneer u zich programmatisch aanmeldt, moet u de tenant-id doorgeven aan uw verificatieverzoek. U hebt ook de ID voor uw toepassing en een verificatiesleutel nodig. U kunt deze waarden als volgt ophalen:

1. Selecteer **Azure Active Directory**.
1. Selecteer uw toepassing in **App-registraties** in Azure AD.
1. Kopieer de directory -id (tenant) en sla deze op in uw toepassingscode.

    ![De map (tenant-id) kopiëren en opslaan in uw app-code](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Kopieer de **Toepassings-id** en sla deze op in uw toepassingscode.

   ![De toepassings-id (client) kopiëren](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certificaten en geheimen
Daemon-toepassingen kunnen twee vormen van referenties gebruiken om te verifiëren met Azure AD: certificaten en toepassingsgeheimen.  We raden u aan een certificaat te gebruiken, maar u ook een nieuw toepassingsgeheim maken.

### <a name="upload-a-certificate"></a>Een certificaat uploaden

U een bestaand certificaat gebruiken als u er een hebt.  Optioneel u alleen een zelfondertekend certificaat maken voor *testdoeleinden.* Open PowerShell en voer [Nieuw-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) uit met de volgende parameters om een zelfondertekend certificaat te maken in het gebruikerscertificaatarchief op uw computer: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exporteer dit certificaat naar een bestand met de MMC-module [Gebruikerscertificaat](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) beheren die toegankelijk is vanuit het Windows Configuratiescherm.

1. Selecteer **Uitvoeren** in het menu **Start** en voer **certmgr.msc in.**

   Het gereedschap Certificaatbeheer voor de huidige gebruiker wordt weergegeven.

1. Als u uw certificaten wilt weergeven, vouwt u onder **Certificaten - Huidige gebruiker** in het linkerdeelvenster de **persoonlijke** map uit.
1. Klik met de rechtermuisknop op het door u gemaakte cert, selecteer **Alle taken->exporteren**.
1. Volg de wizard Certificaatexporteren.  Exporteer de privésleutel, geef een wachtwoord op voor het cert-bestand en exporteer naar een bestand.

Ga als nodig de zoveelste keer als u het certificaat uploadt:

1. Selecteer **Azure Active Directory**.
1. Selecteer uw toepassing in **App-registraties** in Azure AD.
1. Selecteer **Certificaten & geheimen**.
1. Selecteer **Uploadcertificaat** en selecteer het certificaat (een bestaand certificaat of het zelfondertekende certificaat dat u hebt geëxporteerd).

    ![Selecteer Certificaat uploaden en selecteer het certificaat dat u wilt toevoegen](./media/howto-create-service-principal-portal/upload-cert.png)

1. Selecteer **Toevoegen**.

Nadat u het certificaat bij uw aanvraag hebt geregistreerd in het aanvraagregistratieportaal, moet u de clienttoepassingscode inschakelen om het certificaat te gebruiken.

### <a name="create-a-new-application-secret"></a>Een nieuw toepassingsgeheim maken

Als u ervoor kiest geen certificaat te gebruiken, u een nieuw toepassingsgeheim maken.

1. Selecteer **Certificaten & geheimen**.
1. Selecteer **Klantgeheimen -> Nieuw klantgeheim**.
1. Geef een beschrijving van het geheim en een duur. Als u klaar bent, selecteert u **Toevoegen**.

   Na het opslaan van het klantgeheim wordt de waarde van het clientgeheim weergegeven. Kopieer deze waarde omdat u de sleutel later niet meer ophalen. U verstrekt de sleutelwaarde met de toepassings-ID om in te loggen als de toepassing. Bewaar de sleutelwaarde op een locatie waar de toepassing deze kan ophalen.

   ![De geheime waarde kopiëren omdat u deze later niet ophalen](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Toegangsbeleid voor resources configureren
Houd er rekening mee dat u mogelijk toevoegingsmachtigingen moet configureren voor bronnen die uw toepassing moet openen om toegang te krijgen. U moet bijvoorbeeld ook [het toegangsbeleid van een sleutelkluis bijwerken](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) om uw toepassing toegang te geven tot sleutels, geheimen of certificaten.  

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw sleutelkluis en selecteer **Toegangsbeleid**.  
1. Selecteer **Toegangsbeleid toevoegen**en selecteer vervolgens de sleutel-, geheime en certificaatmachtigingen die u wilt toestaan.  Selecteer de serviceprincipal die u eerder hebt gemaakt.
1. Selecteer **Toevoegen** om het toegangsbeleid toe te voegen en **vervolgens Op te slaan** om uw wijzigingen door te voeren.
    ![Toegangsbeleid toevoegen](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>Vereiste machtigingen

U moet over voldoende machtigingen beschikken om een toepassing te registreren bij uw Azure AD-tenant en aan de toepassing een rol in uw Azure-abonnement toe te wijzen.

### <a name="check-azure-ad-permissions"></a>Azure AD-machtigingen controleren

1. Selecteer **Azure Active Directory**.
1. Let op je rol. Als u de rol **Gebruiker** hebt, moet u ervoor zorgen dat niet-beheerders toepassingen kunnen registreren.

   ![Zoek je rol. Als u een gebruiker bent, u ervoor zorgen dat niet-beheerders apps kunnen registreren](./media/howto-create-service-principal-portal/view-user-info.png)

1. Selecteer in het linkerdeelvenster **gebruikersinstellingen**.
1. Controleer de instelling **App-registraties.** Deze waarde kan alleen worden ingesteld door een beheerder. Als deze is ingesteld op **Ja,** kan elke gebruiker in de Azure AD-tenant een app registreren.

Als de instelling voor app-registraties is ingesteld op **Nee,** kunnen alleen gebruikers met een beheerdersrol dit soort toepassingen registreren. Bekijk [beschikbare rollen](../users-groups-roles/directory-assign-admin-roles.md#available-roles) en [functiemachtigingen](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) voor meer informatie over beschikbare beheerdersrollen en de specifieke machtigingen in Azure AD die aan elke rol worden gegeven. Als aan uw account de rol Gebruiker is toegewezen, maar de instelling voor app-registratie beperkt is tot beheerders, vraagt u uw beheerder om u een van de beheerdersrollen toe te wijzen die alle aspecten van app-registraties kunnen maken en beheren, of om gebruikers in staat te stellen zich te registreren Apps.

### <a name="check-azure-subscription-permissions"></a>Machtigingen voor Azure-abonnementen controleren

In uw Azure-abonnement moet `Microsoft.Authorization/*/Write` uw account toegang hebben om een rol toe te wijzen aan een AD-app. Deze toegang wordt verleend via de rol [Eigenaar](../../role-based-access-control/built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](../../role-based-access-control/built-in-roles.md#user-access-administrator). Als aan uw account de rol **Inzender is** toegewezen, hebt u geen adequate toestemming. U ontvangt een foutmelding wanneer u probeert de serviceprincipal een rol toe te wijzen.

Ga als een onderzoek uit naar de machtigingen voor uw abonnement:

1. Zoeken en selecteer **Abonnementen**of selecteer **Abonnementen** op de **startpagina.**

   ![Search](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecteer het abonnement waarin u de serviceprincipal wilt maken.

   ![Abonnement selecteren voor toewijzing](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Als u het abonnement dat u zoekt niet ziet, selecteert u **het filter Globale abonnementen**. Controleer of het gewenste abonnement is geselecteerd voor de portal.

1. Selecteer **Mijn machtigingen**. Selecteer **vervolgens Klik hier om de volledige toegangsgegevens voor dit abonnement weer te geven.**

   ![Selecteer het abonnement waarin u de serviceprincipal wilt maken](./media/howto-create-service-principal-portal/view-details.png)

1. Selecteer **Weergave** in **toewijzingen van rollen** om uw toegewezen rollen weer te geven en bepaal of u voldoende machtigingen hebt om een rol toe te wijzen aan een AD-app. Als dit niet het zo is, vraagt u uw abonnementsbeheerder om u toe te voegen aan de functie Gebruikerstoegangsbeheerder. In de volgende afbeelding krijgt de gebruiker de rol Eigenaar toegewezen, wat betekent dat de gebruiker over voldoende machtigingen beschikt.

   ![In dit voorbeeld wordt weergegeven dat de gebruiker de rol Eigenaar heeft toegewezen](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Role-based Access Control](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het opgeven van beveiligingsbeleid.  
* Zie [Azure Resource Manager Resource Provider-bewerkingen](../../role-based-access-control/resource-provider-operations.md)voor een lijst met beschikbare acties die aan gebruikers kunnen worden verleend of geweigerd.
