# Desafio DevSecOps — Gerenciador de Tarefas

## Sobre o Projeto

Este repositório faz parte do desafio prático do módulo de DevSecOps da ADA Tech.

O projeto consiste em uma pequena aplicação web (um gerenciador de tarefas) que possuía vulnerabilidades propositais e uma pipeline de CI/CD incompleta. O objetivo foi implementar uma esteira de segurança automatizada — capaz de barrar o deploy caso encontre problemas — e corrigir as vulnerabilidades identificadas, para que a aplicação pudesse chegar a produção de forma segura.

## Estado atual

✅ Pipeline completa e funcionando.
✅ Vulnerabilidades corrigidas.
✅ Deploy automatizado via GitHub Pages.

## O que foi implementado

- [x] Secrets Scanning com **Gitleaks**
- [x] SAST com **Semgrep**
- [x] SCA com **Grype**
- [x] Deploy com **GitHub Pages**

## Como a pipeline funciona

A pipeline é executada automaticamente sempre que um novo código é enviado (`push`) para a branch `main`. Ela funciona como uma linha de montagem com pontos de controle: o código só chega à produção se passar por **todos** os passos abaixo, na ordem. Se qualquer etapa de segurança falhar, a pipeline quebra ali mesmo e o deploy **não acontece** — esse é o conceito de *"break the build"*, que garante que nenhuma vulnerabilidade conhecida chegue ao ambiente público.

### 1. 📥 Checkout do Código
Baixa o código do repositório para o ambiente onde a pipeline será executada, para que os passos seguintes tenham acesso aos arquivos do projeto.

### 2. ⚙️ Build
Verifica se os arquivos da aplicação (`src/`) estão presentes e prontos para seguir para as próximas etapas.

### 3. 🔑 Secrets Scanning — Gitleaks
Varre todo o código-fonte e o histórico de commits em busca de segredos expostos, como senhas, chaves de API e tokens.

**Por que isso importa:** se uma chave de API ou senha for deixada no código, qualquer pessoa com acesso ao repositório (ou, no caso de uma aplicação frontend, qualquer visitante do site) pode lê-la e usá-la para acessar sistemas, bancos de dados ou contas de terceiros indevidamente. Esse tipo de vazamento é uma das causas mais comuns de incidentes de segurança reais. Encontrar isso antes do deploy evita que o segredo fique exposto publicamente.

### 4. 🔍 SAST (Static Application Security Testing) — Semgrep
Analisa o código-fonte estaticamente (ou seja, sem executá-lo) em busca de padrões de código conhecidamente inseguros, como uso de funções perigosas ou manipulação insegura de dados vindos do usuário.

**Por que isso importa:** muitas vulnerabilidades — como XSS (Cross-Site Scripting) e injeção de código — vêm de padrões de programação que parecem inofensivos à primeira vista, mas permitem que um atacante execute código malicioso dentro da aplicação. O SAST detecta esses padrões automaticamente, antes mesmo do código ser publicado, funcionando como uma revisão de segurança feita em segundos.

### 5. 📦 SCA (Software Composition Analysis) — Grype
Verifica todas as dependências de terceiros usadas pelo projeto (bibliotecas listadas no `package.json`) e as compara com bancos de dados públicos de vulnerabilidades conhecidas (CVEs).

**Por que isso importa:** aplicações modernas raramente são feitas do zero — elas usam dezenas de bibliotecas externas. Se uma dessas bibliotecas tiver uma versão com uma falha de segurança já catalogada publicamente, a aplicação herda esse risco automaticamente, mesmo que o time nunca tenha escrito nenhum código inseguro. O Grype garante que apenas versões seguras e atualizadas das dependências sigam para produção.

### 6. 🌐 Deploy em Produção
Só é executado se **todos** os passos anteriores tiverem passado com sucesso. Publica o conteúdo da pasta `src/` no GitHub Pages, tornando a aplicação acessível publicamente através de uma URL.

## Vulnerabilidades encontradas e corrigidas

| # | Problema | Ferramenta que detectou | Correção aplicada |
|---|----------|--------------------------|---------------------|
| 1 | Chave de API e senha de banco hardcoded no `script.js` | Gitleaks | Remoção das credenciais do código-fonte |
| 2 | Uso de `eval()` com dado digitado pelo usuário | Semgrep | Remoção do `eval()`, substituído por `console.log` direto |
| 3 | XSS via `innerHTML` com entrada do usuário não sanitizada | Semgrep | Substituição por `textContent`, que trata o conteúdo sempre como texto puro |
| 4 | Exposição do stack trace do erro (`err.stack`) diretamente na tela | Semgrep | Mensagem de erro genérica para o usuário; detalhe técnico mantido apenas no console |
| 5 | Dependências desatualizadas com CVEs conhecidas (`lodash`, `express`, `axios`) | Grype | Atualização para versões corrigidas mais recentes |

## URL de Produção

🔗 **[[Link do GitHub Pages](https://flprbr.github.io/projeto-devsecop-desafio/)]**

---

_Projeto desenvolvido como parte do desafio DevSecOps — ADA Tech._
