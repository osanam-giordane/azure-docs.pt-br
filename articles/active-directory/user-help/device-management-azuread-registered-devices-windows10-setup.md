---
title: Configurar dispositivos registrados no Azure Active Directory | Microsoft Docs
description: Saiba como configurar dispositivos registrados no Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 0c38c0160cea51940ac5b04ee64095c6a6f25b5d
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414667"
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Configurar dispositivos Windows 10 registrados no Azure Active Directory

Com o gerenciamento de dispositivos no Azure AD (Azure Active Directory), você pode garantir que os usuários acessem recursos usando dispositivos que atendam aos padrões de segurança e conformidade. Para obter mais informações, confira [Introdução ao gerenciamento de dispositivos no Azure Active Directory](../device-management-introduction.md).

Se desejar habilitar o cenário **BYOD (traga seu próprio dispositivo)**, você poderá fazer isso configurando dispositivos registrados do Azure AD. No Azure AD, é possível configurar dispositivos registrados no Azure AD para Windows 10, iOS, Android e macOS. Esse artigo fornece as etapas relacionadas para dispositivos Windows 10. 


## <a name="before-you-begin"></a>Antes de começar

Para registrar um dispositivo Windows 10, o serviço de registro do dispositivo deverá ser configurado para permitir que você registre dispositivos. Além disso, você precisa ter menos dispositivos registrados do que o máximo configurado. Para mais informações, consulte [Definir configurações do dispositivo](../devices/device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>O que você deve saber

Ao registrar um dispositivo, você deve ter o seguinte em mente:

- O Windows registra o dispositivo no diretório da organização no Azure AD

- Você talvez precise percorrer o desafio de autenticação multifator. Esse desafio é configurável por um administrador de TI.

- O Azure AD verifica se o dispositivo requer o registro de gerenciamento de dispositivo móvel e faz o registro, se aplicável.

- Se você for um usuário gerenciado, o Windows levará para a área de trabalho por meio da conexão automático.

- Se você for um usuário federado, você será levado para a tela de logon do Windows para inserir suas credenciais.


## <a name="registering-a-device"></a>Registrando um dispositivo

Esta seção fornece as etapas para registrar seu dispositivo Windows 10 ao Azure AD. Um dispositivo registrado com êxito é exibido com uma entrada de **Conta corporativa ou de estudante**.

![Registrar ](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Para registrar seu dispositivo Windows 10:**

1. No menu **Iniciar**, clique em **Configurações**.

    ![Configurações](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Clique em **Contas**.

    ![Contas](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Clique em **Acesso corporativo ou de estudante**.

    ![Acesso corporativo ou de estudante](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Na caixa de diálogo **Acesso corporativo ou de estudante**, clique em **Conectar**.

    ![Connect](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Na caixa de diálogo **Configurar uma conta corporativa ou de estudante**, insira o nome da conta (por exemplo, someone@example.com) e clique em **Avançar**.

    ![Connect](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Na caixa de diálogo **Digitar senha**, digite sua senha e, em seguida, clique em **Avançar**.

    ![Connect](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Na caixa de diálogo **Tudo pronto**, clique em **Concluído**.

    ![Connect](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Verificação

Para verificar se um dispositivo é associado ao seu Azure AD, examine a caixa de diálogo **Acesso corporativo ou de estudante** no seu dispositivo.

![Registrar ](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Como alternativa, você pode examinar as configurações do dispositivo no portal do Azure AD.

![Registrar ](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações, confira a [introdução ao gerenciamento de dispositivos no Azure Active Directory](../device-management-introduction.md).

- Para obter mais informações sobre como gerenciar dispositivos no portal do Azure AD, confira [Gerenciar dispositivos usando o portal do Azure](../device-management-azure-portal.md).




