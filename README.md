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

## 🗂️ Estrutura do Código

### 4. Bibliotecas

```cpp
cpp
CopiarEditar
#include <WiFi.h>
#include <PubSubClient.h>

```

### 5. Configuração da Rede e Broker MQTT

```cpp
cpp
CopiarEditar
const char* ssid = "iot"; // Nome da rede Wi-Fi
const char* password = "iotsenai502"; // Senha da rede Wi-Fi
const char* mqttServer = "IP_DO_RASPBERRY"; // Endereço IP do broker
const int mqttPort = 1883; // Porta padrão do MQTT

```

### 6. Configuração do LED

```cpp
cpp
CopiarEditar
const int ledPin = 26; // Pino onde está conectado o LED

```

# 7. Codigo completo

```arduino
#include <WiFi.h>
#include <PubSubClient.h>

const char* ssid = "iot";
const char* password = "iotsenai502";
const char* mqttServer = "192.168.0.125"; // Ex: "192.168.1.100"
const int mqttPort = 1883;

WiFiClient espClient;
PubSubClient client(espClient);

const int ledPin = 26; // Pino onde está o LED

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(115200);
  Serial.println("Iniciando conexão Wi-Fi...");
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("Wi-Fi conectado!");
  Serial.println(WiFi.localIP());

  client.setServer(mqttServer, mqttPort);
  client.setCallback(callback);

  Serial.println("Conectando ao broker MQTT...");
  while (!client.connected()) {
    if (client.connect("ESP32Client")) {
      Serial.println("Conectado ao broker!");
    } else {
      Serial.print("Falha na conexão. Código: ");
      Serial.print(client.state());
      Serial.println(" Tentando novamente em 2 segundos...");
      delay(2000);
    }
  }

  client.subscribe("led");
  Serial.println("Inscrito no tópico 'led'");
}

void loop() {
  client.loop();
}

void callback(char* topic, byte* payload, unsigned int length) {
  String msg;
  for (int i = 0; i < length; i++) {
    msg += (char)payload[i];
  }

  Serial.print("Mensagem recebida no tópico ");
  Serial.print(topic);
  Serial.print(": ");
  Serial.println(msg);

  if (msg == "on") {
    digitalWrite(ledPin, HIGH);
    Serial.println("LED ligado");
  } else if (msg == "off") {
    digitalWrite(ledPin, LOW);
    Serial.println("LED desligado");
  }
}

```

---

## 🔌 Montagem do Circuito

- Conecte o terminal positivo (anodo) do LED ao pino 26 do ESP32.
- Conecte o terminal negativo (catodo) ao GND, preferencialmente passando por um resistor de 220Ω.

---

## 📡 Funcionamento do Código

### Conexão com Wi-Fi e Broker

- Conecta-se à rede Wi-Fi.
- Estabelece conexão com o broker MQTT.
- Inscreve-se no tópico `"led"`.

### Reação às Mensagens

- Se receber `"on"` → acende o LED.
- Se receber `"off"` → apaga o LED.

---

## 🖥️ Serial Monitor

Mensagens exibidas:

- IP do ESP32
- Status da conexão com o Wi-Fi e broker
- Mensagens recebidas no tópico MQTT
- Estado do LED (ligado/desligado)

---

## 🧪 Testes

1. Suba o código na ESP32.
2. Use um cliente MQTT (ex: MQTT Explorer ou `mosquitto_pub`) para publicar no tópico:
    
    ```bash
    bash
    CopiarEditar
    mosquitto_pub -h IP_DO_RASPBERRY -t led -m "on"
    mosquitto_pub -h IP_DO_RASPBERRY -t led -m "off"
    
    ```
    

---

## 📋 Exemplo de Configuração do Mosquitto no Raspberry Pi

```bash
bash
CopiarEditar
sudo apt update
sudo apt install mosquitto mosquitto-clients
sudo systemctl enable mosquitto

```

---

## ⚠️ Possíveis Erros

- **Falha no broker**: Verifique se o IP está correto e o broker está rodando.
- **Erro ao subir o código**: Tente trocar o cabo USB, mudar a porta COM ou pressionar o botão BOOT na ESP32 durante o upload.
