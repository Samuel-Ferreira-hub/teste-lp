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

## 6. Sacola / checkout
- "Adicionar à sacola" só dispara um toast; não existe carrinho, frete, pagamento ou pedido.
- Precisa: carrinho, cálculo de frete (regra de frete grátis acima de R$ 299 está só no texto), pagamento, 6x sem juros, e-mails transacionais.

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
