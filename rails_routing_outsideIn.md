
#Rails Routing from the Outside Ln

这部分内容覆盖了面向用户的路由的特性。通过学习，你将会收获到如下：

* 了解routes.rb的代码
* 可以利用资源类型或者match方法来建立自己的路由
* 从路由中提取action所需的参数
* 利用route helpers来自动产生路径和URL
* Use advanced techniques such as constraints and Rack endpoints

##	rails路由的作用

rails的路由可以解析URL并且可以分发到具体的控制器中的action。同样的它可以生成path和URL，避免在视图中的硬编码。

### 将URL和代码连接起来

当你的应用接收到一个请求：

    GET /patients/17
这句将会引导到控制器的action，如果首先满足了如下的路由：

    match "/patients/:id" => "patients#show"
它将会被分发到patient控制器的show action，并且参数为{ :id => "17" }。

### 通过代码来产生Path和URL

如果你的应用包含如下的代码：

    @patient = Patient.find(17)
    <% link_to "Patient Record", patient_path( @patinet ) %>
那么将会产生/patients/17，这减少了视图的脆弱性并且使得你的代码容易被理解。即在route helper不用指定id。

## 资源路由：rails默认的路由

利用资源路由可以使你快速的声明所有的资源控制器关联的路由。资源路由只要一句话就可以为index, show, new, edit, create, update 和 destroy 的action产生路由。

### web中的资源
浏览器通过指定的HTTP方法（GET， POST, DELETE, PUT )来向rails请求网页内容。资源路由可以将一系列的请求映射到一个单独的控制器。

当你的rails应用程序接收到如下的请求：

    DELETE /photos/17
它请求路由将其映射到一个控制器的action。如果首要匹配的路由是：

    resources :photos
rails将会分发这个请求到photos控制器中的destroy方法，并且参数为{ :id => "17" }

### CRUD,Verbs，Actions
资源型路由提供了一个HTTP动词和URL到控制器action之间的映射。为了方便起见，每种action映射到对应的数据库中的CRUD操作。例如：

    resources :photos
映射到photos控制器的七种不同的路由表如下所示：

    HTTPVerb Path	            action	    used for
    GET	     /photos	        index	    display a list of all photos
    GET	     /photos/new	    new	        return an HTML form for creating a new photo
    POST	 /photos	        create	    create a new photo
    GET	     /photos/:id	    show	    display a specific photo
    GET	     /photos/:id/edit	edit	    return an HTML form for editing a photo
    PUT	     /photos/:id	    update	    update a specific photo
    DELETE	 /photos/:id	    destroy	    delete a specific photo


### Paths, URL

资源路由同样可以有对应的helpers。例如 resources :photos:

* photos_path 返回 /photos
* new_photo_path 返回 /photos/new
* edit_photo_path(:id) 返回 /photos/:id/edit
* photo_path(:id) 返回 /photos/:id

上述的每一种helpers都有对应的_url helper（例如 photos_url),将会返回同样的路径。

### 同时定义多个资源路由

如果需要创建多个资源路由，你可以将所有的定义在一句话里面：

    resources :photos, :books, :videos

### Singular Resources

有时，你需要访问一个资源但是不用携带相应的ID，例如/profile显示当时登陆用户的信息。此时，你只需要利用singular资源来将/profile(而不是/profile/:id)映射到show action。

    match "profile" => "users#show"
通过资源路由:

    resource :geocoder
将会产生对应的六种不同的映射关系：

    HTTP Verb	Path	        action	used for
    GET	        /geocoder/new	new	    return an HTML form for creating the geocoder
    POST	    /geocoder	    create	create the new geocoder
    GET	        /geocoder	    show	display the one and only geocoder resource
    GET	        /geocoder/edit	edit	return an HTML form for editing the geocoder
    PUT	        /geocoder	    update	update the one and only geoco