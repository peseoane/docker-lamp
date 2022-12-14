# Instalación de LAMP con Docker-Compoose

Vamos criar unha máquina virtual que vaia cargar por nos toda a pila necessária, con a vantaxe de que para a mover entre a casa e a sala de aula do IES, só precisamos (via Git ou o que preferir) de copiar os ficheiros gerados automaticamente, eles case non teñen peso. 

Ou mesmo empregalos nun pendrive!

## Requerimentos

Tanto para Windows como para Linux, isto é interoperábel e compatíbel.

O primeiro paso é instalar o Docker, o que se pode facer utilizando una *GUI*.

https://docs.docker.com/engine/install/

>   Se usar WSL na aula lembre-se que, o desenpeño sobre Docker será de maior rendimento, mas non poderá usar a Virtualbox porque é virtualización en cascada, recomenda-se non a activar.

Tamén é preciso ter no sistema da aula git, mas debería de estar xa instalado.

https://git-scm.com/download/win

>   Se utilizares o Ubuntu, lembrate que Git é instalado por defeto, caso contrario:
>
>   ```bash
>   sudo apt update
>   sudo apt install git
>   ```

## TLDR -> Versión para preguiceiros

1.   Tras instalar docker, abrirlo.

2.   clonar o repositorio:

https://github.com/peseoane/docker-lamp

3.   Escribir na terminal `docker-compose up --build`
4.   Agardar un intre…
     1.   Abrir phpmyadmin:  http://localhost:8000/
     2.   Abrir a web: http://localhost
     3.   Datos de SQL para Workbench:
          1.   localhost: `3306`
          2.   user: `root`
          3.   clave: `estudante`

Tras 30 segundos xa funciona!

![image-20221214201329618](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214201329618.png)

![image-20221214201354597](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214201354597.png)

![image-20221214201415998](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214201415998.png)

## Desprege do LAMP (extendida)

O primero e clonar o repositorio:

https://github.com/peseoane/docker-lamp

Podemos facer isto visualmente

![image-20221214193958756](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214193958756.png)

Ou mediante o emprego da terminal

```bash
git clone  https://github.com/peseoane/docker-lamp.git
```

Una vez clonado o repositorio, iiremos através do terminal até onde esté gardado, no meu caso:

```
pwd
cd c:\git\DAW\BD\php
docker-compose up --build
```

![image-20221214194358321](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214194358321.png)

En canto premamos o *enter*, o docker irá él soiño apenas a criar todo por nós.

![image-20221214194512992](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214194512992.png)

En menos de 30 segundos será terminada a carga do container.

## Acceder ao PHPMyAdmin

No noso ficheiro de configuración temos:

```yaml
  phpmyadmin:
    container_name: bd_phpmyadmin
    image: phpmyadmin/phpmyadmin
    links:
      - db:db
    ports:
      - 8000:80
    environment:
#      PMA_ARBITRARY: 1
      PMA_HOST: db
      MYSQL_USER: estudante
      MYSQL_PASSWORD: estudante
      MYSQL_ROOT_PASSWORD: vanesa
    depends_on:
      - db
    networks:
      - default
```

Por tanto, vemos que `localhost:8000` abre o phpmyadmin dende o navegador.

![image-20221214195022145](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214195022145.png)

![image-20221214195032812](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214195032812.png)

## Acceder ao site web

No fichero de configuración, nos deixamos o porto 80, que é o nativo para http, por tanto `localhost` xa abre o site. Non é preciso escreber `localhost:80`

![image-20221214194632572](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214194632572.png)

## Acceder dende Workbench <-> mySQL

Do mesmo xeito que a vez anterior

Se tes conflictos de portos, cambiao por outro na configuración.

O *default* é o 3306, pero podes poñer calquera outro se quiser.

```yaml
  db:
    image: mysql:8.0
    container_name: bd_mysql
    ports:
      - "1994:3306"  # Agora o porto da BD é o 1994
    command: --default-authentication-plugin=mysql_native_password
    environment:
      MYSQL_DATABASE: daw
      MYSQL_USER: a22pedrovsp
      MYSQL_PASSWORD: 3892
      MYSQL_ROOT_PASSWORD: 3892
    volumes:
      - ./dump:/docker-entrypoint-initdb.d
      - ./db/conf:/etc/mysql/conf.d
      - ./db/data:/var/lib/mysql
    networks:
      - default
```

![image-20221214201454838](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214201454838.png)

![image-20221214201604008](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214201604008.png)

## Reiniciar, apagar e xestionar o LAMP

Dende a GUI do Docker é posibel face-lo.

![image-20221214195354226](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214195354226.png)

*   Container de mysql
*   Container de phpmyadmin
*   Container do php.

# Migración de datos da clase para casa

Terás de sincronizar o cartafol enteiro (faz un fork en git por exemplo) ou pode copiar e colar de cada vez nunha pendrive, ou mesmo executá la mesmo sé ser rápido (USB3).

![image-20221214195659732](Instalaci%C3%B3n%20de%20LAMP%20con%20Docker-Compoose.assets/image-20221214195659732.png)

O cartatol db contén todas as bases de datos.

O cartafol www contén os sites, php…

Ao copiar e colar iso na tua casa, facendo de novo o docker-compose up lá, automáticamente quedará todo ben configurado do mesmo xeito que na aula do IES.

## Script empregado

```
version: "3.1"
services:
  php:
    container_name: bd_php
    #image: php:fpm-alpine
    build: .
    ports:
      - "80:80"
    volumes:
      - ./www:/var/www/html
    links:
      - db
    networks:
      - default
  db:
    image: mysql:8.0
    container_name: bd_mysql
    ports:
      - "3306:3306"
    command: --default-authentication-plugin=mysql_native_password
    environment:
      MYSQL_DATABASE: daw
      MYSQL_USER: a22pedrovsp
      MYSQL_PASSWORD: 3892
      MYSQL_ROOT_PASSWORD: 3892
    volumes:
      - ./dump:/docker-entrypoint-initdb.d
      - ./db/conf:/etc/mysql/conf.d
      - ./db/data:/var/lib/mysql
    networks:
      - default
  phpmyadmin:
    container_name: bd_phpmyadmin
    image: phpmyadmin/phpmyadmin
    links:
      - db:db
    ports:
      - 8000:80
    environment:
#      PMA_ARBITRARY: 1
      PMA_HOST: db
      MYSQL_USER: a22pedrovsp
      MYSQL_PASSWORD: 3892
      MYSQL_ROOT_PASSWORD: 3892
    depends_on:
      - db
    networks:
      - default
volumes:
  persistent:
```

