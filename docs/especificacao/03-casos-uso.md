# Modelagem de Casos de Uso

## 1. Diagrama de Casos de Uso
*(Inserir imagem ou Mermaid)*
```mermaid

%%{init: {'theme': 'base', 'themeVariables': {'primaryColor': '#ffffff', 'edgeLabelBackground': '#ffffff', 'tertiaryColor': '#eeeeee'}}}%%
graph TB

    %% Definicao de Classes %%
    classDef actor fill:#f9f,stroke:#333,stroke-width:2px,rx:10,ry:10;
    classDef system fill:#bbb,stroke:#333,stroke-width:1px,stroke-dasharray:5 5;
    classDef usecase fill:#e1f5fe,stroke:#01579b,stroke-width:1px,rx:15,ry:15;

    %% Atores Externos %%
    Setor_Solicitante["Setor Solicitante<br/>(Externo)"]:::actor
    Paciente["Paciente<br/>(Externo)"]:::actor

    %% Atores Internos %%
    Recepcionista["Tecnico de Recepcao"]:::actor
    Tecnico_Macro["Tecnico de Macroscopia"]:::actor
    Tecnico_Processamento["Tecnico de Processamento"]:::actor
    Tecnico_IHQ["Tecnico de IHQ"]:::actor
    Medico_Patologista["Medico Patologista<br/>(Patologista / Residente / Preceptor)"]:::actor
    Arquivista["Arquivista"]:::actor

    %% Sistemas Externos %%
    Sistema_AGHU("Sistema AGHU"):::system
    Sistema_Siscan("Sistema Siscan"):::system

    %% Fronteira do Sistema %%
    subgraph SAP_System_Boundary["Sistema do Servico de Anatomia Patologica (HC UFPE)"]

        %% Recepcao e Triagem %%
        subgraph Recepcao_UCs["Subsistema Recepcao e Triagem"]
            UC_EntregarAmostra(["Entregar Amostra"]):::usecase
            UC_ConsultarSolicitacaoAGHU(["Consultar Solicitacao de Exame no AGHU"]):::usecase
            UC_CadastrarPaciente(["Cadastrar Paciente no AGHU"]):::usecase
            UC_RealizarSolicitacao(["Realizar Solicitacao de Exame no AGHU"]):::usecase
            UC_GerarCodigoBarras(["Gerar Codigo de Barras de Solicitacao"]):::usecase
            UC_RegistrarAmostraBipagem(["Registrar Amostra por Bipagem"]):::usecase
            UC_RegistrarPlanilhaControle(["Registrar em Planilha de Controle Interno"]):::usecase
            UC_ImprimirFichaTrabalho(["Imprimir Ficha de Trabalho"]):::usecase
            UC_RegistrarLivroProtocolo(["Registrar em Livro de Protocolo"]):::usecase
            UC_EncaminharMacro(["Encaminhar para Macroscopia"]):::usecase
        end

        %% Macroscopia e Congelamento %%
        subgraph Macro_UCs["Subsistema Macroscopia e Congelamento"]
            UC_RealizarAnaliseMacro(["Realizar Analise Macroscopica"]):::usecase
            UC_GerarCassetes(["Gerar Cassetes"]):::usecase
            UC_RegistrarAnaliseAGHU(["Registrar Analise Macroscopica no AGHU"]):::usecase
            UC_EncaminharProcessamento(["Encaminhar para Processamento Tecnico"]):::usecase
            UC_RealizarCongelamentoCortes(["Realizar Congelamento e Cortes"]):::usecase
        end

        %% Processamento Tecnico %%
        subgraph Processamento_UCs["Subsistema Processamento Tecnico"]
            UC_CentrifugarAmostra(["Centrifugar Amostra"]):::usecase
            UC_ProcessarCellBlock(["Processar Cell Block"]):::usecase
            UC_RealizarInclusao(["Realizar Inclusao"]):::usecase
            UC_RealizarCortes(["Realizar Cortes"]):::usecase
            UC_RealizarProcessamentoTecnico(["Realizar Processamento Tecnico"]):::usecase
        end

        %% Imuno-histoquimica %%
        subgraph IHQ_UCs["Subsistema Imuno-histoquimica (IHQ)"]
            UC_RealizarAnaliseIHQ(["Realizar Analise IHQ"]):::usecase
        end

        %% Microscopia %%
        subgraph Microscopia_UCs["Subsistema Microscopia"]
            UC_RevisarResidentPreceptor(["Revisar com Residente e Preceptor"]):::usecase
            UC_SolicitarExamesAdicionais(["Solicitar Exames Adicionais"]):::usecase
            UC_FinalizarDiagnostico(["Finalizar Diagnostico"]):::usecase
            UC_GerarLaudoEnviarAGHU(["Gerar Laudo e Enviar para AGHU"]):::usecase
            UC_GerarLaudoSiscan(["Gerar Laudo no Siscan"]):::usecase
        end

        %% Arquivo %%
        subgraph Arquivo_UCs["Subsistema Arquivo"]
            UC_ArquivarFichaTrabalho(["Arquivar Ficha de Trabalho"]):::usecase
        end
    end

    %% Associacoes: Recepcao %%
    Setor_Solicitante -. "Entregar" .-> UC_EntregarAmostra
    Paciente -. "Entregar" .-> UC_EntregarAmostra

    Recepcionista --- UC_EntregarAmostra
    Recepcionista --- UC_ConsultarSolicitacaoAGHU
    Recepcionista --- UC_CadastrarPaciente
    Recepcionista --- UC_RealizarSolicitacao
    Recepcionista --- UC_RegistrarAmostraBipagem
    Recepcionista --- UC_RegistrarPlanilhaControle
    Recepcionista --- UC_ImprimirFichaTrabalho
    Recepcionista --- UC_RegistrarLivroProtocolo
    Recepcionista --- UC_EncaminharMacro

    %% Associacoes: Macroscopia %%
    Tecnico_Macro --- UC_RealizarAnaliseMacro
    Tecnico_Macro --- UC_GerarCassetes
    Tecnico_Macro --- UC_RegistrarAnaliseAGHU
    Tecnico_Macro --- UC_EncaminharProcessamento
    Tecnico_Macro --- UC_RealizarCongelamentoCortes

    %% Associacoes: Processamento Tecnico %%
    Tecnico_Processamento --- UC_CentrifugarAmostra
    Tecnico_Processamento --- UC_ProcessarCellBlock
    Tecnico_Processamento --- UC_RealizarInclusao
    Tecnico_Processamento --- UC_RealizarCortes
    Tecnico_Processamento --- UC_RealizarProcessamentoTecnico

    %% Associacoes: IHQ %%
    Tecnico_IHQ --- UC_RealizarAnaliseIHQ

    %% Associacoes: Microscopia %%
    Medico_Patologista --- UC_RevisarResidentPreceptor
    Medico_Patologista --- UC_SolicitarExamesAdicionais
    Medico_Patologista --- UC_FinalizarDiagnostico
    Medico_Patologista --- UC_GerarLaudoEnviarAGHU
    Medico_Patologista --- UC_GerarLaudoSiscan

    %% Associacoes: Arquivo %%
    Arquivista --- UC_ArquivarFichaTrabalho

    %% Integracoes com Sistemas Externos %%
    UC_ConsultarSolicitacaoAGHU <--> Sistema_AGHU
    UC_CadastrarPaciente <--> Sistema_AGHU
    UC_RealizarSolicitacao <--> Sistema_AGHU
    UC_GerarCodigoBarras <--> Sistema_AGHU
    UC_RegistrarAnaliseAGHU <--> Sistema_AGHU
    UC_GerarLaudoEnviarAGHU <--> Sistema_AGHU
    UC_GerarLaudoSiscan <--> Sistema_Siscan

    %% Relacionamentos entre Casos de Uso %%
    UC_FinalizarDiagnostico -.-> UC_GerarLaudoEnviarAGHU
    UC_FinalizarDiagnostico -.-> UC_GerarLaudoSiscan
    UC_FinalizarDiagnostico -.-> UC_SolicitarExamesAdicionais
```
## 2. Especificação (Exemplo)
# Especificação Detalhada de Casos de Uso (UC) e SDD (CARE) - SAP / HC-UFPE
---

## 1. Sub-sistema: Recepção & Triagem

### UC001 - Entregar Amostra
- Ator: Setor Solicitante / Paciente / Técnico de Recepção.
- Fluxo: Apresentar amostra física e documentação na recepção -> Recepcionista confere integridade do recipiente -> Identificar se paciente é interno (HU) ou externo.

[CARE-UC001] Recebimento e Conferência de Amostra
- Context: Amostra entregue na recepção física do SAP.
- Action: Registrar o recebimento físico e validar a integridade do recipiente.
- Result: Amostra apta para início da triagem ou devolvida ao setor de origem em caso de inadequação.
- Evaluation: Teste unitário deve validar a rejeição de recipientes marcados como "danificado/vazando".

### UC002 - Consultar Solicitação de Exame no AGHU
- Ator: Técnico de Recepção.
- Fluxo: Buscar solicitação por CPF/CNS ou número do atendimento -> Verificar status no AGHU -> Confirmar vínculo com o paciente.

[CARE-UC002] Integração de Consulta com AGHU
- Context: Operador com requisição em mãos na recepção.
- Action: Enviar requisição GET /api/aghu/solicitacao/{id} para o barramento do AGHU.
- Result: Dados do paciente, médico e exame retornados e exibidos na tela.
- Evaluation: Teste de integração deve simular resposta com sucesso (HTTP 200) e registro não encontrado (HTTP 404).

### UC003 - Cadastrar Paciente no AGHU
- Ator: Técnico de Recepção.
- Fluxo: Identificar ausência do paciente no AGHU -> Inserir dados demográficos (Nome, CPF, CNS, Data de Nasc.) -> Salvar cadastro.

[CARE-UC003] Cadastro Excepcional de Paciente Externo
- Context: Paciente externo sem cadastro prévio no AGHU.
- Action: Executar rotina de criação de paciente no AGHU via API integrada.
- Result: Registro do paciente persistido no AGHU e id_paciente gerado.
- Evaluation: Validar com JSON Schema a obrigatoriedade do CPF ou CNS válido.

### UC004 - Realizar Solicitação de Exame no AGHU
- Ator: Técnico de Recepção.
- Fluxo: Selecionar paciente cadastrado -> Escolher tipo de exame patológico -> Vincular médico solicitante -> Confirmar solicitação.

[CARE-UC004] Geração de Solicitação de Exame
- Context: Paciente cadastrado e sem solicitação cadastrada previamente.
- Action: Inserir pedido do exame na base do AGHU.
- Result: Código de solicitação de exame gerado com sucesso.
- Evaluation: Teste automatizado deve garantir o vínculo correto entre id_paciente e id_solicitacao.

### UC005 - Gerar Código de Barras de Solicitação
- Ator: Sistema AGHU / Técnico de Recepção.
- Fluxo: Processar confirmação da solicitação -> Gerar código de barras único -> Exibir para impressão.

[CARE-UC005] Emissão de Código de Barras
- Context: Solicitação de exame confirmada no AGHU.
- Action: Gerar string codificada e renderizar código de barras padrão Code128/DataMatrix.
- Result: Código impresso ou disponível para leitura óptica imediata.
- Evaluation: Validar se a string do código de barras é idêntica ao id_solicitacao retornado.

### UC006 - Registrar Amostra por Bipagem (RN-04)
- Ator: Técnico de Recepção.
- Fluxo: Bipar código de barras da solicitação -> Executar validação de campos obrigatórios -> Travar entrada se houver inconsistência ou confirmar registro.

[CARE-UC006] Validação Rigorosa da Entrada (RN-04)
- Context: Código de barras lido pelo leitor óptico.
- Action: Validar presença dos campos obrigatórios (Topografia, Material, Solicitante).
- Result: Amostra aceita ou travada com envio de alerta automático de inconsistência ao setor solicitante no AGHU.
- Evaluation: Enviar payload com campo topografia em branco e validar retorno de trava e notificação emitida.

### UC007 - Registrar em Planilha de Controle Interno
- Ator: Técnico de Recepção.
- Fluxo: Localizar campos pendentes na ficha -> Inserir refinamentos manuais de dados -> Atualizar controle paralelo.

[CARE-UC007] Registro Transitório na Planilha Paralela (RN-07)
- Context: Amostra recebida necessitando de refinamento manual de dados.
- Action: Inserir dados complementares no controle temporário antes da descontinuação no go-live.
- Result: Registro temporário atualizado para acompanhamento.
- Evaluation: Teste unitário deve garantir que dados salvos na planilha reflitam no painel do sistema.

### UC008 - Imprimir Ficha de Trabalho
- Ator: Técnico de Recepção.
- Fluxo: Selecionar amostra triada -> Enviar comando de impressão -> Obter Ficha de Trabalho física.

[CARE-UC008] Emissão da Ficha de Trabalho
- Context: Amostra validada e triada com sucesso.
- Action: Gerar documento formatado com dados da requisição, topografia e código de barras.
- Result: Spool de impressão enviado para a impressora térmica/padronizada do setor.
- Evaluation: Teste visual/integrado garantindo que todos os dados do paciente e requisição constam no Layout.

### UC009 - Registrar em Livro de Protocolo Eletrônico (LPE)
- Ator: Técnico de Recepção / Sistema.
- Fluxo: Confirmar triagem -> Gravar dados no Livro Eletrônico -> Selar registro com assinatura eletrônica institucional.

[CARE-UC009] Registro Imutável no Livro Eletrônico (RN-02)
- Context: Amostra pronta para entrar no fluxo analítico.
- Action: Criar entrada no Livro Eletrônico e aplicar assinatura digital institucional.
- Result: Protocolo oficial assinado, imutável e substituindo integralmente o livro físico.
- Evaluation: Tentativa de alteração direta sem rito de retificação deve retornar HTTP 403 Forbidden.

### UC010 - Encaminhar para Macroscopia
- Ator: Técnico de Recepção.
- Fluxo: Juntar amostra física com Ficha de Trabalho -> Colocar na bandeja de transporte -> Mover física e logicamente para a sala de Macroscopia.

[CARE-UC010] Transição de Fase (Recepção -> Macroscopia)
- Context: Livro eletrônico assinado e ficha impressa.
- Action: Atualizar status do protocolo para "ENVIADO_MACROSCOPIA" e disparar cronômetro de SLA da fase.
- Result: Amostra visível na fila de trabalho da Macroscopia no painel de TAT.
- Evaluation: Teste de banco de dados confirmando alteração de status e gravação do timestamp de saída da recepção.

---

## 2. Sub-sistema: Macroscopia / Congelamento

### UC011 - Realizar Análise Macroscópica
- Ator: Técnico de Macroscopia / Médico Patologista.
- Fluxo: Receber amostra com Ficha de Trabalho -> Avaliar peça física -> Identificar se requer congelamento prévio -> Ditar/Digitar relatório macroscópico.

[CARE-UC011] Registro da Descrição Macroscópica
- Context: Amostra disponível na bancada de macroscopia.
- Action: Capturar texto de descrição macroscópica e dimensões da peça.
- Result: Descrição gravada e associada ao protocolo do exame.
- Evaluation: Validar exigência de texto mínimo de 10 caracteres no campo de descrição macroscópica.

### UC012 - Realizar Congelamento e Cortes
- Ator: Técnico de Macroscopia / Médico Patologista.
- Fluxo: Identificar amostra para exame intraoperatório/congelamento -> Resfriar peça em criostato -> Realizar cortes histológicos rápidos -> Encaminhar para análise imediata.

[CARE-UC012] Processamento Rápido de Congelamento
- Context: Amostra assinalada com prioridade de "Congelamento/Intraoperatório".
- Action: Registrar horário de início e fim do corte no criostato com flag de urgência máxima.
- Result: Lâmina de congelamento gerada para leitura imediata com alerta de SLA crítico.
- Evaluation: Validar se o tempo do processo aciona alerta vermelho se exceder 30 minutos.

### UC013 - Gerar Cassetes
- Ator: Técnico de Macroscopia.
- Fluxo: Definir fragmentos da clivagem -> Definir quantidade de fragmentos/blocos -> Imprimir etiquetas com código do cassete.

[CARE-UC013] Clivagem e Identificação de Cassetes
- Context: Exame macroscópico concluído.
- Action: Informar quantidade N de blocos e acionar impressora de cassetes.
- Result: Cassetes identificados e vinculados como filhos do protocolo pai.
- Evaluation: Teste unitário confirmando a criação de N registros na tabela CASSETE vinculados ao id_protocolo.

### UC014 - Registrar Análise Macroscópica no AGHU
- Ator: Técnico de Macroscopia / Sistema.
- Fluxo: Finalizar clivagem -> Sincronizar descrição e quantidade de cassetes com o sistema AGHU.

[CARE-UC014] Sincronização de Macroscopia com AGHU
- Context: Análise macroscópica e cassetes finalizados no SAP.
- Action: Enviar payload com a descrição e número de blocos para a API do AGHU.
- Result: AGHU atualizado com as informações da fase macroscópica.
- Evaluation: Teste de integração verificando retorno HTTP 200 da API do AGHU.

### UC015 - Encaminhar para Processamento Técnico
- Ator: Técnico de Macroscopia.
- Fluxo: Acomodar cassetes no cesto do processador -> Mover Ficha de Trabalho -> Atualizar status no painel.

[CARE-UC015] Transição de Fase (Macroscopia -> Processamento)
- Context: Cassetes produzidos e registrados.
- Action: Atualizar status do exame para "EM_PROCESSAMENTO_TECNICO".
- Result: Fim da contagem de SLA da Macroscopia e início do SLA do Processamento Técnico.
- Evaluation: Confirmar se o tempo de permanência na macroscopia foi gravado na tabela de métricas de TAT.

---

## 3. Sub-sistema: Processamento Técnico

### UC016 - Centrifugar Amostra
- Ator: Técnico de Processamento.
- Fluxo: Identificar amostra líquida (citologia/efusões) -> Acomodar na centrífuga -> Executar ciclo de centrifugação -> Separar sedimento.

[CARE-UC016] Processamento de Amostras Líquidas
- Context: Amostra recebida identificada com flag "Líquido/Citologia".
- Action: Iniciar etapa de centrifugação e separar o botão celular.
- Result: Amostra preparada para confecção de lâmina citológica ou Cell Block.
- Evaluation: Validar alteração no fluxo para ramificação de Citopatologia.

### UC017 - Processar Cell Block
- Ator: Técnico de Processamento.
- Fluxo: Recuperar sedimento centrifugado -> Adicionar meio de inclusão (ágar/formol) -> Solidificar sedimento -> Acomodar em cassete histológico.

[CARE-UC017] Confecção de Cell Block (Bloco de Células)
- Context: Sedimento celular obtido no UC016.
- Action: Converter amostra líquida em um bloco histológico manipulável.
- Result: Cassete de Cell Block gerado e integrado ao fluxo de histologia tradicional.
- Evaluation: Teste unitário confirmando a conversão do tipo da amostra de "Líquida" para "Cell Block".

### UC018 - Realizar Inclusão
- Ator: Técnico de Processamento.
- Fluxo: Retirar cassetes do processador de tecidos -> Despejar parafina líquida em molde -> Posicionar o tecido -> Esfriar para formar o bloco de parafina.

[CARE-UC018] Etapa de Inclusão em Parafina
- Context: Cassetes desidratados e diafanizados.
- Action: Registrar a conclusão do ciclo no processador de tecidos e montagem dos blocos de parafina.
- Result: Blocos rígidos prontos para microtomia (corte).
- Evaluation: Validar se a etapa é concluída sem erros no log de fluxo.

### UC019 - Realizar Cortes
- Ator: Técnico de Processamento.
- Fluxo: Fixar bloco de parafina no micrótomo -> Ajustar espessura de corte (micras) -> Obter fita de tecido -> Pescar fita na água e fixar na lâmina de vidro.

[CARE-UC019] Etapa de Microtomia (Corte)
- Context: Blocos de parafina consolidados.
- Action: Registrar a confecção da lâmina não corada a partir do bloco.
- Result: Lâmina histológica física preparada para coloração.
- Evaluation: Confirmar associação entre o ID do bloco e a lâmina numerada gerada.

### UC020 - Realizar Processamento Técnico
- Ator: Técnico de Processamento.
- Fluxo: Submeter lâminas à bateria de coloração (H&E / Especiais) -> Aplicar resina e lamínula (montagem) -> Organizar lâminas em pranchas -> Distribuir aos médicos patologistas.

[CARE-UC020] Finalização do Processamento Histológico
- Context: Lâminas cortadas disponíveis.
- Action: Executar rotina de coloração/montagem e alterar status para "LÂMINAS_PRONTAS".
- Result: Lâminas físicas entregues aos patologistas e notificação enviada para a fase de Microscopia.
- Evaluation: Validar encerramento do SLA da fase de Processamento Técnico no painel de TAT.

---

## 4. Sub-sistema: Imunohistoquímica (IHQ)

### UC021 - Realizar Análise IHQ
- Ator: Técnico de IHQ / Médico Patologista.
- Fluxo: Receber solicitação de painel de marcadores -> Recuperar bloco de parafina -> Aplicar anticorpos específicos -> Processar revelação -> Entregar lâminas coradas ao Patologista.

[CARE-UC021] Fluxo de Exame Complementar IHQ
- Context: Pedido de ampliação de painel IHQ emitido na Microscopia.
- Action: Registrar a recepção do bloco no setor de IHQ, aplicação dos reagentes e disponibilização da nova lâmina.
- Result: Status do protocolo atualizado com extensão de prazo configurada e lâminas de IHQ vinculadas.
- Evaluation: Teste unitário validando a adição automática do tempo de extensão no SLA global do exame.

---

## 5. Sub-sistema: Microscopia

### UC022 - Revisar com Residente e Preceptor
- Ator: Médico Patologista / Residente.
- Fluxo: Receber lâminas e Ficha de Trabalho -> Analisar achados histológicos no microscópio -> Discutir hipóteses entre residente e preceptor -> Definir conduta.

[CARE-UC022] Sessão de Revisão de Lâminas
- Context: Lâminas físicas e Ficha de Trabalho na bancada médica.
- Action: Registrar impressões diagnósticas preliminares do residente e anotações do preceptor.
- Result: Rascunho de laudo preenchido em ambiente colaborativo.
- Evaluation: Validar permissão de escrita de rascunho para residentes e restrição de assinatura apenas para o preceptor.

### UC023 - Solicitar Exames Adicionais
- Ator: Médico Patologista.
- Fluxo: Identificar incerteza diagnóstica -> Solicitar coloração especial, novos cortes ou painel IHQ -> Enviar amostra de volta para a fase correspondente.

[CARE-UC023] Recuo de Fluxo para Exames Adicionais
- Context: Análise microscópica inconclusiva sem exames complementares.
- Action: Disparar evento de solicitação de re-corte, coloração especial ou IHQ com justificativa médica.
- Result: Amostra reencaminhada para a fila técnica com indicação visual de retorno no painel.
- Evaluation: Teste automatizado garantindo que o status retorna para "EM_PROCESSAMENTO" ou "EM_IHQ" mantendo a rastreabilidade original.

---

## 6. Sub-sistema: Pós-Analítica (Laudos, Notificação & Arquivo)

### UC024 - Finalizar Diagnóstico
- Ator: Médico Patologista.
- Fluxo: Concluir análise histológica -> Inserir texto final do laudo -> Validar adequação dos códigos topográficos/morfológicos -> Aprovar laudo para liberação.

[CARE-UC024] Encerramento da Análise Médica
- Context: Discussão e exames complementares concluídos.
- Action: Inserir texto conclusivo no campo de laudo e acionar botão "Finalizar Diagnóstico".
- Result: Diagnóstico travado para edições diretas e pronto para assinatura/transmissão.
- Evaluation: Validar se todos os campos obrigatórios do laudo foram preenchidos antes de permitir a finalização.

### UC025 - Gerar Laudo e Enviar para AGHU
- Ator: Médico Patologista / Sistema.
- Fluxo: Acionar assinatura eletrônica -> Gerar arquivo PDF do laudo final -> Transmitir laudo assinado para a base do AGHU.

[CARE-UC025] Transmissão Eletrônica de Laudo Assinado
- Context: Diagnóstico finalizado e assinado eletronicamente.
- Action: Gerar PDF/A com marca d'água/hash e invocar webservice de recepção de laudos do AGHU.
- Result: Laudo disponível imediatamente no prontuário eletrônico do paciente no AGHU e cronômetro total de TAT encerrado.
- Evaluation: Executar npm test tests/aghu_laudo.spec.ts validando o upload e confirmação de recebimento pelo AGHU.

### UC026 - Gerar Laudo no Siscan
- Ator: Médico Patologista.
- Fluxo: Identificar se o exame é de notificação obrigatória no Siscan (ex: Colo do Útero/Mama) -> Acessar módulo Siscan -> Inserir formulário específico de laudo -> Confirmar liberação.

[CARE-UC026] Notificação e Laudo para o Siscan
- Context: Exame com topografia/tipo coberto pelo programa Siscan/MS.
- Action: Preencher campos padronizados do formulário do Ministério da Saúde e transmitir ao Siscan.
- Result: Registro de laudo oncológico publicado no sistema federal Siscan.
- Evaluation: Teste de integração validando o preenchimento de todos os códigos de lesão exigidos pelo Siscan.

### UC027 - Arquivar Ficha de Trabalho e Amostras
- Ator: Arquivista / Técnico de Apoio.
- Fluxo: Recolher lâminas e blocos processados e laudados -> Recolher Ficha de Trabalho -> Escanear/Bipar localização de gaveta/caixa -> Armazenar nos arquivos físicos correspondentes.

[CARE-UC027] Rastreabilidade de Arquivo Físico
- Context: Exame laudado e finalizado em todas as fases.
- Action: Vincular o id_protocolo, blocos e lâminas ao código da gaveta/caixa de arquivo físico no sistema.
- Result: Localização exata do material histológico mapeada para futuras consultas, revisões ou amostras judiciais.
- Evaluation: Teste de consulta buscando id_protocolo e validando o retorno do endereço exato do arquivo (Ex: Armário 2, Gaveta B, Caixa 15).
