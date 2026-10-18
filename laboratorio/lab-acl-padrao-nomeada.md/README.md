# Lab: Configuração de ACL IPv4 Padrão Nomeada

Atividade feita no Cisco Packet Tracer. Resultado final: **100/100**.

---

## Objetivos

- Configurar e aplicar uma ACL padrão nomeada
- Verificar a implementação da ACL

## Cenário

O administrador de rede pediu uma ACL padrão pra restringir o acesso a um servidor de arquivos, que guarda a base de dados das aplicações Web. Somente a estação do Web Manager (PC1) e o Servidor Web podem acessar o Servidor de Arquivos. Todo o resto do tráfego pra ele deve ser negado.

## Topologia e tabela de endereçamento

![Topologia e tabela de endereçamento](imagens/01-topologia.png)

| Dispositivo | Interface | Endereço IP | Máscara | Gateway |
| --- | --- | --- | --- | --- |
| R1 | F0/0 | 192.168.10.1 | 255.255.255.0 | N/D |
| R1 | F0/1 | 192.168.20.1 | 255.255.255.0 | N/D |
| R1 | E0/0/0 | 192.168.100.1 | 255.255.255.0 | N/D |
| R1 | E0/1/0 | 192.168.200.1 | 255.255.255.0 | N/D |
| Servidor de Arquivos | Placa de rede | 192.168.200.100 | 255.255.255.0 | 192.168.200.1 |
| Servidor Web | Placa de rede | 192.168.100.100 | 255.255.255.0 | 192.168.100.1 |
| PC0 | Placa de rede | 192.168.20.3 | 255.255.255.0 | 192.168.20.1 |
| PC1 (Web Manager) | Placa de rede | 192.168.20.4 | 255.255.255.0 | 192.168.20.1 |

---

## Parte 1: Configurar e aplicar a ACL

### Etapa 1: Verificar a conectividade antes da ACL

Os três PCs devem conseguir dar ping no Servidor Web e no Servidor de Arquivos.

### Etapa 2: Criar a ACL padrão nomeada

```
R1> enable
R1# configure terminal
R1(config)# ip access-list standard File_Server_Restrictions
R1(config-std-nacl)# permit host 192.168.20.4
R1(config-std-nacl)# permit host 192.168.100.100
R1(config-std-nacl)# deny any
R1(config-std-nacl)# exit
```

- O nome da ACL diferencia maiúsculas de minúsculas
- A ordem das instruções importa, porque a ACL é lida de cima pra baixo

Verificação antes de aplicar:

```
R1# show access-lists
Standard IP access list File_Server_Restrictions
    10 permit host 192.168.20.4
    20 permit host 192.168.100.100
    30 deny any
```

### Etapa 3: Aplicar a ACL na interface

```
R1(config)# interface fastEthernet 0/1
R1(config-if)# ip access-group File_Server_Restrictions out
R1(config-if)# end
R1# copy running-config startup-config
```

Configuração feita no CLI do R1:

![CLI do R1 com a ACL configurada](imagens/02-cli-acl.png)

---

## Parte 2: Verificar a implementação

### Etapa 1: Conferir configuração e aplicação

```
R1# show access-lists
R1# show ip interface fastEthernet 0/1
R1# show running-config
```

### Etapa 2: Testar o funcionamento

- Os três PCs devem conseguir dar ping no **Servidor Web**
- Só o **PC1** e o **Servidor Web** devem conseguir dar ping no **Servidor de Arquivos**
- Rodar `show access-lists` de novo pra ver o contador de pacotes que bateram em cada instrução

---

## Resultado

Atividade concluída com 100/100 no Packet Tracer (ACL `File_Server_Restrictions` e `access-group out` na FastEthernet0/1 corretos).

![Resultado da atividade: 100/100](imagens/03-resultado.png)

---

## Aprendizados

- ACL padrão filtra **só pelo IP de origem**, então o ideal é aplicar o mais perto possível do destino
- ACL nomeada facilita a leitura e a edição das linhas
- Toda ACL termina com um `deny any` implícito, mas aqui ele foi escrito de forma explícita pra aparecer no contador do `show access-lists`
- O `show access-lists` só funciona no modo privilegiado. No modo de configuração dá erro, e nesse caso usa `do show access-lists`
- Em rede de produção, aplicar ACL em interface ativa é arriscado e deve ser evitado quando possível
