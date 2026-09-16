# Charter de Teste Exploratório: Transferência via Pix

> Exemplo gerado com a skill **charter-teste-exploratorio**, a partir do pedido:
>
> *"Crie um charter de teste exploratório para a transferência via Pix (envio por chave Pix) em um aplicativo bancário mobile. Objetivo: validar o fluxo de envio, limites e segurança. Nível de risco alto, sessão de 90 minutos."*

| Campo | Valor |
|---|---|
| **Funcionalidade** | Envio de Pix por chave (CPF/CNPJ, e-mail, telefone e chave aleatória) no app mobile |
| **Objetivo** | Validar o fluxo de envio, as regras de limite e os controles de segurança |
| **Tempo da sessão** | 90 minutos |
| **Nível de risco** | Alto (domínio financeiro, movimentação de saldo em tempo real) |

---

## Missão

Investigar o **envio de Pix por chave** no aplicativo mobile para identificar **falhas de débito, violação de limites e brechas de autenticação**, com foco em **integridade do saldo e segurança da transação**.

---

## Riscos Identificados

### Riscos de Negócio

- **Débito sem crédito:** valor sai da conta de origem e não chega ao destinatário
- **Débito em duplicidade** causado por reenvio ou falha de confirmação
- **Limites ignorados:** transação aprovada acima do limite diário, por transação ou noturno configurado
- **Transferência para destinatário errado** por exibição incorreta dos dados da chave consultada
- **Comprovante divergente** da transação efetivada (valor, data, destinatário ou identificador)
- **Não conformidade** com as regras do arranjo Pix definidas pelo Banco Central

### Riscos Técnicos

- **Timeout na comunicação** com o sistema de pagamentos sem estorno ou atualização de status
- **Inconsistência de saldo** entre a tela inicial, o extrato e o comprovante
- **Concorrência:** duas transferências simultâneas consumindo o mesmo saldo
- **Consulta de chave desatualizada** (chave excluída ou portada para outra instituição)
- **Status da transação preso** em "processando" após perda de conexão
- **Degradação de desempenho** em horários de pico

### Riscos de Segurança e UX

- **Transação concluída sem autenticação** (senha, biometria ou token) ou com sessão expirada
- **Exposição de dados sensíveis** do destinatário além do permitido (CPF completo, por exemplo)
- **Mensagens de erro técnicas** exibidas ao cliente (códigos internos, stack trace)
- **Confirmação acidental:** botão de confirmar muito próximo de outras ações ou sem tela de revisão
- **Captura de tela** permitida em telas com dados sensíveis, contrariando a política do app
- **Ausência de alerta** para valores atípicos ou destinatários nunca utilizados

---

## Áreas para Exploração

- **Fluxo principal:** seleção do tipo de chave, consulta, revisão, autenticação e comprovante
- **Consulta de chave:** exibição do nome e da instituição do destinatário para cada tipo de chave
- **Validação de valor:** formato, casas decimais, valor mínimo e saldo disponível
- **Regras de limite:** limite por transação, diário e noturno, incluindo alteração de limite pelo app
- **Autenticação da transação:** senha, biometria, bloqueio após tentativas e sessão expirada
- **Tratamento de falhas:** perda de rede, app em segundo plano e resposta lenta do servidor
- **Pós-transação:** comprovante, extrato, notificação push e atualização do saldo
- **Pix agendado e favoritos:** reutilização de destinatários salvos e envio programado

---

## Cenários Positivos

- Enviar **R$ 10,00** para chave **CPF** válida com saldo suficiente e autenticação por biometria
- Enviar Pix para chave de **e-mail** válida e conferir nome e instituição exibidos na revisão
- Enviar Pix para chave de **telefone** com DDD e confirmar o crédito na conta de destino
- Enviar Pix para **chave aleatória** colada da área de transferência
- Enviar valor **exatamente igual ao saldo disponível** e verificar saldo final zerado
- Incluir **mensagem opcional** e confirmar que ela aparece no comprovante e para o recebedor
- **Salvar o destinatário como favorito** ao final e reutilizá-lo em um novo envio
- **Agendar um Pix** para o próximo dia útil e verificar a efetivação na data
- **Compartilhar o comprovante** em PDF e conferir todos os dados com o extrato
- Enviar Pix **dentro do limite noturno** configurado, no período em que ele se aplica

---

## Cenários Negativos

- Informar **CPF com dígito verificador inválido** como chave
- Informar **e-mail em formato inválido** (sem "@" ou com domínio incompleto)
- Consultar uma **chave inexistente** ou excluída pelo titular
- Enviar valor **maior que o saldo** disponível
- Enviar valor **acima do limite por transação** configurado
- Enviar valor que **ultrapassa o limite diário** somando transações anteriores do dia
- Confirmar com **senha incorreta** até atingir o bloqueio
- Tentar confirmar após **expiração da sessão** por inatividade na tela de revisão
- Deixar o **campo de valor vazio** ou preenchido com **R$ 0,00**
- Tentar enviar Pix **para a própria chave** da conta de origem
- Inserir **letras ou símbolos** no campo de valor por colagem

---

## Casos Extremos (Edge Cases)

- Valor **exatamente no limite** por transação e **R$ 0,01 acima** dele (BVA)
- Valor mínimo absoluto de **R$ 0,01**
- **Duplo toque** no botão de confirmar (idempotência)
- **Perda de conexão** logo após a autenticação e antes da resposta do servidor
- **Duas transferências simultâneas** em dois dispositivos logados na mesma conta, somando mais que o saldo
- Envio iniciado às **21h59** e confirmado após a entrada do **período de limite noturno**
- Envio iniciado às **23h59** e confirmado após a **virada do dia**, afetando o limite diário
- **Mensagem opcional** com emojis, acentos e no tamanho máximo permitido
- App enviado para **segundo plano** durante o processamento e reaberto em seguida
- Chave de destino **alterada ou portada** entre a consulta e a confirmação

---

## Perguntas Investigativas

1. O que acontece com o saldo se a resposta do servidor não chegar ao app? Existe **estorno automático** ou reconciliação?
2. Um duplo toque no confirmar gera **duas transações** ou o sistema garante idempotência?
3. Quais dados do destinatário são exibidos na revisão, e eles estão **mascarados** conforme a política de privacidade?
4. O limite noturno é calculado pelo **horário do dispositivo** ou do servidor?
5. O limite diário considera **Pix agendados** para o mesmo dia?
6. Após quantas **tentativas de senha incorreta** a conta é bloqueada, e como o cliente é informado?
7. Se a sessão expirar na tela de revisão, os dados digitados são **perdidos** ou mantidos?
8. O comprovante exibe o **identificador da transação** (ID fim a fim) que permite rastreá-la?
9. A **notificação push** chega mesmo com o app fechado, e com qual conteúdo?
10. O saldo exibido na tela inicial é **atualizado imediatamente** ou só após recarregar?
11. Existe **alerta de segurança** para valores altos ou destinatários novos?
12. As **mensagens de erro** diferenciam saldo insuficiente, limite excedido e falha de comunicação?
13. O que acontece com um **Pix agendado** se não houver saldo na data?
14. As transações ficam registradas em **log de auditoria** com dispositivo, data e hora?

---

## Técnicas e Heurísticas Aplicadas

| Técnica | Justificativa |
|---|---|
| **Testes Baseados em Risco** | Prioriza cenários com impacto financeiro direto, como débito sem crédito e duplicidade |
| **Análise de Valor Limite (BVA)** | Aplicada aos limites por transação, diário e noturno, e ao valor mínimo |
| **Particionamento de Equivalência** | Agrupa os tipos de chave (CPF, e-mail, telefone, aleatória) em classes válidas e inválidas |
| **Testes de Segurança** | Cobre autenticação, bloqueio, sessão expirada e exposição de dados do destinatário |
| **Concurrency Testing** | Valida o consumo do mesmo saldo por transações simultâneas |
| **Error Guessing** | Explora falhas comuns do domínio bancário, como timeout sem estorno e status preso |
| **Heurística SFDPOT** | Garante cobertura de estrutura, função, dados, plataforma (Android e iOS), operações e tempo (virada de dia e horário noturno) |
| **Fluxos Alternativos** | Cobre envio por favoritos, agendamento e chave colada da área de transferência |

---

## Evidências Recomendadas

- **Capturas de tela** da revisão, da autenticação e do comprovante de cada cenário crítico
- **Gravação de vídeo** dos casos de concorrência, perda de conexão e duplo toque
- **Saldo e extrato antes e depois** de cada transação, nas duas contas envolvidas
- **Logs de rede** (proxy HTTP) com requisições, respostas e códigos de status
- **Comprovantes em PDF** comparados com o extrato e com o identificador da transação
- **Mensagens de erro completas**, com texto exibido e código, quando houver
- **Anotações com horário** de cada teste, principalmente nos cenários de limite noturno e virada de dia

---

## Dica de Execução

Para uma cobertura completa, divida a investigação em **3 sessões de 90 minutos**:

| Sessão | Foco | Conteúdo |
|---|---|---|
| **1** | Fluxo e validações | Cenários positivos, validação de chave e de valor, comprovante e extrato |
| **2** | Limites e regras | BVA dos limites, limite noturno, virada de dia, Pix agendado e favoritos |
| **3** | Segurança e resiliência | Autenticação, bloqueio, sessão expirada, concorrência, perda de rede e duplo toque |

Em cada sessão, reserve os **10 minutos finais** para registrar as evidências e anotar as perguntas que ficaram sem resposta, que podem virar charters para as próximas sessões.
