# Blog Henrique Max

[![Jekyll](https://img.shields.io/badge/Jekyll-CC0000?style=for-the-badge&logo=jekyll&logoColor=white)](https://jekyllrb.com/)
[![GitHub Pages](https://img.shields.io/badge/GitHub%20Pages-222222?style=for-the-badge&logo=github&logoColor=white)](https://pages.github.com/)
[![Chirpy Theme](https://img.shields.io/badge/Theme-Chirpy-blue?style=for-the-badge)](https://github.com/cotes2020/jekyll-theme-chirpy)

## 🚀 Sobre

Blog pessoal sobre desenvolvimento, trading e tecnologia usando o tema [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) para Jekyll.

**Site:** [henriquemax.com.br](http://henriquemax.com.br)

## 🛠️ Pré-requisitos

- [Ruby](https://www.ruby-lang.org/pt/) (recomendado 3.x)
- [Bundler](https://bundler.io/)
- [Git](https://git-scm.com/)

## 📥 Instalação

1. Clone o repositório:
   ```bash
   git clone https://github.com/rickmax/rickmax.github.io.git
   cd rickmax.github.io
   ```

2. Instale as dependências:
   ```bash
   gem install bundler
   bundle install
   ```

## 🔧 Como rodar localmente

Execute o comando abaixo e acesse `http://localhost:4000` no seu navegador:

```bash
bundle exec jekyll serve --livereload
```

Se precisar parar o servidor, pressione `Ctrl+C` no terminal.

## 📝 Adicionando conteúdo

### Criando um novo post

1. Crie um arquivo na pasta `_posts/` com o formato: `YYYY-MM-DD-titulo-do-post.md`
2. Adicione o frontmatter:
   ```yaml
   ---
   title: "Título do Post"
   date: 2024-01-15 10:00:00 -0300
   categories: [Categoria]
   tags: [tag1, tag2]
   ---
   ```

### Adicionando páginas ao menu

As páginas do menu ficam na pasta `_tabs/` e devem ter o frontmatter:
```yaml
---
title: Nome da Página
icon: fas fa-icon-name
order: 1
---
```

## 🚀 Deploy

Este blog usa GitHub Pages com branch `gh-pages` para deploy automático.

### Passo a passo para deploy:

1. **Faça suas alterações no branch `master`:**
   ```bash
   git add .
   git commit -m "Suas alterações"
   git push origin master
   ```

2. **Atualize o branch `gh-pages` (branch de deploy):**
   ```bash
   git push origin master:gh-pages --force
   ```

3. **Aguarde o deploy:** O GitHub Pages processa automaticamente em 5-10 minutos.

### Configuração de domínio personalizado

Para usar um domínio personalizado como `henriquemax.com.br`:

1. **Configure o DNS no seu provedor:**
   - Adicione registros A apontando para os IPs do GitHub Pages:
     - `185.199.108.153`
     - `185.199.109.153`
     - `185.199.110.153`
     - `185.199.111.153`

2. **Arquivo CNAME:** Já configurado com `henriquemax.com.br`

3. **Configuração no GitHub:**
   - Acesse Settings → Pages no repositório
   - Verifique se o branch `gh-pages` está selecionado
   - Confirme se o domínio personalizado está configurado

### Troubleshooting

- **Site não atualiza:** Verifique se o branch `gh-pages` foi atualizado
- **Erro 404:** Aguarde a propagação do DNS (até 24h)
- **Build falha:** Verifique os logs em Actions do GitHub

## 📁 Estrutura do projeto

```
├── _config.yml          # Configurações do site
├── _posts/              # Posts do blog
├── _tabs/               # Páginas do menu lateral
├── _data/               # Dados do site
├── _includes/           # Componentes reutilizáveis
├── _layouts/            # Templates das páginas
├── _sass/               # Estilos SCSS
├── assets/              # Assets estáticos
├── CNAME                # Configuração de domínio
└── README.md            # Este arquivo
```

## 📄 Licença

Código aberto sob a [MIT LICENSE](LICENSE). 





