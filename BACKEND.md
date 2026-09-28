# Origem — anotações de backend (para a próxima sessão)

Tudo abaixo está **apenas simulado no frontend** (`Origem.dc.html`). Nada persiste.

## 1. Autenticação
- Login com **Google OAuth** (botão "Continuar com Google" já existe no modal).
- Login/cadastro por **e-mail + senha** (campos existem, sem validação).
- Sessão: usuário atual está em `state.user` (string fake `"Cliente Origem"`).
- Regra de negócio já refletida na UI: **é preciso estar logada para comprar** — `addToBag` abre o modal de login quando não há usuário.
- Faltando: recuperação de senha, verificação de e-mail, logout, área da conta (hoje só mostra um toast).

## 2. Catálogo de produtos
- Hoje os produtos são um array fixo `PRODUCTS` no arquivo (9 peças).
- Campos usados por produto: `id, name, category, price, old (preço antes da promoção), desc, specs[], colors[[nome,hex]], photos[]`.
- Precisa vir de API/CMS: CRUD de produtos, categorias, estoque por tamanho/cor, ordenação, paginação.
- **Fotos reais**: hoje são placeholders (blocos tonais). Precisa de upload/CDN e múltiplas fotos por produto (a galeria do detalhe já suporta N fotos).

## 3. Promoções
- Promoção é inferida de `old != null` (mostra "Promoção" em verde + preço original riscado).
- Precisa: regra de desconto (%, valor, cupom), período de vigência, badge automática.

## 4. Busca
- Busca atual é filtro em memória por nome/categoria/descrição.
- Precisa: endpoint de busca (com acentuação/typo tolerante), sugestões, histórico.

## 5. Lista de desejos
- `state.wish` (array de ids) — perde tudo ao recarregar.
- Precisa: persistir por usuário logado (e merge do que foi salvo antes do login).

## 6. Sacola / checkout — InfinitePay

Já funciona, **sem backend**, direto do navegador. Regras vindas de `PERGUNTAS-CLIENTE.md`.

**Como está ligado**: `POST https://api.checkout.infinitepay.io/links` com
`{ handle, items[{quantity, price, description}], order_nsu, redirect_url }` e
redireciona para a `url` que vem na resposta. O endpoint responde
`access-control-allow-origin: *` e não pede chave, então roda no GitHub Pages.
Preços vão **em centavos**. A InfiniteTag é `evelyn-geovana-0fc` (sem o `$`).

**O que está implementado**
- Carrinho real: agrupa por peça + tamanho, quantidade, remover, badge no header.
- Frete grátis acima de R$ 299; abaixo disso, tabela por região pelo 1º dígito do CEP.
- Retirada em mãos / entrega local em Londrina, PR (frete zero).
- A API não tem campo de frete: ele entra como **um item a mais** ("Frete · CEP …").
- `order_nsu` carrega a forma de entrega (`origem-cep86010000-…` ou `origem-retirada-londrina-…`).
- Parcelamento exibido conforme o valor: até R$ 300 mostra 5x, acima disso 12x.
- Sem desconto no Pix (o Pix aparece como forma de pagamento no checkout da InfinitePay).

**Pendências reais**
- **Frete é estimado**, não calculado: a tabela por região em `SHIP_BY_REGION` é
  provisória. O cálculo que a cliente pediu (Correios / Melhor Envio) exige chave
  secreta, ou seja, um backend. Enquanto isso, conferir se os valores fecham com o
  que ela paga na postagem.
- **Limite de 5x até R$ 300 é só visual.** O payload não tem campo de parcelas —
  quem manda é a configuração da conta dela no app InfinitePay.
- **Sem `webhook_url`.** A confirmação de pagamento hoje só chega pelo app da
  InfinitePay. Para o site saber que o pedido foi pago, precisa de backend.
- **Preços saem do frontend.** Sem backend não dá para validar o valor antes de
  cobrar; alguém poderia adulterar o payload. Ela vê o valor no app antes de
  postar, o que segura o risco nesse porte, mas some quando houver backend.
- Sacola **não persiste** (recarregar a página esvazia) e **não confere estoque**.
- `redirect_url` volta para a home — falta uma página de "pedido confirmado" que
  leia `receipt_url` / `order_nsu` / `capture_method` da query string.

## 7. Tabela de medidas
- Modal existe com grade PP–GG × Busto/Cintura/Quadril/Comprimento, **todas as células com "—"** aguardando os dados reais da loja.
- Precisa: tabela por categoria de produto (alfaiataria ≠ tricô).

## 8. Provador virtual ("Veja seu tamanho")
- Formulário coleta altura, peso, busto, cintura; o resultado é fixo ("tamanho M").
- Precisa: algoritmo real cruzando medidas × tabela do produto, e retorno de caimento (justo/solto).

## 9. Outros
- Modo escuro é só CSS (`data-theme` no `<html>`); preferência não é salva.
- Instagram `@origembasics` está linkado no rodapé; um feed real exigiria API.
- Sem analytics, sem SEO/meta dinâmicos, sem internacionalização.
