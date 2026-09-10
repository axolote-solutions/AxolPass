# Documento de Visión (Etapa 0 – Conceptual)

## 1. Problema
Los fraccionamientos y condominios enfrentan tres problemas estructurales que afectan su operación diaria y la experiencia de residentes y visitantes:

- **Fricción y vulnerabilidad en el control de acceso:**  
  El registro manual en casetas genera cuellos de botella, exposición de datos personales y fallas operativas que comprometen la seguridad física.

- **Morosidad persistente:**  
  Las administraciones carecen de herramientas efectivas para incentivar el pago puntual de cuotas, lo que afecta la salud financiera del fraccionamiento.

- **Descontrol en amenidades y estacionamientos:**  
  La falta de reglas claras y herramientas de gestión provoca conflictos vecinales y uso ineficiente de los recursos comunes.

---

## 2. Contexto
El proyecto AxolPass se desarrolla dentro del ecosistema de administración de fraccionamientos y condominios residenciales, donde convergen:

- Operación diaria de accesos vehiculares y peatonales.  
- Gestión de residentes, invitados y proveedores.  
- Administración financiera y operativa del fraccionamiento.  
- Interacción entre residentes, guardias y administradores.

El sistema se concibe como una solución **SaaS B2B multi‑tenant**, operada por Axolote Solutions y consumida por múltiples fraccionamientos independientes.

---

## 3. Actores

### Internos (Axolote Solutions)
- **Sponsors / Inversionistas:**  
  Interesados en la viabilidad comercial y escalabilidad del producto.

- **SystemAdmin (Operaciones):**  
  Encargados del alta de clientes, soporte y gobernanza del sistema.

### Externos (Clientes y Usuarios)
- **Mesa Directiva / Comité de Vigilancia:**  
  Toman decisiones contractuales y representan al fraccionamiento.

- **CommunityAdmin (Administrador Local):**  
  Opera el sistema día a día, gestiona residentes y aplica reglas internas.

- **Residentes (Principales y Secundarios):**  
  Generan invitaciones, reciben notificaciones y utilizan el sistema como parte de su vida cotidiana.

- **Guardias de Seguridad (SecurityGuard):**  
  Operan la caseta y requieren herramientas confiables para registrar accesos.

---

## 4. Escenarios Clave
Los escenarios representan situaciones esenciales que el sistema debe resolver, sin definir aún cómo se implementarán:

- **Aprovisionamiento Comercial B2B:**  
  Alta de fraccionamientos y configuración inicial del entorno operativo.

- **Gestión de Morosidad y Suspensiones:**  
  Aplicación de reglas que afectan la operatividad del fraccionamiento cuando existen deudas.

- **Control de Accesos Seguros:**  
  Generación y validación de pases de acceso para residentes y visitantes.

- **Excepciones y Llegadas Sorpresa:**  
  Manejo de accesos manuales o emergentes que requieren auditoría.

- **Comunicación y Alertas:**  
  Notificaciones a residentes sobre eventos relevantes del sistema.

---

## 5. Restricciones Conceptuales
Estas restricciones condicionan el diseño futuro, pero no definen tecnología:

- **Privacidad Multi‑Tenant:**  
  Los datos de cada fraccionamiento deben estar aislados y protegidos.

- **Seguridad Operativa:**  
  El sistema debe respetar protocolos de seguridad física y responsabilidad civil.

- **Gobernanza de Identidad:**  
  La identidad de usuarios debe ser consistente y administrada de forma centralizada.

- **Reglas de Operación del Cliente:**  
  Cada fraccionamiento puede tener políticas internas que deben respetarse.

- **Continuidad Física (Resiliencia Operativa):**  
  El flujo vehicular y la apertura de barreras no deben detenerse ante interrupciones de conectividad local; la realidad física manda sobre la nube.

---

## 6. Volúmenes Estimados (Conceptuales)
Los volúmenes aquí definidos son aproximaciones, no NFRs técnicos:

- **Número de casas:**  
  Fraccionamientos típicos entre 50 y 500 unidades.

- **Accesos diarios:**  
  Entre 200 y 2,000 eventos por día según tamaño y actividad.

- **Picos de tráfico:**  
  Horarios de entrada/salida laboral y fines de semana.

- **Crecimiento esperado:**  
  Expansión gradual conforme se integren nuevos fraccionamientos.

---

## 7. Filosofías Rectoras (Intención, no arquitectura)
Estas filosofías guían el pensamiento del proyecto, pero no definen implementación:

- **API‑First:**  
  El sistema se diseña pensando primero en sus interfaces públicas.

- **Diseño Guiado por el Dominio:**  
  El entendimiento del negocio y el lenguaje común guían la estructura conceptual.

- **Aislamiento del Dominio:**  
  Las reglas de negocio deben estar protegidas y ser independientes de mecanismos externos.

- **Naturaleza Reactiva:**  
  El sistema se concibe como una red que responde a los hechos del fraccionamiento.

- **Security‑First:**  
  La seguridad es un principio rector desde el inicio.

