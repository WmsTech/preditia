# PreditIA

Um painel em Python para explorar a classificação do estado de um motor de indução trifásico a partir de temperatura, vibração e corrente elétrica. O projeto foi desenvolvido para um desafio de inovação e tecnologia da Spin Engenharia.

![Painel do PreditIA com os controles de sensores e o estado estimado do motor](img/dashboard.png)

O trabalho reúne geração de dados, tratamento de leituras inconsistentes, treinamento de uma Random Forest e uma interface em Streamlit. Os sensores são simulados: os controles permitem mudar os valores e observar como a classificação responde.

## Do dado ao painel

1. `gerador.py` cria 1.500 registros sintéticos em três faixas de operação. Inclui valores ausentes, temperaturas com vírgula e correntes negativas para exercitar o tratamento dos dados.
2. `tratamento_dados.py` converte os tipos, trata valores fora das faixas definidas, preenche lacunas e atribui os rótulos `Saudavel`, `Alerta` e `Falha`.
3. `machine_learning.py` separa 80% dos dados para treino e 20% para teste, treina uma Random Forest com 50 árvores e profundidade máxima de 5 e salva o modelo com Joblib.
4. `app.py` carrega o modelo e apresenta a classificação para os valores escolhidos.

**Tecnologias:** Python, pandas, NumPy, scikit-learn, Joblib, Streamlit e Plotly.

## Critérios do experimento

Estes são os limites implementados em `classificar_estado`, em `tratamento_dados.py`:

| Sinal | Entra em alerta a partir de | Entra em falha a partir de |
| --- | ---: | ---: |
| Temperatura | 100 °C | 130 °C |
| Vibração | 2,3 mm/s | 4,5 mm/s |
| Corrente | 15 A | 17,25 A |

Basta um dos sinais atingir um limite para o registro receber o estado correspondente. A condição de falha tem prioridade. As cores dos medidores usam outras faixas e ainda precisam ser alinhadas a essa regra.

Os rótulos são produzidos por essas regras sobre dados simulados. Uma boa acurácia nesse conjunto indica que o modelo aprendeu a reproduzir os critérios do experimento; ela não mede antecipação de falhas em motores reais. O percentual mostrado como “certeza” na interface é a maior probabilidade retornada pelo classificador, sem uma avaliação de calibração.

## Executar localmente

Com Python 3.11 e Git instalados, no PowerShell:

```powershell
git clone https://github.com/WmsTech/preditia.git
cd preditia
python -m venv .venv
.\.venv\Scripts\python -m pip install -r requirements.txt
.\.venv\Scripts\python -m streamlit run app.py
```

O repositório já contém os CSVs e `modelo_rf_motor_rf.pkl`. Caso a versão instalada do scikit-learn seja incompatível com o modelo salvo, treine-o novamente no mesmo ambiente:

```powershell
.\.venv\Scripts\python machine_learning.py
```

Para refazer também os dados, a ordem é `gerador.py`, `tratamento_dados.py` e `machine_learning.py`. Esses scripts sobrescrevem os arquivos correspondentes. As dependências ainda não têm versões fixadas; a geração precisa de ajuste para versões do pandas que rejeitam texto em colunas numéricas.

Há também um [endereço de demonstração divulgado no projeto](https://preditia-monitoramento.streamlit.app/). Se estiver indisponível, use a execução local.

## Próximos passos

- Alinhar as cores dos medidores e os limites de classificação.
- Comparar a Random Forest com a própria regra de rotulagem e registrar a matriz de confusão.
- Fixar versões e tornar a geração e a divisão dos dados reproduzíveis.
- Avaliar dados reais e evolução temporal antes de tratar o protótipo como um sistema de manutenção preditiva.

Projeto de Washington Marinho dos Santos.
