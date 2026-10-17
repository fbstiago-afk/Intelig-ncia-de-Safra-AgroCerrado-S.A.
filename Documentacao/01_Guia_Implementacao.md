# AgroCerrado S.A. — Inteligência de Safra
### Guia de implementação do projeto Power BI (PBIP / PBIR + TMDL)

> “Cultivar o futuro do Cerrado com excelência e responsabilidade.”

---

## 1. Estrutura entregue

```
AgroCerrado_Dashboard/
├─ AgroCerrado.pbip                     ← abra este arquivo no Power BI Desktop
├─ AgroCerrado.SemanticModel/           ← modelo semântico (TMDL)
│  └─ definition/
│     ├─ model.tmdl · database.tmdl · relationships.tmdl
│     ├─ expressions.tmdl              ← parâmetro CaminhoBase (caminho do Excel)
│     └─ tables/                       ← 7 dimensões, 5 fatos, _Medidas (109 medidas)
├─ AgroCerrado.Report/                  ← relatório (PBIR)
│  ├─ definition/pages/                ← 4 páginas + 3 páginas de tooltip
│  ├─ definition/bookmarks/            ← 6 bookmarks (abrir/fechar filtros por página)
│  └─ StaticResources/RegisteredResources/
│     ├─ AgroCerrado_Tema.json         ← tema da marca (aplicado)
│     ├─ Tela_Inicial_AgroCerrado.png  ← capa
│     └─ Fundo_Paginas_AgroCerrado.png ← fundo das páginas internas (opacidade baixa)
├─ Dados/Base_AgroCerrado_PowerBI.xlsx  ← base (cópia com nome limpo)
├─ Assets/                              ← imagens originais + tema avulso
├─ Deneb/                               ← 3 specs Vega-Lite + config da marca
├─ HTML_CSS/                            ← templates HTML/CSS legíveis de cada medida HTML
└─ Documentacao/                        ← este guia + catálogo DAX
```

## 2. Pré-requisitos

| Item | Detalhe |
|---|---|
| Power BI Desktop | Versão 2025 ou mais recente (formato PBIR + TMDL). Em versões antigas, ative em *Opções → Recursos de visualização*: “Armazenar relatórios usando formato PBIR” e “Formato TMDL”. |
| Visual **HTML Content** (Daniel Marsh-Patrick) | AppSource, id `htmlContent443BE3AD55E043BF878BED274D3A6855`. O Desktop baixa sozinho ao abrir; se a política da empresa bloquear, instale em *Obter mais visuais*. |
| Visual **Deneb** (Vega/Vega-Lite) | AppSource, id `deneb7E15AEF80B9E4D4F8E12924291ECE89A`. Idem. |
| Fontes (opcional) | Montserrat e Open Sans instaladas no Windows deixam os elementos HTML idênticos ao brandbook. Sem elas, o fallback é Segoe UI. |

## 3. Como abrir (3 passos)

1. Mantenha a pasta `AgroCerrado_Dashboard` em `E:\Projetos BI\13 - Agro Serrado\` (caminho curto evita o limite de 260 caracteres do Windows).
2. Dê duplo clique em **`AgroCerrado.pbip`**.
3. Clique em **Atualizar**. Se mover a pasta: *Transformar dados → Editar parâmetros → `CaminhoBase`* e informe o novo caminho do Excel.

> O parâmetro aponta para `...\AgroCerrado_Dashboard\Dados\Base_AgroCerrado_PowerBI.xlsx`. Todas as consultas M pulam as 2 linhas de título das abas, promovem cabeçalhos, selecionam colunas e tipam (inclui colunas PT-BR `MesAbrevPT`, `MesAno`, `AnoMesNum` no calendário).

## 4. Imagens — como foram aplicadas e como trocar

| Página | Imagem | Aplicação |
|---|---|---|
| 1 · Início | *Tela Inicial AgroCerrado no Campo* | Plano de fundo da página, 1920×1080, ajuste **Ajustar**, transparência 0 %. O botão “INICIAR” já desenhado na arte recebe por cima um botão invisível com ação de navegação + anel dourado pulsante (HTML). |
| 2, 3, 4 | *Paisagem Agrícola ao Amanhecer com Arcos Dourados* | Versão “clean” pré-processada com **52 % de opacidade sobre creme #F7F5EE** (`Fundo_Paginas_AgroCerrado.png`). A original está em `Assets/`. |

Para trocar: *Formatar página → Plano de fundo da tela → Imagem → Procurar*; escolha **Ajustar** e transparência 0 % (para a original sem tratamento, use 45–55 % de transparência).

## 5. Arquitetura de UX

### 5.1 Navegação personalizada (sem o navegador nativo)
O cabeçalho é **uma medida HTML** (`[HTML Header …]`) que desenha marca, três “pills” de página (a ativa em branco com ponto dourado), o contexto do período e o botão **Filtros** com contador de filtros ativos. Sobre cada área clicável existe um **botão transparente** (Power BI action button) com estado *hover* translúcido — o visual é 100 % HTML/CSS; o clique é nativo e confiável. As posições usam **porcentagem**, por isso o HTML e os botões continuam alinhados em qualquer zoom.

- Logotipo → volta para a capa
- Pills → `Visão Executiva` · `Produção & Comercial` · `Custos & Clima`

### 5.2 Painel de filtros oculto
- Botão **Filtros** → bookmark `Abrir filtros · <página>` (só exibição, *dados não capturados*), revelando: véu escuro, painel lateral HTML com animação *slide-in* e **8 segmentações**: Ano, Safra, Cultura, UF, Fazenda, Tipo de propriedade, Canal, Tipo de contrato.
- Segmentações **sincronizadas** entre as 3 páginas (grupos `sgAno`, `sgSafra`…).
- **Limpar filtros** = ação nativa *Limpar todas as segmentações*. **Aplicar**, **✕** ou clique no véu → bookmark `Fechar filtros`.
- O painel de filtros nativo do Power BI fica oculto (relatório limpo para o usuário final).

### 5.3 Tooltips HTML ricos (todas as visualizações de dados)
| Página de tooltip | Usada por | Conteúdo |
|---|---|---|
| `TT Fazenda` | Ranking de fazendas (Deneb) · Heatmap (Deneb) | Município/UF, tipo, área · receita, margem, produtividade, custo/ha, ranking “#n de 10”, YoY · insight “x % acima/abaixo da média do portfólio” · mini-gráfico de receita por cultura |
| `TT Comercial` | Receita mensal · Contratos · Top clientes · Preço por cultura | Receita, volume, preço médio, contratos, ticket, variação (MoM no mês / vs ano anterior no trimestre / YoY) · share e prêmio de preço vs média · mini-linha trimestral |
| `TT Custos` | Dispersão clima · Custos por categoria · Custo/ha × margem | Três modos automáticos: **categoria** (% do total, variável, fornecedor principal, impacto de 1 % de economia), **clima** (chuva, acumulado, temperatura, umidade, produtividade) e **cultura** (custo/ha, margem/ha, break-even) |

Os cards HTML (KPIs, leitura executiva, verão × safrinha) têm **tooltip próprio em CSS**: ao passar o mouse, o card sobe, ganha borda dourada e revela um verso verde-escuro com o detalhe.

### 5.4 Páginas
| # | Página | Elementos (máx. 6 por tela) |
|---|---|---|
| 1 | **Início** | Capa full-screen · anel pulsante no INICIAR · manifesto com fade-in |
| 2 | **Visão Executiva** | 5 KPIs HTML (receita c/ sparkline, produção c/ mix, margem, área c/ intensidade, produtividade c/ break-even) · receita mensal × preço · **Deneb lollipop** ranking de fazendas · **leitura executiva automática** (4 insights calculados) |
| 3 | **Produção & Comercial** | **Deneb heatmap** fazenda × cultura (índice vs média) · Verão × Safrinha (HTML) · receita por contrato · top 8 clientes · preço trimestral por cultura |
| 4 | **Custos, Rentabilidade & Clima** | 6 KPIs HTML (custo, desembolso, margem, break-even, clima, correlação r) · **Deneb dispersão** chuva × produtividade com regressão e R² · desembolso por categoria · custo/ha × margem % por cultura |

## 6. Modelo semântico
- Estrela com 17 relacionamentos 1:N de direção única. `Dim_Tempo` marcada como **tabela de datas** (`Data` como chave).
- `Fato_Clima` liga-se a `Dim_Regiao`; as medidas de clima usam `TREATAS(VALUES(Dim_Fazenda[RegiaoKey]), Dim_Regiao[RegiaoKey])` para que o filtro de **Fazenda** também filtre o clima — sem relacionamento bidirecional.
- **Unidades separadas:** grãos em sacas de 60 kg e algodão em arrobas nunca são somados (`Produção Grãos (sc)` × `Produção Algodão (@)`).
- **Dois conceitos de custo**, explicitados nos títulos: *Custo de Produção* (Fato_Producao, base da margem por cultura) e *Desembolso Operacional* (Fato_Custos, quebra por categoria/fornecedor).
- **YoY comparável:** `X (Ano Ref)` usa o último ano do filtro; `X (Ano Ant)` usa o mesmo período do ano anterior até a mesma data (em 2026, dados até 15/out → compara com 01/jan–15/out/2025).
- `discourageImplicitMeasures` ligado: todo número vem de medida governada.

Catálogo completo: `Documentacao/02_Catalogo_Medidas_DAX.md`.

## 7. Tema
`AgroCerrado_Tema.json` — paleta oficial (#1B5E20, #2E7D32, #C9A227, #333333 + apoio terroso), cartões brancos 5 % transparentes, raio 16 px, sombra verde-noite suave, títulos Segoe UI Semibold verde-escuro, eixos/legendas cinza discreto, gridlines pontilhadas, HTML Content sem moldura. Pode ser reaplicado em qualquer relatório: *Exibição → Temas → Procurar temas*.

## 8. Checklist depois de abrir
1. Atualizar dados (barra de status sem erros).
2. Se algum visual mostrar “Não é possível exibir” → instalar HTML Content / Deneb (seção 2).
3. Em um visual Deneb vazio, confira os campos em *Values* (lista no `Deneb/LEIA-ME.md`).
4. Testar: INICIAR → navegação pelas pills → Filtros → selecionar Ano 2025 → Aplicar → Limpar.
5. Publicar: *Página Inicial → Publicar*. No serviço, o bookmark e a navegação funcionam com clique simples (no Desktop, **Ctrl + clique**).

## 9. Por que estas decisões (resumo de design)
- **Poucos elementos, grandes:** no máximo 6 blocos por página, grid de 24 px, respiro generoso — leitura de “consultoria”, não de painel operacional.
- **Hierarquia:** KPI → tendência → ranking → explicação. A leitura executiva transforma números em frases acionáveis.
- **Cor com intenção:** verde para desempenho, dourado só para destaque (top 3, linha de tendência, preço), vermelho apenas para margem negativa.
- **Honestidade analítica:** unidades nunca misturadas, custo definido no título, YoY sobre períodos comparáveis, correlação exibida com sua força real (r ≈ 0,09 → “relação fraca”).
