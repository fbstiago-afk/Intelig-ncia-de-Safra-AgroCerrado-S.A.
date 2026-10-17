# Specs Deneb (Vega-Lite)

Os três visuais já vêm configurados dentro do PBIP. Estes arquivos são a fonte para manutenção/versão.

| Arquivo | Página | Campos no "Values" do Deneb |
|---|---|---|
| `01_Ranking_Fazendas_Lollipop.spec.json` | Visão Executiva | `Dim_Fazenda[NomeFazenda]`, `[Receita Bruta]`, `[Margem Bruta %]` |
| `02_Heatmap_Produtividade.spec.json` | Produção & Comercial | `Dim_Fazenda[NomeFazenda]`, `Dim_Cultura[Cultura]`, `[Produtividade (un/ha)]`, `[Índice Produtividade vs Portfólio]`, `[Área Plantada (ha)]` |
| `03_Dispersao_Clima_Produtividade.spec.json` | Custos, Rentabilidade & Clima | `Dim_Regiao[UF]`, `Dim_Tempo[Safra]`, `[Chuva Média Mensal (mm)]`, `[Produtividade Média (sc/ha)]`, `[Área Grãos (ha)]` |

**Para colar manualmente:** selecione o visual → `...` → *Editar* → aba *Specification* (cole o `.spec.json`) e aba *Config* (cole `00_config_AgroCerrado.json`) → *Apply*. Em *Formatar → Vega → Interatividade* mantenha "Tooltips" ligado para que o tooltip de página (HTML) apareça.