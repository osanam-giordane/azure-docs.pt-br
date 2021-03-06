---
title: 'Tutorial: integração do Azure Active Directory ao Atlassian Cloud | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2018
ms.author: jeedes
ms.openlocfilehash: 68613b8613a2e5a9139b83eb23e66884659efc47
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114927"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Tutorial: Integração do Azure Active Directory com o Atlassian Cloud

Neste tutorial, você aprenderá a integrar o Atlassian Cloud ao Azure AD (Azure Active Directory).

A integração do Atlassian Cloud ao Azure AD oferece os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Atlassian Cloud.
- Você pode habilitar os usuários a fazer logon automaticamente no Atlassian Cloud (Logon Único) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para obter mais informações sobre a integração de aplicativos de SaaS (software como serviço) ao Azure AD, consulte [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Atlassian Cloud, você precisa dos seguintes itens:

- Uma assinatura do Azure AD.
- Para habilitar logon único do SAML (Security Assertion Markup Language) para produtos Atlassian Cloud, é necessário configurar o Atlassian Access. Saiba mais sobre o [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Ao testar as etapas deste tutorial, recomendamos que você não use um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste.
O cenário descrito no tutorial consiste em dois blocos de construção principais:

* Adição do Atlassian Cloud da galeria
* configurar e testar o logon único do AD do Azure

## <a name="add-atlassian-cloud-from-the-gallery"></a>Adicionar o Atlassian Cloud por meio da galeria
Para configurar a integração do Atlassian Cloud ao Azure AD, adicione o Atlassian Cloud da galeria à sua lista de aplicativos SaaS gerenciados, fazendo o seguinte:

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![O botão Azure Active Directory][1]

2. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![O painel Aplicativos Empresariais][2]
    
3. Para adicionar um aplicativo, selecione **Novo aplicativo**.

    ![O botão “Novo aplicativo”][3]

4. Na caixa de pesquisa, digite **Atlassian Cloud**, na lista de resultados, selecione **Atlassian Cloud**e, em seguida, selecione **Adicionar**.

    ![Atlassian Cloud na lista de resultados](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Atlassian Cloud, com base em um usuário de teste chamado *Britta Simon*.

Para que o logon único funcione, o Azure AD precisa identificar o usuário do Atlassian Cloud e sua contraparte do Azure AD. Em outras palavras, você precisa estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Atlassian Cloud.

Para estabelecer a relação de vinculação, atribuir como o *nome de usuário* do Atlassian Cloud o mesmo valor que é atribuído ao *nome de usuário* do Azure AD.

Para configurar e testar o logon único do Azure AD com o Atlassian Cloud, você precisa concluir os blocos de construção nas seguintes seções.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você habilitará o logon único do Azure AD no portal do Azure e configurará o logon único em seu aplicativo Atlassian Cloud.

Para configurar o logon único do Azure AD com o Atlassian Cloud, faça o seguinte:

1. No portal do Azure, na página de integração de aplicativo do **Atlassian Cloud**, selecione **Logon único**.

    ![Link Configurar logon único][4]

2. Na janela **Logon único**, na caixa **Modo Logon único**, selecione **Logon baseado em SAML**.

    ![Janela de Logon único](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. Para configurar o aplicativo em modo **Iniciado por IDP**, no **Domínio e URLs do Atlassian Cloud**, faça o seguinte:

    ![Informações de logon único em Domínio e URLs do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url.png)
    
    a. Na caixa **Identificador**, digite uma URL com o padrão a seguir: `https://auth.atlassian.com/saml/<unique ID>`.
    
    b. Na caixa de **URL de Resposta**, digite uma URL com o seguinte padrão: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`.

    c. Marque **Mostrar configurações de URL avançadas**.

    d. Na caixa de **Estado de Retransmissão**, digite uma URL com o padrão a seguir: `https://<instancename>.atlassian.net`.

    > [!NOTE]
    > Os valores anteriores não são reais. Atualize esses valores com o Identificador e a URL de Resposta reais. Você obterá esses valores reais na tela Configuração de SAML do Atlassian Cloud, explicada posteriormente no tutorial.

4. Para configurar o aplicativo em modo iniciado por SP, selecione as **Mostrar configurações avançadas de URL** e, em seguida, na caixa **URL de Entrada**, digite uma URL com o padrão a seguir: `https://<instancename>.atlassian.net`.

    ![Informações de logon único em Domínio e URLs do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    > [!NOTE]
    > O sinal de URL de Entrada anterior não é real. Atualize o valor com a URL de Entrada real. Contate a [Equipe de suporte ao cliente do Atlassian Cloud](https://support.atlassian.com/) para obter esse valor.

5. Na seção **Certificado de Autenticação do SAML**, selecione **Certificado (Base64)** e salve o arquivo do certificado no computador.

    ![O link de download do Certificado](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png)

6. Seu aplicativo Atlassian Cloud espera encontrar as declarações do SAML em um formato específico, o que exige que você adicione mapeamentos de atributo personalizados de acordo com a sua configuração de Atributos de Token SAML. 

    Por padrão, o valor do **identificador de usuário** é mapeado para user.userprincipalname. Altere este valor para mapear para user.mail. Você também pode escolher qualquer outro valor apropriado de acordo com a configuração de sua organização, mas, na maioria dos casos, o email deve funcionar.

    ![O link de download do Certificado](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_attribute.png)

7. Clique em **Salvar**.

    ![O botão Salvar de Configurar Logon Único](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

8. Para abrir a janela **Configurar o logon**, na seção **Configuração do Atlassian Cloud**, selecione **Configurar Atlassian Cloud**.

9. Na seção **Referência Rápida**, copie a **ID da Entidade SAML** e a **URL do Serviço de Logon Único SAML**.

    ![Configuração do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png)

10. Para configurar o SSO para seu aplicativo, faça logon no portal do Atlassian usando com as credenciais de administrador.

11. É necessário verificar o domínio antes de configurar logon único. Para obter mais informações, consulte o documento de [verificação de domínio do Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

12. No painel à esquerda, selecione **Logon único SAML**. Se você ainda não fez isso, assine o Identity Manager da Atlassian.

    ![Configurar o logon único](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

13. Na janela de **Adicionar configuração SAML**, faça o seguinte:

    ![Configurar o logon único](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Na caixa **ID da Entidade do Provedor de Identidade**, cole o ID da entidade SAML que você copiou do portal do Azure.

    b. Na caixa **URL de SSO do Provedor de Identidade**, cole a URL de serviço de logon único SAML que você copiou do portal do Azure.

    c. Abra o certificado baixado do portal do Azure em arquivo .txt, copie o valor (sem as linhas *Begin Certificate* e *End Certificate*) e cole-os na caixa **Certificado X509 público**.
    
    d. Selecione **Salvar configuração**.
     
14. Para garantir que você configurou as URLs corretas, atualize as configurações do Azure AD fazendo o seguinte:

    ![Configurar o logon único](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Na janela de SAML, copie a **ID de Entidade do SP** e, em seguida, no portal da Azure, na seção **Domínio e URLs** do Atlassian Cloud, cole-o na caixa do **Identificador**.
    
    b. Na janela de SAML, copie a **URL do Serviço do Consumidor de Declaração de SP** e, em seguida, no portal da Azure, na seção **Domínio e URLs** do Atlassian Cloud, cole-o na caixa do **URL de Resposta**. A URL de logon é a URL do locatário do seu Atlassian Cloud.

    > [!NOTE]
    > Se você for um cliente existente, depois de atualizar os valores da **ID de identidade SP** e **URL do Serviço de Consumidor de Declaração de SP** no portal do Azure, selecione **Sim, atualize a configuração**. Se você for um novo cliente, você pode ignorar esta etapa.
    
15. No portal do Azure, selecione **Salvar**.

    ![Configurar o logon único](./media/atlassian-cloud-tutorial/tutorial_general_400.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você cria o usuário de teste Brenda Fernandes no portal do Azure fazendo o seguinte:

   ![Criar um usuário de teste do Azure AD][100]

1. No Portal do Azure, no painel esquerdo, selecione o botão **Azure Active Directory**.

    ![O botão Azure Active Directory](./media/atlassian-cloud-tutorial/create_aaduser_01.png)

2. Para exibir a lista de usuários, selecione **Usuários e grupos** > **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/atlassian-cloud-tutorial/create_aaduser_02.png)

3. Na janela **Todos os usuários**, selecione **Adicionar**.

    ![O botão Adicionar](./media/atlassian-cloud-tutorial/create_aaduser_03.png)

4. Na janela **Usuário**, faça o seguinte:

    ![A janela de usuário](./media/atlassian-cloud-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.

### <a name="create-an-atlassian-cloud-test-user"></a>Criar um usuário de teste do Atlassian Cloud

Para permitir que os usuários do Azure AD entrar no Atlassian Cloud, provisione as contas de usuário manualmente no Atlassian Cloud fazendo o seguinte:

1. No painel **Administração**, selecione **Usuários**.

    ![O link de Usuários do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Para criar um usuário no Atlassian Cloud, selecione **Convidar usuário**.

    ![Criar um usuário do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. Na caixa **Endereço de email**, insira o endereço de email do usuário e atribua o acesso ao aplicativo.

    ![Criar um usuário do Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Para enviar um convite por email para o usuário, selecione **Convidar usuários**. Um convite por email é enviado ao usuário e, depois de aceitar o convite, o usuário está ativo no sistema.

>[!NOTE]
>Você também pode criar usuários em massa clicando no botão **Criar em Massa** na seção **Usuários**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Britta Simon use o logon único do Azure concedendo a ela acesso ao Atlassian Cloud. Para fazer isso, faça o seguinte:

![Atribuir a função de usuário][200]

1. No portal do Azure, abra a exibição de **Aplicativos**, acesse a exibição de diretório e, em seguida, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![Atribuir usuário][201]

2. Na lista de **Aplicativos**, selecione **Atlassian Cloud**.

    ![O link para o Atlassian Cloud na lista de aplicativos](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)

3. No painel esquerdo, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

4. Selecione **Adicionar** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

5. Na janela **Usuários e grupos**, na lista **Usuários**, selecione **Britta Simon**.

6. Na janela **Usuários e grupos**, selecione **Selecionar**.

7. Na janela **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao selecionar o bloco **Atlassian Cloud** no Painel de Acesso, você deve entrar automaticamente no aplicativo Atlassian Cloud.
Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS ao Active Directory do Azure](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/atlassian-cloud-tutorial/tutorial_general_203.png
