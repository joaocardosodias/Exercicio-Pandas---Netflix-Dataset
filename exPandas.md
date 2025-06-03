# Exercicío Pandas


```python
#1.-----------------------------------------------------------------------------------------
import pandas as pd
df_netflix = pd.read_csv('netflix1.csv')
#2.------------------------------------------------------------------------------------------
print(df_netflix.head(10))
#3.------------------------------------------------------------------------------------------
print("Tipos de dados por coluna:")
print(df_netflix.dtypes)
print("\nValores ausentes por coluna:")
print(df_netflix.isnull().sum())
#4.------------------------------------------------------------------------------------------
print(df_netflix.describe())
#5.------------------------------------------------------------------------------------------
df_netflix['date_added'] = pd.to_datetime(df_netflix['date_added'], errors='coerce')
#6.------------------------------------------------------------------------------------------
df_netflix['month_added'] = df_netflix['date_added'].dt.month
df_netflix['year_added'] = df_netflix['date_added'].dt.year
#7.------------------------------------------------------------------------------------------
print(df_netflix['type'].value_counts())
#9.OBS:Se a etapa  9 não for realizada antes da etapa 8 dá erro -----------------------------
split_duration = df_netflix['duration'].str.split(' ', expand=True)
df_netflix['duration_int'] = pd.to_numeric(split_duration[0], errors='coerce')
df_netflix['duration_unit'] = split_duration[1]

print(df_netflix[['title', 'duration', 'duration_int', 'duration_unit']].head())
#8.
filmes_longos_recentes = df_netflix[
    (df_netflix['type'] == 'Movie')& 
    (df_netflix['duration_int'] > 100)&
    (df_netflix['release_year'] >= 2020)
]
print(filmes_longos_recentes[['title', 'duration', 'release_year']])

#10.------------------------------------------------------------------------------------------
df_netflix['seasons_int'] = pd.NA 
df_netflix.loc[df_netflix['type'] == 'TV Show', 'seasons_int'] = df_netflix.loc[df_netflix['type'] == 'TV Show', 'duration_int']
print(df_netflix[df_netflix['type'] == 'TV Show'][['title', 'duration', 'seasons_int']].head())
#11.------------------------------------------------------------------------------------------
serie_mais_longa = df_netflix[df_netflix['type'] == 'TV Show'].sort_values(by='seasons_int', ascending=False).iloc[0]
print(f"Série com mais temporadas: {serie_mais_longa['title']} com {serie_mais_longa['seasons_int']} temporadas.")
filme_mais_longo = df_netflix[
    (df_netflix['type'] == 'Movie') & (df_netflix['duration_unit'] == 'min')
].sort_values(by='duration_int', ascending=False).iloc[0]
print(f"Filme com maior duração: {filme_mais_longo['title']} com {filme_mais_longo['duration_int']} minutos.")
#12.------------------------------------------------------------------------------------------
contagem_por_rating = df_netflix.groupby('rating')['title'].count().sort_values(ascending=False)
print(contagem_por_rating)
#13.------------------------------------------------------------------------------------------
diretores_contagem = df_netflix['director'].dropna().str.split(', ').explode().value_counts()
top_5_diretores = diretores_contagem.head(5)
print("Top 5 diretores com mais títulos:")
print(top_5_diretores)
``` 
#### 14.
##### Hipótese Subjetiva
"A maioria dos títulos disponíveis na Netflix são Filmes, em comparação com Séries de TV (TV Shows)"
```python
import pandas as pd
df_netflix = pd.read_csv('netflix1.csv')
print("\n--- Etapa 14: Testando Hipótese Simples ---")
print("Hipótese: A maioria dos títulos disponíveis na Netflix são Filmes.")
contagem_por_tipo = df_netflix['type'].value_counts()
print("\nContagem de títulos por tipo:")
print(contagem_por_tipo)
if not contagem_por_tipo.empty:
    tipo_mais_comum = contagem_por_tipo.idxmax() 
    contagem_filmes = contagem_por_tipo.get('Movie', 0) 
    contagem_series = contagem_por_tipo.get('TV Show', 0) 
    print(f"\nO tipo de conteúdo mais comum é: {tipo_mais_comum}")
    if tipo_mais_comum == 'Movie':
        print(f"A hipótese é VERDADEIRA. Existem {contagem_filmes} filmes e {contagem_series} séries.")
        if contagem_filmes > contagem_series:
             print("Há mais filmes do que séries de TV no catálogo.")
        else:
             print("A contagem de filmes é maior ou igual à de séries.")
    elif tipo_mais_comum == 'TV Show':
        print(f"A hipótese é FALSA. Existem {contagem_series} séries e {contagem_filmes} filmes.")
        print("Há mais séries de TV do que filmes no catálogo.")
    else:
        print("Não foi possível determinar o tipo mais comum ou os tipos esperados não foram encontrados.")
else:
    print("Não foi possível realizar a contagem por tipo (DataFrame vazio ou coluna 'type' ausente).")
```
--- Etapa 14: Testando Hipótese Simples ---
Hipótese: A maioria dos títulos disponíveis na Netflix são Filmes.

Contagem de títulos por tipo:
type
Movie      6126
TV Show    2664
Name: count, dtype: int64

O tipo de conteúdo mais comum é: Movie
A hipótese é VERDADEIRA. Existem 6126 filmes e 2664 séries.
Há mais filmes do que séries de TV no catálogo.
