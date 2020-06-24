---
title: Aangepaste e-mail verificaties
titleSuffix: Azure AD B2C
description: Meer informatie over het aanpassen van de verificatie-e-mail die naar uw klanten wordt verzonden wanneer deze zich aanmeldt om uw Azure AD B2C-toepassingen te gebruiken.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 63a2b462fe08cb37ca655aa91474601decce8000
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85202837"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Aangepaste e-mail verificatie in Azure Active Directory B2C

Gebruik aangepaste e-mail in Azure Active Directory B2C (Azure AD B2C) om aangepaste e-mail te verzenden naar gebruikers die zich registreren voor het gebruik van uw toepassingen. Met behulp van [DisplayControls](display-controls.md) (momenteel in Preview) en een e-mail provider van derden kunt u uw eigen e-mail sjabloon gebruiken, *van:* adres en onderwerp, en ondersteuning bieden voor lokalisatie en aangepaste otp-instellingen (eenmalig wacht woord).

Voor aangepaste e-mail verificatie moet een e-mail provider van derden worden gebruikt, zoals [SendGrid](https://sendgrid.com) of [SparkPost](https://sparkpost.com), een aangepaste rest API of een op http gebaseerde e-mail provider (inclusief uw eigen). In dit artikel wordt beschreven hoe u een oplossing instelt die gebruikmaakt van SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Een SendGrid-account maken

Als u er nog geen hebt, kunt u beginnen met het instellen van een SendGrid-account (Azure-klanten kunnen 25.000 gratis e-mails per maand vergren delen). Zie de sectie [een SendGrid-account maken](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) voor [informatie over het verzenden van e-mail berichten met behulp van SendGrid met Azure](../sendgrid-dotnet-how-to-send-email.md)voor instructies bij de installatie.

Zorg ervoor dat u de sectie waarin u [een SENDGRID API-sleutel maakt](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key), voltooit. Registreer de API-sleutel voor gebruik in een latere stap.

## <a name="create-azure-ad-b2c-policy-key"></a>Azure AD B2C-beleids sleutel maken

Sla vervolgens de SendGrid API-sleutel op in een Azure AD B2C-beleids sleutel voor uw beleids regels waarnaar moet worden verwezen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies uw Azure AD B2C Directory.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies voor **Opties** `Manual` .
1. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `SendGridSecret`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer in het **geheim**uw client geheim in dat u eerder hebt vastgelegd.
1. Selecteer voor **sleutel gebruik** `Signature` .
1. Selecteer **Maken**.

## <a name="create-sendgrid-template"></a>SendGrid-sjabloon maken

Als er een SendGrid-account is gemaakt en een SendGrid-API-sleutel die is opgeslagen in een Azure AD B2C-beleids sleutel, maakt u een SendGrid- [sjabloon voor dynamische transactionele acties](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/).

1. Open de pagina [transactionele sjablonen](https://sendgrid.com/dynamic_templates) op de site SendGrid en selecteer **sjabloon maken**.
1. Voer een unieke sjabloon naam in `Verification email` , zoals en selecteer vervolgens **Opslaan**.
1. Selecteer **versie toevoegen**om te beginnen met het bewerken van de nieuwe sjabloon.
1. Selecteer **code-editor** en **Ga vervolgens door**.
1. Plak in de HTML-editor de volgende HTML-sjabloon of gebruik uw eigen. De `{{otp}}` `{{email}}` para meters en worden dynamisch vervangen door de waarde voor eenmalig wacht woord en het e-mail adres van de gebruiker.

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

1. Vouw **instellingen** aan de linkerkant uit en voer voor **e-mail onderwerp**in `{{subject}}` .
1. Selecteer **sjabloon opslaan**.
1. Ga terug naar de pagina **transactionele sjablonen** door de pijl-terug te selecteren.
1. Noteer de **id** van de sjabloon die u hebt gemaakt voor gebruik in een latere stap. Bijvoorbeeld `d-989077fbba9746e89f3f6411f596fb96`. U geeft deze ID op wanneer u [de claim transformatie toevoegt](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Azure AD B2C claim typen toevoegen

Voeg in uw beleid de volgende claim typen toe aan het- `<ClaimsSchema>` element binnen `<BuildingBlocks>` .

Deze claim typen zijn nodig voor het genereren en verifiëren van het e-mail adres met een OTP-code (eenmalig wacht woord).

```xml
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

## <a name="add-the-claims-transformation"></a>De claim transformatie toevoegen

Vervolgens hebt u een claim transformatie nodig voor het uitvoeren van een JSON-teken reeks claim die de hoofd tekst van de aanvraag is die wordt verzonden naar SendGrid.

De structuur van het JSON-object wordt gedefinieerd door de Id's in punt notatie van de invoer parameters en de TransformationClaimTypes van de InputClaims. Getallen in de punt notatie impliceren matrices. De waarden zijn afkomstig van de waarden van de InputClaims en de waarde van de eigenschap input parameters. Zie voor meer informatie over JSON-claim transformaties [JSON-claim transformaties](json-transformations.md).

Voeg de volgende claim transformatie toe aan het- `<ClaimsTransformations>` element binnen `<BuildingBlocks>` . Voer de volgende updates uit voor de XML van de claim transformatie:

* Werk de `template_id` waarde input parameter bij met de id van de SendGrid-transactionele sjabloon die u eerder hebt gemaakt in [SendGrid-sjabloon maken](#create-sendgrid-template).
* Werk de `from.email` adres waarde bij. Gebruik een geldig e-mail adres om te voor komen dat de verificatie-e-mail als spam wordt gemarkeerd.
* Werk de waarde van de `personalizations.0.dynamic_template_data.subject` invoer parameter voor de regel onderwerp bij met een onderwerpregel die geschikt is voor uw organisatie.

```xml
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

## <a name="add-datauri-content-definition"></a>Definitie van DataUri-inhoud toevoegen

Voeg onder de claim transformaties binnen de `<BuildingBlocks>` volgende [ContentDefinition](contentdefinitions.md) toe om te verwijzen naar de gegevens-URI van versie 2.0.0:

```xml
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Een DisplayControl maken

Een besturings element voor controle wordt gebruikt om het e-mail adres te verifiëren met een verificatie code die wordt verzonden naar de gebruiker.

Dit voor beeld wordt weer gegeven met de volgende instellingen:

1. Verzamel het `email` adres claim type van de gebruiker.
1. Wacht totdat de gebruiker het `verificationCode` claim type heeft verstrekt met de code die naar de gebruiker wordt verzonden.
1. Ga terug `email` naar het zelfondertekende technische profiel dat een verwijzing naar dit weergave besturings element bevat.
1. `SendCode`Genereer een otp-code met behulp van de actie en verzend een e-mail met de otp-code naar de gebruiker.

![E-mail actie voor verificatie code verzenden](media/custom-email/display-control-verification-email-action-01.png)

Voeg onder inhouds definities nog steeds `<BuildingBlocks>` de volgende [DisplayControl](display-controls.md) van het type [VerificationControl](display-control-verification.md) toe aan uw beleid.

```xml
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

## <a name="add-otp-technical-profiles"></a>Technische OTP-profielen toevoegen

Het `GenerateOtp` technische profiel genereert een code voor het e-mail adres. Het `VerifyOtp` technische profiel controleert de code die is gekoppeld aan het e-mail adres. U kunt de configuratie van de indeling en de verval datum van het eenmalige wacht woord wijzigen. Zie [een eenmalig wacht woord technische profiel definiëren](one-time-password-technical-profile.md)voor meer informatie over technische otp-profielen.

Voeg de volgende technische profielen toe aan het- `<ClaimsProviders>` element.

```xml
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

## <a name="add-a-rest-api-technical-profile"></a>Een REST API technisch profiel toevoegen

In dit REST API technische profiel wordt de e-mail inhoud gegenereerd (met de SendGrid-indeling). Zie een onderliggend [technisch profiel definiëren](restful-technical-profile.md)voor meer informatie over onderhouds technische profielen.

Net als bij de technische profielen voor OTP, voegt u de volgende technische profielen toe aan het- `<ClaimsProviders>` element.

```xml
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

In de laatste stap voegt u een verwijzing toe naar het DisplayControl dat u hebt gemaakt. Vervang uw bestaande, `LocalAccountSignUpWithLogonEmail` zelfondertekende technische profiel met het volgende als u een eerdere versie van Azure AD B2C-beleid hebt gebruikt. Dit technische profiel wordt gebruikt `DisplayClaims` met een verwijzing naar de DisplayControl.

Zie voor meer informatie [het zelf beweringen technisch profiel](restful-technical-profile.md) en weer [gave](display-controls.md).

```xml
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

## <a name="optional-localize-your-email"></a>Beschrijving Uw e-mail adres lokaliseren

Als u het e-mail bericht wilt lokaliseren, moet u gelokaliseerde teken reeksen verzenden naar SendGrid of uw e-mail provider. Bijvoorbeeld voor het lokaliseren van het e-mail onderwerp, de hoofd tekst, het code bericht of de hand tekening van de e-mail. Hiervoor kunt u de [GetLocalizedStringsTransformation](string-transformations.md) -claim transformatie gebruiken om gelokaliseerde teken reeksen te kopiëren naar claim typen. In de `GenerateSendGridRequestBody` claim transformatie, waarmee de JSON-nettolading wordt gegenereerd, gebruikt invoer claims die de gelokaliseerde teken reeksen bevatten.

1. In uw beleid definieert u de volgende teken reeks claims: onderwerp, bericht, codeIntro en hand tekening.
1. Definieer een [GetLocalizedStringsTransformation](string-transformations.md) -claim transformatie om gelokaliseerde teken reeks waarden in de claims uit stap 1 te vervangen.
1. Wijzig de `GenerateSendGridRequestBody` claim transformatie voor het gebruik van invoer claims met het volgende XML-code fragment.
1. Werk uw SendGrind-sjabloon bij om dynamische para meters te gebruiken in plaats van alle teken reeksen die worden gelokaliseerd door Azure AD B2C.

```xml
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

U kunt een voor beeld van een aangepast e-mail verificatie beleid vinden op GitHub:

[Aangepaste e-mail verificatie-DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Voor informatie over het gebruik van een aangepaste REST API of een SMTP-e-mail provider op basis van HTTP, raadpleegt u een onderliggend [technisch profiel definiëren in een Azure AD B2C aangepast beleid](restful-technical-profile.md).
