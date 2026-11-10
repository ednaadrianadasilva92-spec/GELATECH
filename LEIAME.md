# GELATECH: Tecnologia que conecta produção, organização e empreendedorismo

Projeto Integrador Desenvolvedor front-end (UC5), Técnico em Informática para Internet, Senac MT.

## Como abrir no VS Code

1. Abra a pasta `geladinho-facil` no VS Code.
2. Instale a extensão Live Server.
3. Clique com o botão direito em `index.html` e escolha "Open with Live Server".

O sistema não abre com dois cliques no arquivo, porque o navegador bloqueia a leitura do `catalogo.json` fora de um servidor.

## Um arquivo HTML, três áreas, com login

O sistema tem um único `index.html` com três áreas:

| Endereço | Quem usa | O que faz |
| --- | --- | --- |
| `index.html` (sem nada no final) ou `#entrar` | Produtora | Tela de login. No primeiro acesso, ela cria usuário e senha e recebe um código de recuperação |
| `index.html#cardapio` | Cliente | Cardápio, carrinho e pedido pelo WhatsApp (use este link no QR Code) |
| `index.html#painel` (exige login) | Produtora | Início, Pedidos, Fidelidade, Produção e compras, Precificação, Financeiro e DRE, Relatórios, Configurações |

O cardápio é público. O painel só abre depois do login, e o botão Sair fica no topo do painel. A senha não é gravada: o sistema guarda o hash SHA-256 da senha com um sal aleatório, só no aparelho. Login com servidor, válido em qualquer aparelho, fica para a UC11.

Para a banca: no primeiro acesso, crie qualquer usuário e senha e anote o código de recuperação. Cada tela do painel tem seu endereço: `#painel-pedidos`, `#painel-financeiro` e assim por diante. Quem troca de área é o arquivo `js/app.js`.

## Primeiro teste

No painel, toque em "Carregar dados de demonstração". O sistema cria 9 semanas de pedidos e despesas fictícios, com cerca de 110 geladinhos por semana. Os telefones de demonstração são inválidos de propósito: as mensagens abrem o WhatsApp sem destinatário.

## Antes de entregar para a cliente

1. Em `data/catalogo.json`, preencher o WhatsApp da produtora (com DDD, só números) e os preços reais de venda.
2. Trocar `"precosDeExemplo": true` por `false`.
3. No painel, em Relatórios, tocar em "Apagar todos os dados".
4. Em Precificação, cadastrar os preços reais dos ingredientes.

## Publicar no GitHub Pages

1. Criar um repositório e enviar todos os arquivos desta pasta.
2. Em Settings, Pages, escolher a branch `main` e a pasta raiz.
3. A tela inicial fica em `https://USUARIO.github.io/REPOSITORIO/`, o cardápio em `.../#cardapio` e o painel em `.../#painel`.
4. O QR Code da placa aponta para o cardápio, nunca para o painel.

## Power BI

1. No painel, em Relatórios, baixar `pedidos.csv`, `sabores.csv` e `despesas.csv`.
2. No Power BI Desktop: Obter dados, Texto/CSV, delimitador ponto e vírgula.
3. Em Transformar dados, alterar o tipo das colunas numéricas usando a localidade Inglês (Estados Unidos).
4. Criar a tabela `Calendario = CALENDARAUTO()` e ligar a `pedidos[data]` e `despesas[data]`; ligar `pedidos[sabor]` a `sabores[sabor]`.
5. Criar as medidas DAX do documento do projeto.

## Organização do código

| Arquivo | Equipe | Conteúdo |
| --- | --- | --- |
| `js/util.js` | Integração | Moeda, datas, link do WhatsApp, download |
| `js/api.js` | Integração | Leitura assíncrona do catálogo (API simulada) |
| `js/armazenamento.js` | Integração | localStorage, configurações, preço de venda |
| `js/vitrine.js` | 1. Vitrine e WhatsApp | Cardápio e pedido |
| `js/pedidos.js` | 2. Pedidos e fidelidade | Registro, status, mensagens prontas |
| `js/fidelidade.js` | 2. Pedidos e fidelidade | Estrelas, brindes, cartão |
| `js/producao.js` | 3. Produção e precificação | Plano de produção e lista de compras |
| `js/precificacao.js` | 3. Produção e precificação | Custo e preço sugerido |
| `js/financeiro.js` | 4. Financeiro e Power BI | Despesas e DRE |
| `js/relatorios.js` | 4. Financeiro e Power BI | CSV, backup, dados de demonstração |
| `js/painel.js` | Integração | Menu, navegação, Início, Configurações |
| `js/importacao.js` | 2. Pedidos e fidelidade | Lê a mensagem do pedido colada do WhatsApp e registra no painel |
| `js/nuvem.js` | Integração | Versão 2: pedidos automáticos pelo Firebase (desligado até configurar) |
| `firestore.rules` | Integração | Regras de segurança do banco de dados na nuvem |
| `js/acesso.js` | Integração | Login, primeiro acesso, código de recuperação, troca de senha |
| `js/app.js` | Integração | Troca entre login, cardápio e painel, e bloqueio do painel sem login |
| `sw.js`, `manifest.json` | 1. Vitrine e WhatsApp | Instalação como aplicativo (PWA) |

## Limites conhecidos

- Os dados do painel ficam só no aparelho em que ele é usado. Faça backup toda semana (Relatórios).
- Preço alterado na Precificação só chega ao cardápio depois de publicar o novo `catalogo.json`.
- Respostas automáticas dentro do WhatsApp exigem a API oficial do WhatsApp Business e um servidor: evolução prevista para a UC11.

## Versão em arquivo único

A pasta `arquivo-unico` tem um `index.html` com estilo, código, imagens e catálogo embutidos. Ele abre com dois cliques, sem Live Server, e pode ser publicado sozinho no GitHub Pages ou no Netlify. Serve para demonstração e para a banca. Para programar, use os arquivos da pasta principal: o arquivo único não muda quando o código é alterado.

## Pedidos que chegam pelo WhatsApp

O cardápio monta a mensagem com um código (ex.: `Código do pedido: GT-MWBAIQQB8S`), os sabores, o total, o nome e o WhatsApp do cliente. No painel, em Pedidos > Colar pedido do WhatsApp, a produtora cola a mensagem, confere e toca em Registrar pedido. O sistema preenche tudo e recusa o mesmo pedido duas vezes.

## Pedidos automáticos (versão 2, Firebase)

Com o Firebase ligado, o pedido feito no cardápio entra sozinho no painel, sem colar nada. O Firebase é um serviço do Google com plano gratuito (Spark); confira os limites atuais na página de preços do Firebase antes de usar. Os nomes e WhatsApps dos clientes passam a ficar guardados nos servidores do Google: avise os clientes (o cardápio já mostra um aviso) e trate o assunto com a turma à luz da LGPD.

Esta parte do código não foi testada com um projeto Firebase real. Faça o teste do passo 9 antes de entregar à produtora.

1. Acesse console.firebase.google.com com uma conta Google e clique em Adicionar projeto. Nome: gelatech. O Google Analytics pode ficar desligado.
2. No menu Criação (Build), abra Firestore Database > Criar banco de dados. Escolha o local southamerica-east1 (São Paulo) e o modo de produção.
3. Na aba Regras do Firestore, apague o que estiver lá, cole o conteúdo do arquivo `firestore.rules`, troque `EMAIL_DA_PRODUTORA@exemplo.com` pelo e-mail que a produtora vai usar e clique em Publicar.
4. No menu Authentication, clique em Vamos começar, ative E-mail/senha e, na aba Usuários, adicione o e-mail e a senha da produtora.
5. Na engrenagem > Configurações do projeto > Seus apps, clique no ícone da Web (`</>`), dê o apelido gelatech-web e registre. Copie o objeto `firebaseConfig` que aparece.
6. Em `js/nuvem.js`, troque `const FIREBASE_CONFIG = null;` por `const FIREBASE_CONFIG = { ...o objeto copiado... };`. Esses dados podem ficar públicos: a proteção está nas regras do passo 3.
7. Em Authentication > Configurações > Domínios autorizados, confira se o endereço do site (Netlify ou GitHub Pages) está na lista; se não estiver, adicione.
8. Publique o site de novo. No painel, em Configurações > Pedidos automáticos, entre com o e-mail e a senha do passo 4.
9. Teste: faça um pedido no cardápio em outro celular. Em poucos segundos ele deve aparecer em Pedidos, com o aviso "Novo pedido de ... entrou no painel". Confira também, no Firestore, que o pedido ficou com `importado: true`.

O botão Colar pedido do WhatsApp continua funcionando com a nuvem ligada: serve de plano B se a internet falhar.
