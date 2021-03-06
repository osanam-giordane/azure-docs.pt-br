---
title: Gerenciar a personalização de SSO e de token com políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Saiba mais como gerenciar SSO e personalização de token com políticas personalizadas.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 811fb8b2de59c9d324ab4acb8b0f51b4cec80aee
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441790"
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C: Gerenciar a personalização de SSO e de tokens com políticas personalizadas
Usar políticas personalizadas fornece algum controle sobre seu token, sessão e configurações de logon único (SSO) por meio de políticas internas.  Para saber o que faz cada configuração, confira a documentação [aqui](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Configuração de declarações e de tempos de vida de token
Para alterar as configurações em seus tempos de vida de token, você precisa adicionar um elemento `<ClaimsProviders>` ao arquivo de terceira parte confiável da política que você deseja afetar.  O elemento `<ClaimsProviders>` é filho do `<TrustFrameworkPolicy>`.  Nele, você precisará colocar as informações que afetam os tempos de vida de token.  O XML se parece com este exemplo:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

**Tempos de vida de token de acesso** – o tempo de vida de token de acesso pode ser alterado modificando o valor dentro do `<Item>` com Key="token_lifetime_secs" em segundos.  O valor padrão interno é de 3600 segundos (60 minutos).

**Tempo de vida do token de ID** – o tempo de vida do token de ID pode ser alterado por meio da modificação do valor de `<Item>` comKey="id_token_lifetime_secs" em segundos.  O valor padrão interno é de 3600 segundos (60 minutos).

**Tempo de vida do token de atualização** – o tempo de vida do token de atualização pode ser alterado por meio da modificação do valor de `<Item>` com Key="refresh_token_lifetime_secs" em segundos.  O valor padrão interno é 1209600 segundos (14 dias).

**Atualizar o tempo de vida de janela deslizante token** – se quiser definir um tempo de vida de janela deslizante para o token de atualização, modifique o valor de `<Item>` com Key="rolling_refresh_token_lifetime_secs" em segundos.  O valor padrão interno é 7776000 (90 dias).  Se não quiser impor um tempo de vida de janela deslizante, substitua essa linha por:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Declaração do emissor (iss)** – se você quiser alterar a declaração do emissor (iss), modifique o valor no `<Item>` com Key="IssuanceClaimPattern".  Os valores aplicáveis são `AuthorityAndTenantGuid` e `AuthorityWithTfp`.

**Configuração de declaração que representa a ID de política** – as opções para definir esse valor são TFP (política de confiança da estrutura) e ACR (referência do contexto de autenticação).  
É recomendável definir isso como TFP; para fazer isso, verifique se o `<Item>` com Key="AuthenticationContextReferenceClaimPattern" existe e se o valor é `None`.
No seu item do `<OutputClaims>`, adicione este elemento:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
Para o ACR, remova o `<Item>` com Key="AuthenticationContextReferenceClaimPattern".

**Declaração de assunto (sub)** – essa opção é padronizada para a ObjectID, se você quiser trocar para `Not Supported`, faça o seguinte:

Substituir esta linha 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
com esta linha:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Comportamento da sessão e SSO

Para alterar o comportamento da sessão e as configurações de SSO, será necessário adicionar um elemento `<UserJourneyBehaviors>` ao elemento `<RelyingParty>`.  O elemento `<UserJourneyBehaviors>` deve seguir imediatamente o `<DefaultUserJourney>`.  O interior do seu elemento `<UserJourneyBehavors>` deve ter esta aparência:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Configuração de logon único (SSO)** – para alterar a configuração de logon único, você precisa modificar o valor de `<SingleSignOn>`.  Os valores aplicáveis são `Tenant`, `Application`, `Policy` e `Disabled`. 

**Tempo de vida de sessão do aplicativo Web (minutos)** – para alterar o tempo de vida de sessão do aplicativo Web, você precisa modificar o valor do elemento `<SessionExpiryInSeconds>`.  O valor padrão em políticas internas é de 86400 segundos (1440 minutos).

**Tempo limite de sessão de aplicativo Web** – para alterar o tempo limite de sessão do aplicativo Web, será necessário modificar o valor de `<SessionExpiryType>`.  Os valores aplicáveis são `Absolute` e `Rolling`.
