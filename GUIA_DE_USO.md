# 📖 Guia Completo de Uso - Driver Assistant

## 🎯 Objetivo

Este guia irá ajudá-lo a configurar e usar o **Driver Assistant** para maximizar seus ganhos como motorista de aplicativos.

---

## 📱 Instalação

### Opção 1: Instalar APK Pronto

1. Faça o download do arquivo `DriverAssistant.apk`
2. No seu celular Android, vá em **Configurações** → **Segurança**
3. Ative **"Fontes desconhecidas"** ou **"Instalar apps desconhecidos"**
4. Abra o arquivo APK baixado
5. Toque em **"Instalar"**

### Opção 2: Compilar do Código-Fonte

1. Instale o [Android Studio](https://developer.android.com/studio)
2. Abra o projeto `DriverAssistant`
3. Conecte seu celular via USB (com depuração USB ativada)
4. Clique em **"Run"** (▶️) no Android Studio

---

## ⚙️ Configuração Inicial

### Passo 1: Abrir o Aplicativo

1. Encontre o ícone do **Driver Assistant** na sua lista de apps
2. Toque para abrir

### Passo 2: Ativar o Serviço de Acessibilidade

**Por que é necessário?**  
O serviço de acessibilidade permite que o app leia as informações das corridas na tela do Uber, 99 e inDrive.

**Como ativar:**

1. Na tela inicial do Driver Assistant, toque em **"Ativar Serviço de Acessibilidade"**
2. Você será direcionado para as Configurações do Android
3. Procure por **"Driver Assistant"** na lista de serviços
4. Toque no Driver Assistant
5. Ative o botão **"Usar serviço"**
6. Leia o aviso e toque em **"OK"**
7. Volte para o Driver Assistant

### Passo 3: Permitir Sobreposição

**Por que é necessário?**  
A permissão de sobreposição permite que o app exiba o botão flutuante sobre o Uber, 99 e inDrive.

**Como permitir:**

1. Na tela inicial do Driver Assistant, toque em **"Permitir Sobreposição"**
2. Você será direcionado para as Configurações do Android
3. Encontre **"Driver Assistant"** na lista
4. Ative **"Permitir sobreposição"** ou **"Exibir sobre outros apps"**
5. Volte para o Driver Assistant

### Passo 4: Configurar Suas Metas (Opcional mas Recomendado)

1. Na tela inicial, toque em **"Configurar"**
2. Defina sua **Meta de R$/Km** (exemplo: R$ 2,00)
3. Defina sua **Meta de R$/Hora** (exemplo: R$ 40,00)
4. Ative **"Notificação por Voz"** se quiser ouvir a análise
5. Toque em **"Salvar"**

**Dica:** Analise suas corridas anteriores para definir metas realistas!

---

## 🚗 Usando Durante o Trabalho

### Como Funciona

1. **Abra o app de transporte** (Uber, 99 ou inDrive)
2. **Aguarde uma oferta de corrida**
3. Quando a corrida aparecer, o Driver Assistant irá:
   - 📊 Capturar automaticamente os dados (valor, distância, tempo)
   - 🧮 Calcular R$/Km, R$/Hora e lucro estimado
   - 🚦 Classificar a corrida (Verde, Amarelo ou Vermelho)
   - 📱 Exibir um cartão flutuante com a análise

### Interpretando o Cartão Flutuante

O cartão mostra as seguintes informações:

```
┌─────────────────────────────┐
│ [Barra colorida]            │
│                             │
│ Oferta Boa / Média / Ruim   │
│                             │
│ R$/Km        R$/Hora        │
│ R$ 2.35      R$ 52.80       │
│                             │
│ Lucro                       │
│ R$ 38.90                    │
│ ─────────────────────────   │
│ 42 min · 18.3 km         ✕  │
└─────────────────────────────┘
```

#### Cores da Classificação

- **🟢 Verde (Oferta Boa)**: Aceite! A corrida está acima das suas metas
- **🟡 Amarelo (Pense Duas Vezes)**: Razoável. Avalie se compensa
- **🔴 Vermelho (Oferta Ruim)**: Recuse. Abaixo das suas metas

#### Métricas Explicadas

- **R$/Km**: Quanto você ganha por quilômetro rodado
  - Exemplo: R$ 2.35/km significa que a cada km você ganha R$ 2,35
  
- **R$/Hora**: Quanto você ganha por hora trabalhada
  - Exemplo: R$ 52.80/hora significa que você fatura R$ 52,80 por hora
  
- **Lucro**: Ganho estimado após descontar custos
  - Considera combustível (≈ R$ 0,80/km) e tempo (≈ R$ 0,30/min)

### Interagindo com o Cartão

- **Arrastar**: Toque e segure o cartão para movê-lo pela tela
- **Fechar**: Toque no **✕** para fechar o cartão
- **Auto-fechar**: O cartão desaparece automaticamente após 10 segundos

---

## 📊 Consultando o Histórico

1. Na tela inicial do Driver Assistant, toque em **"Histórico"**
2. Você verá todas as corridas analisadas
3. Filtre por:
   - Data
   - App (Uber, 99, inDrive)
   - Classificação (Verde, Amarelo, Vermelho)
4. Toque em uma corrida para ver detalhes completos

**Dica:** Use o histórico para identificar os melhores horários e regiões!

---

## 🔧 Configurações Avançadas

### Notificação por Voz

Quando ativada, o app falará a análise da corrida:

- **Verde**: "Oferta boa! R$ 2,35 por quilômetro. R$ 52,80 por hora. Lucro estimado: R$ 38,90"
- **Amarelo**: "Pense duas vezes. R$ 1,92 por quilômetro. R$ 41,50 por hora"
- **Vermelho**: "Oferta ruim. Apenas R$ 1,18 por quilômetro. Não recomendado"

### Câmera de Segurança

Grava discretamente em segundo plano para sua proteção. Os vídeos são salvos localmente no seu celular.

**Como ativar:**
1. Vá em **Configurar**
2. Ative **"Câmera de Segurança"**
3. Permita o acesso à câmera quando solicitado

---

## ❓ Perguntas Frequentes

### O app funciona offline?

Sim! Todas as análises são feitas localmente no seu celular. Você não precisa de internet.

### O app consome muita bateria?

Não. O Driver Assistant foi otimizado para ter impacto mínimo na bateria.

### Meus dados são enviados para algum servidor?

Não! Todos os dados ficam armazenados apenas no seu celular. Sua privacidade está garantida.

### Funciona com outros apps além de Uber, 99 e inDrive?

Atualmente, apenas esses três apps são suportados. Novos apps podem ser adicionados em futuras atualizações.

### Posso personalizar as cores do cartão?

Atualmente não, mas essa funcionalidade está planejada para futuras versões.

### O app substitui o Uber/99/inDrive?

Não! O Driver Assistant trabalha **junto** com esses apps, apenas analisando as ofertas de corrida.

---

## 🆘 Solução de Problemas

### O cartão não aparece quando recebo uma corrida

**Soluções:**

1. Verifique se o **Serviço de Acessibilidade** está ativado
2. Verifique se a **Permissão de Sobreposição** está concedida
3. Reinicie o Driver Assistant
4. Reinicie o celular

### O app não está capturando os dados corretamente

**Soluções:**

1. Certifique-se de que está usando a versão mais recente do Uber/99/inDrive
2. Verifique se o serviço de acessibilidade está ativo
3. Tente desativar e reativar o serviço de acessibilidade

### A classificação parece errada

**Solução:**

Ajuste suas metas! Vá em **Configurar** e redefina os valores de R$/Km e R$/Hora de acordo com sua realidade.

---

## 💡 Dicas para Maximizar Ganhos

1. **Defina metas realistas**: Analise suas corridas por uma semana e ajuste as metas
2. **Use o histórico**: Identifique padrões de horários e regiões mais lucrativos
3. **Seja seletivo**: Com o Driver Assistant, você pode recusar corridas ruins com confiança
4. **Combine apps**: Use Uber, 99 e inDrive simultaneamente e escolha a melhor oferta
5. **Revise semanalmente**: Ajuste suas estratégias baseado nos dados do histórico

---

## 📞 Suporte

Precisa de ajuda? Entre em contato:

- **Email**: suporte@driverassistant.com
- **WhatsApp**: (11) 9xxxx-xxxx
- **GitHub**: Abra uma issue no repositório

---

**Boas corridas e ótimos ganhos! 🚗💰**
