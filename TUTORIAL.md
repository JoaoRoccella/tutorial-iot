# Tutorial IoT

Para este projeto, você precisará dos seguintes componentes:
- Raspberry Pi 3 (ou qualquer modelo com GPIO)
- Arduino Uno
- Sensor DHT11 (para medir temperatura e umidade)
- Notebook ou computador para programar o Raspberry Pi e o Arduino
- Fios jumper para conexões
- Fonte de alimentação para o Raspberry Pi
- Case para o Raspberry Pi (opcional, mas recomendado)
- Placa de ensaio (breadboard) para facilitar as conexões
- Cartão microSD (com pelo menos 8GB) para o Raspberry Pi
- Cabo USB para conectar o Arduino ao Raspberry Pi
- Acesso à internet (no notebook ou computador) para instalar bibliotecas e dependências e atualizar o Raspberry Pi

## Criação de um hotspot Wi-Fi no notebook

Você vai precisar de um ponto de conexão Wi-Fi compartilhado a partir do seu computador ou notebook para que ganhar acesso ao Raspberry Pi e para que ele possa se conectar à internet para atualizar os arquivos do SO e para instalar os demais softwares de desenvolvimento. Para criar um hotspot Wi-Fi no seu notebook, siga os seguintes passos:
1. Abra o menu inicial do seu sistema operacional.
2. Procure por "Hotspot móvel"
3. Em "Propriedades", configure o nome da rede (SSID) e a senha.
4. Ative o hotspot móvel.
5. Teste a conexão com outro dispositivo, como o seu celular, para garantir que o hotspot está funcionando corretamente. Mantenha o celular conectado ao hotspot para manter a conexão ativa enquanto você configura o Raspberry Pi.

## Criação da Imagem para o Raspberry Pi

Antes de iniciarmos as configurações do Raspberry Pi, é necessário que você tenha um sistema operacional instalado no cartão microSD. Você pode usar o Raspberry Pi OS, que é recomendado para iniciantes. Siga os seguintes passos para criar o cartão microSD com o Raspberry Pi OS:
1. Baixe e instale a ferramenta Raspberry Pi Imager do site oficial (será necessário que o instrutor digite a senha de administrador do computador no momento da instalação).
2. Insira o cartão microSD no leitor de cartões do seu computador.
3. Abra o Raspberry Pi Imager e selecione o tipo de dispositivo Raspberry Pi (nesse caso, Raspberry Pi 3).
4. Escolha o sistema operacional (recomendado: Raspberry Pi OS Lite x64).
5. Selecione o cartão microSD como destino.
6. Clique em "Seguinte". Uma caixa de diálogo aparecerá perguntando se você deseja editar as definições. Clique em "Sim" e configure as seguintes opções:
   - Ativar SSH
   - Nomear o dispositivo (recomendado: "rpi<número do grupo>")
   - Definir um nome de usuário e senha (recomendado: usuário "pi" e senha "raspberry")
   - Configurar o Wi-Fi com o SSID e a senha do hotspot criado anteriormente
7. Clique em "Gravar" e aguarde a conclusão do processo (cerca de 15 minutos).
8. Após a gravação, não remova ainda o cartão microSD do computador. 
9. Abra o diretório do cartão microSD no seu computador e crie um arquivo chamado `wpa_supplicant.conf` com o seguinte conteúdo:
   ```plaintext
   country=BR
   ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
   update_config=1

   network={
       ssid="SeuSSID"
       psk="SuaSenha"
       key_mgmt=WPA-PSK
   }
   ```
10. Substitua `SeuSSID` e `SuaSenha` pelo nome e senha do seu hotspot Wi-Fi.
11. Salve o arquivo.
12. Crie um arquivo vazio chamado `ssh` (sem extensão) no diretório raiz do cartão microSD. Isso ativará o SSH automaticamente quando o Raspberry Pi for iniciado.
13. Ejetar o cartão microSD com segurança do computador. 

## Configuração do Raspberry Pi

Agora que você tem o cartão microSD preparado, insira-o no Raspberry Pi e ligue-o. O Raspberry Pi deve iniciar automaticamente e conectar-se ao hotspot Wi-Fi criado anteriormente. Siga os passos abaixo para configurar o Raspberry Pi:

1. Conecte o Raspberry Pi à fonte de alimentação e aguarde alguns minutos para que ele inicie completamente.

2. Verifique na tela do seu notebook se o Raspberry Pi está conectado ao hotspot Wi-Fi. Você pode visualizar os dispositivos conectados no painel de controle do hotspot.

3. Abra um terminal no seu notebook e conecte-se ao Raspberry Pi via SSH usando o comando:
    ```bash
    ssh <usuario>@<endereço_IP_do_Raspberry_Pi>
    ```
    
    Substitua `<usuario>` pelo usuário configurado na imagem do SO gravado no cartão microSD (aquele editado em "definições") e `<endereço_IP_do_Raspberry_Pi>` pelo endereço IP ou hostname do Raspberry Pi, que pode ser encontrado na lista de dispositivos conectados ao hotspot.

    > ___
    > ℹ️ Se você não souber o endereço IP do Raspberry Pi, você pode usar o comando `arp -a` no terminal do seu notebook para listar os dispositivos conectados à rede e encontrar o IP correspondente ao Raspberry Pi.
    > ___

    > ___
    > ⚠️ Se houver conflito de certificado, devido ao uso de um IP já existente, você pode usar o comando `ssh-keygen -R <endereço_IP_do_Raspberry_Pi>` para remover a chave antiga e tentar novamente a conexão SSH.
    > ___

4. Digite a senha configurada anteriormente quando solicitado.

    > ___
    > ℹ️ Se a conexão for bem-sucedida, você verá o terminal do Raspberry Pi (geralmente algo como `pi@rpi3:~$`).
    > ___

5. Após conectar-se com sucesso, verifique se o tempo do sistema está correto com o comando:
   ```bash
   date
   ```
   Se o horário estiver incorreto, você pode ajustá-lo manualmente:
    ```bash
    sudo date -s "YYYY-MM-DD HH:MM:SS"
    ```
    Substitua `YYYY-MM-DD HH:MM:SS` pela data e hora corretas. 

6. Verifique se o Raspberry Pi está conectado à internet executando o comando:
   ```bash
   ping -c 4 google.com
   ```
   Se você receber respostas, significa que a conexão está funcionando corretamente.

7. Atualize o sistema operacional do Raspberry Pi com os seguintes comandos:
   ```bash
   sudo apt update
   sudo apt upgrade -y
   ```

8. Caso necessário, podemos criar uma memória de swap para melhorar o desempenho do Raspberry Pi. Execute os seguintes comandos:
    ```bash
    # Instalando o dphys-swapfile
    sudo apt install dphys-swapfile -y
    sudo systemctl stop dphys-swapfile

    # Editando o tamanho da memória swap
    sudo dphys-swapfile swapoff
    sudo nano /etc/dphys-swapfile
    # Altere: CONF_SWAPSIZE=1024
    sudo dphys-swapfile setup
    sudo dphys-swapfile swapon

    # Reiniciando o serviço
    sudo systemctl start dphys-swapfile
    sudo systemctl enable dphys-swapfile

    # Verificando o tamanho da memória swap
    free -h
    ```
    Isso aumentará o tamanho da memória swap para 1GB, o que pode ser útil para compilações pesadas ou aplicações que exigem mais memória. Podemos considerar 1GB de swap como um valor razoável para a maioria dos projetos IoT.

9. Verifique se o gerenciamento de energia da interface wlan0 está desativado para evitar desconexões inesperadas. Execute o seguinte comando:
   ```bash
   sudo iwconfig wlan0 power off
   ```
   Isso garantirá que o Wi-Fi permaneça ativo mesmo quando o Raspberry Pi estiver em modo de economia de energia.

> ___
> Como dica adicional, você pode configurar o Raspberry Pi para se reconectar automaticamente ao hotspot Wi-Fi caso a conexão seja perdida. Para isso, edite o arquivo `wpa_supplicant.conf` novamente e adicione a linha `scan_ssid=1` dentro do bloco `network`, como mostrado abaixo:
> ```plaintext
> network={
>     ssid="SeuSSID"
>     psk="SuaSenha"
>     key_mgmt=WPA-PSK
>     scan_ssid=1
> }
> ```
> Isso ajudará o Raspberry Pi a encontrar e se reconectar ao hotspot mesmo que ele não esteja visível inicialmente.
> ___

## Instalação do Arduino CLI no Raspberry Pi

Para programar o Arduino a partir do Raspberry Pi, você precisará instalar o Arduino CLI. Siga os passos abaixo:
1. No terminal do Raspberry Pi, baixe o Arduino CLI com o seguinte comando:
   ```bash
   curl -fsSL https://raw.githubusercontent.com/arduino/arduino-cli/master/install.sh | sh
   ```
2. Após a instalação, adicione o Arduino CLI ao PATH do sistema:
    ```bash
    echo 'export PATH=$PATH:~/bin' >> ~/.bashrc
    source ~/.bashrc
    ```
3. Verifique se a instalação foi bem-sucedida executando:
   ```bash
    arduino-cli version
    ```
4. Atualize o índice de pacotes do Arduino CLI:
   ```bash
   arduino-cli config init
   arduino-cli core update-index
   ```
5. Instale o core do Arduino Uno:
   ```bash
    arduino-cli core install arduino:avr
    ```
6. Verifique se o core foi instalado corretamente:
   ```bash
   arduino-cli core list
   ```
7. Conecte o Arduino Uno ao Raspberry Pi via cabo USB.
8. Verifique se o Arduino foi reconhecido pelo Raspberry Pi com o comando:
   ```bash
   arduino-cli board list
   ```
   Você deve ver o Arduino listado com a porta correspondente.

9. Instale a biblioteca DHT11 necessária para o sensor de temperatura e umidade:
   ```bash
   arduino-cli lib install "DHT sensor library"
   ```
10. Verifique se a biblioteca foi instalada corretamente:
    ```bash
    arduino-cli lib list
    ```

## Programação do Arduino (Teste)

Vamos testar se o Arduino está funcionando corretamente com um exemplo simples. Siga os passos abaixo:

1. Crie um arquivo chamado `blink.ino` com o seguinte conteúdo:
```cpp
void setup() {
  pinMode(LED_BUILTIN, OUTPUT);
}
void loop() {
  digitalWrite(LED_BUILTIN, HIGH); // Liga o LED
  delay(1000);                     // Espera por 1 segundo
  digitalWrite(LED_BUILTIN, LOW);  // Desliga o LED
  delay(1000);                     // Espera por 1 segundo
}
```
2. Compile o código para o Arduino com o seguinte comando:
   ```bash
   arduino-cli compile --fqbn arduino:avr:uno blink.ino
   ```
3. Carregue o código no Arduino com o comando:
   ```bash
   arduino-cli upload -p /dev/ttyUSB0 --fqbn arduino:avr:uno blink.ino
   ```
4. Verifique se o LED embutido no Arduino está piscando a cada segundo. Se sim, o Arduino está funcionando corretamente.

## Conexão do Sensor DHT11 ao Arduino

Agora que o Arduino está funcionando, vamos conectar o sensor DHT11 e programá-lo para ler a temperatura e umidade. Siga os passos abaixo:
1. Conecte o sensor DHT11 ao Arduino da seguinte forma:
   - VCC do DHT11 ao 5V do Arduino
   - GND do DHT11 ao GND do Arduino
   - Data do DHT11 ao pino digital 2 do Arduino
2. Crie um arquivo chamado `dht11.ino` com o seguinte conteúdo:
```cpp
#include <DHT.h>
#define DHTPIN 2     // Pino onde o DHT11 está conectado
#define DHTTYPE DHT11 // Tipo do sensor DHT
DHT dht(DHTPIN, DHTTYPE);
void setup() {
    Serial.begin(9600); // Inicializa a comunicação serial
    dht.begin();         // Inicializa o sensor DHT11
}
void loop() {
    delay(2000); // Espera 2 segundos entre leituras
    float h = dht.readHumidity();    // Lê a umidade
    float t = dht.readTemperature(); // Lê a temperatura em Celsius
    if (isnan(h) || isnan(t)) {
        Serial.println("Falha ao ler do DHT11!");
        return;
    }
    Serial.print("Umidade: ");
    Serial.print(h);
    Serial.print("%, Temperatura: ");
    Serial.print(t);
    Serial.println("°C");
}
```
3. Compile o código para o Arduino com o seguinte comando:
   ```bash
   arduino-cli compile --fqbn arduino:avr:uno dht11.ino
   ```
4. Carregue o código no Arduino com o comando:
   ```bash
   arduino-cli upload -p /dev/ttyUSB0 --fqbn arduino:avr:uno dht11.ino
   ``` 
5. Abra o monitor serial do Arduino com o comando:
   ```bash  
   arduino-cli monitor -p /dev/ttyUSB0 --baud 9600
   ```

Você deve ver as leituras de temperatura e umidade sendo exibidas no monitor serial a cada 2 segundos.

## Instalando um broker MQTT no Raspberry Pi

Para enviar os dados do sensor DHT11 para a nuvem, vamos instalar um broker MQTT no Raspberry Pi. O Mosquitto é uma opção popular e fácil de usar. Siga os passos abaixo:

1. No terminal do Raspberry Pi, instale o Mosquitto com o seguinte comando:
   ```bash
   sudo apt install mosquitto mosquitto-clients -y
   ```
2. Após a instalação, inicie o serviço Mosquitto:
   ```bash
   sudo systemctl start mosquitto
   ```
3. Verifique se o Mosquitto está rodando corretamente:
    ```bash
    sudo systemctl status mosquitto
    ```
    Você deve ver uma mensagem indicando que o serviço está ativo e rodando.
4. Para garantir que o Mosquitto inicie automaticamente ao ligar o Raspberry Pi, execute:
    ```bash
    sudo systemctl enable mosquitto
    ```
5. Teste o broker MQTT publicando uma mensagem de teste:
    ```bash
    mosquitto_pub -h localhost -t "test/topic" -m "Hello, MQTT!"
    ```
6. Para verificar se a mensagem foi recebida, abra outro terminal e execute:
    ```bash
    mosquitto_sub -h localhost -t "test/topic"
    ```
   Você deve ver a mensagem "Hello, MQTT!" sendo exibida.

### Habilitando a persistência do Mosquitto

Para garantir que os dados publicados no broker MQTT sejam persistentes, você pode habilitar a persistência no Mosquitto. Isso significa que, mesmo após reiniciar o serviço ou o Raspberry Pi, as mensagens publicadas serão mantidas. Isso é útil para garantir que os dados não sejam perdidos. O Mosquitto armazena as mensagens em um diretório específico utilizando um arquivo de log. Para habilitar a persistência, siga os passos abaixo: 

1. Abra o arquivo de configuração do Mosquitto:
   ```bash
   sudo nano /etc/mosquitto/mosquitto.conf
   ```

2. Adicione as seguintes linhas ao final do arquivo para habilitar a persistência:
   ```plaintext
    persistence true
    persistence_location /var/lib/mosquitto/
    log_dest file /var/log/mosquitto/mosquitto.log
    log_dest stdout
    log_type all
    allow_anonymous true
   ```

3. Salve e feche o arquivo (no nano, pressione `CTRL + X`, depois `Y` e `Enter`).

4. Reinicie o serviço Mosquitto para aplicar as alterações:
   ```bash
   sudo systemctl restart mosquitto
   ```

5. Verifique se a persistência está funcionando corretamente publicando uma mensagem de teste novamente:
   ```bash
    mosquitto_pub -h localhost -t "test/topic" -m "Hello, MQTT with persistence!"
   ```

6. Em outro terminal, execute o comando de assinatura para verificar se a mensagem foi recebida:
    ```bash
    mosquitto_sub -h localhost -t "test/topic"
    ```

    Você deve ver a mensagem "Hello, MQTT with persistence!" sendo exibida, confirmando que a persistência está funcionando corretamente. A diferença é que agora, mesmo que o Mosquitto seja reiniciado, as mensagens publicadas serão mantidas no diretório de persistência especificado.


## Enviando dados do DHT11 para o broker MQTT

Agora que o broker MQTT está configurado, vamos modificar o código do Arduino para enviar os dados do sensor DHT11 para o broker MQTT. Como nossa placa de Arduino não possui suporte a Wi-Fi, vamos usar o Raspberry Pi para receber os dados do Arduino via USB e, em seguida, publicar esses dados no broker MQTT. Siga os passos abaixo:
1. No Raspberry Pi, crie um novo arquivo chamado `dht11_mqtt.py` com o seguinte conteúdo:
```python
import paho.mqtt.client as mqtt
import serial
import time
# Configurações do MQTT
MQTT_BROKER = "localhost"
MQTT_PORT = 1883
MQTT_TOPIC = "sensor/dht11"
# Configuração da porta serial do Arduino
SERIAL_PORT = "/dev/ttyUSB0"
SERIAL_BAUDRATE = 9600
# Inicializa o cliente MQTT
client = mqtt.Client()
client.connect(MQTT_BROKER, MQTT_PORT, 60)
# Inicializa a porta serial
ser = serial.Serial(SERIAL_PORT, SERIAL_BAUDRATE)
time.sleep(2)  # Aguarda a conexão serial estabilizar
try:
    while True:
        if ser.in_waiting > 0:
            line = ser.readline().decode('utf-8').strip()
            print(f"Recebido do Arduino: {line}")
            # Publica os dados no broker MQTT
            client.publish(MQTT_TOPIC, line)
except KeyboardInterrupt:
    print("Programa interrompido.")
finally:
    ser.close()
    client.disconnect()
```
2. Instale a biblioteca Paho MQTT no Raspberry Pi com o seguinte comando:
   ```bash
   pip install paho-mqtt
   ```
3. Execute o script Python para iniciar a leitura dos dados do Arduino e publicá-los no broker MQTT:
   ```bash
    python dht11_mqtt.py
    ```
4. No terminal do Raspberry Pi, abra outro terminal e execute o comando para assinar o tópico MQTT e verificar se os dados estão sendo recebidos:
    ```bash
    mosquitto_sub -h localhost -t "sensor/dht11"
    ```
    Você deve ver as leituras de temperatura e umidade sendo exibidas no terminal a cada 2 segundos.

### Desafio: publicando dados em um broker MQTT remoto

Você deverá publicar os dados do sensor DHT11 em um broker MQTT remoto, o HiveMQ, que é um serviço gratuito de broker MQTT. Para isso, siga os passos abaixo:


1. No arquivo `dht11_mqtt.py`, modifique as configurações do broker MQTT para usar o HiveMQ:
    ```python
    MQTT_BROKER = "339b63a59e2748728069335a1b73efea.s1.eu.hivemq.cloud"
    MQTT_PORT = 8883
    MQTT_TOPIC = "/sensor/dht11/setor_x"  # Substitua o "x" pelo numero do seu grupo
    MQTT_USERNAME = "seu_usuario"  # Substitua pelo seu usuário do HiveMQ
    MQTT_PASSWORD = "sua_senha"  # Substitua pela sua senha do HiveMQ   
    ```
2. Adicione as seguintes linhas após a inicialização do cliente MQTT para configurar o usuário e senha:
    ```python
    client.username_pw_set(MQTT_USERNAME, MQTT_PASSWORD)
    client.tls_set()  # Configura TLS para conexão segura
    ```
3. Faça o upload do código modificado para o Arduino novamente:
    ```bash
    arduino-cli upload -p /dev/ttyUSB0 --fqbn arduino:avr:uno dht11.ino
    ```
4. Execute o script Python novamente:
    ```bash
    python dht11_mqtt.py
    ```
5. Abra outro terminal e execute o comando para assinar o tópico MQTT no HiveMQ:
    ```bash
    mosquitto_sub -h 339b63a59e2748728069335a1b73efea.s1.eu.hivemq.cloud -p 8883 -t "/sensor/dht11/setor_x" --cafile /etc/ssl/certs/ca-certificates.crt -u seu_usuario -P sua_senha
    ```
    Substitua `seu_usuario` e `sua_senha` pelos seus dados do HiveMQ. Você deve ver as leituras de temperatura e umidade sendo exibidas no terminal a cada 2 segundos.


## Armazenamento dos dados em um banco de dados

Em algumas situações, somente a persistência do broker MQTT não é suficiente, pois você pode precisar de uma forma mais estruturada de armazenar e consultar os dados. Para isso, vamos usar um banco de dados SQLite para armazenar as leituras do sensor DHT11. O SQLite é um banco de dados leve e fácil de usar, ideal para projetos pequenos como este. Siga os passos abaixo para configurar o armazenamento dos dados: 

1. No Raspberry Pi, instale o SQLite com o seguinte comando:
    ```bash
    sudo apt install sqlite3 -y
    ```
2. Crie um banco de dados SQLite chamado `dht11_data.db` e uma tabela para armazenar os dados do sensor:
    ```bash
    sqlite3 dht11_data.db "CREATE TABLE IF NOT EXISTS leituras_ambiente (id INTEGER PRIMARY KEY AUTOINCREMENT, datahora DATETIME DEFAULT CURRENT_TIMESTAMP, temperatura REAL, umidade REAL);"
    ```
3. Crie um novo arquivo chamado `dht11_db.py` com o seguinte conteúdo:
    ```python
    import sqlite3
    import paho.mqtt.client as mqtt
    import time
    # Configurações do banco de dados
    DB_FILE = "dht11_data.db"
    # Configurações do MQTT
    MQTT_BROKER = "localhost"
    MQTT_PORT = 1883
    MQTT_TOPIC = "sensor/dht11"
    # Função para inserir dados no banco de dados
    def insert_data(temperatura, umidade):
        conn = sqlite3.connect(DB_FILE)
        cursor = conn.cursor()
        cursor.execute("INSERT INTO leituras_ambiente (temperatura, umidade) VALUES (?, ?)", (temperatura, umidade))
        conn.commit()
        conn.close()
    # Callback quando uma mensagem é recebida
    def on_message(client, userdata, message):
        payload = message.payload.decode('utf-8')
        print(f"Mensagem recebida: {payload}")
        try:
            temperatura, umidade = map(float, payload.split(","))
            insert_data(temperatura, umidade)
            print(f"Dados inseridos no banco de dados: Temperatura={temperatura}, Umidade={umidade}")
        except ValueError:
            print("Erro ao processar os dados recebidos.")
    # Inicializa o cliente MQTT
    client = mqtt.Client()
    client.on_message = on_message
    client.connect(MQTT_BROKER, MQTT_PORT, 60)
    client.subscribe(MQTT_TOPIC)
    # Inicia o loop do cliente MQTT
    client.loop_start()
    try:
        while True:
            time.sleep(1)  # Mantém o loop ativo
    except KeyboardInterrupt:
        print("Programa interrompido.")
    finally:
        client.loop_stop()
        client.disconnect()
    ```
4. Execute o script Python para iniciar a leitura dos dados do broker MQTT e armazená-los no banco de dados:
    ```bash
    python dht11_db.py
    ```
5. Verifique se os dados foram inseridos corretamente no banco de dados executando o seguinte comando:
    ```bash
    sqlite3 dht11_data.db "SELECT * FROM leituras_ambiente;"
    ```
    Você deve ver as leituras de temperatura e umidade armazenadas no banco de dados.

## Servindo os dados via API RESTful

Agora que temos os dados armazenados no banco de dados, vamos criar uma API RESTful no notebook para acessar esses dados e servi-los para outros dispositivos. Vamos usar o FastAPI, que é uma biblioteca moderna e fácil de usar para criar APIs em Python. Siga os passos abaixo:
1. No notebook, instale o FastAPI com o seguinte comando:
   ```bash
   pip install fastapi[standard]
   ```
2. Crie um novo arquivo chamado `app.py` com o seguinte conteúdo:
```python
from fastapi import FastAPI
from fastapi.responses import JSONResponse
import sqlite3
app = FastAPI()
@app.get("/leituras")
def get_leituras():
    conn = sqlite3.connect("dht11_data.db")
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM leituras_ambiente ORDER BY datahora DESC LIMIT 10;")
    leituras = cursor.fetchall()
    conn.close()
    return JSONResponse(content=[{"id": id, "datahora": datahora, "temperatura": temperatura, "umidade": umidade} for id, datahora, temperatura, umidade in leituras])
```
3. Execute o aplicativo FastAPI com o seguinte comando:
   ```bash
   fastapi run
   ```
4. Abra um navegador e acesse `http://localhost:8000/leituras`. Você deve ver as últimas 10 leituras de temperatura e umidade em formato JSON.

### Desafio: obtendo dados históricos

1. Crie uma rota adicional na API para obter dados históricos de temperatura e umidade. A rota deve aceitar parâmetros de data inicial e final para filtrar as leituras. Por exemplo, a rota pode ser `/leituras/historico?inicio=2023-01-01&fim=2023-01-31`.

2. Crie algumas rotas adicionais na API para obter dados historicos de temperatura e umidade, semelhante à rota anterior, mas que possa ser consultada por um período predefinido, como as últimas 24 horas ou os últimos 7 dias. Por exemplo, a rota pode ser `/leituras/historico/24h`, `/leituras/historico/7d`, etc.


## Consumindo a API RESTful com uma aplicação Mobile

Para consumir a API RESTful criada no notebook, você pode usar uma aplicação mobile. Vamos criar um exemplo simples usando o React Native, que é uma biblioteca popular para desenvolvimento de aplicativos móveis. Siga os passos abaixo:
1. Certifique-se de ter o Node.js e o React Native CLI instalados no seu notebook.
2. Crie um novo projeto React Native com o seguinte comando:
    ```bash
    npx react-native init IoTApp
    ```
3. Navegue até o diretório do projeto:
    ```bash
    cd IoTApp
    ```
4. Abra o arquivo `App.js` e substitua o conteúdo pelo seguinte código:
```javascript
import React, { useEffect, useState } from 'react';
import { View, Text, FlatList, StyleSheet } from 'react-native';
const App = () => {
  const [leituras, setLeituras] = useState([]);
  
  useEffect(() => {
    fetch('http://<endereço_IP_do_notebook>:8000/leituras')
      .then(response => response.json())
      .then(data => setLeituras(data))
      .catch(error => console.error('Erro ao buscar leituras:', error));
  }, []);
  
  return (
    <View style={styles.container}>
      <Text style={styles.title}>Leituras de Temperatura e Umidade</Text>
      <FlatList
        data={leituras}
        keyExtractor={item => item.id.toString()}
        renderItem={({ item }) => (
          <View style={styles.item}>
            <Text>ID: {item.id}</Text>
            <Text>Data/Hora: {item.datahora}</Text>
            <Text>Temperatura: {item.temperatura} °C</Text>
            <Text>Umidade: {item.umidade} %</Text>
          </View>
        )}
      />
    </View>
  );
};
const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 20,
    backgroundColor: '#fff',
  },
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 20,
  },
  item: {
    padding: 10,
    borderBottomWidth: 1,
    borderBottomColor: '#ccc',
  },
});
export default App;
```

5. Substitua `<endereço_IP_do_notebook>` pelo endereço IP do notebook onde a API FastAPI está rodando.

6. Execute o aplicativo React Native com o seguinte comando:
   ```bash
   npx react-native run-android
   ```
   ou, se estiver usando iOS:
   ```bash
   npx react-native run-ios
   ```

7. Para instalar o app em seu celular, você pode usar o Expo ou criar um APK para Android. Consulte a documentação do React Native para mais detalhes sobre como fazer isso.


