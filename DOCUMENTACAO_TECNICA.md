# 📚 Documentação Técnica - Driver Assistant

## 🎯 Visão Geral

O **Driver Assistant** é um aplicativo Android nativo desenvolvido em Kotlin que utiliza o **AccessibilityService** do Android para capturar e analisar ofertas de corrida de aplicativos de transporte (Uber, 99, inDrive) em tempo real, fornecendo métricas calculadas através de um overlay flutuante.

---

## 🏗️ Arquitetura do Sistema

### Diagrama de Componentes

```
┌─────────────────────────────────────────────────────────────┐
│                     Driver Assistant App                     │
├─────────────────────────────────────────────────────────────┤
│                                                               │
│  ┌─────────────┐    ┌──────────────┐    ┌───────────────┐  │
│  │  MainActivity│───▶│SettingsScreen│───▶│HistoryScreen │  │
│  │  (Compose)  │    │  (Compose)   │    │  (Compose)   │  │
│  └─────────────┘    └──────────────┘    └───────────────┘  │
│         │                    │                    │          │
│         └────────────────────┼────────────────────┘          │
│                              │                               │
│                              ▼                               │
│                    ┌──────────────────┐                      │
│                    │   UserGoals      │                      │
│                    │  (DataStore)     │                      │
│                    └──────────────────┘                      │
│                                                               │
│  ┌───────────────────────────────────────────────────────┐  │
│  │         RideAnalysisAccessibilityService              │  │
│  │  ┌─────────────┐         ┌──────────────┐            │  │
│  │  │ Event       │────────▶│ RideDataParser│            │  │
│  │  │ Listener    │         └──────────────┘            │  │
│  │  └─────────────┘                │                     │  │
│  │         │                        │                     │  │
│  │         │                        ▼                     │  │
│  │         │              ┌──────────────────┐            │  │
│  │         │              │  RideAnalyzer    │            │  │
│  │         │              │  (Business Logic)│            │  │
│  │         │              └──────────────────┘            │  │
│  │         │                        │                     │  │
│  │         │                        ▼                     │  │
│  │         │              ┌──────────────────┐            │  │
│  │         └─────────────▶│  OverlayService  │            │  │
│  │                        └──────────────────┘            │  │
│  └───────────────────────────────────────────────────────┘  │
│                              │                               │
│                              ▼                               │
│                    ┌──────────────────┐                      │
│                    │  Room Database   │                      │
│                    │  (RideOffer)     │                      │
│                    └──────────────────┘                      │
│                                                               │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
                    ┌──────────────────┐
                    │  Android System  │
                    │  (Uber/99/inDrive)│
                    └──────────────────┘
```

---

## 🔧 Componentes Principais

### 1. RideAnalysisAccessibilityService

**Responsabilidade**: Capturar eventos de acessibilidade dos apps de transporte e extrair dados das ofertas de corrida.

**Tecnologia**: `AccessibilityService` do Android

**Funcionamento**:
1. Monitora eventos de mudança de janela e conteúdo dos apps suportados
2. Extrai texto da hierarquia de views usando `AccessibilityNodeInfo`
3. Envia o texto para o `RideDataParser`
4. Recebe os dados parseados e envia para o `RideAnalyzer`
5. Salva o resultado no banco de dados
6. Dispara o `OverlayService` para exibir o cartão

**Código-chave**:
```kotlin
override fun onAccessibilityEvent(event: AccessibilityEvent?) {
    val packageName = event.packageName?.toString()
    val appSource = supportedApps[packageName] ?: return
    
    when (event.eventType) {
        TYPE_WINDOW_CONTENT_CHANGED -> {
            val text = extractTextFromNode(rootInActiveWindow)
            analyzeRideText(text, appSource)
        }
    }
}
```

**Permissões necessárias**:
```xml
<uses-permission android:name="android.permission.BIND_ACCESSIBILITY_SERVICE" />
```

---

### 2. RideDataParser

**Responsabilidade**: Extrair informações estruturadas do texto capturado usando expressões regulares.

**Padrões Regex Utilizados**:

| Dado | Regex | Exemplo |
|------|-------|---------|
| Valor | `R\$\s*([0-9]+[,.]?[0-9]*)` | R$ 27,99 |
| Distância | `([0-9]+[,.]?[0-9]*)\s*km` | 18.3 km |
| Duração (min) | `([0-9]+)\s*min` | 42 min |
| Duração (hora) | `([0-9]+)h\s*([0-9]+)?` | 1h30 |
| Avaliação | `([0-9][,.]?[0-9]?)\s*★` | 4.8 ★ |

**Validação**:
```kotlin
fun isValidRideData(data: ParsedRideData): Boolean {
    return data.value != null && data.value > 0 &&
           data.distance != null && data.distance > 0 &&
           data.durationMinutes != null && data.durationMinutes > 0
}
```

---

### 3. RideAnalyzer

**Responsabilidade**: Calcular métricas e classificar a oferta de corrida.

**Fórmulas de Cálculo**:

1. **Valor por Km**:
   ```
   R$/Km = Valor Total ÷ Distância (km)
   ```

2. **Valor por Hora**:
   ```
   R$/Hora = Valor Total ÷ (Duração em minutos ÷ 60)
   ```

3. **Custo Estimado**:
   ```
   Custo = (Distância × R$ 0,80/km) + (Duração × R$ 0,30/min)
   ```

4. **Lucro Estimado**:
   ```
   Lucro = Valor Total - Custo Estimado
   ```

**Algoritmo de Classificação**:

```kotlin
fun classifyRide(valuePerKm: Double, valuePerHour: Double): RideClassification {
    val kmScore = valuePerKm / userGoals.targetValuePerKm
    val hourScore = valuePerHour / userGoals.targetValuePerHour
    
    // Média ponderada (km tem peso 60%, hora tem peso 40%)
    val overallScore = (kmScore * 0.6) + (hourScore * 0.4)
    
    return when {
        overallScore >= 1.0 -> GOOD    // ≥ 100% da meta
        overallScore >= 0.75 -> MEDIUM // 75-99% da meta
        else -> BAD                     // < 75% da meta
    }
}
```

---

### 4. OverlayService

**Responsabilidade**: Exibir o botão flutuante e o cartão de informações sobre outros apps.

**Tecnologia**: `WindowManager` + `TYPE_APPLICATION_OVERLAY`

**Características**:
- Foreground Service (para evitar ser morto pelo sistema)
- Draggable (pode ser arrastado pela tela)
- Auto-dismiss após 10 segundos
- Layout responsivo com cores dinâmicas baseadas na classificação

**Configuração da Janela**:
```kotlin
val params = WindowManager.LayoutParams(
    WRAP_CONTENT,
    WRAP_CONTENT,
    TYPE_APPLICATION_OVERLAY,
    FLAG_NOT_FOCUSABLE,
    PixelFormat.TRANSLUCENT
).apply {
    gravity = Gravity.TOP or Gravity.END
    x = 20
    y = 100
}

windowManager.addView(overlayView, params)
```

**Permissões necessárias**:
```xml
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
```

---

### 5. Room Database

**Responsabilidade**: Persistir histórico de corridas e permitir consultas.

**Entidades**:

```kotlin
@Entity(tableName = "ride_offers")
data class RideOffer(
    @PrimaryKey(autoGenerate = true) val id: Long = 0,
    val appSource: String,
    val timestamp: Long,
    val value: Double,
    val distance: Double,
    val duration: Int,
    val valuePerKm: Double,
    val valuePerHour: Double,
    val estimatedProfit: Double,
    val classification: RideClassification,
    val wasAccepted: Boolean? = null
)
```

**Queries Principais**:
- `getAllRides()`: Retorna todas as corridas ordenadas por data
- `getRidesSince(startTime)`: Corridas a partir de uma data
- `getRidesByApp(appSource)`: Filtra por app (Uber, 99, inDrive)
- `getRidesByClassification(classification)`: Filtra por classificação
- `getAverageValuePerKm(startTime)`: Média de R$/Km em um período
- `getTotalProfit(startTime)`: Lucro total em um período

---

## 📱 Interface do Usuário

### Tecnologia: Jetpack Compose

**Vantagens**:
- UI declarativa e reativa
- Menos código boilerplate
- Melhor performance
- Suporte nativo a Material Design 3

### Telas Principais

#### 1. MainActivity (Tela Inicial)

**Componentes**:
- Status do serviço (ativo/inativo)
- Botões de ativação de permissões
- Acesso a configurações e histórico
- Card informativo sobre como usar

#### 2. SettingsScreen (Configurações)

**Campos**:
- Meta de R$/Km (Slider ou TextField)
- Meta de R$/Hora (Slider ou TextField)
- Toggle para notificação por voz
- Toggle para câmera de segurança
- Seletor de posição do overlay

#### 3. HistoryScreen (Histórico)

**Funcionalidades**:
- Lista de corridas com LazyColumn
- Filtros por data, app e classificação
- Cards coloridos por classificação
- Estatísticas resumidas (média, total)

---

## 🔒 Segurança e Privacidade

### Princípios

1. **Armazenamento Local**: Todos os dados ficam no dispositivo
2. **Sem Conexão Externa**: Nenhum dado é enviado para servidores
3. **Permissões Mínimas**: Apenas as necessárias para funcionamento
4. **Transparência**: Código aberto e auditável

### Permissões Solicitadas

| Permissão | Justificativa | Obrigatória |
|-----------|---------------|-------------|
| `BIND_ACCESSIBILITY_SERVICE` | Ler ofertas de corrida | Sim |
| `SYSTEM_ALERT_WINDOW` | Exibir overlay | Sim |
| `FOREGROUND_SERVICE` | Manter serviço ativo | Sim |
| `CAMERA` | Câmera de segurança | Não |
| `WRITE_EXTERNAL_STORAGE` | Salvar vídeos | Não |

---

## 🧪 Testes

### Testes Unitários

**RideDataParser**:
```kotlin
@Test
fun testExtractValue() {
    val text = "Valor da corrida: R$ 27,99"
    val data = parser.parseRideOffer(text)
    assertEquals(27.99, data.value, 0.01)
}
```

**RideAnalyzer**:
```kotlin
@Test
fun testClassifyGoodRide() {
    val goals = UserGoals(targetValuePerKm = 2.0, targetValuePerHour = 40.0)
    val analyzer = RideAnalyzer(goals)
    val ride = analyzer.analyzeRide("uber", 50.0, 20.0, 60)
    assertEquals(RideClassification.GOOD, ride.classification)
}
```

### Testes de Integração

**AccessibilityService**:
- Simular eventos de acessibilidade
- Verificar extração de texto
- Validar persistência no banco de dados

**OverlayService**:
- Verificar exibição do overlay
- Testar interação de arrastar
- Validar auto-dismiss

---

## 📊 Performance

### Otimizações Implementadas

1. **Coroutines**: Operações de I/O em background threads
2. **Room**: Cache de queries com Flow
3. **Lazy Loading**: LazyColumn para listas grandes
4. **Regex Compilado**: Padrões regex compilados uma vez
5. **View Recycling**: Reutilização de views no overlay

### Benchmarks Esperados

- **Tempo de análise**: < 100ms
- **Consumo de memória**: < 50MB
- **Impacto na bateria**: < 5% por hora de uso
- **Tamanho do APK**: ≈ 5-8MB

---

## 🚀 Build e Deploy

### Compilar o Projeto

```bash
# Debug build
./gradlew assembleDebug

# Release build (assinado)
./gradlew assembleRelease
```

### Gerar APK

```bash
./gradlew build
# APK gerado em: app/build/outputs/apk/debug/app-debug.apk
```

### Instalar no Dispositivo

```bash
adb install app/build/outputs/apk/debug/app-debug.apk
```

---

## 🔄 Fluxo de Dados Completo

```
1. Usuário recebe oferta de corrida no Uber/99/inDrive
   ↓
2. AccessibilityService detecta mudança de conteúdo
   ↓
3. Extrai texto da hierarquia de views
   ↓
4. RideDataParser processa o texto com regex
   ↓
5. RideAnalyzer calcula métricas e classifica
   ↓
6. RideOffer é salvo no Room Database
   ↓
7. OverlayService é iniciado com os dados
   ↓
8. Cartão flutuante é exibido sobre o app
   ↓
9. Usuário visualiza a análise e decide
   ↓
10. Cartão desaparece após 10s ou ao fechar
```

---

## 📝 Próximas Funcionalidades

### Roadmap

- [ ] **Notificação por Voz** (TTS)
- [ ] **Câmera de Segurança** (CameraX)
- [ ] **Backup em Nuvem** (Firebase)
- [ ] **Estatísticas Avançadas** (Gráficos)
- [ ] **Suporte a mais apps** (Cabify, Lyft)
- [ ] **Widget de Dashboard**
- [ ] **Modo Escuro Completo**
- [ ] **Exportar relatórios** (PDF, Excel)

---

## 🤝 Contribuindo

### Como Contribuir

1. Fork o projeto
2. Crie uma branch para sua feature (`git checkout -b feature/MinhaFeature`)
3. Commit suas mudanças (`git commit -m 'Adiciona MinhaFeature'`)
4. Push para a branch (`git push origin feature/MinhaFeature`)
5. Abra um Pull Request

### Padrões de Código

- **Kotlin Style Guide**: Seguir [Kotlin Coding Conventions](https://kotlinlang.org/docs/coding-conventions.html)
- **Commits**: Mensagens descritivas em português
- **Testes**: Adicionar testes para novas funcionalidades
- **Documentação**: Atualizar README e docs quando necessário

---

**Desenvolvido com ❤️ para a comunidade de motoristas de aplicativos**
