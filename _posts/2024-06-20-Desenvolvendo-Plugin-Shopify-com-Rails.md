---
title: "Como Desenvolver um Plugin Shopify com Ruby on Rails"
date: 2024-06-20 16:00:00 -0300
categories: [Web, E-commerce]
tags: [shopify, ruby-on-rails, plugin, e-commerce, api, webhook]
---

O Shopify é uma das plataformas de e-commerce mais populares do mundo, e desenvolver plugins (apps) para ela pode ser muito lucrativo. Neste artigo, vou mostrar como criar um plugin Shopify completo usando Ruby on Rails.

## 🛍️ O que são Shopify Apps?

Os Shopify Apps são aplicações que estendem as funcionalidades da plataforma. Podem ser:
- **Públicos**: Disponíveis na Shopify App Store
- **Privados**: Para uso interno de uma loja específica
- **Personalizados**: Desenvolvidos sob demanda

## 🚀 Configuração Inicial

### **1. Criando o Projeto Rails**

```bash
# Criar novo projeto Rails
rails new shopify_app --database=postgresql
cd shopify_app

# Adicionar gems necessárias
```

### **2. Gemfile Essencial**

```ruby
# Gemfile
gem 'shopify_app', '~> 21.0'
gem 'shopify_api', '~> 12.0'
gem 'dotenv-rails'
gem 'sidekiq'
gem 'redis'
gem 'pg'

group :development, :test do
  gem 'byebug'
  gem 'rspec-rails'
end
```

### **3. Configuração do ShopifyApp**

```bash
# Instalar gems
bundle install

# Gerar configuração do Shopify
rails generate shopify_app
```

## ⚙️ Configuração Básica

### **1. Variáveis de Ambiente**

```bash
# .env
SHOPIFY_API_KEY=sua_api_key
SHOPIFY_API_SECRET=sua_api_secret
SHOPIFY_APP_URL=https://seu-app.ngrok.io
```

### **2. Inicializer do Shopify**

```ruby
# config/initializers/shopify_app.rb
ShopifyApp.configure do |config|
  config.application_name = "Meu Plugin Shopify"
  config.api_key = ENV['SHOPIFY_API_KEY']
  config.secret = ENV['SHOPIFY_API_SECRET']
  config.scope = "read_products,write_products,read_orders"
  config.embedded_app = true
  config.after_authenticate_job = false
  config.api_version = "2023-10"
  config.shop_session_repository = 'Shop'
end
```

## 📊 Modelo de Dados

### **1. Modelo Shop**

```ruby
# app/models/shop.rb
class Shop < ApplicationRecord
  include ShopifyApp::ShopSessionStorage

  validates :shopify_domain, presence: true, uniqueness: true
  validates :shopify_token, presence: true

  def api_version
    ShopifyApp.configuration.api_version
  end
end
```

### **2. Migration**

```ruby
# db/migrate/create_shops.rb
class CreateShops < ActiveRecord::Migration[7.0]
  def change
    create_table :shops do |t|
      t.string :shopify_domain, null: false
      t.string :shopify_token, null: false
      t.datetime :token_expires_at
      t.timestamps
    end

    add_index :shops, :shopify_domain, unique: true
  end
end
```

## 🎛️ Controllers Principais

### **1. Controller de Produtos**

```ruby
# app/controllers/products_controller.rb
class ProductsController < AuthenticatedController
  def index
    @products = ShopifyAPI::Product.find(:all, params: { limit: 50 })
  end

  def show
    @product = ShopifyAPI::Product.find(params[:id])
  end

  def create
    @product = ShopifyAPI::Product.new(product_params)
    
    if @product.save
      redirect_to products_path, notice: 'Produto criado com sucesso!'
    else
      render :new, alert: 'Erro ao criar produto'
    end
  end

  def update
    @product = ShopifyAPI::Product.find(params[:id])
    
    if @product.update_attributes(product_params)
      redirect_to @product, notice: 'Produto atualizado!'
    else
      render :edit, alert: 'Erro ao atualizar produto'
    end
  end

  private

  def product_params
    params.require(:product).permit(:title, :body_html, :vendor, :product_type)
  end
end
```

### **2. Controller Base Autenticado**

```ruby
# app/controllers/authenticated_controller.rb
class AuthenticatedController < ApplicationController
  include ShopifyApp::Authenticated

  private

  def current_shopify_session
    @current_shopify_session ||= ShopifyApp::SessionRepository.retrieve(session[:shopify_session_id])
  end
end
```

## 📡 Trabalhando com Webhooks

### **1. Configuração de Webhooks**

```ruby
# config/initializers/shopify_app.rb
ShopifyApp.configure do |config|
  # ... outras configurações

  config.webhooks = [
    {
      topic: 'orders/create',
      address: 'https://seu-app.com/webhooks/orders/create',
      format: 'json'
    },
    {
      topic: 'products/update',
      address: 'https://seu-app.com/webhooks/products/update',
      format: 'json'
    }
  ]
end
```

### **2. Controller de Webhooks**

```ruby
# app/controllers/webhooks_controller.rb
class WebhooksController < ApplicationController
  include ShopifyApp::WebhookVerification

  def orders_create
    order_data = JSON.parse(request.body.read)
    ProcessOrderJob.perform_later(order_data)
    head :ok
  end

  def products_update
    product_data = JSON.parse(request.body.read)
    UpdateProductCacheJob.perform_later(product_data)
    head :ok
  end
end
```

### **3. Job para Processar Webhooks**

```ruby
# app/jobs/process_order_job.rb
class ProcessOrderJob < ApplicationJob
  queue_as :default

  def perform(order_data)
    # Processar dados do pedido
    order_id = order_data['id']
    customer_email = order_data['customer']['email']
    total_price = order_data['total_price']

    # Sua lógica personalizada aqui
    Rails.logger.info "Novo pedido recebido: #{order_id} - #{total_price}"
    
    # Exemplo: enviar email, atualizar estoque, etc.
    OrderNotificationMailer.new_order(order_data).deliver_now
  end
end
```

## 🎨 Interface do Usuário

### **1. Layout Principal**

```erb
<!-- app/views/layouts/application.html.erb -->
<!DOCTYPE html>
<html>
  <head>
    <title>Meu Plugin Shopify</title>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>
    
    <%= stylesheet_link_tag 'application', 'data-turbo-track': 'reload' %>
    <%= javascript_importmap_tags %>
    
    <!-- Polaris CSS -->
    <link rel="stylesheet" href="https://unpkg.com/@shopify/polaris@latest/build/esm/styles.css">
  </head>

  <body>
    <div id="app">
      <%= yield %>
    </div>
    
    <!-- Shopify App Bridge -->
    <script src="https://unpkg.com/@shopify/app-bridge@3"></script>
    <script>
      const AppBridge = window['app-bridge'];
      const app = AppBridge.createApp({
        apiKey: '<%= ShopifyApp.configuration.api_key %>',
        host: '<%= @shop_session.url %>',
      });
    </script>
  </body>
</html>
```

### **2. View de Produtos**

```erb
<!-- app/views/products/index.html.erb -->
<div class="Polaris-Page">
  <div class="Polaris-Page-Header">
    <div class="Polaris-Page-Header__TitleWrapper">
      <h1 class="Polaris-DisplayText--sizeLarge">Produtos</h1>
    </div>
    <div class="Polaris-Page-Header__Actions">
      <%= link_to "Novo Produto", new_product_path, 
                  class: "Polaris-Button Polaris-Button--primary" %>
    </div>
  </div>

  <div class="Polaris-Card">
    <div class="Polaris-ResourceList">
      <% @products.each do |product| %>
        <div class="Polaris-ResourceList__ItemWrapper">
          <div class="Polaris-ResourceItem">
            <div class="Polaris-ResourceItem__Content">
              <h3><%= link_to product.title, product_path(product) %></h3>
              <p>Vendor: <%= product.vendor %></p>
              <p>Tipo: <%= product.product_type %></p>
            </div>
            <div class="Polaris-ResourceItem__Actions">
              <%= link_to "Editar", edit_product_path(product), 
                          class: "Polaris-Button" %>
            </div>
          </div>
        </div>
      <% end %>
    </div>
  </div>
</div>
```

## 🔧 Funcionalidades Avançadas

### **1. GraphQL API**

```ruby
# app/services/shopify_graphql_service.rb
class ShopifyGraphqlService
  def initialize(shop_session)
    @session = shop_session
  end

  def fetch_products_with_variants
    query = <<~GRAPHQL
      query getProducts($first: Int!) {
        products(first: $first) {
          edges {
            node {
              id
              title
              handle
              variants(first: 10) {
                edges {
                  node {
                    id
                    title
                    price
                    inventoryQuantity
                  }
                }
              }
            }
          }
        }
      }
    GRAPHQL

    response = ShopifyAPI::GraphQL.client.query(query, variables: { first: 50 })
    response.data.products.edges.map(&:node)
  end
end
```

### **2. Cache Inteligente**

```ruby
# app/services/product_cache_service.rb
class ProductCacheService
  CACHE_TTL = 1.hour

  def self.fetch_products(shop_domain)
    Rails.cache.fetch("shop:#{shop_domain}:products", expires_in: CACHE_TTL) do
      shop = Shop.find_by(shopify_domain: shop_domain)
      session = ShopifyApp::SessionRepository.retrieve(shop.id)
      
      ShopifyAPI::Base.activate_session(session)
      ShopifyAPI::Product.find(:all, params: { limit: 250 })
    end
  end

  def self.invalidate_cache(shop_domain)
    Rails.cache.delete("shop:#{shop_domain}:products")
  end
end
```

## 🚀 Deploy em Produção

### **1. Heroku Deploy**

```bash
# Procfile
web: bundle exec puma -C config/puma.rb
worker: bundle exec sidekiq
```

```yaml
# .github/workflows/deploy.yml
name: Deploy to Heroku
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: akhileshns/heroku-deploy@v3.12.12
        with:
          heroku_api_key: ${{secrets.HEROKU_API_KEY}}
          heroku_app_name: "seu-app-shopify"
          heroku_email: "seu-email@exemplo.com"
```

### **2. Configurações de Produção**

```ruby
# config/environments/production.rb
Rails.application.configure do
  # SSL obrigatório para Shopify
  config.force_ssl = true
  
  # Session store seguro
  config.session_store :cookie_store, 
    key: '_shopify_app_session',
    secure: true,
    httponly: true,
    same_site: :none
end
```

## 📈 Monetização e Store

### **1. Cobrança por Assinatura**

```ruby
# app/models/billing_service.rb
class BillingService
  def self.create_recurring_charge(shop, plan_name, price)
    charge = ShopifyAPI::RecurringApplicationCharge.new(
      name: plan_name,
      price: price,
      trial_days: 7,
      test: Rails.env.development?
    )
    
    if charge.save
      charge.confirmation_url
    else
      nil
    end
  end
end
```

### **2. Verificação de Pagamento**

```ruby
# app/controllers/billing_controller.rb
class BillingController < AuthenticatedController
  def confirm
    charge = ShopifyAPI::RecurringApplicationCharge.find(params[:charge_id])
    
    if charge.status == 'accepted'
      charge.activate
      current_shop.update(billing_active: true)
      redirect_to root_path, notice: 'Assinatura ativada!'
    else
      redirect_to pricing_path, alert: 'Pagamento cancelado'
    end
  end
end
```

## 🎯 Boas Práticas

### **1. Tratamento de Erros**

```ruby
# app/controllers/concerns/error_handler.rb
module ErrorHandler
  extend ActiveSupport::Concern

  included do
    rescue_from ShopifyAPI::ValidationException, with: :handle_validation_error
    rescue_from ActiveRecord::RecordNotFound, with: :handle_not_found
  end

  private

  def handle_validation_error(exception)
    render json: { error: exception.message }, status: :unprocessable_entity
  end

  def handle_not_found
    render json: { error: 'Recurso não encontrado' }, status: :not_found
  end
end
```

### **2. Rate Limiting**

```ruby
# app/services/shopify_api_service.rb
class ShopifyApiService
  MAX_RETRIES = 3
  RETRY_DELAY = 2.seconds

  def self.with_retry(&block)
    retries = 0
    begin
      yield
    rescue ShopifyAPI::LimitExceededException => e
      retries += 1
      if retries <= MAX_RETRIES
        sleep(RETRY_DELAY * retries)
        retry
      else
        raise e
      end
    end
  end
end
```

## 🚀 Conclusão

Desenvolver plugins Shopify com Rails é uma excelente oportunidade de negócio. Os pontos principais são:

- **Configure corretamente** as credenciais e webhooks
- **Use Polaris** para interface consistente
- **Implemente billing** para monetização
- **Trate erros** e rate limiting adequadamente
- **Teste thoroughly** antes do deploy

### **Próximos Passos:**
1. Submeter para Shopify App Store
2. Implementar analytics
3. Adicionar testes automatizados
4. Otimizar performance

---

*Com mais de 10 anos desenvolvendo com Rails, considero o Shopify uma das melhores plataformas para monetizar habilidades de desenvolvimento web.*