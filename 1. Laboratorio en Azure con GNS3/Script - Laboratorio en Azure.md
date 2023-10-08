# Configuración de GNS3 en Microsoft Azure

¡Microsoft Azure tiene centros de datos EN TODAS PARTES! Te recomendaría elegir la región más cercana a ti e ingresar eso en la ubicación a continuación. Puedes hacer la prueba con el [siguiente enlace](http://www.azurespeed.com/Azure/Latency).

Reemplaza 'southcentralus' con tu región local. ¡O simplemente déjalo así!

Además, siéntete libre de cambiar el nombre de usuario y la contraseña a continuación. Si no, no te preocupes por ello.

Si no cambias el nombre de usuario/contraseña a continuación, aquí están nuevamente para que puedas iniciar sesión en tu servidor de GNS3:
- Nombre de usuario: grow
- Contraseña: Iniciativa-6r0w!

Hola de nuevo, ¡por favor cambia el nombre de dominio a algo único para ti o podrías obtener errores!

## Configuración Inicial en Azure
> Copia todo entre esta línea y lo que está debajo. Pega en la Azure Cloud Shell

    $ubicacion = 'southcentralus'
    $usuario = "grow"
    $contraseña = convertto-securestring 'Iniciativa-6r0w!' -asplaintext -force
    $credencial = new-object System.Management.Automation.PSCredential ($usuario, $contraseña);
    $nombreDominio = "growgns3"
    
    new-azresourcegroup -name GNS3 -location $ubicacion
    
    New-AzPublicIpAddress -Name GNS3_IP -ResourceGroupName GNS3 -AllocationMethod Static -DomainNameLabel $nombreDominio -Location $ubicacion
    
    New-AzureRmNetworkSecurityGroup -Name GNS3 -ResourceGroupName GNS3 -Location $ubicacion
    
    $nsg=Get-AzureRmNetworkSecurityGroup -Name GNS3 -ResourceGroupName GNS3
    $nsg | Add-AzureRmNetworkSecurityRuleConfig -Name Permitir_Todo -Description "Permitir todo" -Access Allow -Protocol * -Direction Inbound -Priority 100 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange * | Set-AzureRmNetworkSecurityGroup
    
    new-azvm -resourcegroup GNS3 -location $ubicacion -name 'GNS3-SERVIDOR' -image Canonical:0001-com-ubuntu-server-focal:20_04-lts:20.04.202308310 -PublicIPAddressName GNS3_IP -size 'Standard_D4s_v3' -securitygroupname GNS3 -credential $credencial -DomainNameLabel $nombreDominio

## Configuración en el Servidor GNS3
> Copia el código a continuación y pega en tu servidor GNS3 para iniciar la instalación remota

    cd /tmp
    curl https://raw.githubusercontent.com/GNS3/gns3-server/master/scripts/remote-install.sh > gns3-remote-install.sh
    sudo bash gns3-remote-install.sh --with-iou --with-i386-repository
Para verificar el estado del servidor de GNS3, puedes ejecutar:

    sudo service gns3 status

## Enlaces Útiles

-   [Microsoft Azure](https://azure.microsoft.com/)
-   [GNS3 en GitHub](https://github.com/GNS3/gns3-server)
-   [Descarga de GNS3](https://www.gns3.com/software/download)
-    [Marketplace de GNS3](https://gns3.com/marketplace/featured)
-   [Documentación de GNS3](https://docs.gns3.com/docs/)


¡Disfruta configurando tu laboratorio GNS3 en Azure!
