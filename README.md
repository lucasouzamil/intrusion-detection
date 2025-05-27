
# Sistema de Detecção de Intrusão em Tempo Real

Integração entre **CICFlowMeter** (Java) para captura de tráfego e envio de fluxos CSV, e um **Dashboard** (FastAPI + frontend) para predição e visualização em tempo real.

Vídeo:

[![Assistir ao vídeo de demonstração e explicação](http://img.youtube.com/vi/8WfmkdcId_Y/0.jpg)](https://youtu.be/8WfmkdcId_Y?si=KSjdrt6PVae30BHY)


## 🔗 Referência do Modelo

O modelo de Machine Learning (`modelo.pkl`) já está incluído no repositório do Dashboard. Para detalhes sobre pré-processamento e treinamento, consulte (apenas para referência):

* [Repositório de Análise e Treinamento](https://github.com/lucasouzamil/Intrusion-Detection-CICIDS2017)


## 🏗️ Arquitetura Geral

```text
[ Rede ]
   ↓
[CICFlowMeter (Java)] --(CSV via HTTP)--> [Dashboard FastAPI] --(predição)--> [Fluxos com predição]
   ↓                                         ↓
[ API /predict ]                           [ API /flows ]
                                             ↓
                                      [Frontend Web]
```


## 💻 Pré-requisitos

* **Java JDK 8+**, **Maven**, **libpcap-dev** (captura de pacotes)
* **Python 3.8+**, **pip**
* Ambiente virtual (recomendado) para Python


## 🚀 Instalação e Execução

Para qualquer dúvida consulte:

* [Repositório CICFlowMterAPI](https://github.com/lucasouzamil/CICFlowMeter.git)

* [Repositório Dashboard](https://github.com/lucasouzamil/CICIntrusionDetection-DashBoard.git)

### 1. Clonar repositórios

```bash
git clone https://github.com/lucasouzamil/CICFlowMeter.git
git clone https://github.com/lucasouzamil/CICIntrusionDetection-DashBoard.git
```

### 2. Configurar e executar o CICFlowMeter (Java)

```bash
# Instalar dependências do sistema
git clone https://github.com/lucasouzamil/CICFlowMeter.git
cd CICFlowMeter/jnetpcap/linux/jnetpcap-1.4.r1425
sudo apt update && sudo apt install openjdk-11-jdk maven libpcap-dev
sudo mvn install:install-file -Dfile=jnetpcap.jar \
    -DgroupId=org.jnetpcap -DartifactId=jnetpcap \
    -Dversion=1.4.1 -Dpackaging=jar
cd ../../../

# Capturar tráfego e enviar ao Dashboard
sudo ./gradlew liveCapture \
  -Piface=<sua_interface> \
  -Pendpoint=http://localhost:8000/predict
```

### 3. Configurar e executar o Dashboard

```bash
# Dashboard
cd CICIntrusionDetection-DashBoard
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# Certifique-se de que `modelos/modelo.pkl` está presente
uvicorn app:app --reload --host 0.0.0.0 --port 8000
```

Abra **[http://localhost:8000](http://localhost:8000)** no navegador.


## ⚙️ Endpoints Principais

* `POST /predict`: recebe linhas CSV de fluxos pelo CICFlowMeter.
* `GET /flows`: retorna JSON com fluxos recentes e predições.


## 📂 Estrutura de Pastas

```
root/
├── CICFlowMeter/                        # Captura de tráfego e envio por Java
└── CICIntrusionDetection-DashBoard/     # Dashboard FastAPI + frontend
```


## 🔮 Futuras Melhorias

* **Modelo de Predição em Dois Estágios**:

  * Primeiro, implementar um classificador binário para distinguir **ataque** de **não-ataque**.
  * Em caso de ataque, realizar predição multiclasses para identificar o tipo específico de ameaça.

* **Dashboard Interativo**:

  * Adicionar filtros dinâmicos por **IP de origem** e **tipo de ataque**.
  * Implementar sistema de **salvamento periódico** dos dados de fluxo, permitindo consultas históricas por período.


## 📝 Autor

**Autor:** [Lucas Lima](https://github.com/lucasouzamil)