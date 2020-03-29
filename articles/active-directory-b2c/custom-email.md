---
title: Aangepaste e-mailverificaties
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van de verificatie-e-mail die naar uw klanten wordt verzonden wanneer ze zich aanmelden om uw Azure AD B2C-toepassingen te gebruiken.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671634"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Aangepaste e-mailverificatie in Azure Active Directory B2C

Gebruik aangepaste e-mail in Azure Active Directory B2C (Azure AD B2C) om aangepaste e-mail te verzenden naar gebruikers die zich aanmelden om uw toepassingen te gebruiken. Door [DisplayControls](display-controls.md) (momenteel in preview) en een externe e-mailprovider te gebruiken, u uw eigen e-mailsjabloon en *Van:* adres en onderwerp gebruiken, evenals ondersteuning van lokalisatie en aangepaste OTP-instellingen (One Time Time Password).

Aangepaste e-mailverificatie vereist het gebruik van een externe e-mailprovider zoals [SendGrid](https://sendgrid.com) of [SparkPost,](https://sparkpost.com)een aangepaste REST-API of een op HTTP gebaseerde e-mailprovider (inclusief uw eigen e-mailprovider). In dit artikel wordt beschreven dat een oplossing wordt ingesteld die SendGrid gebruikt.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Een SendGrid-account maken

Als u er nog geen hebt, moet u eerst een SendGrid-account instellen (Azure-klanten kunnen elke maand 25.000 gratis e-mails ontgrendelen). Zie de sectie [SendGrid-account maken](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) van [E-mail verzenden met SendGrid met Azure](../sendgrid-dotnet-how-to-send-email.md)voor installatie-instructies.

Zorg ervoor dat u de sectie voltooit waarin u [een SendGrid API-sleutel maakt.](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key) Noteer de API-sleutel voor gebruik in een latere stap.

## <a name="create-azure-ad-b2c-policy-key"></a>Azure AD B2C-beleidssleutel maken

Sla vervolgens de SendGrid API-sleutel op in een Azure AD B2C-beleidssleutel waarnaar uw beleid kan worden verwezen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies uw Azure AD B2C-map.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina Overzicht de optie **Identity Experience Framework**.
1. Selecteer **Beleidssleutels** en selecteer **Vervolgens Toevoegen**.
1. Kies **Options**voor `Manual`Opties .
1. Voer een **naam** in voor de beleidssleutel. Bijvoorbeeld `SendGridSecret`. Het voorvoegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer in **Secret**het geheim van uw klant in dat u eerder hebt opgenomen.
1. Selecteer **voor sleutelgebruik**. `Signature`
1. Selecteer **Maken**.

## <a name="create-sendgrid-template"></a>SendGrid-sjabloon maken

Als een SendGrid-account is gemaakt en sendgrid-API-sleutel is opgeslagen in een Azure AD B2C-beleidssleutel, maakt u een [dynamische transactionele sjabloon](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid .

1. Open op de site SendGrid de pagina [transactionele sjablonen](https://sendgrid.com/dynamic_templates) en selecteer **Sjabloon maken**.
1. Voer een unieke `Verification email` sjabloonnaam like in en selecteer **Opslaan**.
1. Als u wilt beginnen met het bewerken van uw nieuwe sjabloon, selecteert u **Versie toevoegen**.
1. Selecteer **Codeeditor** en ga **verder**.
1. Plak in de HTML-editor de volgende HTML-sjabloon of gebruik uw eigen HTML-sjabloon. De `{{otp}}` `{{email}}` parameters en parameters worden dynamisch vervangen door de eenmalige wachtwoordwaarde en het e-mailadres van de gebruiker.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Vouw **instellingen** aan de linkerkant uit `{{subject}}`en voer voor Onderwerp **e-mail**in .
1. Selecteer **Sjabloon opslaan**.
1. Ga terug naar de pagina **Transactionele sjablonen** door de pijl-terug te selecteren.
1. Noteer de **id** van de sjabloon die u hebt gemaakt voor gebruik in een latere stap. Bijvoorbeeld `d-989077fbba9746e89f3f6411f596fb96`. U geeft deze id op wanneer u [de claimtransformatie toevoegt.](#add-the-claims-transformation)

## <a name="add-azure-ad-b2c-claim-types"></a>Azure AD B2C-claimtypen toevoegen

Voeg in uw beleid de volgende `<ClaimsSchema>` claimtypen toe aan het element binnen `<BuildingBlocks>`.

Deze claimtypen zijn nodig om het e-mailadres te genereren en te verifiëren met behulp van een OTP-code (een eenmalig wachtwoord).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>De claimtransformatie toevoegen

Vervolgens hebt u een claimtransformatie nodig om een JSON-tekenreeksclaim uit te zetten die de hoofdtekst is van het verzoek dat naar SendGrid wordt verzonden.

De structuur van het JSON-object wordt gedefinieerd door de id's in puntnotatie van de InputParameters en de TransformationClaimTypes van de InputClaims. Getallen in de dot notatie impliceren arrays. De waarden zijn afkomstig van de waarden van inputclaims en de eigenschappen 'Waarde' van InputParameters. Zie [JSON claims transformaties](json-transformations.md)voor meer informatie over JSON claims transformations.

Voeg de volgende claimtransformatie toe aan het `<ClaimsTransformations>` element binnen `<BuildingBlocks>`. Breng de volgende updates uit voor de XML voor claimtransformatie:

* Werk `template_id` de waarde InputParameter bij met de id van de transactionele sjabloon SendGrid transactioneel die u eerder hebt gemaakt in [de sjabloon SendGrid maken](#create-sendgrid-template).
* Werk `from.email` de adreswaarde bij. Gebruik een geldig e-mailadres om te voorkomen dat de verificatie-e-mail wordt gemarkeerd als spam.
* Werk de waarde `personalizations.0.dynamic_template_data.subject` van de parameter onderwerpregelinvoer bij met een onderwerpregel die geschikt is voor uw organisatie.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Gegevensuri-inhoudsdefinitie toevoegen

Onder de claimstransformaties binnen `<BuildingBlocks>`, voeg de volgende [ContentDefinition](contentdefinitions.md) toe om te verwijzen naar de versie 2.0.0-gegevens URI:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Een DisplayControl maken

Een verificatieweergavebesturingselement wordt gebruikt om het e-mailadres te verifiëren met een verificatiecode die naar de gebruiker wordt verzonden.

Dit voorbeeld weergavebesturingselement is geconfigureerd om:

1. Verzamel `email` het type adresclaim van de gebruiker.
1. Wacht tot de gebruiker `verificationCode` het claimtype de code geeft die naar de gebruiker is verzonden.
1. Breng `email` de achterkant terug naar het zelfverklaarde technische profiel dat een verwijzing naar dit weergavebesturingselement heeft.
1. Met `SendCode` behulp van de actie, het genereren van een OTP-code en stuur een e-mail met de OTP-code naar de gebruiker.

![E-mailactie verificatiecode verzenden](media/custom-email/display-control-verification-email-action-01.png)

Voeg onder inhoudsdefinities nog steeds binnen `<BuildingBlocks>`, voeg de volgende [DisplayControl](display-controls.md) van type [VerificationControl](display-control-verification.md) toe aan uw beleid.

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Technische PROFIELEN OTP toevoegen

Het `GenerateOtp` technische profiel genereert een code voor het e-mailadres. Het `VerifyOtp` technische profiel verifieert de code die is gekoppeld aan het e-mailadres. U de configuratie van de indeling en het verstrijken van het eenmalige wachtwoord wijzigen. Zie [Een eenmalig wachtwoordtechnisch profiel definiëren](one-time-password-technical-profile.md)voor meer informatie over technische OTP-profielen.

Voeg de volgende technische `<ClaimsProviders>` profielen toe aan het element.

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Een technisch rest-API-profiel toevoegen

Dit technische profiel VAN de REST API genereert de e-mailinhoud (met behulp van de SendGrid-indeling). Zie Een RESTful technisch profiel definiëren voor meer informatie over technische profielen van [RESTful.](restful-technical-profile.md)

Voeg net als bij de technische profielen van `<ClaimsProviders>` OTP de volgende technische profielen toe aan het element.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Een verwijzing naar de DisplayControl maken

Voeg in de laatste stap een verwijzing toe naar de DisplayControl die u hebt gemaakt. Vervang uw `LocalAccountSignUpWithLogonEmail` bestaande zelfverklaarde technische profiel door het volgende als u een eerdere versie van het Azure AD B2C-beleid hebt gebruikt. Dit technische `DisplayClaims` profiel wordt gebruikt met een verwijzing naar de DisplayControl.

Zie [Zelfgeclaimd technisch profiel](restful-technical-profile.md) en [DisplayControl](display-controls.md)voor meer informatie.

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[Optioneel] Uw e-mail lokaliseren

Als u de e-mail wilt lokaliseren, moet u gelokaliseerde tekenreeksen naar SendGrid of uw e-mailprovider verzenden. Bijvoorbeeld om het e-mailonderwerp, de hoofdtekst, uw codebericht of de handtekening van de e-mail te lokaliseren. Hiervoor u de transformatie [van getlocalizedstringstransformation-claims](string-transformations.md) gebruiken om gelokaliseerde tekenreeksen naar claimtypen te kopiëren. In `GenerateSendGridRequestBody` de claimtransformatie, die de JSON-payload genereert, worden invoerclaims gebruikt die de gelokaliseerde tekenreeksen bevatten.

1. Definieer in uw beleid de volgende tekenreeksclaims: onderwerp, bericht, codeIntro en handtekening.
1. Definieer een [GetLocalizedStringsTransformation-claimtransformatie](string-transformations.md) om gelokaliseerde tekenreekswaarden te vervangen in de claims van stap 1.
1. Wijzig `GenerateSendGridRequestBody` de transformatie van claims om invoerclaims te gebruiken met het volgende XML-fragment.
1. Werk de sjabloon SendGrind bij om dynamische parameters te gebruiken in plaats van alle tekenreeksen die worden gelokaliseerd door Azure AD B2C.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
    <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
    <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
    <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>Volgende stappen

U een voorbeeld van een aangepast verificatiebeleid voor e-mail vinden op GitHub:

[Aangepaste e-mailverificatie - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Zie [Een RESTful-technisch profiel definiëren in een aangepast Aangepast Azure B2C-beleid](restful-technical-profile.md)voor informatie over het gebruik van een aangepaste REST API of een http-gebaseerde SMTP-e-mailprovider.
