# SDM - Sistema de Desórbita Monitorada (Simulação de Vela de Arrasto)

## Descrição do Projeto

O **SDM (Sistema de Desórbita Monitorada)** é uma prova de conceito em hardware (Arduino/C++) que simula o comportamento de um satélite de baixa órbita (LEO) em seus estágios finais de vida útil. O sistema monitora a altitude do satélite e, ao cruzar um limite crítico (threshold), aciona um mecanismo mecânico simulado de vela de arrasto (*drag sail*) para acelerar o decaimento orbital e garantir a reentrada segura na atmosfera terrestre.

## Objetivo da Solução

O objetivo principal deste projeto é demonstrar de forma didática e funcional como sistemas passivos de desórbita operam para mitigar a proliferação de lixo espacial. A solução simula a tomada de decisão autônoma do satélite com base na sua altitude, fornecendo telemetria em tempo real para análise de dados.

## Componentes Utilizados

* **1x Microcontrolador:** Arduino Uno / Nano / Mega (compatível).
* **1x Potenciômetro (10kΩ):** Utilizado para simular a variação da altitude (150 km a 400 km).
* **1x LED RGB (Cátodo Comum):** Indicador visual do status da órbita.
* **1x Micro Servo Motor (ex: SG90):** Atuador mecânico que simula o *deploy* (abertura) da vela de arrasto.
* **1x Módulo Display OLED 0.96" (SSD1306):** Interface visual I2C (128x64) para exibição de telemetria.
* **1x Sensor de Pressão/Temperatura (BMP180/BMP085):** Interface I2C para coleta de dados ambientais simulados.
* **1x Acelerômetro/Giroscópio (MPU6050):** Interface I2C para leitura da aceleração (eixo Z) e dinâmica do satélite.
* **Protoboard e Jumpers** para montagem.

## Explicação do Funcionamento

O sistema opera em ciclos de 2000 milissegundos (2 segundos), executando as seguintes rotinas:

1. **Simulação de Altitude:** O potenciômetro mapeia valores de altitude entre 150 km e 400 km. Girar para a esquerda simula uma órbita alta e estável; girar para a direita baixa a altitude.
2. **Monitoramento e Threshold:** O limite crítico está definido em **250 km**.
* **Acima de 250 km:** O satélite opera normalmente com decaimento natural (0,05 km/ciclo). Acelerômetro e barômetro podem aplicar pequenos incrementos no decaimento caso detectem anomalias.
* **Abaixo de 250 km:** A vela de arrasto é ativada automaticamente.


3. **Atuação da Vela de Arrasto (Servo Motor):**
* **Posição 10°:** Vela recolhida dentro da estrutura do satélite.
* **Posição 160°:** Vela ativada/aberta, aumentando a área de arrasto. A taxa de decaimento acelera drasticamente para 0,25 km/ciclo.
* Por algum motivo no wokwi não aparece o servo se movendo, esta dando conflito entre ele e o mega pelo q parece, ja tentei colocar ele no arduino uno mas o uno usa apenas 2kg de memoria e o projeto precisa de no minimo 6kg, não sei se a culpa esta no site ou se esta realmente no codigo, mas foram horas dedicadas a isso e ate agora o servo nao se moveu como deveria.


4. **Feedback Visual (LED RGB):**
* 🟢 **Verde:** Órbita estável (margem > 30 km do threshold).
* 🟠 **Laranja:** Órbita em alerta (menos de 30 km do threshold).
* 🔴 **Vermelho:** Nível crítico, vela de arrasto ativada.


5. **Telemetria (Display OLED e Serial):** O display OLED exibe a altitude atualizada, a taxa de decaimento, o tempo estimado de ciclos até a desórbita completa, a temperatura (BMP180) e uma barra de progresso visual mostrando a proximidade da altitude atual com a linha de *threshold*. Todos esses dados são impressos simultaneamente no Monitor Serial a 9600 baud, permitindo a extração do log para análises em TCC/Pesquisas.

##  Estrutura do Circuito (Pinagem)

| Componente | Pino no Componente | Pino no Arduino | Observação |
| --- | --- | --- | --- |
| **Potenciômetro** | Sinal (Pino Central) | **A0** | VCC em 5V e GND no GND |
| **LED RGB** | R (Red) | **D6** (PWM) | Resistor em série adequado |
| **LED RGB** | G (Green) | **D5** (PWM) | Resistor em série adequado |
| **LED RGB** | B (Blue) | **D3** (PWM) | Resistor em série adequado |
| **Servo Motor** | Sinal (Fio Laranja/Amarelo) | **D9** (PWM) | Alimentação preferencialmente externa ou 5V do Arduino |
| **Display OLED** | SDA | **A4** (ou SDA) | Barramento I2C |
| **Display OLED** | SCL | **A5** (ou SCL) | Barramento I2C |
| **BMP180/085** | SDA | **A4** (ou SDA) | Barramento I2C (Ligado em paralelo) |
| **BMP180/085** | SCL | **A5** (ou SCL) | Barramento I2C (Ligado em paralelo) |
| **MPU6050** | SDA | **A4** (ou SDA) | Barramento I2C (Ligado em paralelo) |
| **MPU6050** | SCL | **A5** (ou SCL) | Barramento I2C (Ligado em paralelo) |

## 🚀 Instruções de Execução

1. **Montagem do Hardware:** Pegue sua protoboard e conecte os componentes seguindo a tabelinha de pinagem ali de cima. Lembre-se de que o display OLED, o sensor BMP180 e o MPU6050 vão todos conectados nos mesmos pinos: A4 (SDA) e A5 (SCL).
2. **Instalação de Bibliotecas:** Se estiver usando o simulador Wokwi: Só precisa ir na aba libraries.txt do projeto e colar este bloco exatamente assim:
Adafruit BMP085 Library
Adafruit MPU6050
Adafruit SSD1306
Adafruit GFX Library
Servo
Servo Hardware PWM
* Se for montar fisicamente na IDE do Arduino: Abra o Gerenciador de Bibliotecas (o atalho Ctrl + Shift + I) e jogue esses mesmos nomes na busca para ir instalando um por um.
3.**Upload do codigo** Vá no menu "Ferramentas", confira se a placa correta (ex: Arduino Uno) e a porta USB (COM) estão selecionadas. Depois, é só clicar no botão Carregar (a setinha para a direita) e esperar ele terminar de compilar.
4.**Testes**Assim que o código subir, abra o Monitor Serial (Ctrl + Shift + M) e certifique-se de que a velocidade ali no cantinho da janela está em 9600 baud (senão os textos vão aparecer todos distorcidos).



## Integrantes do Grupo

* Gabriel Suarez — RM: 569893
* Paulo Henrique — RM: 573174
* William Barbosa — RM: 569736
* Caio Portela — RM: 572562
---
