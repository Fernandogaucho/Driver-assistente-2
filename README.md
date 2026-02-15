# Driver Assistant - Aplicativo Android para Motoristas de Apps

## 📱 Sobre o Projeto

**Driver Assistant** é um aplicativo Android nativo desenvolvido para auxiliar motoristas de aplicativos de transporte (Uber, 99, inDrive) a maximizar seus ganhos através de análise inteligente de ofertas de corrida em tempo real.

Inspirado no aplicativo GigU, o Driver Assistant oferece funcionalidades avançadas de análise de corridas, incluindo:

- ✅ **Análise em Tempo Real**: Captura e analisa ofertas de corrida automaticamente
- 📊 **Cálculo de Métricas**: R$/Km, R$/Hora e lucro estimado
- 🚦 **Sistema de Semáforo**: Classificação visual (Verde/Amarelo/Vermelho)
- 🎯 **Metas Personalizadas**: Configure seus objetivos de ganhos
- 📱 **Botão Flutuante (Overlay)**: Informações sem sair do app de transporte
- 📜 **Histórico de Corridas**: Acompanhe todas as ofertas analisadas
- 🔊 **Notificação por Voz**: Análise hands-free (opcional)
- 📹 **Câmera de Segurança**: Gravação discreta para proteção (opcional)

## 🏗️ Arquitetura Técnica

### Tecnologias Utilizadas

- **Linguagem**: Kotlin 1.9.20
- **UI Framework**: Jetpack Compose + Material Design 3
- **Banco de Dados**: Room Database
- **Arquitetura**: MVVM (Model-View-ViewModel)
- **Coroutines**: Para operações assíncronas
- **Gradle**: 8.2.0

### Componentes Principais

1. **AccessibilityService**: Captura informações da tela dos apps de transporte
2. **OverlayService**: Exibe o botão flutuante e cartão de informações
3. **RideAnalyzer**: Motor de análise e classificação de corridas
4. **RideDataParser**: Extração de dados usando regex
5. **Room Database**: Persistência de histórico e configurações

## 📋 Requisitos do Sistema

- **Android**: 8.0 (API 26) ou superior
- **Compatibilidade**: Até Android 14 (API 34)
- **Permissões Necessárias**:
  - Serviço de Acessibilidade
  - Sobreposição sobre outros apps
  - Câmera (opcional)
  - Armazenamento (opcional)

## 🚀 Como Usar

### 1. Instalação

1. Faça o download do APK ou compile o projeto no Android Studio
2. Instale o aplicativo no seu dispositivo Android
3. Abra o **Driver Assistant**

### 2. Configuração Inicial

1. **Ativar Serviço de Acessibilidade**:
   - Toque em "Ativar Serviço de Acessibilidade"
   - Nas configurações do Android, encontre "Driver Assistant"
   - Ative o serviço

2. **Permitir Sobreposição**:
   - Toque em "Permitir Sobreposição"
   - Nas configurações do Android, permita que o app apareça sobre outros apps

3. **Configurar Metas** (opcional):
   - Defina sua meta de R$/Km
   - Defina sua meta de R$/Hora
   - Ative notificação por voz se desejar

### 3. Uso Durante o Trabalho

1. Abra o app de transporte (Uber, 99 ou inDrive)
2. Quando uma corrida aparecer, o Driver Assistant irá:
   - Capturar automaticamente os dados
   - Calcular as métricas
   - Exibir um cartão flutuante com a análise
   - Classificar como Verde (boa), Amarelo (média) ou Vermelho (ruim)
3. Tome sua decisão baseado na análise!

## 📊 Como Funciona a Análise

### Métricas Calculadas

- **R$/Km**: Valor total da corrida ÷ Distância em km
- **R$/Hora**: Valor total da corrida ÷ Tempo estimado em horas
- **Lucro Estimado**: Valor da corrida - Custos estimados (combustível + tempo)

### Sistema de Classificação

A classificação é baseada em uma pontuação que compara as métricas da corrida com suas metas:

- **🟢 Verde (Oferta Boa)**: Pontuação ≥ 100% da meta
- **🟡 Amarelo (Pense Duas Vezes)**: Pontuação entre 75% e 99% da meta
- **🔴 Vermelho (Oferta Ruim)**: Pontuação < 75% da meta

A pontuação é calculada como: `(R$/Km × 0.6) + (R$/Hora × 0.4)`

## 🛠️ Desenvolvimento

### Estrutura do Projeto

```
DriverAssistant/
├── app/
│   ├── src/
│   │   └── main/
│   │       ├── java/com/driverassistant/
│   │       │   ├── analyzer/          # Motor de análise
│   │       │   │   ├── RideAnalyzer.kt
│   │       │   │   └── RideDataParser.kt
│   │       │   ├── data/              # Modelos e banco de dados
│   │       │   │   ├── RideOffer.kt
│   │       │   │   ├── RideOfferDao.kt
│   │       │   │   └── AppDatabase.kt
│   │       │   ├── service/           # Serviços Android
│   │       │   │   ├── RideAnalysisAccessibilityService.kt
│   │       │   │   ├── OverlayService.kt
│   │       │   │   └── CameraService.kt
│   │       │   └── ui/                # Interface do usuário
│   │       │       ├── MainActivity.kt
│   │       │       └── theme/
│   │       ├── res/
│   │       │   ├── layout/            # Layouts XML
│   │       │   ├── values/            # Strings, cores, temas
│   │       │   └── xml/               # Configurações
│   │       └── AndroidManifest.xml
│   └── build.gradle
├── build.gradle
└── settings.gradle
```

### Compilar o Projeto

```bash
# Clonar o repositório
cd DriverAssistant

# Compilar com Gradle
./gradlew build

# Instalar no dispositivo conectado
./gradlew installDebug
```

## 🔒 Privacidade e Segurança

- ✅ Todos os dados são armazenados **localmente** no dispositivo
- ✅ Nenhuma informação é enviada para servidores externos
- ✅ O serviço de acessibilidade é usado **apenas** para ler ofertas de corrida
- ✅ Permissões são solicitadas com explicações claras
- ✅ Código aberto e auditável

## 📝 Licença

Este projeto é de código aberto e está disponível para uso pessoal e educacional.

## 🤝 Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para:

- Reportar bugs
- Sugerir novas funcionalidades
- Melhorar a documentação
- Enviar pull requests

## 📧 Suporte

Para dúvidas, sugestões ou problemas, abra uma issue no repositório.

## 🙏 Agradecimentos

Este projeto foi inspirado no aplicativo **GigU**, que revolucionou a forma como motoristas de aplicativos analisam suas corridas.

---

**Desenvolvido com ❤️ para motoristas de aplicativos**
