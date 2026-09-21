# Backlog Inicial — Connexa

> Rascunho pronto para ser transcrito ao Azure Boards (Etapa 3 do RA-01), na ordem Epic → Feature → User Story.
> As decisões sobre os pontos deliberadamente em aberto do briefing (slide 23) estão registradas na seção final.

---

## Épico 1 — Ingressante encontra e entra em um grupo compatível logo no início do semestre

**Descrição (Azure Boards):** Reduzir a barreira de descoberta e aproximação que hoje deixa o ingressante fora de qualquer grupo por não saber que eles existem nem como se aproximar, elevando a adesão de ~20% para 45% ao final do semestre, com foco prioritário no público ingressante.

### Feature 1.1 — Perfil e disponibilidade
**Descrição:** Permite que o estudante informe curso, disciplinas cursadas e horários livres, dado de base sem o qual nenhuma sugestão de compatibilidade é possível.

**US-01 — Declarar disciplinas cursadas** · 2 SP · `Must`
```
Como estudante da instituição,
quero informar quais disciplinas estou cursando neste semestre,
para que o Connexa saiba em quais grupos de estudo eu poderia me encaixar.
```
```gherkin
Cenário: Selecionar disciplinas do curso
  Dado que sou um estudante de Análise e Desenvolvimento de Sistemas
    E que estou autenticado no Connexa
  Quando seleciono "Algoritmos" e "Banco de Dados" na lista de disciplinas do meu curso
  Então as duas disciplinas aparecem salvas no meu perfil

Cenário: Tentar salvar sem nenhuma disciplina selecionada
  Dado que estou na tela de perfil
  Quando tento salvar sem marcar nenhuma disciplina
  Então vejo a mensagem "Selecione ao menos uma disciplina"
    E nenhuma alteração é salva

Cenário: Remover uma disciplina já cursada
  Dado que "Algoritmos" está salva no meu perfil
  Quando desmarco "Algoritmos" e salvo
  Então "Algoritmos" deixa de aparecer no meu perfil
    E deixo de receber sugestões de grupos dessa disciplina
```

**US-02 — Declarar horários de disponibilidade** · 3 SP (história de referência) · `Must`
```
Como estudante do período noturno,
quero informar os horários em que estou livre durante a semana,
para que só me sejam sugeridos grupos com os quais eu realmente consigo me encontrar.
```
```gherkin
Cenário: Marcar horários livres na grade semanal
  Dado que estou na tela de disponibilidade
  Quando marco "terça, 19h às 20h30" e "quinta, 19h às 20h30" como livres
  Então esses dois blocos aparecem destacados como disponíveis no meu perfil

Cenário: Tentar salvar disponibilidade vazia
  Dado que nenhum horário está marcado
  Quando tento salvar
  Então vejo a mensagem "Informe ao menos um horário disponível"

Cenário: Atualizar disponibilidade no meio do semestre
  Dado que "terça, 19h às 20h30" estava marcada como livre
  Quando desmarco esse horário porque comecei a trabalhar nesse período
  Então deixo de receber sugestões de grupos que se encontram nesse horário
    E grupos dos quais já participo não são removidos automaticamente
```

### Feature 1.2 — Descoberta e ingresso em grupo
**Descrição:** Dá visibilidade da oferta de grupos existentes por disciplina e permite solicitar ingresso ou criar um novo grupo, atacando diretamente a barreira relatada pelo ingressante.

**US-03 — Visualizar grupos existentes por disciplina** · 3 SP · `Must`
```
Como estudante ingressante,
quero ver a lista de grupos de estudo já existentes na minha disciplina,
para saber que a possibilidade existe e decidir se entro logo no início do semestre.
```
```gherkin
Cenário: Listar grupos de uma disciplina com vagas
  Dado que existem 2 grupos de "Algoritmos" com vagas abertas
  Quando abro a lista de grupos da disciplina "Algoritmos"
  Então vejo os 2 grupos, cada um com horário de encontro e número de integrantes

Cenário: Disciplina sem nenhum grupo formado
  Dado que "Estrutura de Dados" não tem nenhum grupo criado ainda
  Quando abro a lista de grupos dessa disciplina
  Então vejo a mensagem "Nenhum grupo encontrado" com a opção de criar um grupo

Cenário: Grupo já com o número máximo de integrantes
  Dado que um grupo de "Algoritmos" já tem 6 integrantes
  Quando abro a lista de grupos da disciplina
  Então esse grupo aparece marcado como "Vagas encerradas"
    E não posso solicitar ingresso nele
```

**US-04 — Solicitar ingresso em grupo existente** · 3 SP · `Must`
```
Como estudante,
quero solicitar ingresso em um grupo que já se encontra em um horário compatível com o meu,
para começar a estudar em grupo sem precisar montar um do zero.
```
```gherkin
Cenário: Solicitar ingresso em grupo compatível
  Dado que o grupo "Algoritmos - Terças 19h" tem vaga
    E que "terça, 19h às 20h30" está na minha disponibilidade
  Quando envio uma solicitação de ingresso
  Então a solicitação aparece como "Pendente" para mim
    E o organizador do grupo recebe a solicitação para análise

Cenário: Solicitar ingresso em grupo incompatível com meu horário
  Dado que o grupo se encontra "sábado, 9h"
    E que esse horário não está na minha disponibilidade
  Quando tento solicitar ingresso
  Então vejo um aviso "Este horário não está na sua disponibilidade"
    E posso confirmar o envio mesmo assim, sob minha responsabilidade

Cenário: Cancelar solicitação pendente
  Dado que tenho uma solicitação "Pendente" no grupo "Banco de Dados - Quintas 19h"
  Quando cancelo a solicitação antes de resposta do organizador
  Então a solicitação desaparece da minha lista de pendências
```

**US-05 — Criar grupo quando nenhum existente é compatível** · 5 SP · `Should`
```
Como estudante,
quero criar um novo grupo de estudo para uma disciplina,
para formar um grupo quando nenhum dos existentes tem horário compatível comigo.
```
```gherkin
Cenário: Criar grupo com disciplina, horário e tamanho máximo
  Dado que nenhum grupo de "Cálculo II" se encontra em horário compatível comigo
  Quando crio um grupo informando disciplina, horário de encontro e tamanho máximo de 5 integrantes
  Então o grupo aparece na lista de grupos de "Cálculo II" com uma vaga já ocupada por mim
    E passo a ser o organizador do grupo

Cenário: Tentar criar grupo sem informar horário de encontro
  Dado que estou preenchendo o formulário de criação de grupo
  Quando tento salvar sem informar um horário de encontro
  Então vejo a mensagem "Informe o horário de encontro do grupo"

Cenário: Tentar criar grupo com tamanho máximo fora do intervalo permitido
  Dado que estou criando um grupo
  Quando informo tamanho máximo de 8 integrantes
  Então vejo a mensagem "O tamanho do grupo deve ser entre 3 e 6 integrantes"
```

---

## Épico 2 — Monitoria forma e acompanha grupos sem cruzar disponibilidade manualmente

**Descrição:** Eliminar o cruzamento manual de horários que hoje consome as horas de bolsa da monitoria, redirecionando esse tempo ao atendimento direto de estudantes com dificuldade.

### Feature 2.1 — Sugestão de grupo por compatibilidade
**Descrição:** Cruza automaticamente disciplina e disponibilidade declaradas para sugerir agrupamentos, substituindo o cruzamento manual relatado pela monitoria.

**US-06 — Receber sugestão de grupos compatíveis** · 8 SP · `Should`
```
Como estudante,
quero receber sugestões de estudantes com disciplina e horário compatíveis com os meus,
para formar um grupo sem depender de já conhecer alguém.
```
```gherkin
Cenário: Sugestão com estudantes compatíveis existentes
  Dado que há 4 estudantes de "Algoritmos" com disponibilidade em "terça, 19h"
    E que também tenho essa disponibilidade
  Quando acesso a área de sugestões de grupo
  Então vejo uma sugestão de grupo com esses estudantes e o horário em comum

Cenário: Nenhum estudante compatível no momento
  Dado que sou o único estudante de "Estatística" com disponibilidade em "sexta, 20h"
  Quando acesso a área de sugestões de grupo
  Então vejo a mensagem "Nenhuma sugestão no momento" com a opção de criar um grupo

Cenário: Recusar uma sugestão recebida
  Dado que recebi uma sugestão de grupo para "Algoritmos"
  Quando recuso essa sugestão
  Então ela deixa de aparecer para mim
    E os demais estudantes sugeridos não são notificados da minha recusa
```

**US-07 — Aprovar ou recusar solicitação de ingresso** · 3 SP · `Should`
```
Como organizador de um grupo,
quero aprovar ou recusar uma solicitação de ingresso recebida,
para manter o grupo dentro do tamanho e do perfil que fazem sentido para ele.
```
```gherkin
Cenário: Aprovar solicitação dentro do limite do grupo
  Dado que meu grupo tem 4 de 6 vagas ocupadas
    E que recebi uma solicitação pendente
  Quando aprovo a solicitação
  Então o solicitante passa a constar como integrante do grupo
    E o grupo passa a ter 5 de 6 vagas ocupadas

Cenário: Recusar solicitação
  Dado que recebi uma solicitação pendente
  Quando recuso a solicitação
  Então o solicitante é notificado da recusa
    E a vaga permanece disponível

Cenário: Tentar aprovar solicitação com o grupo já no tamanho máximo
  Dado que meu grupo já tem 6 de 6 vagas ocupadas
  Quando tento aprovar uma nova solicitação
  Então vejo a mensagem "Grupo no tamanho máximo, não é possível aprovar"
```

### Feature 2.2 — Encontros
**Descrição:** Registra local ou meio do encontro e sua realização, dado que hoje simplesmente não existe no processo atual.

**US-08 — Registrar local ou meio do encontro** · 2 SP · `Should`
```
Como organizador de um grupo,
quero registrar se o próximo encontro será presencial ou remoto e onde ele ocorre,
para que todos os integrantes saibam onde e como se encontrar.
```
```gherkin
Cenário: Registrar encontro presencial
  Dado que sou organizador do grupo "Algoritmos - Terças 19h"
  Quando registro o próximo encontro como presencial na "Biblioteca, sala 3"
  Então todos os integrantes do grupo veem esse local ao consultar o próximo encontro

Cenário: Registrar encontro remoto
  Dado que sou organizador do grupo
  Quando registro o próximo encontro como remoto, usando a ferramenta de videoconferência já disponível na instituição
  Então todos os integrantes veem que o encontro é remoto

Cenário: Tentar registrar encontro sem escolher presencial ou remoto
  Dado que estou registrando um novo encontro
  Quando tento salvar sem selecionar a modalidade
  Então vejo a mensagem "Selecione se o encontro é presencial ou remoto"
```

**US-09 — Registrar presença no encontro** · 3 SP · `Could`
```
Como integrante de um grupo,
quero confirmar minha presença em um encontro já realizado,
para que o grupo tenha um histórico de encontros efetivamente ocorridos.
```
```gherkin
Cenário: Confirmar presença após o encontro
  Dado que o encontro de "terça, 19h" já ocorreu
  Quando confirmo minha presença nesse encontro
  Então meu nome aparece na lista de presentes daquele encontro

Cenário: Não confirmar presença em nenhum prazo
  Dado que o encontro ocorreu há 5 dias
    E que não confirmei presença
  Quando o grupo consulta o histórico de encontros
  Então apareço como "Não confirmado" nesse encontro, não como ausente

Cenário: Tentar confirmar presença em encontro futuro
  Dado que o próximo encontro do grupo ainda não ocorreu
  Quando tento confirmar presença nele
  Então vejo a mensagem "Este encontro ainda não ocorreu"
```

---

## Épico 3 — Coordenação enxerga, por curso e período, quantos grupos existem e quais estão ativos, a tempo de agir

**Descrição:** Dar à coordenação visibilidade agregada, por curso e período, sobre quantos grupos existem e quais seguem ativos, permitindo intervir antes da evasão em vez de constatar o problema ao final do semestre.

### Feature 3.1 — Sinalização de atividade do grupo
**Descrição:** Sinaliza automaticamente grupos ociosos ou encerrados, resolvendo a falta de aviso relatada pela monitoria quando um grupo simplesmente para de se encontrar.

**US-10 — Sinalizar grupo ocioso automaticamente** · 5 SP · `Could`
```
Como organizador de um grupo,
quero que o Connexa sinalize meu grupo como ocioso quando ele ficar tempo demais sem registro de encontro,
para perceber a tempo que o grupo está se dissolvendo.
```
```gherkin
Cenário: Grupo sem registro de encontro por 14 dias
  Dado que o grupo "Algoritmos - Terças 19h" não tem nenhum encontro registrado há 14 dias corridos
  Quando o painel de status do grupo é consultado
  Então o grupo aparece com o status "Ocioso"

Cenário: Grupo volta a registrar encontro após ficar ocioso
  Dado que o grupo está com status "Ocioso"
  Quando um novo encontro é registrado
  Então o status do grupo volta para "Ativo"

Cenário: Grupo recém-criado, ainda sem nenhum encontro
  Dado que o grupo foi criado há 3 dias e ainda não teve encontro registrado
  Quando o status do grupo é consultado
  Então o grupo aparece como "Ativo", não como "Ocioso"
```

**US-11 — Encerrar grupo inativo automaticamente** · 3 SP · `Could`
```
Como coordenação de curso,
quero que um grupo sem nenhum encontro registrado por muito tempo seja encerrado automaticamente,
para que o painel institucional reflita apenas grupos realmente em funcionamento.
```
```gherkin
Cenário: Grupo ocioso completa 30 dias sem encontro
  Dado que o grupo está com status "Ocioso" há 16 dias adicionais, totalizando 30 dias sem encontro
  Quando a rotina diária de verificação é executada
  Então o grupo passa para o status "Encerrado"
    E deixa de aparecer na lista de grupos com vaga

Cenário: Grupo registra encontro um dia antes de completar 30 dias ociosos
  Dado que o grupo está a um dia de completar 30 dias sem encontro
  Quando um novo encontro é registrado nesse dia
  Então o grupo permanece "Ativo" e não é encerrado

Cenário: Consultar histórico de um grupo encerrado
  Dado que o grupo "Cálculo II - Sábados 9h" foi encerrado por inatividade
  Quando um ex-integrante consulta o grupo
  Então ele vê o status "Encerrado" e o histórico de encontros que chegaram a ocorrer
```

### Feature 3.2 — Painel institucional
**Descrição:** Mostra à coordenação, de forma agregada por curso e período, a quantidade de grupos e a proporção de estudantes em grupo ativo — o indicador que a coordenação leva à direção.

**US-12 — Visualizar quantidade de grupos ativos por curso e período** · 5 SP · `Should`
```
Como coordenação de curso,
quero ver quantos grupos estão ativos, agrupados por curso e por período (matutino/noturno),
para decidir em qual curso e período alocar mais monitoria.
```
```gherkin
Cenário: Visualizar grupos agrupados por curso e período
  Dado que existem 12 grupos ativos no curso de ADS, sendo 8 no período noturno
  Quando abro o painel institucional
  Então vejo o total de 12 grupos ativos em ADS, com a quebra por período

Cenário: Curso sem nenhum grupo ativo no momento
  Dado que o curso de "Gestão de TI" não tem nenhum grupo ativo
  Quando abro o painel institucional
  Então esse curso aparece com "0 grupos ativos", não fica oculto do painel

Cenário: Filtrar o painel por período letivo
  Dado que estou vendo dados do semestre corrente
  Quando seleciono o filtro de período letivo anterior
  Então os números exibidos mudam para refletir apenas aquele semestre
```

**US-13 — Visualizar proporção de estudantes em grupo ativo** · 3 SP · `Should`
```
Como coordenação de curso,
quero ver a proporção de estudantes atualmente em algum grupo ativo,
para acompanhar a evolução em direção à meta de 45% definida para o semestre.
```
```gherkin
Cenário: Proporção calculada sobre o total de estudantes matriculados
  Dado que 500 dos 1.840 estudantes matriculados estão em grupo ativo
  Quando abro o indicador de adesão no painel
  Então vejo a proporção de aproximadamente 27%

Cenário: Nenhum estudante em grupo ativo no início do semestre
  Dado que nenhum grupo foi criado ainda neste semestre
  Quando abro o indicador de adesão
  Então vejo "0%", não um erro ou tela em branco

Cenário: Comparar com a meta institucional
  Dado que a proporção atual é 27%
  Quando visualizo o indicador
  Então vejo essa proporção ao lado da meta declarada de 45% para o semestre
```

---

## Épico 4 — Estudante acessa o Connexa de forma comprovada, segura e acessível

**Descrição:** Garantir que somente quem pertence à instituição acesse o sistema, com e-mail institucional comprovado, senha sob guarda do próprio Connexa e operação acessível — restrições vindas de TI, não negociáveis dentro do projeto.

### Feature 4.1 — Cadastro com e-mail institucional comprovado
**Descrição:** Implementa a exigência de TI de que o cadastro use e-mail institucional comprovado, com senha própria do Connexa sob guarda e responsabilidade da equipe.

**US-14 — Cadastrar-se com e-mail institucional** · 3 SP · `Must`
```
Como estudante da instituição,
quero me cadastrar usando meu e-mail institucional e uma senha própria do Connexa,
para acessar o sistema como alguém que pertence à instituição.
```
```gherkin
Cenário: Cadastro com e-mail institucional válido
  Dado que meu e-mail é "joao.silva@instituicao.edu.br"
  Quando me cadastro informando esse e-mail e uma senha
  Então minha conta é criada com status "Aguardando comprovação"

Cenário: Tentar cadastro com e-mail pessoal
  Dado que tento me cadastrar com "joaosilva@gmail.com"
  Quando envio o formulário de cadastro
  Então vejo a mensagem "Use seu e-mail institucional para se cadastrar"
    E nenhuma conta é criada

Cenário: Tentar cadastro com e-mail institucional já cadastrado
  Dado que "joao.silva@instituicao.edu.br" já possui conta no Connexa
  Quando tento me cadastrar novamente com o mesmo e-mail
  Então vejo a mensagem "Este e-mail já possui cadastro"
```

**US-15 — Comprovar o endereço institucional** · 5 SP · `Must`
```
Como estudante recém-cadastrado,
quero comprovar meu e-mail institucional por meio de um link enviado a ele,
para que minha conta seja liberada para uso completo do Connexa.
```
```gherkin
Cenário: Comprovar e-mail dentro do prazo
  Dado que me cadastrei há 10 minutos e recebi o link de comprovação
  Quando clico no link em até 48 horas do cadastro
  Então minha conta passa para o status "Ativa"

Cenário: Tentar comprovar após o prazo de 48 horas
  Dado que me cadastrei há 50 horas e ainda não cliquei no link
  Quando clico no link de comprovação
  Então vejo a mensagem "Link expirado" com a opção de reenviar

Cenário: Tentar usar o Connexa sem comprovar o e-mail
  Dado que minha conta está com status "Aguardando comprovação"
  Quando tento acessar a lista de grupos
  Então sou redirecionado à tela "Comprove seu e-mail para continuar"
```

**US-16 — Recuperar acesso por senha esquecida** · 3 SP · `Should`
```
Como estudante cadastrado,
quero redefinir minha senha a partir do meu e-mail institucional,
para recuperar o acesso à minha conta quando esqueço a senha.
```
```gherkin
Cenário: Solicitar redefinição com e-mail cadastrado
  Dado que "joao.silva@instituicao.edu.br" possui conta ativa
  Quando solicito redefinição de senha para esse e-mail
  Então recebo um link de redefinição válido por 1 hora

Cenário: Definir nova senha dentro do prazo
  Dado que recebi o link de redefinição há 20 minutos
  Quando defino uma nova senha e confirmo
  Então consigo acessar o Connexa com a nova senha

Cenário: Solicitar redefinição para e-mail não cadastrado
  Dado que "outro.email@instituicao.edu.br" nunca se cadastrou no Connexa
  Quando solicito redefinição de senha para esse e-mail
  Então vejo a mensagem genérica "Se o e-mail existir, enviaremos as instruções"
    E nenhum e-mail é de fato enviado
```

### Feature 4.2 — Acessibilidade e uso mobile
**Descrição:** Garante operação completa por teclado, compatibilidade com leitor de tela e uso confortável em celular, requisitos institucionais declarados por TI como não negociáveis.

**US-17 — Garantir acessibilidade nas telas principais** · 5 SP · `Must`
```
Como estudante que utiliza leitor de tela,
quero navegar e operar as telas de cadastro, lista de grupos e perfil inteiramente pelo teclado,
para usar o Connexa com a mesma autonomia que qualquer outro estudante.
```
```gherkin
Cenário: Navegar pela lista de grupos usando apenas o teclado
  Dado que estou na tela de lista de grupos
  Quando navego usando apenas a tecla Tab e ativo um item com Enter
  Então consigo abrir os detalhes do grupo sem usar o mouse

Cenário: Leitor de tela anuncia o status de uma solicitação
  Dado que uso um leitor de tela
  Quando uma solicitação de ingresso muda de "Pendente" para "Aprovada"
  Então o leitor de tela anuncia essa mudança de status

Cenário: Formulário de cadastro sem rótulos para o leitor de tela
  Dado que o campo de e-mail no formulário de cadastro não possui rótulo associado
  Quando um estudante usando leitor de tela navega até esse campo
  Então essa ausência de rótulo é tratada como defeito bloqueante, não como melhoria futura
```

---

## Resumo de priorização (backlog ordenado)

| Ordem | História | SP | Tag |
|---|---|---|---|
| 1 | US-14 Cadastrar-se com e-mail institucional | 3 | Must |
| 2 | US-15 Comprovar o endereço institucional | 5 | Must |
| 3 | US-17 Garantir acessibilidade nas telas principais | 5 | Must |
| 4 | US-01 Declarar disciplinas cursadas | 2 | Must |
| 5 | US-02 Declarar horários de disponibilidade | 3 | Must |
| 6 | US-03 Visualizar grupos existentes por disciplina | 3 | Must |
| 7 | US-04 Solicitar ingresso em grupo existente | 3 | Must |
| 8 | US-16 Recuperar acesso por senha esquecida | 3 | Should |
| 9 | US-05 Criar grupo quando nenhum existente é compatível | 5 | Should |
| 10 | US-07 Aprovar ou recusar solicitação de ingresso | 3 | Should |
| 11 | US-08 Registrar local ou meio do encontro | 2 | Should |
| 12 | US-12 Visualizar quantidade de grupos ativos por curso e período | 5 | Should |
| 13 | US-13 Visualizar proporção de estudantes em grupo ativo | 3 | Should |
| 14 | US-06 Receber sugestão de grupos compatíveis | 8 | Should |
| 15 | US-09 Registrar presença no encontro | 3 | Could |
| 16 | US-10 Sinalizar grupo ocioso automaticamente | 5 | Could |
| 17 | US-11 Encerrar grupo inativo automaticamente | 3 | Could |

Nenhuma tag `Wont` nesta lista — os itens fora do escopo (notas, mensageria, videoconferência própria, conteúdo didático, cobrança) já foram excluídos do backlog, conforme a Parte 3 do briefing, e por isso nem chegam a virar história.

---

## Decisões registradas sobre os pontos deliberadamente em aberto

| Ponto em aberto (briefing, slide 23) | Decisão adotada nesta versão | Onde aparece |
|---|---|---|
| Como alguém entra num grupo | Por solicitação, aprovada pelo organizador do grupo | US-04, US-07 |
| Tamanho do grupo | Entre 3 e 6 integrantes | US-05 |
| Como a presença é registrada | Autodeclarada por cada integrante | US-09 |
| Quando um grupo está inativo | Ocioso aos 14 dias sem encontro; encerrado aos 30 dias | US-10, US-11 |
| Encontro remoto ou presencial | Ambos suportados, com campo de modalidade | US-08 |
| O que a coordenação enxerga | Visão agregada por curso e período, não nominal | US-12, US-13 |

Estas são decisões de projeto, não do briefing — cabe à equipe justificá-las e, se quiser, registrá-las como ADR mais adiante.
