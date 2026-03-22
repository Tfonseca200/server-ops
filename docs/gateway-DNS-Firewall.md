# Gateway - DNS, Roteamento e Firewall

Esta VM é o ponto central da infraestrutura, sendo responsável por controle de rede, resolução de nomes e intermediação de comunicação entre as redes.

---

## 📌 Responsabilidade do Servidor

O gateway atua como:

* Servidor DNS interno
* Roteador entre redes
* Ponto central de comunicação

Ele conecta a rede do host (`192.168.10.0/24`) com a rede interna (`10.10.0.0/24`).

---

## 🌐 DNS (Bind9)

### Tecnologia utilizada

* Bind9

---

## ⚙️ Configuração do Bind

### Arquivo: `/etc/bind/named.conf.options`
# Gateway - DNS, Roteamento e Firewall

Esta VM é o ponto central da infraestrutura, sendo responsável por controle de rede, resolução de nomes e intermediação de comunicação entre as redes.

---

## 📌 Responsabilidade do Servidor

O gateway atua como:

* Servidor DNS interno
* Roteador entre redes
* Ponto central de comunicação

Ele conecta a rede do host (`192.168.10.0/24`) com a rede interna (`10.10.0.0/24`).

---

## 🌐 DNS (Bind9)

### Tecnologia utilizada

* Bind9

---

## ⚙️ Configuração do Bind

### Arquivo: `/etc/bind/named.conf.options`

Responsável pelas configurações globais do servidor DNS.

```conf
options {
	directory "/var/cache/bind";

	forwarders {
		8.8.8.8;
		9.9.9.9;
	};

	dnssec-validation yes;
	listen-on-v6 { none; };
	listen-on { any; };
	allow-recursion { any; };
	allow-query { any; };
};
```

### Pontos importantes:

* **forwarders**: encaminha requisições externas para DNS públicos
* **listen-on any**: permite escutar em todas interfaces
* **allow-query / recursion**: permite consultas internas

---

### Arquivo: `/etc/bind/named.conf.local`

Define as zonas DNS locais.

```conf
zone "thcompany.lan" {
	type master;
	file "db.thcompany.lan";
};
```

---

### Arquivo de Zona: `/var/cache/bind/db.thcompany.lan`

Responsável pelos registros DNS internos.

```dns
$TTL 3600
@       IN      SOA          ns1.thcompanhy.lan. admin.thcompanhy.lan. (
                2026031301 ; Serial
                3600 ; refresh
                1800 ; retry
                604800 ; expire
                3600 ) ; ttl negativo

@        IN     NS      ns1.thcompanhy.lan.
ns1      IN     A       192.168.10.2
firewall IN     A       192.168.10.2
www      IN     A       192.168.10.40
client   IN     A       192.168.121.239
web      IN     CNAME   www
```

### Registros configurados:

* `ns1`: servidor DNS
* `firewall`: gateway
* `www`: servidor web
* `client`: máquina cliente
* `web`: alias para www

---

## 🧪 Validação de Configuração

### Verificar arquivos do Bind

```bash
cd /etc/bind
named-checkconf
```

### Verificar zona DNS

```bash
cd /var/cache/bind
named-checkzone thcompany.lan db.thcompany.lan
```

---

## ▶️ Execução do Serviço

O serviço do Bind é gerenciado pelo systemd.

```bash
systemctl status bind9
systemctl restart bind9
```

---

## 🔥 Firewall

O gateway também é responsável pelo controle de tráfego entre as redes.

(Em construção com base nas regras aplicadas no ambiente)

---

## 📌 Observações

* O DNS resolve nomes internos da rede `thcompany.lan`
* O gateway centraliza a comunicação entre cliente e servidores
* Uso de forwarders permite acesso a domínios externos

Este servidor é essencial para o funcionamento completo do ambiente.