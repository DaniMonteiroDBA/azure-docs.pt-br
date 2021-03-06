---
title: Criar uma máquina virtual Linux usando o PowerShell na pilha do Azure | Microsoft Docs
description: Crie uma máquina virtual Linux com o PowerShell na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 03EE5929-4F05-47D7-B246-EA93D6FC47CD
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/24/2018
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 86597defad7c76d41065270030a4c77ee901b014
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-linux-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Início rápido: criar uma máquina virtual do servidor Linux usando o PowerShell na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode criar uma máquina virtual de Ubuntu Server 16.04 LTS usando o Azure PowerShell de pilha. Siga as etapas neste artigo para criar e usar uma máquina virtual.  Este artigo fornece as etapas para:

* Conecte-se à máquina virtual com um cliente remoto.
* Limpe os recursos não utilizados.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma imagem do Linux no mercado de pilha do Azure**

   Por padrão, o marketplace do Azure pilha não contêm uma imagem do Linux. Obter o operador de pilha do Azure para fornecer o **Ubuntu Server 16.04 LTS** imagem que você precisa. O operador pode usar as etapas descritas no [baixar itens do marketplace do Azure para o Azure pilha](../azure-stack-download-azure-marketplace-item.md) artigo.

* A pilha do Azure requer uma versão específica do Azure PowerShell para criar e gerenciar recursos. Se você não tiver configurado para a pilha do Azure do PowerShell, entre no [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou um cliente com Windows externo se você estiver [conectado por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) e siga as etapas para [ instalar](azure-stack-powershell-install.md) e [configurar](azure-stack-powershell-configure-user.md) PowerShell.

* Uma chave SSH pública com o id_rsa.pub nome salvo no diretório .ssh do seu perfil de usuário do Windows. Para obter informações detalhadas sobre como criar chaves SSH, consulte [chaves Criando SSH no Windows](../../virtual-machines/linux/ssh-from-windows.md).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico em que você pode implantar e gerenciar recursos da pilha do Azure. O kit de desenvolvimento ou o sistema de pilha do Azure integrado, execute o seguinte bloco de código para criar um grupo de recursos. Os valores são atribuídos para todas as variáveis neste documento, você pode usar esses valores ou atribuir novos valores.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Criar recursos de armazenamento

Criar uma conta de armazenamento e, em seguida, crie um contêiner de armazenamento para a imagem do Ubuntu Server 16.04 LTS.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the virtual machine image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob
```

## <a name="create-networking-resources"></a>Criar recursos de rede

Crie uma rede virtual, sub-rede e um endereço IP público. Esses recursos são usados para fornecer conectividade de rede para a máquina virtual.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"

```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Crie um grupo de segurança de rede e uma regra de grupo de segurança de rede

O grupo de segurança de rede protege a máquina virtual por meio de regras de entrada e saídas. Crie uma regra de entrada para a porta 3389 para permitir conexões de área de trabalho remota de entrada e uma regra de entrada para a porta 80 para permitir o tráfego de entrada da web.

```powershell
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleSSH  -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location local `
-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-virtual-machine"></a>Crie uma placa de rede para a máquina virtual

A placa de rede conecta a máquina virtual a uma sub-rede, um grupo de segurança de rede e um endereço IP público.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma configuração de máquina virtual. Essa configuração inclui as configurações usadas ao implantar a máquina virtual. Por exemplo: as credenciais do usuário, o tamanho e a imagem de máquina virtual.

```powershell
# Define a credential object.
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH Keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Adds the SSH Key to the virtual machine
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual

Depois que a máquina virtual é implantada, configure uma conexão SSH para a máquina virtual. Utilize o comando [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-4.3.1) para retornar o endereço IP público da máquina virtual.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

De um sistema cliente SSH instalado, use o seguinte comando para se conectar à máquina virtual. Se você estiver trabalhando no Windows, você pode usar [Putty](http://www.putty.org/) para criar a conexão.

```
ssh <Public IP Address>
```

Quando solicitado, digite azureuser como o usuário de logon. Se você usou uma senha ao criar as chaves de SSH, você precisará fornecer a frase secreta.

## <a name="clean-up-resources"></a>Limpar recursos

Limpe os recursos que você não precisa mais. Você pode usar o [AzureRmResourceGroup remover](/powershell/module/azurerm.resources/remove-azurermresourcegroup?view=azurermps-4.3.1) comando para remover esses recursos. Para excluir o grupo de recursos e todos os seus recursos, execute o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Este guia de início rápido, você implantado uma máquina de virtual de servidor Linux básica. Para saber mais sobre máquinas virtuais de pilha do Azure, vá para [considerações para máquinas virtuais no Azure pilha](azure-stack-vm-considerations.md).
