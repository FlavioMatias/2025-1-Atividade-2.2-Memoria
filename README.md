# S.O. 2025.1 - Atividade 2.02 - Gestão de memória

## Informações gerais

- **Objetivo do repositório**: Repositório para atividade avaliativa dos alunos
- **Assunto**: Gestão de memória
- **Público alvo**: alunos da disciplina de SO (Sistemas Operacionais) do curso de TADS (Superior em Tecnologia em Análise e Desenvolvimento de Sistemas) no CNAT-IFRN (Instituto Federal de Educação, Ciência e Tecnologia do Rio Grande do Norte - Campus Natal-Central).
- disciplina: **SO** [Sistemas Operacionais](https://github.com/sistemas-operacionais/)
- professor: [Leonardo A. Minora](https://github.com/leonardo-minora)
- Repositótio do aluno: [Flavio S. Matias](https://github.com/FlavioMatias)

## Tarefas do aluno
1. Fork desse repositório e atualizar a linha 10 com o nome e link do github
2. Ler a descrição da atividade
3. Montar a resposta no final deste arqivo, no tópico **Resposta**

---

## 1. Descrição da atividade
### 1.1. Objetivo
Praticar os conceitos de alocação de memória (best-fit), memória virtual e desfragmentação em um sistema com memória limitada.

---

### 1.2. Contexto
Um computador possui apenas **64 KB de RAM** e um **disco rígido para memória virtual**. O sistema operacional deve gerenciar 5 processos com tamanhos diferentes, cuja soma ultrapassa a capacidade da RAM.

#### 1.2.1. Processos iniciais

| Processo | Tamanho (KB) |
|----------|-------------|
| P1       | 20          |
| P2       | 15          |
| P3       | 25          |
| P4       | 10          |
| P5       | 18          |
| **Total**| **88 KB**   |

- **Memória RAM**: 64 KB (contígua, inicialmente vazia).  
- **Memória Virtual (Disco)**: Espaço ilimitado para paginação.

#### 1.2.2. Alocação Inicial com Best-Fit
Os alunos devem simular a alocação dos processos na RAM usando o algoritmo **best-fit**.  
- A memória RAM será representada como um bloco contíguo (ex: `[0KB - 64KB]`).  
- Devem alocar os processos nos menores espaços livres que atendam ao seu tamanho.  

**Alocação inicial**:  
1. P1 (20 KB) → Ocupa [0-20].  
2. P2 (15 KB) → Ocupa [20-15].  
3. _continuar a partir daqui_

#### 1.2.3. Simular Memória Virtual (Paginação)
- Os processos não alocados na RAM devem ser "paginados" no disco.  
- Criar uma tabela de páginas indicando quais partes estão na RAM e quais estão no disco.  

#### 1.2.4. Desfragmentação da RAM
- Desfragmentar a RAM para liberar espaço contíguo.
- Após desfragmentação (compactação), verificar quais processos podem ser alocado.  

### 1.3. Questões para Reflexão
1. Best-fit foi mais eficiente que first-fit ou worst-fit neste cenário?  
2. Como a memória virtual evitou um deadlock?  
3. Qual o impacto da desfragmentação no desempenho do sistema?  

---

## Resposta

### 1. Alocação Inicial com Best-Fit

No algoritmo **best-fit**, a cada processo buscamos o menor “buraco” disponível capaz de acomodá-lo. Partindo de 64 KB de RAM contígua e os processos:

| Processo | Tamanho (KB) |
|:--------:|-------------:|
| P1       |           20 |
| P2       |           15 |
| P3       |           25 |
| P4       |           10 |
| P5       |           18 |
| **Total**|       **88** |

a sequência de alocação é:

1. **P1 (20 KB)** → único buraco de 64 KB → aloca em `[0 – 20)`, sobra **44 KB**  
2. **P2 (15 KB)** → menor buraco restante é de 44 KB → aloca em `[20 – 35)`, sobra **29 KB**  
3. **P3 (25 KB)** → menor buraco restante é de 29 KB → aloca em `[35 – 60)`, sobra **4 KB**  
4. **P4 (10 KB)** → buraco livre de 4 KB → **não cabe** → swap  
5. **P5 (18 KB)** → buraco livre de 4 KB → **não cabe** → swap  

| Processo | Tamanho (KB) | Bloco Alocado    | Fragmento Restante |
|:--------:|-------------:|------------------|--------------------|
| **P1**   |           20 | `[0 – 20]`       | 44 KB              |
| **P2**   |           15 | `[20 – 35]`      | 29 KB              |
| **P3**   |           25 | `[35 – 60]`      | 4 KB               |
| **Total**|       **60** |                  | **4 KB**           |

– **P4 (10 KB)** e **P5 (18 KB)** vão para **memória virtual (swap)**.

**Situação final da RAM:**  \[P1 0–20] | \[P2 20–35] | \[P3 35–60] | \[Livre 60–64] = 4 KB

**Memória virtual (disco):** P4 (10 KB), P5 (18 KB)  

#### 2. Simular Memória Virtual (Paginação)

A paginação será feita identificando quais processos ou partes deles residem na RAM e quais são empurrados para o disco (swap). Com a RAM ocupada por 60 KB (P1, P2 e P3), sobra apenas um fragmento de 4 KB, insuficiente para P4 e P5, que vão integralmente para o disco:

| Processo | Tamanho (KB) | Onde estão       |
|:--------:|-------------:|:-----------------|
| P1       |           20 | RAM              |
| P2       |           15 | RAM              |
| P3       |           25 | RAM              |
| P4       |           10 | Disco (swap)     |
| P5       |           18 | Disco (swap)     |

Como a RAM não comporta P4 nem P5, o sistema operacional mapeia essas páginas diretamente no disco, garantindo continuidade sem comprometer o espaço físico.  

#### 3 Desfragmentação da RAM

- **Antes**:  \[P1 0–20] | \[P2 20–35] | \[P3 35–60] | \[Livre 60–64] = 4 KB

- **Ação**: 
1. O SO identifica que não há espaços internos fragmentados — todo o espaço livre está no final.  
2. Mesmo assim, executa a rotina de compactação: percorre a memória do início ao fim, deslocando cada processo para o endereço imediatamente seguinte ao bloco anterior, garantindo que não restem “buracos”.  
3. Ao concluir, consolida o espaço livre remanescente em um único bloco contínuo no final da RAM.

- **Depois**:  \[P1 0–20] | \[P2 20–35] | \[P3 35–60] | \[Livre 60–64] = 4 KB

- **Resultado**: Ainda há apenas 4 KB livres — não cabe P4 (10 KB) nem P5 (18 KB).  



 ### 4. Questões para Reflexão

1. **Best-Fit vs. First-Fit e Worst-Fit**  
   - O **best-fit** alocou cada processo no menor espaço disponível, reduzindo ao máximo o desperdício interno de memória (fragmentação).  
   - Em contrapartida, o **first-fit** teria alocado imediatamente no primeiro buraco suficientemente grande, mas poderia deixar blocos maiores inutilizados no meio da RAM, gerando fragmentação externa.  
   - Já o **worst-fit** tenderia a usar os maiores blocos, desperdiçando capacidade crítica em cenários de RAM limitada — exatamente o oposto do nosso objetivo de otimização de espaço.  
   - **Conclusão:** neste cenário, o best-fit se mostrou o mais eficiente em termos de aproveitamento de capacidade e minimização de espaço residual.

2. **Como a memória virtual evitou um deadlock**  
   - Sem swap, a tentativa de alocar um processo maior que o espaço físico livre acarretaria num bloqueio permanente de requisição de memória (deadlock por escassez de recurso).  
   - Ao “descarregar” P4 e P5 para o disco, o sistema operacional liberou o throughput de alocação, mantendo o _pipeline_ de execução ativo e prevenindo qualquer situação de espera circular por memória.  
   - Em outras palavras, a memória virtual quebrou o ciclo de dependência pura de RAM, garantindo a continuidade operacional mesmo sob pressão de demanda maior que a oferta física.

3. **Impacto da desfragmentação no desempenho do sistema**  
   - A compactação de blocos consolida todo o espaço livre em um único segmento contíguo, eliminando a fragmentação externa e permitindo a alocação de processos de maior porte.  
   - Entretanto, esse ganho de eficiência espacial vem acompanhado de **overhead**: movimentação de páginas na RAM, consumo adicional de CPU e I/O no subsistema de memória, e potencial interrupção temporária dos processos.  
   - Logo, a desfragmentação deve ser orquestrada de forma estratégica, balanceando o custo de _downtime_ e uso de recursos com a necessidade de manter baixos índices de fragmentação e alta taxa de alocação bem-sucedida.  