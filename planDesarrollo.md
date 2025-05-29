# Sistema de Control Hidroeléctrico Automatizado

## **Requerimientos Técnicos**
| **Parámetro**          | **Valor/Descripción**                     | **Teoría Asociada**                                                                 |
|------------------------|------------------------------------------|------------------------------------------------------------------------------------|
| **Voltaje de corte**    | 14.4V (plomo-ácido) / 14.6V (LiFePO4)   | Evita sobrecarga. Basado en química de la batería (gasificación en plomo-ácido).   |
| **Caudal mínimo**       | 1.4 L/s (100W @ 10m) / 14 L/s (1000W)   | \( P = \rho \cdot g \cdot Q \cdot H \cdot \eta \). Depende de altura (\( H \)) y eficiencia (\( \eta \)). |
| **Frecuencia**          | 50Hz (o 60Hz)                           | Relación: \( \text{RPM} = \frac{120 \cdot f}{N_{\text{polos}}} \). Controlada por caudal y carga. |
| **Control de válvula**  | PWM/Relé + electroválvula 12V 1.25"     | Ley de Torricelli: \( Q \propto \sqrt{H} \). Apertura proporcional a demanda.       |

---

## **Componentes Necesarios**
| **Componente**          | **Especificaciones**                     | **Función**                                                                         |
|-------------------------|------------------------------------------|------------------------------------------------------------------------------------|
| **Microcontrolador**    | ESP32 o Raspberry Pi Pico               | Procesa señales de voltaje/sensor y controla válvula.                              |
| **Sensor de voltaje**   | Divisor resistivo (R1=10kΩ, R2=2.2kΩ)   | Reduce 0-14.4V a 0-3.3V para ADC. Fórmula: \( V_{\text{ADC}} = V_{\text{bat}} \cdot \frac{R2}{R1+R2} \). |
| **Electroválvula**      | 12V DC, 1.25", normalmente cerrada      | Corta flujo de agua al alcanzar 14.4V.                                             |
| **Relé/MOSFET**         | 5V-12V, 10A+                            | Activa/desactiva electroválvula. Protegido con diodo flyback (1N4007).             |
| **Sensor de corriente** | PZEM-004T o SCT-013                     | Mide demanda eléctrica (opcional para sistemas sin batería).                       |
| **Fuente de alimentación** | 12V DC, 2A+                            | Alimenta electroválvula y circuito de control.                                     |

---

## **Teoría Clave**
### 1. **Potencia Hidráulica**
\[
P_{\text{hidráulica}} = \rho \cdot g \cdot Q \cdot H \quad (\text{W})
\]
- \( \rho \): Densidad agua (~1000 kg/m³).  
- \( Q \): Caudal (m³/s).  
- \( H \): Altura neta (m).  

### 2. **Eficiencia del Sistema**
\[
P_{\text{eléctrica}} = \eta_{\text{total}} \cdot P_{\text{hidráulica}}  
\]
- \( \eta_{\text{total}} = \eta_{\text{turbina}} \cdot \eta_{\text{generador}} \cdot \eta_{\text{cables}}} \) (~70-80%).  

### 3. **Control de Frecuencia**
- **Frecuencia (\( f \))**: Depende de RPM del generador.  
  \[
  f = \frac{N_{\text{polos}} \cdot \text{RPM}}{120}
  \]
- **Ajuste**: Aumentar caudal (\( Q \)) para mantener \( f = 50 \text{Hz} \) bajo mayor demanda.

### 4. **Protección de Batería**
- **Sobrevoltaje**: Cortar carga a 14.4V para evitar daños.  
- **Histéresis**: Reanudar carga solo si voltaje cae a ~12.5V (evita ciclado rápido).  

---

## **Diagrama de Flujo del Sistema**
```plaintext
[Agua] → [Válvula Controlada] → [Turbina] → [Generador] → [Rectificador] → [Batería]
                                      ↓                      ↑
               [Sensor de Voltaje] ← [Controlador (ESP32/Pico)] 
 ```              

                                      
