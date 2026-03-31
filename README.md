# CP4-Edge-Computing-e-Computer-Systems

# 💡 FIWARE Descomplicado — PoC Smart Lamp
 
Projeto de monitoramento IoT utilizando ESP32 + sensor LDR integrado ao ecossistema FIWARE, desenvolvido como Check Point da disciplina de IoT da FIAP.
 
---
 
## 📋 Descrição
 
Este projeto implementa uma **Smart Lamp** com monitoramento de luminosidade, utilizando:
 
- **ESP32 DEVKIT 1** com sensor **LDR**
- **FIWARE Descomplicado** rodando em VM na nuvem (Microsoft Azure)
- Comunicação via protocolo **MQTT**
- Padrão **NGSI v2** para modelagem de dados
- Simulação via **Wokwi**
 
---
 
## 🏗️ Arquitetura
 
```
Wokwi (ESP32 + LDR)
        ↓ MQTT (porta 1883)
IoT Agent (porta 4041)
        ↓
Orion Context Broker (porta 1026)
        ↓
STH-Comet (porta 8666) — histórico de dados
        ↓
MongoDB (porta 27017) — persistência
```
 
---
 
## ☁️ Infraestrutura
 
| Recurso | Valor |
|--------|-------|
| Cloud Provider | Microsoft Azure |
| Região | Mexico Central |
| VM | Standard_D2ls_v5 (2 vCPUs, 4GB RAM) |
| Sistema Operacional | Ubuntu Server 24.04 LTS |
| IP Público | 158.23.59.148 |
 
### Portas liberadas (Azure NSG + UFW)
 
| Serviço | Porta |
|---------|-------|
| SSH | 22 |
| Orion Context Broker | 1026 |
| MQTT (Mosquitto) | 1883 |
| IoT Agent | 4041 |
| STH-Comet | 8666 |
 
---
 
## 🚀 Instalação e Configuração
 
### 1. Pré-requisitos
 
- Conta Microsoft Azure com créditos ativos
- VM Ubuntu 24.04 criada no Azure
- Docker e Docker Compose instalados
 
### 2. Instalar o Docker
 
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y ca-certificates curl gnupg lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
 
### 3. Clonar e subir o FIWARE
 
```bash
git clone https://github.com/fabiocabrini/fiware
cd fiware
sudo docker compose up -d
```
 
### 4. Configurar o Firewall
 
```bash
sudo ufw enable
sudo ufw allow 22
sudo ufw allow 1026
sudo ufw allow 1883
sudo ufw allow 4041
sudo ufw allow 8666
```
 
### 5. Verificar containers
 
```bash
sudo docker ps
```
 
Containers esperados:
- ✅ fiware-orion
- ✅ fiware-iot-agent
- ✅ fiware-mosquitto
- ✅ fiware-sth-comet
- ✅ fiware-mongo-internal
- ✅ fiware-mongo-historical
 
---
 
## 🔍 Health Check
 
Utilize a coleção do Postman disponível no repositório [FIWARE Descomplicado](https://github.com/fabiocabrini/fiware).
 
Configure a variável de ambiente `url` com o IP da VM e execute:
 
| Serviço | Endpoint | Método |
|---------|----------|--------|
| IoT Agent | `http://{url}:4041/iot/about` | GET |
| STH-Comet | `http://{url}:8666/version` | GET |
| Orion Context Broker | `http://{url}:1026/version` | GET |
 
---
 
## 💡 Configuração do Dispositivo (ESP32)
 
### Tópicos MQTT
 
| Tópico | Função |
|--------|--------|
| `/TEF/lamp001/cmd` | Receber comandos |
| `/TEF/lamp001/attrs` | Publicar atributos |
| `/TEF/lamp001/attrs/l` | Publicar luminosidade |
 
### Variáveis do código
 
```cpp
const char* default_SSID = "FIAP-IOT";          // Rede Wi-Fi
const char* default_PASSWORD = "F!@p25.IOT";    // Senha Wi-Fi
const char* default_BROKER_MQTT = "158.23.59.148"; // IP do Broker
const int default_BROKER_PORT = 1883;            // Porta MQTT
const char* default_ID_MQTT = "fiware_001";      // ID MQTT
const int default_D4 = 2;                        // Pino do LED onboard
```
 
---
 
## 🧪 Simulação (Wokwi)
 
1. Acesse [wokwi.com]([https://wokwi.com](https://wokwi.com/projects/459965383796606977))
2. Crie um novo projeto com **ESP32**
3. Adicione o componente **LDR** conectado ao pino analógico (GPIO34)
4. Cole o código do ESP32
5. Execute a simulação e verifique os dados chegando no FIWARE via Postman
 
---
 
## 📡 Entidade NGSI v2
 
```json
{
  "id": "urn:ngsi-ld:SmartLamp:001",
  "type": "SmartLamp",
  "luminosity": {
    "type": "Number",
    "value": 0
  },
  "status": {
    "type": "Text",
    "value": "ON"
  }
}
```
 
---
 
## 📁 Repositório de Referência
 
- [FIWARE Descomplicado — Fábio Cabrini](https://github.com/fabiocabrini/fiware)
 
---
 
## 👨‍💻 Autor
 
Projeto desenvolvido para o Check Point de IoT — FIAP  
Grupo: 
- Nicolas Forcione de Oliveira e Souza – RM566998  
- Alexandre Constantino Furtado Junior – RM567188  
- Enrico Dellatorre da Fonseca– RM566824  
- Leonardo Batista de Souza – RM568558  
- Matheus Freitas dos Santos– RM567337
