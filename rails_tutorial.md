
#Chapter 3
#Mostly static pages 靜態頁面

在这一章里面，我们将改进sample应用，剩下的章节我们都是以这个应用作为例子。尽管这个应用拥有users，microposts和整个的登录认证框架，但是我们还是要从一个看起来很有限的主题上开始：静态网页的产生。这个过程看起来很简单，但是确实一个很有意义的练习的过程。

尽管Rails是为依赖数据库的动态网站而来，但是它仍然能处理普通的静态网页。事实上，Rails中使用静态网页还有一个优势：可以很方便的加入一些动态内容。本章我们就来学习如何使用这些功能。依靠这种方法，我们可以尝试自动化测试，通过自动化测试可以增加我们写代码的自信心。另外可以促使我们重构代码的信心，改变代码的形式而非功能。

本章有很多代码，特别是3.2和3.3.如果你是ruby的新手，也不必担心代码的细节。类似1.1.1提示，一个有效的方法就是直接复制粘贴，然后来验证应用程式代码，这样可以不用关注代码的原理。第四章对ruby需要有一个深入的认识，所以会有很多的机会这样做。最后，RSpec测试将会贯穿于整个tutorial，所以如果你碰到障碍，我建议你继续往下学，当你学习过很多章以后，自然会对现在的障碍有个新的认识。

类似于第二章，我们需要重新建立一个新的Rails Project，名字就叫Sample_app.

    $ cd ~/rails_projects
    $ rails new sample_app --skip-test-unit
    $ cd sample_app

这里的--skip-test-unit选项告诉Rails不要产生测试的目录。并不是说我们就不写测试了，相反的，从3.2开始，我们将会用一个可选的测试框架RSpec来完成剩下的测试。

如2.1中所示，下一步我们将会利用编辑器来更新Gemfile。另一方面，我们需要两个Gems：RSpec的Gem和针对Rails的特定RSpec Gem。代码如3.1中所示。

**Listing 3.1** A Gemfile for the sample app.

    source 'http://rubygems.org'
    gem 'rails', '3.2.6'
    group :development, :test do
      gem 'sqlite3', '1.3.5'
      gem 'rspec-rails', '2.10.0'
    end

    group ：asserts do
      gem 'sass-rails', '3.2.4'
      gem 'coffee-rails', '3.2.2'
      gem 'uglifier', '1.3.2'
    end

    gem 'jquery-rails', '2.0.0'

    group :test do
      gem 'capybara', '1.1.2'
    end

    group :production do
      gem 'pg', '0.12.2'
    end

这个文件中的development模式中包含了rspec-rails，这样我们就可以进入rspec-rails生成器中，并且它被包含在test模式中以便测试。我们不需要单独安装rspec，由于rspec-rails依赖于rspec，所以会自动被安装。我们同样包含了Capybara gem，它允许我们用英语的语法来进行测试。同样的，我们在第二章中包含了PostgreSQL以便部署到heroku上面。

    group :production do
      gem 'pg', '0.12.2'
    end

Heroku不允许用其他的数据库，但是对于本应用没有多大的影响，并且SQLite比PostgreSQL安装起来容易的多。安装和配置PostgreSQL被留作练习题。

为了安装和包含新的gem，我们运行bundle install：
    $bundle install --without production

正如第二章中所述，我们用选项--without production屏蔽了production gem的安装。这个选项意味着我们在以后的bundle中不必包含production。

接下来我们需要配置rails利用RSpecc 来代替Test::Unit,可以通过rails generate rspec:install来完成。

    $rails generate rspec:install

如果你的系统没有安装JavaScript环境，访问[execjs page at Github](https://github.com/sstephenson/execjs)来下载。我在这里特别推荐的是[Node.js](http://nodejs.org/)。

经过上述的步骤，剩下的就是初始化Git repository.

    $git init
    $git add .
    $git commit -m "Initial commit"

作为第一个应用，我建议更新以下Readme文件。然后更改github上面的Readme文件。

    $git mv README.rdoc README.md
    $git commit -a -m "Improve the README"

因为我们整本书都是用的一个例子，所以push到github上面是个不错的主意。

    $git remote add origin git@github.com:<username>/sample_app.git
    $git push -u origin mater

你还可以在Github上面找到本书的代码。[code](https://github.com/railstutorial/sample_app_2nd_ed)

当然，我们也可以部署app到heroku上面。

    $heroku create --stack cedar
    $git push heroku master

如果heroku 上面部署出现问题，可以到heroku的log上面看看是否有有用的信息。

    $heroku logs

##3.1 静态页面

Rails有两个主要的途径来处理静态页面。首先，Rails可以处理纯粹的HTML文件。其次，Rails允许我们定义可以渲染的包含原生HTML的视图，服务器可以将这些视图发送到浏览器。

本章我们主要的工作目录是app/controllers 和 app/views。

作为第一部分，我们需要用编辑器或者IDE来打开整个rails目录。不幸的是，这依赖于具体的系统。例如在类Unix系统里面：

    $cd ~/rails_projects/sample_app
    $<editor name> .

###3.1.1 纯的静态网页

我们可以在[http://localhost:3000/](http://localhost:3000/)看到默认的欢迎页面。

可以在public目录中找到index.html文件，因为这个文件有自己的样式信息，虽然有点让人困惑，但是，rails会将public文件夹直接发送到浏览器。

###3.1.2 Rails的静态网页

返回静态HTML文件是很有意义，但是对于动态网页来说，不是很有用。本节我们将会通过创建一系列的action来实现动态页面，这是比静态网页强大很多的一种功能。rails通过脚本化语言generate来生成控制器，为了使用generate来生成RSpec,你需要运行RSpec生成器：

    $ rails generate rspec:install

接下来我们将会生成一个控制器来处理静态网页。同样我们也计划生成首页的控制器，帮助也买你生成器和About生成器。

    $rails generate controller StaticPages home help --no-test-framework
     create  app/controllers/static_pages_controller.rb
       route  get "static_pages/help"
       route  get "static_pages/home"
      invoke  erb
      create    app/views/static_pages
      create    app/views/static_pages/home.html.erb
      create    app/views/static_pages/help.html.erb
      invoke  helper
      create    app/helpers/static_pages_helper.rb
      invoke  assets
      invoke    coffee
      create      app/assets/javascripts/static_pages.js.coffee
      invoke    scss
      create      app/assets/stylesheets/static_pages.css.scss

在这里我们利用选项--no-test-framework屏蔽默认的RSpec测试。controller生成器会自动更新routes文件。rails通过config/routes来通过URI来找到相对应的网页。这是我们第一次碰到config目录。

我们创建了home和help的actions，路由文件已经产生出了对应的规则。

    SampleApp::Application.routes.draw do
    get "static_pages/home"

    get "static_pages/help"
    end

通过上述语句来将路由/static_pates/home映射到对应的home动作。另外，我们利用get方法来响应请求。

为了了解具体的控制器起作用的原理，我们来看一下静态文件控制器代码：

    
    class StaticPagesController < ApplicationController
      def home
      end
    
      def help
      end
    end

我们可以看到定义了一个类StaticPagesController,里面定义了两个方法，来对应home和help动作。小于号表明了这个类继承自AlicationController。它意味着我们的页面还会加载很多Rails特定的内容。

上述的方法均是空的内容，在ruby语法中，这个方法不做任何事。但是在Rails由于所在的类继承自ApplicationController,这就意味着当访问/static_pages/home链接，Rails来查找控制器，并且执行home方法，然后渲染视图view。问题就来了，这个视图view在什么地方呢？

如果你曾经看过rails的目录，就能发现有个view文件夹，里面有home.html.erb。erb说明这是一个模板文件。

##3.2 第一个测试

rails tutorails提供了一个直观的方法来测试，这个测试强调的是应用程序的行为而不是精确的实现。这就是一种不同的测试驱动即行为驱动测试。我们主要的工具就是集成测试和单元测试。集成测试允许我们利用web浏览器来仿真用户行为。结合Capybara中的自然语言语法，集成测试提供了一个强大的测试方法。

TDD的精髓是测试先行。也就是先写测试用例，然后实现代码，测试通过，在一步步的测试过程中增加我们的信心。最终，测试部分作为应用程序代码的客户端，通常可以引导软件设计到一个更加优美的高度。

需要理解的是TDD并不是万能的。接下来我们要利用rspec命令来运行测试。

###3.2.1 TDD

我们首先需要写出失败的测试用例，在许多测试环境中表现为红色，然后我们来实现代码，让测试通过，测试环境变为绿色。最后，如果需要，我们可以重构代码，但是只改变它的形式，而不改变它的功能。用颜色来表示就是“红色，绿色，重构“。

我们需要在Home页面添加一些内容。第一步来产生一个集成测试环境：

    $rails g integration_test static_pages

这条命令创建了spec/requests目录下的 static_pages_spec.rb文件。正如大多数产生出来的代码，结果并不是很漂亮的代码。让我们来进行一个小的修改。

    require 'spec_helper'
    
    describe "StaticPages" do
      describe "Home page" do
        it "should have the content 'Sample App'" do
          # Run the generator again with the --webrat flag if you want to use webrat methods/matchers
          visit '/static_pages/home'
          page.should have_content('Sample App')
      
        end
      end
    end

上述代码为纯粹的ruby代码，但是即使你以前学过ruby语法，这些语句仍然不是很好理解。这是因为RSpec利用了ruby定义的DSL语言的缘故。很重要的一点就是你不用理解Rspec的语法规则，因为RSpec和Capybara设计的本意就是代码读起来可以像英语。

上述代码详解：
第一行表示了我们描述的是 Home页面，这句可以随意设置，只是为了理解方便。后面的语句表达了如果你访问home页面，应该包含Sample App的字符。visit是Capybara的函数，用来模仿访问/static_pages/home。page.should行利用了Capybara提供的page变量来测试被测试页面是否包含相应的内容。

我们有很多选项来运行这个测试，包括一些方便但是不是很先进的工具。现在我们需要用rspec命令：

    $ bundle exec rspec spec/requests/static_pages_spec.rb

我们可以看到有运行失败的结果。

在views/static_pages/home.html.erb中添加如下语句：

    <h1>Sample App</h1>
    <p>
      This is the home page for the
      <a href="http://railstutorial.org/">Ruby on Rails Tutorial</a>
      sample application.
    </p>

继续刚才的命令，可以发现结果运行通过。

基于home页面的测试过程，我们可以实现Help页面的测试，

    urequire 'spec_helper'

    describe "StaticPages" do
      describe "Home page" do
        it "should have the content 'Sample App'" do
      # Run the generator again with the --webrat flag if you want to use webrat methods/matchers
          visit '/static_pages/home'
          page.should have_content('Sample App')
          
        end
      end

      describe "Help page" do
    
        it "should have the content 'Help'" do
          visit '/static_pages/help'
          page.should have_content('Help')
        end
      end
    
    
    end

同样的为了测试用例的通过，我们修改代码如下：

    <h1>Help</h1>
    <p>
      Get help on the Ruby on Rails Tutorial at the
      <a href="http://railstutorial.org/help">Rails Tutorial help page</a>.
      To get help on this sample app, see the
      <a href="http://railstutorial.org/book">Rails Tutorial book</a>.
    </p>

到此，两条测试均可以通过了。
    
