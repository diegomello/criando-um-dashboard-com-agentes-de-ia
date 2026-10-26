# criando-um-dashboard-com-agentes-de-ia
Porsche · Dashboard de Vendas

Dashboard interativo, em um único arquivo HTML, que analisa uma base de 76 vendas de veículos Porsche nos Estados Unidos. O projeto parte de três perguntas de negócio e organiza os gráficos e os filtros para respondê-las.

O dashboard foi feito a partir da base de dados sanitizada do excel no Claude, feito um tratamento no arquivo xlxs para deixar a base mais assertiva e com os dados em igual, não foi feito tratamento adicional para melhorar a qualidade de alguns dados que estavam acusando como INVALIDO.

Perguntas de negócio
#	Pergunta	O que ajuda a decidir
1	Quais estados e cidades lideram o faturamento e onde estão as vendas de maior valor unitário?	Campanhas regionais e logística de entrega
2	Qual a participação de cada modelo no faturamento versus a quantidade de unidades vendidas?	Se o resultado vem de poucos carros de alto valor ou de volume em modelos de entrada
3	Como o método de pagamento se comporta em relação ao preço do veículo?	Fluxo de caixa e parcerias com instituições financeiras
O que o dashboard mostra

Indicadores (KPIs): faturamento total, ticket médio e unidades vendidas.

Filtros: ano do veículo, estado, método de pagamento e a opção "Excluir cancelados" (ligada por padrão).

Pergunta 1: concentração geográfica

Faturamento por estado (top 10)
Faturamento por cidade (top 10)
Valor unitário médio por estado (mínimo de 2 vendas)

Pergunta 2: mix de produtos

Faturamento por família de modelo (911, 718, Cayenne, Macan, Panamera e Taycan)
Comparação entre % do faturamento e % das unidades de cada família

Pergunta 3: perfil de liquidez

Ticket médio por método de pagamento
Forma de pagamento por faixa de preço (até US$ 90 mil, US$ 90–150 mil e acima de US$ 150 mil)

Cada seção tem um texto de conclusão calculado a partir dos dados. Ele é atualizado quando os filtros mudam.

Dados

A base original é uma planilha Excel (.xlsx) com 76 linhas e 12 colunas. As colunas usadas são:

Coluna	Uso
ModelYearSanitized	Filtro de ano do veículo
StateSanitized, CitySanitized	Análise geográfica
PorscheModelSanitized	Agrupado por família (primeira palavra do modelo)
SalesPriceSanitized	Faturamento, ticket médio e faixas de preço
PayMethodSanitized	Análise de liquidez
DeliveryStatusSanitized	Identifica vendas canceladas

As demais colunas (sale_id, SaleDateSanitized, customer_name, VehicleMileageSanitized, salesperson) não entram no dashboard.

Os valores estão em dólares (US$), como na planilha original.

Decisões de modelagem
Família de modelo: a base tem 36 modelos distintos, o que deixaria os gráficos ilegíveis. Por isso os modelos foram agrupados pela primeira palavra (por exemplo, "Macan GTS" e "Macan Electric" viram "Macan").
Vendas canceladas: 6 vendas têm status Cancelled. Por padrão ficam fora dos cálculos de faturamento, e o filtro "Excluir cancelados" permite incluí-las.
Grupos de pagamento: na análise por faixa de preço, os métodos foram agrupados em três grupos:
Financiamento / Lease: Financing e Lease
Cartão: Credit Card e Debit Card
À vista: Cash, Wire Transfer, Bank Transfer, ACH Payment e Crypto Payment
Faixas de preço: até US$ 90 mil, de US$ 90 mil a US$ 150 mil e acima de US$ 150 mil.
Valor unitário por estado: o gráfico considera apenas estados com pelo menos 2 vendas, para evitar que uma única venda defina a média.
Principais achados

Valores com as vendas canceladas excluídas (filtro padrão):

Geografia: Texas lidera com 18% do faturamento, seguido da Califórnia com 15%. Atlanta é a cidade que mais fatura. Os maiores valores unitários médios estão em Louisiana e Kansas, não nos estados líderes em volume.
Mix: o 911 responde por 30% do faturamento com 21% das unidades. O Macan tem 19% das unidades e apenas 11% do faturamento.
Liquidez: nos carros acima de US$ 150 mil, 72% das vendas são à vista e 11% financiadas. Nos carros até US$ 90 mil, 26% são financiadas.
Limitações
A base é pequena (76 vendas). Cada cidade tem de 1 a 3 vendas e cada faixa de preço tem entre 18 e 33, então os resultados por cidade e por faixa indicam tendências e não conclusões estatísticas.
Os dados estão embutidos no próprio HTML. Para atualizar o dashboard com uma base nova, é preciso gerar o arquivo de novo (veja abaixo).
Como executar

Não há instalação nem servidor. Abra o arquivo porsche_dashboard.html no navegador.

O gráfico depende da biblioteca Chart.js, carregada por CDN, então é necessário ter conexão com a internet na primeira abertura.

Como atualizar os dados

O HTML guarda os registros em uma constante JavaScript chamada D. Para gerar essa constante a partir da planilha, use um script em Python como o abaixo e substitua o conteúdo de const D=[...] no HTML:

python
import json
import pandas as pd

df = pd.read_excel("vendas_porsche.xlsx")

rows = [
    {
        "ano": int(r.ModelYearSanitized),
        "uf": r.StateSanitized,
        "cid": f"{r.CitySanitized}, {r.StateSanitized}",
        "pag": r.PayMethodSanitized,
        "fam": r.PorscheModelSanitized.split()[0],
        "preco": float(r.SalesPriceSanitized),
        "st": r.DeliveryStatusSanitized,
    }
    for r in df.itertuples()
]

print(json.dumps(rows, separators=(",", ":")))
Tecnologias
HTML, CSS e JavaScript puro
Chart.js 4.4.1 (via cdnjs)
Python com pandas, usado apenas para preparar os dados
Estrutura sugerida do repositório
.
├── README.md
├── porsche_dashboard.html
└── data/
    └── vendas_porsche.xlsx
