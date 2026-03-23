# Gateway - DNS, Roteamento e Firewall

Este servidor (VM) é o ponto central da infraestrutura, sendo responsável por controle de rede, resolução de nomes com servidor DNS e intermediação de comunicação entre as redes.

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

## ⚙️ Serviço de DNS e configuração do Bind9

O servidor DNS implementei utilizando o Bind9, sendo responsável pela resolução de nomes dentro da rede interna.

Foi definido o domínio: `thcompany.lan`

Através dele, é possível acessar os servidores da infraestrutura utilizando nomes ao invés de endereços IP.


### Arquivo: `/etc/bind/named.conf.options`

Responsável pelas configurações globais do servidor DNS.

Nesta seção são definidos os forwarders, ou seja, os servidores DNS externos para os quais as requisições são encaminhadas quando o DNS interno não consegue resolver um domínio.

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

Responsável por definir as zonas DNS locais gerenciadas pelo servidor.

Neste caso, foi configurada uma zona do tipo master, onde o próprio servidor DNS é a autoridade principal pelo domínio:

```conf
zone "thcompany.lan" {
	type master;
	file "db.thcompany.lan";
};
```

---

### Arquivo de Zona: `/var/cache/bind/db.thcompany.lan`

Responsável pelos registros DNS internos da infraestrutura.

Neste arquivo é realizado o mapeamento entre nomes e endereços IP, permitindo a resolução de nomes dentro do domínio interno.

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

Comandos utilizados para validar a sintaxe dos arquivos de configuração do DNS, garantindo que não há erros antes de subir o serviço.

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

Após alterações nos arquivos de configuração, é necessário reiniciar o serviço para que as mudanças sejam aplicadas.

O serviço lê as configurações atualizadas, Aplica as alterações realizadas (zonas, registros, options) e reinicia o servidor DNS com as novas definições

```bash
systemctl status bind9
systemctl restart bind9
```

---

Demotrações do laboratorio:



## 🔥 Firewall

O gateway também é responsável pelo controle de tráfego entre as redes.

(Em construção com base nas regras aplicadas no ambiente)

---

## 📌 Observações

* O DNS resolve nomes internos da rede `thcompany.lan`
* O gateway centraliza a comunicação entre cliente e servidores
* Uso de forwarders permite acesso a domínios externos

Este servidor é essencial para o funcionamento completo do ambiente.