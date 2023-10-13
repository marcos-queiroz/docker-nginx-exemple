# LAMP + NGINX

Ambiente de desenvolvimento semelhante a grande maioria das hospedagens com PHP + MySQL + PHPMyAdmin.

A maioria das hospedagens populares seguem o mesmo padrão, para facilitar o desenvolvimento utilizando Docker, preparamos esse pequeno exemplo de docker-compose utilizando como base a [Pilha LAMP]().

## Preparando o ambiente

### Virtual Host

Para utilizar os containers em vários projetos, basta utilizar o Virtual Host. No diretório 'config\vhosts' edite o arquivo `default.conf` e inclua os hosts apontado para os diretórios dos novos projetos.

#### Windows

No Windows, deve ser alterado o arquivo de hosts, normalmente localizado em "C:\Windows\System32\drivers\etc".

Acrescente os hosts virtuais como no exemplo, como padrão ao digitar a URL 'http://localhost' no Browser o mesmo será a apontado para o IP 127.0.01, que é o IP local da própria maquina.

```
  127.0.0.1       localhost
  ::1             localhost
```

Para incluir o PHPMyAdmin e MINIO 

```
  127.0.0.1       minio.localhost
  127.0.0.1       phpadmin.localhost
```

Para testar os Hosts em diferentes versões do PHP para o mesmo arquivo `www/index.php`

```
  127.0.0.1       serv1.localhost
  127.0.0.1       serv2.localhost
  127.0.0.1       serv3.localhost
  127.0.0.1       serv4.localhost
```

Para editar o arquivo 'hosts' sera necessário permissões de administrador e a reinicialização da maquina.

## Testes

Copie o arquivo `sample.env` com o comando:

```bash
  cp sample.env .env
```

Suba os containers com o comando:

```bash
  docker compose -f "docker-compose.yml" up -d --build
```

### URLs

- Para testar o PHP 8.2 basta acessar o endereço <http://localhost/> ou <http://serv1.localhost/>
- Para testar o PHP 8.1 basta acessar o endereço <http://serv2.localhost/>
- Para testar o PHP 7.4 basta acessar o endereço <http://serv3.localhost/>
- Para testar o PHP 7.2 basta acessar o endereço <http://serv4.localhost/>

> Isso é especialmente util quando se está migrando a versão do PHP de um projeto por exemplo.

## Nginx 

O objetivo da utilização do Nginx como proxy reverso nesse projeto é demonstrar a possibilidade de acessar N Serviços Web na porta padrão `80` ou  `443`.

Para isso verifique o carregamento dos containers como dependência do container `proxy`:

```yml
  version: "3"

  services:
    proxy:
      image: nginx
      volumes:
        - ./nginx/dev.conf:/etc/nginx/conf.d/default.conf
      ports:
        - "${HOST_MACHINE_UNSECURE_HOST_PORT}:80"
        - "${HOST_MACHINE_SECURE_HOST_PORT}:443"
      depends_on:
        - phpmyadmin
        - minio
        - webserver-01
        - webserver-02
        - webserver-03
        - webserver-04
```

Nesse exemplo estamos utilizando 4 servidores web com diferentes versões do PHP, mas também é possível utilizar servidores Java, Node etc.

A cada nova dependência é necessário acrescentar no arquivo `config/nginx/dev.conf` para qual serviço o host apontara. 

## Bom trabalho

Espero ajudar alguém com esse simples repositório, sinta a vontade para contribuir.