# Especificação de Requisitos do Sistema (SRS) - SAP / Livro Eletrônico

## 📌 Legenda de Níveis de Prioridade
- **Essencial (Alta):** Indispensável para a operação básica e segurança jurídica/sanitária. Sem ela o sistema não vai para produção.
- **Importante (Média):** Agrega alto valor à gestão e otimização do fluxo, mas cuja ausência temporária não paralisa a entrada de dados.
- **Desejável (Baixa):** Recurso secundário ou de usabilidade para sprints futuras.

---

## 1. Requisitos Funcionais (RF)

| ID | Título | Descrição | Prioridade |
| :--- | :--- | :--- | :--- |
| **RF001** | Autenticação Unificada | Permissão de login no sistema via integração com o serviço de diretório do hospital (LDAP/AD/AGHU). | Essencial |
| **RF002** | Livro de Protocolo Eletrônico (LPE) | Substituição do livro físico por registro digital nativo com assinatura eletrônica institucional de valor legal e imutabilidade do histórico. | Essencial |
| **RF003** | Validação & Trava na Recepção (RN-04) | Bloqueio automático de recepção de amostras com dados incompletos/inconsistentes oriundos do AGHU, gerando alerta automático de pendência ao setor solicitante. | Essencial |
| **RF004** | Rito de Retificação & Adendo (RN-05) | Permissão de alteração em registros/laudos já assinados eletronicamente apenas mediante justificativa obrigatória, gerando nova versão e preservando a versão original no histórico. | Essencial |
| **RF005** | Painel de Controle de TAT por Fase (RN-07) | Centralização de todos os fluxos operacionais (substituindo a planilha paralela) com monitoramento em tempo real da permanência da amostra em cada fase (Recepção, Macroscopia, Processamento, Microscopia, Laudo). | Essencial |
| **RF006** | Alertas Visuais de SLA (TAT) | Emissão de sinalização visual (amarelo para 80% do prazo e vermelho para atraso/estourado) parametrizada conforme a criticidade (Rotina vs. Urgência) por fase operacional. | Importante |
| **RF007** | Exportação de Histórico de Auditoria | Emissão de relatórios e relatórios de rastreabilidade completa de amostras para auditorias de acreditação (ONA/PALC). | Importante |

---

## 2. Requisitos Não Funcionais (RNF)

| ID | Categoria | Descrição |
| :--- | :--- | :--- |
| **RNF001** | Criptografia & Sessão | Tokens JWT assinados com HS256/RS256 e Cookies HttpOnly para Refresh Tokens. |
| **RNF002** | LGPD & Trilha de Auditoria | Trilha de auditoria obrigatória (`pista de auditoria` / `audit_logs`) registrando estado anterior e novo (antes/depois), usuário, IP e timestamp para qualquer mutação ou retificação. |
| **RNF003** | Segurança HTTP | Middleware com Security Headers (anti-cache `no-store`, `X-Frame-Options: DENY`, proteção anti-XSS e CSP). |
| **RNF004** | Governança de Segredos | Proibição rigorosa de credenciais/chaves hardcoded no código e validação estrita de variáveis de ambiente na inicialização (`src/config.py`). |
| **RNF005** | Alta Disponibilidade & Desempenho | O painel de controle (dashboard de TAT) deve atualizar em tempo real ou sob polling com tempo de resposta inferior a 2 segundos para até 50 usuários simultâneos. |

---

## 3. Detalhamento SDD (Padrão CARE)

### `[CARE-RF001]` Autenticação LDAP
- **Context (Contexto):** Servidor LDAP/AD do hospital configurado e credenciais de serviço ativas.
- **Action (Ação):** Criar middleware de autenticação que consulte o AD e mapeie os papéis do usuário (Técnico, Patologista, Chefia, Recepção).
- **Result (Resultado):** Token JWT gerado contendo claims de acesso; Retorna HTTP 401 Unauthorized em caso de falha de credenciais.
- **Evaluation (Avaliação):** Executar `npm test tests/auth.spec.ts` (deve atingir 100% de aprovação nos testes de integração).

### `[CARE-RF002]` Livro de Protocolo Eletrônico (LPE) e Assinatura
- **Context (Contexto):** Esquema de banco de dados `LIVRO_PROTOCOLO` e serviço de assinatura digital/eletrônica configurados.
- **Action (Ação):** Criar endpoint `POST /api/protocolos/assinar` para registrar e selar eletronicamente o protocolo da amostra.
- **Result (Resultado):** Registro marcado como assinado, imutável para edição direta, com hash de integridade gravado.
- **Evaluation (Avaliação):** Teste unitário confirmando que tentativas de edição direta via SQL/API em registros assinados retornam erro HTTP 403 Forbidden.

### `[CARE-RF003]` Validação & Trava na Recepção (RN-04)
- **Context (Contexto):** API de integração com o AGHU disponível e payload da requisição recebido na entrada do SAP.
- **Action (Ação):** Validar presença de campos obrigatórios (CNS/CPF do paciente, médico solicitante, topografia da peça e número da requisição). Se faltar algum dado, bloquear a entrada e enviar requisição de pendência ao AGHU.
- **Result (Resultado):** Amostra com status `PENDENTE_ORIGEM_AGHU` e notificação de alerta gerada para o setor solicitante; Impede o avanço para a bancada.
- **Evaluation (Avaliação):** Enviar JSON incompleto via `POST /api/recepcao/validar` e validar retorno HTTP 422 Unprocessable Entity contendo o mapa de campos ausentes.

### `[CARE-RF004]` Rito de Retificação e Versionamento (RN-05)
- **Context (Contexto):** Registro no LPE previamente assinado e necessidade de correção identificada.
- **Action (Ação):** Processar endpoint `POST /api/protocolos/{id}/retificar` exigindo o corpo contendo a nova informação e a string obrigatória `justificativa`.
- **Result (Resultado):** Registro original mantido e marcado como `HISTORICO_SUBSTITUIDO`; Novo registro gerado com versão incrementada (`v2`), vinculado ao registro pai e log de auditoria gravado.
- **Evaluation (Avaliação):** Executar teste automatizado enviando requisição sem justificativa (deve rejeitar com HTTP 400 Bad Request) e com justificativa (deve criar versão `v2` e manter a `v1` inalterada).

### `[CARE-RF005]` Painel de Alertas de TAT por Fase (RN-06/RN-07)
- **Context (Contexto):** Tabela de SLAs cadastrada e amostras em fluxo nas fases de Macroscopia, Processamento, Microscopia e Laudo.
- **Action (Ação):** Endpoint `GET /api/painel/tat` calcula a diferença entre o timestamp de entrada na fase atual e o horário corrente comparando ao SLA da urgência/rotina.
- **Result (Resultado):** Retorno dos itens com flags de SLA: `OK` (verde), `ATENCAO` (amarelo, >=80% do prazo) ou `ESTOURADO` (vermelho, >100% do prazo).
- **Evaluation (Avaliação):** Validar via teste unitário a correta alteração do flag de status visual injetando amostras mockadas com horários retroativos.
