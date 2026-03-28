# Projeto server-ops

Projeto hands-on com foco em operações de infraestrutura e redes, servidor DNS e configurações de DNS interno, servidor web e certificado ssl pra trafego HTTPS, roteamento e serviços em servidores Linux. O ambiente foi construído utilizando Ubuntu Server, com gerenciamento de rede via Netplan e controle de serviços com systemd.

O projeto simula um cenário real de infraestrutura segmentada em zona DMZ, com separação de responsabilidades entre servidores e controle centralizado através de um gateway.

Focando na separação por camadas (DMZ e Backend) e na entrega de conteúdo seguro ao cliente via HTTPS.

<p align="center">
  <img src="../server-ops/img/server-ops.drawio.png" alt="topologia e arquitertura do projeto">
</p>


## 🎯 Objetivo do Projeto

O ambiente foi estruturado com foco em:

* Zona DMZ
* Separação de responsabilidades entre serviços
* Isolamento de rede
* Controle centralizado via gateway
* Simulação de ambiente real de infraestrutura

A idea do projeto é ter como prática em temas como:

* Administração de sistemas Linux
* Redes de computadores
* Virtualização
* Operações de infraestrutura (Server Ops)

---

## 📁 Estrutura do Projeto

```txt
server-ops/
├── services/           # Serviços executados nos servidores
├── .vagrant/           # Configurações do Vagrant / VMs
├── docs/               # Documentação detalhada dos servidores
│   ├── gateway-DNS-Firewall/   # DNS (Bind9), roteamento e firewall
│   └── server-web/             # Configuração do servidor web (nginx)
├── img                 # imagens de demostrações 
├── README.md           # README do projeto
└── Vagrantfile         # Código de provisionamento das VMs local (IaC)
```

---

## 🧰 Tecnologias Utilizadas

### Infraestrutura

* Linux

  * Gateway / Roteador
  * systemd (gerenciamento de serviços)
  * route (roteamento de pacotes)
  * Bind9 (servidor DNS)
* Vagrant (provisionamento das VMs)
* Libvirt (gerenciamento de virtualização)
* KVM (virtualização acelerada)

---

## ⚙️ Funcionalidades do Ambiente

O ambiente implementa os seguintes componentes de infraestrutura:

* Gateway central
* Roteamento entre redes
* DNS interno
* Serviço web (Nginx)
* Acesso via HTTPS (SSL)

---

## 🌐 Networking

### Conceitos aplicados

Os conceitos de redes que foram utilizados na construção do ambiente:

* IP: definição de endereçamento das máquinas
* Netmask: segmentação das redes
* Interface de rede: comunicação entre as VMs
* DNS: resolução de nomes internos
* Gateway: ponto central de comunicação entre redes
* Routing: encaminhamento de pacotes entre redes distintas

---

## 🔀 Topologia da Rede

A infraestrutura foi organizada em duas redes principais, com um gateway responsável por interligá-las:

```
HOST (cliente)
      │
      ▼
192.168.10.0/24
      │
      ▼
server-gateway
(DNS + firewall + router)
      │
      ▼
10.10.0.0/24
      │
      ├── server-web
      └── server-database
```

* A rede `192.168.10.0/24` representa o acesso do host
* A rede `10.10.0.0/24` é a rede interna isolada
* O gateway atua como intermediário entre essas redes

---

## 🖥️ Provisionamento de Servidores (Máquinas Virtuais)

### VM 1 - gateway-DNS-Firewall (Borda)

Responsável por centralizar toda a comunicação da infraestrutura.

* Nome: gateway-DNS-Firewall
* Função:

  * Roteamento entre redes
  * NAT (tradução de endereços)
  * DNS interno
* Sistema operacional: Ubuntu Server
* RAM: `768 MB`
* CPU: `1 vCPU`
* IP Mascarada (NAT / WAN): `192.168.121.249/24`
* IP rede cliente: `192.168.10.2/24`
* IP rede interno: `10.10.0.2.24`

#### Rede

Possui duas interfaces de rede:

* Interface WAN (Internet)

  * Criada automaticamente pelo Vagrant
  * Interface padrão: `eth0`

* Interface LAN (Rede interna)

  * Responsável pela comunicação com os demais servidores
  * Configurada via Vagrantfile

---

### VM 2 - vm-client

* Nome: vm-client
* Função: Máquina cliente utilizada para acessar o servidor web via navegador
* Sistema operacional: `Ubuntu Server`
* RAM: `768 MB`
* CPU: `1 vCPU`
* IP rede cliente: `192.168.10.8/24`

---

### VM 3 - server-web

* Nome: server-web
* Função: Hospedagem do serviço web
* Sistema operacional: `Ubuntu Server`
* RAM: `1024 MB`
* CPU: `1 vCPU`
* IP rede interno: `10.10.0.40/24`

---

### VM 4 - server-database

* Nome: server-database
* Função: Armazenamento de dados
* Sistema operacional: `Ubuntu Server`
* RAM: `1536 MB`
* CPU: `1 vCPU`
IP rede interno: `10.10.0.20/24`

---

## ⚠️ Problemas Encontrados

### Restauração de armazenamento de imagem de VM

Durante a criação do laboratório, foi necessário realizar a restauração das VMs de gateway e web.

Ao subir as máquinas, ocorreu um problema onde não era possível inicializar corretamente ambas as VMs. Para resolver:

* Foram realizadas cópias dos arquivos de armazenamento
* Novas instâncias foram criadas
* Os discos antigos foram reutilizados nas novas VMs

Esse processo permitiu recuperar o estado anterior das máquinas.

* Caminho das imagens do Libvirt:

```
/var/lib/libvirt/images
```

Esse diretório contém os discos das VMs e é crítico para persistência dos dados.

---

## 🚀 Provisionamento

* [Vagrantfile](./Vagrantfile)

---

## 📚 Documentação dos Servidores

* [Servidor gateway-DNS-Firewall](./docs/gateway-DNS-Firewall.md)
* [Servidor Web](./docs/server-web.md)

---

## 💡 Futuras Implementações

* Firewall com iptables
* Migração da arquitetura para cloud (AWS)
* Containerização de serviços
* Deploy e disponibilização pública do servidor web

---
