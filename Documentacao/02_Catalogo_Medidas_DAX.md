# Catálogo de Medidas DAX — AgroCerrado S.A.

Total: **109 medidas** na tabela `_Medidas`, organizadas em 8 pastas (displayFolder).

> Padrões usados: `DIVIDE` para divisões seguras, `VAR/RETURN` em toda lógica composta, `KEEPFILTERS` para recortes de unidade (grãos em sc × algodão em @), `TREATAS` para levar o filtro de Fazenda até o clima regional e comparação YoY **YTD comparável** (ano de referência até a última data disponível vs. mesmo período do ano anterior).

## 01 Produção

| Medida | Formato | Descrição |
|---|---|---|
| `Área Plantada (ha)` | `#,0` | Área colhida somando safra de verão e safrinha. |
| `Área Grãos (ha)` | `#,0` | Área de culturas medidas em sacas de 60 kg (exclui algodão). |
| `Área Total Fazendas (ha)` | `#,0` | Área física das propriedades. |
| `Produção Grãos (sc)` | `#,0` | Volume colhido de grãos em sacas de 60 kg. |
| `Produção Algodão (@)` | `#,0` | Algodão em arrobas (unidade própria, não somada às sacas). |
| `Produtividade Média (sc/ha)` | `#,0.0` | Produtividade ponderada pela área (grãos). |
| `Produtividade (un/ha)` | `#,0.0` | Produtividade na unidade nativa da cultura (sc/ha ou @/ha). |
| `Índice Produtividade vs Portfólio` | `0.00` | 1,00 = média do portfólio para a mesma cultura e período. |
| `Intensidade de Uso da Terra` | `0.00"x"` | Área plantada no ano ÷ área física (>1 indica segunda safra). |
| `Área Verão (ha)` | `#,0` |  |
| `Produtividade Verão (sc/ha)` | `#,0.0` |  |
| `Custo/ha Verão` | `\R$ #,0` |  |
| `Margem % Verão` | `0.0%` | Receita das culturas do ciclo menos custo de produção do ciclo. |
| `Área Safrinha (ha)` | `#,0` |  |
| `Produtividade Safrinha (sc/ha)` | `#,0.0` |  |
| `Custo/ha Safrinha` | `\R$ #,0` |  |
| `Margem % Safrinha` | `0.0%` | Receita das culturas do ciclo menos custo de produção do ciclo. |

### Área Plantada (ha)
```dax
Área Plantada (ha) =
SUM ( Fato_Producao[AreaPlantadaHa] )
```

### Área Grãos (ha)
```dax
Área Grãos (ha) =
CALCULATE ( [Área Plantada (ha)], KEEPFILTERS ( Dim_Cultura[UnidadeMedida] = "sc 60kg" ) )
```

### Área Total Fazendas (ha)
```dax
Área Total Fazendas (ha) =
SUM ( Dim_Fazenda[AreaTotalHa] )
```

### Produção Grãos (sc)
```dax
Produção Grãos (sc) =
CALCULATE ( SUM ( Fato_Producao[VolumeSc] ), KEEPFILTERS ( Dim_Cultura[UnidadeMedida] = "sc 60kg" ) )
```

### Produção Algodão (@)
```dax
Produção Algodão (@) =
CALCULATE ( SUM ( Fato_Producao[VolumeSc] ), KEEPFILTERS ( Dim_Cultura[UnidadeMedida] = "arroba" ) )
```

### Produtividade Média (sc/ha)
```dax
Produtividade Média (sc/ha) =
DIVIDE ( [Produção Grãos (sc)], [Área Grãos (ha)] )
```

### Produtividade (un/ha)
```dax
Produtividade (un/ha) =
DIVIDE ( SUM ( Fato_Producao[VolumeSc] ), [Área Plantada (ha)] )
```

### Índice Produtividade vs Portfólio
```dax
Índice Produtividade vs Portfólio =
DIVIDE ( [Produtividade (un/ha)], CALCULATE ( [Produtividade (un/ha)], REMOVEFILTERS ( Dim_Fazenda ) ) )
```

### Intensidade de Uso da Terra
```dax
Intensidade de Uso da Terra =
DIVIDE ( [Área Plantada (ha) (Ano Ref)], [Área Total Fazendas (ha)] )
```

### Área Verão (ha)
```dax
Área Verão (ha) =
CALCULATE ( [Área Plantada (ha)], KEEPFILTERS ( Fato_Producao[TipoSafra] = "Verão" ) )
```

### Produtividade Verão (sc/ha)
```dax
Produtividade Verão (sc/ha) =
CALCULATE ( [Produtividade Média (sc/ha)], KEEPFILTERS ( Fato_Producao[TipoSafra] = "Verão" ) )
```

### Custo/ha Verão
```dax
Custo/ha Verão =
CALCULATE ( [Custo por Hectare], KEEPFILTERS ( Fato_Producao[TipoSafra] = "Verão" ) )
```

### Margem % Verão
```dax
Margem % Verão =
VAR _culturas = CALCULATETABLE ( VALUES ( Fato_Producao[CulturaKey] ), KEEPFILTERS ( Fato_Producao[TipoSafra] = "Verão" ) )
VAR _receita = CALCULATE ( [Receita Bruta], TREATAS ( _culturas, Dim_Cultura[CulturaKey] ) )
VAR _custo = CALCULATE ( [Custo de Produção], KEEPFILTERS ( Fato_Producao[TipoSafra] = "Verão" ) )
RETURN DIVIDE ( _receita - _custo, _receita )
```

### Área Safrinha (ha)
```dax
Área Safrinha (ha) =
CALCULATE ( [Área Plantada (ha)], KEEPFILTERS ( Fato_Producao[TipoSafra] = "Safrinha" ) )
```

### Produtividade Safrinha (sc/ha)
```dax
Produtividade Safrinha (sc/ha) =
CALCULATE ( [Produtividade Média (sc/ha)], KEEPFILTERS ( Fato_Producao[TipoSafra] = "Safrinha" ) )
```

### Custo/ha Safrinha
```dax
Custo/ha Safrinha =
CALCULATE ( [Custo por Hectare], KEEPFILTERS ( Fato_Producao[TipoSafra] = "Safrinha" ) )
```

### Margem % Safrinha
```dax
Margem % Safrinha =
VAR _culturas = CALCULATETABLE ( VALUES ( Fato_Producao[CulturaKey] ), KEEPFILTERS ( Fato_Producao[TipoSafra] = "Safrinha" ) )
VAR _receita = CALCULATE ( [Receita Bruta], TREATAS ( _culturas, Dim_Cultura[CulturaKey] ) )
VAR _custo = CALCULATE ( [Custo de Produção], KEEPFILTERS ( Fato_Producao[TipoSafra] = "Safrinha" ) )
RETURN DIVIDE ( _receita - _custo, _receita )
```

## 02 Comercial

| Medida | Formato | Descrição |
|---|---|---|
| `Receita Bruta` | `\R$ #,0` |  |
| `Volume Vendido` | `#,0` |  |
| `Preço Médio (R$/un)` | `\R$ #,0.00` | Preço realizado ponderado pelo volume. |
| `Preço Médio Grãos (R$/sc)` | `\R$ #,0.00` |  |
| `Nº Contratos` | `#,0` |  |
| `Ticket Médio por Contrato` | `\R$ #,0` |  |
| `Receita Exportação` | `\R$ #,0` |  |
| `% Exportação` | `0.0%` |  |
| `% Barter` | `0.0%` |  |
| `Share Receita Cliente %` | `0.0%` |  |
| `Share Receita Total %` | `0.0%` | Participação no total da empresa para o mesmo período/fazendas. |
| `Ranking Cliente` | `0` |  |
| `Receita Top 8 Clientes` | `\R$ #,0` |  |
| `Receita Mês Anterior` | `\R$ #,0` |  |
| `Receita MoM %` | `0.0%` |  |
| `Receita Mesmo Período AA` | `\R$ #,0` |  |
| `Receita vs AA %` | `0.0%` |  |
| `Receita Acumulada no Ano` | `\R$ #,0` |  |
| `Preço Médio Empresa (R$/un)` | `\R$ #,0.00` |  |

### Receita Bruta
```dax
Receita Bruta =
SUM ( Fato_Vendas[ReceitaBruta] )
```

### Volume Vendido
```dax
Volume Vendido =
SUM ( Fato_Vendas[VolumeSc] )
```

### Preço Médio (R$/un)
```dax
Preço Médio (R$/un) =
DIVIDE ( [Receita Bruta], [Volume Vendido] )
```

### Preço Médio Grãos (R$/sc)
```dax
Preço Médio Grãos (R$/sc) =
CALCULATE ( [Preço Médio (R$/un)], KEEPFILTERS ( Dim_Cultura[UnidadeMedida] = "sc 60kg" ) )
```

### Nº Contratos
```dax
Nº Contratos =
COUNTROWS ( Fato_Vendas )
```

### Ticket Médio por Contrato
```dax
Ticket Médio por Contrato =
DIVIDE ( [Receita Bruta], [Nº Contratos] )
```

### Receita Exportação
```dax
Receita Exportação =
CALCULATE ( [Receita Bruta], KEEPFILTERS ( Dim_Cliente[Canal] = "Exportação" ) )
```

### % Exportação
```dax
% Exportação =
DIVIDE ( [Receita Exportação], [Receita Bruta] )
```

### % Barter
```dax
% Barter =
DIVIDE ( CALCULATE ( [Receita Bruta], KEEPFILTERS ( Fato_Vendas[TipoContrato] = "Barter" ) ), [Receita Bruta] )
```

### Share Receita Cliente %
```dax
Share Receita Cliente % =
DIVIDE ( [Receita Bruta], CALCULATE ( [Receita Bruta], ALLSELECTED ( Dim_Cliente ) ) )
```

### Share Receita Total %
```dax
Share Receita Total % =
DIVIDE (
    [Receita Bruta],
    CALCULATE ( [Receita Bruta], REMOVEFILTERS ( Dim_Cliente ), REMOVEFILTERS ( Fato_Vendas[TipoContrato] ), REMOVEFILTERS ( Dim_Cultura ) )
)
```

### Ranking Cliente
```dax
Ranking Cliente =
IF ( NOT ISBLANK ( [Receita Bruta] ), RANKX ( ALLSELECTED ( Dim_Cliente[NomeCliente] ), [Receita Bruta],, DESC, DENSE ) )
```

### Receita Top 8 Clientes
```dax
Receita Top 8 Clientes =
IF ( [Ranking Cliente] <= 8, [Receita Bruta] )
```

### Receita Mês Anterior
```dax
Receita Mês Anterior =
CALCULATE ( [Receita Bruta], DATEADD ( Dim_Tempo[Data], -1, MONTH ) )
```

### Receita MoM %
```dax
Receita MoM % =
DIVIDE ( [Receita Bruta] - [Receita Mês Anterior], [Receita Mês Anterior] )
```

### Receita Mesmo Período AA
```dax
Receita Mesmo Período AA =
CALCULATE ( [Receita Bruta], DATEADD ( Dim_Tempo[Data], -1, YEAR ) )
```

### Receita vs AA %
```dax
Receita vs AA % =
DIVIDE ( [Receita Bruta] - [Receita Mesmo Período AA], [Receita Mesmo Período AA] )
```

### Receita Acumulada no Ano
```dax
Receita Acumulada no Ano =
TOTALYTD ( [Receita Bruta], Dim_Tempo[Data] )
```

### Preço Médio Empresa (R$/un)
```dax
Preço Médio Empresa (R$/un) =
CALCULATE ( [Preço Médio (R$/un)], REMOVEFILTERS ( Dim_Cliente ), REMOVEFILTERS ( Fato_Vendas[TipoContrato] ) )
```

## 03 Custos

| Medida | Formato | Descrição |
|---|---|---|
| `Custo de Produção` | `\R$ #,0` | Custo total da safra (base para margem por cultura). |
| `Custo por Hectare` | `\R$ #,0` |  |
| `Custo por Hectare Grãos` | `\R$ #,0` |  |
| `Desembolso Operacional` | `\R$ #,0` | Lançamentos mensais por categoria/fornecedor. |
| `% do Desembolso` | `0.0%` |  |
| `% Custo Variável` | `0.0%` |  |
| `Break-even (sc/ha)` | `#,0.0` | Sacas por hectare necessárias para pagar o custo. |
| `Margem de Segurança (sc/ha)` | `#,0.0` |  |
| `Maior Categoria de Custo` | `texto/HTML` |  |
| `Fornecedor Principal` | `texto/HTML` |  |

### Custo de Produção
```dax
Custo de Produção =
SUM ( Fato_Producao[CustoTotal] )
```

### Custo por Hectare
```dax
Custo por Hectare =
DIVIDE ( [Custo de Produção], [Área Plantada (ha)] )
```

### Custo por Hectare Grãos
```dax
Custo por Hectare Grãos =
CALCULATE ( [Custo por Hectare], KEEPFILTERS ( Dim_Cultura[UnidadeMedida] = "sc 60kg" ) )
```

### Desembolso Operacional
```dax
Desembolso Operacional =
SUM ( Fato_Custos[Valor] )
```

### % do Desembolso
```dax
% do Desembolso =
DIVIDE ( [Desembolso Operacional], CALCULATE ( [Desembolso Operacional], REMOVEFILTERS ( Fato_Custos[CategoriaCusto] ) ) )
```

### % Custo Variável
```dax
% Custo Variável =
DIVIDE ( CALCULATE ( [Desembolso Operacional], KEEPFILTERS ( Fato_Custos[TipoCusto] = "Variável" ) ), [Desembolso Operacional] )
```

### Break-even (sc/ha)
```dax
Break-even (sc/ha) =
DIVIDE ( [Custo por Hectare Grãos], [Preço Médio Grãos (R$/sc)] )
```

### Margem de Segurança (sc/ha)
```dax
Margem de Segurança (sc/ha) =
IF ( NOT ISBLANK ( [Break-even (sc/ha)] ), [Produtividade Média (sc/ha)] - [Break-even (sc/ha)] )
```

### Maior Categoria de Custo
```dax
Maior Categoria de Custo =
VAR _top = TOPN ( 1, VALUES ( Fato_Custos[CategoriaCusto] ), [Desembolso Operacional], DESC )
RETURN MAXX ( _top, Fato_Custos[CategoriaCusto] )
```

### Fornecedor Principal
```dax
Fornecedor Principal =
VAR _top = TOPN ( 1, FILTER ( VALUES ( Dim_Fornecedor[NomeFornecedor] ), NOT ISBLANK ( [Desembolso Operacional] ) ), [Desembolso Operacional], DESC )
RETURN MAXX ( _top, Dim_Fornecedor[NomeFornecedor] )
```

## 04 Rentabilidade

| Medida | Formato | Descrição |
|---|---|---|
| `Margem Bruta` | `\R$ #,0` |  |
| `Margem Bruta %` | `0.0%` |  |
| `Margem por Hectare` | `\R$ #,0` |  |
| `Ranking Fazenda (Receita)` | `0` |  |
| `Ranking Fazenda (Portfólio)` | `0` | Ranking contra todas as fazendas, robusto ao filtro de tooltip (usado nos tooltips). |
| `Ranking Fazenda (Margem %)` | `0` |  |

### Margem Bruta
```dax
Margem Bruta =
VAR _r = [Receita Bruta]
VAR _c = [Custo de Produção]
RETURN IF ( ISBLANK ( _r ) && ISBLANK ( _c ), BLANK (), _r - _c )
```

### Margem Bruta %
```dax
Margem Bruta % =
DIVIDE ( [Margem Bruta], [Receita Bruta] )
```

### Margem por Hectare
```dax
Margem por Hectare =
DIVIDE ( [Margem Bruta], [Área Plantada (ha)] )
```

### Ranking Fazenda (Receita)
```dax
Ranking Fazenda (Receita) =
IF ( NOT ISBLANK ( [Receita Bruta] ), RANKX ( ALLSELECTED ( Dim_Fazenda[NomeFazenda] ), [Receita Bruta],, DESC, DENSE ) )
```

### Ranking Fazenda (Portfólio)
```dax
Ranking Fazenda (Portfólio) =
VAR _atual = [Receita Bruta]
VAR _outras =
    ADDCOLUMNS (
        ALL ( Dim_Fazenda[NomeFazenda] ),
        "@r",
            VAR _f = Dim_Fazenda[NomeFazenda]
            RETURN CALCULATE ( [Receita Bruta], REMOVEFILTERS ( Dim_Fazenda ), Dim_Fazenda[NomeFazenda] = _f )
    )
RETURN IF ( NOT ISBLANK ( _atual ), COUNTROWS ( FILTER ( _outras, [@r] > _atual ) ) + 1 )
```

### Ranking Fazenda (Margem %)
```dax
Ranking Fazenda (Margem %) =
IF ( NOT ISBLANK ( [Margem Bruta %] ), RANKX ( ALLSELECTED ( Dim_Fazenda[NomeFazenda] ), [Margem Bruta %],, DESC, DENSE ) )
```

## 05 Inteligência de Tempo

| Medida | Formato | Descrição |
|---|---|---|
| `Ano Referência` | `0` | Último ano no filtro — base dos cards executivos. |
| `Data Limite Referência` | `dd/mm/yyyy` |  |
| `Rótulo Período Referência` | `texto/HTML` |  |
| `Receita Bruta (Ano Ref)` | `\R$ #,0` |  |
| `Receita Bruta (Ano Ant)` | `\R$ #,0` | Mesmo período do ano anterior (YTD comparável). |
| `Receita Bruta YoY %` | `0.0%` |  |
| `Produção Grãos (sc) (Ano Ref)` | `#,0` |  |
| `Produção Grãos (sc) (Ano Ant)` | `#,0` | Mesmo período do ano anterior (YTD comparável). |
| `Produção Grãos (sc) YoY %` | `0.0%` |  |
| `Produção Algodão (@) (Ano Ref)` | `#,0` |  |
| `Produção Algodão (@) (Ano Ant)` | `#,0` | Mesmo período do ano anterior (YTD comparável). |
| `Produção Algodão (@) YoY %` | `0.0%` |  |
| `Área Plantada (ha) (Ano Ref)` | `#,0` |  |
| `Área Plantada (ha) (Ano Ant)` | `#,0` | Mesmo período do ano anterior (YTD comparável). |
| `Área Plantada (ha) YoY %` | `0.0%` |  |
| `Produtividade Média (sc/ha) (Ano Ref)` | `#,0.0` |  |
| `Produtividade Média (sc/ha) (Ano Ant)` | `#,0.0` | Mesmo período do ano anterior (YTD comparável). |
| `Produtividade Média (sc/ha) YoY %` | `0.0%` |  |
| `Custo de Produção (Ano Ref)` | `\R$ #,0` |  |
| `Custo de Produção (Ano Ant)` | `\R$ #,0` | Mesmo período do ano anterior (YTD comparável). |
| `Custo de Produção YoY %` | `0.0%` |  |
| `Margem Bruta (Ano Ref)` | `\R$ #,0` |  |
| `Margem Bruta (Ano Ant)` | `\R$ #,0` | Mesmo período do ano anterior (YTD comparável). |
| `Margem Bruta YoY %` | `0.0%` |  |
| `Custo por Hectare (Ano Ref)` | `\R$ #,0` |  |
| `Custo por Hectare (Ano Ant)` | `\R$ #,0` | Mesmo período do ano anterior (YTD comparável). |
| `Custo por Hectare YoY %` | `0.0%` |  |
| `Desembolso Operacional (Ano Ref)` | `\R$ #,0` |  |
| `Desembolso Operacional (Ano Ant)` | `\R$ #,0` | Mesmo período do ano anterior (YTD comparável). |
| `Desembolso Operacional YoY %` | `0.0%` |  |
| `Break-even (sc/ha) (Ano Ref)` | `#,0.0` |  |
| `Preço Médio Grãos (R$/sc) (Ano Ref)` | `\R$ #,0.00` |  |
| `Margem Bruta % (Ano Ref)` | `0.0%` |  |
| `Margem Bruta % (Ano Ant)` | `0.0%` |  |
| `Margem Bruta % Δ p.p.` | `+0.0;-0.0;0.0` | Variação em pontos percentuais. |

### Ano Referência
```dax
Ano Referência =
MAX ( Dim_Tempo[Ano] )
```

### Data Limite Referência
```dax
Data Limite Referência =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( MAX ( Dim_Tempo[Data] ), Dim_Tempo[Ano] = _ano )
```

### Rótulo Período Referência
```dax
Rótulo Período Referência =
VAR _d = [Data Limite Referência]
RETURN
    FORMAT ( [Ano Referência], "0" )
        & IF ( _d < DATE ( YEAR ( _d ), 12, 31 ), " · até " & FORMAT ( _d, "dd/mmm" ), "" )
```

### Receita Bruta (Ano Ref)
```dax
Receita Bruta (Ano Ref) =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( [Receita Bruta], Dim_Tempo[Ano] = _ano )
```

### Receita Bruta (Ano Ant)
```dax
Receita Bruta (Ano Ant) =
VAR _ano = [Ano Referência]
VAR _limite = EDATE ( [Data Limite Referência], -12 )
RETURN CALCULATE ( [Receita Bruta], Dim_Tempo[Ano] = _ano - 1, Dim_Tempo[Data] <= _limite )
```

### Receita Bruta YoY %
```dax
Receita Bruta YoY % =
VAR _atual = [Receita Bruta (Ano Ref)]
VAR _anterior = [Receita Bruta (Ano Ant)]
RETURN IF ( NOT ISBLANK ( _atual ) && NOT ISBLANK ( _anterior ), DIVIDE ( _atual - _anterior, _anterior ) )
```

### Produção Grãos (sc) (Ano Ref)
```dax
Produção Grãos (sc) (Ano Ref) =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( [Produção Grãos (sc)], Dim_Tempo[Ano] = _ano )
```

### Produção Grãos (sc) (Ano Ant)
```dax
Produção Grãos (sc) (Ano Ant) =
VAR _ano = [Ano Referência]
VAR _limite = EDATE ( [Data Limite Referência], -12 )
RETURN CALCULATE ( [Produção Grãos (sc)], Dim_Tempo[Ano] = _ano - 1, Dim_Tempo[Data] <= _limite )
```

### Produção Grãos (sc) YoY %
```dax
Produção Grãos (sc) YoY % =
VAR _atual = [Produção Grãos (sc) (Ano Ref)]
VAR _anterior = [Produção Grãos (sc) (Ano Ant)]
RETURN IF ( NOT ISBLANK ( _atual ) && NOT ISBLANK ( _anterior ), DIVIDE ( _atual - _anterior, _anterior ) )
```

### Produção Algodão (@) (Ano Ref)
```dax
Produção Algodão (@) (Ano Ref) =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( [Produção Algodão (@)], Dim_Tempo[Ano] = _ano )
```

### Produção Algodão (@) (Ano Ant)
```dax
Produção Algodão (@) (Ano Ant) =
VAR _ano = [Ano Referência]
VAR _limite = EDATE ( [Data Limite Referência], -12 )
RETURN CALCULATE ( [Produção Algodão (@)], Dim_Tempo[Ano] = _ano - 1, Dim_Tempo[Data] <= _limite )
```

### Produção Algodão (@) YoY %
```dax
Produção Algodão (@) YoY % =
VAR _atual = [Produção Algodão (@) (Ano Ref)]
VAR _anterior = [Produção Algodão (@) (Ano Ant)]
RETURN IF ( NOT ISBLANK ( _atual ) && NOT ISBLANK ( _anterior ), DIVIDE ( _atual - _anterior, _anterior ) )
```

### Área Plantada (ha) (Ano Ref)
```dax
Área Plantada (ha) (Ano Ref) =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( [Área Plantada (ha)], Dim_Tempo[Ano] = _ano )
```

### Área Plantada (ha) (Ano Ant)
```dax
Área Plantada (ha) (Ano Ant) =
VAR _ano = [Ano Referência]
VAR _limite = EDATE ( [Data Limite Referência], -12 )
RETURN CALCULATE ( [Área Plantada (ha)], Dim_Tempo[Ano] = _ano - 1, Dim_Tempo[Data] <= _limite )
```

### Área Plantada (ha) YoY %
```dax
Área Plantada (ha) YoY % =
VAR _atual = [Área Plantada (ha) (Ano Ref)]
VAR _anterior = [Área Plantada (ha) (Ano Ant)]
RETURN IF ( NOT ISBLANK ( _atual ) && NOT ISBLANK ( _anterior ), DIVIDE ( _atual - _anterior, _anterior ) )
```

### Produtividade Média (sc/ha) (Ano Ref)
```dax
Produtividade Média (sc/ha) (Ano Ref) =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( [Produtividade Média (sc/ha)], Dim_Tempo[Ano] = _ano )
```

### Produtividade Média (sc/ha) (Ano Ant)
```dax
Produtividade Média (sc/ha) (Ano Ant) =
VAR _ano = [Ano Referência]
VAR _limite = EDATE ( [Data Limite Referência], -12 )
RETURN CALCULATE ( [Produtividade Média (sc/ha)], Dim_Tempo[Ano] = _ano - 1, Dim_Tempo[Data] <= _limite )
```

### Produtividade Média (sc/ha) YoY %
```dax
Produtividade Média (sc/ha) YoY % =
VAR _atual = [Produtividade Média (sc/ha) (Ano Ref)]
VAR _anterior = [Produtividade Média (sc/ha) (Ano Ant)]
RETURN IF ( NOT ISBLANK ( _atual ) && NOT ISBLANK ( _anterior ), DIVIDE ( _atual - _anterior, _anterior ) )
```

### Custo de Produção (Ano Ref)
```dax
Custo de Produção (Ano Ref) =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( [Custo de Produção], Dim_Tempo[Ano] = _ano )
```

### Custo de Produção (Ano Ant)
```dax
Custo de Produção (Ano Ant) =
VAR _ano = [Ano Referência]
VAR _limite = EDATE ( [Data Limite Referência], -12 )
RETURN CALCULATE ( [Custo de Produção], Dim_Tempo[Ano] = _ano - 1, Dim_Tempo[Data] <= _limite )
```

### Custo de Produção YoY %
```dax
Custo de Produção YoY % =
VAR _atual = [Custo de Produção (Ano Ref)]
VAR _anterior = [Custo de Produção (Ano Ant)]
RETURN IF ( NOT ISBLANK ( _atual ) && NOT ISBLANK ( _anterior ), DIVIDE ( _atual - _anterior, _anterior ) )
```

### Margem Bruta (Ano Ref)
```dax
Margem Bruta (Ano Ref) =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( [Margem Bruta], Dim_Tempo[Ano] = _ano )
```

### Margem Bruta (Ano Ant)
```dax
Margem Bruta (Ano Ant) =
VAR _ano = [Ano Referência]
VAR _limite = EDATE ( [Data Limite Referência], -12 )
RETURN CALCULATE ( [Margem Bruta], Dim_Tempo[Ano] = _ano - 1, Dim_Tempo[Data] <= _limite )
```

### Margem Bruta YoY %
```dax
Margem Bruta YoY % =
VAR _atual = [Margem Bruta (Ano Ref)]
VAR _anterior = [Margem Bruta (Ano Ant)]
RETURN IF ( NOT ISBLANK ( _atual ) && NOT ISBLANK ( _anterior ), DIVIDE ( _atual - _anterior, _anterior ) )
```

### Custo por Hectare (Ano Ref)
```dax
Custo por Hectare (Ano Ref) =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( [Custo por Hectare], Dim_Tempo[Ano] = _ano )
```

### Custo por Hectare (Ano Ant)
```dax
Custo por Hectare (Ano Ant) =
VAR _ano = [Ano Referência]
VAR _limite = EDATE ( [Data Limite Referência], -12 )
RETURN CALCULATE ( [Custo por Hectare], Dim_Tempo[Ano] = _ano - 1, Dim_Tempo[Data] <= _limite )
```

### Custo por Hectare YoY %
```dax
Custo por Hectare YoY % =
VAR _atual = [Custo por Hectare (Ano Ref)]
VAR _anterior = [Custo por Hectare (Ano Ant)]
RETURN IF ( NOT ISBLANK ( _atual ) && NOT ISBLANK ( _anterior ), DIVIDE ( _atual - _anterior, _anterior ) )
```

### Desembolso Operacional (Ano Ref)
```dax
Desembolso Operacional (Ano Ref) =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( [Desembolso Operacional], Dim_Tempo[Ano] = _ano )
```

### Desembolso Operacional (Ano Ant)
```dax
Desembolso Operacional (Ano Ant) =
VAR _ano = [Ano Referência]
VAR _limite = EDATE ( [Data Limite Referência], -12 )
RETURN CALCULATE ( [Desembolso Operacional], Dim_Tempo[Ano] = _ano - 1, Dim_Tempo[Data] <= _limite )
```

### Desembolso Operacional YoY %
```dax
Desembolso Operacional YoY % =
VAR _atual = [Desembolso Operacional (Ano Ref)]
VAR _anterior = [Desembolso Operacional (Ano Ant)]
RETURN IF ( NOT ISBLANK ( _atual ) && NOT ISBLANK ( _anterior ), DIVIDE ( _atual - _anterior, _anterior ) )
```

### Break-even (sc/ha) (Ano Ref)
```dax
Break-even (sc/ha) (Ano Ref) =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( [Break-even (sc/ha)], Dim_Tempo[Ano] = _ano )
```

### Preço Médio Grãos (R$/sc) (Ano Ref)
```dax
Preço Médio Grãos (R$/sc) (Ano Ref) =
VAR _ano = [Ano Referência]
RETURN CALCULATE ( [Preço Médio Grãos (R$/sc)], Dim_Tempo[Ano] = _ano )
```

### Margem Bruta % (Ano Ref)
```dax
Margem Bruta % (Ano Ref) =
DIVIDE ( [Margem Bruta (Ano Ref)], [Receita Bruta (Ano Ref)] )
```

### Margem Bruta % (Ano Ant)
```dax
Margem Bruta % (Ano Ant) =
DIVIDE ( [Margem Bruta (Ano Ant)], [Receita Bruta (Ano Ant)] )
```

### Margem Bruta % Δ p.p.
```dax
Margem Bruta % Δ p.p. =
VAR _a = [Margem Bruta % (Ano Ref)]
VAR _b = [Margem Bruta % (Ano Ant)]
RETURN IF ( NOT ISBLANK ( _a ) && NOT ISBLANK ( _b ), ( _a - _b ) * 100 )
```

## 06 Clima

| Medida | Formato | Descrição |
|---|---|---|
| `Chuva Média Mensal (mm)` | `#,0.0` | Precipitação média por mês. TREATAS faz o filtro de Fazenda chegar ao clima da região. |
| `Precipitação Acumulada (mm)` | `#,0` | Soma do período por região (média entre regiões). |
| `Temperatura Média (°C)` | `#,0.0` |  |
| `Umidade Relativa Média` | `0"%"` |  |
| `Dias de Chuva (média/mês)` | `#,0.0` |  |
| `Correlação Chuva x Produtividade (r)` | `0.00` | Pearson entre chuva média e produtividade (pontos UF × Safra). |
| `Leitura da Correlação` | `texto/HTML` |  |

### Chuva Média Mensal (mm)
```dax
Chuva Média Mensal (mm) =
CALCULATE ( AVERAGE ( Fato_Clima[PrecipitacaoMm] ), TREATAS ( VALUES ( Dim_Fazenda[RegiaoKey] ), Dim_Regiao[RegiaoKey] ) )
```

### Precipitação Acumulada (mm)
```dax
Precipitação Acumulada (mm) =
CALCULATE (
    AVERAGEX ( VALUES ( Dim_Regiao[RegiaoKey] ), CALCULATE ( SUM ( Fato_Clima[PrecipitacaoMm] ) ) ),
    TREATAS ( VALUES ( Dim_Fazenda[RegiaoKey] ), Dim_Regiao[RegiaoKey] )
)
```

### Temperatura Média (°C)
```dax
Temperatura Média (°C) =
CALCULATE ( AVERAGE ( Fato_Clima[TempMediaC] ), TREATAS ( VALUES ( Dim_Fazenda[RegiaoKey] ), Dim_Regiao[RegiaoKey] ) )
```

### Umidade Relativa Média
```dax
Umidade Relativa Média =
CALCULATE ( AVERAGE ( Fato_Clima[UmidadeRelativa] ), TREATAS ( VALUES ( Dim_Fazenda[RegiaoKey] ), Dim_Regiao[RegiaoKey] ) )
```

### Dias de Chuva (média/mês)
```dax
Dias de Chuva (média/mês) =
CALCULATE ( AVERAGE ( Fato_Clima[DiasChuva] ), TREATAS ( VALUES ( Dim_Fazenda[RegiaoKey] ), Dim_Regiao[RegiaoKey] ) )
```

### Correlação Chuva x Produtividade (r)
```dax
Correlação Chuva x Produtividade (r) =
VAR _pontos =
    FILTER (
        ADDCOLUMNS (
            CROSSJOIN ( VALUES ( Dim_Regiao[UF] ), VALUES ( Dim_Tempo[Safra] ) ),
            "@x", [Chuva Média Mensal (mm)],
            "@y", [Produtividade Média (sc/ha)]
        ),
        NOT ISBLANK ( [@x] ) && NOT ISBLANK ( [@y] )
    )
VAR _n = COUNTROWS ( _pontos )
VAR _mx = AVERAGEX ( _pontos, [@x] )
VAR _my = AVERAGEX ( _pontos, [@y] )
VAR _cov = SUMX ( _pontos, ( [@x] - _mx ) * ( [@y] - _my ) )
VAR _sx = SQRT ( SUMX ( _pontos, ( [@x] - _mx ) ^ 2 ) )
VAR _sy = SQRT ( SUMX ( _pontos, ( [@y] - _my ) ^ 2 ) )
RETURN IF ( _n >= 3, DIVIDE ( _cov, _sx * _sy ) )
```

### Leitura da Correlação
```dax
Leitura da Correlação =
VAR _r = [Correlação Chuva x Produtividade (r)]
VAR _abs = ABS ( _r )
RETURN
    SWITCH (
        TRUE (),
        ISBLANK ( _r ), "Amostra insuficiente",
        _abs < 0.3, "Relação fraca: manejo e mix de culturas pesam mais que a chuva",
        _abs < 0.6, IF ( _r > 0, "Relação moderada positiva", "Relação moderada negativa" ),
        IF ( _r > 0, "Relação forte positiva", "Relação forte negativa" )
    )
```

## 07 Navegação & Contexto

| Medida | Formato | Descrição |
|---|---|---|
| `Filtros Ativos (qtd)` | `0` |  |
| `Texto Filtros Ativos` | `texto/HTML` |  |

### Filtros Ativos (qtd)
```dax
Filtros Ativos (qtd) =
INT ( ISFILTERED ( Dim_Tempo[Ano] ) ) + INT ( ISFILTERED ( Dim_Tempo[Safra] ) )
    + INT ( ISFILTERED ( Dim_Cultura[Cultura] ) ) + INT ( ISFILTERED ( Dim_Regiao[UF] ) )
    + INT ( ISFILTERED ( Dim_Fazenda[NomeFazenda] ) ) + INT ( ISFILTERED ( Dim_Fazenda[TipoPropriedade] ) )
    + INT ( ISFILTERED ( Dim_Cliente[Canal] ) ) + INT ( ISFILTERED ( Fato_Vendas[TipoContrato] ) )
```

### Texto Filtros Ativos
```dax
Texto Filtros Ativos =
VAR _n = [Filtros Ativos (qtd)]
RETURN IF ( _n = 0, "Visão consolidada · sem filtros", _n & IF ( _n = 1, " filtro ativo", " filtros ativos" ) )
```

## 08 HTML & Tooltips

| Medida | Formato | Descrição |
|---|---|---|
| `HTML Header Executiva` | `texto/HTML` | Cabeçalho com marca, navegação, contexto e botão Filtros. |
| `HTML Header Producao` | `texto/HTML` |  |
| `HTML Header Custos` | `texto/HTML` |  |
| `HTML KPIs Executivo` | `texto/HTML` | Cinco cards animados com YoY, sparkline, mix e detalhe no hover. |
| `HTML Leitura Executiva` | `texto/HTML` | Narrativa automática com 4 insights calculados. |
| `HTML Verao x Safrinha` | `texto/HTML` | Comparativo animado entre os ciclos de safra. |
| `HTML KPIs Custos` | `texto/HTML` | Faixa de KPIs de custo, margem, break-even e clima. |
| `HTML Painel Filtros` | `texto/HTML` | Fundo animado (slide-in) do painel lateral de filtros. |
| `HTML Capa Pulso` | `texto/HTML` | Anel pulsante ao redor do botão INICIAR. |
| `HTML Capa Manifesto` | `texto/HTML` | Manifesto da marca com fade-in. |
| `HTML Tooltip Fazenda` | `texto/HTML` | Tooltip rico para fazenda / fazenda × cultura. |
| `HTML Tooltip Comercial` | `texto/HTML` | Tooltip rico para cliente, contrato e período. |
| `HTML Tooltip Custos` | `texto/HTML` | Tooltip rico para categoria de custo, clima (UF × Safra) e cultura. |

## 08 HTML & Tooltips — expressões completas

As medidas HTML são longas (CSS + HTML concatenados). O código completo está em `AgroCerrado.SemanticModel/definition/tables/_Medidas.tmdl` e os templates legíveis em `HTML_CSS/`.
