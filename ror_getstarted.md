+++
title = '开始Ruby On Rails'
date = 2024-09-30T08:08:08+08:00
+++

# Prepare
- [nodejs](https://tdtc-hrb.github.io/csdn/post/nodejs-ubuntu/)

- foreman
```
gem install foreman
```

## mysql-dev
- ubuntu
```
sudo apt install mysql-client libmysqlclient-dev
gem install mysql2
```
### [Debian](https://stackoverflow.com/a/34384709)
- mariadb
```
sudo apt-get install mariadb-server
```
- [mariadbclient-dev](https://github.com/ansible-community/ara-collection/issues/8)
```
sudo apt-get install libmariadb-dev-compat libmariadb-dev
```
- [client](https://stackoverflow.com/questions/72000977/how-do-i-install-libmysqlclient21-on-debianbuster-slim-based-docker-image)
```
sudo apt-get install mariadb-client mysql-common default-mysql-client
```
- mysql2
```
gem install mysql2
```

## Yarn
upload [yarn_1.22.22_all.deb](https://github.com/yarnpkg/yarn/releases/download/v1.22.22/yarn_1.22.22_all.deb) to ubuntu:
```cmd
scp d:\yarn_1.22.22_all.deb tdtc@192.168.3.99:/home/tdtc/
```
install:
```bash
sudo chmod +x yarn_1.22.22_all.deb
sudo dpkg -i yarn_1.22.22_all.deb
```


# Creating a New Rails Project
new:
```bash
rails new veic_web-r2 -j esbuild --css bulma -d mysql
```


## upload images
windows 10:
```cmd
tar zcvf img.tar.gz ./img/ --transform s/img/images/
scp d:\projects\img.tar.gz tdtc@192.168.3.99:/home/tdtc/veic_web-r2/app/assets/
```

ubuntu:
```bash
cd ~/veic_web-r2/app/assets/
tar zxvf img.tar.gz
```

## config db
```
vi ./config/database.yml
```
remote user:
```
username: tdtc2022
password: qaz1xsw2
```
Remote db server:
```
host: 192.168.3.128
```

# Retrieval
```
cd veic_web-r2
rails generate controller Product index --skip-routes
```

## data manage
```
rails console
```

### model
- Parameter    
```
rails generate model Parameter --skip-migration
```
- Image    
```
rails generate model Image --skip-migration
```
- Product    
```
rails generate model Product parameter:references image:references --skip-migration
```

### 3NF

- Parameter    
add "./app/models/parameter.rb":
```
has_many :products
```

- Image    
add "./app/models/image.rb":
```
has_many :products
```

## Showing product data
see routes:
```
rails routes
```
default Action name:
```
       Prefix Verb   URI Pattern                    Controller#Action
         root GET    /                              product#index
product_index GET    /product(.:format)             product#index
              POST   /product(.:format)             product#create
  new_product GET    /product/new(.:format)         product#new
 edit_product GET    /product/:id/edit(.:format)    product#edit
      product GET    /product/:id(.:format)         product#show
              PATCH  /product/:id(.:format)         product#update
              PUT    /product/:id(.:format)         product#update
              DELETE /product/:id(.:format)         product#destroy
```

Modify route in "./config/routes.rb":
```
  resources :product do
    collection do
      get 'catalog'
    end
  end
```

### Actions
In "./app/controllers/product_controller.rb"

- default
```
  def index
    @products = Product.all
  end

  def show
    @product = Product.find(params[:id])
  end
```

- Custom
```
  def catalog
    @product = Product.find_by(name: params[:name], lang_id: params[:lang])
    @parameter = Parameter.find(@product.param_id)
    @image = Image.find(@product.img_id)
  end
```

### views
- index    
in "./app/views/product/index.html.erb":
```
<h1>Products</h1>

<ul>
  <% @products.each do |product| %>
    <li>
      <%= link_to product.name + " - " + product.lang_id.to_s, product %>
    </li>
  <% end %>
</ul>
```

- show    
in "./app/views/product/show.html.erb":
```
<h1><%= @product.name %></h1>

<p>
  <%= @product.description %>
</p>
```

- catalog    
In "./app/views/product/catalog.html.erb" with bulma:
```
<h1><%= @product.name %></h1>

<div id="tabs-with-content">
  <div class="tabs is-centered">
    <ul>
      <li><a>Home</a></li>
      <li><a>Tech Parameters</a></li>
    </ul>
  </div>
  <div>
    <section class="tab-content">
      <table class="table">
        <tbody>
          <td>
            <%= image_tag "#{@image.name}" %>
          </td>
          <td>
            <%= @product.description %>
          </td>
        </tbody>
      </table>
    </section>
    <section class="tab-content">
      <%= @parameter.ipxx %>
    </section>
  </div>
</div>
```

## launch
startup server:
```
$ ./bin/dev
```

### config env
- edit Procfile.dev
```
web: env RUBY_DEBUG_OPEN=true bin/rails server -p 3000 -b 0.0.0.0
```
If the port is defined in /bin/dev, then:
```
web: env RUBY_DEBUG_OPEN=true bin/rails server -b 0.0.0.0
```

### allow port
```
$ sudo ufw allow 3000
```
If it is a clone project, you need to execute:
```
$ bundle exec rake app:update:bin
```

### Permission denied
- Error message
```
./bin/dev: Permission denied
 env: ‘bin/rails’: Permission denied
```
- exec
```bash
sudo chmod +x ./bin/dev
sudo chmod +x ./bin/rails
```

### node modules
When moving this folder, some files will be lost!
```
esbuild: not found
sass: not found
```
Need to rebuild:
```bash
rm -rf ./node_modules
npm install
```

### web access addr
- [product list](http://192.168.3.99:3000/product/)
- [product id1](http://192.168.3.99:3000/product/1)
- [product catalog](http://192.168.3.99:3000/product/catalog?name=aei&lang=4136)


# Ref
- [runtimes.rb:58:in `autodetect': Could not find a JavaScript runtime.](https://stackoverflow.com/a/9333316)
- [Ruby on rails route to a custom method in the controller from the view](https://stackoverflow.com/a/11178349)
- [Weird rails error "permission denied: bin/rails" for old rails apps](https://stackoverflow.com/a/56686825)
- [Bulma: Tabs with Content](https://eiji.dev/bulma-tabs-with-content.html)
- [bulma css grid](https://bulma.io/documentation/columns/options/#multiline)
