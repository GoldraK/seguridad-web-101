# Introducción

En este manual aprenderemos los conceptos básicos de seguridad web utilizando la plataforma [DVWA](https://github.com/ethicalhack3r/DVWA) para practicas.

DVWA es una plataforma vulnerable escrita en PHP/MySQL separada por las diferentes vulnerabilidades que existen, para ayudar a los profesionales de la seguridad y programadores a aprender la menera de vulnerar y progeter sus aplicaciones.

Para el desarrollo del manual hemos utilizado la versión 1.10 Development, que tiene alguna vulnerabilidad mas que la 1.9 estable, para bajar el código tenéis que visitar su [GitHub](https://github.com/ethicalhack3r/DVWA).

Disponéis de un docker con dicha versión en el siguiente [enlace](https://github.com/GoldraK/docker-vulnerable-dvwa)

## Descarga de responsabilidad <a href="#aviso-importante" id="aviso-importante"></a>

El autor no se hace responsable de los daños que se puedan causar por el mal uso de estas técnicas. Este manual esta para aprender seguridad web y mejorar nuestras técnicas de desarrollo seguras.

En ningún momento el material aquí incluido esta pensando para usos delictivos solo es información para mejorar nuestros conocimientos.

## Apartados

Listado de los apartados del manual y su estado.

* [x] ~~XSS~~
  * [x] ~~Reflejado~~
  * [x] ~~Almacenado~~
  * [x] ~~Dom~~
* [x] ~~SQLi~~
  * [x] ~~Union~~
  * [x] ~~Blind~~
* [x] ~~Command Injection~~
* [x] ~~File Inclusion~~
  * [x] ~~LFI~~
  * [x] ~~RFI~~
* [x] ~~File Upload~~
* [ ] Brute Force
* [ ] CSRF
* [ ] Weak Session IDs
* [ ] Insecure CAPTCHA
* [ ] CSP Bypass
* [ ] JavaScripts Attacks



Si alguien quiere colaborar en algún apartado, que no lo dude ;)

## Comparte <a href="#feedback" id="feedback"></a>

La obre se distribuye bajo Licencia Creative Commos, por lo que eres libre de compartirla como quieras y realizar modificaciones u obras derivadas de ella.

![](.gitbook/assets/ccbysa.png)

Seguridad Web 101 por [Rafael Otal Simal](https://legacy.gitbook.com/book/goldrak/seguridad-web-101/details) se distribuye bajo una [Licencia Creative Commons Atribución-CompartirIgual 4.0 Internacional](http://creativecommons.org/licenses/by-sa/4.0/)

## Feedback <a href="#feedback" id="feedback"></a>

Puedes contactar conmigo por [Twitter](https://twitter.com/goldrak) en cualquier momento para decir que te parece el manual o como mejorarlo, siempre hay que aprender.

## Agradecimientos

* [Anabel Pérez González](https://twitter.com/tricky\_monstruo)
* [Kevin Gonzalvo](https://twitter.com/interh4ck)
* [Jose Dongil](https://twitter.com/jdonsan)
* [Jorge Puente](https://twitter.com/forges82)

Comencemos :)
