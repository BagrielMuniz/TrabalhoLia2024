# TrabalhoLia2024
## Sistema de Alarme Simples com Arduino
### Materiais Necessários

- Arduino Uno ou qualquer outro modelo de Arduino.
- Sensor de Movimento (PIR) - para detectar movimentos.
- Buzzer - para emitir um som quando o alarme disparar.
- LEDs - para indicar o status do alarme (ativado, desativado, disparado).
- Resistores - para os LEDs.
- Botão de Ativação/Desativação - para ligar e desligar o sistema.
- Jumpers e Protoboard - para as conexões.

### Sistema de Alarme Simples com Arduino

Este sistema de alarme simples utiliza um **sensor de movimento PIR**, **buzzer**, **LEDs** e um **botão de ativação/desativação** para detectar movimento e emitir um som de alarme. O sistema pode ser facilmente configurado utilizando um Arduino Uno e alguns componentes básicos.

---

### **Funcionamento do Sistema**

1. **Estado de Inatividade**:
   - O sistema começa em estado de "inatividade" com o LED de estado desativado.
   - O botão de ativação/desativação permite ligar ou desligar o alarme. Ao pressionar o botão, o sistema alterna entre os estados de **"ativado"** e **"desativado"**.
   - Quando o sistema está ativado, o LED indicativo acende para sinalizar que o alarme está pronto para detectar movimento.

2. **Detecção de Movimento**:
   - Quando o sensor PIR detecta movimento, ele envia um sinal ao Arduino, que ativa o **buzzer** e o **LED de disparo**.
   - O buzzer emite um som de alarme, e o LED de disparo acende para sinalizar que o movimento foi detectado e o alarme foi disparado.

3. **Reset e Controle**:
   - O alarme continuará ativo até que o botão de ativação/desativação seja pressionado novamente, retornando o sistema ao estado de inatividade.

---

### **Esquema de Conexão**

#### **Componentes e Conexões:**

1. **Arduino Uno**:
   - Fornecerá a lógica e controle do sistema.

2. **Sensor de Movimento PIR**:
   - **VCC** → 5V (Arduino)
   - **GND** → GND (Arduino)
   - **OUT** → Pino digital 2 (Arduino)

3. **Buzzer**:
   - **Pino positivo (+)** → Pino digital 3 (Arduino)
   - **Pino negativo (-)** → GND (Arduino)

4. **LED Indicativo de Ativação** (Verde):
   - **Anodo (+)** → Pino digital 4 (Arduino) com resistor de 220Ω
   - **Catodo (-)** → GND

5. **LED Indicativo de Disparo** (Vermelho):
   - **Anodo (+)** → Pino digital 5 (Arduino) com resistor de 220Ω
   - **Catodo (-)** → GND

6. **Botão de Ativação/Desativação**:
   - **Um terminal** → Pino digital 6 (Arduino)
   - **Outro terminal** → GND

7. **Protoboard e Jumpers**:
   - Para organizar e realizar as conexões entre os componentes e o Arduino.

---

### **Esquema Fritzing (Simples)**

- O esquema Fritzing pode ser criado conectando todos os componentes ao Arduino em uma **protoboard**. O botão de ativação/desativação será conectado a um pino digital e ao GND, enquanto os LEDs, sensor PIR e buzzer serão conectados aos pinos descritos acima.
  
---

### **Código Arduino**

```cpp
// Definições de pinos
const int pirPin = 2;
const int buzzerPin = 3;
const int ledAtivo = 4;
const int ledDisparo = 5;
const int botaoPin = 6;

bool alarmeAtivado = false;
bool movimentoDetectado = false;
bool botaoPressionado = false;

void setup() {
  pinMode(pirPin, INPUT);
  pinMode(buzzerPin, OUTPUT);
  pinMode(ledAtivo, OUTPUT);
  pinMode(ledDisparo, OUTPUT);
  pinMode(botaoPin, INPUT_PULLUP);  // Configurando o botão com pull-up interno
  
  Serial.begin(9600);
}

void loop() {
  int botaoEstado = digitalRead(botaoPin);
  
  // Verifica se o botão foi pressionado
  if (botaoEstado == LOW && !botaoPressionado) {
    alarmeAtivado = !alarmeAtivado;  // Alterna o estado do alarme
    botaoPressionado = true;
    delay(200);  // Anti-debounce simples
  } else if (botaoEstado == HIGH) {
    botaoPressionado = false;
  }

  // LED indicador do estado do alarme
  if (alarmeAtivado) {
    digitalWrite(ledAtivo, HIGH);  // Liga o LED de ativação
  } else {
    digitalWrite(ledAtivo, LOW);   // Desliga o LED de ativação
    digitalWrite(ledDisparo, LOW); // Garante que o LED de disparo está apagado
    digitalWrite(buzzerPin, LOW);  // Garante que o buzzer está desligado
  }

  // Verifica se o alarme está ativado e se há movimento
  if (alarmeAtivado) {
    movimentoDetectado = digitalRead(pirPin);
    
    if (movimentoDetectado) {
      digitalWrite(ledDisparo, HIGH);  // Acende o LED de disparo
      digitalWrite(buzzerPin, HIGH);   // Aciona o buzzer
      Serial.println("Movimento detectado! Alarme disparado.");
    } else {
      digitalWrite(ledDisparo, LOW);   // Apaga o LED de disparo
      digitalWrite(buzzerPin, LOW);    // Desliga o buzzer
    }
  }
}
```

---

### **Como Funciona o Código**

1. **Configuração Inicial**:
   - Define os pinos de entrada e saída e inicia a comunicação serial para depuração.
   
2. **Controle do Alarme**:
   - O botão alterna o estado de "ativado" ou "desativado" do alarme.
   - Se o alarme está ativado, o sistema monitora o sensor PIR para detectar movimento.
   
3. **Alerta de Movimento**:
   - Quando o sensor PIR detecta movimento, o LED de disparo acende e o buzzer toca.
   - O alarme só pode ser desativado pressionando o botão novamente.
