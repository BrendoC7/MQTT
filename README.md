# 💡 Projeto IoT com MQTT e Raspberry Pi — Controle de LED

## 1. 📌 Introdução

### Visão Geral
Este projeto de Internet das Coisas (IoT) demonstra a comunicação entre dois dispositivos utilizando o protocolo MQTT, com o objetivo de **acender ou apagar um LED remotamente**.

- Um dispositivo atua como **publisher**, enviando comandos MQTT (`ON` ou `OFF`).
- Outro dispositivo atua como **subscriber**, recebendo os comandos e controlando o estado de um **LED**.

### Justificativa

- **MQTT** é ideal para aplicações IoT por ser leve, eficiente e confiável.
- A **Raspberry Pi** funciona como broker MQTT, hospedando o serviço e facilitando a troca de mensagens.

### Diagrama de Arquitetura

```plaintext
+-------------------+       MQTT        +------------------+       MQTT       +-----------------+
| Dispositivo Pub   |  ---> Publish  -->|   Raspberry Pi   |  -->  Subscribe ->| LED com GPIO    |
| (Envia ON/OFF)    |                   |    (Broker)      |                   | (Subscriber)     |
+-------------------+                   +------------------+                   +-----------------+
````
## 2. Instalação mosquitto
- sudo apt update
- sudo apt install -y mosquitto mosquitto-clients
- sudo systemctl enable mosquitto
- sudo systemctl start mosquitto

## 3. Instalação MQTT Explorer
- Instalamos um software chamado MQTT explorer para poder fazer o teste de comunição entre dois dispositivos, entre a rasp berry pi e um notebook
- dentro do MQTT Explorer
