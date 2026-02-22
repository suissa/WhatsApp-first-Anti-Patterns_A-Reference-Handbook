# WhatsApp-first Anti-Patterns: A Reference Handbook

Catalogo de Anti-Patterns de Whatsapp-first conhecidos publicamente até agora.

---

**Legenda de Severidade:**
- 🔴 **CRÍTICO**: Bloqueia deploy. Risco de fraude, perda financeira ou violação legal.
- 🟠 **ALTO**: Degrada experiência severamente. Requer correção prioritária.
- 🟡 **MÉDIO**: Impacto moderado. Deve ser endereçado em sprints futuras.
- 🟢 **BAIXO**: Melhoria de UX. Não bloqueante.

---

## 1. SEGURANÇA & CONFIANÇA (8 anti-patterns)

### 🔴 AP-01: Ignorar Anti-Fraude
Número dá identidade implícita, não autenticação perfeita. Ações de risco exigem fricção progressiva.

**Exemplo:** Alguém com o WhatsApp do cliente (celular roubado, chip clonado) pede troca de endereço. Sistema aceita só porque "é o número do cliente".

**Efeito:** Entrega desviada + fraude.

---

### 🔴 AP-02: Dados Sensíveis em Plain Text
CPF, cartão, receita médica — tudo armazenado sem criptografia/redaction.

**Sinal:** Log completo expõe PII sem proteção.

---

### 🔴 AP-03: Confirmação Fraca (sim/não) sem Vincular ao Objeto
"Sim" sem amarrar ao pedido/ação vira ambiguidade e fraude.

**Exemplo:** Usuário responde "sim" e você aplica no item/pedido errado.

---

### 🔴 AP-04: Deep Link sem Token de Segurança
Links mágicos de recuperação de senha ou acesso sem expiry/token único.

**Sinal:** Link de "resetar senha" funciona mesmo depois de usado ou vencido.

---

### 🟠 AP-05: Alterações Críticas sem "Freio de Risco"
Troca de endereço, cancelamento, reembolso — sem aumento de verificação.

**Sinal:** Qualquer ação crítica é executada do mesmo jeito que "ver status".

---

### 🟠 AP-06: Webhook sem Validação de Assinatura
Aceita eventos da API WhatsApp sem verificar o `X-Hub-Signature`.

**Sinal:** Qualquer POST malicioso consegue disparar ações no backend.

---

### 🟠 AP-07: Segurança "Tudo ou Nada"
Ou não pede nada nunca, ou pede login/senha pra tudo.

**Sinal:** Ausência de fricção progressiva por risco.

---

### 🟡 AP-08: Privacidade Laxa em Dados Multimodais
Armazena áudios/imagens sem anonimização ou TTL, violando LGPD.

**Sinal:** Logs retêm voz do usuário indefinidamente sem consentimento.

---

## 2. INTEGRIDADE & CONSISTÊNCIA (9 anti-patterns)

### 🔴 AP-09: Sem Idempotência
Chat duplica. Webhook duplica. Usuário reenviou. Se repetir comando gera efeito duplicado, você cobra duas vezes.

**Exemplo:** Cliente pede "PAGAR pedido 1837 no Pix". WhatsApp reenvia/webhook duplica. Sistema gera duas cobranças.

**Efeito:** Caos financeiro e perda de confiança.

---

### 🔴 AP-10: Sem Trilha de Evidência
Não registra o que foi pedido, confirmado e executado. Não audita nem recupera.

**Exemplo:** Cliente confirma "pode substituir por genérico". Depois reclama: "eu não autorizei". Você não tem registro do "SIM".

**Efeito:** Perde disputa e vira prejuízo.

---

### 🔴 AP-11: Webhooks sem Retry/Idempotency
WhatsApp manda evento duplicado ou atrasado e você processa errado.

**Sinal:** Mesma mensagem cria 2 pedidos.

---

### 🟠 AP-12: Concorrência Invisível (dois fluxos ao mesmo tempo)
Usuário dispara duas intenções e o bot mistura estados.

**Sinal:** Conversa vira "interleaving" e ninguém sabe onde está.

---

### 🟠 AP-13: Sem Lock em Recursos Compartilhados
Múltiplos fluxos editam o mesmo pedido sem deduplicação.

**Sinal:** Dois agentes alteram endereço simultaneamente → entrega errada.

---

### 🟠 AP-14: Sem "Cooldown" de Comandos Críticos
Usuário spamma "cancelar" ou "pagar" e você processa em paralelo.

**Sinal:** Múltiplos eventos concorrentes para o mesmo pedido.

---

### 🟠 AP-15: Carrinho Fantasma
Guarda itens sem vincular claramente a sessão/usuário. Carrinho "vaza" entre conversas.

**Sinal:** Usuário volta dias depois e vê itens de outra pessoa.

---

### 🟡 AP-16: Sem Distinção entre "Cancelar Fluxo" e "Cancelar Pedido"
"Cancelar" vira comando ambíguo.

**Sinal:** Usuário perde pedido real porque quis só sair da pergunta.

---

### 🟡 AP-17: Troca de Número sem Migração de Histórico
Cliente trocou de chip e vira usuário novo — perde pedidos, perde contexto.

**Sinal:** "Não encontrei seu pedido" (está vinculado ao número antigo).

---

## 3. UX CONVERSACIONAL (12 anti-patterns)

### 🟠 AP-18: Menu Infinito
Recriou dashboard ruim em texto. Chat não é lugar de listar tudo.

**Exemplo:** Cliente manda "quero dipirona". Bot responde com 25 opções misturadas.

**Efeito:** Cliente não escolhe nada, repete a pergunta ou pede humano.

---

### 🟠 AP-19: Dependência de Painel pra Finalizar
Jornada começa no WhatsApp mas ação crítica só fecha no web.

**Exemplo:** "Clique no link e finalize no site".

**Efeito:** WhatsApp vira só triagem. Conversão cai.

---

### 🟠 AP-20: Sem Confirmação Contextual em Ações de Impacto
Cancelar, pagar, trocar endereço… sem confirmação explícita.

**Exemplo:** Bot cancela imediatamente sem "Confirmar cancelamento do pedido 1837?"

**Efeito:** Cancelamento acidental, briga, retrabalho.

---

### 🟠 AP-21: Perder Contexto
Fazer usuário repetir o que acabou de dizer.

**Exemplo:** Cliente: "quero dorflex e entrega hoje". Bot pergunta CEP, depois: "Qual produto você deseja?"

**Efeito:** Usuário passa a não confiar e pede humano.

---

### 🟠 AP-22: Perguntas Abertas Demais
"Como posso ajudar?" sem estrutura vira caos.

**Exemplo:** Cliente manda: "tô com dor, febre, e preciso pra criança". Bot tenta "conversar" sem estruturar triagem.

**Efeito:** Bagunça e risco em saúde.

---

### 🟠 AP-23: "Gambi UI" — Mandar Link Toda Hora
Todo fluxo termina com "clique no link". Isso é WhatsApp-to-Web, não WhatsApp-first.

**Sinal:** Catálogo/carrinho/pagamento sempre fora do WhatsApp.

---

### 🟡 AP-24: UX Inconsistente de Opções
Uma hora é "1/2/3", outra é "sim/não", outra é "digite CONFIRMAR".

**Sinal:** Usuário erra não por falta de vontade, mas por falta de padrão.

---

### 🟡 AP-25: Mensagens Longas (Textão) em Etapa de Decisão
Pede decisão depois de parágrafo gigante; a pessoa não lê.

**Sinal:** Resposta do bot ocupa a tela inteira antes da escolha.

---

### 🟡 AP-26: Bot que Improvisa Demais
Mesma intenção gera respostas diferentes toda hora.

**Exemplo:** "tem Ozempic?" → uma hora "Tem sim", outra "Não vendemos".

**Efeito:** Sistema parece instável.

---

### 🟡 AP-27: Sem Caminho de Voltar / Correção Barata
Usuário erra e precisa recomeçar do zero.

**Exemplo:** "Entrada inválida. Reinicie o atendimento enviando 'menu'".

**Efeito:** A pessoa desiste.

---

### 🟡 AP-28: Menu Recursivo
Toda resposta inesperada leva de volta ao menu principal.

**Sinal:** Usuário tenta falar algo e sempre cai no mesmo menu.

---

### 🟢 AP-29: Humanização Forçada (Uncanny Valley)
Bot finge ser humano demais ("Vou verificar aqui com meu colega") e é descoberto.

**Sinal:** Usuário pergunta "você é robô?" e o bot nega.

---

## 4. ARQUITETURA & RESILIÊNCIA (10 anti-patterns)

### 🔴 AP-30: Intenção Fantasma — LLM sem Guardrail
Confiar 100% no NLU/LLM sem validação. Modelo erra e você executa ação crítica.

**Exemplo:** Cancelamento acionado só porque o bot "entendeu" errado.

**Efeito:** Riscos catastróficos.

---

### 🟠 AP-31: Observabilidade Zero
Não existe "status do que aconteceu". Se deu erro, ninguém sabe o que ocorreu.

**Sinal:** "Ocorreu um erro" sem código, sem contexto, sem alternativa.

---

### 🟠 AP-32: Recovery Inexistente
Falhou pagamento, pedido, envio — e usuário fica preso.

**Sinal:** Fluxo morre e manda recomeçar.

---

### 🟠 AP-33: Dependência Crítica sem Circuit Breaker
API de pagamento cai e você trava todas as conversas.

**Sinal:** Timeout de 30s em todo atendimento porque um serviço está lento.

---

### 🟠 AP-34: Integração Externa sem Retry
Chamadas para APIs externas falham uma vez e o fluxo morre.

**Sinal:** "Erro na integração" sem tentativa automática.

---

### 🟠 AP-35: Sessão Frágil
Expira rápido e perde tudo. Usuário volta e bot recomeça do zero.

**Sinal:** "Seu atendimento expirou, digite MENU".

---

### 🟠 AP-36: Timeout Silencioso
Bot some no meio do fluxo e só avisa depois que já morreu.

**Sinal:** "Sua sessão expirou" sem countdown, sem retry.

---

### 🟡 AP-37: Feature Flag Caótico
Releases sem controle de rollback. Ativa feature nova pra 100% sem teste gradual.

**Sinal:** Bug atinge toda base de uma vez.

---

### 🟡 AP-38: Migração de Versão sem Compatibilidade Reversa
Atualiza fluxo e conversas antigas quebram.

**Sinal:** Usuário no meio de atendimento fica perdido após deploy.

---

### 🟡 AP-39: Testes Só em Produção
Valida flow novo direto com cliente real.

**Sinal:** Bug descobre em horário de pico.

---

## 5. MULTIMODALIDADE & INPUTS (6 anti-patterns)

### 🟠 AP-40: Não Tratar Áudio/Imagem como Input de Primeira Classe
WhatsApp é multimodal. Ignorar áudio e anexos mata aderência.

**Sinal:** "Não entendi, digite em texto" (sempre).

---

### 🟡 AP-41: Não Suportar Mensagens Fora de Ordem
Resposta pode vir atrasada, citando mensagem antiga, ou em áudio.

**Sinal:** Fluxo assume ordem perfeita e quebra com qualquer atraso.

---

### 🟡 AP-42: Ignorar Anexos Úteis
Trata só texto/áudio, mas ignora localização ou PDFs.

**Sinal:** Usuário manda mapa de entrega e bot responde "digite o endereço".

---

### 🟡 AP-43: Campos Longos sem Estratégia
Exige input perfeito como se fosse formulário.

**Sinal:** Usuário manda endereço quebrado em 4 mensagens e fluxo se perde.

---

### 🟡 AP-44: Pergunta Dupla/Tripla na Mesma Mensagem
Joga 3 decisões em uma mensagem.

**Sinal:** "Qual produto? Qual endereço? E forma de pagamento?" no mesmo turno.

---

### 🟢 AP-45: Bias Cultural em NLU/LLM
Modelo assume inglês/americano, falhando em gírias brasileiras.

**Sinal:** "Quero pão de queijo" é interpretado como erro.

---

## 6. GOVERNANÇA & OPS (8 anti-patterns)

### 🔴 AP-46: Rate Limit do WhatsApp Ignorado
Manda 10 mensagens seguidas e leva ban/throttle.

**Sinal:** Conta bloqueada por "spam" mesmo sendo fluxo legítimo.

---

### 🟠 AP-47: Consentimento Ignorado em Mensagens Proativas
Manda promo/status sem opt-in e vira spam.

**Sinal:** Campanha "ativa" sem controle de frequência.

---

### 🟠 AP-48: Sem Governança de Catálogo/Conteúdo
IA inventa preço, disponibilidade, recomendação.

**Sinal:** Resposta não é ancorada em "fonte de verdade".

---

### 🟠 AP-49: Templates de Notificação Engessados
Só pode mandar mensagens proativas pré-aprovadas, não adapta ao contexto.

**Sinal:** "Seu pedido #12345 foi enviado" quando usuário tem 3 pedidos ativos.

---

### 🟡 AP-50: Custo Cego por Categoria
Não monitora qual tipo de conversa (service vs marketing) está gerando conta.

**Sinal:** Fatura explode porque fluxo de suporte usa template de marketing.

---

### 🟡 AP-51: Abuso da Janela de 24h
Tenta reengajar usuário fora da janela de sessão livre sem template aprovado.

**Sinal:** Mensagem proativa bloqueada pela API.

---

### 🟡 AP-52: Template Category Mismatch
Usa template de marketing para aviso transacional (ou vice-versa).

**Sinal:** Rejeição de template pela Meta.

---

### 🟡 AP-53: Documentação Inexistente do Comportamento Esperado
Nem você sabe o que o bot deveria fazer em caso X.

**Sinal:** Discussão de "bug vs feature" vira debate filosófico.

---

## 7. JORNADA & CONVERSÃO (7 anti-patterns)

### 🟠 AP-54: Não Validar Disponibilidade/Estoque Antes de Prometer
Confirma pedido e só depois descobre que não tem o item.

**Sinal:** "Confirmado!" seguido de "na verdade não temos".

---

### 🟠 AP-55: Preço Desatualizado
Mostra valor de cache antigo e só falha no checkout.

**Sinal:** "R$ 10" vira "R$ 15" no último passo.

---

### 🟠 AP-56: Frete Surpresa
Custo adicional aparece tarde demais.

**Sinal:** Usuário já disse "sim" e aí descobre +R$ 20 de frete.

---

### 🟠 AP-57: Substituição sem Política Explícita
Trocar marca/genérico sem consentimento rastreável.

**Sinal:** Troca acontece "porque sim".

---

### 🟡 AP-58: Sem "Resumo do Estado" Antes de Confirmação
Pede "confirmar" sem mostrar o que exatamente será executado.

**Sinal:** "Confirmar?" sem repetir item, preço, entrega.

---

### 🟡 AP-59: Desconto/Cupom Aplicado Tarde
Mostra total, usuário confirma, depois lembra do cupom — e você não recalcula.

**Sinal:** "Já confirmou, não dá pra voltar".

---

### 🟡 AP-60: Sem Diferenciação de Urgência
Reclamação crítica, pedido novo, dúvida boba — todos na mesma fila.

**Sinal:** "Meu pedido veio errado" espera 2h igual a "qual o horário?".

---

## 8. HANDOFF & HUMANO (4 anti-patterns)

### 🟠 AP-61: "Fallback Humano" como Muleta Padrão
Bot só funciona quando dá tudo certo; no primeiro atrito vira fila humana.

**Sinal:** "Vou te encaminhar para um atendente" em qualquer desvio pequeno.

---

### 🟠 AP-62: Handoff Humano sem Contexto
Bot transfere pra atendente mas não manda histórico/estado.

**Sinal:** Humano pergunta tudo de novo.

---

### 🟡 AP-63: Respostas do Bot Competindo com Humanas
Atendente assume e bot continua respondendo.

**Sinal:** Dois "agentes" falando ao mesmo tempo.

---

### 🟢 AP-64: Sem Política de Privacidade Conversacional
Pede dado sensível no chat sem orientar limite, uso e cuidado.

**Sinal:** Solicitar CPF sem contexto e sem redução de exposição.

---

## REPROVAÇÃO AUTOMÁTICA (Deploy Bloqueado)

Presença de qualquer um desses **bloqueia deploy**:

| ID | Anti-Pattern | Categoria |
|----|--------------|-----------|
| AP-01 | Ignorar Anti-Fraude | Segurança |
| AP-02 | Dados Sensíveis em Plain Text | Segurança |
| AP-09 | Sem Idempotência | Integridade |
| AP-10 | Sem Trilha de Evidência | Integridade |
| AP-11 | Webhooks sem Retry/Idempotency | Integridade |
| AP-30 | Intenção Fantasma (LLM sem Guardrail) | Arquitetura |
| AP-46 | Rate Limit do WhatsApp Ignorado | Governança |
| AP-54 | Não Validar Estoque Antes de Prometer | Jornada |

---

## Estatísticas

- **Total:** 64 anti-patterns
- **Críticos (🔴):** 8
- **Alto (🟠):** 26
- **Médio (🟡):** 26
- **Baixo (🟢):** 4

**Distribuição por Categoria:**
- Segurança: 8
- Integridade: 9
- UX Conversacional: 12
- Arquitetura: 10
- Multimodalidade: 6
- Governança: 8
- Jornada: 7
- Handoff: 4

