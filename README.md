# Troubleshooting de Ambiente Simulado

Modelo de Troubleshooting é baseado no modelo ISO/OSI 7 Camadas. Neste modelo usamos até a camada 3

- [Modelo OSI](https://pt.wikipedia.org/wiki/Modelo_OSI) : Link com informações sobre o modelo OSI e suas camadas.

## **OBJETIVO**

Este é um ambiente simulado onde a Rede SENAI não funciona. Devemos exercitar o conhecimento sobre o modelo ISO/OSI de 7 camadas e restabelecer o mais prontamente possivel a infraestrutura do cliente.

# Topologia

Primeiro passo é analisar a sua topologia, o que voçê tem de informação sobre a rede defeituosa.

![fluxograma](Photo-cenário.png)

Analisando a topologia fornecida devemos fazer anotações sobre cada detalhe que temos, todas as informações possiveis descritas(Abaixo um exemplo de como realizar as anotações).

## Informações

- Rede: 192.168.10.1/24 > VLAN 10(RH) | G0/0.10
- Rede: 192.168.20.1/24 > VLAN 20(VENDAS) | G0/0.20
- Rede: 192.168.30.1/24 > VLAN 30(TI) | G0/0.30
- Rede: 192.168.40.1/24 > VLAN 40(GERÊNCIA) | G0/0.40
- VTP SERVER - VLANS 10,20,30,40 | SENAI-SW1
- VTP CLIENT - VLANS 10,20,30,40 | SENAI-SW2

Após algumas anotações sobre a rede, partimos para a resolução dos problemas. No modelo OSI, usamos o método em que começamos de baixo para cima, ou seja, Camada 1 - Física. Na camada física o principal problema encontrado é sempre relacionado a hardware, algo físico, em ambientes virtuais temos configurações que representa os itens citados anteriormente. Posteriormente seguimos com a Camada 2 (Enlace) e Camada 3 (Rede) na seuquência.

## Troubleshooting

**SENAI-PC1**

FÍSICO (Conexão de cabos)
1. Estado da máquina  
	* (Ligado na tomada)
2. Conexão de portas
	* (F0/0 <> F0/1) 

LÓGICO (Sistema operacional)
1. Configuração base
	* Hostname
	 (PC0 > SENAI-PC1)

2. Interface(s) de rede
	* IP Configuration - OK
	* Máscara > OK
	* Gateway > OK

**SENAI-PC2**

FÍSICO (Conexão de cabos)
1. Estado da máquina 
	* (Ligado na tomada)
2. Conexão de portas
	* (F0/0 <> F0/2) 

LÓGICO (Sistema operacional)
1. Configuração base
	* Hostname
	 (PC1 > SENAI-PC2)
2. Interface(s) de rede
	* IP Configuration - OK
	* Máscara > OK
	* Gateway > OK

**SENAI-PHONE**

FÍSICO (Conexão de cabos)
1. Estado da máquina
	* (Cabo desconectado > Ligar Cabo na Interface de Configuração do Packet)
2. Conexão de portas
	* (Fa <> F0/3)

LÓGICO (Sistema operacional)
1. Configuração base
	* Hostname
			(IP Phone0 > SENAI-PHONE)
		
**SENAI-PC3**

FÍSICO (Conexão de cabos)
1. Estado da máquina
	* (Ligado na tomada)
2. Conexão de portas
	* (F0/0 <> Fa <> F0/3)

LÓGICO (Sistema operacional)
1. Configuração base
	* Hostname
			(PC2 > SENAI-PC3)
2. Interface(s) de rede
	* IP Incorreto 	IP Correto
	* 192.168.30.10 > 192.168.30.20 
	* Máscara > OK
	* Mateway > OK

**SENAI-RT**

FÍSICO (Conexão de cabos)
1. Estado do aparelho
	* (Ligado na tomada)
2. Conexão de portas
	* Porta em interface errada
		(G0/0 > F0/11 || G0/0 > F0/10)

LÓGICO (Sistema operacional)
1. Configuração base
	* Hostname
			(RT-SENAC > SENAI-RT)
2. Interface(s) de rede (NIC)
	* int G0/0 | Padrão shutdown
	* shutdown > no shutdown
3. Sub-interface(s)
	* int G0/0.10 | IP Incorreto
	* 192.168.100.1 > 192.168.10.1
	* Máscara > OK

	* int G0/0.20 | IP Correto
	* 192.168.20.1
	* Máscara > OK
	* Encapsulamento VLAN Incorreto
	* dot1Q 200 > dot1Q 20
			
	* int G0/0.30 | IP Correto
	* 192.168.30.1 
	* Máscara Incorreta 
	* 255.255.255.252 > 255.255.255.0
			
	* int G0/0.40 | IP Correto
	* 192.168.40.1 
	* Máscara > OK
	* Sub-interface Incorreta 
	* int G0/0.400 > int G0/0.40

**SENAI-SW1**

FÍSICA (Conexão de cabos)
1. Estado do aparelho
	* Ligado na tomada
2. Conexão de portas
	* Porta em interface errada
		(F0/11 > G0/0 || F0/10 > G0/0)

LÓGICO (Sistema operacional)
1. Configuração base
	* Hostname > OK
2. Interface(s) de rede (NIC)
	* int F0/10 | Modo de gerenciamento de VLAN Incorreto
	* Modo ACCESS > Modo TRUNK
	* int F0/23-24 | Modo ativado, mas sem permissão.
	* Modo TRUNK > Modo TRUNK ALLOWED VLAN 1,10,20,30,40
3. Vlan(s)
	* 10 RH
	* 20 VENDAS
	* 30 TI
	* 40 GERÊNCIA

4. VTP Server
	* Versão VTP-V2 | Desabilitada
	* Domínio Incoerentem0n0.local > SENAI-3RT
	* Senha | Padrão Senai@132
			
**SENAI-SW2**

FÍSICA (Conexão de cabos)
1. Estado do aparelho
	* Ligado na tomada
2. Conexão de portas
	* Porta em interface errada
	(F0/11 > G0/0 || F0/10 > G0/0)
LÓGICO (Sistema operacional)
1. Configuração base
	* Hostname > OK
2. Interface(s) de rede (NIC)
	* int F0/1 | Modo gerenciamento correto
	* int F0/2 | Modo gerenciamento correto
	* int F0/3 | Modo gerenciamento correto
	* int F0/23-24 | Modo ativado, mas sem permissão 
	* Modo TRUNK > Modo TRUNK ALLOWED VLAN 1,10,20,30,40
3. Vlan(s)
	* 10 RH | Atrelada a F0/1
	* 20 VENDAS | Atrelada a F0/2
	* 30 TI | Atrelada a F0/3
	* 40 GERÊNCIA
4. VTP Client
	* Versão VTP-V2 | Desabilitada
	* Domínio Incoerente (m0n0.local > SENAI-3RT)
	* Senha | Padrão Senai@132
			
