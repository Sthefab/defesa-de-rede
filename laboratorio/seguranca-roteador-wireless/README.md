# Fortalecimento e Segurança de Roteador Sem Fio (Packet Tracer)

Laboratório da Cisco Networking Academy (curso Segurança de Endpoint) onde configurei a segurança de um roteador sem fio doméstico, conectei os clientes de forma segura e testei o isolamento entre a rede principal e a rede de convidados.

## Objetivo

Corrigir problemas de segurança de um roteador sem fio configurado só pela metade, protegendo o acesso administrativo, as redes Wi-Fi e os dispositivos conectados.

## O que foi feito

### 1. Configurações básicas do roteador

- Troca da senha padrão (`admin`) por uma senha forte na aba **Administração**
- Desativação do **gerenciamento remoto**, já que uma porta aberta e escutando pode ser alvo de ataque de senha. Se fosse realmente necessário, o ideal seria uma solução VPN

### 2. Segurança das redes sem fio

- **SSID HomeNet** configurado e com transmissão habilitada nas três redes do roteador (2.4 GHz, 5 GHz - 1 e 5 GHz - 2)
- Esconder o SSID não é uma medida de segurança real, porque existem ferramentas que descobrem o nome da rede pelo tráfego
- **Criptografia** configurada nas três redes:
  - Security Mode: WPA2 Personal
  - Encryption: AES
  - Passphrase definida
- **GuestNet** criada para os convidados, também com WPA2 Personal e AES, mantendo o tráfego deles separado do resto

### 3. Conexão dos clientes

- Laptops conectados com a chave pré-compartilhada de cada rede (Laptop 1 na HomeNet e Laptop 2 na GuestNet)
- Dispositivos IoT (Home_Webcam, Home_Siren e Home_Door) configurados com:
  - SSID: HomeNet
  - Autenticação: WPA2-PSK
  - Criptografia: AES
  - IP via DHCP, recebendo endereço da rede 192.168.0.0/24

### 4. Verificação

- Teste de acesso à internet pelos dois laptops
- Teste de isolamento entre as redes: o Laptop 2 (GuestNet) fez ping no Laptop 1 (HomeNet) e **respondeu**, mostrando que o convidado alcançava a rede principal
- Desmarquei a opção **"Permitir que os convidados se encontrem e acessem a rede local"** na aba Rede de convidados
- Novo ping do Laptop 2 para o Laptop 1: **100% de perda**, confirmando que as redes ficaram isoladas

## Prints

### Senha do roteador e gerenciamento remoto desativado

![Administração do roteador](img/administracao.png)

### SSID HomeNet transmitindo nas três redes

![SSID HomeNet](img/ssid-homenet.png)

### WPA2 Personal com AES

![Segurança sem fio](img/wpa2-aes.png)

### Home_Webcam

![Home_Webcam](img/home-webcam.png)

### Home_Siren

![Home_Siren](img/home-siren.png)

### Ping antes do isolamento (responde)

![Ping antes](img/ping-antes.png)

### Ping depois do isolamento (100% de perda)

![Ping depois](img/ping-depois.png)

## Conceitos praticados

- Troca de credenciais padrão
- Redução da superfície de ataque (gerenciamento remoto desativado)
- Criptografia WPA2 com AES
- Segmentação entre rede principal e rede de convidados
- Verificação de segurança com testes de conectividade (ping)

## Ferramentas

- Cisco Packet Tracer
