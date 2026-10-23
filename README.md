# Painel de combustíveis

Um site que se atualiza sozinho todo dia útil às 7h (Brasília): coleta Brent, gasolina e diesel internacionais e dólar,
junta com o etanol Cepea e os preços que vocês digitam, calcula variações, tendência e projeção, e diz quanto comprar.

**Sem servidor para cuidar.** Roda de graça no GitHub (agendamento + site). Se algo falhar, o GitHub manda e-mail.

## Instalação (uma vez, ~15 minutos)

1. Crie uma conta em github.com e um repositório novo chamado `painel-combustiveis` (pode ser privado, mas o site
   publicado só funciona em plano pago; no plano gratuito o repositório precisa ser **público**. Os dados são só
   cotações de mercado e os valores que vocês digitarem em `manual.json`, então decidam se isso é aceitável).
2. Em **Add file > Upload files**, envie as pastas `coletor`, `docs`, `data` e os arquivos `config.json`, `manual.json`.
3. A pasta oculta `.github` pode não subir no arraste. Faça assim: **Add file > Create new file**, digite o nome
   `.github/workflows/atualizar.yml` (as barras criam as pastas) e cole o conteúdo do arquivo de mesmo nome.
4. **Settings > Pages**: em *Branch* escolha `main` e a pasta `/docs`. Salve. O endereço do site aparece ali.
5. **Settings > Actions > General > Workflow permissions**: marque *Read and write permissions*.
6. Aba **Actions > Atualizar painel > Run workflow**. Na primeira vez ele baixa 1 ano de histórico. Em 1 ou 2 minutos
   o site está no ar.

## Dia a dia

- **Valores que vocês digitam:** abra `manual.json` no GitHub, clique no lápis, atualize e salve. O painel se
  atualiza sozinho em ~1 minuto.
  - `etanol_cepea` e `gasolina_bomba` (R$/L): alimentam a paridade etanol/gasolina.
  - `preco_distribuidora_gasolina` e `preco_distribuidora_diesel` (R$/L): o que vocês pagam hoje. Mostram a defasagem
    contra o PPI.
- **Ajustes de regra:** `config.json` (limiar de variação, fatores de compra, constantes do PPI).
- **Se algo der errado:** o GitHub avisa por e-mail quando o workflow falha. A faixa vermelha no topo do site também
  lista qual fonte falhou.

## Calibrar o PPI (importante)

O PPI é uma estimativa: `(preço internacional + frete) ÷ litros por galão × dólar + custos`. Os `custos_*_rl` em
`config.json` começam em 1,20 só como ponto de partida. Para calibrar: num dia em que vocês acham que o preço da
distribuidora estava alinhado à paridade, ajuste o custo até o PPI do painel ficar próximo desse preço. Depois disso,
ligue `"usar_defasagem": true` para a recomendação considerar a distância entre o preço da distribuidora e o PPI.

## Limites que vale conhecer

- Yahoo Finance é gratuito mas não oficial: pode mudar o formato e quebrar a coleta. Se acontecer, a faixa vermelha
  avisa, e a correção é pontual no `coletor/coletar.py`.
- O Cepea diário é pago; por isso o etanol é digitado.
- As recomendações são regras simples e transparentes, não previsão de preço. Testem contra o histórico de compras
  de vocês antes de confiar nos fatores 120% / 80%.

## Próximas melhorias possíveis

- Aviso no WhatsApp/Telegram quando a recomendação mudar.
- Backtest: comparar o que o painel teria recomendado com o que foi comprado e o custo real.
- Preços ANP por cidade (levantamento semanal) para comparar com a concorrência de cada posto.
- Recomendação por posto, usando o volume médio e o estoque de cada um.
- Alerta de defasagem (distribuidora muito abaixo da paridade) antes de reajustes.
