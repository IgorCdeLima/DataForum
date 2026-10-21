# DataForum

**Análise de Dados · Aprenda · Compartilhe · Evolua**

O DataForum é um fórum de artigos sobre análise de dados, no formato de um blog pessoal. É onde
reúno anotações, resumos de conteúdos que estudo e artigos que escrevo sobre a área de dados,
com Python como linguagem principal.

O projeto é aberto: qualquer pessoa interessada em dados pode ler, usar os exemplos e os
exercícios, e acompanhar a evolução do conteúdo.

## O que você encontra

- **Tutoriais e Guias:** artigos que explicam conceitos de análise de dados, passo a passo.
- **Projetos:** estudos e projetos práticos de análise de dados.
- **Recursos:** uma coleção organizada de livros, documentações (Python, pandas, NumPy,
  Matplotlib, Seaborn, Streamlit) e sites para encontrar artigos científicos, como IEEE Xplore,
  arXiv e SciELO.

## Como os artigos são organizados

Cada artigo é curto e responde sempre às mesmas perguntas:

1. **O que é?**
2. **Para que serve?**
3. **Como é feito?** Com exemplos de código em Python.
4. **Qual a utilidade no mercado de trabalho?**
5. **Exercícios práticos**, com respostas.
6. **Referências** para continuar estudando.

Os datasets usados nos exemplos e exercícios são disponibilizados pela própria plataforma ou
por link do Google Drive, indicado em cada artigo.

## Artigos publicados

| Artigo | Categoria |
|---|---|
| [Análise de dados no mercado de TI](pages/analise_dados_mercado_ti.html) | Tutoriais e Guias |

## Tecnologias

O site é propositalmente simples: apenas **HTML e CSS**, sem JavaScript e sem etapa de build.

- Fontes: [Pixelify Sans](https://fonts.google.com/specimen/Pixelify+Sans) na identidade visual
  e [Adamina](https://fonts.google.com/specimen/Adamina) nos textos.
- Ilustrações em pixel art feitas em SVG.

## Estrutura do projeto

```
.
├── index.html                  # página inicial
├── css/style.css               # todo o estilo do site
├── img/                        # ilustrações em pixel art
├── design/                     # layout de referência da página inicial (SVG)
└── pages/
    ├── tutoriais.html          # categoria: Tutoriais e Guias
    ├── projetos.html           # categoria: Projetos
    ├── recursos.html           # livros, documentações e artigos científicos
    ├── model_article.html      # modelo usado para escrever novos artigos
    └── analise_dados_mercado_ti.html
```
