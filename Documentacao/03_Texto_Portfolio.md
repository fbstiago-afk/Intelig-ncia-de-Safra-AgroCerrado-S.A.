# AgroCerrado S.A. — Inteligência de Safra
### Dashboard executivo em Power BI para uma operação de grãos no Cerrado

**Descrição curta**
Painel executivo para uma produtora de soja, milho, sorgo e algodão com 10 fazendas em 5 estados. Junta produção, vendas, custos e clima numa experiência com cara de aplicativo: capa de marca, navegação própria, filtros que ficam escondidos e tooltips que explicam cada número.

**Contexto e objetivos**
Empresas do agro costumam ter os dados espalhados: a colheita fica na fazenda, os contratos no comercial, o custo no financeiro e a chuva numa planilha à parte. O objetivo foi colocar tudo isso numa única leitura, que a diretoria e as tradings parceiras entendam em menos de um minuto:
- Quanto produzimos, vendemos e ganhamos neste ano, comparado ao mesmo período do ano anterior?
- Quais fazendas e culturas geram valor, e quais estão perdendo dinheiro?
- Quanto pesam o tipo de contrato (Barter, a termo, à vista) e o canal de exportação?
- O clima explica a produtividade, ou o manejo pesa mais?

**Metodologia e abordagem**
- **Modelo estrela** com 7 dimensões e 5 fatos (produção, vendas, custos, estoque e clima), feito em PBIP/TMDL, versionável em Git.
- **109 medidas DAX** em 8 pastas: produção, comercial, custos, rentabilidade, inteligência de tempo, clima, navegação e HTML. Usam VAR, DIVIDE, KEEPFILTERS, TREATAS e um YoY comparável (YTD contra o mesmo período do ano anterior).
- **Identidade visual aplicada de ponta a ponta:** tema JSON com as cores da marca, capa com botão INICIAR, fundo de paisagem com opacidade baixa e no máximo 6 blocos por página.
- **UX de aplicativo:**
  - menu de páginas em HTML com botões invisíveis por cima;
  - painel lateral de filtros com 8 segmentações sincronizadas, aberto por bookmark;
  - cards animados em CSS que viram para mostrar o detalhe quando o mouse passa por cima.
- **3 visuais Deneb (Vega-Lite):** ranking lollipop, heatmap de produtividade e dispersão clima × produtividade com regressão.
- **Tooltips HTML ricos** em todos os gráficos, com 3 páginas de tooltip que mudam de conteúdo conforme o contexto: fazenda, cliente/período ou custo/clima.
- **Leitura executiva automática:** a medida escreve 4 insights em texto conforme os filtros ativos.

**Desafios e aprendizados**
- **Unidades diferentes:** algodão é medido em arroba e grãos em saca. As duas nunca são somadas, para não inflar o KPI.
- **Dois custos diferentes:** custo de produção (base da margem) e desembolso operacional (por categoria) aparecem separados e com nome explícito no título.
- **Clima é regional, fazenda é local:** um TREATAS leva o filtro de fazenda até o clima sem usar relação bidirecional.
- **Ano incompleto:** 2026 vai só até outubro, então o YoY compara o acumulado do ano com o mesmo período de 2025, nunca com o ano cheio.
- **HTML com clique nativo:** o desenho é todo em HTML/CSS e o clique fica num botão transparente. Posições em porcentagem mantêm o alinhamento em qualquer zoom.

**Insights e descobertas**
- **Receita em alta:** R$ 1,06 bi em 2026 até outubro (+70% contra o mesmo período de 2025), com margem bruta de 48,4%.
- **Sorgo dá prejuízo:** margem de −17%. É a principal alavanca de revisão de mix na safrinha.
- **Algodão é a cultura mais rentável:** margem de 74%, mesmo com custo de R$ 9,6 mil/ha.
- **Área grande não significa margem alta:** a Fazenda Horizonte Verde tem a maior área e a menor margem (17,6%).
- **Barter domina a receita:** responde por 44%, uma proteção natural contra o custo de insumos. Exportação gera 58%.
- **Fertilizantes são o maior desembolso:** 34% do total, o principal alvo de negociação.
- **Chuva explica pouco:** a correlação entre chuva e produtividade é fraca (r = 0,09). Manejo e mix de culturas pesam mais.
- **Folga confortável:** o break-even dos grãos é de 38 sc/ha, contra 66,9 sc/ha produzidos (cerca de 29 sc/ha de margem de segurança).

**Resultados de impacto**
- **Uma fonte da verdade** substitui 5 planilhas de áreas diferentes, com atualização em um clique.
- **Leitura executiva em menos de 60 segundos:** KPIs com YoY, ranking e insights escritos automaticamente.
- **Decisões que viram ação:** revisar o sorgo na safrinha, renegociar fertilizantes e auditar o custo/ha da Horizonte Verde.
- **Pronto para escalar:** formato PBIP/TMDL em Git, tema e specs reutilizáveis, e modelo que aceita novas fazendas e safras sem retrabalho.
- **Experiência premium:** navegação, filtros e tooltips no padrão de consultoria e grande trading, que aumenta a adoção pela diretoria.
