# Monitoramento Ambiental com IoT: Temperatura e Umidade em Tempo Real

Este repositório é um tutorial de IoT (Internet das Coisas). A ideia é apresentar conceitos básicos e avançados de IoT, utilizando o Arduino como plataforma de desenvolvimento e o Raspberry Pi como servidor.

## Situação-Problema (Contexto Profissional):

Imagine uma empresa agrícola de pequeno porte que está começando a adotar tecnologias da Indústria 4.0 para otimizar a produção em estufas automatizadas. Um dos primeiros passos é o monitoramento em tempo real das condições ambientais dentro dessas estufas — temperatura e umidade —, para ajustar automaticamente sistemas de irrigação, ventilação e aquecimento.

O responsável pelo setor de TI precisa desenvolver um sistema simples e de baixo custo capaz de:

- Coletar os dados de sensores ambientais,
- Transmitir esses dados via rede local utilizando um protocolo leve e eficiente,
- Armazenar os dados para análise posterior,
- Permitir o acesso remoto às informações em tempo real através de uma interface.

A equipe de desenvolvedores júnior da empresa — representada por você e seus colegas — será responsável por elaborar o protótipo funcional do sistema, usando hardware disponível (Arduino Uno, sensor DHT11 e Raspberry Pi), conectividade por MQTT e visualização web com Flask.

### Desafio:

Desenvolver um sistema de monitoramento ambiental usando um sensor DHT11 conectado a um Arduino Uno, que se comunica via serial com um Raspberry Pi atuando como gateway MQTT. O Raspberry Pi hospedará o broker Mosquitto com persistência e servirá os dados aos clientes da rede Wi-Fi criada com hotspot. O sistema deverá disponibilizar esses dados em tempo real e permitir visualização histórica em uma interface web simples.

### Entrega:

Ao final deste projeto, você deverá ser capaz de:

- Coletar e transmitir dados de sensores por MQTT.
- Configurar e operar um broker Mosquitto com persistência.
- Utilizar o Arduino-CLI para programar e interagir com o microcontrolador.
- Criar uma aplicação Flask para consumir dados MQTT e exibir gráficos ambientais.
- Trabalhar em equipe, aplicando conceitos técnicos de IoT, redes e programação.



