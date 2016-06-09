---
layout: page
published: true
title: Testar HTTPS localmente
---

Por questões de segurança, alguns dos temas introduzidos só funcionam através do protocolo HTTPS, para o qual é necessário um certificado SSL, o que não permite abrir um ficheiro HTML local diretamente no browser.
Uma solução para contornar o problema consiste na criação local de um servidor com certificado SSL.
Segue-se um curto guia de como fazer isto em MacOS e Linux. 

##### Gerar um Certificado SSL
Na linha de comandos correr: 

```
openssl req -new -x509 -keyout server.pem -out path/to/serverCertificate.pem -days 365 -nodes
```
E preencher a informação pedida.

##### Criar um servidor HTTPS
- Copiar o seguinte código para o ficheiro "SimpleSecureHTTPServer.py", substituindo a localização do certificado:
 
```python
import BaseHTTPServer, SimpleHTTPServer
import ssl

httpd = BaseHTTPServer.HTTPServer(('localhost', 4443), SimpleHTTPServer.SimpleHTTPRequestHandler)
httpd.socket = ssl.wrap_socket (httpd.socket, certfile='path/to/serverCertificate.pem', server_side=True)
httpd.serve_forever()
```
- Na linha de comandos, dentro da mesma pasta, correr `python -m SimpleSecureHTTPServer` ;
- Aceder ao `localhost` pela porta 4443 (
poderá aparecer um aviso a dizer que o certificado não é reconhecido mas deve-se prosseguir.)

