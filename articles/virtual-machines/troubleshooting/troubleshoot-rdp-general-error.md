---
title: Solucionar um problema de erro geral de protocolo RDP em uma VM Windows no Azure | Microsoft Docs
description: Aprenda a solucionar um problema de erro geral de protocolo RDP em uma VM Windows no Azure | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 7fc0fbf3362d18284ad6a80afa6396b6be1270a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "71058012"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Solucionar um problema de erro geral de protocolo RDP em uma VM do Azure

Este artigo descreve um erro geral que você pode encontrar ao fazer uma conexão de protocolo RDP para uma VM (Máquina Virtual) Windows no Azure.

## <a name="symptom"></a>Sintoma

Quando você faz uma conexão de protocolo RDP em uma VM Windows no Azure, você pode receber a seguinte mensagem de erro geral:

**A Área de Trabalho Remota não pode se conectar ao computador remoto por um dos seguintes motivos:**

1. **O acesso remoto ao servidor não está habilitado**

2. **O computador remoto está desligado**

3. **O computador remoto não está disponível na rede**

**Verifique se o computador remoto está ligado e conectado à rede e que o acesso remoto está habilitado.**

## <a name="cause"></a>Causa

Esse problema pode ocorrer devido às causas a seguir:

### <a name="cause-1"></a>Causa 1

O componente RDP está desabilitado da seguinte maneira:

- No nível do componente
- No nível do ouvinte
- No servidor Host da Sessão da Área de Trabalho Remota
- Na função de Host da Sessão da Área de Trabalho Remota

### <a name="cause-2"></a>Causa 2

Os Serviços de Área de Trabalho Remota (TermService) não estão em execução.

### <a name="cause-3"></a>Causa 3

O ouvinte RDP está configurado incorretamente.

## <a name="solution"></a>Solução

Para resolver esse problema, [faça backup do disco do sistema operacional](../windows/snapshot-copy-managed-disk.md) e [anexe o disco do sistema operacional a uma VM de resgate](troubleshoot-recovery-disks-portal-windows.md), depois siga as etapas.

### <a name="serial-console"></a>Console Serial

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>Etapa 1: instância CMD aberta no Console serial

1. Acesse o [console serial](serial-console-windows.md) selecionando **suporte & solução de problemas** > **console serial (versão prévia)**. Se o recurso estiver habilitado na VM, você poderá conectar a VM com êxito.

2. Crie um novo canal para uma instância de CMD. Digite **CMD** para iniciar o canal e obter o nome do canal.

3. Alterne para o canal que estiver executando a instância de CMD, nesse caso, deve ser o canal 1.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>Etapa 2: Verifique os valores das chaves de registro do RDP:

1. Verifique se o RDP está desabilitado por políticas.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Se a política de domínio existir, a configuração na política local será substituída.
      - Se a política de domínio declara que o RDP está desabilitado (1), atualize a política do AD por meio do controlador de domínio.
      - Se a política de domínio indica que o RDP está habilitado (0), nenhuma atualização é necessária.
      - Se a política de domínio não existe e a política local declara que o RDP está desabilitado (1), habilite o RDP usando o seguinte comando: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Verifique a configuração atual do servidor Host da Sessão da Área de Trabalho Remota.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Se o comando retornar 0, o servidor Host da Sessão da Área de Trabalho Remota estará desabilitado. Em seguida, habilite o servidor Host da Sessão da Área de Trabalho Remota da seguinte maneira:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. O módulo de Servidor de Terminal será configurado para o modo de drenagem se o servidor estiver em um farm de servidores de terminal (RDS ou Citrix). Verifique o modo atual do módulo do servidor Host da Sessão da Área de Trabalho Remota.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Se o comando retornar 1, o módulo de servidor Host da Sessão da Área de Trabalho Remota será definido para o modo de drenagem. Em seguida, defina o módulo para o modo de funcionamento, da seguinte maneira:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Verifique se você pode se conectar ao servidor Host da Sessão da Área de Trabalho Remota.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Se o comando retornar 1, você não poderá se conectar ao servidor Host da Sessão da Área de Trabalho Remota. Em seguida, habilite a conexão da seguinte maneira:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Verifique a configuração atual do ouvinte RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Se o comando retornar 0, o ouvinte RDP estará desabilitado. Em seguida, habilite o ouvinte da seguinte maneira:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Verifique se você pode se conectar ao ouvinte RDP.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Se o comando retornar 1, você não poderá se conectar ao ouvinte RDP. Em seguida, habilite a conexão da seguinte maneira:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Reinicie a VM.

8. Saia da instância do CMD digitando `exit` e, em seguida, pressione **Enter** duas vezes.

9. Reinicie a VM digitando `restart` e, em seguida, conecte-se à VM.

Se o problema ainda ocorrer, passe para a etapa 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Etapa 2: habilitar Serviços de Área de Trabalho Remota

Para obter mais informações, confira [Os Serviços de Área de Trabalho Remota não estão iniciando em uma VM do Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Etapa 3: redefinir o ouvinte RDP

Para obter mais informações, confira [A Área de Trabalho Remota se desconecta com frequência na VM do Azure](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Reparo offline

#### <a name="step-1-turn-on-remote-desktop"></a>Etapa 1: Ativar a área de trabalho remota

1. [Anexe o disco do sistema operacional a uma VM de recuperação](../windows/troubleshoot-recovery-disks-portal.md).
2. Inicie uma conexão de área de trabalho remota para a VM de recuperação.
3. Verifique se o disco está sinalizado como **Online** no console de Gerenciamento de Disco. Anote a letra da unidade atribuída ao disco do SO anexado.
4. Inicie uma conexão de área de trabalho remota para a VM de recuperação.
5. Abra uma sessão de prompt de comando com privilégios elevados (**Executar como administrador**). Execute os scripts a seguir. Nesse script, presumimos que a letra da unidade atribuída ao disco do SO anexado é F. Substitua essa letra da unidade pelo valor apropriado para a VM.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. Se a VM estiver ingressada no domínio, verifique a seguinte chave do Registro para ver se há uma política de grupo que desabilitará o RDP. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Se esse valor de chave está definido como 1, isso significa que o RDP está desabilitado pela política. Para habilitar a Área de Trabalho Remota por meio da política de GPO, altere a seguinte política por meio do controlador de domínio:

   
      **Configuração do Computador \ Políticas \ Modelos Administrativos:**

      Definições do Azure Policy\Componentes do Windows\Serviços de Área de Trabalho Remota\Host de Sessão da Área de Trabalho Remota\Conexões\Permitir que usuários se conectem remotamente usando os Serviços de Área de Trabalho Remota
  
1. Desanexe o disco fixo da VM de resgate.
1. [Crie uma nova VM do disco](../windows/create-vm-specialized.md).

Se o problema ainda ocorrer, passe para a etapa 2.

#### <a name="step-2-enable-remote-desktop-services"></a>Etapa 2: habilitar Serviços de Área de Trabalho Remota

Para obter mais informações, confira [Os Serviços de Área de Trabalho Remota não estão iniciando em uma VM do Azure](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>Etapa 3: redefinir o ouvinte RDP

Para obter mais informações, confira [A Área de Trabalho Remota se desconecta com frequência na VM do Azure](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
