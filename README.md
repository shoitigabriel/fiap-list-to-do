# To-Do List Android

Aplicativo Android de gerenciamento de tarefas desenvolvido como atividade individual da disciplina **Android Development** da FIAP. O projeto permite listar, cadastrar, editar, concluir, desmarcar e excluir tarefas, mantendo os dados em persistência local.

A implementação integra a camada de interface criada com Jetpack Compose à arquitetura existente, utilizando Repository, ViewModel, Room, Coroutines/Flow e Navigation Compose.

## Funcionalidades

- Visualização das tarefas cadastradas
- Cadastro de novas tarefas
- Edição de tarefas existentes
- Marcação de tarefa como concluída ou pendente
- Exclusão de tarefas
- Navegação entre a listagem e o formulário
- Persistência local dos dados com Room
- Atualização reativa da interface por meio de Flow e estado observável

## Tecnologias utilizadas

- **Kotlin**: linguagem principal do projeto
- **Jetpack Compose**: construção declarativa das telas
- **Material Design 3**: componentes visuais da aplicação
- **Room**: persistência dos dados em banco local
- **DAO**: definição das operações de acesso aos dados
- **Repository**: abstração entre a fonte de dados e a camada de apresentação
- **Coroutines e Flow**: execução assíncrona e observação reativa dos dados
- **ViewModel**: gerenciamento do estado e das ações da interface
- **Navigation Compose**: navegação entre as telas
- **Android Studio e Gradle**: desenvolvimento e automação do build

## Arquitetura da aplicação

O projeto separa responsabilidades entre persistência, acesso aos dados, gerenciamento de estado, interface e navegação.

```text
Telas em Jetpack Compose
        |
        v
TarefaViewModel
        |
        v
TarefaRepository
        |
        v
TarefaDao
        |
        v
Banco de dados Room
```

Esse fluxo evita que a interface acesse o banco diretamente. As telas enviam ações para a `TarefaViewModel`, que coordena as operações por meio do `TarefaRepository`. Quando os dados persistidos são alterados, o fluxo observado pela ViewModel atualiza o estado da interface.

## Componentes implementados

### TarefaRepository

A classe `TarefaRepository` centraliza o acesso ao `TarefaDao`. Sua responsabilidade é oferecer à camada de apresentação uma API para consultar e modificar tarefas, sem expor os detalhes da persistência.

O Repository encaminha as operações de:

- observação da lista de tarefas;
- busca de uma tarefa por identificador;
- inserção de uma nova tarefa;
- atualização de uma tarefa existente;
- exclusão de uma tarefa.

Essa abstração reduz o acoplamento entre a ViewModel e o Room e facilita a manutenção da fonte de dados.

### TarefaViewModel

A `TarefaViewModel` funciona como intermediária entre as telas e o Repository. Ela mantém o estado necessário para a interface e disponibiliza operações de inserção, atualização, conclusão e exclusão.

A lista de tarefas é exposta como estado observável. Assim, a `ListaTarefasScreen` é recomposta quando os dados são alterados. As operações que acessam o banco são executadas com coroutines no escopo da ViewModel, evitando bloquear a interface.

A criação por meio de uma `Factory` permite fornecer o `TarefaRepository` à ViewModel, mantendo explícita a configuração de suas dependências.

### ListaTarefasScreen

A `ListaTarefasScreen` apresenta os registros em uma `LazyColumn`, apropriada para renderizar listas de forma eficiente no Jetpack Compose.

A tela observa o estado disponibilizado pela `TarefaViewModel` e transforma cada tarefa em um item visual. As ações do usuário são encaminhadas à ViewModel ou à navegação:

- marcar ou desmarcar uma tarefa atualiza sua situação;
- selecionar a edição abre o formulário com o ID da tarefa;
- excluir remove o registro persistido;
- selecionar a opção de nova tarefa abre o formulário em modo de cadastro.

A tela também possui Preview para facilitar a visualização dos componentes durante o desenvolvimento.

### FormularioTarefaScreen

A `FormularioTarefaScreen` é reutilizada para cadastro e edição. O modo de funcionamento é determinado pelo identificador recebido na navegação:

- **sem um ID válido**: o formulário inicia vazio e salva uma nova tarefa;
- **com o ID de uma tarefa**: os dados existentes são carregados, exibidos nos campos e posteriormente atualizados.

Os valores informados são mantidos como estado da tela. Ao salvar, a operação adequada é disparada pela ViewModel e a navegação retorna à listagem. A tela também permite voltar sem encerrar o aplicativo e contém Preview para apoiar seu desenvolvimento.

### AppNavigation

A navegação é organizada no componente `AppNavigation`, que define um `NavHost` e as rotas da aplicação.

As rotas principais são:

- `lista`: exibe a lista de tarefas;
- `formulario`: abre o formulário para cadastro;
- `formulario/{tarefaId}`: abre o formulário para editar a tarefa correspondente ao ID informado.

A passagem do identificador pela rota permite que o mesmo formulário seja utilizado nos dois cenários. Depois de salvar ou selecionar a ação de voltar, a pilha de navegação retorna à tela anterior.

### MainActivity

A `MainActivity` é o ponto de entrada da aplicação. Ela inicializa as dependências necessárias, cria a `TarefaViewModel` por meio de sua Factory e configura o conteúdo Compose.

Em vez de exibir o conteúdo de exemplo do template do Android Studio, a Activity inicia o `AppNavigation`, que apresenta a rota de listagem e controla o fluxo entre as telas.

## Fluxo de uso

1. O aplicativo inicia na tela de listagem.
2. O usuário seleciona a opção de adicionar uma tarefa.
3. O formulário é aberto em modo de cadastro.
4. Ao salvar, a tarefa é persistida e passa a aparecer na lista.
5. Ao selecionar uma tarefa existente para edição, seu ID é enviado ao formulário.
6. O formulário carrega os dados, permite alterações e atualiza o registro.
7. Na listagem, a tarefa pode ser concluída, desmarcada ou excluída.

## Como executar o projeto

### Pré-requisitos

- Android Studio atualizado
- Android SDK instalado
- JDK compatível com a versão do Gradle utilizada no projeto
- Emulador Android configurado ou dispositivo físico com depuração USB habilitada

### Passos

1. Clone o repositório:

```bash
git clone URL_DO_SEU_REPOSITORIO
```

2. Abra a pasta do projeto no Android Studio.
3. Aguarde a sincronização do Gradle.
4. Selecione um emulador ou dispositivo Android.
5. Execute o módulo `app` pelo botão **Run**.

Também é possível verificar o build pelo terminal, na raiz do projeto:

```bash
# Linux ou macOS
./gradlew build

# Windows
./gradlew.bat build
```

## Evidências

As evidências de execução devem ser armazenadas em `docs/evidencias`. Para que as imagens abaixo apareçam no README, mantenha os nomes sugeridos ou atualize os caminhos conforme os arquivos existentes.

### Tela inicial com a lista de tarefas

![Tela inicial com a lista de tarefas](docs/evidencias/01-lista-tarefas.png)

### Cadastro de uma nova tarefa

![Formulário de cadastro](docs/evidencias/02-cadastro-tarefa.png)

### Tarefa cadastrada na lista

![Tarefa cadastrada](docs/evidencias/03-tarefa-cadastrada.png)

### Edição de uma tarefa existente

![Edição de tarefa](docs/evidencias/04-edicao-tarefa.png)

### Tarefa marcada como concluída

![Tarefa concluída](docs/evidencias/05-tarefa-concluida.png)

### Exclusão de uma tarefa

![Exclusão de tarefa](docs/evidencias/06-exclusao-tarefa.png)

### Navegação entre lista e formulário

![Navegação entre telas](docs/evidencias/07-navegacao.png)

### Build ou execução sem erros

![Build executado sem erros](docs/evidencias/08-build-sucesso.png)

> Substitua as imagens sugeridas pelas capturas reais do projeto antes da entrega. As evidências devem estar legíveis e demonstrar claramente cada funcionalidade.

## Organização sugerida das evidências

```text
docs/
└── evidencias/
    ├── 01-lista-tarefas.png
    ├── 02-cadastro-tarefa.png
    ├── 03-tarefa-cadastrada.png
    ├── 04-edicao-tarefa.png
    ├── 05-tarefa-concluida.png
    ├── 06-exclusao-tarefa.png
    ├── 07-navegacao.png
    └── 08-build-sucesso.png
```

## Referências de implementação

A evolução proposta para a atividade foi organizada nos seguintes commits de referência:

- [`80dcd64`](https://github.com/carreiras/to-do-list/commit/80dcd646f67a40501680526730d8e294269287e0): Repository e ViewModel
- [`3978a81`](https://github.com/carreiras/to-do-list/commit/3978a8153ce2243d209f7616bacbdcd3bef378ea): tela de listagem e ações CRUD
- [`0c6c971`](https://github.com/carreiras/to-do-list/commit/0c6c97158f96d8baad44754b420690e05453a5fa): formulário de cadastro e edição
- [`cc4f148`](https://github.com/carreiras/to-do-list/commit/cc4f148ccefac47b9f39243d0998812887ad193b): rotas e navegação
- [`5788686`](https://github.com/carreiras/to-do-list/commit/57886860775559228effea503ef9a705955d72ad): integração na MainActivity

## Checklist de entrega

- [ ] O projeto compila e executa
- [ ] É possível cadastrar uma tarefa
- [ ] É possível editar uma tarefa
- [ ] É possível concluir e desmarcar uma tarefa
- [ ] É possível excluir uma tarefa
- [ ] A navegação entre as telas funciona
- [ ] O README descreve a arquitetura implementada
- [ ] As evidências reais estão em `docs/evidencias`
- [ ] Arquivos de build, cache e configurações locais não foram versionados
- [ ] O repositório contém a versão final da atividade

## Autor

**Gabriel Shoiti Yoshida**  
FIAP | Android Development
