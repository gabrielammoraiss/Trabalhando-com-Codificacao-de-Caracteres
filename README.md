# Tratamento de Codificação de Caracteres e Preparação de Dados

## Sobre o projeto

Este projeto demonstra como identificar e corrigir problemas de **codificação de caracteres** durante a leitura de arquivos CSV com Python e Pandas.

O estudo foi realizado em sala de aula, com a orientação da professora **Talita Berbel**, como parte das atividades acadêmicas do curso de **Ciência de Dados para Negócios da FATEC Votorantim**.

O notebook apresenta um fluxo prático para diagnosticar erros de leitura, testar diferentes codificações, utilizar o pacote `chardet` para estimar a codificação correta, tratar valores ausentes, padronizar categorias e salvar uma nova versão do dataset em UTF-8.

A aplicação principal utiliza o dataset público **Fatal Police Shootings in the US**, compilado pelo *The Washington Post*, para demonstrar as etapas de preparação e análise exploratória de dados.

> Este projeto tem finalidade educacional e foi desenvolvido durante uma atividade acadêmica orientada pela professora Talita Berbel. A análise descreve procedimentos técnicos de preparação de dados e não pretende estabelecer relações causais sobre os eventos registrados.

## Objetivos

- Compreender a diferença entre strings, bytes e codificações de caracteres;
- Demonstrar problemas causados por incompatibilidade entre a codificação do arquivo e a codificação utilizada na leitura;
- Identificar a codificação provável de um arquivo utilizando `chardet`;
- Carregar corretamente arquivos CSV com codificação diferente de UTF-8;
- Identificar e tratar valores ausentes;
- Padronizar categorias para facilitar a análise;
- Gerar uma versão final do dataset codificada em UTF-8;
- Realizar uma análise exploratória inicial dos dados.

## Tecnologias utilizadas

- Python 3;
- Jupyter Notebook;
- Google Colab;
- Pandas;
- NumPy;
- Matplotlib;
- Seaborn;
- Chardet.

## Dataset principal

O projeto utiliza o dataset **Fatal Police Shootings in the US**, que reúne registros de mortes causadas por policiais nos Estados Unidos.

Entre as variáveis disponíveis estão:

- Identificador do registro;
- Nome da vítima;
- Data da ocorrência;
- Forma da morte;
- Situação relacionada a armas;
- Idade;
- Gênero;
- Raça;
- Cidade e estado;
- Sinais de doença mental;
- Nível de ameaça;
- Tentativa de fuga;
- Uso de câmera corporal.

O dataset utilizado no notebook contém **2.535 registros e 14 colunas** antes do tratamento.

## Estrutura do projeto

```text
Projeto4/
│
├── Projeto4.ipynb
├── PoliceKillingsUS.csv
├── PoliceKillingsUS-utf8.csv
└── README.md
```

## Etapas desenvolvidas

### 1. Conceitos de codificação

O notebook apresenta a diferença entre texto e bytes por meio de exemplos com caracteres acentuados e o símbolo do euro.

Também demonstra como uma codificação incorreta pode gerar caracteres ilegíveis, conhecidos como *mojibake*, ou erros como `UnicodeDecodeError`.

### 2. Identificação da codificação com Chardet

A análise mostra que a quantidade de bytes examinada pode influenciar a estimativa da codificação.

No dataset de projetos do Kickstarter, a leitura inicial com UTF-8 gera erro. A análise dos primeiros 10 mil bytes indica `Windows-1252` com 73% de confiança, permitindo a leitura correta do arquivo.

No dataset de mortes por policiais, a análise de uma amostra menor indica `ASCII` com 100% de confiança. Entretanto, essa estimativa não representa o arquivo inteiro e a tentativa de leitura gera um novo erro.

Após aumentar a amostra para 100 mil bytes, o `chardet` identifica `Windows-1252` com 73% de confiança, possibilitando o carregamento correto do dataset.

### 3. Leitura do arquivo

A leitura é realizada com `pandas.read_csv()`, informando explicitamente a codificação identificada:

```python
import pandas as pd

lista_labels_valores_ausentes = ["n/a", "na", "undefined"]

dataset = pd.read_csv(
    "PoliceKillingsUS.csv",
    na_values=lista_labels_valores_ausentes,
    encoding="Windows-1252"
)
```

### 4. Tratamento de valores ausentes

O notebook calcula o percentual de dados ausentes antes e depois do tratamento.

- Percentual inicial de células ausentes: **0,97%**;
- Percentual após o tratamento: **0,00%**.

As principais decisões adotadas foram:

| Coluna | Tratamento aplicado |
|---|---|
| `armed` | Substituição de valores ausentes por `undetermined` |
| `age` | Preenchimento pela mediana |
| `race` | Substituição por `Unknown` |
| `gender` | Substituição por `Unknown` |
| `flee` | Substituição por `Unknown` |

O preenchimento da idade pela mediana reduz a influência de valores extremos. Para variáveis categóricas, a utilização de categorias como `Unknown` ou `undetermined` preserva os registros sem criar uma informação que não estava disponível na fonte.

### 5. Padronização das categorias

O projeto também converte códigos abreviados de raça em descrições mais legíveis:

```python
race_mapping = {
    "W": "White",
    "B": "Black",
    "H": "Hispanic",
    "A": "Asian",
    "N": "Native American",
    "O": "Other",
    "Unknown": "Unknown"
}

dataset["race"] = dataset["race"].map(race_mapping)
```

Além disso, os valores da coluna `threat_level` são padronizados para facilitar agrupamentos e visualizações.

### 6. Análise exploratória

O notebook propõe visualizações para investigar:

- Distribuição das vítimas por raça e gênero;
- Percentual de registros com sinais de doença mental;
- Tipos de armas mais frequentes;
- Distribuição geográfica dos registros por estado;
- Relação entre nível de ameaça e sinais de doença mental.

Essas análises devem ser interpretadas como associações descritivas da base. Elas não permitem, isoladamente, concluir relações de causa e efeito.

### 7. Conversão para UTF-8

Após a limpeza, o dataset é salvo em UTF-8, formato amplamente utilizado e recomendado para facilitar a integração com outras ferramentas:

```python
dataset.to_csv(
    "PoliceKillingsUS-utf8.csv",
    index=False,
    encoding="utf-8"
)
```

## Principais aprendizados

Este projeto demonstra competências importantes para a rotina de análise de dados:

- Diagnóstico de erros de leitura em arquivos;
- Conhecimento prático de UTF-8, ASCII e Windows-1252;
- Uso de bytes para investigar arquivos brutos;
- Detecção automática de codificação com `chardet`;
- Importação de dados com Pandas;
- Tratamento de valores ausentes;
- Padronização de variáveis categóricas;
- Preparação de dados para análise exploratória;
- Exportação de arquivos em formato padronizado.


## Fontes dos dados

- [Kaggle — Data Cleaning Challenge: Character Encodings](https://www.kaggle.com/code/rtatman/data-cleaning-challenge-character-encodings )
- [The Washington Post — Fatal Police Shootings Database](https://www.washingtonpost.com/graphics/investigations/police-shootings-database/ )

## Autora

**Gabriela Maria Rodrigues de Morais**

- GitHub: [github.com/gabrielammoraiss](https://github.com/gabrielammoraiss )
- LinkedIn: [linkedin.com/in/gabriela-morais-63a7a7180](https://www.linkedin.com/in/gabriela-morais-63a7a7180 )
- E-mail: gabrielammoraiss@gmail.com

## Observação sobre os dados

O dataset contém informações sobre mortes envolvendo ação policial e deve ser utilizado com responsabilidade, respeitando o contexto, as limitações da fonte e a natureza sensível dos registros.
