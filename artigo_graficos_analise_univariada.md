# Gráficos para análise univariada de dados

Uma análise univariada examina **uma variável por vez**. Nesta página você vai ver quando usar um gráfico, qual escolher para cada tipo de dado e o que cada gráfico precisa mostrar. Todos os exemplos usam o dataset **penguins** do Seaborn.

## Quando usar um gráfico

Uma tabela responde perguntas exatas: "qual é a média?", "quantos registros existem?". Um gráfico responde perguntas de **padrão**: "os valores se concentram em algum lugar?", "existe um valor muito fora do normal?", "essa distribuição é simétrica?".

Use gráficos quando:

- a tabela tem muitas linhas e você precisa de uma visão geral rápida;
- quer **comparar distribuições** (por exemplo, o peso de duas espécies);
- procura **outliers** ou valores faltando;
- precisa apresentar o resultado para outras pessoas.

Não use gráficos quando:

- você só quer um número, como a média ou a contagem total;
- os dados são poucos (duas ou três observações não formam um padrão);
- o gráfico só enfeita e não responde nenhuma pergunta.

Gráfico não substitui a estatística descritiva. Ele **complementa**. Um exemplo clássico é o **Quarteto de Anscombe** (Anscombe, 1973): quatro conjuntos de dados com média, variância e correlação praticamente idênticas, mas com formatos muito diferentes quando plotados. Por isso, a regra é: **visualize antes de resumir**.

## Qual gráfico é melhor para cada dado

A escolha depende do **tipo da variável**:

| Tipo da variável | Exemplo nos pinguins | Gráfico recomendado | Por quê |
|---|---|---|---|
| Categórica nominal | `species`, `island`, `sex` | Gráfico de barras | Mostra a frequência de cada categoria sem sugerir uma ordem |
| Categórica ordinal | (ex.: P, M, G) | Gráfico de barras na ordem natural | A ordem das categorias faz parte da informação |
| Numérica discreta | (ex.: número de filhos) | Barras ou linhas | Cada valor é tratado como uma categoria, se houver poucos valores |
| Numérica contínua | `body_mass_g`, `flipper_length_mm` | Histograma, KDE, box-plot, violin, ECDF | Mostram forma, centro, dispersão e outliers |
| Verificação de normalidade | `body_mass_g` | QQ-plot | Compara os quantis da amostra com os de uma normal |

### Variáveis categóricas

Para uma variável nominal, o **gráfico de barras** é a escolha padrão. O gráfico de pizza também existe, mas só funciona bem com **poucas categorias** e diferenças grandes entre elas. Com muitas fatias, fica difícil comparar.

```python
import seaborn as sns
import matplotlib.pyplot as plt

pinguins = sns.load_dataset("penguins")

sns.countplot(data=pinguins, x="species")
plt.title("Quantidade de pinguins por espécie")
plt.show()
```

Para uma variável ordinal, mantenha a ordem natural no eixo X. Ordenar as barras por tamanho apaga a informação de ordem.

### Variáveis numéricas contínuas

**Histograma.** Divide os valores em intervalos (classes) e mostra quantos caem em cada um. É o gráfico mais usado para começar a explorar uma variável numérica. O número de classes influencia a leitura: poucas classes escondem detalhes, muitas criam ruído.

```python
sns.histplot(data=pinguins, x="body_mass_g", bins=20)
plt.title("Distribuição da massa corporal")
plt.show()
```

**Curva de densidade (KDE).** Estima a forma da distribuição com uma curva suave, sem depender de intervalos fixos. É útil para ver se a distribuição tem um ou mais picos.

```python
sns.kdeplot(data=pinguins, x="flipper_length_mm", fill=True)
plt.title("Densidade do comprimento da nadadeira")
plt.show()
```

**Curva gaussiana (normal).** A distribuição normal é uma curva em forma de sino, simétrica em torno da média. Ela é descrita por dois parâmetros: a média (μ), que define o centro, e o desvio-padrão (σ), que define a largura:

$$f(x) = \frac{1}{\sigma\sqrt{2\pi}} \, e^{-\frac{(x-\mu)^2}{2\sigma^2}}$$

Para usar a gaussiana como referência, calcule μ e σ da amostra e desenhe a curva sobre o histograma. Se o histograma e a curva se parecem, a normal é um bom modelo. Se não se parecem, ela não serve para aquela variável.

```python
import numpy as np
from scipy.stats import norm

massa = pinguins["body_mass_g"].dropna()
mu, sigma = massa.mean(), massa.std()

sns.histplot(massa, stat="density", bins=25, color="lightgray")
x = np.linspace(massa.min(), massa.max(), 200)
plt.plot(x, norm.pdf(x, loc=mu, scale=sigma), color="crimson",
         label=f"Normal (μ={mu:.0f}, σ={sigma:.0f})")
plt.title("Massa corporal com curva normal sobreposta")
plt.legend()
plt.show()
```

Repare que a massa corporal **global** não segue bem a normal, porque os dados misturam três espécies com massas médias diferentes. A distribuição fica com mais de um pico. Separar por espécie costuma melhorar o ajuste, e essa é uma pista de que a variável precisa ser analisada por grupo.

**Box-plot.** Resume a distribuição com cinco valores: mediana (linha no meio da caixa), quartil 1 e quartil 3 (bordas da caixa), e os limites inferior e superior (extremidades dos "bigodes"). Pontos fora dos bigodes são candidatos a outliers. É o gráfico ideal para comparar a dispersão de vários grupos lado a lado.

```python
sns.boxplot(data=pinguins, x="bill_depth_mm")
plt.title("Profundidade do bico")
plt.show()
```

**Violin plot.** É parecido com o box-plot, mas mostra a **densidade** da distribuição no formato de um violino. Ajuda a ver se há mais de um pico, algo que o box-plot esconde.

```python
sns.violinplot(data=pinguins, x="bill_depth_mm")
plt.title("Distribuição da profundidade do bico")
plt.show()
```

**ECDF (função de distribuição acumulada empírica).** Mostra, para cada valor, a proporção de observações menores ou iguais a ele. Não depende de intervalos nem de suavização, e é ótima para comparar grupos e ler percentis.

```python
sns.ecdfplot(data=pinguins, x="flipper_length_mm", hue="species")
plt.title("ECDF do comprimento da nadadeira por espécie")
plt.show()
```

**QQ-plot.** Compara os quantis da amostra com os quantis de uma distribuição normal. Se os pontos seguem uma reta, a amostra é aproximadamente normal. Desvios nas pontas indicam caudas mais pesadas ou outliers.

```python
from scipy import stats

stats.probplot(pinguins["flipper_length_mm"].dropna(), dist="norm", plot=plt)
plt.title("QQ-plot do comprimento da nadadeira")
plt.show()
```

### Variáveis discretas

Para uma variável discreta com poucos valores distintos, como número de filhos, o **gráfico de barras** ou o **gráfico de linhas verticais** funcionam bem. Quando a variável tem muitos valores diferentes, trate-a como contínua e use histograma ou box-plot. Frequências absolutas, relativas e acumuladas podem ser mostradas nos mesmos gráficos.

### Outros gráficos úteis

- **Ramo-e-folhas**: mostra os valores individualmente agrupados por dezena. Bom para amostras pequenas, mas quase não é usado com ferramentas modernas.
- **Gráfico de Pareto**: barras ordenadas em ordem decrescente com uma linha de porcentagem acumulada. Útil para identificar as poucas categorias que concentram a maior parte dos casos.

## O que o gráfico deve informar

Um bom gráfico univariado responde a algumas perguntas básicas. Antes de publicar, confira se o seu consegue responder a todas:

- **Forma**: a distribuição é simétrica, assimétrica, com um pico ou com vários picos?
- **Centro**: onde estão a média e a mediana?
- **Dispersão**: quão espalhados estão os valores?
- **Outliers e valores faltando**: há pontos muito distantes ou dados ausentes que mudam a leitura?
- **Tamanho da amostra**: quantas observações estão representadas?
- **Escala e unidade**: os eixos têm nome e unidade (mm, g, contagem)?

Além disso, o **título deve afirmar a mensagem**, e não só dizer o nome da variável:

- Título fraco: "Massa corporal"
- Título forte: "Gentoo tem massa corporal maior que Adelie e Chinstrap"

Lembre que um mesmo gráfico pode esconder problemas. Um histograma com poucas classes pode esconder um segundo pico, e um box-plot pode esconder a forma da distribuição. Por isso, quando possível, combine dois gráficos (por exemplo, box-plot e violin plot).

## Como esses gráficos são usados no mercado

Os gráficos univariados aparecem em praticamente todo trabalho com dados. Três contextos se destacam.

### Ciência e análise de dados

Antes de responder uma pergunta de negócio ou treinar um modelo, o analista precisa entender os dados. Um estudo com 18 analistas de universidades e da indústria mostrou que o uso mais citado da EDA é **checar a qualidade dos dados** antes da análise: todos os entrevistados relataram essa prática. O histograma e o gráfico de contagem foram os gráficos univariados mais citados, por 15 dos 18 analistas. O box-plot apareceu com menos frequência, citado explicitamente por apenas dois. Como a amostra foi recrutada por contatos, o estudo mostra a variedade de práticas, não a frequência exata de cada uma no mercado ([Goals, Process, and Challenges of Exploratory Data Analysis: An Interview Study](https://arxiv.org/pdf/1911.00568)).

Na prática, isso significa que o histograma é o primeiro gráfico que o analista desenha para uma variável nova, e o box-plot é usado para encontrar outliers antes de decidir como tratá-los.

### Business intelligence e relatórios

Em relatórios de negócio, o **gráfico de barras** mostra a frequência ou o total de cada categoria (produtos, regiões, clientes). O **gráfico de pizza** aparece quando o objetivo é mostrar a **composição** de um todo, como a participação de cada área em um orçamento. Um exemplo citado em uma fonte de visualização de dados é a comparação de áreas de crescimento, como lucro e gastos ([Comet: Different Plots Used in EDA](https://www.comet.com/site/blog/different-plots-used-in-exploratory-data-analysis-eda/)). Por isso, a pizza deve ser usada apenas com poucas categorias, como vimos na seção de escolha de gráficos.

### Controle de qualidade e processos industriais

Na indústria, a análise univariada é a base de ferramentas clássicas de qualidade:

- **Histograma**: mostra a distribuição de uma medida do processo (por exemplo, o diâmetro de uma peça) e permite verificar se os dados são simétricos e se há valores atípicos.
- **Gráfico de Pareto**: ordena as causas de defeitos da mais frequente para a menos frequente. A ideia é concentrar esforços nas poucas causas que geram a maior parte dos problemas, a regra dos 20% das causas que geram 80% dos problemas.
- **Gráfico de controle**: monitora uma característica ao longo do tempo, com limites calculados a partir dos próprios dados do processo. Pontos fora dos limites indicam causas irregulares que devem ser investigadas ([6sigma.us: Six Sigma Tools](https://www.6sigma.us/?p=588)).

Essas ferramentas aparecem em processos de manufatura e em empresas de serviços que medem a qualidade de suas operações.

### Resumo

| Contexto | Gráficos mais usados | Objetivo |
|---|---|---|
| Ciência e análise de dados | Histograma, gráfico de contagem, box-plot | Checar a qualidade dos dados e encontrar outliers antes da análise |
| Business intelligence | Barras, pizza (poucas categorias) | Comparar categorias e mostrar a composição de um total |
| Controle de qualidade | Histograma, Pareto, gráfico de controle | Identificar causas de defeitos e monitorar a estabilidade do processo |

Em todos os casos, o gráfico serve para tomar uma decisão. Por isso, o que você aprende sobre forma, centro, dispersão e outliers vale nas três áreas.

## Exercícios

Use o dataset `penguins` em todos os exercícios.

### Exercício 1: Contagem por espécie

Quantos pinguins existem em cada espécie? Qual gráfico é o mais adequado para mostrar essa informação?

<details>
<summary>Ver resposta</summary>

```python
import seaborn as sns
import matplotlib.pyplot as plt

pinguins = sns.load_dataset("penguins")
print(pinguins["species"].value_counts())
# Adelie: 152, Gentoo: 124, Chinstrap: 68

sns.countplot(data=pinguins, x="species")
plt.show()
```

A variável `species` é categórica nominal, então o gráfico de barras é o mais adequado.

</details>

### Exercício 2: Valores faltando em sexo

Quantos registros de `sex` estão faltando? Como isso aparece no gráfico se você não tratar os valores ausentes?

<details>
<summary>Ver resposta</summary>

```python
print(pinguins["sex"].value_counts(dropna=False))
# Inclui a contagem de NaN: 11 registros sem sexo informado.

sns.countplot(data=pinguins, x="sex")   # não mostra os NaN
plt.show()
```

Por padrão, `value_counts()` e `countplot()` ignoram valores faltando. Para mostrar o total de registros sem informação, use `dropna=False` na contagem e deixe isso explícito no texto do gráfico.

</details>

### Exercício 3: Distribuição da massa com normal

Desenhe o histograma de `body_mass_g` com a curva normal sobreposta. A normal descreve bem essa variável?

<details>
<summary>Ver resposta</summary>

O código está na seção sobre a curva gaussiana, acima.

A normal **não** descreve bem a massa global, porque os dados misturam três espécies com massas médias diferentes. Separe por espécie e repita o gráfico para comparar:

```python
for especie in pinguins["species"].unique():
    grupo = pinguins.loc[pinguins["species"] == especie, "body_mass_g"].dropna()
    sns.kdeplot(grupo, label=especie, fill=True, alpha=0.3)
plt.legend()
plt.title("Massa corporal por espécie")
plt.show()
```

A conclusão é que a análise deve considerar a estrutura dos grupos antes de assumir normalidade.

</details>

### Exercício 4: Outliers com box-plot

Crie um box-plot de `bill_depth_mm` e identifique os pontos fora dos bigodes. Confirme a contagem com a regra do intervalo interquartil (IQR).

<details>
<summary>Ver resposta</summary>

```python
import seaborn as sns
import matplotlib.pyplot as plt

sns.boxplot(data=pinguins, x="bill_depth_mm")
plt.show()

q1 = pinguins["bill_depth_mm"].quantile(0.25)
q3 = pinguins["bill_depth_mm"].quantile(0.75)
iqr = q3 - q1
limite_inf = q1 - 1.5 * iqr
limite_sup = q3 + 1.5 * iqr

outliers = pinguins[(pinguins["bill_depth_mm"] < limite_inf) |
                    (pinguins["bill_depth_mm"] > limite_sup)]
print(f"Outliers: {len(outliers)}")
print(outliers[["species", "bill_depth_mm"]])
```

Os pontos fora dos bigodes são exatamente os valores que a regra do IQR classifica como outliers. Rode o código para ver quais são e de qual espécie vêm.

</details>

### Exercício 5: Desafio com QQ-plot

Faça um QQ-plot de `flipper_length_mm` e um histograma da mesma variável. Os dois gráficos concordam sobre a normalidade?

<details>
<summary>Ver resposta</summary>

```python
from scipy import stats
import seaborn as sns
import matplotlib.pyplot as plt

dados = pinguins["flipper_length_mm"].dropna()

fig, eixos = plt.subplots(1, 2, figsize=(10, 4))
sns.histplot(dados, kde=True, ax=eixos[0])
eixos[0].set_title("Histograma")
stats.probplot(dados, dist="norm", plot=eixos[1])
eixos[1].set_title("QQ-plot")
plt.show()
```

O histograma mostra a forma geral, e o QQ-plot mostra os desvios nas pontas com mais precisão. Como a variável combina três espécies, o resultado costuma indicar que a normalidade não é uma boa suposição para o conjunto todo. Discuta com a turma se os dois gráficos concordam e por quê.

</details>

## Referências

- ANSCOMBE, F. J. Graphs in statistical analysis. *The American Statistician*, v. 27, n. 1, p. 17–21, 1973.
- FERREIRA, R. G. C. *Preparação e análise exploratória de dados* (material didático). Sagah Educação. Capítulo sobre análise univariada de dados.
- IBM. *O que é gerenciamento de dados?* Disponível em: https://www.ibm.com/br-pt/think/topics/data-management. Acesso em: 28 out. 2026.
- REIS, J.; HOUSLEY, M. *Fundamentals of Data Engineering*. O'Reilly, 2022. Contexto sobre o ciclo de vida dos dados.
- THE PYTHON GRAPH GALLERY. Disponível em: https://python-graph-gallery.com/.
- SEABORN. Tutorial oficial. Disponível em: https://seaborn.pydata.org/tutorial.html.
- Dataset Palmer Penguins: https://allisonhorst.github.io/palmerpenguins/
- *Goals, Process, and Challenges of Exploratory Data Analysis: An Interview Study*. arXiv:1911.00568, 2019. Disponível em: https://arxiv.org/pdf/1911.00568.
- COMET. *Different Plots Used in Exploratory Data Analysis (EDA)*. Disponível em: https://www.comet.com/site/blog/different-plots-used-in-exploratory-data-analysis-eda/.
- 6SIGMA. *Six Sigma Tools*. Disponível em: https://www.6sigma.us/?p=588.
