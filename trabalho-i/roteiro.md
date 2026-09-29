# Roteiro de Apresentação: Estilo Arquitetural Microkernel
**Disciplina:** Engenharia de Software I  
**Duração Total:** 30 minutos (aprox. 7 min 30 seg por pessoa)  

---

### PESSOA 1
**Foco:** VS Code, Conceito do Estilo e ADRs.

**[Slide 1 e 2: Capa e Introdução]** *(~2 min)*
- "Olá a todos. Nós somos a Equipe 3, composta por mim (Felipe), Jaedson, Lucas e Nadson. Nosso trabalho aborda o estilo arquitetural Microkernel, amplamente conhecido como 'Arquitetura Baseada em Plugins'."
- "Para aplicar a teoria do livro-texto, nós dissecamos a documentação e o código de dois colossos do mercado Open Source: o Visual Studio Code (da Microsoft) e o OBS Studio."
- "O estilo Microkernel brilha quando o software precisa crescer infinitamente, sem se transformar no temido 'Big Ball of Mud'. O segredo é ter um núcleo enxuto e plugar as funcionalidades ao redor dele."

**[Slide 3: VS Code - Características Arquiteturais]** *(~2,5 min)*
- "Começando pelo VS Code. Filtramos dezenas de características para encontrar o Top 4 que define o sistema."
- "A primeira é a **Extensibilidade**: o coração do projeto. O suporte a Git ou Markdown não faz parte do núcleo, eles rodam como plugins através das mesmas APIs abertas para o público."
- "A segunda é a **Tolerância a Falhas**, porque o núcleo precisa se proteger ativamente de plugins mal feitos para não travar o editor."
- "A terceira é o **Desempenho**: a digitação precisa ser sempre fluida."
- "E a quarta é a **Interoperabilidade e Reutilização**, alcançada pelo uso de protocolos de linguagem abertos, que permitem reutilizar a inteligência de código do VS Code em outros editores."

**[Slide 4: VS Code - Decisões (ADRs)]** *(~3 min)*
- "Como eles construíram isso? Identificamos 4 ADRs cruciais."
- "A *ADR-001* consolida a escolha do Microkernel para manter o vocabulário de componentes limpo."
- "A *ADR-002* é o trunfo deles: o **Extension Host**. Eles rodam os plugins em um processo isolado da interface gráfica do usuário. Se o plugin trava, o processo morre silenciosamente, mas o editor continua funcionando."
- "A *ADR-003* adotou o Language Server Protocol (LSP), delegando a checagem de erros no código para servidores externos."
- "Por fim, a *ADR-004* utilizou o framework Electron, que garantiu uma portabilidade extrema. Agora passo a palavra para o Jaedson."

---

### PESSOA 2
**Foco:** VS Code, Diagramas e Táticas.

**[Slide 5: VS Code - Componentes Candidatos]** *(~2 min)*
- "Dando sequência, mapeamos os componentes lógicos em três blocos principais."
- "O **Core System** abriga a Interface (Workbench) e o editor de texto Monaco."
- "A **Infraestrutura de Plugins** é a ponte segura. O *Extension Service* monitora e joga os plugins no processo isolado que o Felipe citou."
- "E na base temos os **Plugins** em si: temas, debuggers e servidores LSP."

**[Slide 6: VS Code - Diagrama de Componentes]** *(~2,5 min)*
- "Aqui isolamos os 30% mais críticos em UML. Notem que os plugins de terceiros não conversam diretamente com a Interface do Usuário."
- "O *Plugin Manager* consome os contratos rígidos da *Extension API* e atua como uma barreira. É essa arquitetura assíncrona que impede que a tela congele."

**[Slide 7: VS Code - Diagrama de Módulos]** *(~1,5 min)*
- "No Diagrama de Módulos vemos a organização física do código-fonte. O repositório da Microsoft respeita perfeitamente o padrão: o que é interface fica em uma pasta, o núcleo em outra, e a API atua como camada de inclusão entre eles."

**[Slide 8: VS Code - Táticas]** *(~1,5 min)*
- "Finalizando o VS Code, as táticas que conectam o problema à solução foram:"
- "A Tolerância a falhas usou a tática de **Isolamento de Processos**."
- "O Desempenho utilizou a tática de **Mensagens Assíncronas**, libertando a thread principal."
- "A Extensibilidade usou o **Particionamento de Domínio**. O Lucas vai iniciar agora o cenário do OBS Studio."

---

### PESSOA 3
**Foco:** OBS Studio, Conceito e ADRs.

**[Slide 9: OBS Studio - Introdução e Características]** *(~2,5 min)*
- "Obrigado, Jaedson. Nosso segundo sistema é o OBS Studio. Diferente do VS Code, o OBS processa áudio e vídeo de múltiplas fontes em tempo real. Uma falha de milissegundos significa a perda de quadros na gravação."
- "Nosso Top 4 de características reflete essa urgência:"
- "1. **Extensibilidade:** O OBS precisa suportar infinitas placas de captura e sites de streaming sem precisar alterar seu núcleo."
- "2. **Desempenho:** A latência de compressão de vídeo precisa ser praticamente zero."
- "3. **Confiabilidade e Tolerância a Falhas:** Uma queda na internet não pode congelar toda a interface de transmissão."
- "4. **Implantabilidade e Portabilidade:** O código deve rodar suavemente em Windows, macOS e Linux de forma padronizada."

**[Slide 10: OBS Studio - ADRs]** *(~5 min)*
- "Para sustentar isso, levantamos 4 decisões técnicas (ADRs) brutais implementadas por eles:"
- "A *ADR-IMP-001*: O Microkernel (o `libobs`) é inteiramente escrito em C. Isso fugiu das abstrações de alto nível e garantiu velocidade máxima, além de criar uma API C pura que qualquer plugin consegue ler."
- "A *ADR-IMP-002*: O frontend visual foi construído em Qt (C++), totalmente desacoplado. A interface não possui nenhuma regra de processamento de vídeo."
- "A *ADR-IMP-003*: O Compartilhamento Zero-Copy. É aqui que está a mágica: os plugins do OBS não copiam os pixels do vídeo na memória RAM. Eles passam apenas referências pela placa de vídeo (GPU), livrando a CPU e mantendo o Desempenho."
- "E a *ADR-IMP-004*: O carregamento dinâmico permite injetar as bibliotecas de plugins do seu sistema operacional no exato momento que o software abre. Passo agora ao Nadson."

---

### PESSOA 4
**Foco:** OBS Studio, Diagramas, Táticas e Conclusão.

**[Slide 11: OBS Studio - Componentes Candidatos]** *(~2 min)*
- "Para mapear os componentes do OBS, temos o **Microkernel (libobs)** no centro de tudo, acompanhado do **obs-module**, que contém as regras (contratos) que os plugins devem seguir."
- "Na periferia, temos a Interface do Usuário e as três categorias vitais de plugins: **Source Plugins** (que extraem imagem/webcam), **Encoder Plugins** (que fazem a compressão pesada) e **Output Plugins** (que transmitem para a internet)."

**[Slide 12 e 13: OBS Studio - Diagramas de Componentes e Módulos]**
- "*(Slide 12)* O diagrama de componentes mostra exatamente o pipeline da arquitetura. O `libobs` tem relação de agregação e gerencia os três plugins principais usando os contratos."
- "*(Slide 13)* Já o diagrama de módulos expõe as pastas físicas do repositório no GitHub: o frontend totalmente separado do libobs, a pasta isolada de plugins e um diretório focado apenas nas dependências externas (`deps`)."

**[Slide 14: OBS Studio - Táticas]** *(~1,5 min)*
- "As táticas utilizadas amarram as características com as soluções do código."
- "O *Desempenho* foi alcançado pela **Aceleração de Hardware** e texturas na GPU."
- "A *Confiabilidade e Tolerância a falhas* aplicou o **Isolamento de falhas com threads assíncronas**. Se o módulo de internet cai, ele tenta se reconectar numa thread paralela, sem nunca paralisar o `libobs`."
- "A *Extensibilidade* se dá pela padronização rigorosa da API."

**[Slide 15: Conclusão]** *(~1,5 min)*
- "Concluímos que a adoção do estilo Microkernel resolveu desafios de escala imensos em dois cenários bem diferentes."
- "No VS Code, o foco do Microkernel foi criar um isolamento protetor, para a interface do usuário não sofrer com códigos de terceiros."
- "No OBS, o foco foi estruturar um pipeline de altíssimo desempenho integrado ao hardware do computador."
- "Em ambos os casos, a grande lição é que o uso de contratos claros e APIs bem definidas é a única forma de impedir a degradação de um software longo e complexo."
- "Agradecemos muito a atenção de todos!"