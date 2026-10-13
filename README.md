# NFLX Insight — análise de dados da Netflix

Pipeline em Python/Colab e dashboard HTML: coleta rastreável, limpeza e auditoria, EDA, 20 características, comparação de modelos, avaliação temporal e interpretação contábil.

## Problema
Verificar se características históricas de preços, risco e volume permitem superar uma previsão simples do próximo retorno de NFLX; contextualizar resultados com S&P 500 e demonstrações da SEC.

## Fontes e qualidade
- CSV original: 1.009 linhas, preservado em commit fixo.
- Yahoo Finance: 2.206 sessões por série, de 02/01/2018 a 12/10/2026.
- SEC EDGAR: 23.911 observações, 505 conceitos; 34 trimestres, 26 diretos e 8 Q4 derivados.
- Snapshots com SHA-256 e manifesto; tipagem, OHLC, duplicidades e ausências auditadas. Desdobramento 10:1 reconciliado entre fontes sem aplicar ajuste duplicado.
- Calendários NASDAQ/NYSE: nenhuma data ausente ou inesperada na execução original.
- Receita, lucro operacional e líquido reconciliados com valores anuais de 2018 a 2025 sem diferenças.

## Avaliação e resultados observados
Treino: 1.448 linhas até 2023; validação: 252 linhas de 2024; teste: 445 linhas de 2025–2026. Validação interna temporal com gap de um pregão; padronização dentro do pipeline Ridge.

| Modelo | MAE validação (bps) | MAE teste (bps) |
|---|---:|---:|
| Baseline retorno zero | 131,531 | 154,379 |
| Ridge | 132,220 | 155,224 |
| HistGradientBoosting | 134,798 | Não avaliado |

Baseline escolhido na validação e mantido após teste. Nenhum ganho preditivo demonstrado pela Ridge no teste.

## Comparação histórica A/B
Não é teste causal randomizado. A opera abertura-fechamento diariamente; B opera somente se previsão Ridge > 10 bps. Ambas sem alavancagem, caixa com rendimento zero; custos assumidos de 5 bps por ponta.

| Estratégia | Retorno bruto | Retorno líquido |
|---|---:|---:|
| A | −12,371% | −43,845% |
| B | +14,624% | −15,423% |

Diferença média B−A: 8,682 bps/sessão; intervalo bootstrap em blocos aproximado de 95%: [−1,379; 19,327]. Inclui zero. Caixa supera ambas no cenário principal. Custos de transação mudam a conclusão.

## Interpretação financeira
2019Q4: benefício tributário compensou despesas não operacionais. 2026Q1: outras receitas incluíram taxa de rescisão WBD de US$ 2,8 bilhões; margem líquida não representa exclusivamente desempenho operacional recorrente. Fontes SEC e reconciliações estão no notebook e dashboard.

## Executar sem instalação local
Abra `Netflix-stock-analysis.ipynb` no Google Colab e execute de cima para baixo. Informe contato privado para SEC e autorize o Drive quando solicitado. Pacotes são instalados somente no runtime. Salve o notebook separadamente.

`requirements.txt` lista dependências, sem alegar versões reproduzidas. A última célula gera `requirements-run.txt` com as versões efetivamente instaladas. Nova coleta pode mudar valores por revisões dos provedores. Para reprodução exata são necessários os snapshots e ambiente da execução, preservados nos checkpoints do Drive; eles não estão neste pacote.

## Arquivos
- `Netflix-stock-analysis.ipynb`: notebook organizado, sem saídas antigas.
- `dashboard/index.html`: dashboard aprovado, com scripts incorporados.
- `requirements.txt`: dependências diretas.

O notebook gera data/raw, data/processed, reports e exports e salva checkpoint final no Drive. O HTML pode ser hospedado em GitHub Pages. Nenhum repositório foi alterado nesta preparação.

## Limitações
Histórico ajustado retrospectivamente, não point-in-time. EDA incluiu todo o período antes do teste. S&P 500 é índice de preços sem reinvestimento de dividendos. Alvo fechamento-fechamento difere da execução abertura-fechamento. Custos são hipóteses. Q4 derivados entre documentos podem sofrer reclassificações; reconciliação anual não valida sozinha cada componente trimestral. Filed não é data do anúncio. APIs sujeitas a bloqueios e revisões. Não houve experimento A/B com usuários.

## Verificação desta revisão
Sintaxe das células Python validada e dependências de ordem corrigidas. O usuário confirmou execução completa em uma sessão limpa do Colab em 13/10/2026, com checkpoint final salvo. Os resultados numéricos documentados são da execução original fornecida; não foram reextraídos do checkpoint final.
