rails敏捷开发

#一，创建应用程序

##创建rails应用程序

    rails new depot

###生成脚手架

    rails generate scaffold Product title:string description:text image_url:string price:decimal

这个命令利用scaffold来产生数据库映射Product，Product有属性title，description,image_url，这些均是字符类型，price为单精度类型。此时生成的Product还未存储到数据库中。

###迁移到数据库中

首先系统中需要安转有数据库如：mysql,sqlite3等，幸好linux系统默认就有sqlite3.
修改数据库模型中的价格属性：8位有效数字，小数点后2位。

'
class CreateProducts < ActiveRecord::Migration
  def change
    create_table :products do |t|
      t.string :title
      t.text :description
      t.string :image_url
      t.decimal :price, :precision => 8, :scale => 2

      t.timestamps
    end
  end
end
'

如下命令可以迁入到数据库中：

    rake db:migrate

###打开服务器

如下命令就可以打开服务器：

    rails server

可以在浏览器中输入 localhost:3000查看结果
输入 localhost:3000/products就可以查看生成商品列表的页面。

###加入测试

    rake test

##部署到heroku

现在命令行执行 gem install heroku

继续执行 gem install taps(未知)

由于本机安装的是sqlite3数据库，所以需要修改默认的Gemfile。

将Gemfile中的 gem 'sqlite3' 为 gem 'pg'。然后在命令行执行，bundle install。完成以后执行：

    git add .
    git commit -m "init"

提交到heroku：

    git push heroku master

    heroku run rake db:migrate

此时就可以在heroku上面看到结果了。

##验证和单元测试

###验证

在validates 后添加要验证项，并添加属性：

    class Product < ActiveRecord::Base
      attr_accessible :description, :image_url, :price, :title
      validates :title, :description, :image_url, :presence => true
      validates :price, :numericality => { :greater_than_or_equal_to => 0.01 }
      validates :title, :uniqueness => true
      validates :image_url, :format => {
        :with => %r{\.(gif|jpg|png)$}i,
        :message => 'must be a URL for GIF, JPG or PNG image.'
      }
    end

接下来运行 rake test，出现两个错误，是因为我们没有添加测试数据，在/depot/test/functional/products_controller_test.rb中添加如下代码：

    test "should create product" do
      assert_difference('Product.count') do
    #      post :create, product: { description: @product.description, image_url: @product.image_url, price: @product.price, title: @product.title }
        post :create, :product => @update
      end
    
      assert_redirected_to product_path(assigns(:product))
    end

    test "should update product" do
    #    put :update, id: @product, product: { description: @product.description, image_url: @product.image_url, price: @product.price, title: @product.title }
      put :update, :id => @product.to_param, :product => @update
      assert_redirected_to product_path(assigns(:product))
    end


继续执行rake test，可以发现测试通过。

###单元测试

可以在test/unit/product_test.rb中添加单元测试项:
    test "product attributes must not be empty" do
      product = Product.new
      assert product.invalid?
      assert product.errors[:title].any?
      assert product.errors[:description].any?
      assert product.errors[:price].any?
      assert product.errors[:image_url].any?
    end

##添加商品目录
###创建商品目录清单
    Depot::Application.routes.draw do
      get "store/index"
      
      resource :products
      root :to => "store#index", :as => "store"
    end
同时需要删除rm public/index.html.随后启动server: 
    rails s
在浏览器中输入localhost:3000就可以看到主页变成了store的index页面了。

接下来需要在主页中显示商品目录，首先需要从控制器中传送出数据，如下(app/controller/store_controller.rb
    class StoreController < ApplicationController 
      def index
        @products = Product.all
      end
    end

控制器中的数据被传到那里呢？就是view视图中，rails提供的视图与控制器相对应。(app/view/store/index.html.erb

    <h1>Store#index</h1>
    <p>Find me in app/views/store/index.html.erb</p>

    <% if notice %>
    <p id="notice"><%= notice %></p>
    <% end %>
    
    <h1>Your Pragmatic Catalog</h1>

    <% @products.each do |product| %>
      <div class="entry">
        <%= image_tag(product.image_url) %>
        <h3><%= product.title %></h3>
        <%= sanitize(product.description) %>
        <div class="price_line">
          <span class="price"><%= product.price %></span>
        </div>
      </div>
    <% end %>

###此时可以将应用部署到heroku上面，前提是你已经申请了heroku帐户。

    gem install heroku
    heroku keys:add
    heroku create railsagile --stack cedar
    git push heroku cedar
    heroku rake db:migrate      

注意：部署到heroku上面提示如下错误：
    We're sorry, but something went wrong.
后来发现，由于app/view/layouts/application.html.erb中的stylesheet_link_tag一句导致问题出现。
    <%= stylesheet_link_tag  "application", :media => "all" %>
上述代码即可使得程序正常运行。

##添加购物车
    rails g scaffold cart
