---
title: Een Azure AD-App & Service-Principal maken in de portal
titleSuffix: Microsoft identity platform
description: Maak een nieuwe Azure Active Directory app & Service-Principal voor het beheren van de toegang tot resources met toegangs beheer op basis van rollen in Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0261c060358b9f0f42624008341f80245ff57aca
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424534"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Procedure: een Azure AD-toepassing en service-principal maken met behulp van de portal om toegang te krijgen tot resources

In dit artikel wordt beschreven hoe u een nieuwe Azure Active Directory (Azure AD)-toepassing en Service-Principal maakt die kunnen worden gebruikt met het toegangs beheer op basis van rollen. Wanneer u code hebt die resources moet openen of wijzigen, kunt u een identiteit voor de app maken. Deze identiteit staat bekend als een service-principal. U kunt vervolgens de vereiste machtigingen toewijzen aan de Service-Principal. In dit artikel leest u hoe u de-portal kunt gebruiken om de service-principal te maken. Het is gericht op een toepassing met één Tenant waarbij de toepassing alleen binnen één organisatie kan worden uitgevoerd. Normaal gesp roken gebruikt u toepassingen met één Tenant voor line-of-business-toepassingen die binnen uw organisatie worden uitgevoerd.

> [!IMPORTANT]
> In plaats van een service-principal te maken, kunt u overwegen beheerde identiteiten te gebruiken voor Azure-resources voor uw toepassings identiteit. Als uw code wordt uitgevoerd op een service die beheerde identiteiten ondersteunt en toegang krijgt tot bronnen die ondersteuning bieden voor Azure AD-verificatie, zijn beheerde identiteiten een betere optie voor u. Zie [Wat is beheerde identiteiten voor Azure-resources?](../managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten voor Azure-resources, waaronder de services die momenteel door worden ondersteund.

## <a name="create-an-azure-active-directory-application"></a>Een Azure Active Directory-toepassing maken

Laten we meteen beginnen met het maken van de identiteit. Als u een probleem ondervindt, controleert u de [vereiste machtigingen](#required-permissions) om te controleren of uw account de identiteit kan maken.

1. Meld u aan bij uw Azure-account via de [Azure Portal](https://portal.azure.com).
1. Selecteer **Azure Active Directory**.
1. Selecteer **App-registraties**.
1. Selecteer **nieuwe registratie**.
1. Geef de toepassing een naam. Selecteer een ondersteund account type, dat bepaalt wie de toepassing kan gebruiken. Onder **omleidings-URI**selecteert u **Web** voor het type toepassing dat u wilt maken. Voer de URI in waarnaar het toegangs token wordt verzonden. U kunt geen referenties maken voor een [systeem eigen toepassing](../manage-apps/application-proxy-configure-native-client-application.md). U kunt dit type niet gebruiken voor een geautomatiseerde toepassing. Nadat u de waarden hebt ingesteld, selecteert u **registreren**.

   ![Typ een naam voor uw toepassing](./media/howto-create-service-principal-portal/create-app.png)

U hebt uw Azure AD-toepassing en service-principal gemaakt.

## <a name="assign-the-application-to-a-role"></a>De toepassing aan een rol toewijzen

Als u toegang wilt krijgen tot resources in uw abonnement, moet u de toepassing toewijzen aan een rol. Bepaal welke rol de juiste machtigingen voor de toepassing biedt. Zie voor meer informatie over de beschikbare rollen, [RBAC: ingebouwde rollen](../../role-based-access-control/built-in-roles.md).

U kunt het bereik instellen op het niveau van het abonnement, de resource groep of de resource. Machtigingen worden overgenomen op lagere niveaus van bereik. Als u bijvoorbeeld een toepassing toevoegt aan de rol van lezer voor een resource groep, betekent dit dat de resource groep en alle resources die deze bevat, kunnen worden gelezen.

1. Selecteer in het Azure Portal het bereik dat u aan de toepassing wilt toewijzen. Als u bijvoorbeeld een rol wilt toewijzen aan het abonnements bereik, zoekt en selecteert u **abonnementen**, of selecteert u **abonnementen** op de **Start** pagina.

   ![U kunt bijvoorbeeld een rol aan het abonnements bereik toewijzen](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecteer het specifieke abonnement waaraan u de toepassing wilt toewijzen.

   ![Abonnement selecteren voor toewijzing](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Als u het gewenste abonnement niet ziet, selecteert u **globaal abonnementen filter**. Zorg ervoor dat het gewenste abonnement is geselecteerd voor de portal.

1. Klik op **Toegangsbeheer (IAM)** .
1. Selecteer **roltoewijzing toevoegen**.
1. Selecteer de rol die u aan de toepassing wilt toewijzen. Als u bijvoorbeeld wilt toestaan dat de toepassing acties uitvoert zoals **opnieuw opstarten**, exemplaren **starten** en **stoppen** , selecteert u de rol **Inzender** .  Meer informatie over de [beschik bare rollen](../../role-based-access-control/built-in-roles.md) : Azure AD-toepassingen worden standaard niet weer gegeven in de beschik bare opties. Zoek de naam en selecteer deze om uw toepassing te vinden.

   ![Selecteer de rol die u aan de toepassing wilt toewijzen](./media/howto-create-service-principal-portal/select-role.png)

1. Selecteer **opslaan** voltooien van de rol toe te wijzen. U ziet uw toepassing in de lijst met gebruikers die zijn toegewezen aan een rol voor dat bereik.

De Service-Principal is ingesteld. U kunt deze gebruiken om uw scripts of apps uit te voeren. In de volgende sectie ziet u hoe u waarden kunt ophalen die nodig zijn bij het programmatisch aanmelden.

## <a name="get-values-for-signing-in"></a>Waarden ophalen voor aanmelden

Wanneer u zich programmatisch aanmeldt, moet u de Tenant-ID door geven aan uw verificatie aanvraag. U hebt ook de ID voor uw toepassing en een verificatie sleutel nodig. U kunt deze waarden als volgt ophalen:

1. Selecteer **Azure Active Directory**.
1. Selecteer uw toepassing in **app-registraties** in azure AD.
1. Kopieer de Directory-ID (Tenant) en sla deze op in de code van uw toepassing.

    ![Kopieer de directory (Tenant-ID) en sla deze op in uw app-code](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Kopieer de **Toepassings-id** en sla deze op in uw toepassingscode.

   ![De toepassings-ID (client) kopiëren](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certificaten en geheimen
Daemon-toepassingen kunnen twee soorten referenties gebruiken om te verifiëren met Azure AD: certificaten en toepassings geheimen.  We raden u aan een certificaat te gebruiken, maar u kunt ook een nieuw toepassings geheim maken.

### <a name="upload-a-certificate"></a>Een certificaat uploaden

U kunt een bestaand certificaat gebruiken als u er een hebt.  Desgewenst kunt u een zelfondertekend certificaat maken voor test doeleinden. Open Power shell en voer [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) uit met de volgende para meters om een zelfondertekend certificaat te maken in het certificaat archief van de gebruiker op uw computer: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Dit certificaat exporteren naar een bestand met behulp van de MMC-module [gebruikers certificaat beheren](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) , toegankelijk via het configuratie scherm van Windows.

Het certificaat uploaden:

1. Selecteer **certificaten & geheimen**.
1. Selecteer **certificaat uploaden** en selecteer het certificaat (een bestaand certificaat of het zelfondertekende certificaat dat u hebt geëxporteerd).

    ![Selecteer certificaat uploaden en selecteer het account dat u wilt toevoegen.](./media/howto-create-service-principal-portal/upload-cert.png)

1. Selecteer **Toevoegen**.

Nadat u het certificaat hebt geregistreerd bij uw toepassing in de portal voor toepassings registratie, moet u de client toepassings code inschakelen om het certificaat te gebruiken.

### <a name="create-a-new-application-secret"></a>Een nieuw toepassings geheim maken

Als u ervoor kiest geen certificaat te gebruiken, kunt u een nieuw toepassings geheim maken.

1. Selecteer **certificaten & geheimen**.
1. Selecteer **client geheimen-> nieuw client Secret**.
1. Geef een beschrijving op van het geheim en een duur. Wanneer u klaar bent, selecteert u **toevoegen**.

   Nadat het client geheim is opgeslagen, wordt de waarde van het client geheim weer gegeven. Kopieer deze waarde omdat u de sleutel niet later kunt ophalen. U geeft de sleutel waarde op met de toepassings-ID om u aan te melden als de toepassing. Bewaar de sleutelwaarde op een locatie waar de toepassing deze kan ophalen.

   ![Kopieer de geheime waarde omdat u deze later niet kunt ophalen](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Toegangs beleid voor resources configureren
Houd er rekening mee dat u extra machtigingen moet configureren voor bronnen waartoe uw toepassing toegang moet hebben. U moet bijvoorbeeld ook [het toegangs beleid van een sleutel kluis bijwerken](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) om uw toepassing toegang te geven tot sleutels, geheimen of certificaten.  

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar uw sleutel kluis en selecteer **toegangs beleid**.  
1. Selecteer **toegangs beleid toevoegen**en selecteer vervolgens de sleutel, het geheim en de certificaat machtigingen die u uw toepassing wilt verlenen.  Selecteer de service-principal die u eerder hebt gemaakt.
1. Selecteer **toevoegen** om het toegangs beleid toe te voegen en **Sla** vervolgens op om uw wijzigingen door te voeren.
    toegangs beleid ![toevoegen](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>Vereiste machtigingen

U moet over voldoende machtigingen beschikken om een toepassing te registreren bij uw Azure AD-Tenant en de toepassing toe te wijzen aan een rol in uw Azure-abonnement.

### <a name="check-azure-ad-permissions"></a>Azure AD-machtigingen controleren

1. Selecteer **Azure Active Directory**.
1. Noteer uw rol. Als **u de gebruikersrol** hebt, moet u ervoor zorgen dat niet-beheerders toepassingen kunnen registreren.

   ![Zoek uw rol. Als u een gebruiker bent, moet u ervoor zorgen dat niet-beheerders apps kunnen registreren](./media/howto-create-service-principal-portal/view-user-info.png)

1. Selecteer **gebruikers instellingen**in het linkerdeel venster.
1. Controleer de instelling **app-registraties** . Deze waarde kan alleen worden ingesteld door een beheerder. Als deze instelling is ingesteld op **Ja**, kan elke gebruiker in de Azure AD-Tenant een app registreren.

Als de instelling app-registraties is ingesteld op **Nee**, kunnen alleen gebruikers met een rol beheerder deze typen toepassingen registreren. Bekijk de [beschik bare rollen](../users-groups-roles/directory-assign-admin-roles.md#available-roles) en [rolmachtigingen](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) voor meer informatie over beschik bare beheerders rollen en de specifieke machtigingen in azure AD die aan elke rol worden gegeven. Als uw account is toegewezen aan de gebruikersrol, maar de instelling van de app-registratie is beperkt tot gebruikers met beheerders rechten, vraagt u uw beheerder om u toe te wijzen aan een van de beheerders rollen die alle aspecten van app-registraties kunnen maken en beheren, of om gebruikers in staat te stellen om apps registreren.

### <a name="check-azure-subscription-permissions"></a>Azure-abonnements machtigingen controleren

In uw Azure-abonnement moet uw account beschikken over `Microsoft.Authorization/*/Write` toegang om een AD-app toe te wijzen aan een rol. Deze toegang wordt verleend via de rol [Eigenaar](../../role-based-access-control/built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](../../role-based-access-control/built-in-roles.md#user-access-administrator). Als uw account is toegewezen aan de rol **Inzender** , hebt u niet de juiste machtigingen. Er wordt een fout bericht weer gegeven wanneer u een service-principal aan een rol probeert toe te wijzen.

Uw abonnements machtigingen controleren:

1. Selecteer uw account in de rechter bovenhoek en selecteer **...-> mijn machtigingen**.

   ![Uw account en uw gebruikers machtigingen selecteren](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Selecteer in de vervolg keuzelijst het abonnement waarvoor u de Service-Principal wilt maken in. Selecteer vervolgens **Klik hier om de volledige toegangs gegevens voor dit abonnement weer te geven**.

   ![Selecteer het abonnement waarvoor u de Service-Principal wilt maken in](./media/howto-create-service-principal-portal/view-details.png)

1. Selecteer **roltoewijzingen** om uw toegewezen rollen weer te geven en te bepalen of u voldoende machtigingen hebt om een AD-app toe te wijzen aan een rol. Als dat niet het geval is, vraagt u uw abonnements beheerder om u toe te voegen aan de rol beheerder van gebruikers toegang. In de volgende afbeelding is de gebruiker toegewezen aan de rol eigenaar, wat betekent dat de gebruiker over de juiste machtigingen beschikt.

   ![In dit voor beeld ziet u dat de gebruiker is toegewezen aan de rol eigenaar](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [Access Control op basis van rollen](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het opgeven van beveiligings beleid.  
* Zie Azure Resource Manager-bewerkingen van de [resource provider](../../role-based-access-control/resource-provider-operations.md)voor een lijst met beschik bare acties die kunnen worden verleend of geweigerd aan gebruikers.
