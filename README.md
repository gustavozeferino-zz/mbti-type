# mbti-type
https://www.kaggle.com/datasnaek/mbti-type/

# Personality types classification problem

## Considerações iniciais

No passado, para um problmea similar a este, utilizei Python para limpar e indexar os termos, R para os modelos estatísticos incluindo SVD, SQL para armazenar o modelo (scores tf-idf, etc) e Python novamente para executar o classificar (via chamada de linha de comando)
Para esta tarefa, escolhi utilizar somente sklearn que, mesmo provavelmente não sendo o melhor para produção, é um ambiente rápido para prototipação.

Um desafio a parte é instalar scipy no Windows (meu ambiente atualmente). Depois de diversas tentativas na mão, a solução final foi excluir tudo e instalar Anaconda. Resolveu.

## Estratégias inicias para o problema

As etapas iniciais para este tipo de problema se resuma a:
 * limpar a base de dados
 * aplicar algum tipo de ponderação
 * treinar e validar um classificador
 * Ajuste fino (tunning)

### Limpeza da base

Básico:

 * O separador "|||" foi introduzido para organizar a base, então ele deve ser removido. (feito)
 * Como o terno somente importa, colocar o texto em `lowercase` elimina ter dois termos iguais com escrita diferente
 * Outro procedimento na mesma linha é remover acentos, caracteres especiais, etc.
 * Quanto mais perto de um texto para pessoas, há necessidade de remover stopwords e correção de sinônimos

Focados no problema
* Remoção de tags de HTML, XML, etc.
* substituir os links pelo título da páginas que eles direcionam (mesmo que seja um vídeo, geralmente a páginas tem um título)
* Substituir os links pelo texto da página em questão (menos prioritário já que introduz muito ruído)

### Ponderação

 * Para texto, basicamente se resume a aplicar TF-IDF para "corrigir" a matriz de frequências e ter scores que diferencie mais os termos e seu impacto no documento
 * Entre as principais configurações do TF-IDF está a configuração do seu kernel de cálculo. Algo que sempre testo é a aplicação de logaritmo no cálculo (suavização se houver muita repetição de termos)
 * Aplicar SVD sobre a matriz de TF-IDF é algo a se considerar já que isto é muito utilizado em classificadores de texto incluíndo mecanismos de busca como o Google.


### Classificar

 * Dado que a base foi limpa e ponderada, é testar qual classificar se sai melhor e fazer seu ajuste. A maior parte do trabalho se concentra nas etapas anteriores

## Resolução do desafio

O desafio foi feito tendo como base o tutorial do sklearn para classificar textos:
[Working With Text Data](http://scikit-learn.org/stable/tutorial/text_analytics/working_with_text_data.html)

O ambiente do sklearn oferece funções que vão do processo de limpeza até a validação cruzada e seleção de "features".

Para a validação cruzada, foram utilizados 5 partições dos dados. A acurácia final considerada para o modelo foi a média das 5 execuções. O limite em 5 é devido ao tempo para execução. Como o procedimento é estocástico, é interessante gerar diversos valores de acurácia para que se possa trabalhar com a distribuição empírica de probabilidade e realizar comparações entre modelos pela curva de probabilidade e não apenas por uma execução.

### Resultados obtidos até o momento


| Classifier | LSA | Mean accuracy |
|-:|:-:|-:|
| GaussianNB | True | 0.594357 |
| SGDClassifier | False | 0.660164 |
| SGDClassifier | True | 0.572088 |
| RandomForestClassifier | False | 0.275707 |
| RandomForestClassifier | True | 0.439191 |
| ExtraTreesClassifier | False | 0.273415 |
| ExtraTreesClassifier | True | 0.400218 |
| DecisionTreeClassifier | False | 0.464427 |
| DecisionTreeClassifier | True | 0.334520 |

### Próximos passos

O classificador linear baseado em Gradiente Descente (SGDClassifier) apresentou bons resultados é um candidato a sofrer um ajuste fino para ver até onde o valor da acurácia pode ir.

Fora a parte dos classificadores, o trabalho mais importante é realmente analisar a base de dados e verificar:
 * limpar mais a base e trabalhar a parte de sinônimos, conteúdo dos links (título da página), correção de erros (e.g. ngrams)
 * se a amostra é balanceada entre os diversos tipos - isto pode afetar o classifidor
 * entender o que compões os posts para poder remover ruídos e melhorar as features coletadas
 * apesar do desafio em sí é o somente a base de dados rotulada, num contexto real deve ser considerado que o teste original é baseado na percepção dos respondentes considerando um questionário estruturado. Postagens representam a projeção do que o usuário seleciona para compor sua imagem. Por exemplo, tenho um visão particular muito forte sobre política mas não posto nada sobre o assunto em lugar algum. O que posto reflete como quero ser visto para os diferentes stakeholders da vida real e não o que realmente penso.
 * SVD não foi descartado. Alguns classificadores obtiveram uma melhora com ele. É necessário analisar a curva de autovalores e definir qual o melhor ponto de corte (número de valores singulares ou valor de truncamento).
 
Considerando a parte dos classificadores, `tensorFlow` (Deep learning) é uma biblioteca que vem dando bons resultados. Desconheço o custo de execução e como se sairia em produção, mas é algo a ser considerado.
 














